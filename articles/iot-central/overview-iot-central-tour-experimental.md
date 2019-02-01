---
title: Take a tour of the Azure IoT Central UI | Microsoft Docs
description: As a builder, become familiar with the key areas of the Azure IoT Central UI that you use to create an IoT solution.
author: dominicbetts
ms.author: dobett
ms.date: 01/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
---

# Take a tour of the Azure IoT Central UI (New UI design)

This article introduces you to the Microsoft Azure IoT Central UI. You can use the UI to create, manage, and use an Azure IoT Central solution and its connected devices.

As a _builder_, you use the Azure IoT Central UI to define your Azure IoT Central solution. You can use the UI to:

- Define the types of device that connect to your solution.
- Configure the rules and actions for your devices.
- Customize the UI for an _operator_ who uses your solution.

As an _operator_, you use the Azure IoT Central UI to manage your Azure IoT Central solution. You can use the UI to:

- Monitor your devices.
- Configure your devices.
- Troubleshoot and remediate issues with your devices.
- Provision new devices.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## Use the left navigation menu

Use the left navigation menu to access the different areas of the application:

| Menu | Description |
| ---- | ----------- |
| ![Left navigation menu](media/overview-iot-central-tour-experimental/navigationbar.png) | <ul><li>The **Home** button displays the home page of your application. As a builder, you can customize this home page for your operators.</li><li>The **Device Explorer** button lists the simulated and real devices associated with each device template in the application. As an operator, you use the **Device Explorer** to manage your connected devices.</li><li>The **Device Sets** button enables you to view and create device sets. As an operator, you can create device sets as a logical collection of devices specified by a query.</li><li>The **Analytics** button shows analytics derived from device telemetry for devices and device sets. As an operator, you can create custom views on top of device data to derive insights from your application.</li><li>The **Jobs** button enables bulk device management by having you create and run jobs to perform updates at scale.</li><li>The **Device Templates** button shows the tools a builder uses to create and manage device templates.</li><li>The **Continuous Data Export** button an administrator to configure a continuous export to other Azure services such as storage and queues.</li><li>The **Administration** button shows the application administration pages where an administrator can manage application settings, users, and roles.</li></ul> |

## Search, help, and support

The top menu appears on every page:

![Toolbar](media/overview-iot-central-tour-experimental/toolbar.png)

- To search for device templates and devices, choose the **Search** icon.
- To change the UI language, choose the **Language** icon.
- To get help and support, choose the **Help** drop-down for a list of resources.
- To change the UI theme, or sign out of the application, choose the **Account** icon.

You can choose between a light theme or a dark theme for the UI:

![Choose a theme for the UI](media/overview-iot-central-tour-experimental/themes.png)

## Home page

![Home page](media/overview-iot-central-tour-experimental/homepage.png)

The home page is the first page you see when you sign in to your Azure IoT Central application. As a builder, you can customize the home page for other users of the application by adding tiles. To learn more, see the [Customize the Azure IoT Central operator's view](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) tutorial.

## Device explorer

![Explorer page](media/overview-iot-central-tour-experimental/explorer.png)

The explorer page shows the _devices_ in your Azure IoT Central application grouped by _device template_.

* A device template defines a type of device that can connect to your application. To learn more, see the [Define a new device type in your Azure IoT Central application](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* A device represents either a real or simulated device in your application. To learn more, see the [Add a new device to your Azure IoT Central application](tutorial-add-device.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## Device sets

![Device Sets page](media/overview-iot-central-tour-experimental/devicesets.png)

The _device sets_ page shows device sets created by the builder. A device set is a collection of related devices. A builder defines a query to identify the devices that are included in a device set. You use device sets when you customize the analytics in your application. To learn more, see the [Use device sets in your Azure IoT Central application](howto-use-device-sets.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) article.

## Analytics

![Analytics page](media/overview-iot-central-tour-experimental/analytics.png)

The analytics page shows charts that help you understand how the devices connected to your application are behaving. An operator uses this page to monitor and investigate issues with connected devices. The builder can define the charts shown on this page. To learn more, see the [Create custom analytics for your Azure IoT Central application](howto-create-analytics.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) article.

## Jobs

![Jobs page](media/overview-iot-central-tour-experimental/jobs.png)

The jobs page allows you to perform bulk device management operations onto your devices. The builder uses this page to update device properties, settings, and commands. To learn more, see the [Run a job](howto-run-a-job.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) article.

## Device Templates

![Device Templates page](media/overview-iot-central-tour-experimental/templates.png)

The device templates page is where a builder creates and manages the device templates in the application. To learn more, see the [Define a new device type in your Azure IoT Central application](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) tutorial.

## Continuous Data Export

![Continuous Data Export page](media/overview-iot-central-tour-experimental/export.png)

The continuous data export page is where an administrator defines how to export data, such as telemetry, from the application. Other services can store the exported data or use it for analysis. To learn more, see the [Export your data in Azure IoT Central](howto-export-data.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) article.

## Administration

![Administration page](media/overview-iot-central-tour-experimental/administration.png)

The administration page contains links to the tools an administrator uses such as defining users and roles in the application. To learn more, see the [Administer your Azure IoT Central application](howto-administer.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) article.

## Next steps

Now that you have an overview of Azure IoT Central and are familiar with the layout of the UI, the suggested next step is to complete the [Create an Azure IoT Central application](quick-deploy-iot-central.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) quickstart.