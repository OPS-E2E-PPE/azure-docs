---
title: Recover from accidental deletion of resource bridge VM
description: Learn how to perform recovery operations for the Azure Arc resource bridge VM in Azure Arc-enabled System Center Virtual Machine Manager disaster scenarios.
ms.topic: how-to 
ms.custom:
ms.date: 12/06/2023
ms.services: azure-arc
ms.subservice: azure-arc-scvmm
author: Farha-Bano
ms.author: v-farhabano
manager: jsuri
---

# Recover from accidental deletion of resource bridge virtual machine

In this article, you learn how to recover the Azure Arc resource bridge connection into a working state in disaster scenarios such as accidental deletion. In such cases, the connection between on-premises infrastructure and Azure is lost and any operations performed through Arc will fail.

## Recover the Arc resource bridge in case of virtual machine deletion

To recover from Arc resource bridge VM deletion, you need to deploy a new resource bridge with the same resource ID as the current resource bridge using the following steps.

>[!Note]
> This note is applicable only if you're performing this recovery operation to upgrade your Arc resource bridge.<br><br>
> If you have VMs that are still in the older version, i.e., have *Enabled (Deprecated)* set under the *Virtual hardware operations* column in the Virtual Machines inventory of your SCVMM server in Azure, switch them to the new version by following the steps in [this article](./switch-to-the-new-version-scvmm.md#switch-to-the-new-version-existing-customer) before proceeding with the steps for resource bridge recovery.

>[!Note]
> DHCP-based Arc Resource Bridge deployment is no longer supported.<br><br>
If you had deployed Arc Resource Bridge earlier using DHCP, you must clean up your deployment by removing your resources from Azure and do a [fresh onboarding](./quickstart-connect-system-center-virtual-machine-manager-to-arc.md).

1. Copy the Azure region and resource IDs of the Arc resource bridge, custom location, and SCVMM Azure resources.

2. Download the [onboarding script](/azure/azure-arc/system-center-virtual-machine-manager/quickstart-connect-system-center-virtual-machine-manager-to-arc#download-the-onboarding-script) from the Azure portal and update the following section in the script, using the same information as the original resources in Azure.

    ```powershell
    $location = <Azure region of the resources>
    $applianceSubscriptionId = <subscription-id>
    $applianceResourceGroupName = <resource-group-name>
    $applianceName = <resource-bridge-name>

    $customLocationSubscriptionId = <subscription-id>
    $customLocationResourceGroupName = <resource-group-name>
    $customLocationName = <custom-location-name>

    $vmmserverSubscriptionId = <subscription-id>
    $vmmserverResourceGroupName = <resource-group-name>
    $vmmserverName= <SCVMM-name-in-azure>
    ```

3. [Run the onboarding script](/azure/azure-arc/system-center-virtual-machine-manager/quickstart-connect-system-center-virtual-machine-manager-to-arc#download-the-onboarding-script) again with the `-Force` parameter.

    ``` powershell-interactive
    ./resource-bridge-onboarding-script.ps1 -Force
    ```

4. [Provide the inputs](/azure/azure-arc/system-center-virtual-machine-manager/quickstart-connect-system-center-virtual-machine-manager-to-arc#script-runtime) as prompted.

5. In the same machine, run the following scripts, as applicable:
     - [Download the script](https://download.microsoft.com/download/6/b/4/6b4a5009-fed8-46c2-b22b-b24a4d0a06e3/arcvmm-appliance-dr.ps1) if you're running the script from a Windows machine
     - [Download the script](https://download.microsoft.com/download/0/5/c/05c2bcb8-87f8-4ead-9757-a87a0759071c/arcvmm-appliance-dr.sh) if you're running the script from a Linux machine

6. Once the script is run successfully, the old Resource Bridge is recovered and the connection is re-established to the existing Azure-enabled SCVMM resources.

## Next steps

[Troubleshoot Azure Arc resource bridge issues](../resource-bridge/troubleshoot-resource-bridge.md)

If the recovery steps mentioned above are unsuccessful in restoring Arc resource bridge to its original state, try one of the following channels for support:

- Get answers from Azure experts through [Microsoft Q&A](/answers/topics/azure-arc.html).
- Connect with [@AzureSupport](https://twitter.com/azuresupport), the official Microsoft Azure account for improving customer experience. Azure Support connects the Azure community to answers, support, and experts.
- [Open an Azure support request](../../azure-portal/supportability/how-to-create-azure-support-request.md).