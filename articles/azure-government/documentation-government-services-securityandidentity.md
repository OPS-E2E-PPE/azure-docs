---
title: Azure Government Security + Identity | Microsoft Docs
description: This provides a comparision of features and guidance on developing applications for Azure Government
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
Key Vault is generally available in Azure Government.

For details on this service and how to use it, see the [Azure Key Vault public documentation](../key-vault/index.md).

### Data Considerations
The following information identifies the Azure Government boundary for Azure Key Vault:

| Regulated/controlled data permitted | Regulated/controlled data not permitted |
| --- | --- |
| All data encrypted with an Azure Key Vault key may contain Regulated/controlled data. |Azure Key Vault metadata is not permitted to contain export-controlled data. This metadata includes all configuration data entered when creating and maintaining your Key Vault.  Do not enter Regulated/controlled data into the following fields: **Resource group names, Key Vault names, Subscription name** |

## Next Steps
For supplemental information and updates, subscribe to the
<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government Blog. </a>

