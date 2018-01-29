---
title: Monitoring in Microsoft Azure | Microsoft Docs
description:  Overview of the different Microsoft services and functionality that contribute to a complete monitoring strategy for your Azure services and applications. 
author: robb
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: robb,bwren

---

# Monitoring Azure resources

Monitoring is the act of collecting and analyzing data to determine the performance, health, and availability of your business application and the resources it depends on. An effective monitoring strategy will help you understand the detailed operation of the different components of your application and to increase your uptime by proactively notifying you of critical issues so that you can resolve them before they become problems.

Azure includes multiple services that individually perform a specific role or task in the monitoring space and together deliver a comprehensive solution for collecting, analyzing and acting on telemetry from your application and the underlying Azure resources supporting them.  Understanding the tools and data that are available is the first step in developing a complete monitoring strategy for your application hosted in Azure. 

The following diagram shows a conceptual view of the different components that work together to provide monitoring of Azure resources.  Each of these is described in the following sections with links to detailed technical information.

![Monitoring overview](media/monitoring-overview/overview.png)

## Monitoring services
The following Azure services provide rich capabilities for collecting and analyzing monitoring data.  They leverage common functionality in Azure and provide powerful analytics with collected data to give you unique insights to your applications and infrastructure.  They present data within the context of particular scenarios targeted to different audiences.

### Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) allows collection of [Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [Activity Logs](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), and [Diagnostic logs](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)  that provide details on the operation and performance of Azure resources.  You can view this data with one of the explorers in the Azure portal, send it to Log Analytics for trending and detailed analysis, or create alert rules to proactively notify you of critical issues.


### Application Insights
[Application Insights](http://azure.microsoft.com/documentation/services/application-insights) allows you to monitor your live web application.  This includes collecting performance metrics both from the server and client, identifying exceptions in the application, and proactively testing it from different locations.  Administrators can track application usage and performance, while developers can track exceptions down to the actual failing code.  Application Insights provides extensive analysis tools for interacting with its data, or you use the Log Analytics query language for ad hoc and custom analysis.


### Service Map
[Service Map](../operations-management-suite/operations-management-suite-service-map.md) provides insight into your IaaS environment by analyzing virtual machines with their different processes and dependencies on other computers and external processes.  It integrates events, performance data, and management solutions in Log Analytics so that you can view this data in the context of each computer and its relation to the rest of your environment.  Service Map is similar to the [application map in Application Insights](../application-insights/app-insights-app-map.md) but focuses on the infrastructure components supporting your applications.

### Network Watcher
[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) provides scenario-based monitoring and diagnostics for different network scenarios in Azure.  It stores data in Azure metrics and diagnostics for further analysis and works with [management solutions in Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) for complete monitoring of your network resources.

### Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) plays a central role in Azure monitoring by collecting data from a variety of resources into a single repository where it can be analyzed with a powerful query language.  Application Insights and Azure Security Center store their data in the Log Analytics  data store and leverage its analytics engine.  This combined with data collected from Azure resources, management solutions, and agents installed on virtual machines allow you to form a complete picture of your entire environment. 

### Management solutions
[Management solutions](../log-analytics/log-analytics-add-solutions.md) are packaged sets of logic that provide insights for a particular application or service.  They rely on Log Analytics to store and analyze the monitoring data they collect.  Management solutions are available from Microsoft and partners that provide monitoring for various Azure and third party services. 


## Shared functionality
The following Azure tools provide critical functionality to the primary monitoring services.  They are shared by multiple services allowing you to leverage common functionality and configurations across multiple services.

### Alerts
[Azure Alerts](../monitoring-and-diagnostics/monitoring-overview-alerts.md) proactively notify you of critical conditions and potentially take corrective action.  Alert rules can leverage data from multiple sources including metrics and logs. Alert rules can share [Action Groups](../monitoring-and-diagnostics/monitoring-action-groups.md) which contain unique sets of recipients and actions in response to an alert.

Log Analytics currently has its own [alerting based on log searches](../log-analytics/log-analytics-alerts.md).  Like Azure alerts, Log Analytics alerts can notify you of an issue or start an external process to attempt corrective action, but they cannot currently use the same action groups used by Azure alerts.


### Dashboards
[Azure Dashboards](../azure-portal/azure-portal-dashboards.md) allow you to combine different kinds of data  into a single pane in the Azure portal.  For example, you could create a dashboard that combines tiles showing a graph of metrics, a table of activity logs, a usage chart from Application Insights, and the output of a log search in Log Analytics.

[Views in Log Analytics]() provide a wider range of visualizations and detail than Azure dashboards, but they can only include data stored in Log Analytics.  Since Log Analytics can collect data from a variety of sources though, you can often collect any data that you require for your view.  Management solutions will typically include custom views in Log Analytics to visualize the data they collect.

You can also export Log Analytics data to [Power BI](https://docs.microsoft.com/power-bi/) to take advantage of additional visualizations and also to make the data available to others within and outside of your organization.

### Metrics and Log Explorers
Metrics and Log data collected by Azure Monitor are accessible in the Azure portal with the [Metrics Explorer](../monitoring-and-diagnostics/monitoring-overview-metrics.md) and [Log Explorer](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).  These provide a common interface to view and analyze monitoring data either for a particular resource or for multiple resources together.  


## Monitoring data
Monitoring data is collected by a variety of sources and used by the Azure monitoring services.  There are fundamentally two kinds of monitoring data, metrics and logs.

Metrics are ideal for detecting issues.  They are numerical values generated by Azure resources that help you understand the operation and performance of the resource.  They are small and fast and can generate alerts when thresholds are surpassed in near real time.  You can view metrics for a particular resource on its page in the Azure portal or view metrics from multiple resources in Metrics Explorer.  Metrics are also available in Application Insights for a monitored application and can be sent to Log Analytics for analysis with data from other sources.


Logs are ideal for diagnosing issues.  They contain detailed information about the operation of a particular application or service.  They are larger and slower than metrics but contain valuable information that can generate alerts when certain criteria are detected.  You can view logs for a particular resource on its page in the Azure portal or view logs from multiple resources in Activity Log Explorer.  Azure logs can also be sent to Log Analytics so they can be analyzed with data collected by management solutions, agents on virtual machines, and other sources.




## Example scenarios
Following are high level examples that illustrate how you would leverage different monitoring tools in Azure for different scenarios.

### Monitoring a web application
Consider a web application deployed in Azure using App Services, Azure Storage, and a SQL database.  You could start by accessing metrics and activity logs for each of these individual resources on their pages in Azure portal.  This would include critical information such as the number of requests to the application and average response time in addition to identifying any configuration changes.

You could then go to Azure Monitor in the portal in order to view metrics and logs for the different resources together.  As you determine standard parameters for the metrics, you create alert rules to proactively notify you when, for example, average response time increases beyond a threshold.  In order to get a quick view of your application's daily performance, you create an Azure dashboard to show graphs of metrics representing critical KPIs.

To perform deeper monitoring of your application, you configure it for Application Insights.  You can now view the same metrics you were collecting before with additional data providing further insight into the operation and performance of your application.  The Application map, for example, will provide a visual representation of the components of your application with their related metrics and alerts.  You create Availability tests to proactively test your application from different regions, and then use Visual Studio to simulate user activity.  To help your developers, you enable the Profiler so you can track live requests and any exceptions down to a specific line of code.  

After some time, you decide to investigate the root cause for time periods when performance on the site has fallen below threshold.  You write a query using Log Analytics to correlate the usage and performance data collected by Application Insights with configuration and performance data across the Azure resources supporting your application.


### Monitoring virtual machines
You have a mix of Windows and Linux virtual machines running in the Azure.  You add the Azure Diagnostics extension to them in order to collect metrics which you view in the Azure portal for each machine.  You open Azure Monitor to view the metrics together and create alert rules to proactively notify you when basic metrics such processor utilization and memory cross thresholds.

To collect more details about virtual machines running a business application, you create a Log Analytics workspace and enable the VM extension on each machine.  You then add management solutions such as Agent Health and Wire Data to the workspace which automatically start collecting additional data.  You configure data collection of custom logs for your application and create views to report on its daily operation and performance.  You then create alert rules to notify you when particular error events are received.

To gain further insight into the application you add the dependency agent to the virtual machines in order to add them to Service Map.  It discovers critical processes and identifies connections between machines with other services.  After a reported outage, you use Service Map to perform forensics to identify the particular machines that experienced the problem.  You then create a query on the Log Analytics data to identify the issue in the future and create an alert rule to proactively notify you when the condition has been detected.



## Next steps
Learn more about

* [Azure Monitor in a video from Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Getting Started with Azure Monitor](monitoring-get-started.md)
* [Azure Diagnostics](../azure-diagnostics.md) if you are attempting to diagnose problems in your Cloud Service, Virtual Machine, Virtual machine scale set, or Service Fabric application.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) if you are trying to diagnostic problems in your App Service Web app.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) for analyzing collected monitoring data.
