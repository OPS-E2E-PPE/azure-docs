---
title: 'Injecting default route to Azure VMware Solution'
description: Learn about how to advertise a default route to Azure VMware Solution with Azure Route Server.
services: route-server
author: erjosito
ms.service: route-server
ms.topic: conceptual
ms.date: 02/03/2022
ms.author: jomore
---

# Injecting a default route to Azure VMware Solution

[Azure VMware Solution](../azure-vmware/introduction.md) is an Azure service where native VMware vSphere workloads run and communicate with other Azure services. This communication happens over ExpressRoute, and Azure Route Server can be used to modify the default behavior of Azure VMware Solution networking. For example, a default route can be injected from a Network Virtual Appliance (NVA) in Azure to attract traffic from AVS and inspect it before sending it out to the public Internet, or to analyze traffic between AVS and the on-premises network.

Additionally, similar designs can be used to interconnect AVS and on-premises networks sending traffic through an NVA, either because traffic inspection is not required or because ExpressRoute Global Reach is not available in the relevant regions.

## Topology

The following diagram describes a basic hub and spoke topology connected to an AVS cloud and to an on-premises network through ExpressRoute. The diagram shows how the default route (`0.0.0.0/0`) is originated by the NVA in Azure, and propagated by Azure Route Server to Azure VMware Solution through ExpressRoute.

:::image type="content" source="./media/scenarios/vmware-solution-default.png" alt-text="Diagram of Azure VMware Solution with Route Server and default route.":::

> [!IMPORTANT]
> The default route advertised by the NVA will be propagated to the on-premises network as well, so it needs to be filtered out in the customer routing environment.

Communication between Azure VMware Solution and the on-premises network will typically happen over ExpressRoute Global Reach, as described in [Peer on-premises environments to Azure VMware Solution](../azure-vmware/tutorial-expressroute-global-reach-private-cloud.md).

## Communication between Azure VMware Solution and the on-premises network via NVA

There are two main scenarios for this pattern:

- ExpressRoute Global Reach might not be available on a particular region to interconnect the ExpressRoute circuits of AVS and the on-premises network.
- Some organizations might have the requirement to send traffic between AVS and the on-premises network through an NVA (typically a firewall).

If both ExpressRoute circuits (to AVS and to on-premises) are terminated in the same ExpressRoute gateway, you could think that the gateway is going to route packets across them. However, an ExpressRoute gateway is not designed to do that. Instead, you need to hairpin the traffic to a Network Virtual Appliance that is able to route the traffic. To that purpose, the NVA should advertise a superset of the AVS and on-premises prefixes, as the following diagram shows:

:::image type="content" source="./media/scenarios/vmware-solution-to-on-premises-hairpin.png" alt-text="Diagram of AVS to on-premises communication with Route Server in a single region.":::

As the diagram shows, the NVA needs to advertise a more generic (less specific) prefix that both the on-premises network and AVS. You need to be careful with this approach, since the NVA might be potentially attracting traffic that it should not (since it is advertising wider ranges, in the example above the whole `10.0.0.0/8` network).

If two regions are involved, you would need an NVA in each region, and both NVAs would exchange the routes they learn from their respective Azure Route Servers via BGP and some sort of encapsulation protocol such as VXLAN or IPsec, as the following diagram shows.

:::image type="content" source="./media/scenarios/vmware-solution-to-on-premises.png" alt-text="Diagram of AVS to on-premises communication with Route Server in two regions.":::

The reason why encapsulation is needed is because the NVA NICs would learn the routes from ExpressRoute or from the Route Server, so they would send packets that need to be routed to the other NVA in the wrong direction (potentially creating a routing loop returning the packets to the local NVA).

## Next steps

* [Learn how Azure Route Server works with ExpressRoute](expressroute-vpn-support.md)
* [Learn how Azure Route Server works with a network virtual appliance](resource-manager-template-samples.md)
