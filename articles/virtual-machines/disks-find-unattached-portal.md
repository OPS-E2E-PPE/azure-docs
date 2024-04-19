---
title: Identify unattached Azure disks - Azure portal
description: How to find unattached Azure managed and unmanaged (VHDs/page blobs) disks by using the Azure portal.
author: roygara
ms.service: azure-disk-storage
ms.topic: how-to
ms.date: 04/18/2024
ms.author: rogarana
---

# Find and delete unattached Azure managed and unmanaged disks - Azure portal

**Applies to:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Flexible scale sets :heavy_check_mark: Uniform scale sets.

When you delete a virtual machine (VM) in Azure, by default, any disks that are attached to the VM aren't deleted. This helps prevent data loss from unintentional deletion of VMs. After a VM is deleted, you'll continue to pay for unattached disks. This article shows you how to find and delete any unattached disks using the Azure portal, and reduce unnecessary costs. Deletions are permanent, you won't be able to recover data once you delete a disk.

## Managed disks: Find and delete unattached disks

If you have unattached managed disks and no longer need the data on them, the following process explains how to find them from the Azure portal:

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Search for and select **Disks**.

    On the **Disks** page, you're presented with a list of all your disks.

1. Select the disk you'd like to delete, this brings you to the individual disk's page.

    > [!NOTE]
    > The disk's page also displays the **LastOwnershipUpdateTime** property. This property represents when the disk’s state was last updated. For an unattached disk, this shows the time when the disk was unattached. This property is blank for newly created disks, until their state changes.

1. On the individual disk's page, confirm the disk state is unattached, then select **Delete**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="Screenshot of an individual managed disks blade. This page shows unattached in the disk state if it is unattached. You can delete this disk if you don't need to preserve its data any longer.":::

## Unmanaged disks: Find and delete unattached disks

Unmanaged disks are VHD files that are stored as [page blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) in [Azure storage accounts](../storage/common/storage-account-overview.md).

If you have unmanaged disks that aren't attached to a VM, no longer need the data on them, and would like to delete them, the following process explains how to do so from the Azure portal:

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Search for and select **Disks (Classic)**.

    You're presented with a list of all your unmanaged disks. Any disk that has "**-**" in the **Attached to** column is an unattached disk.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="Screenshot of the unmanaged disks blade. Disks in this blade that have - in the attached to column are unattached.":::

1. Select the unattached disk you'd like to delete, this brings up the individual disk's blade.

1. On that individual disk's blade, you can confirm it's unattached, since **Attached to** will still be **-**.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="Screenshot of an individual unmanaged disk blade. It has - as the attached to value if it's unattached. If you no longer need this disks data, you can delete it.":::

1. Select **Delete**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="Screenshot of an individual unmanaged disk blade, highlighting delete.":::

## Next steps

If you'd like an automated way of finding and deleting unattached storage accounts, see our [CLI](linux/find-unattached-disks.md) or [PowerShell](windows/find-unattached-disks.md) articles.

For more information, see [Delete a storage account](../storage/common/storage-account-create.md#delete-a-storage-account) and [Identify Orphaned Disks Using PowerShell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell).
