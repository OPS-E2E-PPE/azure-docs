---
title: Azure Private Link frequently asked questions (FAQ)
description: Learn about Azure Private Link.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud

---

# Azure Private Link frequently asked questions (FAQ)

## Private Link

### What is Azure Private Link service and Private Endpoint?

- **Azure Private Endpoint**: Azure Private Endpoint is a network interface that connects you privately and securely to a service powered by Azure Private Link. You can use Private Endpoints to connect to an Azure PaaS service that supports Private Link or to your own Private Link Service.
- **Azure Private Link service**: Azure Private Link service is a service created by a service provider. Currently, a Private Link service can be attached to the frontend IP configuration of a Standard Load Balancer. 

### Can I connect my service to multiple Private Endpoints?
Yes. One Private Link service can receive connections from multiple Private Endpoints. However one Private Endpoint can only connect to one Private Link service.  
 
### Is data transferred over Private Link always private?
Yes. All data over Azure Private Link stays on the Microsoft backbone. It doesn’t traverse the internet.  
 
### What is the difference between a VNet Service Endpoint and a Private Endpoint?
- VNet Service Endpoints extend your virtual network private address space and the identity of your VNet to the Azure services, over a direct connection. Service Endpoints allow you to secure your critical Azure service resources to only your virtual networks, while the traffic is route optimized and stays in the Microsoft backbone network it is sdestined to the Public IP address of the service.
- Private Endpoint is a networking resource that acts as an entry point in your virtual network and uses Private IP address to reach the services powered by Private Link. The traffic is route optimized and stays in the Microsoft backbone network.   

### What is the relationship between Private Link service and Private Endpoint?
It is a one-to-many relationship. One Private Link service can receive connections from multiple private endpoints. On the other hand, one private endpoint can only connect to one Private Link service.    
 
### Will VNet Service Endpoints be deprecated once Private Endpoints are available? 
No. VNet Service Endpoints and Private Endpoints are independent technologies/resources. They can complement each other and both will co-exist. Some functionality and use cases may overlap, you can choose the model that fits your needs.  
 
### I am a service provider using Azure Private Link. Do I need to make sure all my customers have unique IP space and don’t overlap with my IP space? 
No. Azure Private Link provides this functionality for you. Hence, you are not required to have non-overlapping address space with your customer's address space. 
 
## Private Link Service
 
### What are the pre-requisites for creating a Private Link service? 
Your service backends should be in a Virtual network and behind a Standard Load Balancer.
 
### How can I scale my Private Link service? 
You can scale your Private Link service in a few different ways: 
- Add Backend VMs to the pool behind your Standard Load Balancer 
- Add an IP to the Private Link service. We allow up to 8 IPs per Private Link service.  
- Add new Private Link service to Standard Load Balancer. We allow up to eight Private Link services per load balancer.   


### How should I control the exposure of my Private Link service?
You can control the exposure using the visibility configuration on Private Link service. Visibility supports three settings:

- **None** - Only subscriptions with RBAC access can locate the service. 
- **Restrictive** - Only subscriptions that are whitelisted and with RBAC access can locate the service. 
- **All** - Everyone can locate the service. 
 
### Can I create a Private Link service with Basic Load Balancer? 
No. Private Link service over a Basic Load Balancer is not supported.
 
### Is a dedicated subnet required for Private Link service? 
No. Private Link service doesn’t require a dedicated subnet. You can choose any subnet in your VNet where your service is deployed.   

## Private Endpoint 
 
### CanI create multiple Private Endpoints in same VNet? Can they connect to different Services? 
Yes. You can have multiple private endpoints in same VNet or subnet. They can connect to different services.  
 
### Do I require a dedicated subnet for private endpoints? 
No. You don't require a dedicated subnet for private endpoints. You can choose a private endpoint IP from any subnet from the VNet where your service is deployed.  
 
### Can Private Endpoint connect to Private Link service across Azure Active Directory Tenants? 
Yes. Private endpoints can connect to Private Link services or Azure PaaS across AD tenants.  
 
### Can private endpoint connect to Azure PaaS resources across Azure regions?
Yes. Private endpoints can connect to Azure PaaS resources across Azure regions.

##  Next steps

- Learn about [Azure Private Link](private-link-overview.md)
