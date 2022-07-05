---
title: Manage sensors with Defender for IoT in the Azure portal
description: Learn how to onboard, view, and manage sensors with Defender for IoT in the Azure portal.
ms.date: 03/30/2022
ms.topic: how-to
---

# Manage sensors with Defender for IoT in the Azure portal

This article describes how to onboard, view, and manage sensors with [Defender for IoT in the Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## Purchase sensors or download software for sensors

This procedure describes how to use the Azure portal to contact vendors for pre-configured appliances, or how to download software for you to install on your own appliances. 

1. In the Azure portal, go to **Defender for IoT** > **Getting started** > **Sensor**.

1. Do one of the following:

    - To buy a pre-configured appliance, select **Contact** under **Buy preconfigured appliance**. This opens an email to [hardware.sales@arrow.com](mailto:hardware.sales@arrow.com) with a template request for Defender for IoT appliances. For more information, see [Pre-configured physical appliances for OT monitoring](ot-pre-configured-appliances.md).

    - To install software on your own appliances, do the following:

        1. Make sure that you have a supported appliance available.

        1. Under *Select version**, select the software version you want to install. We recommend that you always select the most recent version.

        1. Select **Download**. Download the sensor software and save it in a location that you can access from your selected appliance.

        1. Install your software. For more information, see [Defender for IoT installation](how-to-install-software.md).

## Onboard sensors

Onboard a sensor by registering it with Microsoft Defender for IoT. For OT sensors, you'll also need to download a sensor activation file.

Select one of the following tabs, depending on the type of network you're working with.
# [OT sensors](#tab/ot)

**Prerequisites**: Make sure that you've set up your sensor and configured your SPAN port or TAP. 

For more information, see [Activate and set up your sensor](how-to-activate-and-set-up-your-sensor.md) and [Defender for IoT installation](how-to-install-software.md), or our [Tutorial: Get started with Microsoft Defender for IoT for OT security](tutorial-onboarding.md).

**To onboard your OT sensor to Defender for IoT**:

1. In the Azure portal, navigate to **Defender for IoT** > **Getting started** and select **Set up OT/ICS Security**.

    :::image type="content" source="media/tutorial-onboarding/onboard-a-sensor.png" alt-text="Screenshot of the Set up O T/I C S Security button on the Get started page.":::

    Alternately, from the Defender for IoT **Sites and sensors** page, select **Onboard OT sensor**.

1. By default, on the **Set up OT/ICS Security** page, **Step 1: Did you set up a sensor?** and **Step 2: Configure SPAN port or TAP​** of the wizard are collapsed. If you haven't completed these steps, do so before continuing.

1. In **Step 3: Register this sensor with Microsoft Defender for IoT** enter or select the following values for your sensor:

    1. In the **Sensor name** field, enter a meaningful name for your sensor.  We recommend including your sensor's IP address as part of the name, or using another easily identifiable name that can help you keep track between the registration name in the Azure portal and the IP address of the sensor shown in the sensor console.

    1. In the **Subscription** field, select your Azure subscription.

    1. Toggle on the **Cloud connected** option to have your sensor connected to other Azure services, such as Microsoft Sentinel, and to push [threat intelligence packages](how-to-work-with-threat-intelligence-packages.md) from Defender for IoT to your sensors.

    1. In the **Sensor version** field, select which software version is installed on your sensor machine. We recommend that you select **22.X and above** to get all of the latest features and enhancements.

        If you haven't yet upgraded to version 22.x, see [Update a standalone sensor version](how-to-manage-individual-sensors.md#update-a-standalone-sensor-version) and [Reactivate a sensor for upgrades to version 22.x](#reactivate-a-sensor-for-upgrades-to-version-22x-from-a-legacy-version).

    1. In the **Site** section, select the **Resource name** and enter the **Display name** for your site. Add any tags as needed to help you identify your sensor.

    1. In the **Zone** field, select a zone from the menu, or select **Create Zone** to create a new one.

1. Select **Register**.

A success message appears and your activation file is automatically downloaded. Your sensor is now shown under the configured site on the Defender for IoT **Sites and sensors** page.

Until you activate your sensor, the sensor's status will show as **Pending Activation**.

Make the downloaded activation file accessible to the sensor console admin so that they can activate the sensor. For more information, see [Upload new activation files](how-to-manage-individual-sensors.md#upload-new-activation-files).

# [Enterprise IoT sensors](#tab/eiot)

**To set up an Enterprise IoT sensor**:

1. Navigate to the [Azure portal](https://portal.azure.com#home).

1. Select **Set up Enterprise IoT Security**.

    :::image type="content" source="media/tutorial-get-started-eiot/onboard-sensor.png" alt-text="Screenshot of the Set up Enterprise I O T Security button on the Get started page.":::

1. In the **Sensor name** field, enter a meaningful name for your sensor.

1. From the **Subscription** drop-down menu, select the subscription where you want to add your sensor.

1. Select **Register**. A **Sensor registration successful** screen shows your next steps and the command you'll need to start the sensor installation.

    For example:

    :::image type="content" source="media/tutorial-get-started-eiot/successful-registration.png" alt-text="Screenshot of the successful registration of an Enterprise I O T sensor.":::

1. Copy the command to a safe location, and continue with installing the sensor. For more information, see [Install the sensor](tutorial-getting-started-eiot-sensor.md#install-the-sensor).

> [!NOTE]
> As opposed to OT sensors, where you define your sensor's site, all Enterprise IoT sensors are automatically added to the **Enterprise network** site.

---
## Manage on-boarded sensors

Sensors that you've on-boarded to Defender for IoT are listed on the Defender for IoT **Sites and sensors** page. This page supports the following management tasks:

- **Export sensor data**. To export a CSV file with details about all sensors listed, select **Export** at the top of the page.

- **Edit sensor details**. To edit a sensor zone, or to toggle on/off the **Automatic Threat Intelligence Update** option, select the **...** options menu at the right of a sensor row > **Edit**.

    Make your changes as needed and select **Save**.

- **Delete a sensor**. Delete sensors if you're no longer working with them. Select the **...** options menu at the right of a sensor row > **Delete sensor**.

- **Download an activation file**. You'll need to download a new activation file for your sensor if you want to [reactivate the sensor](#reactivate-a-sensor). Select the **...** options menu at the right of a sensor row > **Download activation file**.

- **Prepare to update to 22.X**. Use this option specifically when upgrading sensors to version 22.x. For more information, see [below](#reactivate-a-sensor-for-upgrades-to-version-22x-from-a-legacy-version).

## Reactivate a sensor

You may need to reactivate your sensor because you want to:

- **Work in cloud-connected mode instead of locally managed mode**: After reactivation, existing sensor detections are displayed in the sensor console, and newly detected alert information is delivered through Defender for IoT in the Azure portal. This information can be shared with other Azure services, such as Microsoft Sentinel.

- **Work in locally managed mode instead of cloud-connected mode**: After reactivation, sensor detection information is displayed only in the sensor console.

- **Associate the sensor to a new site**:  To do this, re-register the sensor with new site definitions and use the new activation file to activate.

In such cases, do the following:

1. [Delete your existing sensor](#manage-on-boarded-sensors).
1. [Onboard your sensor](#onboard-sensors), registering it again with any new settings.
1. [Upload your new activation file](how-to-manage-individual-sensors.md#upload-new-activation-files).

### Reactivate a sensor for upgrades to version 22.x from a legacy version

If you're updating your sensor version from a legacy version to 22.1.x or higher, you'll need a different activation procedure than for earlier releases.

Make sure that you've started with the relevant updates steps for this update. For more information, see [Update a standalone sensor version](how-to-manage-individual-sensors.md#update-a-standalone-sensor-version).

> [!NOTE]
> After upgrading to version 22.1.x, the new upgrade log can be found at the following path, accessed via SSH and the *cyberx_host* user: `/opt/sensor/logs/legacy-upgrade.log`.
>

## Upload a diagnostics log for support (Public preview)

If you need to open a support ticket for a locally managed sensor, upload a diagnostics log to the Azure portal for the support team.

> [!TIP]
> For cloud-connected sensors, the diagnostics log is automatically available to your support team when you open a support ticket.
>
**To upload a diagnostics report**:

1. Make sure you have the diagnostics report available for upload. For more information, see [Download a diagnostics log for support](how-to-manage-individual-sensors.md#download-a-diagnostics-log-for-support).

1. In Defender for IoT in the Azure portal, go to the **Sites and sensors** page and select the locally-managed sensor that's related to your support ticket.

1. For your selected sensor, select the **...** options menu on the right > **Send diagnostic files to support (Preview)**. For example:

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/upload-diagnostics-log.png" alt-text="Screenshot of the send diagnostic files to support option." lightbox="media/how-to-manage-sensors-on-the-cloud/upload-diagnostics-log.png":::


## Next steps

[View and manage alerts on the Defender for IoT portal (Preview)](how-to-manage-cloud-alerts.md)
