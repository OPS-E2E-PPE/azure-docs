---
title: Frequently asked questions for Azure Kubernetes Service
description: Provides answers to some of the common questions about Azure Kubernetes Service.
services: container-service
author: neilpeterson
manager: jeconnoc

ms.service: container-service
ms.topic: article
ms.date: 6/15/2018
ms.author: nepeters
---

# Frequently asked questions about Azure Kubernetes Service (AKS)

This article addresses frequent questions about Azure Kubernetes Service (AKS).

## Which Azure regions provide the Azure Kubernetes Service (AKS) today?

- Australia East
- Canada Central
- Canada East
- Central US
- East US
- North Europe
- UK South
- West Europe
- West US 2
- West US

## When will additional regions be added?

Additional regions are added as demand increases.

## Are security updates applied to AKS agent nodes?

Azure automatically applies security patches to the nodes in your cluster on a nightly schedule. However, you are responsible for ensuring that nodes are rebooted as required. You have several options for performing node reboots:

- Manually, through the Azure portal or the Azure CLI.
- By upgrading your AKS cluster. Cluster upgrades automatically [cordon and drain nodes](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), then bring them back up with the latest Ubuntu image. Update the OS image on your nodes without changing Kubernetes versions by specifying the current cluster version in `az aks upgrade`.
- Using [Kured](https://github.com/weaveworks/kured), an open-source reboot daemon for Kubernetes. Kured runs as a [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) and monitors each node for the presence of a file indicating that a reboot is required. It then orchestrates those reboots across the cluster, following the same cordon and drain process described earlier.

## When will ACS be deprecated?

ACS will be deprecated around the time AKS that becomes GA. You will have 12 months after that date to migrate clusters to AKS. During the 12-month period, you can run all ACS operations.

## Does AKS support node autoscaling?

Node autoscaling is not supported but is on the roadmap. You might want to take a look at this open-sourced [autoscaling implementation][auto-scaler].

## Does AKS support Kubernetes role-based access control (RBAC)?

Yes, RBAC can be enabled when deploying an AKS cluster from the Azure CLI or Azure Resource Manager template. This functionality will soon come to the Azure portal.

## Can I deploy AKS into my existing virtual network?

Yes, this is supported through the [advanced networking feature](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## Is Azure Key Vault integrated with AKS?

No, it is not but this integration is planned. In the meantime, try out the following solution from [Hexadite][hexadite].

## Can I run Windows Server containers on AKS?

To run Windows Server containers, you need to run Windows Server-based nodes. Windows Server-based nodes are currently in [private preview](https://azure.microsoft.com/en-us/blog/kubernetes-on-azure/). If you need to run Windows Server containers on Kubernetes in Azure outside of the preview, please see the [documentation for acs-engine](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## Why are two resource groups created with AKS?

Each AKS deployment spans two resource groups. The first is created by you and contains only the AKS resource. The AKS resource provider automatically creates the second one during deployment with a name like *MC_myResourceGroup_myAKSCluster_eastus*. The second resource group contains all of the infrastructure resources associated with the cluster, such as VMs, networking, and storage. It is created to simplify resource cleanup.

If you are creating resources that will be used with your AKS cluster, such as storage accounts or reserved public IP address, you should place them in the automatically generated resource group.

## Does AKS offer a service level agreement?

In a service level agreement (SLA), the provider agrees to reimburse the customer for the cost of the service should the published service level not be met. Since AKS itself is free, there is no cost available to reimburse and thus no formal SLA. However, we seek to maintain availability of at least 99.5% for the Kubernetes API server.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
