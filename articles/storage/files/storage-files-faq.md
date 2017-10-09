---
title: Frequently asked questions about Azure Files | Microsoft Docs
description: Find answers to frequently asked questions about Azure Files.
services: storage
documentationcenter: ''
author: RenaShahMSFT
manager: aungoo
editor: tysonn

ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/19/2017
ms.author: renash
---
# Frequently Asked Questions about Azure Files

## General
* **Q. What is Azure Files?**  
   
    Azure Files is a distributed file system in Azure. It provides an SMB protocol interface which allows users to mount the storage as a native share on supported Azure Virtual Machine or on-premises machine.

* **Q. Why is Azure Files useful?**  
   
    Azure Files provides shared data access across multiple VMs and platforms. Refer to [Why Azure Files is useful](storage-files-introduction.md#why-azure-files-is-useful).

* **Q. When should I use Azure File vs Azure Blob vs Azure Disk ?**  
   
    Microsoft Azure provides several ways to store and access data in the cloud.  
   
    Azure Files - Provides an SMB interface, client libraries, and a REST interface that allows easy access from anywhere to stored files.  
   
    Azure Blobs - Provides client libraries and a REST interface that allows unstructured data to be stored and accessed at a massive scale in block blobs.  
   
    Azure Data Disks - Provides client libraries and a REST interface that allows data to be persistently stored and accessed from an attached virtual hard disk.  
   
    Learn more on [Deciding when to use Azure Blobs, Azure Files, or Azure Data Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

* **Q. How do I get started using Azure Files?**  
   
    You can start by creating an Azure file share. You can create Azure File shares using Azure Portal, the Azure Storage PowerShell cmdlets, the Azure Storage client libraries, or the Azure Storage REST API.In this tutorial, you will learn:

    * [Learn how to create Azure File share using the Portal](storage-how-to-use-files-portal.md)
    * [Learn how to create Azure File share using Powershell](storage-how-to-create-file-share.md#create-file-share-through-powershell)
    * [Learn how to create Azure File share using CLI](storage-how-to-create-file-share.md#create-file-share-through-command-line-interface-cli)

* **Q. What replications are supported by Azure Files?**  
   
    Azure Files only supports LRS or GRS right now. We plan to support RA-GRS but there is no timeline to share yet.
    
## Scale Targets/Limits

* **Q. How many clients can access the same file simultaneously?**

    There is a 2000 open handles quota on a single file. Once you have 2000 open handles, you will get an error that quota is reached.

## Security, Authentication and Access Control

* **Q. What are different ways to access files in Azure Files?**
    
    You can mount the file share on your local machine using SMB 3.0 protocol or use tools like [Storage Explorer](http://storageexplorer.com/) to access files in your file share. From your application, you can use storage client libraries, REST APIs or Powershell to access your files in Azure File share.

* **Q. How can I provide access to a specific file using a web browser?**
    
    Using SAS, you can generate tokens with specific permissions that are valid for a specified time interval. For example, you can generate a token with read-only access to a particular file for a specific period of time. Anyone who possesses this url can access the file directly from any web browser while it is valid. SAS keys can be easily generated from UI like Storage Explorer.

* **Q. Is it possible to specify read-only or write-only permissions on folders within the share?**
    
    You don't have this level of control over permissions if you mount the file share via SMB. However, you can achieve this by creating a shared access signature (SAS) via the REST API or client libraries.  

* **Q. How can I enable Server Side encryption for Azure Files?**

    [Server Side Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) for Azure Files is generally available in all regions and public and national clouds. You can enable SSE for Azure Files using [Azure portal](https://portal.azure.com/),[Microsoft Azure Storage Resource Provider API](/rest/api/storagerp/storageaccounts), [Azure Powershell](https://msdn.microsoft.com/library/azure/mt607151.aspx) or [Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
    
    After enabling SSE on Azure Files, any new data written to the file storage in that storage account will be automatically encrypted. This feature is available for all new data written to existing or new shares in an existing or new storage account. There is no additional charge for enabling this feature. Learn more on [how to enable SSE on Azure Files](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Q. Is Active Directory-based authentication supported by Azure Files?**
   
    We currently do not support AD-based authentication or ACLs, but do have it in our list of feature requests. For now, the Azure Storage account keys are used to provide authentication to the file share. We do offer a workaround using shared access signatures (SAS) via the REST API or the client libraries. Using SAS, you can generate tokens with specific permissions that are valid for a specified time interval. For example, you can generate a token with read-only access to a given file with 10 minutes expiry. Anyone who possesses this token while it is valid has read-only access to that file for those 10 minutes.
   
    SAS is only supported via the REST API or client libraries. When you mount the file share via the SMB protocol, you can't use a SAS to delegate access to its contents. 
    
* **Q. What are the data compliance policies supported for Azure Files?**

   Azure Files runs on top of the same storage architecture as other storage services in Azure Storage and applies the same data compliance policies. More information on Azure Storage data compliance, you can download and refer to [Microsoft Azure Data Protection document](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409).

## On-Premises Access

* **Q.Do I have to use Azure ExpressRoute to connect to Azure Files from an on-premises VM?**
   
    No. If you don't have ExpressRoute, you can still access the file share from on-premises as long as you have port 445 (TCP Outbound) open for Internet access. However, you can use ExpressRoute with Azure Files if you like.

* **Q. How can I mount an Azure File share on my local machine?** 
    
    You can mount the file share via the SMB protocol as long as port 445 (TCP Outbound) is open and your client supports the SMB 3.0 protocol (for example, you're using Windows 10 or Windows Server 2012). Please work with your local ISP provider to unblock the port. In the interim, you can view your files using [Storage Explorer](../../vs-azure-tools-storage-explorer-files.md#view-a-file-shares-contents).


## Share snapshots

### Share snapshots - General

* **Q. What is file share snapshot?**
    
     Azure file share snapshots allows you to create a read-only versions of your file shares. It also allows you to copy an older version of your content back to the same share or an alternate location in Azure or on-premises for further modifications. To learn more about share snapshot, see at [share snapshot overview](storage-snapshots-files.md).

* **Q. Where are my share snapshots stored?**
    
     Share snapshots are stored in the same storage account as the file share.

* **Q. Are there any performance implications?**
    
     Share snapshots do not have any performance overhead.


* **Q. Are share snapshots application consistent?**
    
     No. Share snapshots are not application consistent. User will have to flush the writes from the application to the share before taking share snapshot.

* **Q. Are there any limits on the number of share snapshots?**
    
     There is a limit of 200 share snapshots that Azure Files can retain. Share snapshots do not count towards the share quota so there is no per share limit on the total space utilized by all the share snapshots.Storage account limits still apply. After 200 share snapshots, older snapshots will have to be deleted in order to create new share snapshots.

### Create share snapshots

* **Q. Can I create share snapshot of individual files?**
    
     Share snapshots are created at file share level. You can restore individual files from the file share snapshot but you cannot create file-level share snapshots. However, if you have taken a share level share snapshot and you want to list share snapshots where a particular file has changed, you can do so from "Previous Versions" experience on a Windows mounted share.

* **Q. Can I create share snapshots of Encrypted fileshare?**
    
     You can take a share snapshot of Azure File shares which has Encryption at rest enabled. You can restore files from a share snapshot to an encrypted file share. If your share is encrypted, your share snapshot will also be encrypted.

* **Q.Can I create geo-redundant share snapshots?**
    
     Redundancy tier of share snapshot will be same as the base Azure File Share. If you have selected GRS for your account your share snapshot will also be stored redundantly in a different geo.

### Manage share snapshots

* **Q. Can I browse my share snapshots from Linux?**
    
     You can use Azure CLI 2.0 to create, list, browse and restore on Linux.

* **Q. Can I copy the share snapshots to a different storage account?**
    
    One can copy files from share snapshots to another location but not the share snapshots themselves.

### Restore data from share snapshots

* **Q. Can I promote a share snapshot to the base share?**
    
    You can only copy data from a share snapshot to any desired destination. However you cannot promote a share snapshot to the base share.

* **Q. Can I restore data from my share snapshot to a different storage account?**
    
    Yes. Files from a share snapshot can be copied to original or an alternate location which includes same/different storage account in same or different regions. You can also copy files to on-premises or any other cloud.    
  
### Cleanup share snapshot

* **Q. Can I delete my share but not share snapshots?**
    
    You will not be able to delete your share if you have active share snapshots on your share. There is an API available for deleting share snapshots along with share and you can achieve the same from Azure portal as well.

* **Q. What happens to my share snapshots if I delete my Storage Account?**
    
    If you delete your storage account, the share snapshots will be deleted as well.

## Billing and Pricing

* **Q. Does the network traffic between an Azure VM and a file share count as external bandwidth that is charged to the subscription?**
   
    If the file share and VM are in the same Azure region, the traffic between them is free. If they are in different regions, the traffic between them will be charged as external bandwidth.

* **Q. How much does share snapshots cost?**
    
     During Public Preview share snapshot capacity won't be charged. Standard Storage egress and transaction costs sill apply. After GA, both capacity and transactions on share snapshot will be charged.
     
     Sshare snapshots are incremental in nature. The base share snapshot is the share itself. All the subsequent share snapshots are incremental and will only store the diff from the previous share snapshot. You are billed only for the changed content. If you have a share with 100 GB of data but only 5 GB has changed after your last share snapshot, the share snapshot will consumes only 5 additional GB and you will be billed only 105 GB. See [Pricing page](https://azure.microsoft.com/en-us/pricing/details/storage/files/) for more information on transaction and standard egress charges.

## Backup

* **Q. How do I backup my Azure File Share?**
    
    You can use periodic share snapshots for protection against accidental deletes. You can also use AzCopy, RoboCopy, or a 3rd party backup tool that can backup a mounted file share.

## Performance

* **Q. What are the scale limits of Azure Files?**
    For information on scalability and performance targets of Azure Files, see [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#scalability-targets-for-blobs-queues-tables-and-files).

* **Q. My performance was slow when trying to unzip files into Azure Files. What should I do?**
    
    To transfer large numbers of files into Azure Files, we recommend that you use AzCopy(Windows, Preview for Linux/Unix) or Azure Powershell as these tools have been optimized for network transfer.

* **Q. What patches has been released to fix slow-performance issue with Azure Files?**
    
    The Windows team recently released a patch to fix a slow performance issue when the customer accesses Azure Files from Windows 8.1 or Windows Server 2012 R2. For more information, please check out the associated KB article, [Slow performance when you access Azure Files from Windows 8.1 or Server 2012 R2](https://support.microsoft.com/kb/3114025).

## Features and Interoperability with other services
* **Q. Is a "File Share Witness" for a failover cluster one of the use cases for Azure Files?**
   
    This is not currently supported.

* **Q. Is there a rename operation in the REST API?**
   
    Not at this time.

* **Q. Can you have nested shares, in other words, a share under a share?**
    
    No. The file share is the virtual driver that you can mount, so nested shares are not supported.

* **Q. Using Azure Files with IBM MQ**
    
    IBM has released a document to guide IBM MQ customers when configuring Azure Files with their service. For more information, please check out [How to setup IBM MQ Multi instance queue manager with Microsoft Azure File Service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).


## Troubleshooting
* **Q. How do I troubleshoot Azure Files errors?**
    
    You can refer to [Azure Files Troubleshooting Article](storage-troubleshoot-windows-file-connection-problems.md) for end-to-end troubleshooting guidance. 


## See also
See these links for more information about Azure Files.

### Conceptual articles and videos
* [Azure Files: a frictionless cloud SMB file system for Windows and Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [How to use Azure Files with Linux](storage-how-to-use-files-linux.md)

### Tooling support for File storage
* [How to use AzCopy with Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Using the Azure CLI with Azure Storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Troubleshooting Azure Files problems](storage-troubleshoot-linux-file-connection-problems.md)

### Blog posts
* [Azure Files is now generally available](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure Files](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrating data to Azure Files](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### Reference
* [Storage Client Library for .NET reference](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File Service REST API reference](http://msdn.microsoft.com/library/azure/dn167006.aspx)
