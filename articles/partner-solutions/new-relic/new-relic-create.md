---
title: Create an instance of Azure Native New Relic Service Preview
description: Learn how to create a resource by using Azure Native New Relic Service.

ms.topic: quickstart
ms.date: 01/16/2023
---

# Quickstart: Get started with Azure Native New Relic Service Preview

In this quickstart, you create an instance of Azure Native New Relic Service Preview. You can either [create a New Relic account](new-relic-create.md) or [link to an existing New Relic account](new-relic-link-to-existing.md).

When you use the integrated New Relic experience in the Azure portal by using Azure Native New Relic Service, the service creates and maps the following entities for monitoring and billing purposes.

:::image type="content" source="media/new-relic-create/new-relic-subscription.png" alt-text="Conceptual diagram that shows the relationship between Azure and New Relic.":::

- **New Relic resource in Azure**: By using the New Relic resource, you can manage the New Relic account on Azure. The resource is created in the Azure subscription and resource group that you select during the creation process or linking process.
- **New Relic organization**: The New Relic organization on New Relic software as a service (SaaS) is used for user management and billing.
- **New Relic account**: The New Relic account on New Relic SaaS is used to store and process telemetry data.
- **Azure Marketplace SaaS resource**: When you set up a new account and organization on New Relic by using Azure Native New Relic Service, the SaaS resource is created automatically, based on the plan that you select from the Azure New Relic offer in Azure Marketplace. This resource is used for billing.

## Prerequisites

Before you link the subscription to New Relic, complete the pre-deployment configuration. For more information, see [Configure pre-deployment for Azure Native New Relic Service](new-relic-how-to-configure-prereqs.md).

## Find an offer

Use the Azure portal to find the Azure Native New Relic Service application:

1. Go to the [Azure portal](https://portal.azure.com/) and sign in.

1. If you visited Azure Marketplace in a recent session, select the icon from the available options. Otherwise, search for **marketplace** and then select the **Marketplace** result under **Services**.

   :::image type="content" source="media/new-relic-create/new-relic-search.png" alt-text="Screenshot that shows entering the word Marketplace in a search box.":::

1. In Azure Marketplace, search for **new relic** and select the **Azure Native New Relic Service** result. The page for the service opens.

   :::image type="content" source="media/new-relic-create/new-relic-marketplace.png" alt-text="Screenshot that shows Azure Native New Relic Service in Azure Marketplace.":::

1. Select **Subscribe**.

## Create a New Relic resource on Azure

1. When you're creating a New Relic resource, you have two options. One creates a New Relic account, and the other links an Azure subscription to an existing New Relic account. For this example, select **Create** under the **Create a New Relic resource** option.

   :::image type="content" source="media/new-relic-create/new-relic-create.png" alt-text="Screenshot that shows New Relic resources.":::

1. A form to create a New Relic resource appears on the **Basics** tab.

   :::image type="content" source="media/new-relic-create/new-relic-basics.png" alt-text="Screenshot that shows the tab for basic information about a New Relic resource.":::

1. Provide the following values:

   |  Property | Description |
   |--|--|
   |  **Subscription**   |  Select the Azure subscription that you want to use for creating the New Relic resource. You must have owner access.|
   |  **Resource group**  |Specify whether you want to create a new resource group or use an existing one. A [resource group](/azure/azure-resource-manager/management/overview) is a container that holds related resources for an Azure solution.|
   |  **Resource name**  |Specify a name for the New Relic resource. This name will be the friendly name of the New Relic account.|
   |  **Region**         |Select the region where the New Relic resource on Azure and the New Relic account will be created.|

1. When you're choosing the organization under which to create the New Relic account, you have two options: create a new organization, or select an existing organization to link the newly created account.

   > [!IMPORTANT]
   > You can't use **Associate with existing** functionality, presently. The ability to create a new New Relic resource and associate it with an existing organization is currently disabled.

   If you opt to create a new organization, you can choose a plan from the list of available plans by selecting **Change Plan** on the working pane.

   :::image type="content" source="media/new-relic-create/new-relic-change-plan.png" alt-text="Screenshot of the panel for changing a plan.":::

 
## Configure metrics and logs

Your next step is to configure metrics and logs on the **Logs** tab. When you're creating the New Relic resource, you can set up automatic log forwarding for two types of logs:

1. To send subscription-level logs to New Relic, select **Subscription activity logs**. If you leave this option cleared, no subscription-level logs will be sent to New Relic.

   These logs provide insight into the operations on your resources at the [control plane](/azure/azure-resource-manager/management/control-plane-and-data-plane). These logs also include updates on service-health events. 

   Use the activity log to determine what, who, and when for any write operations (`PUT`, `POST`, `DELETE`). There's a single activity log for each Azure subscription.

1. To send Azure resource logs to New Relic, select **Azure resource logs** for all supported resource types. The types of Azure resource logs are listed in [Azure Monitor Resource Log categories](/azure/azure-monitor/essentials/resource-logs-categories).

   These logs provide insight into operations that were taken on an Azure resource at the [data plane](/azure/azure-resource-manager/management/control-plane-and-data-plane). For example, getting a secret from a key vault is a data plane operation. Making a request to a database is also a data plane operation. The content of resource logs varies by the Azure service and resource type.

   :::image type="content" source="media/new-relic-create/new-relic-metrics.png" alt-text="Screenshot of the tab for logs in a New Relic resource, with resource logs selected.":::

1. When the checkbox for Azure resource logs is selected, logs are forwarded for all resources by default. To filter the set of Azure resources that send logs to New Relic, use inclusion and exclusion rules and set Azure resource tags:

   - All Azure resources with tags defined in include rules send logs to New Relic.
   - All Azure resources with tags defined in exclude rules don't send logs to New Relic.
   - If there's a conflict between inclusion and exclusion rules, the exclusion rule applies.
    
   Azure charges for logs sent to New Relic. For more information, see the [pricing of platform logs](/azure.microsoft.com/pricing/details/monitor/) sent to Azure Marketplace partners.

   > [!NOTE]
   > You can collect metrics for virtual machines and app services by installing the New Relic agent after you create the New Relic resource.

1. After you finish configuring metrics and logs, select **Next**.

## Set up resource tags

On the **Tags** tab, you can choose to set up tags for the New Relic resource. 

:::image type="content" source="media/new-relic-create/new-relic-tags.png" alt-text="Screenshot of Tags tab of the New Relic resource.":::

You can also skip this step and go directly to the **Review and Create** tab.

## Review and create the resource

1. On the **Review and Create** tab, review the resource setup information. 

   :::image type="content" source="media/new-relic-create/new-relic-review.png" alt-text="Screenshot of the tab for reviewing and creating a New Relic resource.":::

1. Ensure that you've passed validation, and then select **Create** to begin the resource deployment. 

## Next steps

- [Manage the New Relic resource](new-relic-how-to-manage.md)
