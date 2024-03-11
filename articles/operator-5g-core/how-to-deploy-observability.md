---
title: Deploy Azure Operator 5G Core observability(preview) on Azure Kubernetes Services
description: Learn the high-level process to deploy Azure Operator 5G Core observability (preview) on Azure Kubernetes Services.
author: HollyCl
ms.author: HollyCl
ms.service: azure-operator-5g-core
ms.topic: how-to #required; leave this attribute/value as-is
ms.date: 02/22/2024

#CustomerIntent: As a < type of user >, I want < what? > so that < why? >.
---

# Deploy Azure Operator 5G Core observability (preview) on Azure Kubernetes Services (AKS) or Nexus Azure Kubernetes Services (NAKS)

Use the following Azure CLI commands to deploy observability resources for Azure Operator 5G Core Preview.

## Deploy observability


```azurecli
New-AzResourceGroupDeployment ` 

-Name <DEPLOYMENT NAME> ` 

-ResourceGroupName <RESOURCE GROUP> ` 

-TemplateFile ./releases/2311.0-1/AKS/bicep/obsvTemplate.bicep ` 

-TemplateParameterFile ./releases/2311.0-1/AKS/params/obsvParams.json ` 

-resourceName <RESOURCE NAME> –Verbose
```

## Next step

- [Deploy a network function on Azure Kubernetes Services or Nexus Azure Kubernetes Services](how-to-deploy-network-functions.md)