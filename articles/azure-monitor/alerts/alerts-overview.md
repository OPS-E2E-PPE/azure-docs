---
title: Overview of Azure Monitor Alerts
description: Learn about Azure Monitor alerts, alert rules, action processing rules, and action groups, and how they work together to monitor your system.
author: AbbyMSFT
ms.author: abbyweisberg
ms.topic: overview 
ms.date: 07/19/2022
ms.custom: template-overview 
ms.reviewer: harelbr    
---
# What are Azure Monitor Alerts?

Alerts help you detect and address issues before users notice them by proactively notifying you when Azure Monitor data indicates that there may be a problem with your infrastructure or application.

You can alert on any metric or log data source in the Azure Monitor data platform.

This diagram shows you how alerts work:

:::image type="content" source="media/alerts-overview/alerts.png"  alt-text="Graphic explaining Azure Monitor alerts." lightbox="media/alerts-overview/alerts.png":::

An **alert rule** monitors your telemetry and captures a signal that indicates that something is happening on the specified resource. The alert rule captures the signal and checks to see if the signal meets the criteria of the condition. If the conditions are met, an alert is triggered, which initiates the associated action group and updates the state of the alert. 
 
An alert rule combines:
 - The resource(s) to be monitored
 - The signal or telemetry from the resource
 - Conditions

If you're monitoring more than one resource, the condition is evaluated separately for each of the resources and alerts are fired for each resource separately.

Once an alert is triggered, the alert is made up of:
 - **Alert processing rules** allow you to apply processing on fired alerts. Alert processing rules modify the fired alerts as they're being fired. You can use alert processing rules to add or suppress action groups, apply filters or have the rule processed on a pre-defined schedule.
 - **Action groups** can trigger notifications or an automated workflow to let users know that an alert has been triggered. Action groups can include:
     - Notification methods such as email, SMS, and push notifications.
     - Automation Runbooks
     - Azure functions
     - ITSM incidents
     - Logic Apps
     - Secure webhooks
     - Webhooks
     - Event hubs
- **Alert conditions** are set by the system. When an alert fires, the alert’s monitor condition is set to ‘fired’, and when the underlying condition that caused the alert to fire clears, the monitor condition is set to ‘resolved’.
- The **user response** is set by the user and doesn’t change until the user changes it. 

You can see all alert instances in all your Azure resources generated in the last 30 days on the **[Alerts page](alerts-page.md)** in the Azure portal. 

## Types of alerts

This table provides a brief description of each alert type. 
See [this article](alerts-types.md) for detailed information about each alert type and how to choose which alert type best suits your needs.

|Alert type|Description|
|:---------|:---------|
|[Metric alerts](alerts-types.md#metric-alerts)|Metric alerts evaluate resource metrics at regular intervals. Metrics can be platform metrics, custom metrics, logs from Azure Monitor converted to metrics or Application Insights metrics. Metric alerts have several additional features, such as the ability to apply multiple conditions and dynamic thresholds.|
|[Log alerts](alerts-types.md#log-alerts)|Log alerts allow users to use a Log Analytics query to evaluate resource logs at a predefined frequency.|
|[Activity log alerts](alerts-types.md#activity-log-alerts)|Activity log alerts are triggered when a new activity log event occurs that matches defined conditions. **Resource Health** alerts and **Service Health** alerts are activity log alerts that report on your service and resource health.|
|[Smart detection alerts](alerts-types.md#smart-detection-alerts)|Smart detection on an Application Insights resource automatically warns you of potential performance problems and failure anomalies in your web application. You can migrate smart detection on your Application Insights resource to create alert rules for the different smart detection modules.|
|[Prometheus alerts (preview)](alerts-types.md#prometheus-alerts-preview)|Prometheus alerts are used for alerting on performance and health of Kubernetes clusters (including AKS). The alert rules are based on PromQL, which is an open source query language.|

## Out-of-the-box alert rules (preview)

If you don't have alert rules defined for the selected resource, you can [enable recommended out-of-the-box alert rules in the Azure portal](alerts-manage-alert-rules.md#enable-recommended-alert-rules-in-the-azure-portal-preview).

> [!NOTE]
> The alert rule recommendations feature is currently in preview and is only enabled for:
> - Virtual machines
> - AKS resources
> - Log Analytics workspaces

## Azure role-based access control (Azure RBAC) for alerts

You can only access, create, or manage alerts for resources for which you have permissions.

To create an alert rule, you need to have:
 - Read permission on the target resource of the alert rule
 - Write permission on the resource group in which the alert rule is created (if you’re creating the alert rule from the Azure portal, the alert rule is created by default in the same resource group in which the target resource resides)
 - Read permission on any action group associated with the alert rule (if applicable)

These built-in Azure roles, supported at all Azure Resource Manager scopes, have permissions to and access alerts information and create alert rules:
 - **Monitoring contributor**: can create alerts and use resources within their scope 
 - **Monitoring reader**: can view alerts and read resources within their scope

If the target action group or rule location is in a different scope than the two built-in roles, you need to create a user with the appropriate permissions. 

## Alerts and State

You can configure whether log or metric alerts are stateful or stateless. Activity log alerts are stateless. 
- Stateless alerts fire each time the condition is met, even if fired previously.

    The frequency of notifications for stateless metric alerts differs based on the alert rule's configured frequency:
    - **Alert frequency of less than 5 minutes**: While the condition continues to be met, a notification is sent somewhere between one and six minutes.
    - **Alert frequency of more than 5 minutes**: While the condition continues to be met, a notification is sent between the configured frequency and double the frequency. For example, for an alert rule with a frequency of 15 minutes, a notification is sent somewhere between 15 to 30 minutes.

- Stateful alerts fire when the condition is met and then don't fire again or trigger any more actions until the conditions are resolved.  
For stateful alerts, the alert is considered resolved when:

|Alert type  |The alert is resolved when |
|---------|---------|
|Metric alerts|The alert condition isn't met for three consecutive checks.|
|Log alerts| A log alert is considered resolved when the condition isn't met for a specific time range. The time range differs based on the frequency of the alert:<ul> <li>**1 minute**: The alert condition isn't met for 10 minutes.</li> <li>**5-15 minutes**: The alert condition isn't met for three frequency periods.</li> <li>**15 minutes - 11 hours**: The alert condition isn't met for two frequency periods.</li> <li>**11 to 12 hours**: The alert condition isn't met for one frequency period.</li></ul>|

When an alert is considered resolved, the alert rule sends out a resolved notification using webhooks or email, and the monitor state in the Azure portal is set to resolved.

## Pricing
See the [Azure Monitor pricing page](https://azure.microsoft.com/pricing/details/monitor/) for information about pricing.

## Next steps

- [See your alert instances](./alerts-page.md)
- [Create a new alert rule](alerts-log.md)
- [Learn about action groups](../alerts/action-groups.md)
- [Learn about alert processing rules](alerts-action-rules.md)
- [Manage your alerts programmatically](alerts-manage-alert-instances.md#manage-your-alerts-programmatically)
