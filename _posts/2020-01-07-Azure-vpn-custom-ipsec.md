---
layout: post
title: "Create a Route Based Azure VPN with Custom IPsec Parameters"
subtitle: "Using the Azure CLI"
date: 2020-01-07 05:15:00 -0600
background: '/img/posts/sander-weeteling-4I41IQtmSs0-unsplash.jpg'
image: '/img/posts/sander-weeteling-4I41IQtmSs0-unsplash.jpg'
---

<h2 class="section-heading">Overview</h2>

I recently set up a VPN to a customer network that needed custom IPsec parameters. Below are the Azure CLI commands used to create the infrastructure. 

The workflow for the commands is as follows:

1. Create a virtual network and a VPN gateway
2. Create a local network gateway for the cross-premises connection
3. Create a connection (IPsec) with the standard IPsec/IKE policy
4. Add an IPsec/IKE policy with selected algorithms and parameters
5. View/remove an IPsec/IKE policy for an existing connection


<h2 class="section-heading">Getting into the Code</h2>

For our customers, we use a three-letter abbreviation (TLA) to identify the customer. In the example below, a variable sets the TLA that is used in the naming for the resources. I've also included the commands to create a VNet for testing in a sandbox environment. 

```console

#set subscription - change to your subscription name
az account set --subscription "Visual Studio Enterprise"

#set TLA variable
tla="tla"

#create resource group
az group create --location northcentralus -n rg-$tla

#create vnet - change network ranges as required
az network vnet create -g rg-$tla -n prd-$tla --address-prefix 192.168.10.0/25 
az network vnet subnet create -g rg-$tla --vnet-name prd-$tla -n admin --address-prefix 192.168.10.0/26
az network vnet subnet create -g rg-$tla --vnet-name prd-$tla -n GatewaySubnet --address-prefix 192.168.10.96/27

#create nsg
az network nsg create --name prd-$tla-admin-nsg --resource-group rg-$tla

#associate nsg to subnet
az network vnet subnet update --vnet-name prd-$tla --name admin --resource-group rg-$tla --network-security-group prd-$tla-admin-nsg

#pip for vpn gateway
az network public-ip create --name prd-$tla-vpn-pip --resource-group rg-$tla --allocation-method Dynamic

#create vpn gateway
az network vnet-gateway create --name prd-$tla-gw --public-ip-address prd-$tla-vpn-pip --resource-group rg-$tla --vnet prd-$tla --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait

#create local gateway - change the 1.1.1.1 ip address to the remote VPN gateway's public ip and local address prefixes that are being protected
az network local-gateway create -g rg-$tla -n prd-$tla-lgw  --gateway-ip-address 1.1.1.1 --local-address-prefixes 192.168.11.0/24

#create connection - change the shared secret key
az network vpn-connection create -g rg-$tla -n prd-$tla-vpn --vnet-gateway1 prd-$tla-gw --local-gateway2 prd-$tla-lgw  --shared-key changethiskey
```
At this point, you have all the required resources in place to configure the custom IPsec policy. 

![Configuration Dowload](/img/posts/azure-vpn-setup-custom-ipsec-2019-12-31 123827.png){:class="img-fluid"}

<h2 class="section-heading">Custom IPsec Configuration</h2>
For reference the default IPsec/IKE parameters for Azure connections are as follows:

- IKE version:             IKEv2
    - Encryption algorithm:  aes-cbc-256
    - Integrityalgorithm:    sha1
    - Diffie-Hellman group:  2
    - SA lifetime (seconds): 3600
    - Pre-shared key:        
    - UsePolicyBasedTS:      False

- IPsec
    - Encryption algorithm:  esp-gcm 256
    - Integrity algorithm:   
    - PFS Group:             none
    - SA lifetime (seconds): 3600

You can download the configuration from the Connection in Azure. 

![Configuration Dowload](/img/posts/vpn-download-configuration-2019-12-31 105731.png){:class="img-fluid"}

To create the custom policy, use the following command: 

``` console
#create custom ipsec policy - change as required
az network vpn-connection ipsec-policy add -g rg-$tla --connection-name prd-$tla-vpn \
    --dh-group DHGroup14 --ike-encryption AES256 --ike-integrity SHA256 --ipsec-encryption GCMAES256 \
    --ipsec-integrity GCMAES256 --pfs-group PFS14 --sa-lifetime 3600 --sa-max-size 1024
``` 
This example creates a custom connection with Diffie-Hellman group to 14, and the IKE Integrity algorithm set to sha256.

The [Azure documentation](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell#part-2---supported-cryptographic-algorithms--key-strengths) has a full list of supported cryptographic algorithms and key strengths. 


To view the current IPsec policy run the following command:

``` yaml
#vpn ipsec list
az network vpn-connection ipsec-policy list -g rg-$tla --connection-name prd-$tla-vpn

```

![Policy List output](/img/posts/view-custom-policy-2019-12-31 105933.png){:class="img-fluid"}

To clear the IPsec policy and reset it back to default, run the following command: 

``` yaml
#clear custom ipsec policy
az network vpn-connection ipsec-policy clear -g rg-$tla --connection-name prd-$tla-vpn
```

<h2 class="section-heading">Wrapping Up</h2>

As you can see from the commands, it's pretty straightforward to configure a custom IPsec policy. The CLI commands make quick work of configuring the infrastructure and IPsec policy. 

[Click here](/downloads/vpn-custom-ipsec.azcli) to download all of the commands in one AZCLI file. 


****
Credits:
<p>Photo by Sander Weeteling on <a href="https://unsplash.com/">Unsplash</a>.</p>

