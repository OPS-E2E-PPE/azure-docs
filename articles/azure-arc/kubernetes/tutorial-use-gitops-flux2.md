---
title: "Tutorial: Deploy applications using GitOps with Flux v2"
description: "This tutorial shows how to use GitOps with Flux v2 to manage configuration and application deployment in Azure Arc and AKS clusters."
ms.date: 11/29/2022
ms.topic: tutorial
ms.custom: template-tutorial, devx-track-azurecli, references_regions, ignite-2022
---

# Tutorial: Deploy applications using GitOps with Flux v2

GitOps with Flux v2 can be enabled as a [cluster extension](conceptual-extensions.md) in Azure Arc-enabled Kubernetes clusters or Azure Kubernetes Service (AKS) clusters. After the `microsoft.flux` cluster extension is installed, you can create one or more `fluxConfigurations` resources that sync your Git repository sources to the cluster and reconcile the cluster to the desired state. With GitOps, you can use your Git repository as the source of truth for cluster configuration and application deployment.

> [!NOTE]
> Eventually Azure will stop supporting GitOps with Flux v1, so begin using Flux v2 as soon as possible.

This tutorial describes how to use GitOps in a Kubernetes cluster. Before you dive in, take a moment to [learn how GitOps with Flux works conceptually](./conceptual-gitops-flux2.md).

> [!IMPORTANT]
> The `microsoft.flux` extension released major version 1.0.0. This includes the [multi-tenancy feature](conceptual-gitops-flux2.md#multi-tenancy). If you have existing GitOps Flux v2 configurations that use a previous version of the `microsoft.flux` extension, you can upgrade to the latest extension manually using the Azure CLI: `az k8s-extension create -g <RESOURCE_GROUP> -c <CLUSTER_NAME> -n flux --extension-type microsoft.flux -t <CLUSTER_TYPE>` (use `-t connectedClusters` for Arc clusters and `-t managedClusters` for AKS clusters).

> [!TIP]
> When using this extension with [AKS hybrid clusters provisioned from Azure](extensions.md#aks-hybrid-clusters-provisioned-from-azure-preview) you must set `--cluster-type` to use `provisionedClusters` and also add `--cluster-resource-provider microsoft.hybridcontainerservice` to the command. Installing Azure Arc extensions on AKS hybrid clusters provisioned from Azure is currently in preview.

## Prerequisites

To manage GitOps through the Azure CLI or the Azure portal, you need the following:

### For Azure Arc-enabled Kubernetes clusters

* An Azure Arc-enabled Kubernetes connected cluster that's up and running.
  
  [Learn how to connect a Kubernetes cluster to  Azure Arc](./quickstart-connect-cluster.md). If you need to connect through an outbound proxy, then assure you [install the Arc agents with proxy settings](./quickstart-connect-cluster.md?tabs=azure-cli#connect-using-an-outbound-proxy-server).

* Read and write permissions on the `Microsoft.Kubernetes/connectedClusters` resource type.

### For Azure Kubernetes Service clusters

* An MSI-based AKS cluster that's up and running.

  > [!IMPORTANT]
  > Ensure that the AKS cluster is created with MSI (not SPN), because the `microsoft.flux` extension won't work with SPN-based AKS clusters.
  > For new AKS clusters created with `az aks create`, the cluster will be MSI-based by default. For already created SPN-based clusters that need to be converted to MSI, run `az aks update -g $RESOURCE_GROUP -n $CLUSTER_NAME --enable-managed-identity`”`. For more information, see [Use a managed identity in AKS](../../aks/use-managed-identity.md).

* Read and write permissions on the `Microsoft.ContainerService/managedClusters` resource type.

### Common to both cluster types

* Read and write permissions on these resource types:

  * `Microsoft.KubernetesConfiguration/extensions`
  * `Microsoft.KubernetesConfiguration/fluxConfigurations`

* Azure CLI version 2.15 or later. [Install the Azure CLI](/cli/azure/install-azure-cli) or use the following commands to update to the latest version:

  ```azurecli
  az version
  az upgrade
  ```

* The Kubernetes command-line client, [kubectl](https://kubernetes.io/docs/user-guide/kubectl/). `kubectl` is already installed if you use Azure Cloud Shell.

  Install `kubectl` locally using the [`az aks install-cli`](/cli/azure/aks#az-aks-install-cli) command:

  ```azurecli
  az aks install-cli
  ```

* Registration of the following Azure resource providers:

  ```azurecli
  az provider register --namespace Microsoft.Kubernetes
  az provider register --namespace Microsoft.ContainerService
  az provider register --namespace Microsoft.KubernetesConfiguration
  ```

  Registration is an asynchronous process and should finish within ten minutes. To monitor the registration process, use the following command:

  ```azurecli
  az provider show -n Microsoft.KubernetesConfiguration -o table

  Namespace                          RegistrationPolicy    RegistrationState
  ---------------------------------  --------------------  -------------------
  Microsoft.KubernetesConfiguration  RegistrationRequired  Registered
  ```

### Version and region support

GitOps is currently supported in [all regions that Azure Arc-enabled Kubernetes supports](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=kubernetes-service,azure-arc). GitOps is currently supported in a subset of the regions that AKS supports. The GitOps service is adding new supported regions on a regular cadence.

The most recent version of the Flux v2 extension and the two previous versions (N-2) are supported. We generally recommend that you use the most recent version of the extension.

### Network requirements

The GitOps agents require outbound (egress) TCP to the repo source on either port 22 (SSH) or port 443 (HTTPS) to function. The agents also require access to the following outbound URLs:

| Endpoint (DNS) | Description |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com` | Required for the agent to communicate with the Kubernetes Configuration service. |
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Data plane endpoint for the agent to push status and fetch configuration information. Depends on `<region>` (the supported regions mentioned earlier). |
| `https://login.microsoftonline.com` | Required to fetch and update Azure Resource Manager tokens. |
| `https://mcr.microsoft.com` | Required to pull container images for Flux controllers. |

## Enable CLI extensions

Install the latest `k8s-configuration` and `k8s-extension` CLI extension packages:

```azurecli
az extension add -n k8s-configuration
az extension add -n k8s-extension
```

To update these packages to the latest versions:

```azurecli
az extension update -n k8s-configuration
az extension update -n k8s-extension
```

To see a list of all installed Azure CLI extensions and their versions, use the following command:

```azurecli
az extension list -o table

Experimental   ExtensionType   Name                   Path                                                       Preview   Version
-------------  --------------  -----------------      -----------------------------------------------------      --------  --------
False          whl             connectedk8s           C:\Users\somename\.azure\cliextensions\connectedk8s         False     1.2.7
False          whl             k8s-configuration      C:\Users\somename\.azure\cliextensions\k8s-configuration    False     1.5.0
False          whl             k8s-extension          C:\Users\somename\.azure\cliextensions\k8s-extension        False     1.1.0
```

> [!TIP]
> For help resolving any errors, see the Flux v2 suggestions in [Azure Arc-enabled Kubernetes and GitOps troubleshooting](troubleshooting.md#flux-v2---general).

## Apply a Flux configuration by using the Azure CLI

Use the `k8s-configuration` Azure CLI extension (or the Azure portal) to enable GitOps in an AKS or Arc-enabled Kubernetes cluster. For a demonstration, use the public [gitops-flux2-kustomize-helm-mt](https://github.com/Azure/gitops-flux2-kustomize-helm-mt) repository.

> [!IMPORTANT]
> The demonstration repo is designed to simplify your use of this tutorial and illustrate some key principles. To keep up to date, the repo can get breaking changes occasionally from version upgrades. These changes won't affect your new application of this tutorial, only previous tutorial applications that have not been deleted. To learn how to handle these changes please see the [breaking change disclaimer](https://github.com/Azure/gitops-flux2-kustomize-helm-mt#breaking-change-disclaimer-%EF%B8%8F).

In the following example:

* The resource group that contains the cluster is `flux-demo-rg`.
* The name of the Azure Arc cluster is `flux-demo-arc`.
* The cluster type is Azure Arc (`-t connectedClusters`), but this example also works with AKS (`-t managedClusters`).
* The name of the Flux configuration is `cluster-config`.
* The namespace for configuration installation is `cluster-config`.
* The URL for the public Git repository is `https://github.com/Azure/gitops-flux2-kustomize-helm-mt`.
* The Git repository branch is `main`.
* The scope of the configuration is `cluster`. This gives the operators permissions to make changes throughout cluster. To use `namespace` scope with this tutorial, [see the changes needed](conceptual-gitops-flux2.md#multi-tenancy).
* Two kustomizations are specified with names `infra` and `apps`. Each is associated with a path in the repository.
* The `apps` kustomization depends on the `infra` kustomization. (The `infra` kustomization must finish before the `apps` kustomization runs.)
* Set `prune=true` on both kustomizations. This setting assures that the objects that Flux deployed to the cluster will be cleaned up if they're removed from the repository or if the Flux configuration or kustomizations are deleted.

```azurecli
az k8s-configuration flux create -g flux-demo-rg \
-c flux-demo-arc \
-n cluster-config \
--namespace cluster-config \
-t connectedClusters \
--scope cluster \
-u https://github.com/Azure/gitops-flux2-kustomize-helm-mt \
--branch main  \
--kustomization name=infra path=./infrastructure prune=true \
--kustomization name=apps path=./apps/staging prune=true dependsOn=\["infra"\]
```

The `microsoft.flux` extension will be installed on the cluster if it isn't already. When the flux configuration is first installed, the initial compliance state may be `Pending` or `Non-compliant` because reconciliation is still ongoing. After a minute or so, query the configuration again to see the final compliance state.

```console
az k8s-configuration flux show -g flux-demo-rg -c flux-demo-arc -n cluster-config -t connectedClusters
```

These namespaces were created:

* `flux-system`: Holds the Flux extension controllers.
* `cluster-config`: Holds the Flux configuration objects.
* `nginx`, `podinfo`, `redis`: Namespaces for workloads described in manifests in the Git repository.

To confirm the namespaces, run the following command:

```azurecli
kubectl get namespaces
```

The `flux-system` namespace contains the Flux extension objects:  

* Azure Flux controllers: `fluxconfig-agent`, `fluxconfig-controller`
* OSS Flux controllers: `source-controller`, `kustomize-controller`, `helm-controller`, `notification-controller`

The Flux agent and controller pods should be in a running state. Confirm this using the following command:

```azurecli
kubectl get pods -n flux-system

NAME                                      READY   STATUS    RESTARTS   AGE
fluxconfig-agent-9554ffb65-jqm8g          2/2     Running   0          21m
fluxconfig-controller-9d99c54c8-nztg8     2/2     Running   0          21m
helm-controller-59cc74dbc5-77772          1/1     Running   0          21m
kustomize-controller-5fb7d7b9d5-cjdhx     1/1     Running   0          21m
notification-controller-7d45678bc-fvlvr   1/1     Running   0          21m
source-controller-df7dc97cd-4drh2         1/1     Running   0          21m
```

The namespace `cluster-config` has the Flux configuration objects.

```console
kubectl get crds

NAME                                                   CREATED AT
alerts.notification.toolkit.fluxcd.io                  2022-04-06T17:15:48Z
arccertificates.clusterconfig.azure.com                2022-03-28T21:45:19Z
azureclusteridentityrequests.clusterconfig.azure.com   2022-03-28T21:45:19Z
azureextensionidentities.clusterconfig.azure.com       2022-03-28T21:45:19Z
buckets.source.toolkit.fluxcd.io                       2022-04-06T17:15:48Z
connectedclusters.arc.azure.com                        2022-03-28T21:45:19Z
customlocationsettings.clusterconfig.azure.com         2022-03-28T21:45:19Z
extensionconfigs.clusterconfig.azure.com               2022-03-28T21:45:19Z
fluxconfigs.clusterconfig.azure.com                    2022-04-06T17:15:48Z
gitconfigs.clusterconfig.azure.com                     2022-03-28T21:45:19Z
gitrepositories.source.toolkit.fluxcd.io               2022-04-06T17:15:48Z
helmcharts.source.toolkit.fluxcd.io                    2022-04-06T17:15:48Z
helmreleases.helm.toolkit.fluxcd.io                    2022-04-06T17:15:48Z
helmrepositories.source.toolkit.fluxcd.io              2022-04-06T17:15:48Z
imagepolicies.image.toolkit.fluxcd.io                  2022-04-06T17:15:48Z
imagerepositories.image.toolkit.fluxcd.io              2022-04-06T17:15:48Z
imageupdateautomations.image.toolkit.fluxcd.io         2022-04-06T17:15:48Z
kustomizations.kustomize.toolkit.fluxcd.io             2022-04-06T17:15:48Z
providers.notification.toolkit.fluxcd.io               2022-04-06T17:15:48Z
receivers.notification.toolkit.fluxcd.io               2022-04-06T17:15:48Z
volumesnapshotclasses.snapshot.storage.k8s.io          2022-03-28T21:06:12Z
volumesnapshotcontents.snapshot.storage.k8s.io         2022-03-28T21:06:12Z
volumesnapshots.snapshot.storage.k8s.io                2022-03-28T21:06:12Z
websites.extensions.example.com                        2022-03-30T23:42:32Z
```

Confirm additional details of the configuration by using the following commands.

```console
kubectl get fluxconfigs -A

NAMESPACE        NAME             SCOPE     URL                                                       PROVISION   AGE
cluster-config   cluster-config   cluster   https://github.com/Azure/gitops-flux2-kustomize-helm-mt   Succeeded   44m
```

```console
kubectl get gitrepositories -A

NAMESPACE        NAME             URL                                                       READY   STATUS                                                            AGE
cluster-config   cluster-config   https://github.com/Azure/gitops-flux2-kustomize-helm-mt   True    Fetched revision: main/4f1bdad4d0a54b939a5e3d52c51464f67e474fcf   45m
```

```console
kubectl get helmreleases -A

NAMESPACE        NAME      READY   STATUS                             AGE
cluster-config   nginx     True    Release reconciliation succeeded   66m
cluster-config   podinfo   True    Release reconciliation succeeded   66m
cluster-config   redis     True    Release reconciliation succeeded   66m
```

```console
kubectl get kustomizations -A


NAMESPACE        NAME                   READY   STATUS                                                            AGE
cluster-config   cluster-config-apps    True    Applied revision: main/4f1bdad4d0a54b939a5e3d52c51464f67e474fcf   65m
cluster-config   cluster-config-infra   True    Applied revision: main/4f1bdad4d0a54b939a5e3d52c51464f67e474fcf   65m
```

Workloads are deployed from manifests in the Git repository.

```console
kubectl get deploy -n nginx

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
nginx-ingress-controller                   1/1     1            1           67m
nginx-ingress-controller-default-backend   1/1     1            1           67m

kubectl get deploy -n podinfo

NAME      READY   UP-TO-DATE   AVAILABLE   AGE
podinfo   1/1     1            1           68m

kubectl get all -n redis

NAME                 READY   STATUS    RESTARTS   AGE
pod/redis-master-0   1/1     Running   0          68m

NAME                     TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
service/redis-headless   ClusterIP   None          <none>        6379/TCP   68m
service/redis-master     ClusterIP   10.0.13.182   <none>        6379/TCP   68m

NAME                            READY   AGE
statefulset.apps/redis-master   1/1     68m
```

### Control which controllers are deployed with the Flux cluster extension

The `source`, `helm`, `kustomize`, and `notification` Flux controllers are installed by default. The `image-automation` and `image-reflector` controllers must be enabled explicitly. You can use the `k8s-extension` CLI to make those choices:

* `--config source-controller.enabled=<true/false>` (default `true`)
* `--config helm-controller.enabled=<true/false>` (default `true`)
* `--config kustomize-controller.enabled=<true/false>` (default `true`)
* `--config notification-controller.enabled=<true/false>` (default `true`)
* `--config image-automation-controller.enabled=<true/false>` (default `false`)
* `--config image-reflector-controller.enabled=<true/false>` (default `false`)

Here's an example for including the [Flux image-reflector and image-automation controllers](https://fluxcd.io/docs/components/image/). If the Flux extension was created automatically when a Flux configuration was first created, the extension name will be `flux`.

```console
az k8s-extension create -g <cluster_resource_group> -c <cluster_name> -t <connectedClusters or managedClusters> --name flux --extension-type microsoft.flux --config image-automation-controller.enabled=true image-reflector-controller.enabled=true
```

### Using Kubelet identity as authentication method for Azure Kubernetes Clusters

When working with Azure Kubernetes clusters, one of the authentication options to use is kubelet identity. In order to let Flux use this, add a parameter --config useKubeletIdentity=true at the time of Flux extension installation.

```console
az k8s-extension create --resource-group <resource-group> --cluster-name <cluster-name> --cluster-type managedClusters --name flux --extension-type microsoft.flux --config useKubeletIdentity=true
```

### Red Hat OpenShift onboarding guidance

Flux controllers require a **nonroot** [Security Context Constraint](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.2/html/authentication/managing-pod-security-policies) to properly provision pods on the cluster. These constraints must be added to the cluster prior to onboarding of the `microsoft.flux` extension.

```console
NS="flux-system"
oc adm policy add-scc-to-user nonroot system:serviceaccount:$NS:kustomize-controller
oc adm policy add-scc-to-user nonroot system:serviceaccount:$NS:helm-controller
oc adm policy add-scc-to-user nonroot system:serviceaccount:$NS:source-controller
oc adm policy add-scc-to-user nonroot system:serviceaccount:$NS:notification-controller
oc adm policy add-scc-to-user nonroot system:serviceaccount:$NS:image-automation-controller
oc adm policy add-scc-to-user nonroot system:serviceaccount:$NS:image-reflector-controller
```

For more information on OpenShift guidance for onboarding Flux, refer to the [Flux documentation](https://fluxcd.io/docs/use-cases/openshift/#openshift-setup).

## Work with parameters

Flux supports many parameters to enable various scenarios. For a description of all parameters that Flux supports, see the [official Flux documentation](https://fluxcd.io/docs/). Flux in Azure doesn't support all parameters yet. Let us know if a parameter you need is missing from the Azure implementation.

For more information about available parameters and how to use them, see [GitOps Flux v2 configurations with AKS and Azure Arc-enabled Kubernetes](conceptual-gitops-flux2.md#parameters).

## Manage GitOps configurations by using the Azure portal

The Azure portal is useful for managing GitOps configurations and the Flux extension in Azure Arc-enabled Kubernetes or AKS clusters. In the Azure portal, you can see all of the Flux configurations associated with each cluster and get detailed information, including the overall compliance state of each cluster.

The Flux objects that have been deployed to each  cluster are also shown, along with their installation parameters, compliance state, and any errors.

You can also use the Azure portal to create, update, and delete GitOps configurations.

## Manage cluster configuration by using the Flux Kustomize controller

The Flux Kustomize controller is installed as part of the `microsoft.flux` cluster extension. It allows the declarative management of cluster configuration and application deployment by using Kubernetes manifests synced from a Git repository. These Kubernetes manifests can optionally include a *kustomize.yaml* file.

For usage details, see the following:

* [Flux Kustomize controller](https://fluxcd.io/docs/components/kustomize/)
* [Kustomize reference documents](https://kubectl.docs.kubernetes.io/references/kustomize/)
* [The kustomization file](https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/)
* [Kustomize project](https://kubectl.docs.kubernetes.io/references/kustomize/)
* [Kustomize guides](https://kubectl.docs.kubernetes.io/guides/config_management/)

## Manage Helm chart releases by using the Flux Helm controller

The Flux Helm controller is installed as part of the `microsoft.flux` cluster extension. It allows you to declaratively manage Helm chart releases with Kubernetes manifests that you maintain in your Git repository.

For usage details, see the following:

* [Flux for Helm users](https://fluxcd.io/docs/use-cases/helm/)
* [Manage Helm releases](https://fluxcd.io/docs/guides/helmreleases/)
* [Migrate to Flux v2 Helm from Flux v1 Helm](https://fluxcd.io/docs/migration/helm-operator-migration/)
* [Flux Helm controller](https://fluxcd.io/docs/components/helm/)

> [!TIP]
> Because of how Helm handles index files, processing Helm charts is an expensive operation and can have very high memory footprint. As a result, reconciling a large number of Helm charts at once can cause memory spikes and `OOMKilled` errors. By default, the controller sets its memory limit at 1Gi and its memory requests at 64Mi. To increase this limit and requests due to a high number of large Helm chart reconciliations, run the following command after installing the microsoft.flux extension:
>
> `az k8s-extension update -g <resource-group> -c <cluster-name> -n flux -t connectedClusters --config source-controller.resources.limits.memory=2Gi source-controller.resources.requests.memory=300Mi`

### Use the GitRepository source for Helm charts

If your Helm charts are stored in the `GitRepository` source that you configure as part of the `fluxConfigurations` resource, you can indicate that the configured source should be used as the source of the Helm charts by adding `clusterconfig.azure.com/use-managed-source: "true"` to your HelmRelease.yaml file, as shown in the following example:

```yaml
---
apiVersion: helm.toolkit.fluxcd.io/v2beta1
kind: HelmRelease
metadata:
  name: somename
  namespace: somenamespace
  annotations:
    clusterconfig.azure.com/use-managed-source: "true"
spec:
  ...
```

By using this annotation, the HelmRelease that is deployed will be patched with the reference to the configured source. Currently, only `GitRepository` source is supported.

## Delete the Flux configuration and extension

Use the commands below to delete your Flux configuration and, if desired, the Flux extension itself.

### Delete the Flux configuration

The command below deletes both the `fluxConfigurations` resource in Azure and the Flux configuration objects in the cluster. Because the Flux configuration was originally created with the `prune=true` parameter for the kustomization, all of the objects created in the cluster based on manifests in the Git repository will be removed when the Flux configuration is removed. However, this command does not remove the Flux extension itself.

For an Azure Arc-enabled Kubernetes cluster, use this command:

```azurecli
az k8s-configuration flux delete -g flux-demo-rg -c flux-demo-arc -n cluster-config -t connectedClusters --yes
```

For an AKS cluster, use the same command but with `-t managedClusters` replacing `-t connectedClusters`.

### Delete the Flux cluster extension

You can delete the Flux extension by using either Azure CLI or the Azure portal. The delete action removes both the `microsoft.flux` extension resource in Azure and the Flux extension objects in the cluster.

If the Flux extension was created automatically when the Flux configuration was first created, the extension name will be `flux`.

For an Azure Arc-enabled Kubernetes cluster, use this command:

```azurecli
az k8s-extension delete -g flux-demo-rg -c flux-demo-arc -n flux -t connectedClusters --yes
```

For an AKS cluster, use the same command but with `-t managedClusters`replacing `-t connectedClusters`.

## Migrate from Flux v1

If you've been using Flux v1 in Azure Arc-enabled Kubernetes or AKS clusters and want to migrate to using Flux v2 in the same clusters, you first need to delete the Flux v1 `sourceControlConfigurations` from the clusters.  The `microsoft.flux` cluster extension won't install if there are Flux v1 `sourceControlConfigurations` resources in the cluster.

Use these Azure CLI commands to find and then delete existing `sourceControlConfigurations` in a cluster:

```azurecli
az k8s-configuration list --cluster-name <Arc or AKS cluster name> --cluster-type <connectedClusters OR managedClusters> --resource-group <resource group name>
az k8s-configuration delete --name <configuration name> --cluster-name <Arc or AKS cluster name> --cluster-type <connectedClusters OR managedClusters> --resource-group <resource group name>
```

You can also use the Azure portal to view and delete existing GitOps configurations in Azure Arc-enabled Kubernetes or AKS clusters.

More information about migration from Flux v1 to Flux v2 is available in the fluxcd project: [Migrate from Flux v1 to v2](https://fluxcd.io/docs/migration/).

## Next steps

* Read more about [configurations and GitOps](conceptual-gitops-flux2.md).
* Learn how to [use Azure Policy to enforce GitOps at scale](./use-azure-policy-flux-2.md).
