---
title: About Azure Migrate | Microsoft Docs
description: Provides an overview of the Azure Migrate service.
services: migrate
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''

ms.assetid: 7b313bb4-c8f4-43ad-883c-789824add3288
ms.service: migrate
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2017
ms.author: raynew

---

# About Azure Migrate

The Azure Migrate service assesses on-premises workloads for migration to Azure. The service assesses migration suitability and performance-based sizing, and provides cost estimations for running your on-premises machines in Azure. If you're contemplating lift-and-shift migrations, or are in the early assessment stages of migration, this service is for you. After the assessment, you can use services such as Azure Site Recovery and Azure Database Migration, to migrate the machines to Azure.

> [!NOTE]
> Azure Migrate is currently in preview.

## Why use Azure Migrate?

Azure Migrate helps you to:

- **Assess Azure readiness**: Assess whether your on-premises machines are suitable for running in Azure. 
- **Get size recommendations**: Recommended sizing for Azure VMs after migration, based on the performance history of on-premises VMs. 
- **Estimate monthly costs**: Estimated costs for running on-premises machines in Azure.
- **Migrate with high confidence**: As you group on-premises machines for assessment, you can increase assessment confidence by visualizing dependencies. You can accurately view dependencies for a specific machine, or for all machines in a group.

## Current limitations

- Currently, you can assess on-premises VMware virtual machines (VMs) for migration to Azure VMs.
- You can assess up to 1000 VMs in a single assessment, and up to 1500 machines in a single Azure Migrate project. If you need to assess more, you can increase the number of projects or assessments. [Learn more](how-to-scale-assessment.md).
- VM you want to assess must be managed by a vCenter server, version 5.5, 6.0, or 6.5.
- The Azure Migrate portal is currently available in English only.
- You can only create an Azure Migrate project in the West Central US region. However, you can assess VMs for a different target Azure location. 
- Azure Migrate currently supports only [Locally Redundant Storage (LRS)](../storage/common/storage-introduction.md#replication) replication.

## What do I need to pay for?

You don't need to pay for assessment. To support [dependency visualization](concepts-dependency-visualization.md), Azure Migrate creates a Log Analytics workspace by default. If you use dependency visualization, or use the workspace outside Azure Migrate), you're charged for workspace usage. [Learn more](https://www.microsoft.com/cloud-platform/operations-management-suite) about Service Map solution pricing. 


## What's in an assessment?

Azure Migrate assessments are based on the settings summarized in the table.

**Setting** | **Details**
--- | ---
**Target location** | The Azure location to which you want to migrate. By default, this is the location in which you create the Azure Migrate project. You can modify this setting.   
**Storage redundancy** | The type of storage that the Azure VMs will use after migration. LRS is the default.
**Pricing plans** | The assessment considers whether you're enrolled in software assurance, and can use the [Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). It also considers Azure offers that should be applied, and allows you to specify subscription-specific discounts (%), that you get on top of the offer. 
**Pricing tier** | You can specify the [pricing tier (basic/standard)](../virtual-machines/windows/sizes-general.md) of Azure VMs. This helps you to migrate to a suitable Azure VM family, based on whether you're in a production environment. By default the [standard](../virtual-machines/windows/sizes-general.md) tier is used.
**Performance history** | By default, Azure Migrate evaluates the performance of on-premises machines using a month of history, with a 95% percentile value. You can modify this setting.
**Comfort factor** | Azure Migrate considers a buffer (comfort factor) during assessment. This buffer is applied on top of machine utilization data for VMs (CPU, memory, disk, and network). The comfort factor accounts for issues such as seasonal usage, short performance history, and likely increases in future usage.<br/><br/> For example, 10-core VM with 20% utilization normally results in a 2-core VM. However, with a comfort factor of 2.0x, the result is a 4-core VM instead. The default comfort setting is 1.3x.


## How does Azure Migrate work?

1.	You create an Azure Migrate project.
2.	Azure Migrate uses an on-premises VM called the collector appliance, to discover information about your on-premises machines. To create the appliance, you download the setup file in Open Virtualization Appliance (.ova) format, and import it as a VM on your on-premises vCenter server.
3.	You connect to the VM using read-only credentials for the vCenter server, and run the collector.
4.	The collector collects VM metadata using VMware PowerCLI cmdlets. Discovery is agentless, and doesn't install anything on VMware hosts or VMs. The collected metadata includes VM information (cores, memory, disks, disk sizes, and network adapters). It also collects performance data for VMs, including CPU and memory usage, disk IOPS, disk throughput (MBps), and network output (MBps).
5.	The metadata is pushed to the Azure Migrate project. You can view it in the Azure portal.
6.	For the purposes of assessment, you gather VMs into groups. For example, you might group VMs that run the same app. You can group VMs using tagging in vCenter, or in the vCenter portal. Use visualization to verify dependencies for a specific machine, or for all machines in a group.
7.	You create an assessment for a group.
8.	After the assessment finishes, you can view it in the portal, or download it in Excel format.



  ![Azure Planner architecture](./media/migration-planner-overview/overview-1.png)

## What are the port requirements?

The table summarizes the ports needed for Azure Migrate communications.

|Component          |To communicate with     |Port required  |Reason   |
|-------------------|------------------------|---------------|---------|
|Collector          |Azure Migrate service   |TCP 443        |The collector connects to the service over SSL port 443|
|Collector          |vCenter Server          |Default 9443   | By default the collector connects to the vCenter server on port 9443. If the servers listens on a different port, it should be configured as an outgoing port on the collector VM. |
|On-premises VM     | OMS Workspace          |[TCP 443](../log-analytics/log-analytics-windows-agents.md#system-requirements-and-required-configuration) |The MMA agent uses TCP 443 to connect to Log Analytics. You only need this port if you're using the dependency visualization feature, and are installing the MMA agent. |


  
## What happens after assessment?

After you've assessed on-premises machines for migration with the Azure Migrate service, you can use a couple of tools to perform the migration:

- **Azure Site Recovery**: You can use Azure Site Recovery to migrate to Azure, as follows:
  - Prepare Azure resources, including an Azure subscription, an Azure virtual network, and a storage account.
  - Prepare your on-premises VMware servers for migration. You verify VMware support requirements for Site Recovery, prepare VMware servers for discovery, and prepare to install the Site Recovery Mobility service on VMs that you want to migrate. 
  - Set up migration. You set up a Recovery Services vault, configure source and target migration settings, set up a replication policy, and enable replication. You can run a disaster recovery drill to check that migration of a VM to Azure is working correctly.
  - Run a failover to migrate on-premises machines to Azure. 
  - [Learn more](../site-recovery/tutorial-migrate-on-premises-to-azure.md) in the Site Recovery migration tutorial.

- **Azure Database Migration**: If your on-premises machines are running a database such as SQL Server, MySQL, or Oracle, you can use the Azure Database Migration Service to migrate them to Azure. [Learn more](https://azure.microsoft.com/campaigns/database-migration/).



## Next steps 
[Follow a tutorial](tutorial-assessment-vmware.md) to create an assessment for an on-premises VMware VM.