---
title: 'Connect to a VM using the native Windows client and Azure Bastion'
titleSuffix: Azure Bastion
description: Learn how to connect to a VM from a Windows computer by using Bastion and the native Windows client.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 11/01/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
---

# Connect to a VM using Bastion and the native client on your Windows computer

Azure Bastion now offers support for connecting to target VMs in Azure using a native client on your Windows local computer. This feature lets you connect to your target VMs via Bastion using Azure CLI and expands your sign-in options to include local SSH key pair, local username/password, and Azure Active Directory (Azure AD). This article helps you configure Bastion with the required settings, and then connect to a VM in the VNet. For more information, see the [What is Azure Bastion?](bastion-overview.md).

> [!NOTE]
> This configuration requires the Standard SKU for Azure Bastion.
>

Currently, this feature has the following limitations:

* Signing in to your target VM using a custom port or protocol is not yet available with native client support. If you want to use a custom port or protocol to sign in to your target VM via Bastion, use the Azure portal experience.

* Signing in using an SSH private key stored in Azure Key Vault is not supported with this feature. Download your private key to a file on your local machine before logging into your Linux VM using an SSH key pair.

## <a name="prereq"></a>Prerequisites

Before you begin, verify that you have met the following criteria:

* The latest version of the CLI commands is installed. For information about installing the CLI commands, see [Install the Azure CLI](/cli/azure/install-azure-cli) and [Get Started with Azure CLI](/cli/azure/get-started-with-azure-cli).
* An Azure virtual network.
* A virtual machine in the virtual network.

## Configure Bastion

Follow the instructions that pertain to your environment.

### To modify an existing bastion host

If you have already configured Bastion for your VNet, modify the following settings:

1. Navigate to the **Configuration** page for your Bastion resource. Verify that the SKU is **Standard**. If it isn't, change it to **Standard** from the dropdown.
1. Check the box for **Native Client Support** and apply your changes.

    :::image type="content" source="./media/connect-native-client-windows/update-host.png" alt-text="Settings for updating an existing host with Native Client Support box selected." lightbox="./media/connect-native-client-windows/update-host-expand.png":::
1. Install the CLI commands. Review the [Prerequisites](#prereq) for steps to install the latest version of the CLI commands.

### To configure a new bastion host

If you don't already have a bastion host configured, see [Create a bastion host](tutorial-create-host-portal.md#createhost). When configuring the bastion host, specify the following settings:

1. On the **Basics** tab, for **Instance Details -> Tier** select **Standard** to create a bastion host using the Standard SKU.

   :::image type="content" source="./media/connect-native-client-windows/standard.png" alt-text="Settings for a new bastion host with Standard SKU selected." lightbox="./media/connect-native-client-windows/standard.png":::
1. On the **Advanced** tab, check the box for **Native Client Support**.

   :::image type="content" source="./media/connect-native-client-windows/new-host.png" alt-text="Settings for a new bastion host with Native Client Support box selected." lightbox="./media/connect-native-client-windows/new-host-expand.png":::
1. Install the CLI commands. Review the [Prerequisites](#prereq) for steps to install the latest version of the CLI commands.

## Verify roles and ports

Verify that the following roles and ports are configured in order to connect.

### Required roles

* Reader role on the virtual machine.
* Reader role on the NIC with private IP of the virtual machine.
* Reader role on the Azure Bastion resource.

### Ports

To connect to a Windows VM using native client support, you must have the following ports open on your Windows VM:

* Inbound port: RDP (3389)

To connect to a Linux VM using native client support, you must have the following ports open on your Linux VM:

* Inbound port: SSH (22)

If you would like to use a custom port to connect to your target VM, use the Azure portal instructions instead.

## <a name="connect"></a>Connect to a VM

This section helps you connect to your virtual machine. Use the steps that correspond to the type of VM you want to connect to.

### Connect to a Linux VM

1. Sign into your Azure account and select your subscription containing your Bastion resource.

   ```azurecli-interactive
   az login
   az account list
   az account set --subscription "<subscription ID>"
   ```

1. Log into your target Linux VM using one of the following options:

   * If you are logging into an Azure AD-joined (AADJ) VM, use the following example. To learn more about how to use Azure AD to log into your Azure Linux VMs, see [Azure Linux VMs and Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md).

     ```azurecli-interactive
     az network bastion ssh --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type  "AAD"
     ```

   * If you are logging in using a local username and password, use the following example:

      ```azurecli-interactive
      az network bastion ssh "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "password" --username "<Username>" --password "<Password>"
      ```

   * If you are logging in using an SSH key pair, use the following example.

      ```azurecli-interactive
      az network bastion ssh "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "ssh-key" --username "<Username>" --ssh-key "<Filepath>"
      ```

### Connect to a Windows VM

1. Sign into your Azure account and select your subscription containing your Bastion resource.

   ```azurecli-interactive
   az login
   az account list
   az account set –subscription "<subscription ID>"

1. Log into your target Windows VM using one of the following options:

   * If you are logging into an Azure AD-joined (AADJ) VM, use the following command. To learn more about how to use Azure AD to log into your Azure Windows VMs, see [Azure Windows VMs and Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md).

      ```azurecli-interactive
      az network bastion rdp --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "AAD"
      ```

   * If you are logging in using a local username and password:

      ```azurecli-interactive
      az network bastion rdp "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "password" --username "<Username>" --password "<Password>" 
      ```

1. Once you log into your target VM, the native client on your local computer will open up with your VM session (**mstc** for RDP sessions and **az ssh** for SSH sessions).

## Next steps

Read the [Bastion FAQ](bastion-faq.md).
