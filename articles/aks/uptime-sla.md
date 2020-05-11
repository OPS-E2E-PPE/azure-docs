---
title: Azure Kubernetes Service (AKS) high availability with Uptime SLA
description: Learn about the optional high availability Uptime SLA offering for the Azure Kubernetes Service (AKS) API Server.
services: container-service
ms.topic: conceptual
ms.date: 05/11/2020
---

# Azure Kubernetes Service (AKS) Uptime SLA

Uptime SLA is an optional feature to enable financially backed higher SLA for a cluster. Uptime SLA guarantees 99.95% availability of the Kubernetes API server endpoint for clusters that use [Availability Zone][availability-zones] and 99.9% of availability for clusters that don't use availability zones. AKS uses master node replicas across update and fault domains to ensure SLA requirements are met.

Customers needing SLA for compliance reasons or extending SLA's to their customers should turn on this feature. Customers with critical workloads who need higher availability with an option of SLA benefit from enabling this feature. Enable the feature with Availability Zones to obtain higher availability of the Kubernetes API server.  

Customers can create unlimited free clusters with a service level objective (SLO) of 99.5%.

> [!Important]
> For clusters with egress lockdown, see [limit egress traffic](limit-egress-traffic.md) to open appropriate ports for Uptime SLA.

## SLA terms and conditions

Uptime SLA is a paid feature and enabled per cluster. Uptime SLA pricing is determined by the number of clusters, and not by the size of the clusters. You can view [Uptime SLA pricing details](https://azure.microsoft.com/pricing/details/kubernetes-service/) for more information.

## Region Availability

Uptime SLA is available in the following regions:

* Australia East
* Canada Central
* East US
* East US 2
* South Central US
* South East Asia
* West US 2

## Before you begin

* The Azure CLI version 2.7.0 or later

## Creating a cluster with Uptime SLA

To create a new cluster with the Uptime SLA, you use the Azure CLI.

The following example creates a resource group named *myResourceGroup* in the *eastus* location.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Use the [az aks create][az-aks-create] command to create an AKS cluster. The following example creates a cluster named *myAKSCluster* with one node. Azure Monitor for containers is also enabled using the *--enable-addons monitoring* parameter.  This operation takes several minutes to complete.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
After a few minutes, the command completes and returns JSON-formatted information about the cluster. The following JSON snippet shows the paid tier for the SKU, indicating your cluster is enabled with Uptime SLA.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": null
```

## Limitations

* You can't currently add Uptime SLA to existing clusters.
* Currently, there is no way to remove Uptime SLA from an AKS cluster.  

## Next steps

Use [Availability Zones][availability-zones] to increase high availability with your AKS cluster workloads.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
