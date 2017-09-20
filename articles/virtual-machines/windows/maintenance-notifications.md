---
title: Handling maintenance notifications for Windows VMs in Azure | Microsoft Docs
description: View maintenance notifications for Windows virtual machines running in Azure and start self-service maintenance.
services: virtual-machines-windows
documentationcenter: ''
author: zivraf
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: zivr

---


# Handling planned maintenance notifications for Windows virtual machines

Azure periodically performs updates to improve the reliability, performance, and security of the host infrastructure for virtual machines. Updates are changes like patching the hosting environment or upgrading and decommissioning hardware. A majority of these updates are performed without any impact to the hosted virtual machines. However, there are cases where updates do have an impact:

- If the maintenance does not require a reboot, Azure uses in-place migration to pause the VM while the host is updated.

- If maintenance requires a reboot, you get a notice of when the maintenance is planned. In these cases, you are given a time window where you can start the maintenance yourself, when it works for you.


Planned maintenance that requires a reboot, is scheduled in waves. Each wave has different scope (regions).

- A wave starts with a notification to customers. By default, notification is sent to subscription owner and co-owners. You can add more recipients and messaging options like email, SMS, and Webhooks, to the notifications.  
- Soon after the notification, a self-service window is set. During this window, you can find which of your virtual machines is included in this wave and start maintenance using proactive redeploy. 
- Following the self-service window, a scheduled maintenance window begins. At this time, Azure schedules and applies the required maintenance to your virtual machine. 

The goal in having two windows is to give you enough time to start maintenance and reboot your virtual machine while knowing when Azure will automatically start maintenance.


You can use the Azure portal, PowerShell, REST API, and CLI to query for the maintenance windows for your VMs and start self-service maintenance.

 > [!NOTE]
 > If you try to start maintenance and fail, Azure marks your VM as **skipped** and does not reboot it during the scheduled maintenance window. Instead, you are contacted in a later time with a new schedule. 


[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## Check maintenance status using PowerShell

You can also use Azure Powershell to see when VMs are scheduled for maintenance. Planned maintenance information is available from the [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) cmdlet when you use the `-status` parameter.
 
Maintenance information is returned only if there is maintenance planned. If there is no maintenance scheduled that impacts the VM, the cmdlet does not return any maintenance information. 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

The following properties are returned under MaintenanceRedeployStatus: 
| Value	| Description	|
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indicates whether you can start maintenance on the VM at this time ||
| PreMaintenanceWindowStartTime         | The beginning of the maintenance self-service window when you can initiate maintenance on your VM ||
| PreMaintenanceWindowEndTime           | The end of the maintenance self-service window when you can initiate maintenance on your VM ||
| MaintenanceWindowStartTime            | The beginning of the maintenance scheduled window when you can initiate maintenance on your VM ||
| MaintenanceWindowEndTime              | The end of the maintenance scheduled window when you can initiate maintenance on your VM ||
| LastOperationResultCode               | The result of the last attempt to initiate maintenance on the VM ||



You can also get the maintenance status for all VMs in a resource group by using [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) and not specifying a VM.
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName --Status
```

The following PowerShell function takes your subscription ID and prints out a list of VMs that are scheduled for maintenance.

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]
        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### Start maintenance on your VM using PowerShell

Using information from the function in the previous section, the following starts maintenance on a VM if **IsCustomerInitiatedMaintenanceAllowed** is set to true.

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```
 

## Next Steps

Learn how you can register for maintenance events from within the VM using [Scheduled Events](scheduled-events.md).