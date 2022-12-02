---
author: MikeRayMSFT
ms.author: mikeray
ms.service: azure-arc
ms.topic: include
ms.date: 11/28/2022
---

<!---

At this time, a test or preview build is not available for the next release.

-->

|Component|Value|
|-----------|-----------|
|Container images registry/repository |`mcr.microsoft.com/arcdata/test`|
|Container images tag |`v1.14.0_2022-12-13`|
|CRD names and version|`datacontrollers.arcdata.microsoft.com`: v1beta1, v1 through v6<br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1, v2<br/>`kafkas.arcdata.microsoft.com`: v1beta1, v1beta2<br/>`monitors.arcdata.microsoft.com`: v1beta1, v1, v2<br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1 through v7<br/>`postgresqls.arcdata.microsoft.com`: v1beta1, v1beta2, v1beta3<br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1, v1<br/>`failovergroups.sql.arcdata.microsoft.com`: v1beta1, v1beta2, v1 through v2<br/>`activedirectoryconnectors.arcdata.microsoft.com`: v1beta1, v1beta2, v1<br/>`sqlmanagedinstancereprovisionreplicatask.tasks.sql.arcdata.microsoft.com`: v1beta1<br/>`telemetrycollectors.arcdata.microsoft.com`: v1beta1, v1beta2, v1beta3 *use to be otelcollectors*<br/>`telemetryrouters.arcdata.microsoft.com`: v1beta1, v1beta2<br/>|
|Azure Resource Manager (ARM) API version|2022-06-15-preview|
|`arcdata` Azure CLI extension version|1.4.9 ([Download](https://aka.ms/az-cli-arcdata-ext))|
|Arc-enabled Kubernetes helm chart extension version|1.14.0|
|Azure Arc Extension for Azure Data Studio<br/>`arc`<br/>`azcli`|*No Changes*<br/>1.7.0 ([Download](https://aka.ms/ads-arcdata-ext))</br>1.7.0 ([Download](https://aka.ms/ads-azcli-ext))|

<!---
New for this release:

- Azure Arc data controller
  - Feature description
- Arc-enabled SQL managed instance
  - Feature description

- Arc-enabled PostgreSQL server
  - Feature description

- `arcdata` Azure CLI extension
  - Feature description
--->