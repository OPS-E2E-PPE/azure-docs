---
title: Enable Azure Automation Change Tracking and Inventory from Automation account
description: This article tells how to enable Change Tracking and Inventory from an Automation account.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
---
# Enable Change Tracking and Inventory from an Automation account

This article describes how you can use your Automation account to enable the [Change Tracking and Inventory](change-tracking.md) feature for VMs in your environment. To enable Azure VMs at scale, you must enable an existing VM using Change Tracking and Inventory. 

> [!NOTE]
> When enabling Change Tracking and Inventory, only certain regions are supported for linking a Log Analytics workspace and an Automation account. For a list of the supported mapping pairs, see [Region mapping for Automation account and Log Analytics workspace](how-to/region-mappings.md).

## Prerequisites

* Azure subscription. If you don't have one yet, you can [activate your MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) or sign up for a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation account](automation-offering-get-started.md) to manage machines.
* A [virtual machine](../virtual-machines/windows/quick-create-portal.md).

## Sign in to Azure

Sign in to Azure at https://portal.azure.com.

## Enable Change Tracking and Inventory

1. Navigate to your Automation account and select either **Inventory** or **Change tracking** under **Configuration Management**.

2. Choose the Log Analytics workspace and Automation account and click **Enable** to enable Change Tracking and Inventory. The setup takes up to 15 minutes to complete.

    ![Enable Change Tracking and Inventory](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="scope-configuration"></a>Check the scope configuration

Change Tracking and Inventory uses a scope configuration within the workspace to target the computers to receive changes. The scope configuration is a group of one or more saved searches that is used to limit the scope of the feature to specific computers. For more information, see [Work with scope configurations for Change Tracking and Inventory](automation-scope-configurations-change-tracking.md).

## Enable Azure VMs

1. From your Automation account, select **Inventory** or **Change tracking** under **Configuration Management**.

2. Click **+ Add Azure VMs** and select one or more VMs from the list. Virtual machines that can't be enabled are grayed out and unable to be selected. Azure VMs can exist in any region no matter the location of your Automation account. 

3. Click **Enable** to add the selected VMs to the computer group saved search for the feature. For more information, see [Work with scope configurations for Change Tracking and Inventory](automation-scope-configurations-change-tracking.md).

    ![Enable Azure VMs](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## Enable non-Azure VMs

Machines not in Azure need to be added manually. 

1. From your Automation account select **Inventory** or **Change tracking** under **Configuration Management**.

2. Click **Add non-Azure machine**. This action opens up a new browser window with [instructions to install and configure the Log Analytics agent for Windows](../azure-monitor/platform/log-analytics-agent.md) so that the machine can begin reporting Change Tracking and Inventory operations. If you're enabling a machine that's currently managed by Operations Manager, a new agent isn't required and the workspace information is entered into the existing agent.

## Enable machines in the workspace

Manually installed machines or machines already reporting to your workspace must to be added to Azure Automation for Change Tracking and Inventory to be enabled. 

1. From your Automation account, select **Inventory** or **Change tracking** under **Configuration Management**.

2. Select **Manage machines**. The **Manage machines** button might be grayed out if you previously chose the option **Enable on all available and future machines**

    ![Saved searches](media/automation-enable-changes-from-auto-acct/managemachines.png)

4. To enable Change Tracking and Inventory for all available machines, select **Enable on all available machines** on the Manage Machines page. This action disables the control to add machines individually. This task adds all the names of the machines reporting to the workspace to the computer group saved search query. When selected, this action disables the **Manage Machines** button.

5. To enable the feature for all available machines and future machines, select **Enable on all available and future machines**. This option deletes the saved searches and scope configurations from the workspace and opens the feature for all Azure and non-Azure machines that are reporting to the workspace. When selected, this action disables the **Manage Machines** button permanently, as there's no scope configuration left.

6. If necessary, you can add the scope configurations back by re-adding the initial saved searches. For more information, see [Work with scope configurations for Change Tracking and Inventory](automation-scope-configurations-change-tracking.md).

7. To enable the feature for one or more machines, select **Enable on selected machines** and click **Add** next to each machine to enable for the feature. This task adds the selected machine names to the computer group saved search query for the feature.

## Next steps

* [Manage Change Tracking and Inventory](change-tracking-file-contents.md)
* [Work with scope configurations for Change Tracking and Inventory](automation-scope-configurations-change-tracking.md)
* [Identify software on a VM](automation-tutorial-installed-software.md)
* [Unlink workspace from Automation account for Change Tracking and Inventory](automation-unlink-workspace-change-tracking.md)
* [Remove VMs from Change Tracking and Inventory](automation-remove-vms-from-change-tracking.md)
* [Troubleshoot changes on an Azure VM](automation-tutorial-troubleshoot-changes.md)
* [Troubleshoot Change Tracking and Inventory issues](troubleshoot/change-tracking.md)