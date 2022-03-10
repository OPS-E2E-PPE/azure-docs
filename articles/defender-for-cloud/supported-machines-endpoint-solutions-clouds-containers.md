---
title: Microsoft Defender for Containers feature availability
description: Learn about the availability of Microsoft Defender for Cloud containers features according to OS, machine type, and cloud deployment.
ms.topic: overview
ms.date: 03/08/2022
ms.custom: references_regions
---

# Defender for Containers feature availability

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

The **tabs** below show the features of Microsoft Defender for Cloud that are available for Windows and Linux machines.

## Supported features by environment 

### [**Azure (AKS)**](#tab/azure-aks)

| Domain | Feature | Supported Resources | Release state <sup>[1](#footnote1)</sup> | Windows support | Agentless/Agent-based | Pricing Tier | Azure clouds availability |
|--|--|--|--|--|--|--|--|
| Compliance | Docker CIS | VMs | GA | X | Log Analytics agent | Defender for Servers |  |
| VA | Registry scan | ACR, Private ACR | GA | ✓ (Preview) | Agentless | Defender for Containers  | Commercial clouds<br><br> National clouds: Azure Government, Azure China 21Vianet |
| VA | View vulnerabilities for running images | AKS | Preview | X | Defender profile | Defender for Containers | Commercial clouds |
| Hardening | Control plane recommendations | ACR, AKS | GA | ✓ | Agentless | Free | Commercial clouds<br><br> National clouds: Azure Government, Azure China 21Vianet |
| Hardening | Kubernetes data plane recommendations | AKS | GA | X | Azure Policy | Free | Commercial clouds<br><br> National clouds: Azure Government, Azure China 21Vianet |
| Runtime Threat Detection | Agentless threat detection | AKS | GA | ✓ | Agentless | Defender for Containers | Commercial clouds<br><br> National clouds: Azure Government, Azure China 21Vianet |
| Runtime Threat Detection | Agent-based threat detection | AKS | Preview | X | Defender profile | Defender for Containers | Commercial clouds |
| Discovery and Auto provisioning | Discovery of uncovered/unprotected clusters | AKS | GA | ✓ | Agentless | Free | Commercial clouds<br><br> National clouds: Azure Government, Azure China 21Vianet |
| Discovery and Auto provisioning | Auditlog collection for agentless threat detection | AKS | GA | ✓ | Agentless | Defender for Containers | Commercial clouds<br><br> National clouds: Azure Government, Azure China 21Vianet |
| Discovery and Auto provisioning | Auto provisioning of Defender profile | AKS | GA | X | Agentless | Defender for Containers | Commercial clouds<br><br> National clouds: Azure Government, Azure China 21Vianet |
| Discovery and Auto provisioning | Auto provisioning of Azure policy add-on | AKS | GA | X | Agentless | Free | Commercial clouds<br><br> National clouds: Azure Government, Azure China 21Vianet |

<sup><a name="footnote1"></a>1</sup> Specific features are in preview. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

### [**AWS (EKS)**](#tab/aws-eks)

| Domain | Feature | Supported Resources | Release state <sup>[1](#footnote1)</sup> | Windows support | Agentless/Agent-based | Pricing tier |
|--|--| -- | -- | -- | -- | --| 
| Compliance | Docker CIS | EC2 | Preview | X | Log Analytics agent | Defender for Servers |
| VA | Registry scan | N/A | - | - | - | - |
| VA | View vulnerabilities for running images | N/A | - | - | - | - |
| Hardening | Control plane recommendations | N/A | - | - | - | - |
| Hardening | Kubernetes data plane recommendations | EKS | Preview | X | Azure Policy extension | Defender for Containers |
| Runtime Threat Detection | Agentless threat detection | EKS | Preview | X | Agentless | Defender for Containers |
| Runtime Threat Detection | Agent-based threat detection | EKS | Preview | X | Defender extension | Defender for Containers |
| Discovery and Auto provisioning | Discovery of uncovered/unprotected clusters | EKS | Preview | X | Agentless | Free |
| Discovery and Auto provisioning | Auditlog collection for agentless threat detection | EKS | Preview | X | Agentless | Defender for Containers |
| Discovery and Auto provisioning | Auto provisioning of Defender extension | N/A | N/A | X | - | - |
| Discovery and Auto provisioning | Auto provisioning of Azure policy extension | N/A | N/A | X | - | - |

<sup><a name="footnote1"></a>1</sup> Specific features are in preview. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

### [**GCP (GKE)**](#tab/gcp-gke)

| Domain | Feature | Supported Resources | Release state <sup>[1](#footnote1)</sup> | Windows support | Agentless/Agent-based | Pricing tier |
|--|--| -- | -- | -- | -- | --| 
| Compliance | Docker CIS | GCP VMs | Preview | X | Log Analytics agent | Defender for Servers |
| VA | Registry scan | N/A | - | - | - | - |
| VA | View vulnerabilities for running images | N/A | - | - | - | - |
| Hardening | Control plane recommendations | N/A | - | - | - | - |
| Hardening | Kubernetes data plane recommendations | GKE | Preview | X | Azure Policy extension | Defender for Containers |
| Runtime Threat Detection | Agentless threat detection | GKE | Preview | X | Agentless | Defender for Containers |
| Runtime Threat Detection | Agent-based threat detection | GKE | Preview | X | Defender extension | Defender for Containers |
| Discovery and Auto provisioning | Discovery of uncovered/unprotected clusters | GKE | Preview | X | Agentless | Free |
| Discovery and Auto provisioning | Auditlog collection for agentless threat detection | GKE | Preview | X | Agentless | Defender for Containers |
| Discovery and Auto provisioning | Auto provisioning of Defender DaemonSet | GKE | Preview | X | Agentless | Defender for Containers |
| Discovery and Auto provisioning | Auto provisioning of Azure policy extension | GKE | Preview | X | Agentless | Defender for Containers |

<sup><a name="footnote1"></a>1</sup> Specific features are in preview. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

### [**On-prem/IasS (ARC)**](#tab/iass-arc)

| Domain | Feature | Supported Resources | Release state <sup>[1](#footnote1)</sup> | Windows support | Agentless/Agent-based | Pricing tier |
|--|--| -- | -- | -- | -- | --| 
| Compliance | Docker CIS | Arc enabled VMs | Preview | X | Log Analytics agent | Defender for Servers |
| VA | Registry scan | ACR, Private ACR | Preview | ✓ | Agentless | Defender for Containers |
| VA | View vulnerabilities for running images | Arc enabled K8s clusters | Preview | X | Defender extension | Defender for Containers |
| Hardening | Control plane recommendations | N/A | - | - | - | - |
| Hardening | Kubernetes data plane recommendations | Arc enabled K8s clusters | Preview | X | Azure Policy extension | Defender for Containers |
| Runtime Threat Detection | Threat detection via auditlog | Arc enabled K8s clusters | - | ✓ | Defender extension | Defender for Containers |
| Runtime Threat Detection | Agent-based threat detection | Arc enabled K8s clusters | Preview | X | Defender extension | Defender for Containers |
| Discovery and Auto provisioning | Discovery of uncovered/unprotected clusters | Arc enabled K8s clusters | Preview | - | Agentless | Free |
| Discovery and Auto  provisioning | Auditlog collection for threat detection | Arc enabled K8s clusters | Preview | ✓ | Defender extension | Defender for Containers |
| Discovery and Auto provisioning | Auto provisioning of Defender extension | Arc enabled K8s clusters | Preview | ✓ | Agentless | Defender for Containers |
| Discovery and Auto provisioning | Auto provisioning of Azure policy extension | Arc enabled K8s clusters | Preview | X | Agentless | Defender for Containers |

<sup><a name="footnote1"></a>1</sup> Specific features are in preview. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

---

## Additional information

### Registries and images

| Aspect | Details |
|--|--|
| Registries and images | **Supported**<br> • [ACR registries protected with Azure Private Link](../container-registry/container-registry-private-link.md) (Private registries requires access to Trusted Services) <br> • Windows images (Preview). This is free while it's in preview, and will incur charges (based on the Defender for Containers plan) when it becomes generally available.<br><br>**Unsupported**<br> • Super-minimalist images such as [Docker scratch](https://hub.docker.com/_/scratch/) images<br> • "Distroless" images that only contain an application and its runtime dependencies without a package manager, shell, or OS<br> • Images with [Open Container Initiative (OCI) Image Format Specification](https://github.com/opencontainers/image-spec/blob/master/spec.md) |


### Kubernetes distributions and configurations

| Aspect | Details |
|--|--|
| Kubernetes distributions and configurations | **Supported**<br> • Any Cloud Native Computing Foundation (CNCF) certified Kubernetes clusters<br>• [Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)<sup>[1](#footnote1)</sup><br> • [Amazon Elastic Kubernetes Service (EKS)](https://aws.amazon.com/eks/)<br> • [Google Kubernetes Engine (GKE) Standard](https://cloud.google.com/kubernetes-engine/) <br><br> **Supported via Arc enabled Kubernetes** <sup>[2](#footnote2)</sup> <sup>[3](#footnote3)</sup><br>• [Azure Kubernetes Service on Azure Stack HCI](/azure-stack/aks-hci/overview)<br> • [Kubernetes](https://kubernetes.io/docs/home/)<br> • [AKS Engine](https://github.com/Azure/aks-engine)<br> • [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)<br> • [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (version 4.6 or newer)<br> • [VMware Tanzu Kubernetes Grid](https://tanzu.vmware.com/kubernetes-grid)<br> • [Rancher Kubernetes Engine](https://rancher.com/docs/rke/latest/en/)<br><br>**Unsupported**<br> • Any [taints](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/) applied to your nodes *might* disrupt the configuration of Defender for Containers<br>  |

<sup><a name="footnote1"></a>1</sup>The AKS Defender profile doesn't support AKS clusters that don't have RBAC role enabled.<br>
<sup><a name="footnote2"></a>2</sup>Any Cloud Native Computing Foundation (CNCF) certified Kubernetes clusters should be supported, but only the specified clusters have been tested.<br>
<sup><a name="footnote3"></a>3</sup>To get [Microsoft Defender for Containers](../azure-arc/kubernetes/overview.md) protection for you should onboard to [Azure Arc-enabled Kubernetes](https://mseng.visualstudio.com/TechnicalContent/_workitems/recentlyupdated/) and enable Defender for Containers as an Arc extension.

> [!NOTE]
> For additional requirements for Kuberenets workload protection, see [existing limitations](../governance/policy/concepts/policy-for-kubernetes.md#limitations).

## Next steps

- Learn how [Defender for Cloud collects data using the Log Analytics Agent](enable-data-collection.md).
- Learn how [Defender for Cloud manages and safeguards data](data-security.md).
- Review the [platforms that support Defender for Cloud](security-center-os-coverage.md).
