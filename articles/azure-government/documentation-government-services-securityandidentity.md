   
---
title: Azure Government Security + Identity | Microsoft Docs
description: This article provides a comparision of features and guidance on developing applications for Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: zakramer
manager: liki

ms.assetid: e2fe7983-5870-43e9-ae01-2d45d3102c8a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 08/19/2017
ms.author: zsk0646

---
# Azure Government Security + Identity
## Key Vault
For details about Azure Key Vault and how to use it, see the [Azure Key Vault public documentation](../key-vault/index.md).

### Data considerations
The following information identifies the Azure Government boundary for Azure Key Vault:

| Regulated/controlled data that's permitted | Regulated/controlled data that's not permitted |
| --- | --- |
|All data that's encrypted with Azure Key Vault key might contain regulated or controlled data. |Azure Key Vault metadata cannot contain export-controlled data. This metadata includes all configuration data that you enter while creating and maintaining  Key Vault.  Do not enter regulated or controlled data into the following fields: **Resource group names**, **Key Vault names**, **Subscription names**.|

Key Vault is generally available in Azure Government.


## Azure Active Directory Premium
Azure Active Directory Premium is in Limited Preview in Azure Government.

For details about this service and how to use it, see the [Azure Active Directory Documentation](../active-directory/index.md). For details about how to sign up for Preview, see the [announcement blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/07/azure-active-directory-premium-is-now-in-limited-preview-in-us-government-cloud/).

### Variations

The following Azure Active Directory Premium features are currently not available in Azure Government:

  * B2B collaboration ([vote for this feature](https://feedback.azure.com/forums/558487-azure-government/suggestions/20588554-azure-ad-b2b-in-azure-government))

  * Group-based licensing

  * Azure Active Directory app gallery

  * Azure Active Directory domain services

  * Intune-enabled conditional access scenarios

## Next steps
For supplemental information and updates, subscribe to the
<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government blog. </a>

