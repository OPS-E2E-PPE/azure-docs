---
 title: include file
 description: include file
 services: firewall
 author: vhorne
 ms.service: 
 ms.topic: include
 ms.date: 8/13/2018
 ms.author: victorh
 ms.custom: include file
---

### What is Azure Firewall?

Azure Firewall is a managed, cloud-based network security service that protects your Azure Virtual Network resources. It's a fully stateful firewall-as-a-service with built-in high availability and unrestricted cloud scalability. You can centrally create, enforce, and log application and network connectivity policies across subscriptions and virtual networks. Azure Firewall is currently in public preview.

### Which capabilities are supported in the Azure Firewall public preview release?  

* Stateful firewall as a service
* Built-in high availability with unrestricted cloud scalability
* FQDN filtering 
* Network traffic filtering rules
* Outbound SNAT support
* The ability to centrally create, enforce, and log application and network connectivity policies across Azure subscriptions and virtual networks
* Full integration with Azure Monitor for logging and analytics 

### How can I join the Azure Firewall Public Preview?

Azure Firewall is currently a managed public preview that you can join by  using the Register-AzureRmProviderFeature PowerShell command. This command is explained in the Azure Firewall public preview documentation.

### What is the pricing for Azure Firewall?

Azure Firewall has a fixed and variable cost. The prices are as follows, and are further discounted by 50% during public preview.

* Fixed fee: $1.25/firewall/hour
* Variable fee: $0.03/GB processed by the firewall (ingress or egress)

### What is the typical deployment model for Azure Firewall?

You can deploy Azure Firewall on any virtual network, but customers typically deploy it on a central virtual network and peer other virtual networks to it in a hub-and-spoke model. You can then set the default route from the peered virtual networks to point to this central firewall virtual network.

### How can I install the Azure Firewall?

You can set up Azure Firewall by using the Azure portal, PowerShell, REST API, or by using templates. See [Tutorial: Deploy and configure Azure Firewall using the Azure portal](../articles/firewall/tutorial-firewall-deploy-portal.md) for step-by-step instructions.

### What are some Azure Firewall concepts?

Azure Firewall supports rules and rule collections. A rule collection is a set of rules that share the same order and priority. Rule collections are executed in order of their priority. Network rule collections are higher priority than application rule collections, and all rules are terminating.

There are two types of rule collections:

* *Application rules*: Enable you to configure fully qualified domain names (FQDNs) that can be accessed from a subnet. 
* *Network rules*: Enable you to configure rules that contain source addresses, protocols, destination ports, and destination addresses. 

### Does Azure Firewall support inbound traffic filtering?

Azure Firewall public preview supports outbound filtering only. Inbound protection for non-HTTP/S protocols (for example, RDP, SSH, or FTP) is tentatively planned for Azure Firewall GA.  
 
### Which logging and analytics services are supported by the Azure Firewall?

Azure Firewall is integrated with Azure Monitor for viewing and analyzing firewall logs. Logs can be sent to Log Analytics, Azure Storage, or Event Hubs. They can be analyzed in Log Analytics or by different tools such as Excel and Power BI. For more information, see [Tutorial: Monitor Azure Firewall logs](../articles/firewall/tutorial-diagnostics.md).

### How does Azure Firewall work differently from existing services such as NVAs in the marketplace?

Azure Firewall is a basic firewall service that can address certain customer scenarios. It's expected that you will have a mix of third-party NVAs and Azure Firewall. Working better together is a core priority.
 
### What is the difference between Application Gateway WAF and Azure Firewall?

The Web Application Firewall (WAF) is a feature of Application Gateway that provides centralized inbound protection of your web applications from common exploits and vulnerabilities. Azure Firewall provides outbound network-level protection for all ports and protocols and application-level protection for outbound HTTP/S. Inbound protection for non-HTTP/S protocols (for example, RDP, SSH, FTP) is tentatively planned for Azure Firewall GA.

### What is the difference between Network Security Groups (NSGs) and Azure Firewall?

The Azure Firewall service complements network security group functionality. Together, they provide better "defense-in-depth" network security. Network security groups provide distributed network layer traffic filtering to limit traffic to resources within virtual networks in each subscription. Azure Firewall is a fully stateful, centralized network firewall as-a-service, which provides network- and application-level protection across different subscriptions and virtual networks. 

### How do I set up Azure Firewall with my service endpoints?

For secure access to PaaS services, we recommend service endpoints. You can choose to enable service endpoints in the Azure Firewall subnet and disable them on the connected spoke virtual networks. This way you benefit from both features-- service endpoint security and central logging for all traffic.

### How can I stop and start Azure Firewall?

You can use Azure PowerShell *deallocate* and *allocate* methods.

For example:

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name” -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzureRmFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzureRmPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzureRmFirewall -AzureFirewall $azfw
```

### What are the known service limits?

* Azure Firewall has a soft limit of 1000 TB per firewall per month. 
* An instance of Azure Firewall that is running in a central virtual network has virtual network peering limitations, with a maximum of 50 spoke virtual networks.  
* Azure Firewall does not work with global peering, so you should have at least one firewall deployment per region.
* Azure Firewall supports 10k application rules and 10k network rules.