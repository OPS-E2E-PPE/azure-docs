---
title: Message routing with IoT Hub — Azure portal | Microsoft Docs
description: A how-to article that creates and deletes routes and endpoints in IoT Hub, using the Azure portal.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: how-to
ms.date: 11/11/2022
ms.author: kgremban
---

# Message routing with IoT Hub — Azure portal

This article shows you how to create a route and endpoint in your IoT hub, then delete your route and endpoint. We use the Azure portal to create routes and endpoints to Event Hubs, Service Bus queue, Service Bus topic, and Azure Storage.

To learn more about how routing works in IoT Hub, see [Use IoT Hub message routing to send device-to-cloud messages to different endpoints](/azure/iot-hub/iot-hub-devguide-messages-d2c). To walk through setting up a route that sends messages to storage and testing on a simulated device, see [Tutorial: Send device data to Azure Storage using IoT Hub message routing](/azure/iot-hub/tutorial-routing?tabs=portal).

## Prerequisites

**Azure portal**

This article uses the Azure portal interface to work with IoT Hub and other Azure services. To understand the portal better, see [What is the Azure portal?](/azure/azure-portal/azure-portal-overview)

**IoT Hub and an endpoint service**

You need an IoT hub and at least one other service to serve as an endpoint to an IoT hub route. You can choose which Azure service (Event Hubs, Service Bus queue or topic, or Azure Storage) endpoint that you'd like to connect with your IoT Hub route.

* An IoT hub in your [Azure subscription](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). If you don't have a hub yet, you can follow the steps in [Create an IoT hub using the Azure portal](/azure/iot-hub/iot-hub-create-through-portal).

* (Optional) An Event Hubs resource (namespace and entity). If you need to create a new Event Hubs resource, see [Quickstart: Create an event hub using Azure portal](/azure/event-hubs/event-hubs-create).

* (Optional) A Service Bus queue resource (namespace and queue). If you need to create a new Service Bus queue, see [Use Azure portal to create a Service Bus namespace and a queue](/azure/service-bus-messaging/service-bus-quickstart-portal).

* (Optional) A Service Bus topic resource (namespace and topic). If you need to create a new Service Bus topic, see [Use the Azure portal to create a Service Bus topic and subscriptions to the topic](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal).

* (Optional) An Azure Storage resource (account and container). If you need to create a new Azure Storage, see [Create a storage account](/azure/storage/common/storage-account-create?tabs=azure-portal). There are lots of options (tabs) with a storage account, but you only need a new container in your account for this article.

* (Optional) A Cosmos DB resource (account, database, and container). If you need to create a new Cosmos DB, see [Create an Azure Cosmos DB account](/azure/cosmos-db/nosql/quickstart-portal#create-account). For the API option, choose **Azure Cosmos DB for NoSQL**.

## Create a route

In IoT Hub, you can create a route to send messages or capture events. Each route has an endpoint, where the messages or events end up, and a data source, where the messages or events originate. You choose these locations when creating a new route in the IoT Hub. Routing queries are then used to filter messages or events before they go to the endpoint.

You can use Events Hubs, a Service Bus queue or topic, or an Azure storage to be the endpoint for your IoT hub route. The service must first exist in your Azure account.

Go to your IoT hub in the Azure portal and select **Message routing** from the **Hub settings** menu. Select **+ Add** to add a new route.

:::image type="content" source="media/how-to-routing-portal/message-routing-add.jpg" alt-text="Screenshot that shows location of the add button, to add a new route in your IoT hub.":::

Next, decide which route type (Event Hubs, Service Bus queue or topic, or Azure Storage) you want to create and do the steps in the following tab.

# [Event Hubs](#tab/eventhubs)

If you need to create an Event Hubs resource, see [Quickstart: Create an event hub using Azure portal](/azure/event-hubs/event-hubs-create). 

In the Azure portal, you can create a route and endpoint at the same time. Using Azure CLI or PowerShell, you must create an endpoint first and then create a route.

1. In the **Add a route** blade that appears, create a unique **Name**. Optionally, you might want to include the endpoint type in the name, such as **my-event-hubs-route**.

1. For **Endpoint**, select the **+ Add endpoint** dropdown list and choose **Event hubs**.

   :::image type="content" source="media/how-to-routing-portal/add-endpoint-event-hubs.jpg" alt-text="Screenshot that shows location of the 'Add endpoint' dropdown list.":::

1. A new page, **Add an event hub endpoint**, opens. Create an **Endpoint name** for your IoT Hub. This will display in your IoT Hub.
   
   For **Event hub namespace**, choose the namespace you previously created in your Event Hubs resource from the dropdown list.

   For **Event hub instance**, choose the event hub you created in your Event Hubs resource from the dropdown list.

   Select **Create** at the bottom and you'll go back to the **Add a route** page.

   :::image type="content" source="media/how-to-routing-portal/add-event-hub.jpg" alt-text="Screenshot that shows all options to choose on the 'Add an event hub endpoint' page.":::

1. Leave all the other values as their defaults on the **Add a route** page.

1. Select **Save** at the bottom to create your new route. You should now see the route on your **Message routing** page. 
   
   :::image type="content" source="media/how-to-routing-portal/see-new-route.jpg" alt-text="Screenshot that shows the new route you created on the 'Message routing' page." lightbox="media/how-to-routing-portal/see-new-route.jpg":::

# [Service Bus queue](#tab/servicebusqueue)

If you need to create a Service Bus queue, see [Use Azure portal to create a Service Bus namespace and a queue](/azure/service-bus-messaging/service-bus-quickstart-portal).

1. In the **Add a route** blade that appears, create a unique **Name**. Optionally, you might want to include the endpoint type in the name, such as **my-service-bus-route**.

1. For **Endpoint**, select the **+ Add endpoint** dropdown list and choose **Service bus queue**.

1. A new page appears called **Add a service bus endpoint**. Think of a unique name for the **Endpoint name** field.

1. Select the dropdown list for **Service bus namespace** and select your service bus.

1. Select the dropdown list for **Service bus queue** and select your service bus queue.

1. Leave all other values in their default states and choose **Create** at the bottom.

   :::image type="content" source="media/how-to-routing-portal/add-service-bus-endpoint.jpg" alt-text="Screenshot that shows the 'Add a service bus endpoint' page with correct options selected.":::

1. Leave all the other values as their defaults on the **Add a route** page.

1. Select **Save** at the bottom to create your new route. You should now see the route on your **Message routing** page. 

   :::image type="content" source="media/how-to-routing-portal/see-new-service-bus-route.jpg" alt-text="Screenshot that shows the new service bus queue route you created on the 'Message routing' page." lightbox="media/how-to-routing-portal/see-new-service-bus-route.jpg":::

# [Service Bus topic](#tab/servicebustopic)

If you need to create a Service Bus topic, see [Use the Azure portal to create a Service Bus topic and subscriptions to the topic](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal).

1. In the **Add a route** blade that appears, create a unique **Name**. Optionally, you might want to include the endpoint type in the name, such as **my-service-bus-topic-route**.

1. For **Endpoint**, select the **+ Add endpoint** dropdown list and choose **Service bus topic**.

1. A new page appears called **Add a service bus endpoint**. Think of a unique name for the **Endpoint name** field.

1. Select the dropdown list for **Service bus namespace** and select your service bus.

1. Select the dropdown list for **Service Bus Topic** and select your Service Bus topic.

1. Leave all other values in their default states and choose **Create** at the bottom.

   :::image type="content" source="media/how-to-routing-portal/add-service-bus-topic-endpoint.jpg" alt-text="Screenshot that shows the 'Add a service bus endpoint' page with correct options selected.":::

1. Leave all the other values as their defaults on the **Add a route** page.

1. Select **Save** at the bottom to create your new route. You should now see the route on your **Message routing** page. 

   :::image type="content" source="media/how-to-routing-portal/see-new-service-bus-topic-route.jpg" alt-text="Screenshot that shows your new Service Bus topic route on the 'Message routing' page." lightbox="media/how-to-routing-portal/see-new-service-bus-topic-route.jpg":::

# [Azure Storage](#tab/azurestorage)

If you need to create an Azure storage resource (with container), see [Create a storage account](/azure/iot-hub/tutorial-routing?tabs=portal#create-a-storage-account).

1. In the **Add a route** blade that appears, create a unique **Name**. Optionally, you might want to include the endpoint type in the name, such as **my-service-bus-topic-route**.

1. For **Endpoint**, select the **+ Add endpoint** dropdown list and choose **Storage**.

1. A new page appears called **Add a storage endpoint**. Think of a unique name for the **Endpoint name** field.

1. Select the button for **Pick a container**. Select your storage account and then its container. You return to the **Add a storage endpoint** page.

1. Leave all other values in their default states and choose **Create** at the bottom.

   :::image type="content" source="media/how-to-routing-portal/add-storage-endpoint.jpg" alt-text="Screenshot that shows the 'Add a storage endpoint' page with correct options selected.":::

1. Leave all the other values as their defaults on the **Add a route** page.

1. Select **Save** at the bottom to create your new route. You should now see the route on your **Message routing** page. 

   :::image type="content" source="media/how-to-routing-portal/see-new-storage-route.jpg" alt-text="Screenshot that shows your new storage route on the 'Message routing' page." lightbox="media/how-to-routing-portal/see-new-storage-route.jpg":::

# [Cosmos DB](#tab/cosmosdb)

If you need to create a Cosmos DB resource, see [Create an Azure Cosmos DB account](/azure/cosmos-db/nosql/quickstart-portal#create-account).

1. Go to your IoT hub resource on the **Message routing** page and select the **Custom endpoints** tab.

1. Select **+ Add**, then choose **CosmosDB** in the dropdown menu.

   :::image type="content" source="media/how-to-routing-portal/add-cosmosdb-endpoint.png" alt-text="Screenshot that shows location of the 'Add' button on the 'Message routing' page in the 'Custom endpoint' tab of the IoT Hub resource.":::

1. Complete the fields in the form **Add a Cosmos DB endpoint**.

   * **Endpoint name** — create a unique name.

   * **Cosmos DB account**, **Database**, and **Collection** — choose your Cosmos DB account, database, and collection from the dropdown lists.

   * **Partition key name** and **Partition key template** — these will fill in automatically, based on the previous selections, or you can change the partition template based on your business logic. For more information on partitioning, see [Partitioning and horizontal scaling in Azure Cosmos DB](/azure/cosmos-db/partitioning-overview).

   :::image type="content" source="media/how-to-routing-portal/add-cosmosdb-endpoint-form.jpg" alt-text="Screenshot that shows details of the 'Add a Cosmos DB endpoint' form." lightbox="media/how-to-routing-portal/add-cosmosdb-endpoint-form.jpg":::  

   * Select **Save** at the bottom of the form. You should now see the route on your **Message routing** page.

   :::image type="content" source="media/how-to-routing-portal/cosmosdb-confirm.jpg" alt-text="Screenshot that shows a new Cosmos DB route in the IoT Hub 'Message routing' page." lightbox="media/how-to-routing-portal/cosmosdb-confirm.jpg":::  

---

## Update a route

Updating a route in the Azure portal is as easy as selecting your route from the **Message routing** menu in your IoT hub and changing the properties.

You can make changes to an existing route:

* Select a different endpoint from the **Endpoint** dropdown list or create a new endpoint
* Select a new source from the **Date source** dropdown list
* Enable or disable your route in the **Enable route** section
* Create or change queries in the **Routing query** section

:::image type="content" source="media/how-to-routing-portal/update-route.jpg" alt-text="Screenshot that shows where and how to modify an existing IoT Hub route.":::

Select **Save** at the bottom after making changes.

> [!NOTE]
> While you can't modify an existing endpoint, you can create new ones for your IoT hub route and change the endpoint your route uses with the **Endpoint** dropdown list.

## Delete a route

To delete a route in the Azure portal:

1. Check the box next to your route located in the **Message routing** menu. 

1. Select the delete button.

:::image type="content" source="media/how-to-routing-portal/delete-route-portal.jpg" alt-text="Screenshot that shows where and how to delete an existing IoT Hub route." lightbox="media/how-to-routing-portal/delete-route-portal.jpg":::

## Delete a custom endpoint

To delete a custom endpoint in the Azure portal:

1. From the **Message routing** menu, select the **Custom endpoints** tab.

1. Check the box next to your Event hubs endpoint.

1. Select the **Delete** button.

:::image type="content" source="media/how-to-routing-portal/delete-endpoint-portal.jpg" alt-text="Screenshot that shows where and how to delete an existing Event Hubs endpoint." lightbox="media/how-to-routing-portal/delete-endpoint-portal.jpg":::

## Next Steps

In this how-to article you learned how to create a route and endpoint for your Event Hubs, Service Bus queue or topic, and Azure Storage. 

To further your exploration into message routing, see [Tutorial: Send device data to Azure Storage using IoT Hub message routing](/azure/iot-hub/tutorial-routing?tabs=portal). In this tutorial, you'll create a storage route and test it with a device in your IoT hub.
