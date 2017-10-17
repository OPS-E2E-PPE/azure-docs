---
title: Create a Service Fabric cluster in Azure | Microsoft Docs
description: Learn how to create a Windows or Linux Service Fabric cluster in Azure using PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''

ms.assetid:
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: cristyg
ms.custom: mvc

---

# Create a secure cluster on Azure using PowerShell
This is the first step in a series of tutorials that shows you how to move a .NET application to the cloud using Service Fabric clusters as well as containers. In the following steps you will learn how to create a Service Fabric cluster (Windows or Linux) running in Azure. When you're finished, you will have a secure cluster running in the cloud to which you can deploy applications.

## Prerequisites
Before you begin this tutorial:
- If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Install the [Service Fabric SDK](service-fabric-get-started.md)
- Install the [Azure Powershell module version 4.1 or higher](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (if needed,  [Install Azure PowerShell](/powershell/azure/overview) or [update to a newer version](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps))


# Create a Service Fabric cluster

This script creates a single-node preview Service Fabric cluster. The cluster is secured by a self-signed certificate that gets created along with the cluster and then placed in a key vault. Single-node clusters cannot be scaled beyond one virtual machine and preview clusters cannot be upgraded to newer versions.

To calculate cost incurred by running a Service Fabric cluster in Azure, use the [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/).
For more information on creating Service Fabric clusters, see [Create a Service Fabric cluster by using Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## Log in to Azure
Open a PowerShell console, log in to Azure, and select the subscription you want to deploy the cluster in:

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## Cluster parameters

   This script uses the following parameters and concepts. Customize the parameters to fit your requirements.

   | Parameter       | Description | Suggested Value |
   | --------------- | ----------- | --------------- |
   | Location | The Azure region to which to deploy the cluster. | *for example, westeurope, eastasia, eastus* |
   | Name     | Name of the cluster you want to create. The name must be between 4-23 characters and can only have lowercase letters, numbers, and hyphens. | *for example, bobs-sfpreviewcluster* |
   | ResourceGroupName   | Name of the resource group in which to create the cluster. | *for example, myresourcegroup* |
   | VmSku  | Virtual Machine SKU to use for the nodes. | *Any valid Virtual Machine SKU* |
   | OS  | Virtual Machine OS to use for the nodes. | *Any valid Virtual Machine OS* |
   | KeyVaultName | Name of the new KeyVault to associate with the cluster. | *for example, mykeyvault* |
   | ClusterSize | The number of virtual machines in your cluster (can be 1 or 3-99).| *Specify only one virtual machine for a preview cluster* |
   | CertificateSubjectName | The subject name of the certificate to be created. | *Follows the format <name>.<location>.cloudapp.azure.com* |

## Default parameter values
**Virtual Machine** settings are optional. If you don't specify them, the admin username defaults to "vmadmin" and PowerShell will prompt you for a Virtual Machine password before the cluster is created.

**Ports** will default to ports 80 and 8081. You can specify additional ports by following the guidance for [ports in Service Fabric clusters](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule).

**Diagnostics** are enabled by default.

**DNS service** by default is not enabled.

**Reverse proxy** is also not enabled by default.

## Create the cluster with your parameters

Once you have decided on the parameters that fit your requirements, execute the following command to generate a secure Service Fabric Cluster and its certificate.

You can modify this script to include additional parameters. For more information on parameters for cluster creation, see the  [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md) cmdlet.

>[!NOTE]
>Before executing this command you must create a folder in which to output the cluster certificate.

```powershell

    # Certificate variables. This will create and encrypt a password to be used by Service Fabric.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

    # You must create the folder where you want to output the certificate on your machine before executing this step.
    $certfolder="c:\mycertificates\"

    # Variables for common values. Change the values to fit your needs.
    $clusterloc="WestUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=1

    # Create the Service Fabric cluster and its self-signed certificate. The OS specified here allows you use this cluster with any applications that are also leveraging containers.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

The creation process can take several minutes. Once the configuration finishes, it outputs information about the cluster created in Azure. It also copies the cluster certificate to the -CertificateOutputFolder directory on the path you specified for this parameter.

Take note of the **ManagementEndpoint** URL for your cluster, which may be similar to the following URL: *https://mycluster.westeurope.cloudapp.azure.com:19080*

## Import the certificate

When the cluster is successfully created, execute the following command to ensure that you can use the self-signed certificate,

```powershell

    # Connect to the cluster using by installing the certificate into the Personal (My) store of the current user on your computer.
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
            -Password $certpwd
```

This command installs the certificate on the current user of your machine.  You need this certificate to access Service Fabric Explorer and view the health of your cluster.


## View your cluster (Optional)

Once you have both the cluster and the imported certificate, you can connect to the cluster and view its health. There's multiple ways to connect, via either Service Fabric Explorer or PowerShell.

### Service Fabric Explorer
You can view the health of your cluster by opening Service Fabric Explorer. To do so, navigate to the **ManagementEndpoint** URL for your cluster using a web browser, then select the certificate saved on your machine.

>[!NOTE]
>When opening Service Fabric Explorer, you see a certificate error, as you are using a self-signed certificate. In Edge, you have to click *Details* and then the *Go on to the webpage* link. In Chrome, you have to click *Advanced* and then the *proceed* link.

### PowerShell

The **Service Fabric** PowerShell module provides many cmdlets for managing Service Fabric clusters, applications, and services.  Use the [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet to connect to the secure cluster. The certificate thumbprint and connection endpoint details are found in the output from a previous step.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

You can also check that you are connected and the cluster is healthy using the [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet.

```powershell
Get-ServiceFabricClusterHealth
```

## Next steps
In this tutorial, you learned how to create a secure Service Fabric cluster in Azure using PowerShell

Next, advance to the following tutorial to learn how to deploy an existing application.
> [!div class="nextstepaction"]
> [Deploy an existing .NET application with Docker Compose](service-fabric-host-app-in-a-container.md)

---
