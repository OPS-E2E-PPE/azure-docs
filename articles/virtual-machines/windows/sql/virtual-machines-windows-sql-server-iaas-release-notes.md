---
title: SQL Server on Azure VM Release Notes| Microsoft Docs
description: Learn about the new features and improvements of SQL Server on an Azure VM
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/5/2019
---
# SQL Server on Azure Virtual Machine release notes

Azure allows you to deploy a virtual machine with an image of SQL Server built in. This article summarizes the new features and improvements in the recent releases of [SQL Server on Azure virtual machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). The article also lists notable content updates that are not directly related to the release but published in the same time frame. For improvements to other Azure services, see [Service updates](https://azure.microsoft.com/updates)

## December 2018

### Service improvements

| Service improvements | Details |
| --- | --- |
| **New SQL cluster group resource provider** | A new resource provider (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) that defines the metadata of the Windows Failover Cluster. Joining a SQL Server VM to the *SqlVirtualMachineGroups* bootstraps the Windows Failover Cluster service and joins the VM to the cluster.  |
|**Automate setting up an availability group deployment with Azure Quickstart Templates** |It is now possible to create the Windows Failover Cluster, join SQL Server VMs to it, create the listener, and configure the Internal Load Balancer with two Azure Quickstart Templates. For more information, see [Create WSFC, listener, and configure ILB for an Always On availability group on a SQL Server VM with Azure Quickstart Template](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Automatic SQL VM Resource Provider Registration** | SQL Server VMs deployed after this month are automatically registered with the new SQL Server resource provider. SQL Server VMs deployed prior to this month still need to be manually registered. For more information, see [Register existing SQL VM with new resource provider](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |

### Documentation improvements

| Documentation improvements | Details |
| --- | --- |
|none | |
| | |

## November 2018

### Service improvements

| Service improvements | Details |
| --- | --- |
| **New SQL VM resource provider** |  A new resource provider for SQL Server VMs (Microsoft.SqlVirtualMachine) that provides better management of your SQL Server VM. For more information on registering your VM, see [Register existing SQL VM with new resource provider](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider). |
|**Switch licensing model** |You can now switch between the pay-per-usage and bring-your-own license model for your SQL VM using Azure CLI or Powershell. For more information, see [How to change the licensing model for a SQL VM](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

### Documentation improvements

| Documentation improvements | Details |
| --- | --- |
|none | |
| | |

## Additional resources

**Windows VMs**:

* [Overview of SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-overview.md).
* [Provision a SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrating a Database to SQL Server on an Azure VM](virtual-machines-windows-migrate-sql.md)
* [High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Performance best practices for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Application Patterns and Development Strategies for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VMs**:

* [Overview of SQL Server on a Linux VM](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Provision a SQL Server Linux Virtual Machine](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server on Linux documentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)