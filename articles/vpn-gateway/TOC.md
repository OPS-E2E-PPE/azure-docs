# [VPN Gateway Documentation](index.md)

# Overview
## [About VPN Gateway](vpn-gateway-about-vpngateways.md)

# Tutorials
## [Create and manage a VPN gateway](vpn-gateway-tutorial-create-gateway-powershell.md)

# Samples
## [Create a VPN gateway](./scripts/vpn-gateway-sample-create-vpn-gateway-powershell.md)
## [Point-to-Site - RADIUS authentication](./scripts/vpn-gateway-sample-point-to-site-radius-authentication-powershell.md)
## [Point-to-Site - certificate authentication](./scripts/vpn-gateway-sample-point-to-site-certificate-authentication-powershell.md)
## [Site-to-Site](./scripts/vpn-gateway-sample-site-to-site-powershell.md)

# Concepts
## [Planning and design for VPN Gateway](vpn-gateway-plan-design.md)
## [About VPN Gateway settings](vpn-gateway-about-vpn-gateway-settings.md)
## [About VPN devices](vpn-gateway-about-vpn-devices.md)
## [About cryptographic requirements](vpn-gateway-about-compliance-crypto.md)
## [About BGP and VPN Gateway](vpn-gateway-bgp-overview.md)
## [About highly available connections](vpn-gateway-highlyavailable.md)
## [About Point-to-Site connections](point-to-site-about.md)
## [About Point-to-Site routing](vpn-gateway-about-point-to-site-routing.md)

# How-to guides
## Configure Site-to-Site connections
### [Azure portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
## [Download VPN device configuration scripts](vpn-gateway-download-vpndevicescript.md)
## Configure Point-to-Site connections - native Azure certificate authentication
### Configure a P2S VPN
#### [Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
### Generate self-signed certificates
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [Create and install VPN client configuration files](point-to-site-vpn-client-configuration-azure-cert.md)
### [Install client certificates](point-to-site-how-to-vpn-client-install-azure-cert.md)
## Configure Point-to-Site connections - RADIUS authentication
### Configure a P2S VPN
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [Create and install VPN client configuration files](point-to-site-vpn-client-configuration-radius.md)
### [Integrate P2S VPN RADIUS authentication with NPS server](vpn-gateway-radiuis-mfa-nsp.md)
## Configure VNet-to-VNet connections
### [Azure portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
## Configure a VNet-to-VNet connection between deployment models
### [Azure portal](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Configure Site-to-Site and ExpressRoute coexisting connections
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Configure multiple Site-to-Site connections
### [Azure portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
## Connect multiple policy-based VPN devices
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Configure IPsec/IKE policies on connections
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Configure highly available active-active connections
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)

## Routing, BGP, and VNet Peering

### Configure BGP for a VPN gateway
#### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
#### [Azure CLI](bgp-how-to-cli.md)

### Configure forced tunneling
#### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
#### [Azure PowerShell (classic)](vpn-gateway-about-forced-tunneling.md)

### [Configure gateway transit for VNet peering](vpn-gateway-peering-gateway-transit.md)

## Modify local network gateway settings
### [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)

## Create a route-based VPN gateway
### [Azure portal](create-routebased-vpn-gateway-portal.md)
### [Azure PowerShell](create-routebased-vpn-gateway-powershell.md)
### [Azure CLI](create-routebased-vpn-gateway-cli.md)
## [Verify a VPN gateway connection](vpn-gateway-verify-connection-resource-manager.md)
## [Reset a VPN gateway](vpn-gateway-resetgw-classic.md)
## Delete a VPN gateway
### [Azure portal](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
## [Gateway SKUs (legacy)](vpn-gateway-about-skus-legacy.md)
## Configure third-party VPN devices
### [Overview & Azure configuration](vpn-gateway-3rdparty-device-config-overview.md)
### [Sample: Cisco ASA device (IKEv2/no BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)

## [Troubleshoot](vpn-gateway-troubleshoot.md)
### [Community-suggested VPN or firewall device settings](vpn-gateway-third-party-settings.md)
### [Configure and validate VNet or VPN connections](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)
### [Validate VPN throughput to a VNet](vpn-gateway-validate-throughput-to-vnet.md)
### Point-to-Site connections
#### [Point-to-Site connection problems](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
#### [Point-to-Site connection problems - Mac OS X VPN client](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
### Site-to-Site connection issues
#### [Site-to-Site connections](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)
#### [Site-to-Site connection disconnects intermittently](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)

## Classic deployment model articles
### [Configure a Site-to-Site connection](vpn-gateway-howto-site-to-site-classic-portal.md)
### [Configure a Point-to-Site connection](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### [Configure a VNet-to-VNet connection](vpn-gateway-howto-vnet-vnet-portal-classic.md)
### [Configure forced tunneling](vpn-gateway-about-forced-tunneling.md)
### [Delete a VPN gateway](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
### [Configure multiple S2S connections](vpn-gateway-multi-site.md)
### [Configure a VPN gateway](vpn-gateway-configure-vpn-gateway-mp.md)
### [Classic to Resource Manager migration](vpn-gateway-classic-resource-manager-migration.md)

# Reference
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (classic)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (classic)](https://msdn.microsoft.com/library/jj154113)
## [Azure CLI](/cli/azure/network/vnet-gateway)

# Resources
## [VPN Gateway FAQ](vpn-gateway-vpn-faq.md)
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog](https://azure.microsoft.com/blog/topics/networking)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Subscription and service limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## [Pricing](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [Pricing calculator](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [Videos](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
