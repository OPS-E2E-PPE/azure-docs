---
author: inward-eye
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: include
ms.date: 12/01/2022
ms.custom: references_regions
---

Create a new Azure SQL Database instance, or use an existing one, in one of the currently available regions for this preview feature. You can follow [this guide to create an Azure SQL Database instance](/azure/azure-sql/database/single-database-create-quickstart).

#### Region support
All [Microsoft Purview regions](https://azure.microsoft.com/explore/global-infrastructure/products-by-region/?products=purview) are supported.

Enforcement of Microsoft Purview policies is available only in the following regions for Azure SQL Database:

- East US
- East US2
- South Central US
- West Central US
- West US3
- Canada Central
- Brazil South
- West Europe
- North Europe
- France Central
- UK South
- South Africa North
- Central India
- East Asia
- Australia East
    
#### Configure the Azure SQL Database instance for policies from Microsoft Purview
For the logical server associated with Azure SQL Database to honor policies from Microsoft Purview, you need to configure an Azure Active Directory admin. In the Azure portal, go to the logical server that hosts the Azure SQL Database instance. On the side menu, select **Azure Active Directory**. Set an admin name to any Azure Active Directory user or group that you prefer, and then select **Save**.

![Screenshot that shows the assignment of an Active Directory admin to a logical server associated with Azure SQL Database.](../media/how-to-policies-data-owner-sql/assign-active-directory-admin-azure-sql-db.png)

Then, on the side menu, select **Identity**. Under **System assigned managed identity**, turn the status to **On**, and then select **Save**.

![Screenshot that shows the assignment of a system-assigned managed identity to a logical server associated with Azure SQL Database.](../media/how-to-policies-data-owner-sql/assign-identity-azure-sql-db.png)

You also need to enable (and verify) external policy-based authorization on the logical server associated with Azure SQL Database. You can do this in PowerShell:

```powershell
Connect-AzAccount -UseDeviceAuthentication -TenantId xxxx-xxxx-xxxx-xxxx-xxxx -SubscriptionId xxxx-xxxx-xxxx-xxxx

$server = Get-AzSqlServer -ResourceGroupName "RESOURCEGROUPNAME" -ServerName "SERVERNAME"

#Initiate the call to the REST API to set the externalPolicyBasedAuthorization property to true
Invoke-AzRestMethod -Method PUT -Path "$($server.ResourceId)/externalPolicyBasedAuthorizations/MicrosoftPurview?api-version=2021-11-01-preview" -Payload '{"properties":{"externalPolicyBasedAuthorization":true}}'

# Verify that externalPolicyBasedAuthorization is set to true
Invoke-AzRestMethod -Method GET -Path "$($server.ResourceId)/externalPolicyBasedAuthorizations/MicrosoftPurview?api-version=2021-11-01-preview"
```

In the response, `"properties":{"externalPolicyBasedAuthorization":true}` should appear under `Content`.
