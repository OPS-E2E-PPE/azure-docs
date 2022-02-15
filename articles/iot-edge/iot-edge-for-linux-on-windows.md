---
title: What is Azure IoT Edge for Linux on Windows | Microsoft Docs
description: Overview of you can run Linux IoT Edge modules on Windows 10 devices
author: kgremban

# this is the PM responsible
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/09/2022
ms.author: kgremban
---

# What is Azure IoT Edge for Linux on Windows

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge for Linux on Windows (EFLOW) allows you to run containerized Linux workloads alongside Windows applications in Windows IoT deployments. Businesses that rely on Windows IoT to power their edge devices can now take advantage of the cloud-native analytics solutions being built in Linux.

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
>[!NOTE]
>The latest version of [Azure IoT Edge for Linux on Windows continuous release (EFLOW CR)](./version-history.md), based on IoT Edge version 1.2, is in [public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A clean installation may be required for devices going into production use once the general availability (GA) release is available. For more information, see [EFLOW continuous release](https://github.com/Azure/iotedge-eflow/wiki/EFLOW-Continuous-Release).
:::moniker-end
<!-- end 1.2 -->

Azure IoT Edge for Linux on Windows works by running a Linux virtual machine on a Windows device. The Linux virtual machine comes pre-installed with the Azure IoT Edge runtime. Any Azure IoT Edge modules deployed to the device run inside the virtual machine. Meanwhile, Windows applications running on the Windows host device can communicate with the modules running in the Linux virtual machine.

[Get started](how-to-provision-single-device-linux-on-windows-symmetric.md) today.

## Components

Azure IoT Edge for Linux on Windows uses the following components to enable Linux and Windows workloads to run alongside each other and communicate seamlessly:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
* **A Linux virtual machine running Azure IoT Edge**: A Linux virtual machine, based on Microsoft's first party [CBL-Mariner](https://github.com/microsoft/CBL-Mariner) operating system, is built with the Azure IoT Edge runtime and validated as a tier 1 supported environment for Azure IoT Edge workloads.

* **Windows Admin Center**: An Azure IoT Edge extension for Windows Admin Center facilitates installation, configuration, and diagnostics of Azure IoT Edge on the Linux virtual machine. Windows Admin Center can deploy Azure IoT Edge for Linux on Windows on the local device, or can connect to target devices and manage them remotely.

* **Microsoft Update**: Integration with Microsoft Update keeps the Windows runtime components, the CBL-Mariner Linux VM, and Azure IoT Edge up to date.

![Windows and the Linux VM run in parallel, while the Windows Admin Center controls both components](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
* **A Linux virtual machine running Azure IoT Edge**: A Linux virtual machine, based on Microsoft's first party [CBL-Mariner](https://github.com/microsoft/CBL-Mariner) operating system, is built with the Azure IoT Edge runtime and validated as a tier 1 supported environment for Azure IoT Edge workloads.

* **Microsoft Update**: Integration with Microsoft Update keeps the Windows runtime components, the CBL-Mariner Linux VM, and Azure IoT Edge up to date.

> [!NOTE]
> Azure IoT Edge for Linux on Windows extension for Windows Amin Center (WAC) is not supported with this EFLOW version.

[ ![Windows and the Linux VM run in parallel, while the Windows Admin Center controls both components](./media/iot-edge-for-linux-on-windows/architecture-eflow1-2.png) ](./media/iot-edge-for-linux-on-windows/architecture-eflow1-2.png#lightbox)
:::moniker-end
<!-- end 1.2 -->

Bi-directional communication between Windows process and the Linux virtual machine means that Windows processes can provide user interfaces or hardware proxies for workloads run in the Linux containers.


## Prerequisites

A Windows device with the following minimum requirements:

* System Requirements
   * Windows 10<sup>1</sup>/11 (Pro, Enterprise, IoT Enterprise)
   * Windows Server 2019<sup>1</sup>/2022  
   <sub><sup>1</sup> Windows 10 and Windows Server 2019 minimum build 17763 with all current cumulative updates installed.</sub>

* Hardware requirements
  * Minimum Free Memory: 1 GB
  * Minimum Free Disk Space: 10 GB

## Supported versions
Azure IoT Edge for Linux on Windows supports the following versions:
- 1.1 LTS using [Azure IoT Edge 1.1 LTS](./version-history.md)
- Continuous Release (CR) using [Azure IoT Edge 1.2](./version-history.md) currently in [public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## Platform support
Azure IoT Edge for Linux on Windows supports the following architectures:

| Version | AMD64 | ARM64 |
| ---------------- | ----- |  ----- |
| EFLOW 1.1 LTS | ![AMD64](./media/support/green-check.png) | |
| EFLOW CR (public preview) | ![AMD64](./media/support/green-check.png) | ![ARM64](./media/support/green-check.png) |

## Samples

Azure IoT Edge for Linux on Windows emphasizes interoperability between the Linux and Windows components.

For samples that demonstrate communication between Windows applications and Azure IoT Edge modules, see [EFLOW GitHub](https://aka.ms/AzEFLOW-Samples).

## Support

Use the Azure IoT Edge support and feedback channels to get assistance with Azure IoT Edge for Linux on Windows.

**Reporting bugs** – Bugs related to Azure IoT Edge for Linux on Windows can be reported on the [iotedge-eflow issues page](https://aka.ms/AzEFLOW-Issues). Bugs related to Azure IoT Edge can be reported on the [issues page](https://github.com/azure/iotedge/issues) of the Azure IoT Edge open-source project.

**Microsoft Customer Support team** – Users who have a [support plan](https://azure.microsoft.com/support/plans/) can engage the Microsoft Customer Support team by creating a support ticket directly from the [Azure portal](https://portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Feature requests** – The Azure IoT Edge product tracks feature requests via the product's [User Voice page](https://feedback.azure.com/d365community/forum/0e2fff5d-f524-ec11-b6e6-000d3a4f0da0).

## Next steps

Watch [Azure IoT Edge for Linux on Windows 10 IoT Enterprise](https://aka.ms/azeflow-show) for more information and a sample in action.

Follow the steps in [Manually provision a single Azure IoT Edge for Linux on a Windows device](how-to-provision-single-device-linux-on-windows-symmetric.md) to set up a device with Azure IoT Edge for Linux on Windows.