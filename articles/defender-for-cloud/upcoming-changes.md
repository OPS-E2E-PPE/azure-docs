---
title: Important changes coming to Microsoft Defender for Cloud
description: Upcoming changes to Microsoft Defender for Cloud that you might need to be aware of and for which you might need to plan 
ms.topic: overview
ms.date: 02/09/2023
---

# Important upcoming changes to Microsoft Defender for Cloud

> [!IMPORTANT]
> The information on this page relates to pre-release products or features, which may be substantially modified before they are commercially released, if ever. Microsoft makes no commitments or warranties, express or implied, with respect to the information provided here.

On this page, you'll learn about changes that are planned for Defender for Cloud. It describes planned modifications to the product that might affect things like your secure score or workflows.

If you're looking for the latest release notes, you'll find them in the [What's new in Microsoft Defender for Cloud](release-notes.md).

## Planned changes

| Planned change | Estimated date for change |
|--|--|
| [The built-in policy [Preview]: Private endpoint should be configured for Key Vault is will be deprecated](#the-built-in-policy-preview-private-endpoint-should-be-configured-for-key-vault-will-be-deprecated) | February 2023 |
| [Three alerts in Defender for Azure Resource Manager plan will be deprecated](#three-alerts-in-defender-for-azure-resource-manager-plan-will-be-deprecated) | March 2023 |
| [Alerts automatic export to Log Analytics workspace will be deprecated](#alerts-automatic-export-to-log-analytics-workspace-will-be-deprecated) | March 2023 |
| [Deprecation and improvement of selected alerts for Windows and Linux Servers](#deprecation-and-improvement-of-selected-alerts-for-windows-and-linux-servers) | April 2023 |

### The built-in policy \[Preview]: Private endpoint should be configured for Key Vault will be deprecated

**Estimated date for change: February 2023**

The built-in policy [`[Preview]: Private endpoint should be configured for Key Vault`](https://ms.portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) is set to be deprecated and will be replaced with the [`[Preview]: Azure Key Vaults should use private link`](https://ms.portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6abeaec-4d90-4a02-805f-6b26c4d3fbe9) policy.

The related [policy definition](https://ms.portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7c1b1214-f927-48bf-8882-84f0af6588b1) will also be replaced by this new policy in all standards displayed in the regulatory compliance dashboard.

### Three alerts in Defender for Azure Resource Manager plan will be deprecated

**Estimated date for change: March 2023**

As we continue to improve the quality of our alerts, the following three alerts from the Defender for ARM plan will be deprecated:
1. `Activity from a risky IP address (ARM.MCAS_ActivityFromAnonymousIPAddresses)`
1. `Activity from infrequent country (ARM.MCAS_ActivityFromInfrequentCountry)`
1. `Impossible travel activity (ARM.MCAS_ImpossibleTravelActivity)`

You can learn more details about each of these alerts from the [alerts reference list](alerts-reference.md#alerts-resourcemanager).

In the scenario where an activity from a suspicious IP address is detected, one of the following Defender for ARM plan alerts `Azure Resource Manager operation from suspicious IP address` or `Azure Resource Manager operation from suspicious proxy IP address` will be present.

### Alerts automatic export to Log Analytics workspace will be deprecated

**Estimated date for change: March 2023**

Currently, Defender for Cloud security alerts are automatically exported to a default Log Analytics workspace on the resource level. This causes an indeterministic behavior and therefore, this feature is set to be deprecated.

You can export your security alerts to a dedicated Log Analytics workspace with the [Continuous Export](continuous-export.md#set-up-a-continuous-export) feature. 
If you have already configured continuous export of your alerts to a Log Analytics workspace, no further action is required.

### Deprecation and improvement of selected alerts for Windows and Linux Servers

**Estimated date for change: April 2023**

The security alert quality improvement process for Defender for Servers includes the deprecation of some alerts for both Windows and Linux servers. The deprecated alerts will now be sourced from and covered by Defender for Endpoint threat alerts.  

If you already have the Defender for Endpoint integration enabled, no further action is required. You may experience a decrease in your alerts volume in April 2023.

If you don't have the Defender for Endpoint integration enabled in Defender for Servers, you'll need to enable the Defender for Endpoint integration to maintain and improve your alert coverage. 

All Defender for Server customers, have full access to the Defender for Endpoint’s integration as a part of the [Defender for Servers plan](plan-defender-for-servers-select-plan.md#plan-features).  

You can learn more about [Microsoft Defender for Endpoint onboarding options](integration-defender-for-endpoint.md#enable-the-microsoft-defender-for-endpoint-integration).

You can also view the [full list of alerts](alerts-reference.md#defender-for-servers-alerts-to-be-deprecated) that are set to be deprecated.

Read the [Microsoft Defender for Cloud blog](https://techcommunity.microsoft.com/t5/microsoft-defender-for-cloud/defender-for-servers-security-alerts-improvements/ba-p/3714175).
## Next steps

For all recent changes to Defender for Cloud, see [What's new in Microsoft Defender for Cloud?](release-notes.md).
