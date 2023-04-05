---
title: "Azure Operator Nexus: Creation of L2 isolation-domain"
description: Learn how create L2 isolation-domain.
author: jaredr80 #Required; your GitHub user alias, with correct capitalization.
ms.author: jaredro #Required; microsoft alias of author; optional team alias.
ms.service: azure-operator-nexus #Required;
ms.topic: include #Required; leave this attribute/value as-is.
ms.date: 01/26/2023 #Required; mm/dd/yyyy format.
---

- Create a L2 isolation-domain

```azurecli
  az nf l2domain create --resource-name "<YourL2IsolationDomainName>" \
    --resource-group "<YourResourceGroupName>" \
    --subscription "<YourSubscription>" \
    --nf-id "<NetworkFabricResourceId>" \
    --location "<ClusterAzureRegion>" \
    --vlan-id <YourNetworkVlan> \
    --mtu "<MtuOfThisNetwork>
```
