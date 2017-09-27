---
title: Azure Government Compute | Microsoft Docs
description: This provides a comparision of features and guidance on developing applications for Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: kydeeds
manager: zakramer---

ms.assetid: fb11f60c-5a70-46a9-82a0-abb2a4f4239b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 8/30/2017
ms.author: kydeeds

---
# Azure Government Compute
## Virtual Machines
For details on this service and how to use it, see [Azure Virtual Machines Sizes](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### Variations
For available virtual machine sizes in Azure Government, see <a href="https://azure.microsoft.com/regions/services/">Products Available by Region</a>

### Data Considerations
The following information identifies the Azure Government boundary for Azure Virtual Machines:

| Regulated/controlled data permitted | Regulated/controlled data not permitted |
| --- | --- |
| Data entered, stored, and processed within a VM can contain export-controlled data. Binaries running within Azure Virtual Machines. Static authenticators, such as passwords and smartcard PINs for access to Azure platform components. Private keys of certificates used to manage Azure platform components. SQL connection strings.  Other security information/secrets, such as certificates, encryption keys, master keys, and storage keys stored in Azure services. |Metadata is not permitted to contain export-controlled data. This metadata includes all configuration data entered when creating and maintaining your Azure Virtual Machine.  Do not enter Regulated/controlled data into the following fields:  Tenant role names, Resource groups, Deployment names, Resource names, Resource tags |

## Batch 
For details on this service and how to use it, see [Azure Batch documentation](https://docs.microsoft.com/en-us/azure/batch/batch-technical-overview).

### Variations
The URLs for accessing and managing the Batch service are different:

| Service Type | Azure Public | Azure Government |
| --- | --- | --- |
| Batch | *.batch.azure.com | *.batch.usgovcloudapi.net |

## Azure Functions
The [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/) service is now available (General Availability) for the Azure Government environment, with some differences, which you can read about below. 

### Variations
The following Functions features are not currently available in Azure Government:

- Functions access through the Azure Government Portal
- Consumption Plan
    -   The [App Service plan](https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale#app-service-plan) is available in Azure Government. To learn more about the two hosting plans, click [here](https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale).
	
	
### Quickstarts
The [Functions Quickstart for Government](documentation-government-functions.md) will guide you through creating your first Azure Function on Azure Government and contains other useful tutorials. 


## Next Steps
For supplemental information and updates, subscribe to the
<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government Blog. </a>

