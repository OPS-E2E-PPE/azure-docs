---
title: Configure Microsoft Sentinel Solution for SAP
description: This article shows you how to configure the deployed Microsoft Sentinel Solution for SAP
author: MSFTandrelom
ms.author: andrelom
ms.topic: how-to
ms.date: 04/27/2022
---

# Configure Microsoft Sentinel Solution for SAP

This article provides best practices for configuring the Microsoft Sentinel Solution for SAP. The full deployment process is detailed in a whole set of articles linked under [Deployment milestones](deployment-overview.md#deployment-milestones).

Deployment of the data collector agent and solution in Microsoft Sentinel provides you with the ability to monitor SAP systems for suspicious activities and identify threats. However, for best results, best practices for operating the solution strongly recommend carrying out several additional configuration steps that are very dependent on the SAP deployment.

## Deployment milestones

Track your SAP solution deployment journey through this series of articles:

1. [Deployment overview](deployment-overview.md)

1. [Deployment prerequisites](prerequisites-for-deploying-sap-continuous-threat-monitoring.md)

1. [Prepare SAP environment](preparing-sap.md)

1. [Deploy data connector agent](deploy-data-connector-agent-container.md)

1. [Deploy SAP security content](deploy-sap-security-content.md)

1. **Configure Microsoft Sentinel Solution for SAP (*You are here*)**

1. Optional deployment steps
   - [Configure auditing](configure-audit.md)
   - [Configure data connector to use SNC](configure-snc.md)

## Configure watchlists

Microsoft Sentinel Solution for SAP configuration is accomplished by providing customer-specific information in the provisioned watchlists.

> [!NOTE]
>
> After initial solution deployment, it may take some time before watchlists are populated with data.
> If you edit a watchlist and find it is empty, please wait a few minutes and retry opening the watchlist for editing.

### SAP - Systems watchlist
SAP - Systems watchlist defines which SAP Systems are present in the monitored environment. For every system, specify its SID, whether it is a production system or a dev/test environment, as well as a description.
This information is used by some analytics rules, which may react differently if relevant events appear in a Development or a Production system.

### SAP - Networks watchlist
SAP - Networks watchlist outlines all networks used by the organization. It is primarily used to identify whether or not user logons are originating from within known segments of the network, also if user logon origin changes unexpectedly.

There are a number of approaches for documenting network topology. You could define a broad range of addresses, like 172.16.0.0/16, and name it "Corporate Network", which will be good enough for tracking logons from outside that range. A more segmented approach, however, allows you better visibility into potentially atypical activity. 

For example: define the following two segments and their geographical locations:

| Segment | Location |
| ---- | ---- |
| 192.168.10.0/23 | Western Europe |
| 10.15.0.0/16 | Australia |

Now Microsoft Sentinel will be able to differentiate a logon from 192.168.10.15 (in the first segment) from a logon from 10.15.2.1 (in the second segment) and alert you if such behavior is identified as atypical.

### Sensitive data watchlists

- SAP - Sensitive Function Modules
- SAP - Sensitive Tables
- SAP - Sensitive ABAP Programs
- SAP - Sensitive Transactions
- SAP - Critical Authorizations

All of these watchlists identify sensitive actions or data that can be carried out or accessed by users. Several well-known operations, tables and authorizations have been pre-configured in the watchlists, however we recommend you consult with the SAP BASIS team to identify which operations, transactions, authorizations and tables are considered to be sensitive in your SAP environment.

### User master data watchlists

- SAP - Sensitive Profiles
- SAP - Sensitive Roles
- SAP - Privileged Users

Microsoft Sentinel Solution for SAP uses User Master data gathered from SAP systems to identify which users, profiles, and roles should be considered sensitive. Some sample data is included in the watchlists, though we recommend you consult with the SAP BASIS team to identify sensitive users, roles and profiles and populate the watchlists accordingly.

## Start enabling analytics rules
By default, all analytics rules provided in the Microsoft Sentinel Solution for SAP are disabled. When you install the solution, it's best if you don't enable all the rules at once so you don't end up with a lot of noise. Instead, use a staged approach, enabling rules over time, ensuring you are not receiving noise or false positives. Ensure alerts are operationalized, that is, have a response plan for each of the alerts. We consider the following rules to be easiest to implement, so best to start with them:

1. Deactivation of Security Audit Log
1. Client Configuration Change
1. Change in Sensitive Privileged User
1. Client configuration change
1. Sensitive privileged user logon
1. Sensitive privileged user makes a change in other
1. Sensitive privilege user password change and login
1. System configuration change
1. Brute force (RFC)
1. Function module tested

