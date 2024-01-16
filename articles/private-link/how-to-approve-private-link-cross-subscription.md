---
title: Approve private link connections across subscriptions
titleSuffix: Azure Private Link
description: Get started learning how to approve and manage private link connections across subscriptions with Azure Private Link.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: how-to 
ms.date: 01/11/2024
#customer intent: As a Network Administrator, I want the approve private link connections across Azure subscriptions.

---

# Approve private link connections across subscriptions 

Azure Private Link enables you to connect privately to Azure resources. Private Link connections are scoped to a specific subscription. This article shows you how to approve a private endpoint connection across subscriptions.

## Prerequisites

- Two active Azure subscriptions. 
    
    - One subscription hosts the Azure resource and the other subscription contains the consumer private endpoint and virtual network.

- An administrator account for each subscription or an account with permissions in each subscription to create and manage resources.

Resources used in this article:

| Resource | Subscription | Resource group | Location |
| --- | --- | --- | --- |
| **storage1** *(This name is unique, replace with the name you create)* | subscription-1 | test-rg | East US 2 |
| **vnet-1** | subscription-2 | test-rg | East US 2 |
| **private-endpoint** | subscription-2 | test-rg | East US 2 |

## Sign in to subscription-1

Sign in to **subscription-1** in the [Azure portal](https://portal.azure.com).

## Create a resource group

1. In the search box at the top of the portal, enter **Resource group**. Select **Resource groups** in the search results.

1. Select **+ Create**.

1. In the **Basics** tab of **Create a resource group**, enter or select the following information:

    | Setting | Value |
    | ------- | ----- |
    | **Project details** |  |
    | Subscription | Select **subscription-1**. |
    | Resource group | Enter **test-rg**. |
    | Region | Select **East US 2**. |

1. Select **Review + Create**.

1. Select **Create**.

[!INCLUDE [create-storage-account.md](../../includes/create-storage-account.md)]

## Obtain storage account resource ID

You need the storage account resource ID to create the private endpoint connection in **subscription-2**. Use the following steps to obtain the storage account resource ID.

1. In the search box at the top of the portal, enter **Storage account**. Select **Storage accounts** in the search results.

1. Select **storage1** or the name of your existing storage account.

1. In **Settings**, select **Endpoints**.

1. Copy the entry in **Storage account resource ID**.

## Sign in to subscription-2

Sign in to **subscription-2** in the [Azure portal](https://portal.azure.com).

## Register the `Microsoft.Storage` resource provider

For the private endpoint connection to complete successfully, the Microsoft.Storage resource provider must be registered in **subscription-2**. Use the following steps to register the resource provider. If the `Microsoft.Storage` resource provider is already registered, skip this step.

> [!IMPORTANT]
> If you're using a different resource type, you must register the resource provider for that resource type if it's not already registered.

1. In the search box at the top of the portal, enter **Subscription**. Select **Subscriptions** in the search results.

1. Select **subscription-2**.

1. In **Settings**, select **Resource providers**.

1. In the **Resource providers** filter box, enter **Microsoft.Storage**. Select **Microsoft.Storage**.

1. Select **Register**.

[!INCLUDE [virtual-network-create.md](../../includes/virtual-network-create.md)]

## Create private endpoint

1. In the search box at the top of the portal, enter **Private endpoint**. Select **Private endpoints**.

1. Select **+ Create** in **Private endpoints**.

1. In the **Basics** tab of **Create a private endpoint**, enter or select the following information:

    | Setting | Value |
    | ------- | ----- |
    | **Project details** |  |
    | Subscription | Select **subscription-2**. |
    | Resource group | Select **test-rg** |
    | **Instance details** |   |
    | Name | Enter **private-endpoint**. |
    | Network Interface Name | Leave the default of **private-endpoint-nic**. |
    | Region | Select **East US 2**. |

1. Select **Next: Resource**.

1. Select **Connect to an Azure resource by resource ID or alias**.

1. In **Resource ID or alias**, paste the storage account resource ID that you copied earlier.

1. In **Target sub-resource**, enter **blob**.

1. Select **Next: Virtual Network**.

1. In **Virtual Network**, enter or select the following information:

    | Setting | Value |
    | ------- | ----- |
    | **Networking** |  |
    | Virtual network | Select **vnet-1 (test-rg)**. |
    | Subnet | Select **subnet-1**. |

1. Select **Next: DNS**.

1. Select **Next: Tags**.

1. Select **Review + Create**.

1. Select **Create**.

## Approve private endpoint connection

The private endpoint connection is in a **Pending** state until approved. Use the following steps to approve the private endpoint connection in **subscription-1**.

1. In the search box at the top of the portal, enter **Private endpoint**. Select **Private endpoints**.

1. Select **Pending connections**.

1. Select the box next to your storage account in **subscription-1**.

1. Select **Approve**.

1. Select **Yes** in **Approve connection**.

## Next steps

In this article, you learned how to approve a private endpoint connection across subscriptions. To learn more about Azure Private Link, continue to the following articles:

- [Azure Private Link overview](private-link-overview.md)

- [Azure Private endpoint overview](private-endpoint-overview.md)