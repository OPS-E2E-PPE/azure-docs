---
title: Plan an Azure Arc-enabled data services deployment
description: This article explains the considerations for planning an Azure Arc-enabled data services deployment.
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
---
# Plan an Azure Arc-enabled data services deployment

This article describes how to plan to deploy Azure Arc-enabled data services.

As you begin planning to deploy Azure Arc-enabled data services, it's important to properly understand your database workloads and your business requirements for those workloads. For example, you need to consider availability, business continuity, and capacity requirements for memory, CPU, and storage for the workloads. And you need to carefully prepare the infrastructure to support the database workloads, based on your business requirements.

## Prerequisites

Before you start, be sure that you've met certain prerequisites and have the necessary background and information ready. To ensure a successful deployment, your infrastructure environment must be properly configured with the right level of access and the appropriate capacity for storage, CPU, and memory. 

Review the following articles:

- [Sizing guidance](sizing-guidance.md)
- [Storage configuration](storage-configuration.md)
- [Connectivity modes and their requirements](connectivity.md)

Verify that:

- The [arcdata CLI extension](install-arcdata-extension.md) is installed.
- The other [client tools](install-client-tools.md) are installed.
- You have access to the Kubernetes cluster.
- Your *kubeconfig* file is configured. It should point to the Kubernetes cluster that you want to deploy to. To verify the current context of the cluster, run the following command:
   ```console
   kubectl cluster-info
   ```  
- You have an Azure subscription that resources such as an Azure Arc data controller, Azure Arc-enabled SQL managed instance, or Azure Arc-enabled PostgreSQL Hyperscale server will be projected and billed to.

After you're prepared the infrastructure, deploy Azure Arc-enabled data services in the following way:
1. Create an Azure Arc-enabled data controller on one of the validated distributions of a Kubernetes cluster.
1. Create an Azure Arc-enabled SQL managed instance and/or an Azure Arc-enabled PostgreSQL Hyperscale server group.

> [!CAUTION]
> Some of the data services tiers and modes are in [general availability (GA)](release-notes.md), and some are in preview. We recommend that you don't mix GA and preview services on the same data controller. If you mix GA and preview services on the same data controller, you can't upgrade in place. In that scenario, when you want to upgrade, you must remove and re-create the data controller and data services.

## Overview: Create an Azure Arc-enabled data controller

You can create Azure Arc-enabled data services on various types of Kubernetes clusters, and you can create managed Kubernetes services by using a variety of approaches.

Currently, the validated list of Kubernetes services and distributions includes:

- Amazon Elastic Kubernetes Service (Amazon EKS)
- Azure Kubernetes Service (AKS)
- Azure Kubernetes Service on Azure Stack HCI
- Azure Red Hat OpenShift
- Google Kubernetes Engine (GKE)
- Open source, upstream Kubernetes (typically deployed by using kubeadm)
- OpenShift Container Platform (OCP)

> [!IMPORTANT]
> * The minimum supported version of Kubernetes is v1.19. For more information, see the "Known issues" section of [Release notes&nbsp;- Azure Arc-enabled data services](./release-notes.md#known-issues).
> * The minimum supported version of OCP is 4.7.
> * If you're using Azure Kubernetes Service, your cluster's worker node virtual machine (VM) size should be at least Standard_D8s_v3 and use Premium Disks. 
> * The cluster should not span multiple availability zones. 
> * For more information, see the "Known issues" section of [Release notes&nbsp;- Azure Arc-enabled data services](./release-notes.md#known-issues).

When you're creating Azure Arc-enabled data services, regardless of the service or distribution option you choose, you'll need to provide the following information:

- **Data controller name**: A descriptive name for your data controller (for example, *production-dc* or *seattle-dc*). The name must meet [Kubernetes naming standards](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).

- **Username**: The username for the Kibana/Grafana administrator user.
- **Password**: The password for the Kibana/Grafana administrator user.
- **Name of your Kubernetes namespace**: The name of the Kubernetes namespace where you want to create the data controller.
- **Connectivity mode**: Determines the degree of connectivity from your Azure Arc-enabled data services environment to Azure. *Indirect* connectivity mode is generally available, and *direct* connectivity mode is in preview. Your choice of connectivity mode determines the options for deployment methods. For more information, see [Connectivity modes and requirements](./connectivity.md).
- **Azure subscription ID**: The Azure subscription GUID for where you want to create the data controller resource in Azure. All Azure Arc-enabled SQL managed instances and Azure Database for PostgreSQL Hyperscale server groups are also created in and billed to this subscription.
- **Azure resource group name**: The name of the resource group where you want to create the data controller resource in Azure. All Azure Arc-enabled SQL managed instances and Azure Database for PostgreSQL Hyperscale server groups are also created in this resource group.
- **Azure location**: The Azure location where the data controller resource metadata will be stored in Azure. For a list of available regions, see the [Products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) page for Azure global infrastructure. The metadata and billing information about the Azure resources that are managed by your deployed data controller is stored only in the location in Azure that you specify as the location parameter. If you're deploying in direct connectivity mode, the location parameter for the data controller is the same as the location of your targeted custom location resource.
- **Service principal information**: 
   - If you're deploying in direct connectivity mode during the creation of the Azure Arc data controller, you need the service principal information. For more information, see the "Assign roles to the service principal" section of [Upload usage data, metrics, and logs to Azure](upload-metrics-and-logs-to-azure-monitor.md). 
   - In indirect connectivity mode, you still need the service principal to export and upload manually, but only after you've created the Azure Arc data controller.
- **Infrastructure**: For billing purposes, you must indicate the infrastructure on which you're running Azure Arc-enabled data services. The options are *alibaba*, *aws*, *azure*, *gcp*, *onpremises*, or *other*.

## Additional concepts for direct connectivity mode

As we've outlined in [Connectivity modes and requirements](./connectivity.md), you can deploy the Azure Arc data controller in direct or indirect connectivity mode. Deploying Azure Arc data services in direct connectivity mode requires an understanding of a couple of additional concepts and considerations:

* First, the Kubernetes cluster where the Azure Arc-enabled data services will be deployed needs to be an [Azure Arc-enabled Kubernetes cluster](../kubernetes/overview.md). Deploying the Kubernetes cluster to Azure Arc provides Azure connectivity with capabilities such as automatic upload of usage information, logs, and metrics. By connecting your Kubernetes cluster to Azure, you can also deploy and manage Azure Arc data services to your cluster directly from the Azure portal. To learn how, see [Connect your cluster to Azure](../kubernetes/quickstart-connect-cluster.md).

* After the Kubernetes cluster is deployed to Azure Arc, deploy Azure Arc-enabled data services on an Azure Arc-enabled Kubernetes cluster by doing the following:
   1. Create the Arc data services extension. To learn how, see [Cluster extensions on Azure Arc-enabled Kubernetes](../kubernetes/conceptual-extensions.md).
   1. Create a custom location. To learn how, see [Custom locations on top of Azure Arc-enabled Kubernetes](../kubernetes/conceptual-custom-locations.md).
   1. Create the Azure Arc data controller.

   You can perform all three of these steps in a single step by using the Azure Arc data controller creation wizard in the Azure portal.

After you've installed the Azure Arc data controller, you can access data services such as Azure Arc-enabled SQL Managed Instance or Azure Arc-enabled PostgreSQL Hyperscale.


## Next steps

You have several additional options for creating the Azure Arc data controller:

> **Just want to try things out?**
> Get started quickly with [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) on AKS, Amazon EKS, or GKE, or in an Azure VM.
>
- [Create a data controller in direct connectivity mode with the Azure portal](create-data-controller-direct-prerequisites.md)
- [Create a data controller in indirect connectivity mode with CLI](create-data-controller-indirect-cli.md)
- [Create a data controller in indirect connectivity mode with Azure Data Studio](create-data-controller-indirect-azure-data-studio.md)
- [Create a data controller in indirect connectivity mode from the Azure portal via a Jupyter notebook in Azure Data Studio](create-data-controller-indirect-azure-portal.md)
- [Create a data controller in indirect connectivity mode with Kubernetes tools such as kubectl or oc](create-data-controller-using-kubernetes-native-tools.md)
