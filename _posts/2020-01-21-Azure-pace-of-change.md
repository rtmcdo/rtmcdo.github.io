---
layout: post
title: "Azure Pace of Change"
subtitle: "Analysis of 2019 Azure Services and Updates"
date: 2020-01-21 05:15:00 -0600
background: '/img/posts/chris-lawton-5IHz5WhosQE-unsplash.jpg'
image: '/img/posts/chris-lawton-5IHz5WhosQE-unsplash.jpg'
---
<h2 class="section-heading">Introduction</h2>

When leveraging Cloud technologies, keeping up with the pace of change is always going to be a significant organizational challenge. It's an interesting time in I&O - the velocity of innovation, information, and change makes it difficult to keep pace. 
 

<h2 class="section-heading">Pace of Change Analysis</h2>

For a previous [blog post](https://www.10thmagnitude.com/overcome-cloud-management-challenges-with-azure/) on 10thMagnitude.com, we quantified the updates by year, from the [Azure Updates](https://azure.microsoft.com/en-us/updates/) page. For that analysis, I used python code to scrape the post date and title from the Azure Updates page to understand the number of updates by year. I re-ran this recently and here are the results:

![Azure Updates](/img/posts/Azure-Updates-by-Year.png){:class="img-fluid"}

As you can see, updates in 2019 (1165) outpaced updates in 2018 (849) and were up by 37.22%. 

I would assume Microsoft has improved its internal processes to ensure that all updates are getting published, so that might account from some of the increase. Regardless the 2019 updates average out to over three updates per day! 

For fun, I made a word cloud of the 2019 updates at [Wordclouds.com](https://www.wordclouds.com/). 

![Azure Updates Word Cloud](/img/posts/Azure-updates-word-cloud.png){:class="img-fluid"}

<h3 class="section-heading">Services</h3>

I also ran similar python code to export all of the service names from this [page](https://azure.microsoft.com/en-us/services/). There isn’t a way to determine when they were added, but at the time of writing, there were 181 unique services. For the blog post mentioned above, I did the same thing in July of 2019, and there were 160 unique services at that time. 

<h2 class="section-heading">New services I'm excited about that came out in 2019</h2>

From an I&O perspective below are some of the services that caught my eye this year. 

- [Azure Firewall](https://docs.microsoft.com/en-us/azure/firewall/overview)
- [Azure Sentinal](https://azure.microsoft.com/en-us/services/azure-sentinel/)
- [Azure Bastion Host](https://azure.microsoft.com/en-us/services/azure-bastion/)
- [Azure Arc](https://azure.microsoft.com/en-us/services/azure-arc/)
- [Cost Management](https://azure.microsoft.com/en-us/services/cost-management/)
- [Azure Lighthouse](https://azure.microsoft.com/en-us/services/azure-lighthouse/)

I'm looking foward to continued enhancements in Azure Firewall, Sentinel, and Bastion Host. I'm also excited to see how Azure Arc matures. Extending Azure's management capability to other clouds and on-premises could signifigantly improve management in distributed environments. 

<h2 class="section-heading">Cloud Adoption Framework</h2>
Outside of all platform updates and new services, I think one of the most significant developments in 2019 from Microsoft was the release of the Cloud Adoption Framework. 

If you aren’t familiar with the Cloud Adoption Framework (CAF) here is Microsoft's description from the documentation:

> "The Cloud Adoption Framework is the One Microsoft approach to cloud adoption in Azure, consolidating and sharing best practices from Microsoft employees, partners, and customers. The framework gives customers a set of tools, guidance, and narratives that help shape technology, business, and people strategies for driving desired business outcomes during their adoption effort. This guidance aligns to the following phases of the cloud adoption lifecycle, ensuring easy access to the right guidance at the right time."

CAF covers the full lifecycle of cloud, but from an operations perspective, I have been focusing on the Govern and Manage sections. One particular area of interest is the "Five Disciplines of Cloud Governance."

![Five Disciplines of Cloud Governance](/img/posts/azure-caf.png){:class="img-fluid"}

From an operations perspecive these are areas where we see companies struggle. In future blog posts, I will be digging into each of the five areas in more detail, so stay tuned!

<h2 class="section-heading">If you were wondering… </h2>

I used a python library called BeautifulSoup to scrape the data from Microsoft's website. If you are interested in learning more I'd recommend this tutorial by Corey Schafer on YouTube - [Python Tutorial: Web Scraping with BeautifulSoup and Requests](https://youtu.be/ng2o98k983k).

Credits:
<p>Photo by Chris Lawton on <a href="https://unsplash.com/">Unsplash</a>.</p>

