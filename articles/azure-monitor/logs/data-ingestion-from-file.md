---
title: Ingest data from a file using Data Collection Rules (DCR)
description: Learn how to ingest data from a file into a Log Analytics workspace from files using DCR.
author: osalzberg
services: azure-monitor
ms.topic: how-to
ms.date: 03/21/2022
ms.author: guywi-ms
ms.reviewer: osalzberg
# Customer intent: As a DevOps specialist, I want to ingest external data from a file into a workspace.  
---
# Collect and ingest data from a file using Data Collection Rules (DCR) (Preview)

If you want to collect,  log files from your systems using agents, you can use a Data Collection Rules.

You can define how Azure Monitor transforms and stores data ingested into your workspace by setting [Data Collection Rules (DCR)](https://docs.microsoft.com/azure/azure-monitor/essentials/data-collection-rule-overview). Using DCR lets you ingest data quickly from different log formats.

This tutorial explains how to ingest data from a file into a Log Analytics workspace using DCR.

>[!NOTE]
> * To use this method, you need to make use of MMA agent. We recommend using AMA that has more native integration with Custom Logs v2 (currently in preview)
> * Use [Custom Logs v2](https://docs.microsoft.com/azure/azure-monitor/logs/custom-logs-overview) that allows transformations and exports

## Prerequisites

To complete this tutorial, you need a [Log Analytics workspace](quick-create-workspace.md).

## Create a custom log table

>[!TIP]
> * If you already have a custom log table, you can skip this step and go and set a DCR.

Before you can send data to the workspace, you need to create the custom table that the data will be sent to:

1. Go to the **Log Analytics workspaces** menu in the Azure portal and select a workspace. 
1. Select **Custom Log** > **Add custom log**.
1. Upload a sample log file.
1. Select a record delimiter.
1. Set a collection path:
    1. Select Windows or Linux to specify which path format you're adding.
    1. Set the path on to the custom log file on your machine.  
1. Specify a name for the table. Azure Monitor automatically adds the *_CL* (custom log) suffix to the table name. 
1. Select **Create**.
## Create a Data Collection Rule (DCR)
1. Make sure the name of the stream is `Custom-{TableName}`. 

    For example:

    ```json
    {
      "properties": {
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "/subscriptions/<SubscriptionID/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<DCRName>",
              "workspaceId": "WorkspaceID",
              "name": "MyLogFolder"
            }
          ]
        },
        "dataFlows": [
          {
            "streams": [
              "Custom-DataPullerE2E_CL"
            ],
            "destinations": [
              "MyLogFolder"
            ],
            "transformKql": "source",
            "outputStream": "Custom-DataPullerE2E_CL"
          }
        ]
      },
      "location": "eastus2euap",
      "id": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.Insights/dataCollectionRules/<DCRName>",
      "name": "<DCRName>",
      "type": "Microsoft.Insights/dataCollectionRules"
    }    
    ```

1. Set the Data Collection Rule to be the default on the workspace. Use the following API command:
  
    ```json
      PUT https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>?api-version=2015-11-01-preview
      {
        "properties": {
          "defaultDataCollectionRuleResourceId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.Insights/dataCollectionRules/<DCRName>"
        },
        "location": "eastus2euap",
        "type": "Microsoft.OperationalInsights/workspaces"
      }    
      ```

1. Set the table as file-based custom log ingestion via DCR eligible, use the Custom log definition API.

    1. First run the following Get command:
   
        ```json
        GET https://management.azure.com/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/MyLogFolder/logsettings/customlogs/definitions/DataPullerE2E_CL?api-version=2020-08-01
        ```
    
    1. Copy the response and send a PUT request:

        ```JSON
        {
          "Name": "DataPullerE2E_CL",
          "Description": "custom log to test Data puller E2E",
          "Inputs": [
            {
              "Location": {
                "FileSystemLocations": {
                  "WindowsFileTypeLogPaths": [
                    "C:\\MyLogFolder\\*.txt",
                    "C:\\MyLogFolder\\MyLogFolder.txt"
                  ]
                }
              },
              "RecordDelimiter": {
                "RegexDelimiter": {
                  "Pattern": \\n,
                  "MatchIndex": 0,
                  "NumberedGroup": null
                }
              }
            }
          ],
          "Properties": [
            {
              "Name": "TimeGenerated",
              "Type": "DateTime",
              "Extraction": {
                "DateTimeExtraction": {}
              }
            }
          ],
          "SetDataCollectionRuleBased": true 
        }
        ```

        >[!Note]
        > * The `SetDataCollectionRuleBased` flag, from the last API command, enables the table as data puller.
        > * Once you switch to DCREnabled mode, data will stop flowing unless you have DCR configured.

         * To validate that the value is updated, run:
        ```json
        GET https://management.azure.com/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.operationalinsights/workspaces/MyLogFolder/datasources?api-version=2020-08-01&$filter=(kind%20eq%20'CustomLog')
        ```