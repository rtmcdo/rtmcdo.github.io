---
layout: post
title: "CLI Commands to Create a Route Based Azure VPN with custom IPSEC parameters"
date: 2019-12-31 08:15:00 -0600
background: '/img/posts/sander-weeteling-4I41IQtmSs0-unsplash.jpg'
---

<h2 class="section-heading">Overview</h2>

I recently setup a 

``` yaml

#set subscription
az account set --subscription "Visual Studio Enterprise"

#set some variables
tla="tla"

#create resource group
az group create --location northcentralus -n rg-$tla

#create vnet
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

#create local gateway
az network local-gateway create -g rg-$tla -n prd-$tla-lgw  --gateway-ip-address 1.1.1.1 --local-address-prefixes 192.168.11.0/24

#create connection
az network vpn-connection create -g rg-$tla -n prd-$tla-vpn --vnet-gateway1 prd-$tla-gw --local-gateway2 prd-$tla-lgw  --shared-key changethiskey
```
At this point, you have all the required resources in place to configure the custom ipsec poliy. 




Default IPsec/IKE parameters are as follows:

- IKE version:             IKEv2
    - Encryption algorithm:  aes-cbc-256
    - Integrityalgorithm:    sha1
    - Diffie-Hellman group:  2
    - SA lifetime (seconds): 3600
    - Pre-shared key:        changethiskey
    - UsePolicyBasedTS:      False

- IPsec
    - Encryption algorithm:  esp-gcm 256
    - Integrity algorithm:   
    - PFS Group:             none
    - SA lifetime (seconds): 3600

You can download the configuation from the connection. 

![Configuration Dowload](/img/posts/vpn-download-configuration-2019-12-31 105731.png){:class="img-fluid"}

To create the a custom policy, use the following command. The example below creates a customer connection with Diffie-Hellman group to 14, and the IKE Integrity algoritm to sha256. 



``` yaml
#create custom ipsec policy
az network vpn-connection ipsec-policy add -g rg-$tla --connection-name prd-$tla-vpn \
    --dh-group DHGroup14 --ike-encryption AES256 --ike-integrity SHA256 --ipsec-encryption GCMAES256 \
    --ipsec-integrity GCMAES256 --pfs-group PFS14 --sa-lifetime 3600 --sa-max-size 1024
``` 
The Azure [documentation](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell#part-2---supported-cryptographic-algorithms--key-strengths) has a full list of supported cryptographic algorithms and key strengths. 


To view the current ipsec policy run the following command. 

``` yaml
#vpn ipsec list
az network vpn-connection ipsec-policy list -g rg-$tla --connection-name prd-$tla-vpn

```

![Policy List output](/img/posts/view-custom-policy-2019-12-31 105933.png){:class="img-fluid"}

To clear the ipsec policy back to defaul, run the following command. 

``` yaml
#clear custom ipsec policy
az network vpn-connection ipsec-policy clear -g rg-$tla --connection-name prd-$tla-vpn
```

****
Credits:
<p>Photo by Sander Weeteling on <a href="https://unsplash.com/">Unsplash</a>.</p>

