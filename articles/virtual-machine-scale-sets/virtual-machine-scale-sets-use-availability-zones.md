---
title: Create an Azure scale set that uses Availability Zones (Preview) | Microsoft Docs
description: Learn how to create Azure virtual machine scale sets that use Availability Zones for increased redundancy against outages
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor:
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: iainfou

---

# Create a virtual machine scale set that uses Availability Zones (Preview)
To protect your virtual machine scale sets from datacenter-level failures, you can create a scale set in an Availability Zone. Azure regions that support Availability Zones have a minimum of three separate zones, each with their own independent power source, network, and cooling. For more information, see [Overview of Availability Zones](../availability-zones/az-overview.md).

To use Availability Zones, your scale set must be created in a [supported Azure region](../availability-zones/az-overview.md#regions-that-support-availability-zones). You can create a scale set that uses Availability Zones with one of the following methods:

- [Azure portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager templates](#use-azure-resource-manager-templates)


## Use the Azure portal
The process to create a scale set that uses an Availability Zone is the same as detailed in the [getting started article](virtual-machine-scale-sets-create-portal.md). When you select a supported Azure region, you can create a scale set in one of the available zones, as shown in the following example:

![Create a scale set in a single Availability Zone](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

The scale set and supporting resources, such as the Azure load balancer and public IP address, are created in the single zone that you specify.


## Use the Azure CLI 2.0
The process to create a scale set that uses an Availability Zone is the same as detailed in the [getting started article](virtual-machine-scale-sets-create-cli.md). To use Availability Zones, you must create your scale set in a supported Azure region. Add the `--zones` parameter to the [az vmss create](/cli/azure/vmss#az_vmss_create) command and specify which zone to use (such as zone *1*, *2*, or *3*). The following example creates a scale set named *myScaleSet* in zone *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

It takes a few minutes to create and configure all the scale set resources and VMs in the zone that you specify.


## Use Azure PowerShell
The process to create a scale set that uses an Availability Zone is the same as detailed in the [getting started article](virtual-machine-scale-sets-create-powershell.md). To use Availability Zones, you must create your scale set in a supported Azure region. Add the `-Zone` parameter to the [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) command and specify which zone to use (such as zone *1*, *2*, or *3*). The following example creates a scale set config named *vmssConfig* in *East US 2* zone *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

To create the actual scale set, follow the additional steps as detailed in the [getting started article](virtual-machine-scale-sets-create-powershell.md).


## Use Azure Resource Manager templates
The process to create a scale set that uses an Availability Zone is the same as detailed in the getting started article for [Linux](virtual-machine-scale-sets-create-template-linux.md) or [Windows](virtual-machine-scale-sets-create-template-windows.md). To use Availability Zones, you must create your scale set in a supported Azure region. Add the `zones` property to the *Microsoft.Compute/virtualMachineScaleSets* resource type in your template and specify which zone to use (such as zone *1*, *2*, or *3*). The following example creates a Linux scale set named *myScaleSet* in *East US 2* zone *1*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

To create the actual scale set, follow the additional steps as detailed in the getting started article for [Linux](virtual-machine-scale-sets-create-template-linux.md) or [Windows](virtual-machine-scale-sets-create-template-windows.md)


## Next steps
Now that you have created a scale set in an Availability Zone, you can learn how to [Deploy applications on virtual machine scale sets](virtual-machine-scale-sets-deploy-app.md) or [Use autoscale with virtual machine scale sets](virtual-machine-scale-sets-autoscale-overview.md).
