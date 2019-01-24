---
title: Monitor resource usage and query statistics for an search service - Azure Search
description: Get query activity metrics, resource consumption and other system data from an Azure Search service.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: heidist
ms.custom: seodec2018
---
# Monitor an Azure Search service in Azure portal

In the Overview page of your Azure Search service, you can view system data about resource usage, plus query metrics like Queries Per Second (QPS), query latency, and percentage of requests that were throttled. Additionally, you can use the portal to leverage a range of monitoring capabilities in the Azure platform for deeper data collection. 

This article identifies and compares available options for logging Azure Search operations. It includes instructions for enabling logging and log storage, and how to expand on the information that is collected.

If you are filing a support ticket, there are no particular tasks or information you need to provide. Support engineers have the necessary information for investigating specific issues.  

## Metrics at a glance

**Usage** and **Monitoring** sections built into Overview visualize storage consumption and query execution metrics. This information becomes available as soon as you start using the service, with no configuration required. This page is refreshed every few minutes. If you are finalizing decisions about [which tier to use for production workloads](search-sku-tier.md), or whether to [adjust the number of active replicas and partitions](search-capacity-planning.md), these metrics can help you with those decisions by showing you how quickly resources are consumed and how well the current configuration handles the existing load.

The **Usage** tab shows you resource availability relative to current [limits](search-limits-quotas-capacity.md). The following illustration is for the free service, which is capped at 3 objects of each type and 50 MB of storage. A Basic or Standard service has higher limits, and if you increase the partition counts, maximum storage goes up proportionally.

![Usage status relative to effective limits](./media/search-monitor-usage/usage-tab.png
 "Usage status relative to effective limits")

## Queries per second (QPS) and other metrics

The **Monitoring** tab shows moving averages for metrics like search *Queries Per Second* (QPS), aggregated per minute. 
*Search latency* is the amount of time the search service needed to process search queries, aggregated per minute. *Throttled search queries percentage* (not shown) is the percentage of search queries that were throttled, also aggregated per minute.

![Queries per second activity](./media/search-monitor-usage/monitoring-tab.png "Queries per second activity")

## Activity logs

The **Activity log** collects information from Azure Resource Manager. Examples of information found in the Activity log include creating or deleting a service, updating a resource group, checking for name availability, or getting a service access key to handle a request. 

You can access the **Activity log** from the left-navigation pane, or from Notifications in the top window command bar, or from the **Diagnose and solve problems** page.

For in-service tasks like creating an index or deleting a data source, you'll see generic notifications like "Get Admin Key" for each request, but not the specific action itself. For this level of information, you must enable an add-on monitoring solution.

## Add-on monitoring solutions

Azure Search does not store any data beyond the objects it manages, which means log data has to be stored externally. You can configure any of the resources below if you want to persist log data. 

The following table compares options for storing logs and adding in-depth monitoring of service operations and query workloads through Application Insights.

| Resource | Used for |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | [Search traffic analytics](search-traffic-analytics.md). This is the only solution that captures additional information about requests, going beyond the values identified in the logging and metrics schemas below. With this approach, you copy-paste instrumentation code into your source files to route request information to Application Insights for analysis on query term inputs, queries with zero matches, and so forth. We recommend Power BI as the analytics front end to data stored in Application Insights.  |
| [Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Requests and metrics, based one the schemas below. Events are logged to a Blob container. We recommend Excel or Power BI as the analytics front end to stored data in Azure Blob storage.|
| [Event Hub](https://docs.microsoft.com/azure/event-hubs/) | Requests and metrics, based on the schemas documented in this article. Choose this as an alternative data collection service for very large logs. |

Azure search provides a monitoring [Power BI Content Pack](https://app.powerbi.com/getdata/services/azure-search) so that you can analyze log data. The content pack consists of reports configured to automatically connect to your data and provide visual insights about your search service. For more information, see the [content pack help page](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

The Blob storage option is available as Free shared service so that you can try it out at no charge for the lifetime of your Azure subscription. The next section walks you through the steps of enabling and using Azure Blob storage to collect and access log data created by Azure Search operations.

## Enable logging

Logging for indexing and query workloads is off by default and depends on add-on solutions for both logging infrastructure and external storage. By itself, the only persisted data in Azure Search is indexes, so logs must be stored elsewhere.

In this section, you'll learn how to use Blob storage to contain logged events and metrics data.

1. [Create a storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) if you don't already have one. You can place it in the same resource group as Azure Search to simplify clean up later if you want to delete all resources used in this exercise.

2. Open your search service Overview page. In the left-navigation pane, scroll down to **Monitoring** and click **Enable Monitoring**.

   ![Enable monitoring](./media/search-monitor-usage/enable-monitoring.png "Enable monitoring")

3. Choose the data you want to export: Logs, Metrics or both. You can copy it to a storage account, send it to an event hub or export it to Log Analytics.

   For archival to Blob storage, only the storage account must exist. Containers and blobs will be created when log data is exported.

   ![Configure blob storage archive](./media/search-monitor-usage/configure-blob-storage-archive.png "Configure blob storage archive")

4. Save the profile.

5. Test logging by creating or deleting objects (generates an operational log) and by submitting queries (generates metrics). 

Logging is enabled once you save the profile, containers are only created when there is an event to log or measure. It can take several minutes for the containers to appear. You can [visualize the data in Power BI](#analyze-with-power-bi) once it becomes available.

When the data is copied to a storage account, the data is formatted as JSON and placed in two containers:

* insights-logs-operationlogs: for search traffic logs
* insights-metrics-pt1m: for metrics

There is one blob, per hour, per container.

Example path: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json`

## Log schema
Blobs containing your search service traffic logs are structured as described in this section. Each blob has one root object called **records** containing an array of log objects. Each blob contains records for all the operations that took place during the same hour.

| Name | Type | Example | Notes |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Timestamp of the operation |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Your ResourceId |
| operationName |string |"Query.Search" |The name of the operation |
| operationVersion |string |"2017-11-11" |The api-version used |
| category |string |"OperationLogs" |constant |
| resultType |string |"Success" |Possible values: Success or Failure |
| resultSignature |int |200 |HTTP result code |
| durationMS |int |50 |Duration of the operation in milliseconds |
| properties |object |see the following table |Object containing operation-specific data |

**Properties schema**

| Name | Type | Example | Notes |
| --- | --- | --- | --- |
| Description |string |"GET /indexes('content')/docs" |The operation's endpoint |
| Query |string |"?search=AzureSearch&$count=true&api-version=2017-11-11" |The query parameters |
| Documents |int |42 |Number of documents processed |
| IndexName |string |"testindex" |Name of the index associated with the operation |

## Metrics schema

Metrics are captured for query requests.

| Name | Type | Example | Notes |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |your resource id |
| metricName |string |"Latency" |the name of the metric |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |the operation's timestamp |
| average |int |64 |The average value of the raw samples in the metric time interval |
| minimum |int |37 |The minimum value of the raw samples in the metric time interval |
| maximum |int |78 |The maximum value of the raw samples in the metric time interval |
| total |int |258 |The total value of the raw samples in the metric time interval |
| count |int |4 |The number of raw samples used to generate the metric |
| timegrain |string |"PT1M" |The time grain of the metric in ISO 8601 |

All metrics are reported in one-minute intervals. Every metric exposes minimum, maximum and average values per minute.

For the SearchQueriesPerSecond metric, minimum is the lowest value for search queries per second that was registered during that minute. The same applies to the maximum value. Average, is the aggregate across the entire minute.
Think about this scenario during one minute: one second of high load that is the maximum for SearchQueriesPerSecond, followed by 58 seconds of average load, and finally one second with only one query, which is the minimum.

For ThrottledSearchQueriesPercentage, minimum, maximum, average and total, all have the same value: the percentage of search queries that were throttled, from the total number of search queries during one minute.

## Analyze with Power BI

We recommend using [Power BI](https://powerbi.microsoft.com) to explore and visualize your data, particularly if you enabled [search traffic analytics](search-traffic-analytics.md). For more information, see the [content pack help page](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

Connections require the storage account name and access key, which you can get from Azure portal pages on the **Access keys** page of your storage account dashboard.

1. Install the [Power BI Content Pack](https://app.powerbi.com/getdata/services/azure-search). The content pack adds predefined charts and tables useful for analyzing the additional data captured for search traffic analytics. 

   If you are using Blob storage or another storage mechanism, and you did not add instrumentation to your code, you can skip the content pack and use built-in Power BI visualizations.

2. Open **Power BI**, click **Get Data** > **Services** > **Azure Search**.

3. Enter the name of the storage account, select **Key** for authentication, and then paste in an access key.

4. Import the data and then click **View data**.

The following screenshot shows the built-in reports and charts for analyzing search traffic analytics.

![Power BI dashboard for Azure Search](./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png "Power BI dashboard for Azure Search")

## Get sys-info APIs
Both the Azure Search REST API and the .NET SDK provide programmatic access to service metrics, index and indexer information, and document counts.

* [Get Services Statistics](/rest/api/searchservice/get-service-statistics)
* [Get Index Statistics](/rest/api/searchservice/get-index-statistics)
* [Count Documents](/rest/api/searchservice/count-documents)
* [Get Indexer Status](/rest/api/searchservice/get-indexer-status)

To enable using PowerShell or the Azure CLI, see the documentation [here](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

## Next steps

[Manage your Search service on Microsoft Azure](search-manage.md) for more information on service administration and [Performance and optimization](search-performance-optimization.md) for tuning guidance.

Learn more about creating amazing reports. See [Getting started with Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) for details.

