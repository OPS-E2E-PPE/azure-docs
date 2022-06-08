---
title: Configure ContainerLogv2 schema (preview) for Container Insights
description: Switch your ContainerLog table to the ContainerLogv2 schema
author: aul
ms.author: bwren
ms.subservice: logs
ms.custom: event-tier1-build-2022
ms.topic: conceptual
ms.date: 05/11/2022
ms.reviewer: aul
---

# Enable ContainerLogV2 schema (preview)
Azure Monitor Container Insights is now in Public Preview of new schema for container logs called ContainerLogV2. As part of this schema, there are new fields to make common queries to view AKS (Azure Kubernetes Service) and Azure Arc enabled Kubernetes data. In addition, this schema is compatible as a part of [Basic Logs](../logs/basic-logs-configure.md), which offer a low cost alternative to standard analytics logs.

> [!NOTE]
> The ContainerLogv2 schema is currently a preview feature, Container Insights does not yet support the "View in Analytics" option, however the data is still available when queried directly from the [Log Analytics](./container-insights-log-query.md) interface.

>[!NOTE]
>The new fields are:
>* ContainerName
>* PodName
>* PodNamespace

## ContainerLogV2 schema
```kusto
 Computer: string,
 ContainerId: string,
 ContainerName: string,
 PodName: string,
 PodNamespace: string,
 LogMessage: dynamic,
 LogSource: string,
 TimeGenerated: datetime
```
## Enable ContainerLogV2 schema
1. Customers can enable ContainerLogV2 schema at cluster level. 
2. To enable ContainerLogV2 schema, configure the cluster's configmap, Learn more about [configmap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) in Kubernetes documentation & [Azure Monitor configmap](./container-insights-agent-config.md#configmap-file-settings-overview).
3. Follow the instructions accordingly when configuring an existing ConfigMap or using a new one.

### Configuring an existing ConfigMap
If your ConfigMap doesn't yet have the "[log_collection_settings.schema]" field, you'll need to append the following section in your existing ConfigMap yaml file:

```yaml
[log_collection_settings.schema]
          # In the absence of this configmap, default value for containerlog_schema_version is "v1"
          # Supported values for this setting are "v1","v2"
          # See documentation at https://aka.ms/ContainerLogv2 for benefits of v2 schema over v1 schema before opting for "v2" schema
          containerlog_schema_version = "v2"
```

### Configuring a new ConfigMap
1. Download the new ConfigMap from [here](https://aka.ms/container-azm-ms-agentconfig). For the newly downloaded configmapdefault, the value for containerlog_schema_version is "v1"
1. Update the "containerlog_schema_version = "v2""

```yaml
[log_collection_settings.schema]
          # In the absence of this configmap, default value for containerlog_schema_version is "v1"
          # Supported values for this setting are "v1","v2"
          # See documentation at https://aka.ms/ContainerLogv2 for benefits of v2 schema over v1 schema before opting for "v2" schema
          containerlog_schema_version = "v2"
```

1. Once you have finished configuring the configmap, run the following kubectl command: kubectl apply -f `<configname>`

>[!TIP]
>Example: kubectl apply -f container-azm-ms-agentconfig.yaml.

>[!NOTE]
>* The configuration change can take a few minutes to complete before taking effect, all omsagent pods in the cluster will restart. 
>* The restart is a rolling restart for all omsagent pods, it will not restart all of them at the same time.

## Next steps
* Configure [Basic Logs](../logs/basic-logs-configure.md) for ContainerLogv2
