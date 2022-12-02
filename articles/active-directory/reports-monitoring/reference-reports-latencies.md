---
title: Azure Active Directory reporting latencies | Microsoft Docs
description: Learn about the amount of time it takes for reporting events to show up in your Azure portal
services: active-directory
author: shlipsey3
manager: amycolannino
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/31/2022
ms.author: sarahlipsey
ms.reviewer: dhanyahk

ms.collection: M365-identity-device-management
---

# Azure Active Directory reporting latencies

Latency is the amount of time it takes for Azure Active Directory (Azure AD) reporting data to show up in the [Azure portal](https://portal.azure.com). This article lists the expected latency for the different types of reports. 

## Activity reports

There are two types of activity reports:

- [Sign-ins](concept-sign-ins.md) – Provides information about the usage of managed applications and user sign-in activities
- [Audit logs](concept-audit-logs.md) - Provides system activity information about users and groups, managed applications and directory activities

The following table lists the latency information for activity reports. 

> [!NOTE]
> **Latency (95th percentile)** refers to the time by which 95% of the logs will be reported, and **Latency (99th percentile)** refers to the time by which 99% of the logs will be reported. 
>

| Report | Latency (95th percentile) |Latency (99th percentile)|
| :-- | --- | --- |
| Audit logs | 2 mins  | 5 mins  |
| Sign-ins | 2 mins  | 5 mins |

### How soon can I see activities data after getting a premium license?

When you upgrade to Azure AD P1 or P2 from a free version of Azure AD, the reports associated with P1 and P2 will begin to retain and display data from your tenant. You should expect a delay of roughly 24 hours from when you upgrade your tenant before all premium reporting features show data. Many premium reporting features will only begin retaining data after this 24 hour period following your upgrade to P1 or P2. 

## Security reports

There are two types of security reports:

- [Risky sign-ins](../identity-protection/overview-identity-protection.md) - A risky sign-in is an indicator for a sign-in attempt that might have been performed by someone who isn't the legitimate owner of a user account. 
- [Users flagged for risk](../identity-protection/overview-identity-protection.md) - A risky user is an indicator for a user account that might have been compromised. 

The following table lists the latency information for security reports.

| Report | Minimum | Average | Maximum |
| :-- | --- | --- | --- |
| Users at risk          | 5 minutes   | 15 minutes  | 2 hours  |
| Risky sign-ins         | 5 minutes   | 15 minutes  | 2 hours  |

## Risk detections

Azure AD uses adaptive machine learning algorithms and heuristics to detect suspicious actions that are related to your user accounts. Each detected suspicious action is stored in a record called a **risk detection**.

The following table lists the latency information for risk detections.

| Report | Minimum | Average | Maximum |
| :-- | --- | --- | --- |
| Sign-ins from anonymous IP addresses |5 minutes |15 Minutes |2 hours |
| Sign-ins from unfamiliar locations |5 minutes |15 Minutes |2 hours |
| Users with leaked credentials |2 hours |4 hours |8 hours |
| Impossible travel to atypical locations |5 minutes |1 hour |8 hours  |
| Sign-ins from infected devices |2 hours |4 hours |8 hours  |
| Sign-ins from IP addresses with suspicious activity |2 hours |4 hours |8 hours  |


## Next steps

* [Azure AD reports overview](overview-reports.md)
* [Programmatic access to Azure AD reports](concept-reporting-api.md)
* [Azure Active Directory risk detections](../identity-protection/overview-identity-protection.md)
