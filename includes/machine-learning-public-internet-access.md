---
title: Include file
description: Include file
author: blackmist
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 01/10/2023
ms.author: larryfr
ms.custom: include file
---

Azure Machine Learning requires both inbound and outbound access to the public internet. The following tables provide an overview of what access is required and what purpose it serves. For service tags that end in `.region`, replace `region` with the Azure region that contains your workspace. For example, `Storage.westus`:

> [!TIP]
> The required tab lists the required inbound and outbound configuration. The situational tab lists optional inbound and outbound configurations required by specific configurations you may want to enable.

# [Required](#tab/required)

| Direction | Protocol &<br>ports | Service tag | Purpose |
| ----- |-----| ----- | ----- |
| Outbound | TCP: 80, 443 | `AzureActiveDirectory` | Authentication using Azure AD. |
| Outbound | TCP: 443, 18881<br>UDP: 5831 | `AzureMachineLearning` | Using Azure Machine Learning services.<br>Port 18881 is used for Python intellisense in notebooks.<br>Port 5831 is used to create, update, and delete Azure Machine Learning compute instance. |
| Outbound | ANY: 443 | `BatchNodeManagement.region` | Communication with Azure Batch back-end for Azure Machine Learning compute instances/clusters. |
| Outbound | TCP: 443 | `AzureResourceManager` | Creation of Azure resources with Azure Machine Learning, Azure CLI, and Azure Machine Learning SDK. |
| Outbound | TCP: 443 | `Storage.region` | Access data stored in the Azure Storage Account for compute cluster and compute instance. This outbound can be used to exfiltrate data. For more information, see [Data exfiltration protection](../articles/machine-learning/how-to-prevent-data-loss-exfiltration.md). |
| Outbound | TCP: 443 | `AzureFrontDoor.FrontEnd`</br>* Not needed in Azure China. | Global entry point for [Azure Machine Learning studio](https://ml.azure.com). Store images and environments for AutoML. This outbound can be used to exfiltrate data. For more information, see [Data exfiltration protection](../articles/machine-learning/how-to-prevent-data-loss-exfiltration.md).|
| Outbound | TCP: 443 | `MicrosoftContainerRegistry.region`</br>**Note** that this  tag has a dependency on the `AzureFrontDoor.FirstParty` tag | Access docker images provided by Microsoft. Setup of the Azure Machine Learning router for Azure Kubernetes Service. |

# [Situational](#tab/situational)

| Direction | Protocol & <br>ports | Service tag | Purpose |
| ----- |-----| ----- | ----- |
| Inbound | TCP: 44224 | `AzureMachineLearning` | Create, update, and delete of Azure Machine Learning compute instance/cluster. **Required if instance/cluster configured with a Public IP option.**|
| Outbound | TCP: 8787 | `AzureMachineLearning` | Using Azure Machine Learning services.<br> **Port 8787 is required if you use RStudio.** |
| Outbound | TCP: 445 | `Storage.region` | Access data stored in the Azure Storage Account for compute cluster and compute instance. This outbound can be used to exfiltrate data. For more information, see [Data exfiltration protection](../articles/machine-learning/how-to-prevent-data-loss-exfiltration.md).<br>**445 is only required if you have a firewall between your virtual network for Azure ML and a private endpoint for your storage accounts.**|
| Outbound | TCP: 443 | `AzureMonitor` | Used to log monitoring and metrics to App Insights and Azure Monitor. |
| Outbound | TCP: 443 | `Keyvault.region` | Access the key vault for the Azure Batch service. Only needed if your workspace was created with the [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) flag enabled. |

-----

> [!TIP]
> If you need the IP addresses instead of service tags, use one of the following options:
> * Download a list from [Azure IP Ranges and Service Tags](https://www.microsoft.com/download/details.aspx?id=56519).
> * Use the Azure CLI [az network list-service-tags](/cli/azure/network#az-network-list-service-tags) command.
> * Use the Azure PowerShell [Get-AzNetworkServiceTag](/powershell/module/az.network/get-aznetworkservicetag) command.
> 
> The IP addresses may change periodically.

> [!IMPORTANT]
> When using a compute cluster that is configured for __no public IP address__, you must allow the following traffic:
> * **Inbound** from source of **VirtualNetwork** and any port source, to destination of **VirtualNetwork**, and destination port of **29876, 29877**.
> * **Inbound** from source **AzureLoadBalancer** and any port source to destination **VirtualNetwork** and port **44224** destination.

You may also need to allow __outbound__ traffic to Visual Studio Code and non-Microsoft sites for the installation of packages required by your machine learning project. The following table lists commonly used repositories for machine learning:

| Host name | Purpose |
| ----- | ----- |
| **anaconda.com**</br>**\*.anaconda.com** | Used to install default packages. |
| **\*.anaconda.org** | Used to get repo data. |
| **pypi.org** | Used to list dependencies from the default index, if any, and the index isn't overwritten by user settings. If the index is overwritten, you must also allow **\*.pythonhosted.org**. |
| **cloud.r-project.org** | Used when installing CRAN packages for R development. |
| **\*pytorch.org** | Used by some examples based on PyTorch. |
| **\*.tensorflow.org** | Used by some examples based on Tensorflow. |
| **code.visualstudio.com** | Required to download and install VS Code desktop. This is not required for VS Code Web. |
| **update.code.visualstudio.com**</br>**\*.vo.msecnd.net** | Used to retrieve VS Code server bits that are installed on the compute instance through a setup script. |
| **marketplace.visualstudio.com**</br>**vscode.blob.core.windows.net**</br>**\*.gallerycdn.vsassets.io** | Required to download and install VS Code extensions. These enable the remote connection to Compute Instances provided by the Azure ML extension for VS Code, see [Connect to an Azure Machine Learning compute instance in Visual Studio Code](../articles/machine-learning/how-to-set-up-vs-code-remote.md) for more information. |
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | Used to retrieve websocket server bits, which are installed on the compute instance. The websocket server is used to transmit requests from Visual Studio Code client (desktop application) to Visual Studio Code server running on the compute instance.|

When using Azure Kubernetes Service (AKS) with Azure Machine Learning, allow the following traffic to the AKS VNet:

* General inbound/outbound requirements for AKS as described in the [Restrict egress traffic in Azure Kubernetes Service](../articles/aks/limit-egress-traffic.md) article.
* __Outbound__ to mcr.microsoft.com.
* When deploying a model to an AKS cluster, use the guidance in the [Deploy ML models to Azure Kubernetes Service](../articles/machine-learning/v1/how-to-deploy-azure-kubernetes-service.md#connectivity) article.