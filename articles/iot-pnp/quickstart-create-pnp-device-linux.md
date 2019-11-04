---
title: Create an Azure IoT Plug and Play Preview device (Linux) | Microsoft Docs
description: Use a device capability model to generate device code. Then run the device code and see the device connect to your IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc

# As a device builder, I want to try out generating device code from a model so I can understand the purpose of device capability models.
---

# Quickstart: Use a device capability model to create an IoT Plug and Play Preview device (Linux)

A _device capability model_ (DCM) describes the capabilities of an IoT Plug and Play device. A DCM is often associated with a product SKU. The capabilities defined in the DCM are organized into reusable interfaces. You can generate skeleton device code from a DCM. This quickstart shows you how to use VS Code on Ubuntu Linux to create an IoT Plug and Play device using a DCM.

## Prerequisites

This quickstart assumes you're using Ubuntu Linux with a desktop environment. The steps in this tutorial were tested using Ubuntu 18.04.

To complete this quickstart, you need to install the following software on your local Linux machine:

* Install **GCC**, **Git**, **cmake**, and all dependencies using the `apt-get` command:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verify the version of `cmake` is above **2.8.12** and the version of **GCC** is above **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### Install Azure IoT Tools

Use the following steps to install the [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) extension pack:

1. In VS Code, select the **Extensions** tab.
1. Search for **Azure IoT Tools**.
1. Select **Install**.

### Get the connection string for your company model repository

You can find your _company model repository connection string_ in the [Azure Certified for IoT portal](https://preview.catalog.azureiotsolutions.com) portal when you sign in with a Microsoft work or school account, or your Microsoft Partner ID if you have one. After you sign in, select **Company repository** and then **Connection strings**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Prepare an IoT hub

You need an Azure IoT hub in your Azure subscription to complete this quickstart. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

If you're using the Azure CLI locally, the `az` version should be **2.0.75** or later, the Azure Cloud Shell uses the latest version. Use the `az --version` command to check the version installed on your machine.

Add the Microsoft Azure IoT Extension for Azure CLI:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

The steps in this quickstart require version **0.8.5** or later of the extension. Use the `az extension list` command to check the version you have installed, and the `az extension update` command to update if necessary.

If don't have an IoT hub, create one using the following commands, replacing `{YourIoTHubName}` with a unique name of your choice. If you're running these commands locally, first sign in to your Azure subscription using `az login`. If you're running these commands in the Azure cloud shell, you're signed in automatically:

  ```azurecli-interactive
  az group create --name pnpquickstarts_rg --location centralus
  az iot hub create --name {YourIoTHubName} \
    --resource-group pnpquickstarts_rg --sku S1
  ```

The previous commands create a resource group called `pnpquickstarts_rg` and an IoT hub in the central US region.

> [!IMPORTANT]
> During public preview, IoT Plug and Play features are only available on IoT hubs created in the **Central US**, **North Europe**, and **Japan East** regions.

Run the following command to create a device identity for a device called `mypnpdevice` in your IoT hub. Replace the `{YourIoTHubName}` placeholder with the name of your IoT hub:

```azurecli-interactive
az iot hub device-identity create --hub-name {YourIoTHubName} --device-id mypnpdevice
```

Run the following commands to get the _device connection string_ for the device you just registered. You need this connection string later in this quickstart:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id mypnpdevice --output table
```

## Author your model

In this quickstart, you use an existing sample device capability model and associated interfaces.

1. Create a `pnp_app` directory in your home directory. You use this folder for the device model files and device code stub.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Download the device capability model and interface sample files to the `pnp_app` folder.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Open `pnp_app` folder with VS Code. You can view the files with intellisense:

    ![Device capability model](media/quickstart-create-pnp-device-linux/dcm.png)

1. In the files you downloaded, replace `<YOUR_COMPANY_NAME_HERE>` in the `@id` and `schema` fields with a unique value. Use only the characters a-z, A-Z, 0-9, and underscore. For more information, see [Digital Twin identifier format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## Generate the C code stub

Now you have a DCM and its associated interfaces, you can generate the device code that implements the model. To generate the C code stub in VS code:

1. With the `pnp_app` folder open in VS code, use **Ctrl+Shift+P** to open the command palette, enter **IoT Plug and Play**, and select **Generate Device Code Stub**.

    > [!NOTE]
    > The first time you use the IoT Plug and Play Code Generator utility, it takes a few seconds to download.

1. Choose the **SampleDevice.capabilitymodel.json** file to use to generate the device code stub.

1. Enter the project name **sample_device**, it will be the name of your device application.

1. Choose **ANSI C** as your language.

1. Choose **Via IoT Hub device connection string** as connection method.

1. Choose **CMake Project on Linux** as your project template.

1. Choose **Via Source Code** as the way to include the SDK.

1. VS Code opens a new window with generated device code stub files.
    ![Device code](media/quickstart-create-pnp-device-linux/device-code.png)

## Build and run the code

You use the device SDK source code to build the generated device code stub. The application you build simulates a device that connects to an IoT hub. The application sends telemetry and properties and receives commands.

1. Run the following commands to download the device SDK source code:

    ```bash
    cd ~/pnp_app/sample_device
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

1. Create a **CMake** build folder for the **sample_device** application:

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Run CMake to build your app with the SDK:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build .
    ```

1. After the build completes successfully, run your application passing the IoT hub device connection string as parameter.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "{IoT Hub device connection string}"
    ```

1. The device application starts sending data to IoT Hub.

    ![Device app running](media/quickstart-create-pnp-device-linux/device-app-running.png)

## Validate the code

### Publish device model files to model repository

To validate the device code with the **az** CLI, you need to publish the files to the model repository.

1. With the `pnp_app` folder open in VS code, use **Ctrl+Shift+P** to open the command palette, type and select **IoT Plug & Play: Submit files to Model Repository**.

1. Select `SampleDevice.capabilitymodel.json` and `EnvironmentalSensor.interface.json` files.

1. Enter your company model repository connection string.

    > [!NOTE]
    > The connection string is only required the first time you connect to the repository.

1. In VS Code output window and notification, you can check the files have been published successfully.

    > [!NOTE]
    > If you get errors on publishing the device model files, you can try use command **IoT Plug and Play: Sign out Model Repository** to sign out and go through the steps again.

### Use the Azure IoT CLI to validate the code

After the device client sample starts, you can check it's working with the Azure CLI.

Use the following command to view the telemetry the sample device is sending. You may need to wait a minute or two before you see any telemetry in the output:

```azurecli-interactive
az iot dt monitor-events --hub-name {your IoT hub} --device-id mypnpdevice
```

Use the following command to view all the properties sent by the device:

```azurecli-interactive
az iot dt list-properties --device-id mypnpdevice --hub-name {Your IoT hub name} --source private --repo-login "{Your company model repository connection string}"
```

## Next steps

In this quickstart, you learned how to create an IoT Plug and Play device using a DCM.

To learn more about DCMs and how to create your own models, continue to the tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Create a test a device capability model using Visual Studio Code](tutorial-pnp-visual-studio-code.md)
