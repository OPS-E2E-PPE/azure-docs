---
title: Networking architecture in Azure Container Apps
description: Learn how to configure virtual networks in Azure Container Apps.
services: container-apps
author: cachai
ms.service: container-apps
ms.topic:  conceptual
ms.date: 03/29/2023
ms.author: cachai
---

# Networking architecture in Azure Container Apps

Azure Container Apps run in the context of an [environment](environment.md), which is supported by a virtual network (VNet). By default, your Container App Environment is created with a VNet that is automatically generated for you. Generated VNets are inaccessible to you as they're created in Microsoft's tenant. This VNet is publicly accessible over the internet, can only reach internet accessible endpoints, and supports a limited subset of networking capabilities such as ingress IP restrictions and container app level ingress controls.

Use the Custom VNet configuration to provide your own VNet if you need more Azure networking features such as:

- Integration with Application Gateway
- Network Security Groups
- Communicating with resources behind private endpoints in your virtual network

The features available depend on your architecture selection.

## Architecture Selection

There are two architectures in Container Apps: the Consumption only architecture supports only the [Consumption plan (GA)](./plans.md) and the workload profiles architecture that supports both the [Consumption + Dedicated plan structure (preview)](./plans.md). The two architectures share many of the same networking characteristics. However, there are some key differences.

| Architecture Type | Description |
|-----------|-------------|
| Workload profiles architecture (preview) | Supports UDR and egress through NAT Gateway. The minimum required subnet size is /27. |
| Consumption only architecture | Doesn't support user defined routes (UDRs) and egress through NAT Gateway. The minimum required subnet size is /23. |

## Accessibility Levels

In Container Apps, you can configure whether your container app allows public ingress or only ingress from within your VNet at the environment level.

| Accessibility level | Description |
|---------------------|-------------|
| [External](vnet-custom.md) | Container Apps environments deployed as external resources are available for public requests. External environments are deployed with a virtual IP on an external, public facing IP address. |
| [Internal](vnet-custom-internal.md) | When set to internal, the environment has no public endpoint. Internal environments are deployed with a virtual IP (VIP) mapped to an internal IP address. The internal endpoint is an Azure internal load balancer (ILB) and IP addresses are issued from the custom VNet's list of private IP addresses. |

## Custom VNet configuration

As you create a custom VNet, keep in mind the following situations:

- If you want your container app to restrict all outside access, create an [internal Container Apps environment](vnet-custom-internal.md).

- When you provide your own VNet, you need to provide a subnet that is dedicated to the Container App environment you deploy. This subnet isn't available to other services.

- Network addresses are assigned from a subnet range you define as the environment is created. 

  - You can define the subnet range used by the Container Apps environment.
  - You can restrict inbound requests to the environment exclusively to the VNet by deploying the environment as [internal](vnet-custom-internal.md).

As you begin to design the network around your container app, refer to [Plan virtual networks](../virtual-network/virtual-network-vnet-plan-design-arm.md) for important concerns surrounding running virtual networks on Azure.

:::image type="content" source="media/networking/azure-container-apps-virtual-network.png" alt-text="Diagram of how Azure Container Apps environments use an existing V NET, or you can provide your own.":::

> [!NOTE]
> Moving VNets among different resource groups or subscriptions is not supported if the VNet is in use by a Container Apps environment.

## HTTP edge proxy behavior

Azure Container Apps uses [Envoy proxy](https://www.envoyproxy.io/) as an edge HTTP proxy. TLS is terminated on the edge and requests are routed based on their traffic splitting rules and routes traffic to the correct application.

HTTP applications scale based on the number of HTTP requests and connections. Envoy routes internal traffic inside clusters. Downstream connections support HTTP1.1 and HTTP2 and Envoy automatically detects and upgrades the connection should the client connection be upgraded. Upstream connection is defined by setting the `transport` property on the [ingress](azure-resource-manager-api-spec.md#propertiesconfiguration) object.

### Ingress configuration

Under the [ingress](azure-resource-manager-api-spec.md#propertiesconfiguration) section, you can configure the following settings:

- **Accessibility level**: You can set your container app as externally or internally accessible in the environment. An environment variable `CONTAINER_APP_ENV_DNS_SUFFIX` is used to automatically resolve the FQDN suffix for your environment.

- **Traffic split rules**: You can define traffic splitting rules between different revisions of your application.  For more information, see [Traffic splitting](traffic-splitting.md).

For more information about ingress configuration, see [Ingress in Azure Container Apps](ingress-overview.md).

### Scenarios

For more information about scenarios, see [Ingress in Azure Container Apps](ingress-overview.md).

## Portal dependencies

For every app in Azure Container Apps, there are two URLs.

Container Apps generates the first URL, which is used to access your app. See the *Application Url* in the *Overview* window of your container app in the Azure portal for the fully qualified domain name (FQDN) of your container app.

The second URL grants access to the log streaming service and the console. If necessary, you may need to add `https://azurecontainerapps.dev/` to the allowlist of your firewall or proxy.

## Ports and IP addresses

>[!NOTE]
> The subnet associated with a Container App Environment requires a CIDR prefix of `/23` or larger.

The following ports are exposed for inbound connections.

| Use | Port(s) |
|--|--|
| HTTP/HTTPS | 80, 443 |

IP addresses are broken down into the following types:

| Type | Description |
|--|--|
| Public inbound IP address | Used for app traffic in an external deployment, and management traffic in both internal and external deployments. |
| Outbound public IP | Used as the "from" IP for outbound connections that leave the virtual network. These connections aren't routed down a VPN. Outbound IPs aren't guaranteed and may change over time. Using a NAT gateway or other proxy for outbound traffic from a Container App environment is only supported on the workload profile architecture. |
| Internal load balancer IP address | This address only exists in an internal deployment. |
| App-assigned IP-based TLS/SSL addresses | These addresses are only possible with an external deployment, and when IP-based TLS/SSL binding is configured. |

## Subnet

Virtual network integration depends on a dedicated subnet. How IP addresses are allocated in a subnet and what subnet sizes are supported depends on which plan you're using in Azure Container Apps. Selecting an appropriately sized subnet for the scale of your Container Apps is important as subnet sizes can't be modified post creation in Azure.

- Consumption only architecture:
    - /23 is the minimum subnet size required for virtual network integration. 
    - Container Apps reserves a minimum of 60 IPs for infrastructure in your VNet, and the amount may increase up to 256 addresses as your container environment scales.
    - As your app scales, a new IP address is allocated for each new replica.

- Workload profiles architecture:
    - /27 is the minimum subnet size required for virtual network integration. 
    - The subnet you're integrating your container app with must be delegated to `Microsoft.App/environments`.
    - 11 IP addresses are automatically reserved for integration with the subnet. When your apps are running on workload profiles, the number of IP addresses required for infrastructure integration doesn't vary based on the scale of your container apps. 
    - More IP addresses are allocated depending on your Container App's workload profile:
        - When you're using Consumption workload profiles for your container app, IP address assignment behaves the same as when running on the Consumption only architecture. As your app scales, a new IP address is allocated for each new replica.
        - When you're using the Dedicated workload profile for your container app, each node has 1 IP address assigned.

As a Container Apps environment is created, you provide resource IDs for a single subnet.

If you're using the CLI, the parameter to define the subnet resource ID is `infrastructure-subnet-resource-id`. The subnet hosts infrastructure components and user app containers.

### Subnet Address Range Restrictions

Subnet address ranges can't overlap with the following ranges reserved by AKS:

- 169.254.0.0/16
- 172.30.0.0/16
- 172.31.0.0/16
- 192.0.2.0/24

In addition, Container Apps on the workload profiles architecture reserve the following addresses:

- 100.100.0.0/17
- 100.100.128.0/19
- 100.100.160.0/19
- 100.100.192.0/19

If you're using the Azure CLI and the [platformReservedCidr](vnet-custom-internal.md#networking-parameters) range is defined, both subnets must not overlap with the IP range defined in `platformReservedCidr`.

## Routes

User Defined Routes (UDR) and controlled egress through NAT Gateway are supported in the workload profiles architecture, which is in preview. In the Consumption only architecture, these features aren't supported.

### User defined routes (UDR) - preview

You can use UDR on the workload profiles architecture to restrict outbound traffic from your container app through Azure Firewall or other network appliances.  Configuring UDR is done outside of the Container Apps environment scope. 

:::image type="content" source="media/networking/udr-architecture.png" alt-text="Diagram of how UDR is implemented for Container Apps.":::

Important notes for configuring UDR with Azure Firewall:

- You need to allow the `MicrosoftContainerRegistry` and its dependency `AzureFrontDoor.FirstParty` service tags to your Azure Firewall. Alternatively, you can add the following FQDNs: *mcr.microsoft.com* and **.data.mcr.microsoft.com*.
- If you're using Azure Container Registry (ACR), you need to add the `AzureContainerRegistry` service tag and the **.blob.core.windows.net* FQDN in the Azure Firewall.
- If you're using [Docker Hub registry](https://docs.docker.com/desktop/allow-list/) and want to access it through the firewall, you need to add the following FQDNs to your firewall: *hub.docker.com*, *registry-1.docker.io*, and *production.cloudflare.docker.com*.
- External environments aren't supported.

Azure creates a default route table for your virtual networks upon create. By implementing a user-defined route table, you can control how traffic is routed within your virtual network. For example, you can create a UDR that routes all traffic to the firewall. For a guide on how to setup UDR with Container Apps to restrict outbound traffic with Azure Firewall, visit the [how to for Container Apps and Azure Firewall](./user-defined-routes.md).

### NAT gateway integration - preview

You can use NAT Gateway to simplify outbound connectivity for your outbound internet traffic in your virtual network on the workload profiles architecture. NAT Gateway is used to provide a static public IP address, so when you configure NAT Gateway on your Container Apps subnet, all outbound traffic from your container app is routed through the NAT Gateway's static public IP address. 

### Lock down your Container App environment

:::image type="content" source="media/networking/locked-down-network.png" alt-text="Diagram of how to fully lock down your network for Container Apps.":::

With the workload profiles architecture (preview), you can fully secure your ingress/egress networking traffic. To do so, you should use the following features:
- Create your internal container app environment on the workload profiles architecture. For steps, see [here](./workload-profiles-manage-cli.md).
- Integrate your Container Apps with an Application Gateway. For steps, see [here](./waf-app-gateway.md).
- Configure UDR to route all traffic through Azure Firewall. For steps, see [here](./user-defined-routes.md).

## DNS

-	**Custom DNS**: If your VNet uses a custom DNS server instead of the default Azure-provided DNS server, configure your DNS server to forward unresolved DNS queries to `168.63.129.16`. [Azure recursive resolvers](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) uses this IP address to resolve requests. If you don't use the Azure recursive resolvers, the Container Apps environment can't function.

-	**VNet-scope ingress**: If you plan to use VNet-scope [ingress](ingress-overview.md) in an internal Container Apps environment, configure your domains in one of the following ways:

    1. **Non-custom domains**: If you don't plan to use custom domains, create a private DNS zone that resolves the Container Apps environment's default domain to the static IP address of the Container Apps environment. You can use [Azure Private DNS](../dns/private-dns-overview.md) or your own DNS server.  If you use Azure Private DNS, create a Private DNS Zone named as the Container App Environment’s default domain (`<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io`), with an `A` record. The A record contains the name `*<DNS Suffix>` and the static IP address of the Container Apps environment.

    1. **Custom domains**: If you plan to use custom domains, use a publicly resolvable domain to [add a custom domain and certificate](./custom-domains-certificates.md#add-a-custom-domain-and-certificate) to the container app. Additionally, create a private DNS zone that resolves the apex domain to the static IP address of the Container Apps environment. You can use [Azure Private DNS](../dns/private-dns-overview.md) or your own DNS server. If you use Azure Private DNS, create a Private DNS Zone named as the apex domain, with an `A` record that points to the static IP address of the Container Apps environment.

The static IP address of the Container Apps environment can be found in the Azure portal in  **Custom DNS suffix** of the container app page or using the Azure CLI `az containerapp env list` command.

## Managed resources

When you deploy an internal or an external environment into your own network, a new resource group prefixed with `MC_` is created in the Azure subscription where your environment is hosted. This resource group contains infrastructure components managed by the Azure Container Apps platform, and shouldn't be modified. The resource group contains Public IP addresses used specifically for outbound connectivity from your environment and a load balancer. The resource group name can be configured during container app environment creation. In addition to the [Azure Container Apps billing](./billing.md), you're billed for:

- Two standard static [public IPs](https://azure.microsoft.com/pricing/details/ip-addresses/), one for ingress and one for egress. If you need more IPs for egress due to SNAT issues, [open a support ticket to request an override](https://azure.microsoft.com/support/create-ticket/).

- Two standard [Load Balancers](https://azure.microsoft.com/pricing/details/load-balancer/) if using an internal environment, or one standard [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) if using an external environment. Each load balancer has fewer than six rules. The cost of data processed (GB) includes both ingress and egress for management operations.


## Next steps

- [Deploy with an external environment](vnet-custom.md)
- [Deploy with an internal environment](vnet-custom-internal.md)
