---
title: Get started with Microsoft Defender for IoT
description: In this quickstart, set up a trial for Microsoft Defender for IoT and understand next steps required to configure your network sensors.
ms.topic: quickstart
ms.date: 03/24/2022
---

# Quickstart: Get started with Defender for IoT

This quickstart takes you through the initial steps of setting up Defender for IoT, including:

- Identify and plan OT monitoring system architecture
- Add Defender for IoT to an Azure subscription

You can use this procedure to set up a Defender for IoT trial. The trial provides 30-day support for 1000 devices and a virtual sensor, which you can use to monitor traffic, analyze data, generate alerts, understand network risks and vulnerabilities and more.

## Prerequisites

Before you start, make sure that you have:

- An Azure account. If you don't already have an Azure account, you can [create your free Azure account today](https://azure.microsoft.com/free/).

- Access to the Azure portal as a [Security Admin](../../role-based-access-control/built-in-roles.md#security-admin), [Contributor](../../role-based-access-control/built-in-roles.md#contributor), or [Owner](../../role-based-access-control/built-in-roles.md#owner). For more information, see [Azure user roles for OT and Enterprise IoT monitoring with Defender for IoT](roles-azure.md).

If you're using a Defender for IoT sensor version earlier than 22.1.x, you must also have an Azure IoT Hub (Free or Standard tier) **Contributor** role, for cloud-connected management. Make sure that the **Microsoft Defender for IoT** feature is enabled.

### Supported service regions

Defender for IoT routes all traffic from all European regions to the *West Europe* regional datacenter. It routes traffic from all remaining regions to the *East US* regional datacenter.

If you're using a legacy experience of Defender for IoT and are connecting through your own IoT Hub, the IoT Hub supported regions are also relevant for your organization. For more information, see [IoT Hub supported regions](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## Identify and plan your OT solution architecture

We recommend that you identify system requirements and plan your OT network monitoring architecture before you start, even if you plan to start with a trial subscription.

- To deploy Defender for IoT, you'll need network switches that support traffic monitoring via a SPAN port and hardware appliances for NTA sensors.

   For on-premises machines, including network sensors, on-premises management consoles and for air-gapped environments you'll need administrative user permissions for activities. These include activation, managing SSL/TLS certificates, managing passwords, and so on.

- Research your own network architecture and monitor bandwidth. Check requirements for creating certificates and other network details, and clarify the sensor appliances you'll need for your own network load.

   Calculate the approximate number of devices you'll be monitoring. Devices can be added in intervals of **100**, such as **100**, **200**, **300**. The numbers of monitored devices are called *committed devices*. For more information, see [What is a Defender for IoT committed device?](architecture.md#what-is-a-defender-for-iot-committed-device)

Microsoft Defender for IoT supports both physical and virtual deployments. For physical deployments, you'll be able to purchase certified, pre-configured appliances, or download software to install yourself.

For more information, see:

- [Best practices for planning your OT network monitoring](best-practices/plan-network-monitoring.md)
- [Sensor connection methods](architecture-connections.md)
- [Prepare your OT network for Microsoft Defender for IoT](how-to-set-up-your-network.md)
- [Predeployment checklist](pre-deployment-checklist.md)
- [Identify required appliances](how-to-identify-required-appliances.md)

## Add a Defender for IoT plan for OT networks

This procedure describes how to add a Defender for IoT plan for OT networks to an Azure subscription.

**To onboard a Defender for IoT plan for OT networks**:

1. In the Azure portal, go to **Defender for IoT** > **Plans and pricing**.

1. Select **Add plan**.

1. In the **Plan settings** pane, define the plan:

    - **Subscription**. Select the subscription where you would like to add a plan.

        You'll need a [Security admin](/azure/role-based-access-control/built-in-roles#security-admin), [Contributor](/azure/role-based-access-control/built-in-roles#contributor), or [Owner](/azure/role-based-access-control/built-in-roles#owner) role for the subscription.

        > [!TIP]
        > If your subscription isn't listed, check your account details and confirm your permissions with the subscription owner.

     - **Price plan**. Select a monthly or annual commitment, or a [trial](billing.md#free-trial).

        Microsoft Defender for IoT provides a 30-day free trial for the first 1,000 committed devices for evaluation purposes.

        For more information, see the [Microsoft Defender for IoT pricing page](https://azure.microsoft.com/pricing/details/iot-defender/).

    - **Committed sites**. Relevant for annual commitments only. Enter the number of committed sites.

    - **Number of devices**. If you selected a monthly or annual commitment, enter the number of [committed devices](architecture.md#what-is-a-defender-for-iot-committed-device) you'll want to monitor. If you select a trial, there is a default of 1000 devices.

    For example:

    :::image type="content" source="media/how-to-manage-subscriptions/onboard-ot-plans-pricing.png" alt-text="Screenshot of the plan settings pane to add or edit a plan for OT networks." lightbox="media/how-to-manage-subscriptions/onboard-ot-plans-pricing.png":::

1. Select **Next**.

1. Review your plan, select the **I accept the terms** option, and then select **Purchase**.

Your new plan is listed under the relevant subscription in the **Plans** grid. For more information, see [Manage your subscriptions](how-to-manage-subscriptions.md).

## Next steps

Continue with [Tutorial: Get started with OT network security](tutorial-onboarding.md) or [Enhance IoT security monitoring with an Enterprise IoT network sensor (Public preview)](eiot-sensor.md).

For more information, see:

- [Welcome to Microsoft Defender for IoT for organizations](overview.md)
- [Microsoft Defender for IoT architecture](architecture.md)
- [Defender for IoT subscription billing](billing.md)
