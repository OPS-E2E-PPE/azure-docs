---
title: Microsoft Sentinel in the Microsoft Defender portal
description: Learn about changes in the Microsoft Defender portal with the integration of Microsoft Sentinel.
author: cwatson-cat
ms.author: cwatson
ms.topic: conceptual
ms.date: 04/11/2024
appliesto: 
    - Microsoft Sentinel in the Microsoft Defender portal
ms.collection: usx-security
---

# Microsoft Sentinel in the Microsoft Defender portal (preview)

Microsoft Sentinel is available as part of the public preview for the unified security operations platform in the Microsoft Defender portal. For more information, see:

- [Unified security operations platform with Microsoft Sentinel and Defender XDR](https://aka.ms/unified-soc-announcement)
- [Connect Microsoft Sentinel to Microsoft Defender XDR](/microsoft-365/security/defender/microsoft-sentinel-onboard)

 This article describes the Microsoft Sentinel experience in the Microsoft Defender portal.
> [!IMPORTANT]
> Information in this article relates to a prerelease product which may be substantially modified before it's commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.

## New and improved capabilities

The following table describes the new or improved capabilities available in the Defender portal with the integration of Microsoft Sentinel and Defender XDR.

|Capabilities |Description |
|---------|---------|
|Advanced hunting | Query from a single portal across different data sets to make hunting more efficient and remove the need for context-switching. View and query all data including data from Microsoft security services and Microsoft Sentinel. Use all your existing Microsoft Sentinel workspace content, including queries and functions.<br><br>  For more information, see [Advanced hunting in the Microsoft Defender portal](https://go.microsoft.com/fwlink/p/?linkid=2264410).|
|Attack disrupt      |  Deploy automatic attack disruption for SAP with both the unified security operations platform and the Microsoft Sentinel solution for SAP applications. For example, contain compromised assets by locking suspicious SAP users in case of a financial process manipulation attack.  <br><br>Attack disruption capabilities for SAP are available in the Defender portal only. To use attack disruption for SAP, update your data connector agent version and ensure that the relevant Azure role is assigned to your agent's identity. <br><br>  For more information, see [Automatic attack disruption for SAP (Preview)](sap/deployment-attack-disrupt.md).  |
|Unified entities| Entity pages for devices, users, IP addresses, and Azure resources in the Defender portal display information from Microsoft Sentinel and Defender data sources. These entity pages give you an expanded context for your investigations of incidents and alerts in the Defender portal.<br><br>For more information, see [Investigate entities with entity pages in Microsoft Sentinel](/azure/sentinel/entity-pages).|
|Unified incidents| Manage and investigate security incidents in a single location and from a single queue in the Defender portal. Incidents include:<br>- Data from the breadth of sources<br>- AI analytics tools of security information and event management (SIEM)<br>- Context and mitigation tools offered by extended detection and response (XDR) <br><br> For more information, see [Incident response in the Microsoft Defender portal](/microsoft-365/security/defender/incidents-overview).|


## Capability differences between portals

Most Microsoft Sentinel capabilities are available in both the Azure and Defender portals. In the Defender portal, some Microsoft Sentinel experiences open out to the Azure portal for you to complete a task.

This section covers the Microsoft Sentinel capabilities or integrations in the unified security operations platform that are only available in either the Azure portal or Defender portal or other significant differences between the portals. It excludes the Microsoft Sentinel experiences that open the Azure portal from the Defender portal.

### Defender portal only

The following capabilities are only available in the Defender portal.

|Capability  |Learn more  |
|---------|---------|
|Attack disruption for SAP  |  [Automatic attack disruption in the Microsoft Defender portal](/microsoft-365/security/defender/automatic-attack-disruption)         |


### Azure portal only

The following capabilities are only available in the Azure portal.

|Capability  |Learn more  |
|---------|---------|
|Add entities to threat intelligence from incidents | [Add entity to threat indicators](add-entity-to-threat-intelligence.md)   |
| Automation | Some automation procedures are available only in the Azure portal. <br><br>Other automation procedures are the same in the Defender and Azure portals, but differ in the Azure portal between workspaces that are onboarded to the unified security operations platform and workspaces that aren't.  <br><br>For more information, see [Automation with the unified security operations platform](automation.md#automation-with-the-unified-security-operations-platform). |
| Hunt using bookmarks | [Bookmarks](/azure/sentinel/bookmarks) aren't supported in the advanced hunting experience in the Microsoft Defender portal. In the Defender portal, they are supported in the **Microsoft Sentinel > Threat management > Hunting**. |
|Tasks   |     [Use tasks to manage incidents in Microsoft Sentinel](incident-tasks.md)    |

### Other portal differences

The following table describes the significant differences between the portals that you might notice after you onboard Microsoft Sentinel to the unified security operations platform.

|Feature area  |Description  |
|---------|---------|
|Data connectors |In the Defender portal, after you onboard Microsoft Sentinel, the following data connectors that are part of the unified security operations platform aren't shown in the **Data connectors** page:<br><br>- Microsoft Defender for Cloud Apps<br>- Microsoft Defender for Endpoint<br>- Microsoft Defender for Identity<br>- Microsoft Defender for Office 365 (Preview)<br>- Microsoft Defender XDR<br>- Subscription-based Microsoft Defender for Cloud (Legacy)<br>- Tenant-based Microsoft Defender for Cloud (Preview)<br><br>In the Azure portal, these data connectors are still listed with the installed data connectors in Microsoft Sentinel.|

## Quick reference

Some Microsoft Sentinel capabilities, like the unified incident queue, are integrated with Microsoft Defender XDR in the unified security operations platform. Many other Microsoft Sentinel capabilities are available in the **Microsoft Sentinel** section of the Defender portal.

The following image shows the **Microsoft Sentinel** menu in the Defender portal:

:::image type="content" source="media/microsoft-sentinel-defender-portal/navigation-defender-portal.png" alt-text="Screenshot of the Defender portal left navigation with the Microsoft Sentinel section." lightbox="media/microsoft-sentinel-defender-portal/navigation-defender-portal.png":::

The following sections describe where to find Microsoft Sentinel features in the Defender portal. The sections are organized as Microsoft Sentinel is in the Azure portal.

### General

The following table lists the changes in navigation between the Azure and Defender portals for the **General** section in the Azure portal.

|Azure portal  |Defender portal  |
|---------|---------|
|Overview    |  Overview        |
|Logs     |     Investigation & response > Hunting > Advanced hunting    |
|News & guides     |  Not available      |
|Search     | Microsoft Sentinel > Search        |


### Threat management

The following table lists the changes in navigation between the Azure and Defender portals for the **Threat management**  section in the Azure portal.

|Azure portal  |Defender portal  |
|---------|---------|
|Incidents    |   Investigation & response > Incidents & alerts > Incidents      |
|Workbooks     | Microsoft Sentinel > Threat management> Workbooks      |
|Hunting     | Microsoft Sentinel > Threat management >  Hunting      |
|Notebooks     | Microsoft Sentinel > Threat management >  Notebooks         |
|Entity behavior     | *User entity page:* Assets > Identities > *{user}* > Sentinel events<br>*Device entity page:* Assets > Devices > *{device}* > Sentinel events<br><br>Also, find the entity pages for the user, device, IP, and Azure resource entity types from incidents and alerts as they appear.       |
|Threat intelligence    | Microsoft Sentinel > Threat management > Threat intelligence        |
|MITRE ATT&CK|Microsoft Sentinel > Threat management > MITRE ATT&CK  |


### Content management

The following table lists the changes in navigation between the Azure and Defender portals for the **Content management** section in the Azure portal.

|Azure portal  |Defender portal  |
|---------|---------|
|Content hub    |  Microsoft Sentinel > Content management > Content hub      |
|Repositories     |    Microsoft Sentinel > Content management > Repositories     |
|Community   |   Not available      |

### Configuration

The following table lists the changes in navigation between the Azure and Defender portals for the **Configuration** section in the Azure portal.

|Azure portal  |Defender portal  |
|---------|---------|
|Workspace manager  |  Not available       |
|Data connectors     | Microsoft Sentinel > Configuration > Data connectors        |
|Analytics     |  Microsoft Sentinel > Configuration > Analytics       |
|Watchlists    |      Microsoft Sentinel > Configuration > Watchlists    |
|Automation   |   Microsoft Sentinel > Configuration > Automation       |
|Settings   | System > Settings > Microsoft Sentinel       |

## Related content

- [Connect Microsoft Sentinel to Microsoft Defender XDR](/microsoft-365/security/defender/microsoft-sentinel-onboard)
- [Microsoft Defender XDR documentation](/microsoft-365/security/defender)
