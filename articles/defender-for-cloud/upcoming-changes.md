---
title: Important changes coming to Microsoft Defender for Cloud
description: Upcoming changes to Microsoft Defender for Cloud that you might need to be aware of and for which you might need to plan 
ms.topic: overview
ms.date: 04/20/2023
---

# Important upcoming changes to Microsoft Defender for Cloud

> [!IMPORTANT]
> The information on this page relates to pre-release products or features, which may be substantially modified before they are commercially released, if ever. Microsoft makes no commitments or warranties, express or implied, with respect to the information provided here.

On this page, you'll learn about changes that are planned for Defender for Cloud. It describes planned modifications to the product that might affect things like your secure score or workflows.

If you're looking for the latest release notes, you'll find them in the [What's new in Microsoft Defender for Cloud](release-notes.md).

## Planned changes

| Planned change | Estimated date for change |
|--|--|
| [Deprecation of legacy compliance standards across cloud environments](#deprecation-of-legacy-compliance-standards-across-cloud-environments) | April 2023 |
| [Multiple changes to identity recommendations](#multiple-changes-to-identity-recommendations) | May 2023 |
| [DevOps Resource Deduplication for Defender for DevOps](#devops-resource-deduplication-for-defender-for-devops) | June 2023 |

### Deprecation of legacy compliance standards across cloud environments

**Estimated date for change: April 2023**

We're announcing the full deprecation of support of [PCI DSS](/azure/compliance/offerings/offering-pci-dss) standard/initiative in Azure China 21Vianet.

Legacy PCI DSS v3.2.1 and legacy SOC TSP are set to be fully deprecated and replaced by [SOC 2 Type 2](/azure/compliance/offerings/offering-soc-2) initiative and [PCI DSS v4](/azure/compliance/offerings/offering-pci-dss) initiative.
Learn how to [Customize the set of standards in your regulatory compliance dashboard](update-regulatory-compliance-packages.md).

#### Deprecation of identity recommendations V1

The following security recommendations will be deprecated as part of this change:

| Recommendation | Assessment Key |
|--|--|
| MFA should be enabled on accounts with owner permissions on subscriptions | 94290b00-4d0c-d7b4-7cea-064a9554e681 |
| MFA should be enabled on accounts with write permissions on subscriptions | 57e98606-6b1e-6193-0e3d-fe621387c16b |
| MFA should be enabled on accounts with read permissions on subscriptions | 151e82c5-5341-a74b-1eb0-bc38d2c84bb5 |
| External accounts with owner permissions should be removed from subscriptions | c3b6ae71-f1f0-31b4-e6c1-d5951285d03d |
| External accounts with write permissions should be removed from subscriptions | 04e7147b-0deb-9796-2e5c-0336343ceb3d |
| External accounts with read permissions should be removed from subscriptions | a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b |
| Deprecated accounts with owner permissions should be removed from subscriptions | e52064aa-6853-e252-a11e-dffc675689c2 |
| Deprecated accounts should be removed from subscriptions | 00c6d40b-e990-6acf-d4f3-471e747a27c4 |

We recommend updating custom scripts, workflows, and governance rules to correspond with the V2 recommendations.

We've improved the coverage of the V2 identity recommendations by scanning all Azure resources (rather than just subscriptions) which allows security administrators to view role assignments per account. These changes may result in changes to your Secure Score throughout the GA process.

### Multiple changes to identity recommendations

**Estimated date for change: May 2023**

We announced previously the [availability of identity recommendations V2 (preview)](release-notes-archive.md#extra-recommendations-added-to-identity), which included enhanced capabilities.

As part of these changes, the following recommendations will be released as General Availability (GA) and replace the V1 recommendations that are set to be deprecated.

#### General Availability (GA) release of identity recommendations V2 

The following security recommendations will be released as GA and replace the V1 recommendations:
 
|Recommendation | Assessment Key|
|--|--|
|Accounts with owner permissions on Azure resources should be MFA enabled | 6240402e-f77c-46fa-9060-a7ce53997754 |
|Accounts with write permissions on Azure resources should be MFA enabled | c0cb17b2-0607-48a7-b0e0-903ed22de39b |
| Accounts with read permissions on Azure resources should be MFA enabled | dabc9bc4-b8a8-45bd-9a5a-43000df8aa1c |
| Guest accounts with owner permissions on Azure resources should be removed | 20606e75-05c4-48c0-9d97-add6daa2109a |
| Guest accounts with write permissions on Azure resources should be removed | 0354476c-a12a-4fcc-a79d-f0ab7ffffdbb |
| Guest accounts with read permissions on Azure resources should be removed | fde1c0c9-0fd2-4ecc-87b5-98956cbc1095 |
| Blocked accounts with owner permissions on Azure resources should be removed | 050ac097-3dda-4d24-ab6d-82568e7a50cf |
| Blocked accounts with read and write permissions on Azure resources should be removed | 1ff0b4c9-ed56-4de6-be9c-d7ab39645926 |

### DevOps Resource Deduplication for Defender for DevOps

**Estimated date for change: June 2023**

To improve the Defender for DevOps user experience and enable further integration with Defender for Cloud's rich set of capabilities, Defender for DevOps will no longer support duplicate instances of a DevOps organization to be onboarded to an Azure tenant. 

If you don't have an instance of a DevOps organization onboarded more than once to your organization, no further action is required. If you do have more than one instance of a DevOps organization onboarded to your tenant, the subscription owner will be notified and will need to delete the DevOps Connector(s) they don't want to keep by navigating to Defender for Cloud Environment Settings. 

Customers will have until June 30, 2023 to resolve this issue. After this date, only the most recent DevOps Connector created where an instance of the DevOps organization exists, will remain onboarded to Defender for DevOps.


## Next steps

For all recent changes to Defender for Cloud, see [What's new in Microsoft Defender for Cloud?](release-notes.md).
