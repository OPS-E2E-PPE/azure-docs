---
title: Azure Arc-enabled data services - release versions
description: A log of versions by release date for Azure Arc-enabled data services
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/04/2021
ms.topic: conceptual
# Customer intent: As a data professional, I want to understand what versions of components align with specific releases.
---

# Version log

The following table describes the various versions over time as they change:

|Date|Release name|Container images tag|CRD prefixes and versions|ARM API version|`arcdata` Azure CLI extension version|Arc enabled Kubernetes helm chart extension version|Arc Data extension for Azure Data Studio|
|---|---|---|---|---|---|---|---|
|November 2, 2021|November 2021|v1.1.0_2021-11-02|`datacontrollers`: v1beta1, v1, v2 <br/>`exporttasks.tasks`: v1beta1, v1, v2 <br/>`monitors`: v1beta1, v1, v2 <br/>`sqlmanagedinstances.sql`: v1beta1, v1, v2 <br/>`postgresqls`: v1beta1, v2beta2 <br/>`sqlmanagedinstancerestoretasks.tasks.sql`: v1beta1, v1 <br/>`dags.sql`: v1beta1, v2beta2 <br/>|2021-11-01 |1.1.0, (Nov 3),</br>1.1.1 (Nov4)|1.0.17551005 - Required if upgrade from GA <br/><br/> 1.1.17561007 - GA+1/Nov release chart|0.9.7|
|July 30, 2021|Arc enabled SQL Managed Instance General Purpose and Arc enabled SQL Server General Availability|v1.0.0_2021-07-30|`datacontrollers`: v1beta1, v1 <br/>`exporttasks.tasks`: v1beta1, v1 <br/>`monitors`: v1beta1, v1 <br/>`sqlmanagedinstances.sql`: v1beta1, v1 <br/>`postgresqls`: v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql`: v1beta1 <br/>`dags.sql`: v1beta1 <br/>|2021-08-01 (stable)|1.0|1.0.16701001, release train: stable|0.9.5|
|Aug 3, 2021|Update to Azure Arc extension for Azure Data Studio to align with July 30, General Availability|No change|No change|No change|No change|No change|0.9.6|

For the complete CRD, append `.arcdata.microsoft.com` to the prefix. 
