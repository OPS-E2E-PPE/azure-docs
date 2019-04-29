---
title: Refresh Azure Analysis Services models with Azure Automation | Microsoft Docs
description: Learn how to code model refreshes by using Azure Automation.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound

---
# Refresh with Azure Automation

By using Azure Automation and PowerShell Runbooks, you can perform data-refresh operations on your Azure Analysis tabular models.  

The example in this article uses the PowerShell SqlServer modules.  For more information on these modules, see the following article:  https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps

A sample PowerShell Runbook is provided later in this article which demonstrates refreshing the Analysis Services model.  

## Authentication

All calls must be authenticated with a valid Azure Active Directory (OAuth 2) token.  The example in this article will use a Service Principal (SPN) to authenticate to Azure Analysis Services.

For more information on creating a Service Principal, see the following article: https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal

## Pre-requisites

> [!IMPORTANT]
> The following example assume that the Azure Analysis Services firewall is disabled.  If the firewall is enabled then the public IP address of the request initiator will need to be white listed in the Azure Analysis Services firewall.

Install the SqlServer modules from the gallery.

1. In your Azure Automation Account, Click **Modules**, then **Browse gallery**.

2. In the search bar, search for **SqlServer**.

    ![Search Modules](./media/analysis-services-refresh-azure-automation/1.png)

3. Select SqlServer, then click **Import**.
 
    ![Import Module](./media/analysis-services-refresh-azure-automation/2.png)

4. Click **OK**
 
Make sure you have a Service Principal (SPN) created.

Configure permissions to Azure Analysis Services.

For more information on creating a Service Principal, see the following article https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal

For more information on assigning a service principal to server administrators, see the following article https://docs.microsoft.com/azure/analysis-services/analysis-services-addservprinc-admins.

## Designing the Azure Automation Runbook

1. In the Automation Account, create a **Credentials** resource which will be used to securely store the Service Principal.

    ![Create credential](./media/analysis-services-refresh-azure-automation/6.png)

2. Enter the details for the credential.  For the **User name**, enter the **SPN ClientId**, for the **Password**, enter the **SPN Secret**.

    ![Create credential](./media/analysis-services-refresh-azure-automation/7.png)

3. Import the Automation Runbook

    ![Import Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Browse for the **Refresh-Model.ps1** file, provide a **Name**, and give the runbook a **description**.  Then click **Create**.

    ![Import Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. When the Runbook has been created, it will automatically go into edit mode.  Select **Publish**.

    ![Publish Runbook](./media/analysis-services-refresh-azure-automation/10.png)

> [!NOTE]
> The credential resource that was created previously is retrieved by the runbook by using the **Get-AutomationPSCredential** command.  This command is then passed to the **Invoke-ProcessASADatabase** PowerShell command to perform the authentication to Azure Analysis Services.

6. Test the runbook by clicking **Start**.

    ![Start the Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Fill out the **DATABASENAME**, **ANALYSISSERVER** and **REFRESHTYPE** parameters and click **OK**.  The **WEBHOOKDATA** parameter is not required when the Runbook is run manually.

    ![Start the Runbook](./media/analysis-services-refresh-azure-automation/12.png)

If the Runbook was executed successfully, you will receive an output like the following:

![Successful Run](./media/analysis-services-refresh-azure-automation/13.png)

## Using a self-contained Azure Automation Runbook

The Runbook can be configured to trigger the Azure Analysis Services model refresh on a scheduled basis.

This can be configured as follows:

1. In the Automation Runbook, click **Schedules**, then **Add a Schedule**.
 
    ![Create schedule](./media/analysis-services-refresh-azure-automation/14.png)

2. Click **Schedule** > **Create a new schedule** and fill in the details.

    ![Configure schedule](./media/analysis-services-refresh-azure-automation/15.png)

3. Click **Create**.

4. Fill in the parameters for the schedule.  These will be used each time the Runbook triggers.  The **WEBHOOKDATA** parameter should be left blank when running via a schedule.

    ![Configure parameters](./media/analysis-services-refresh-azure-automation/16.png)

5. Click **OK**.

## Consume with Data Factory

To consume the runbook using Azure Data Factory, first create a **Webhook** for the runbook.  The **Webhook** will provide a URL which can be called via an Azure Data Factory web activity.

> [!IMPORTANT]
> To create a **Webhook**, the status of the Runbook must be **Published**.

1. In your Automation Runbook, click **Webhooks**, then Add **Webhook**.

   ![Add Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Give the Webhook a name and an expiry.  The name only identifies the Webhook inside the Automation Runbook, it doesn't form part of the URL.

   >[!CAUTION]
   >Ensure to copy the URL before closing the wizard as you cannot get it back once closed.
    
   ![Configure Webhook](./media/analysis-services-refresh-azure-automation/18.png)

The parameters for the webhook can remain blank.  When configuring the Azure Data Factory web activity, the parameters can be passed into the body of the web call.

3. In Data Factory, configure a **web activity**

Example

   ![Example Web Activity](./media/analysis-services-refresh-azure-automation/19.png)

The **URL** is the URL created from the Webhook.

The **body** is a JSON document which should contain the following properties:


|Property  |Value  |
|---------|---------|
|**AnalysisServicesDatabase**     |The name of the Azure Analysis Services database <br/> Example: AdventureWorksDB         |
|**AnalysisServicesServer**     |The Azure Analysis Services server name. <br/> Example: https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |The type of refresh to perform. <br/> Example: Full         |

Example JSON body:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

These parameters are defined in the runbook PowerShell script.  When the web activity is executed, the JSON payload passed is WEBHOOKDATA.

This is deserialized and stored as PowerShell parameters, which are then used by the Invoke-ProcesASDatabase PowerShell command.

![Deserialized Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## Using a Hybrid Worker with Azure Analysis Services

An Azure Virtual Machine with a static public IP address can be used as an Azure Automation Hybrid Worker.  This public IP address can then be added to the Azure Analysis Services firewall.

> [!IMPORTANT]
> Ensure that the Virtual Machine public IP address is configured as static.
>
>To learn more about configuring Azure Automation Hybrid Workers see the following article https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker#installing-a-hybrid-runbook-worker

Once a Hybrid Worker is configured, create a Webhook as described in the section [Consume with Data Factory](#consume-with-data-factory).  The only difference here is to select the **Run on** > **Hybrid Worker** option when configuring the Webhook.

Example webhook using Hybrid Worker:

![Example Hybrid Worker Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## Sample PowerShell Runbook

The following code snippet is an example of how to perform the Azure Analysis Services model refresh using a PowerShell Runbook.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## Next steps

[Samples](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)