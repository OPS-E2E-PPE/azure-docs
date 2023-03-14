---
title: Configure Deployment Environments by using the Azure CLI extension
titleSuffix: Azure Deployment Environments
description: Learn how to set up and use the Azure Deployment Environments Preview Azure CLI extension to configure the Deployment Environments service.
ms.service: deployment-environments
ms.custom: ignite-2022
ms.author: rosemalcolm
author: RoseHJM
ms.date: 10/26/2022
ms.topic: how-to
---

# Configure Azure Deployment Environments by using the Azure CLI extension

This article shows you how to use the Azure Deployment Environments Preview Azure CLI extension to configure a Deployment Environments service. In Deployment Environments, you'll use Deployment Environments Azure CLI extension to create and work with [environments](./concept-environments-key-concepts.md#environments).

> [!IMPORTANT]
> Azure Deployment Environments is currently in preview. For legal terms that apply to Azure features that are in beta, in preview, or otherwise not yet released into general availability, see the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Setup

1. Install the Deployment Environments Azure CLI extension:

    1. [Download and install the Azure CLI](/cli/azure/install-azure-cli).
    1. Install the Deployment Environments AZ CLI extension:

      - **Automated installation**

        To install, execute the script *https://aka.ms/DevCenter/Install-DevCenterCli.ps1* directly in PowerShell:

        ```powershell
        iex "& { $(irm https://aka.ms/DevCenter/Install-DevCenterCli.ps1 ) }"
        ```
  
        Any existing dev center extension is uninstalled and the latest version is installed.

      - **Manual installation**

        In the Azure CLI, run the following command:

        ```azurecli
        az extension add --source https://fidalgosetup.blob.core.windows.net/cli-extensions/devcenter-0.1.0-py3-none-any.whl
        ```

1. Sign in to the Azure CLI:

    ```azurecli
    az login
    ```

1. Set the default subscription to the subscription you'll use to create your specific Deployment Environment resources:

    ```azurecli
    az account set --subscription {subscriptionId}
    ```

## Commands

### Create a new resource group

```azurecli
az group create -l <region-name> -n <resource-group-name>
```

Optionally, set defaults so that you don't need to pass the argument into each command:

```azurecli
az configure --defaults group=<resource-group-name>
```

### Get help for a command

```azurecli
az devcenter admin <command> --help
```

```azurecli
az devcenter dev <command> --help
```

**Dev centers**

### Create a dev center with an associated user-assigned identity

```azurecli
az devcenter admin devcenter create --identity-type "UserAssigned" --user-assigned-identity
    "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/identityGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testidentity1" --location <location-name> -g <resource-group-name> - <name>
```

### Create a dev center with an associated system-assigned identity

```azurecli
az devcenter admin devcenter create --location <location-name> -g <resource-group-name> -n <name> \
    --identity-type "SystemAssigned"
```

### List dev centers (in the specified resource group)

```azurecli
az devcenter admin devcenter list -g <resource-group-name>
```

### List dev centers (in the selected subscription if resource group isn't specified or configured in defaults)

```azurecli
az devcenter admin devcenter list --output table
```

### Get a specific dev center

```azurecli
az devcenter admin devcenter show -g <resource-group-name> --name <name>
```

### Delete a dev center

```azurecli
az devcenter admin devcenter delete -g <resource-group-name> --name <name>
```

### Force-delete a dev center

```azurecli
az devcenter admin devcenter delete -g <resource-group-name> --name <name> --yes
```

**Environment types**

### Create an environment type

```azurecli
az devcenter admin environment-type create --dev-center-name <devcenter-name> -g <resource-group-name> --name <name>
```

### List environment types by dev center

```azurecli
az devcenter admin environment-type list --dev-center-name <devcenter-name> --resource-group <resource-group-name>
```

### List environment types by project

```azurecli
az devcenter admin environment-type list --project-name <devcenter-name> --resource-group <resource-group-name>
```

### Delete an environment type

```azurecli
az devcenter admin environment-type delete --dev-center-name <devcenter-name> --name "{environmentTypeName}" \
    --resource-group <resource-group-name>
```

### List environment types by dev center and project for developers

```azurecli
az devcenter dev environment list --dev-center <devcenter-name> --project-name <project-name>
```

**Project environment types**

### Create project environment types

```azurecli
az devcenter admin project-environment-type create --description "Developer/Testing environment" --dev-center-name \
    <devcenter-name> --name "{environmentTypeName}" --resource-group <resource-group-name> \
    --deployment-target-id "/subscriptions/00000000-0000-0000-0000-000000000000" \
    --status Enabled --type SystemAssigned
```

### List project environment types by dev center

```azurecli
az devcenter admin project-environment-type list --dev-center-name <devcenter-name> \
    --resource-group <resource-group-name>
```

### List project environment types by project

```azurecli
az devcenter admin project-environment-type list --project-name <project-name> --resource-group <resource-group-name>
```

### Delete project environment types

```azurecli
az devcenter admin project-environment-type delete --project-name <project-name> \
    --environment-type-name "{environmentTypeName}" --resource-group <resource-group-name>
```

#### List allowed project environment types

```azurecli
az devcenter admin project-allowed-environment-type list --project-name <project-name> \
    --resource-group <resource-group-name>
```

**Catalogs**

### Create a catalog that uses a GitHub repository

```azurecli
az devcenter admin catalog create --git-hub secret-identifier="https://<key-vault-name>.azure-int.net/secrets/<secret-name>" uri=<git-clone-uri> branch=<git-branch> -g <resource-group-name> --name <name> --dev-center-name <devcenter-name>
```

### Create a catalog that uses an Azure DevOps repository

```azurecli
az devcenter admin catalog create --ado-git secret-identifier="https://<key-vault-name>.azure-int.net/secrets/<secret-name>" uri=<git-clone-uri> branch=<git-branch> -g <resource-group-name> --name <name> --dev-center-name <devcenter-name>
```

### Sync a catalog

```azurecli
az devcenter admin catalog sync  --name <name> --dev-center-name <devcenter-name> -g <resource-group-name>
```

### List catalogs in a dev center

```azurecli
az devcenter admin catalog list -g <resource-group-name> --dev-center-name <devcenter-name>
```

### Delete a catalog

```azurecli
az devcenter admin catalog delete -g <resource-group-name> --dev-center-name <devcenter-name> -n <name>
```

**Catalog items**

### List catalog items that are available in a project

```azurecli
az devcenter dev catalog-item list --dev-center-name <devcenter-name> --project-name <name>
```

**Projects**

### Create a project

```azurecli
az devcenter admin project create -g <resource-group-name> -n <project-name> --dev-center-id <devcenter-resource-id>
```

### List projects (in the specified resource group)

```azurecli
az devcenter admin project list -g <resource-group-name>
```

### List projects (in the selected subscription if resource group isn't specified or configured in defaults)

```azurecli
az graph query -q "Resources | where type =~ 'microsoft.devcenter/projects' | project id, name"
```

### Delete a project

```azurecli
az devcenter admin project delete -g <resource-group-name> --name <project-name>
```

**Environments**

### Create an environment

```azurecli
az devcenter dev environment create --dev-center-name <devcenter-name> \
    --project-name <project-name> -n <name> --environment-type <environment-type-name> \
    --catalog-item-name <catalog-item-name> ---catalog-name <catalog-name> \
    --parameters <deployment-parameters-json-string>
```

### Deploy an environment

```azurecli
az devcenter environment deploy-action --action-id "deploy" --dev-center <devcenter-name> \
    -g <resource-group-name> --project-name <project-name> -n <name> --parameters <parameters-json-string>
```

### List the environments in a project

```azurecli
az devcenter dev environment list --dev-center <devcenter-name> --project-name <project-name>
```

#### Delete an environment

```azurecli
az devcenter dev environment delete --dev-center <devcenter-name>  --project-name <project-name> -n <name> --user-id "me"
```
