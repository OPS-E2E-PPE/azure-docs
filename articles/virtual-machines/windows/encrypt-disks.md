---
title: Encrypt disks on a Windows VM in Azure | Microsoft Docs
description: How to encrypt virtual disks on a Windows VM for enhanced security using Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager

ms.assetid:
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn

---
# How to encrypt virtual disks on a Windows VM
For enhanced virtual machine (VM) security and compliance, virtual disks in Azure can be encrypted. Disks are encrypted using cryptographic keys that are secured in an Azure Key Vault. You control these cryptographic keys and can audit their use. This article details how to encrypt virtual disks on a Windows VM using Azure PowerShell. You can also [encrypt a Linux VM using the Azure CLI](../linux/encrypt-disks.md).

## Overview of disk encryption

Virtual disks on Windows VMs are encrypted at rest using Bitlocker. There is no charge for encrypting virtual disks in Azure. Cryptographic keys are stored in Azure Key Vault using software-protection, or you can import or generate your keys in Hardware Security Modules (HSMs) certified to FIPS 140-2 level 2 standards. These cryptographic keys are used to encrypt and decrypt virtual disks attached to your VM. You retain control of these cryptographic keys and can audit their use. 

The process for encrypting a VM is as follows:

1. Create a cryptographic key in an Azure Key Vault.
1. Configure the cryptographic key to be usable for encrypting disks.
1. Enable disk encryption for your virtual disks.
1. The required cryptographic keys are requested from Azure Key Vault.
1. The virtual disks are encrypted using the provided cryptographic key.

### Azure Key Vault

Disk encryption relies on [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Key Vault is used to safeguard cryptographic keys and secrets used for the disk encryption/decryption process. 
  * If one exists, you can use an existing Azure Key Vault. You do not have to dedicate a Key Vault to encrypting disks.
  * To separate administrative boundaries and key visibility, you can create a dedicated Key Vault.


## Requirements and limitations

Supported scenarios and requirements for disk encryption:

* Enabling encryption on new Windows VMs from Azure Marketplace images or custom VHD image.
* Enabling encryption on existing Windows VMs in Azure.
* Enabling encryption on Windows VMs that are configured using Storage Spaces.
* Disabling encryption on OS and data drives for Windows VMs.
* All resources (such as Key Vault, Storage account, and VM) must be in the same Azure region and subscription.
* Standard tier VMs, such as A, D, DS, G, and GS series VMs.

Disk encryption is not currently supported in the following scenarios:

* Basic tier VMs.
* VMs created using the Classic deployment model.
* Updating the cryptographic keys on an already encrypted VM.
* Integration with on-prem Key Management Service.

## Create Azure Key Vault and keys

Before you start, make sure that the latest version of the Azure PowerShell module has been installed. For more information, see [How to install and configure Azure PowerShell](/powershell/azure/overview). Throughout the command examples, replace all example parameters with your own names, location, and key values. The following examples use a convention of *myResourceGroup*, *myKeyVault*, *myVM*, etc.

The first step is to create an Azure Key Vault to store your cryptographic keys. Azure Key Vault can store keys, secrets, or passwords that allow you to securely implement them in your applications and services. For virtual disk encryption, you create a Key Vault to store a cryptographic key that is used to encrypt or decrypt your virtual disks. 

Enable the Azure Key Vault provider within your Azure subscription with [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), then create a resource group with [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). The following example creates a resource group name *myResourceGroup* in the *East US* location:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

The Azure Key Vault containing the cryptographic keys and associated compute resources such as storage and the VM itself must reside in the same region. Create an Azure Key Vault with [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) and enable the Key Vault for use with disk encryption. Specify a unique Key Vault name for *keyVaultName* as follows:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

You can store cryptographic keys using software or Hardware Security Model (HSM) protection. Using an HSM requires a premium Key Vault. There is an additional cost to creating a premium Key Vault rather than standard Key Vault that stores software-protected keys. To create a premium Key Vault, in the preceding step add the *-Sku "Premium"* parameters. The following example uses software-protected keys since we created a standard Key Vault. 

For both protection models, the Azure platform needs to be granted access to request the cryptographic keys when the VM boots to decrypt the virtual disks. Create a cryptographic key in your Key Vault with [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). The following example creates a key named *myKey*:

```azurepowershell-interactive
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## Create virtual machine
To test the encryption process, create a VM with [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). The following example creates a VM named *myVM* using a *Windows Server 2016 Datacenter* image. When prompted for credentials, enter the username and password to be used for your VM:

```azurepowershell-interactive
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## Encrypt virtual machine
To encrypt the virtual disks, you bring together all the previous components:

1. Specify the Azure Active Directory service principal and password.
2. Specify the Key Vault to store the metadata for your encrypted disks.
3. Specify the cryptographic keys to use for the actual encryption and decryption.
4. Specify whether you want to encrypt the OS disk, the data disks, or all.

Encrypt your VM with [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) using the Azure Key Vault key and Azure Active Directory service principal credentials. The following example retrieves all the key information then encrypts the VM named *myVM*:

```azurepowershell-interactive
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Accept the prompt to continue with the VM encryption. The VM restarts during the process. Once the encryption process completes and the VM has rebooted, review the encryption status with [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

The output is similar to the following example:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## Next steps
* For more information about managing Azure Key Vault, see [Set up Key Vault for virtual machines](key-vault-setup.md).
* For more information about disk encryption, such as preparing an encrypted custom VM to upload to Azure, see [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
