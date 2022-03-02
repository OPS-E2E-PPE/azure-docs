---
title: Collect the required information for a service
titleSuffix: Azure Private 5G Core Preview
description: In this how-to guide, you'll learn how to collect all the required information to configure a service for Azure Private 5G Core Preview.
author: djrmetaswitch
ms.author: drichards
ms.service: private-5g-core
ms.topic: how-to
ms.date: 01/16/2022
ms.custom: template-how-to
---

# Collect the required information for a service for Azure Private 5G Core Preview

A *service* is a set of quality of service (QoS) characteristics you want to offer SIMs. For example, you may want to configure a service that provides higher bandwidth limits for particular traffic. You can also use services to block particular traffic types or traffic from specific sources.

Each service has a set of rules to identify the service data flows (SDFs) to which the QoS characteristics should be applied. For more information, see [Policy control](policy-control.md). 

In this how-to guide, you'll learn how to collect all the required information to configure a service for Azure Private 5G Core Preview.

You'll enter each value you collect into its corresponding field (given in the **Azure portal field name** columns in the tables below) as part of the procedure in [Configure a service for Azure Private 5G Core Preview - Azure portal](configure-service-azure-portal.md).

## Prerequisites

Read [Policy control](policy-control.md) and make sure you're familiar with Azure Private 5G Core policy control configuration.

## Collect top-level setting values

Each service has many top-level settings that determine its name and the QoS characteristics it will offer.

Collect each of the values in the table below for your service.

| Value | Azure portal field name | 
|--|--|
| The name of the service. This name must only contain alphanumeric characters, dashes, or underscores. You also must not use any of the following reserved strings: *default*; *requested*; *service*. | **Service name** |
| A precedence value that the packet core instance must use to decide between services when identifying the QoS values to offer. This value must be an integer between 0 and 255 and must be unique among all services configured on the packet core instance. A lower value means a higher priority. | **Service precedence** |
| The maximum bit rate (MBR) for uplink traffic (traveling away from user equipment (UEs)) across all SDFs that match data flow policy rules configured on this service. The MBR must be given in the following form: `<Quantity>` `<Unit>` </br></br>`<Unit>` must be one of the following: </br></br>- *bps* </br>- *Kbps* </br>- *Mbps* </br>- *Gbps* </br>- *Tbps* </br></br>`<Quantity>` is the quantity of your chosen unit. </br></br>For example, `10 Mbps`. | **Maximum bit rate (MBR) - Uplink** | 
| The maximum bit rate (MBR) for downlink traffic (traveling towards UEs) across all SDFs that match data flow policy rules configured on this service. The MBR must be given in the following form: `<Quantity>` `<Unit>` </br></br>`<Unit>` must be one of the following: </br></br>- *bps* </br>- *Kbps* </br>- *Mbps* </br>- *Gbps* </br>- *Tbps* </br></br>`<Quantity>` is the quantity of your chosen unit. </br></br>For example, `10 Mbps`. | **Maximum bit rate (MBR) - Downlink** | 
| The default QoS Flow Allocation and Retention Policy (ARP) priority level for this service. Flows with a higher ARP priority level preempt flows with a lower ARP priority level. The ARP priority level must be an integer between 1 (highest priority) and 15 (lowest priority). See 3GPP TS 23.501 for a full description of the ARP parameters. | **Allocation and Retention Priority level** |
| The default 5G QoS Indicator (5QI) value for this service. The 5QI value identifies a set of 5G QoS characteristics that control QoS forwarding treatment for QoS Flows. See 3GPP TS 23.501 for a full description of the 5QI parameter. </br></br>We recommend you choose a 5QI value that corresponds to a non-GBR QoS Flow (as described in 3GPP TS 23.501). Non-GBR QoS Flows are in the following ranges: 5-9; 69-70; 79-80.</br></br>You can also choose a non-standardized 5QI value.</p><p>Azure Private 5G Core doesn't support 5QI values corresponding GBR or delay-critical GBR QoS Flows. Don't use a value in any of the following ranges: 1-4; 65-67; 71-76; 82-85. | **5G QoS Indicator (5QI)** |
| The default QoS Flow preemption capability for QoS Flows for this service. The preemption capability of a QoS Flow controls whether it can preempt another QoS Flow with a lower priority level. You can choose from the following values: </br></br>- **May not preempt** </br>- **May preempt** </br></br>See 3GPP TS 23.501 for a full description of the ARP parameters. | **Preemption capability** |
| The default QoS Flow preemption vulnerability for QoS Flows for this service. The preemption vulnerability of a QoS Flow controls whether it can be preempted another QoS Flow with a higher priority level. You can choose from the following values: </br></br>- **Preemptable** </br>- **Not preemptable** </br></br>See 3GPP TS 23.501 for a full description of the ARP parameters. | **Preemption vulnerability** |

## Data flow policy rule(s)

Each service must have one or more data flow policy rules. Data flow policy rules identify the service data flows (SDFs) to which the service should be applied. They can also be used to block certain SDFs.

For each data flow policy rule, take the following steps:

- Collect the values in [Collect data flow policy rule values](#collect-data-flow-policy-rule-values) to determine whether SDFs matching this data flow policy rule will be allowed or blocked, and how this data flow policy rule should be prioritized against other data flow policy rules.
- Collect the values in [Collect data flow template values](#collect-data-flow-template-values) for one or more data flow templates to use for this data flow policy rule. Data flow templates provide the packet filters the packet core instance will use to match on SDFs.

### Collect data flow policy rule values

Collect the values in the table below for each data flow policy rule you want to use on this service.

| Value | Azure portal field name |
|--|--|
| The name of the data flow policy rule. This name must only contain alphanumeric characters, dashes, or underscores. It must not match any other rule configured on the same service. You also must not use any of the following reserved strings: *default*; *requested*; *service*. | **Rule name** |
| A precedence value that the packet core instance must use to decide between data flow policy rules. This value must be an integer between 0 and 255 and must be unique among all data flow policy rules configured on the packet core instance. A lower value means a higher priority. | **Policy rule precedence** |
| A traffic control setting to determine whether flows that match a data flow template on this data flow policy rule are permitted. Choose one of the following values: </br></br>- **Enabled** - Matching flows are permitted. </br>- **Blocked** - Matching flows are blocked. | **Traffic control** |

### Collect data flow template values

Collect the following values for each data flow template you want to use for a particular data flow policy rule.

| Value | Azure portal field name |
|--|--|
| The name of the data flow template. This name must only contain alphanumeric characters, dashes, or underscores. It must not match any other template configured on the same rule. You also must not use any of the following reserved strings: *default*; *requested*; *service*. | **Template name** |
| The protocol(s) allowed for this flow. </br></br>If you want to allow the flow to use any protocol within the Internet Protocol suite, you can set this field to **All**.</br></br>If you want to allow a selection of protocols, you can select them from the list displayed in the field. If a protocol isn't in the list, you can specify it by entering its corresponding IANA Assigned Internet Protocol Number, as described in the [IANA website](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml). For example, for IGMP, you must use 2. | **Protocols**  |
| The direction of this flow. Choose one of the following values: </br></br>- **Uplink** - traffic flowing away from UEs. </br>- **Downlink** - traffic flowing towards UEs.</br>-  **Bidirectional** - traffic flowing in both directions. | **Direction** |
| The remote IP address(es) to which UEs will connect for this flow. </br></br>If you want to allow connections on any IP address, you must use the value `any`. </br></br>Otherwise, you must provide each remote IP address or IP address range to which the packet core instance will connect for this flow. Provide these IP addresses in CIDR notation, including the netmask (for example, `192.0.2.0/24`). </br></br>Provide a comma-separated list of IP addresses and IP address ranges. For example: </br></br>`192.0.2.54/32, 198.51.100.0/24` | **Remote IPs** |
| The remote port(s) to which UEs will connect for this flow. You can specify one or more ports or port ranges. Port ranges must be specified as `<FirstPort>-<LastPort>`. </br></br>This setting is optional. If you don't specify a value, the packet core instance will allow connections for all remote ports. </br></br>Provide a comma-separated list of your chosen ports and port ranges. For example: </br></br>`8080, 8082-8085` | **Ports** |

## Next steps

- [Configure a service for Azure Private 5G Core Preview - Azure portal](configure-service-azure-portal.md)
