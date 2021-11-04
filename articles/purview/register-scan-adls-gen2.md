---
title: 'Register and scan Azure Data Lake Storage (ADLS) Gen2'
description: This article outlines the process to register an Azure Data Lake Storage Gen2 data source in Azure Purview including instructions to authenticate and interact with the Azure Data Lake Storage Gen2 source
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
---
# Connect to Azure Data Lake Gen2 in Azure Purview

This article outlines the process to register an Azure Data Lake Storage Gen2 data source in Azure Purview including instructions to authenticate and interact with the Azure Data Lake Storage Gen2 source

## Supported capabilities

|**Metadata Extraction**|  **Full Scan**  |**Incremental Scan**|**Scoped Scan**|**Classification**|**Access Policy**|**Lineage**|
|---|---|---|---|---|---|---|
| [Yes](#register) | [Yes](#scan)|[Yes](#scan) | [Yes](#scan)|[Yes](#scan)| Yes | [Data Factory Lineage](how-to-link-azure-data-factory.md) |

## Prerequisites

* An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* An active [Purview resource](create-catalog-portal.md).

* You will need to be a Data Source Administrator and Data Reader to register a source and manage it in the Purview Studio. See our [Azure Purview Permissions page](catalog-permissions.md) for details.

## Register

This section will enable you to register the ADLS Gen2 data source and set up an appropriate authentication mechanism to ensure successful scanning of the data source.

### Steps to register

It is important to register the data source in Azure Purview prior to setting up a scan for the data source.

1. Go to the [Azure portal](https://portal.azure.com), and navigate to the **Purview accounts** page and click on your _Purview account_

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-purview-acct.png" alt-text="Screenshot that shows the Purview account used to register the data source":::

1. **Open Purview Studio** and navigate to the **Data Map --> Sources**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-open-purview-studio.png" alt-text="Screenshot that shows the link to open Purview Studio":::

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sources.png" alt-text="Screenshot that navigates to the Sources link in the Data Map":::

1. Create the [Collection hierarchy](./quickstart-create-collection.md) using the **Collections** menu and assign permissions to individual sub-collections, as required

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-collections.png" alt-text="Screenshot that shows the collection menu to create collection hierarchy":::

1. Navigate to the appropriate collection under the **Sources** menu and click on the **Register** icon to register a new ADLS Gen2 data source

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-register-source.png" alt-text="Screenshot that shows the collection used to register the data source":::

1. Select the **Azure Data Lake Storage Gen2** data source and click **Continue**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-select-data-source.png" alt-text="Screenshot that allows selection of the data source":::

1. Provide a suitable **Name** for the data source, select the relevant **Azure subscription**, existing **Data Lake Store account name** and the **collection** and click on **Apply**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-data-source-details.png" alt-text="Screenshot that shows the details to be entered in order to register the data source":::

1. The ADLS Gen2 storage account will be shown under the selected Collection

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-data-source-collection.png" alt-text="Screenshot that shows the data source mapped to the collection to initiate scanning":::

## Scan

### Prerequisites for scan

In order to have access to scan the data source, an authentication method in the ADLS Gen2 Storage account needs to be configured.
The following options are supported:

> [!Note]
> If you have firewall enabled for the storage account, you must use Managed Identity authentication method when setting up a scan.

* **Managed Identity (Recommended)** - As soon as the Azure Purview Account is created, a system **Managed Identity** is created automatically in Azure AD tenant. Depending on the type of resource, specific RBAC role assignments are required for the Azure Purview MSI to perform the scans.

* **Account Key** - Secrets can be created inside an Azure Key Vault to store credentials in order to enable access for Azure Purview to scan data sources securely using the secrets. A secret can be a storage account key, SQL login password or a password.

   > [!Note]
   > If you use this option, you need to deploy an _Azure key vault_ resource in your subscription and assign _Azure Purview account’s_ MSI with required access permission to secrets inside _Azure key vault_.

* **Service Principal** - In this method, you can create a new or use an existing service principal in your Azure Active Directory tenant.

### Authentication for a scan

#### Using Managed Identity for scanning

It is important to give your Purview account the permission to scan the ADLS Gen2 data source. You can add the Catalog's MSI at the Subscription, Resource Group, or Resource level, depending on what you want it to have scan permissions on.

> [!Note]
> You need to be an owner of the subscription to be able to add a managed identity on an Azure resource.

1. From the [Azure portal](https://portal.azure.com), find either the subscription, resource group, or resource (for example, an Azure Data Lake Storage Gen2 storage account) that you would like to allow the catalog to scan.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-storage-acct.png" alt-text="Screenshot that shows the storage account":::

1. Click on **Access Control (IAM)** in the left navigation and then click on **+ Add** --> **Add role assignment**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-control.png" alt-text="Screenshot that shows the access control for the storage account":::

1. Set the **Role** to **Storage Blob Data Reader** and enter your _Azure Purview account name_ under **Select** input box. Then, select **Save** to give this role assignment to your Purview account.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-assign-permissions.png" alt-text="Screenshot that shows the details to assign permissions for the Purview account":::

> [!Note]
> For more details, please see steps in [Authorize access to blobs and queues using Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md)

> [!NOTE]
> If you have firewall enabled for the storage account, you must use **Managed Identity** authentication method when setting up a scan.

1. Go into your ADLS Gen2 storage account in [Azure portal](https://portal.azure.com)
1. Navigate to **Security + networking > Networking**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-networking.png" alt-text="Screenshot that shows the details to provide firewall access":::

1. Choose **Selected Networks** under **Allow access from**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-network-access.png" alt-text="Screenshot that shows the details to allow access to selected networks":::

1. In the **Exceptions** section, select **Allow trusted Microsoft services to access this storage account** and hit **Save**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-permission-microsoft-services.png" alt-text="Screenshot that shows the exceptions to allow trusted Microsoft services to access the storage account":::

#### Using Account Key for scanning

When authentication method selected is **Account Key**, you need to get your access key and store in the key vault:

1. Navigate to your ADLS Gen2 storage account
1. Select **Security + networking > Access keys**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-keys.png" alt-text="Screenshot that shows the access keys in the storage account":::

1. Copy your *key* and save it separately for the next steps

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-key.png" alt-text="Screenshot that shows the access keys to be copied":::

1. Navigate to your key vault

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-key-vault.png" alt-text="Screenshot that shows the key vault":::

1. Select **Settings > Secrets** and click on **+ Generate/Import**
:::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-generate-secret.png" alt-text="Screenshot that shows the key vault option to generate a secret":::

1. Enter the **Name** and **Value** as the *key* from your storage account

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-secret-values.png" alt-text="Screenshot that shows the key vault option to enter the secret values":::

1. Select **Create** to complete

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-secret.png" alt-text="Screenshot that shows the key vault option to create a secret":::

1. If your key vault is not connected to Purview yet, you will need to [create a new key vault connection](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Finally, [create a new credential](manage-credentials.md#create-a-new-credential) using the key to setup your scan

#### Using Service Principal for scanning

##### Creating a new service principal

If you need to [Create a new service principal](./create-service-principal-azure.md), it is required to register an application in your Azure AD tenant and provide access to Service Principal in your data sources. Your Azure AD Global Administrator or other roles such as Application Administrator can perform this operation.

##### Getting the Service Principal's Application ID

1. Copy the **Application (client) ID** present in the **Overview** of the [_Service Principal_](./create-service-principal-azure.md) already created

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-appln-id.png" alt-text="Screenshot that shows the Application (client) ID for the Service Principal":::

##### Granting the Service Principal access to your ADLS Gen2 account

It is important to give your service principal the permission to scan the ADLS Gen2 data source. You can add the Catalog's MSI at the Subscription, Resource Group, or Resource level, depending on what you want it to have scan permissions on.

> [!Note]
> You need to be an owner of the subscription to be able to add a service principal on an Azure resource.

1. From the [Azure portal](https://portal.azure.com), find either the subscription, resource group, or resource (for example, an Azure Data Lake Storage Gen2 storage account) that you would like to allow the catalog to scan.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-storage-acct.png" alt-text="Screenshot that shows the storage account":::

1. Click on **Access Control (IAM)** in the left navigation and then click on **+ Add** --> **Add role assignment**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-control.png" alt-text="Screenshot that shows the access control for the storage account":::

1. Set the **Role** to **Storage Blob Data Reader** and enter your _service principal_ under **Select** input box. Then, select **Save** to give this role assignment to your Purview account.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-permission.png" alt-text="Screenshot that shows the details to provide storage account permissions to the service principal":::

### Create the scan

1. Open your **Purview account** and click on the **Open Purview Studio**
1. Navigate to the **Data map** --> **Sources** to view the collection hierarchy
1. Click on the **New Scan** icon under the **ADLS Gen2 data source** registered earlier

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-new-scan.png" alt-text="Screenshot that shows the screen to create a new scan":::

#### If using Managed Identity

1. Provide a **Name** for the scan, select the **Purview MSI** under **Credential**, choose the appropriate collection for the scan and click on **Test connection**. On a successful connection, click **Continue**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-managed-identity.png" alt-text="Screenshot that shows the Managed Identity option to run the scan":::

#### If using Account Key

1. Provide a **Name** for the scan, choose the appropriate collection for the scan and select **Authentication method** as _Account Key_

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-acct-key.png" alt-text="Screenshot that shows the Account Key option for scanning":::

#### If using Service Principal

1. Provide a **Name** for the scan, choose the appropriate collection for the scan and click on the **+ New** under **Credential**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-option.png" alt-text="Screenshot that shows the option for service principal to enable scanning":::

1. Select the appropriate **Key vault connection** and the **Secret name** that was used while creating the _Service Principal_. The **Service Principal ID** is the **Application (client) ID** copied earlier.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-service-principal-option.png" alt-text="Screenshot that shows the service principal option":::

1. Click on **Test connection**. On a successful connection, click **Continue**

### Scope and run the scan

1. You can scope your scan to specific folders and subfolders by choosing the appropriate items in the list.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scope-scan.png" alt-text="Scope your scan":::

1. Then select a scan rule set. You can choose between the system default, existing custom rule sets, or create a new rule set inline.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scan-rule-set.png" alt-text="Scan rule set":::

1. If creating a new _scan rule set_, select the **file types** to be included in the scan rule.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-file-types.png" alt-text="Scan rule set file types":::

1. You can select the **classification rules** to be included in the scan rule

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-classification rules.png" alt-text="Scan rule set classification rules":::

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-select-scan-rule-set.png" alt-text="Scan rule set selection":::

1. Choose your scan trigger. You can set up a schedule or run the scan once.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scan-trigger.png" alt-text="scan trigger":::

1. Review your scan and select **Save and run**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-review-scan.png" alt-text="review scan":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## Next steps

Now that you have registered your source, follow the below guides to learn more about Purview and your data.

- [Data insights in Azure Purview](concept-insights.md)
- [Lineage in Azure Purview](catalog-lineage-user-guide.md)
- [Search Data Catalog](how-to-search-catalog.md)
