---
title: Create Azure-SSIS integration runtime in Azure Data Factory | Microsoft Docs
description: Learn how to create an Azure-SSIS integration runtime in Azure Data Factory so you can deploy and run SSIS packages in Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
---

# Create the Azure-SSIS integration runtime in Azure Data Factory
This article provides steps for provisioning an Azure-SSIS integration runtime in Azure Data Factory. Then, you can use SQL Server Data Tools (SSDT) or SQL Server Management Studio (SSMS) to deploy and run SQL Server Integration Services (SSIS) packages in this runtime in Azure.

The tutorial [Tutorial: deploy SQL Server Integration Services packages (SSIS) to Azure](tutorial-create-azure-ssis-runtime-portal.md) shows you how to create an Azure-SSIS Integration Runtime (IR) by using Azure SQL Database to host the SSIS Catalog. This article expands on the tutorial and shows you how to do the following things: 

- Optionally use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview) as the database server to host your SSIS catalog (SSISDB database). As a prerequisite, you will need to join your Azure-SSIS IR to a virtual network and configure virtual network permissions and settings as necessary, see [Join Azure-SSIS IR to a virtual network](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  
- Optionally use Azure Active Directory (AAD) authentication with your Azure Data Factory Managed Service Identity (MSI) for Azure-SSIS IR to connect to the database server. As a prerequisite, you will need to add your Data Factory MSI into an AAD group with access permissions to the database server, see [Enable AAD authentication for Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

> [!NOTE]
> This article applies to version 2 of Data Factory, which is currently in preview. If you are using version 1 of the Data Factory service, which is generally available (GA), see [documentation for Data Factory version 1](v1/data-factory-introduction.md).


## Overview
This article shows different ways of provisioning an Azure-SSIS IR:

- [Azure portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager template](#azure-resource-manager-template)

When you create an Azure-SSIS IR, Data Factory connects to your Azure SQL Database to prepare the SSIS Catalog database (SSISDB). The script also configures permissions and settings for your virtual network, if specified, and joins the new instance of Azure-SSIS integration runtime to the virtual network.

When you provision an instance of Azure-SSIS IR, the Azure Feature Pack for SSIS and the Access Redistributable are also installed. These components provide connectivity to Excel and Access files and to various Azure data sources, in addition to the data sources supported by the built-in components. You can also install additional components. For more info, see [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## Prerequisites

- **Azure subscription**. If you don't have a subscription, you can create a [free trial](http://azure.microsoft.com/pricing/free-trial/) account.
- **Azure SQL Database server/Managed Instance (Preview)**. If you don't already have a database server, create one in the Azure portal before you get started. This server hosts the SSIS Catalog database (SSISDB). We recommend that you create the database server in the same Azure region as the integration runtime. This configuration lets the integration runtime write execution logs to SSISDB without crossing Azure regions. Based on the selected database server, SSISDB can be created on your behalf as a standalone database, part of an Elastic Pool, or in a Managed Instance (Preview) and accessible in public network or by joining a virtual network. For a list of supported pricing tiers for Azure SQL Database, see [SQL Database resource limits](../sql-database/sql-database-resource-limits.md).

    Confirm that your Azure SQL Database server/Managed Instance (Preview) does not have an SSIS Catalog (SSIDB database). The provisioning of Azure-SSIS IR does not support using an existing SSIS Catalog.
- **Classic/Azure Resource Manager virtual network (optional)**. You must have an Azure virtual network if at least one of the following conditions is true:
    - You are hosting the SSIS Catalog database in Azure SQL Database with virtual network service endpoints/Managed Instance (Preview) that is inside a virtual network.
    - You want to connect to on-premises data stores from SSIS packages running on an Azure-SSIS integration runtime.
- **Azure PowerShell**. Follow the instructions in [How to install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps), if you use PowerShell to run a script to provision Azure-SSIS integration runtime that runs SSIS packages in the cloud. 

> [!NOTE]
> - You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
> - You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.

## Azure portal
In this section, you use the Azure portal, specifically the Data Factory UI, to create an Azure-SSIS IR. 

### Create a data factory

1. Launch **Microsoft Edge** or **Google Chrome** web browser. Currently, Data Factory UI is supported only in Microsoft Edge and Google Chrome web browsers.
2. Log in to the [Azure portal](https://portal.azure.com/).    
3. Click **New** on the left menu, click **Data + Analytics**, and click **Data Factory**. 
   
   ![New->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. In the **New data factory** page, enter **MyAzureSsisDataFactory** for the **name**. 
      
     ![New data factory page](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   The name of the Azure data factory must be **globally unique**. If you receive the following error, change the name of the data factory (for example, yournameMyAzureSsisDataFactory) and try creating again. See [Data Factory - Naming Rules](naming-rules.md) article for naming rules for Data Factory artifacts.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`

3. Select your Azure **subscription** in which you want to create the data factory. 
4. For the **Resource Group**, do one of the following steps:
     
      - Select **Use existing**, and select an existing resource group from the drop-down list. 
      - Select **Create new**, and enter the name of a resource group.   
         
      To learn about resource groups, see [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md).  
4. Select **V2 (Preview)** for the **version**.
5. Select the **location** for the data factory. Only locations that are supported for creation of data factories are shown in the list.
6. Select **Pin to dashboard**.     
7. Click **Create**.
8. On the dashboard, you see the following tile with status: **Deploying data factory**. 

	![deploying data factory tile](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. After the creation is complete, you see the **Data Factory** page as shown in the image.
   
   ![Data factory home page](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Click **Author & Monitor** to launch the Data Factory User Interface (UI) in a separate tab. 

### Provision an Azure SSIS integration runtime

1. In the get started page, click **Configure SSIS Integration Runtime** tile. 

   ![Configure SSIS Integration Runtime tile](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. On the **General Settings** page of **Integration Runtime Setup**, complete the following steps: 

    ![General settings](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. For **Name**, enter the name of your integration runtime.
    
    b. For **Description**, enter the description of your integration runtime.
    
    c. For **Location**, select the location of your integration runtime. Only supported locations are displayed. We recommend that you select the same location of your database server to host SSISDB.
    
    d. For **Node Size**, select the size of node in your integration runtime cluster. Only supported node sizes are displayed. Select a large node size (scale up), if you want to run many compute/memory –intensive packages.
    
    e. For **Node Number**, select the number of nodes in your integration runtime cluster. Only supported node numbers are displayed. Select a large cluster with many nodes (scale out), if you want to run many packages in parallel.
     
    f. For **Edition/License**, select SQL Server edition/license for your integration runtime: Standard or Enterprise. Select Enterprise, if you want to use advanced/premium features on your integration runtime. 
    
    g. For **Save Money**, select Azure Hybrid Benefit (AHB) option for your integration runtime: Yes or No. Select Yes, if you want to bring your own SQL Server license with Software Assurance to benefit from cost savings with hybrid use.
    
    h. Click **Next**. 
3. On the **SQL Settings** page, complete the following steps: 

    ![SQL settings](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. For **Subscription**, select the Azure subscription that has your database server to host SSISDB. 
    
    b. For **Location**, select the location of your database server to host SSISDB. We recommend that you select the same location of your integration runtime.
    
    c. For **Catalog Database Server Endpoint**, select the endpoint of your database server to host SSISDB. Based on the selected database server, SSISDB can be created on your behalf as a standalone database, part of an Elastic Pool, or in a Managed Instance (Preview) and accessible in public network or by joining a virtual network.
	
    d. On **Use AAD authentication...** checkbox, select the authentication method for your database server to host SSISDB: SQL or Azure Active Directory (AAD) with your Azure Data Factory Managed Service Identity (MSI). If you check it, you need to add your Data Factory MSI into an AAD group with access permissions to the database server, see [Enable AAD authentication for Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    
    e. For **Admin Username**, enter SQL authentication username for your database server to host SSISDB.
    
    f. For **Admin Password**, enter SQL authentication password for your database server to host SSISDB.  
    
    g. For **Catalog Database Service Tier**, select the service tier for your database server to host SSISDB: Basic/Standard/Premium tier or Elastic Pool name.
    
    h. Click **Test Connection** and if successful, click **Next**. 
4.  On the **Advanced Settings** page, complete the following steps:   

    ![Advanced settings](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
    
    a. For **Maximum Parallel Executions Per Node**, select the maximum number of packages to execute concurrently per node in your integration runtime cluster. Only supported package numbers are displayed. Select a low number, if you want to use more than one core to run a single large/heavy-weight package that is compute/memory -intensive. Select a high number, if you want to run one or more small/light-weight packages in a single core. 
    
    b. For **Custom Setup Container SAS URI**, optionally enter Shared Access Signature (SAS) Uniform Resource Identifier (URI) of your Azure Storage Blob container where your setup script and its associated files are stored, see [Custom setup for Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 
5. On **Select a virtual network...** checkbox, select whether you want to join your integration runtime to a virtual network. Check it if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview) to host SSISDB or require access to on-premises data; that is, you have on-premises data sources/destinations in your SSIS packages, see [Join Azure-SSIS IR to a virtual network](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). If you check it, complete the following steps:


    ![Advanced settings with virtual network](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    a. For **Subscription**, select the Azure subscription that has your virtual network. 
    
    b. For **Location**, the same location of your integration runtime is selected.
    
    b. For **Type**, select the type of your virtual network: Classic or Azure Resource Manager. We recommend that you select Azure Resource Manager virtual network, since Classic virtual network will be deprecated soon.
    
    c. For **VNet Name**, select the name of your virtual network. This virtual network should be the same virtual network used for Azure SQL Database with virtual network service endpoints/Managed Instance (Preview) to host SSISDB and or the one connected to your on-premises network.

    d. For **Subnet Name**, select the name of subnet for your virtual network. This virtual network should be a different subnet than the one used for Managed Instance (Preview) to host SSISDB.
6. Click **VNet Validation** and if successful, click **Finish** to start the creation of your Azure-SSIS integration runtime. 

    > [!IMPORTANT]
    > - This process takes approximately 20 to minutes to complete
    > - The Data Factory service connects to your Azure SQL Database to prepare the SSIS Catalog database (SSISDB). The script also configures permissions and settings for your virtual network, if specified, and joins the new instance of Azure-SSIS integration runtime to the virtual network.
7. In the **Connections** window, switch to **Integration Runtimes** if needed. Click **Refresh** to refresh the status. 

    ![Creation status](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Use the links under **Actions** column to stop/start, edit, or delete the integration runtime. Use the last link to view JSON code for the integration runtime. The edit and delete buttons are enabled only when the IR is stopped. 

    ![Azure SSIS IR - actions](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### Azure SSIS integration runtimes in the portal

1. In the Azure Data Factory UI, switch to the **Edit** tab, click **Connections**, and then switch to **Integration Runtimes** tab to view existing integration runtimes in your data factory. 
    ![View existing IRs](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Click **New** to create a new Azure-SSIS IR. 

    ![Integration runtime via menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. To create an Azure-SSIS integration runtime, click **New** as shown in the image. 
3. In the Integration Runtime Setup window, select **Lift-and-shift existing SSIS packages to execute in Azure**, and then click **Next**.

    ![Specify the type of integration runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. See the [Provision an Azure SSIS integration runtime](#provision-an-azure-ssis-integration-runtime) section for the remaining steps to set up an Azure-SSIS IR.

## Azure PowerShell
In this section, you use the Azure PowerShell to create an Azure-SSIS IR.

### Create variables
Define variables for use in the script in this tutorial:

```powershell
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = “[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = “[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"
```

### Log in and select subscription
Add the following code the script to log in and select your Azure subscription: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### Validate the connection to database
Add the following script to validate your Azure SQL Database server endpoint. 

```powershell
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    if(![string]::IsNullOrEmpty($SSISDBPricingTier))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=“ + $SSISDBServerAdminPassword
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_"  ;
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}
```

### Configure virtual network
Add the following script to automatically configure virtual network permissions/settings for your Azure-SSIS integration runtime to join.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### Create a resource group
Create an [Azure resource group](../azure-resource-manager/resource-group-overview.md) using the [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) command. A resource group is a logical container into which Azure resources are deployed and managed as a group. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### Create a data factory
Run the following command to create a data factory.

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### Create an integration runtime
Run the following commands to create an Azure-SSIS integration runtime that runs SSIS packages in Azure. 

If you do not use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview) to host SSISDB nor require access to on-premises data, you can omit VNetId and Subnet parameters or pass empty values for them. Otherwise, you cannot omit them and must pass valid values from your virtual network configuration, see [Join Azure-SSIS IR to a virtual network](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

If you use Managed Instance (Preview) to host SSISDB, you can omit CatalogPricingTier parameter or pass an empty value for it. Otherwise, you cannot omit it and must pass a valid value from the list of supported pricing tiers for Azure SQL Database, see [SQL Database resource limits](../sql-database/sql-database-resource-limits.md). 

If you use Azure Active Directory (AAD) authentication with your Azure Data Factory Managed Service Identity (MSI) to connect to the database server, you can omit CatalogAdminCredential parameter, but you must add your Data Factory MSI into an AAD group with access permissions to the database server, see [Enable AAD authentication for Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

```powershell			    
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

You have to pass values for VnetId and Subnet parameters with Azure SQL Managed Instance (Preview) that joins a virtual network. The CatalogPricingTier parameter does not apply for Azure SQL Managed Instance (Preview). 

### Start integration runtime
Run the following command to start the Azure-SSIS integration runtime: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

This command takes from **20 to 30 minutes** to complete. 

### Full script

Here is the full script that creates an Azure-SSIS integration runtime. 

```powershell
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = “[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = “[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    if(![string]::IsNullOrEmpty($SSISDBPricingTier))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=“ + $SSISDBServerAdminPassword
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_"  ;
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}

### Configure virtual network
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime   
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## Azure Resource Manager template
In this section, you use the Azure Resource Manager template to create Azure-SSIS integration runtime. Here is a sample walkthrough: 

1. Create a JSON file with the following Azure Resource Manager template. Replace values in the angled brackets (place holders) with your own values. 

    ```json
    {
    	"contentVersion": "1.0.0.0",
    	"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    	"parameters": {},
    	"variables": {},
    	"resources": [{
    		"name": "<Specify a name for your data factory>",
    		"apiVersion": "2017-09-01-preview",
    		"type": "Microsoft.DataFactory/factories",
    		"location": "East US",
    		"properties": {},
    		"resources": [{
    			"type": "integrationruntimes",
    			"name": "<Specify a name for your Azure-SSIS IR>",
    			"dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
    			"apiVersion": "2017-09-01-preview",
    			"properties": {
    				"type": "Managed",
    				"typeProperties": {
    					"computeProperties": {
    						"location": "East US",
    						"nodeSize": "Standard_D4_v2",
    						"numberOfNodes": 1,
    						"maxParallelExecutionsPerNode": 8
    					},
    					"ssisProperties": {
    						"catalogInfo": {
    							"catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
    							"catalogAdminUserName": "<Azure SQL Database server admin username>",
    							"catalogAdminPassword": {
    								"type": "SecureString",
    								"value": "<Azure SQL Database server admin password>"
    							},
    							"catalogPricingTier": "Basic"
    						}
    					}
    				}
    			}
    		}]
    	}]
    }
    ```
    
2. To deploy the Azure Resource Manager template, run New-AzureRmResourceGroupDeployment command as shown in the following example, where ADFTutorialResourceGroup is the name of your resource group and ADFTutorialARM.json is the file that contains JSON definition for your data factory and Azure-SSIS IR. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    This command creates your data factory and Azure-SSIS IR in it, but it does not start the IR. 
3. To start your Azure-SSIS IR, run Start-AzureRmDataFactoryV2IntegrationRuntime command: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## Deploy SSIS packages
Now, use SQL Server Data Tools (SSDT) or SQL Server Management Studio (SSMS) to deploy your SSIS packages to Azure. Connect to your database server that hosts the SSIS catalog (SSISDB). The name of database server is in the format: &lt;Azure SQL Database server name&gt;.database.windows.net or &lt;Managed Instance (Preview) name.DNS prefix&gt;.database.windows.net. See [Deploy packages](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) article for instructions.

## Next steps
See the other Azure-SSIS IR topics in this documentation:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). This article provides conceptual information about integration runtimes in general including the Azure-SSIS IR. 
- [Tutorial: deploy SSIS packages to Azure](tutorial-create-azure-ssis-runtime-portal.md). This article provides step-by-step instructions to create an Azure-SSIS IR and uses an Azure SQL database to host the SSIS catalog. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). This article shows you how to retrieve information about an Azure-SSIS IR and descriptions of statuses in the returned information. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). This article shows you how to stop, start, or remove an Azure-SSIS IR. It also shows you how to scale out your Azure-SSIS IR by adding more nodes to the IR. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md). This article provides conceptual information about joining your Azure-SSIS IR to an Azure virtual network. It also provides steps to use Azure portal to configure virtual network so that Azure-SSIS IR can join the virtual network. 

