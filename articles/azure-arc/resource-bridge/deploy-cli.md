---
title: Azure Arc resource bridge (preview) deployment command overview
description: Learn about the Azure CLI commands which can be used to manage your Azure Arc resource bridge (preview) deployment.
ms.date: 02/06/2023
ms.topic: overview
---

# Azure Arc resource bridge (preview) deployment command overview

[Azure CLI](/cli/azure/install-azure-cli) is required to deploy the Azure Arc resource bridge. When deploying Arc resource bridge with a corresponding partner product, the Azure CLI commands may be combined into an automation script, along with additional provider-specific commands. To learn about installing Arc resource bridge with a corresponding partner product, see:

- [Connect VMware vCenter Server to Azure with Arc resource bridge](../vmware-vsphere/quick-start-connect-vcenter-to-arc-using-script.md)
- [Connect System Center Virtual Machine Manager (SCVMM) to Azure with Arc resource bridge](../system-center-virtual-machine-manager/quickstart-connect-system-center-virtual-machine-manager-to-arc.md#download-the-onboarding-script)
- [Azure Stack HCI VM Management through Arc resource bridge](/azure-stack/hci/manage/azure-arc-vm-management-prerequisites)
- [AKS on HCI (AKS hybrid) - Arc resource bridge deployment](/azure/aks/hybrid/deploy-arc-resource-bridge-windows-server)

This topic provides an overview of the [Azure CLI commands](/cli/azure/arcappliance) that are used to manage Arc resource bridge (preview) deployment, in the order in which they are typically used for deployment.

## az arcappliance createconfig

Creates the configuration files used by Arc resource bridge. Credentials that are provided during `createconfig`, such as vCenter credentials for VMware vSphere, are stored in a configuration file and locally within Arc resource bridge. These credentials should be a separate user account used only by Arc resource bridge, with permission to view, create, delete, and manage on-premises resources. If the credentials change, then the credentials on the resource bridge should be rotated.

The `createconfig` command features two modes: interactive and non-interactive. Interactive mode provides helpful prompts that explain the parameter and what to pass. To initiate interactive mode, pass only the three required parameters. Non-interactive mode allows you to pass all the parameters needed to create the configuration files without being prompted, which saves time and is useful for automation scripts. Three configuration files are generated: resource.yaml, appliance.yaml and infra.yaml.  These files should be kept and stored in a secure location, as they're required for maintenance of Arc resource bridge.

> [!NOTE]
> Azure Stack HCI and Hybrid AKS use different commands to create the Arc resource bridge configuration files.

## az arcappliance validate

Checks the configuration files for a valid schema, cloud and core validations (such as management machine connectivity to required URLs), network settings, and no proxy settings.  

## az arcappliance prepare

Downloads the OS images from Microsoft and uploads them to the on-premises cloud image gallery to prepare for the creation of the appliance VM.

This command can take up to 30 minutes to complete, depending on the network download speed. Allow the command to complete before continuing with the deployment.

## az arcappliance deploy

Deploys an on-premises instance of Arc resource bridge as an appliance VM, bootstrapped to be a Kubernetes management cluster. Gets all necessary pods into a running state.

## az arcappliance create

Creates Arc resource bridge in Azure as an ARM resource, then establishes the connection between the ARM resource and on-premises appliance VM.

Running this command is the last step in the deployment process.  

## az arcappliance show

Gets the ARM resource information for Arc resource bridge. This information helps you monitor the status of the appliance. Successful appliance creation results in `ProvisioningState = Succeeded` and `Status = Running`.

## az arcappliance delete

Deletes the appliance VM and Azure resources. It doesn't clean up the OS image, which remains in the on-premises cloud gallery.

If a deployment fails, you must run this command to clean up the environment before you attempt to deploy again.

## Next steps

- Explore the full list of [Azure CLI commands and required parameters](/cli/azure/arcappliance) for Arc resource bridge.
- Get [troubleshooting tips for Arc resource bridge](troubleshoot-resource-bridge.md).
