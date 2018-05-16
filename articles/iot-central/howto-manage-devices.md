---
# Mandatory fields. See more on aka.ms/skyeye/meta.
title: Manage the devices in your Azure IoT Central application | Microsoft Docs
description: As an operator, learn how to manage devices in your Azure IoT Central application.
services: iot-central
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: article
# Use only one of the following. Use ms.service for services, ms.prod for on-prem. Remove the # before the relevant field.
ms.prod: microsoft-iot-central
# product-name-from-white-list

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.devlang:devlang-from-white-list
# ms.suite: 
# ms.tgt_pltfrm:
# ms.reviewer:
manager: timlt
---

# Manage devices in your Azure IoT Central application

This article describes how, as an operator, to manage devices in your Microsoft Azure IoT Central application. As an operator, you can:

- Use the **Explorer** page to view, add, and delete devices connected to your Azure IoT Central application.
- Maintain an up-to-date inventory of your devices.
- Keep your device metadata up-to-date by changing the values stored in the device properties.
- Control the behavior of your devices by updating a setting on a specific device from the **Settings** page.

## View your devices

To view an individual device:

1. Choose **Explorer** on the left navigation menu. Here you see a list of your [device templates](howto-set-up-template.md).

1. Choose a **Device Template** in the left-hand pane.

1. In the right-hand pane, you see a list of devices created from that device template. Choose an individual device to see the **Device Details** page for that device:

    ![Device Details Page](./media/howto-manage-devices/image1.png)

## Add a device

To add a device to your Azure IoT Central application:

1. Choose **Explorer** on the left navigation menu.

1. Choose the device template from which you want to create a device.

1. Choose + **New**.

1. Choose **Real** or **Simulated**. A real device is for a physical device that you connect to your Azure IoT Central application. A simulated device has sample data generated for you by Azure IoT Central. This example uses a real device. Choose **Real** to navigate to the **Device Details** page for your new device.

## Delete a device

To delete either a real or simulated device from your Azure IoT Central application:

1. Choose **Explorer** on the navigation menu.

1. Choose the device template of the device you want to delete.

1. Check the box next to the device to delete.

1. Choose **Delete**.

## Change a device setting

Settings control the behavior of a device. In other words, they enable you to provide inputs to your device. You can view and update device settings on the **Device Details** page.

1. Choose **Explorer** on the navigation menu.

1. Choose the device template of the device whose settings you want to change.

1. Choose the **Settings** tab. Here you see all the settings your device has and their current values. For each setting, you can see if the device is still syncing.

1. Modify the settings to your desired values. You can modify multiple settings at once and update them all at the same time.

1. Choose **Update**. The values are sent to your device. When the device acknowledges the setting change, the status of the setting goes back to **synced**.

## Change a property

Properties are the device metadata associated with the device, such as city and serial number. You can view and update properties on the **Device Details** page.

1. Choose **Explorer** on navigation menu.

1. Choose the device template of the device whose properties you want to change.

1. Choose the **Properties** tab, where you see all the properties.

1. Modify the properties to your desired values. You can modify multiple properties at once and update them all at the same time.

1. Choose **Update**.

> [!NOTE]
> You cannot change the value of _device properties_. Device properties are set by the device and are read-only within the Azure IoT Central application.

## Next steps

Now that you have learned how to manage devices in your Azure IoT Central application, here is the suggested next step:

> [!div class="nextstepaction"]
> [How to use device sets](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->