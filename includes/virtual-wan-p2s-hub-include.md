---
ms.author: cherylmc
author: cherylmc
ms.date: 05/25/2022
ms.service: virtual-wan
ms.topic: include
---

1. Go to the virtual WAN that you created. On the virtual WAN page left pane, under the **Connectivity**, select **Hubs**.

1. On the **Hubs** page, select **+New Hub** to open the **Create virtual hub** page.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="Screenshot of new hub.":::

1. On the **Create virtual hub** page, view the **Basics** tab.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="Screenshot of create virtual hub.":::

1. On the **Basics** tab, configure the following settings:

   * **Region**: This setting was previously referred to as location. It's the region in which you want to create your virtual hub.
   * **Name**: The name by which you want the virtual hub to be known.
   * **Hub private address space**: The hub's address range in CIDR notation. The minimum address space is /24 to create a hub.
   * **Virtual hub capacity**: Select from the dropdown. For more information, see [Virtual hub settings](../articles/virtual-wan/hub-settings.md).
   * **Hub routing preference**: This field is only available as part of the virtual hub routing preference preview and can only be viewed in the [preview portal](https://portal.azure.com/?feature.customRouterAsn=true&feature.virtualWanRoutingPreference=true#home). See [Virtual hub routing preference](../articles/virtual-wan/about-virtual-hub-routing-preference.md) for more information.
   * **Router ASN**: Set the Autonomous System Number for the virtual hub router. You can use any ASN number except numbers that are reserved by [Azure or IANA](../articles/vpn-gateway/vpn-gateway-bgp-overview.md#what-asns-autonomous-system-numbers-can-i-use).

1. Click the **Point to site** tab to open the configuration page for point-to-site. To view the point to site settings, click **Yes**.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="Screenshot of virtual hub configuration with point-to-site selected.":::

1. Configure the following settings:

   * **Gateway scale units** - This represents the aggregate capacity of the User VPN gateway. If you select 40 or more gateway scale units, plan your client address pool accordingly. For information about how this setting impacts the client address pool, see [About client address pools](../articles/virtual-wan/about-client-address-pools.md). For information about gateway scale units, see the [FAQ](../articles/virtual-wan/virtual-wan-faq.md#for-user-vpn-point-to-site--how-many-clients-are-supported).
   * **Point to site configuration** - Select the User VPN configuration that you created in a previous step.
   * **Routing preference** - Azure routing preference enables you to choose how your traffic routes between Azure and the Internet. You can choose to route traffic either via the Microsoft network, or, via the ISP network (public internet). These options are also referred to as cold potato routing and hot potato routing, respectively. The public IP address in Virtual WAN is assigned by the service based on the routing option selected. For more information about routing preference via Microsoft network or ISP, see the [Routing preference](../articles/virtual-network/ip-services/routing-preference-overview.md) article.
   * **Use Remote/On-premises RADIUS server** - When a Virtual WAN User VPN gateway is configured to use RADIUS-based authentication, the User VPN gateway acts as a proxy and sends RADIUS access requests to your RADIUS server. The "Use Remote/On-premises RADIUS server" setting is disabled by default, meaning the User VPN gateway will only be able to forward authentication requests to RADIUS servers in virtual networks connected to the gateway's hub. Enabling the setting will enable the User VPN gateway to authenticate with RADIUS servers connected to remote hubs or deployed on-premises.

      > [!NOTE]
      > The Remote/On-premises RADIUS server setting and related proxy IPs are only used if the Gateway is configured to use RADIUS-based authentication. If the Gateway is not configured to use RADIUS-based authentication, this setting will be ignored.
      >

      You must turn on "Use Remote/On-premises RADIUS server" if users will connect to the global VPN profile instead of the hub-based profile. For more information, see [global and hub-level profiles](../articles/virtual-wan/global-hub-profile.md).

      After you create the User VPN gateway, go to gateway and note the RADIUS proxy IPs field. The RADIUS proxy IPs are the source IPs of the RADIUS packets the User VPN gateway sends to your RADIUS server. Therefore, your RADIUS server needs to be configured to accept authentication requests from the RADIUS proxy IPs. If the RADIUS proxy IPs field is blank or none, configure the RADIUS server to accept authentication requests from the hub's address space.  

      :::image type="content" source="media/virtual-wan-p2s-hub/radius-proxy-ips.png" alt-text="Screenshot of User V P N Config with RADIUS Proxy I Ps." lightbox="media/virtual-wan-p2s-hub/radius-proxy-ips.png":::

   * **Client address pool** -  The address pool from which IP addresses will be automatically assigned to VPN clients. For more information, see [About client address pools](../articles/virtual-wan/about-client-address-pools.md).
   * **Custom DNS Servers** - The IP address of the DNS server(s) the clients will use. You can specify up to 5.

1. Select **Review + create** to validate your settings.

1. When validation passes, select **Create**. Creating a hub can take 30 minutes or more to complete.