---
title: Provision device with a virtual TPM on Linux - Azure IoT Edge
description: Use a simulated TPM on a Linux device to test Azure Device Provisioning Service for Azure IoT Edge
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 07/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
---
# Create and provision IoT Edge devices at-scale with a TPM on Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

This article provides end-to-end instructions for auto-provisioning a Linux IoT Edge device using a Trusted Platform Module (TPM). You can automatically provision Azure IoT Edge devices with the [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). If you're unfamiliar with the process of auto-provisioning, review the [provisioning overview](../iot-dps/about-iot-dps.md#provisioning-process) before continuing.

This article outlines two methodologies. Select your preference based on the architecture of your solution:

1. Auto-provision a Linux device with physical TPM hardware. For example, the [Infineon OPTIGA&trade; TPM SLB 9670](https://devicecatalog.azure.com/devices/3f52cdee-bbc4-d74e-6c79-a2546f73df4e).
1. Auto-provision a Linux virtual machine (VM) with a simulated TPM running on a Windows development machine with Hyper-V enabled. This is recommended only as a testing scenario, because a simulated TPM does not offer the same security as a physical TPM.

Instructions differ based on your methodology, so make sure you are on the correct tab going forward.

# [Physical device](#tab/physical-device)

The tasks are as follows:

1. Retrieve provisioning information for your TPM.
1. Create an individual enrollment for your device in an instance of the IoT Hub Device Provisioning Service.
1. Install the IoT Edge runtime and connect the device to the IoT hub.

# [Virtual machine](#tab/virtual-machine)

The tasks are as follows:

1. Create a Linux virtual machine in Hyper-V with a simulated TPM for hardware security.
1. Retrieve provisioning information for your TPM.
1. Create an individual enrollment for your device in an instance of the IoT Hub Device Provisioning Service.
1. Install the IoT Edge runtime and connect the device to the IoT hub.

---

## Prerequisites

# [Physical device](#tab/physical-device)

* An active IoT hub.
* An instance of the IoT Hub Device Provisioning Service in Azure, linked to your IoT hub.
  * If you don't have a Device Provisioning Service instance, you can follow the instructions in the [Create a new IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) and [Link the IoT hub and your Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) sections of the IoT Hub Device Provisioning Service quickstart.
  * After you have the Device Provisioning Service running, copy the value of **ID Scope** from the overview page. You use this value when you configure the IoT Edge runtime.

# [Virtual machine](#tab/virtual-machine)

* An active IoT Hub.
* An instance of the IoT Hub Device Provisioning Service in Azure, linked to your IoT hub.
  * If you don't have a Device Provisioning Service instance, you can follow the instructions in the [Create a new IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) and [Link the IoT hub and your Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) sections of the IoT Hub Device Provisioning Service quickstart.
  * After you have the Device Provisioning Service running, copy the value of **ID Scope** from the overview page. You use this value when you configure the IoT Edge runtime.
* A Windows development machine with [Hyper-V enabled](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). This article uses Windows 10 running an Ubuntu Server VM.

---

> [!NOTE]
> TPM 2.0 is required when using TPM attestation with DPS.
>
> You can only create individual, not group, DPS enrollments when using a TPM.

## Set up your device

# [Physical device](#tab/physical-device)

If you're using a physical Linux device with a TPM, there are no extra steps to set up your device.

You're ready to continue.

# [Virtual machine](#tab/virtual-machine)

In this section, you create a new Linux virtual machine on Hyper-V. You configure this virtual machine with a simulated TPM for testing how automatic provisioning works with IoT Edge.

> [!TIP]
> If you are using a virtual machine, you'll copy and paste on the virtual machine many times throughout this article. Copying and pasting is not easy through the Hyper-V Manager connection application. You may want to connect to the virtual machine through Hyper-V Manager once to retrieve its IP address. First run `sudo apt install net-tools` and then `hostname -I`. Then, you can use the IP address to connect through SSH: `ssh <username>@<ipaddress>`.

### Create a virtual switch

A virtual switch enables your virtual machine to connect to a physical network.

1. Open Hyper-V manager on your Windows machine.

1. In the **Actions** menu, select **Virtual Switch Manager**.

1. Choose an **External** virtual switch, then select **Create Virtual Switch**.

1. Give your new virtual switch a name. For example, **EdgeSwitch**. Make sure that the connection type is set to **External network**, then select **Ok**.

1. A pop-up warns you that network connectivity may be disrupted. Select **Yes** to continue.

> [!TIP]
> If you see errors while creating the new virtual switch, ensure that no other switches are using the ethernet adapter, and that no other switches use the same name.

### Create the virtual machine

Create a new VM from a bootable image file.

1. Download a disk image file to use for your virtual machine and save it locally. For example, [Ubuntu server 18.04](http://releases.ubuntu.com/18.04/). For information about supported operating systems for IoT Edge devices, see [Azure IoT Edge supported systems](/support.md).

1. In Hyper-V Manager, select **Action** > **New** > **Virtual Machine** in the **Actions** menu.

1. Complete the **New Virtual Machine Wizard** with the following specific configurations:

   1. **Specify Generation**: Select **Generation 2**. Generation 2 virtual machines have nested virtualization enabled, which is required to run IoT Edge on a virtual machine.
   1. **Configure Networking**: Set the value of **Connection** to the virtual switch that you created in the previous section.
   1. **Installation Options**: Select **Install an operating system from a bootable image file** and browse to the disk image file that you saved locally.

1. Select **Finish** in the wizard to create the virtual machine.

It may take a few minutes to create the new VM.

### Enable virtual TPM

Once your VM is created, open its settings to enable the virtual TPM that lets you auto-provision the device.

1. In Hyper-V Manager, right-click on the VM and select **Settings**.

1. Navigate to **Security**.

1. Uncheck **Enable Secure Boot**.

1. Check **Enable Trusted Platform Module**.

1. Click **OK**.

### Start the virtual machine

Start your virtual machine to complete the installation process.

1. In Hyper-V Manager, start your VM and connect to it.

1. Follow the prompts within the virtual machine to finish the installation process and reboot the machine.

Once the installation is finished and you're logged back into your VM, you're ready to continue.

---

## Retrieve provisioning information for your TPM

In this section, you build a tool that you can use to retrieve the **Registration ID** and **Endorsement key** for your TPM.

1. Log onto your device, then follow the steps in [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) to install and build the Azure IoT device SDK for C.

1. Run the following commands to build the SDK tool that retrieves your device provisioning information for your TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. The output window displays the device's **Registration ID** and the **Endorsement key**. Copy these values for use later when you create an individual enrollment for your device in DPS.

> [!TIP]
> If you do not want to use the SDK tool to retrieve the information, you need to find another way to obtain the provisioning information. The **Endorsement key**, which is unique to each TPM chip, is obtained from the TPM chip manufacturer associated with it. You can derive a unique **Registration ID** for your TPM device by, for example, creating an SHA-256 hash of the endorsement key.

Once you have your registration ID and endorsement key, you're ready to continue.

## Create a DPS enrollment

Retrieve the provisioning information from your TPM, and use that to create an individual enrollment in the Device Provisioning Service.

When you create an enrollment in DPS, you have the opportunity to declare an **Initial Device Twin State**. In the device twin, you can set tags to group devices by any metric used in your solution, like region, environment, location, or device type. These tags are used to create [automatic deployments](how-to-deploy-at-scale.md).

> [!TIP]
> The steps in this article are for the Azure portal, but you can also create individual enrollments using the Azure CLI. For more information, see [az iot dps enrollment](/cli/azure/iot/dps/enrollment). As part of the CLI command, use the **edge-enabled** flag to specify that the enrollment is for an IoT Edge device.

1. In the [Azure portal](https://portal.azure.com), navigate to your instance of the IoT Hub Device Provisioning Service.

1. Under **Settings**, select **Manage enrollments**.

1. Select **Add individual enrollment**, then complete the following steps to configure the enrollment:

   1. For **Mechanism**, select **TPM**.

   1. Provide the **Endorsement key** and **Registration ID** that you copied from your virtual machine or physical device.

   1. Provide an ID for your device if you'd like. If you don't provide a device ID, the registration ID is used.

   1. Select **True** to declare that your virtual machine or physical device is an IoT Edge device.

   1. Choose the linked IoT hub that you want to connect your device to, or select **Link to new IoT Hub**. You can choose multiple hubs, and the device will be assigned to one of them according to the selected assignment policy.

   1. Add a tag value to the **Initial Device Twin State** if you'd like. You can use tags to target groups of devices for module deployment. For more information, see [Deploy IoT Edge modules at scale](how-to-deploy-at-scale.md).

   1. Select **Save**.

Now that an enrollment exists for this device, the IoT Edge runtime can automatically provision the device during installation.

## Install the IoT Edge runtime

In this section, you prepare your Linux virtual machine or physical device for IoT Edge. Then, you will install IoT Edge.

There are two steps you need to complete on your device before it is ready to install the IoT Edge runtime. Your device needs access to the Microsoft installation packages, and it needs a container engine installed.

### Access the Microsoft installation packages

Your device must have access to the Microsoft installation packages.

1. Install the repository configuration that matches your device's operating system.

   * **Ubuntu Sever 18.04**:

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. Copy the generated list to the sources.list.d directory.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Install the Microsoft GPG public key.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trust.gpg.d/
   ```

> [!NOTE]
> Azure IoT Edge software packages are subject to the license terms located in each package (`usr/share/doc/{package-name}` or the `LICENSE` directory). Read the license terms prior to using a package. Your installation and use of a package constitutes your acceptance of these terms. If you do not agree with the license terms, do not use that package.

### Install a container engine

Azure IoT Edge relies on an OCI-compatible container runtime. For production scenarios, we recommend that you use the Moby engine. The Moby engine is the only container engine officially supported with Azure IoT Edge. Docker CE/EE container images are compatible with the Moby runtime.

1. Update package lists on your device.

   ```bash
   sudo apt-get update
   ```

1. Install the Moby engine.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > If you get errors when installing the Moby container engine, verify your Linux kernel for Moby compatibility. Some embedded device manufacturers ship device images that contain custom Linux kernels without the features required for container engine compatibility. Run the following command, which uses the [check-config script](https://github.com/moby/moby/blob/master/contrib/check-config.sh) provided by Moby, to check your kernel configuration.
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > In the output of the script, check that all items under `Generally Necessary` and `Network Drivers` are enabled. If you are missing features, enable them by rebuilding your kernel from source and selecting the associated modules for inclusion in the appropriate kernel .config. Similarly, if you are using a kernel configuration generator like `defconfig` or `menuconfig`, find and enable the respective features and rebuild your kernel accordingly. Once you have deployed your newly modified kernel, run the check-config script again to verify that all the required features were successfully enabled.

### Install IoT Edge

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

The IoT Edge security daemon provides and maintains security standards on the IoT Edge device. The daemon starts on every boot and bootstraps the device by starting the rest of the IoT Edge runtime.

The steps in this section represent the typical process to install the latest version on a device that has internet connectivity. If you need to install a specific version, like a pre-release version, or need to install while offline, follow the Offline or specific version installation steps.

1. Update package lists on your device.

   ```bash
   sudo apt-get update
   ```

1. Install IoT Edge version 1.1* along with the **libiothsm-std** package.

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > *IoT Edge version 1.1 is the long-term support branch of IoT Edge. If you running an older version, we recommend installing or updating to the latest patch as older versions are no longer supported.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

The IoT Edge service provides and maintains security standards on the IoT Edge device. The service starts on every boot and bootstraps the device by starting the rest of the IoT Edge runtime.

The IoT identity service was introduced along with version 1.2 of IoT Edge. This service handles the identity provisioning and management for IoT Edge and for other device components that need to communicate with IoT Hub.

The steps in this section represent the typical process to install the latest version on a device that has internet connection. If you need to install a specific version, like a pre-release version, or need to install while offline, follow the Offline or specific version installation steps.

Update package lists on your device.

   ```bash
   sudo apt-get update
   ```

Check to see which versions of IoT Edge and the IoT identity service are available.

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

To install the latest version of IoT Edge and the IoT identity service package, use the following command:

   ```bash
   sudo apt-get install aziot-edge
   ```

Or, if you choose to install a different version of IoT Edge than the latest, be sure to install the same version for both the `aziot-edge` and the `aziot-identity-service` services.

:::moniker-end
<!-- end 1.2 -->

## Configure the device with provisioning information

Once the runtime is installed on your device, configure the device with the information it uses to connect to the Device Provisioning Service and IoT Hub.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Know your DPS **ID Scope** and device **Registration ID** that were gathered previously.

1. Open the configuration file on your IoT Edge device.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Find the provisioning configuration section of the file. Uncomment the lines for TPM provisioning, and make sure any other provisioning lines are commented out.

   The `provisioning:` line should have no preceding whitespace, and nested items should be indented by two spaces.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Update the values of `scope_id` and `registration_id` with your DPS and device information. The `scope_id` is the **ID Scope** from your DPS instance's overview page

1. Optionally, use the `always_reprovision_on_startup` or `dynamic_reprovisioning` lines to configure your device's reprovisioning behavior. If a device is set to reprovision on startup, it will always attempt to provision with DPS first and then fall back to the provisioning backup if that falls. If a device is set to dynamically provision itself, IoT Edge with restart and reprovision if a reprovisioning event is detected. For more information, see [IoT Hub device reprovisioning concepts](../iot-dps/concepts-device-reprovision.md).

1. Save and close the file.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Know your DPS **ID Scope** and device **Registration ID** that were gathered previously.

1. Create a configuration file for your device based on a template file that is provided as part of the IoT Edge installation.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Open the configuration file on the IoT Edge device.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Find the provisioning configurations section of the file. Uncomment the lines for TPM provisioning, and make sure any other provisioning lines are commented out.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. Update the values of `id_scope` and `registration_id` with your DPS and device information. The `scope_id` is the **ID Scope** from your DPS instance's overview page

1. Optionally, find the auto-reprovisioning mode section of the file. Use the `auto_reprovisioning_mode` parameter to configure your device's reprovisioning behavior to either `Dynamic`, `AlwaysOnStartup`, or `OnErrorOnly`. For more information, see [IoT Hub device reprovisioning concepts](../iot-dps/concepts-device-reprovision.md).

1. Save and close the file.

:::moniker-end
<!-- end 1.2 -->

## Give IoT Edge access to the TPM

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

The IoT Edge runtime needs to access the TPM to automatically provision your device.

You can give TPM access to the IoT Edge runtime by overriding the systemd settings so that the `iotedge` service has root privileges. If you don't want to elevate the service privileges, you can also use the following steps to manually provide TPM access.

1. Create a new rule that will give the IoT Edge runtime access to tpm0 and tpmrm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Open the rules file.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Copy the following access information into the rules file. The `tpmrm0` may not be present on devices using a kernel earlier than 4.12. Devices that do not have tpmrm0 will safely ignore that rule.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

1. Save and exit the file.

1. Trigger the udev system to evaluate the new rule.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. Verify that the rule was successfully applied.

   ```bash
   ls -l /dev/tpm*
   ```

   Successful output appears as follows:

   ```output
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpm0
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   If you don't see that the correct permissions have been applied, try rebooting your machine to refresh udev.

1. Restart the IoT Edge runtime so that it picks up all the configuration changes that you made on the device.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

The IoT Edge runtime relies on a TPM service that broker's access to a device's TPM. This service needs to access the TPM to automatically provision your device.

You can give access to the TPM by overriding the systemd settings so that the `aziottpm` service has root privileges. If you don't want to elevate the service privileges, you can also use the following steps to manually provide TPM access.

1. Create a new rule that will give the IoT Edge runtime access to tpm0 and tpmrm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Open the rules file.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Copy the following access information into the rules file. The `tpmrm0` may not be present on devices using a kernel earlier than 4.12. Devices that do not have tpmrm0 will safely ignore that rule.

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

1. Save and exit the file.

1. Trigger the udev system to evaluate the new rule.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. Verify that the rule was successfully applied.

   ```bash
   ls -l /dev/tpm*
   ```

   Successful output appears as follows:

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   If you don't see that the correct permissions have been applied, try rebooting your machine to refresh udev.

1. Apply the configuration changes that you made on the device.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## Restart IoT Edge and verify successful installation

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
If you did not already, restart the IoT Edge runtime so that it picks up all the configuration changes that you made on the device.

   ```bash
   sudo systemctl restart iotedge
   ```

Check to see that the IoT Edge runtime is running.

   ```bash
   sudo systemctl status iotedge
   ```

Examine daemon logs.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

If you see provisioning errors, it may be that the configuration changes haven't taken effect yet. Try restarting the IoT Edge daemon again.

   ```bash
   sudo systemctl daemon-reload
   ```

Or, try restarting your virtual machine to see if the changes take effect on a fresh start.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
If you did not already, apply the configuration changes that you made on the device.

   ```bash
   sudo iotedge config apply
   ```

Check to see that the IoT Edge runtime is running.

   ```bash
   sudo iotedge system status
   ```

Examine daemon logs.

   ```cmd/sh
   sudo iotedge system logs
   ```

If you see provisioning errors, it may be that the configuration changes haven't taken effect yet. Try restarting the IoT Edge daemon.

   ```bash
   sudo systemctl daemon-reload
   ```

Or, try restarting your virtual machine to see if the changes take effect on a fresh start.
:::moniker-end
<!-- end 1.2 -->

If the runtime started successfully, you can go into your IoT Hub and see that your new device was automatically provisioned. Now your device is ready to run IoT Edge modules.

List running modules.

```cmd/sh
iotedge list
```

You can verify that the individual enrollment that you created in Device Provisioning Service was used. Navigate to your Device Provisioning Service instance in the Azure portal. Open the enrollment details for the individual enrollment that you created. Notice that the status of the enrollment is **assigned** and the device ID is listed.

## Next steps

The DPS enrollment process lets you set the device ID and device twin tags at the same time as you provision the new device. You can use those values to target individual devices or groups of devices using automatic device management. Learn how to [Deploy and monitor IoT Edge modules at scale using the Azure portal](how-to-deploy-at-scale.md) or [using Azure CLI](how-to-deploy-cli-at-scale.md).
