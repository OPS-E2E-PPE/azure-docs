---
title: "Azure CLI example: Create a managed instance" 
description: Use this Azure CLI example script to create a managed instance in Azure SQL Managed Instance
services: sql-managed-instance
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: urosmil 
ms.author: urmilano
ms.reviewer:  mathoma
ms.date: 01/26/2022 
---

# Create an Azure SQL Managed Instance using the Azure CLI

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

This Azure CLI script example creates an Azure SQL Managed Instance in a dedicated subnet within a new virtual network. It also configures a route table and a network security group for the virtual network. Once the script has been successfully run, the managed instance can be accessed from within the virtual network or from an on-premises environment. See [Configure Azure VM to connect to an Azure SQL Managed Instance](/azure/azure-sql/managed-instance/connect-vm-instance-configure) and [Configure a point-to-site connection to an Azure SQL Managed Instance from on-premises](/azure/azure-sql/managed-instance/point-to-site-p2s-configure).

> [!IMPORTANT]
> For limitations, see [supported regions](/azure/azure-sql/managed-instance/resource-limits#supported-regions) and [supported subscription types](/azure/azure-sql/managed-instance/resource-limits#supported-subscription-types).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## Sample script

[!INCLUDE [cli-run-local-sign-in.md](../../../../includes/cli-run-local-sign-in.md)]

### Run the script

:::code language="azurecli" source="~/azure_cli_scripts/sql-database/managed-instance/create-managed-instance.sh" id="FullScript":::

## Clean up resources

[!INCLUDE [cli-clean-up-resources.md](../../../../includes/cli-clean-up-resources.md)]

```azurecli
az group delete --name $resourceGroup
```

## Sample reference

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Description |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Virtual network commands. |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Virtual network subnet commands. |
| [az network route-table](/cli/azure/network/route-table) | Network route table commands. |
| [az sql mi](/cli/azure/sql/mi) | SQL Managed Instance commands. |

## Next steps

For more information on Azure CLI, see [Azure CLI documentation](/cli/azure).

Additional SQL Database CLI script samples can be found in the [Azure SQL Database documentation](/azure/azure-sql/database/az-cli-script-samples-content-guide).
