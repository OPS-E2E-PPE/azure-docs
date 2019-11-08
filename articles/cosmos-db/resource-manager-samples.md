---
title: Azure Resource Manager templates for Azure Cosmos DB
description: Use Azure Resource Manager templates to create and configure Azure Cosmos DB. 
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: thvankra
---

# Azure Resource Manager templates for Azure Cosmos DB

The following tables include links to Azure Resource Manager templates for Azure Cosmos DB:

## SQL (Core) API

|**Template**|**Description**|
|---|---|
|[Create an Azure Cosmos account, database, container](manage-sql-with-resource-manager.md#create-resource) | This template creates a SQL (Core) API account in two regions with two containers with shared database throughput and a container with dedicated throughput. Throughput can be updated by resubmitting the template with updated throughput property value. |
|[Create an Azure Cosmos account, database and container with a stored procedure, trigger and UDF](manage-sql-with-resource-manager.md#create-sproc) | This template creates a SQL (Core) API account in two regions with a stored procedure, trigger and UDF for a container. |

## MongoDB API

|**Template**|**Description**|
|---| ---|
|[Create an Azure Cosmos account, database, collection](manage-mongodb-with-resource-manager.md#create-resource) | This template creates an account using Azure Cosmos DB API for MongoDB in two regions with multi-master enabled. The Azure Cosmos account will have two containers that share database-level throughput. |
|[Update throughput (RU/s) for a database](manage-mongodb-with-resource-manager.md#database-ru-update) | This template updates the throughput for a database in a MongoDB API account. |
|[Update throughput (RU/s) for a collection](manage-mongodb-with-resource-manager.md#collection-ru-update) | This template updates the throughput for a container in a MongoDB API account. |

## Cassandra API

|**Template**|**Description**|
|---| ---|
|[Create an Azure Cosmos account, keyspace, table](manage-cassandra-with-resource-manager.md#create-resource) | This template creates a Cassandra API account in two regions with multi-master enabled. The Azure Cosmos account will have two tables that share keyspace-level throughput. |

## Gremlin API

|**Template**|**Description**|
|---| ---|
|[Create an Azure Cosmos account, database, graph](manage-gremlin-with-resource-manager.md#create-resource) | This template creates a Gremlin API account in two regions with multi-master enabled. The Azure Cosmos account will have two graphs that share database-level throughput. |
|[Update throughput (RU/s) for a database](manage-gremlin-with-resource-manager.md#database-ru-update) | This template updates the throughput for a database in a Gremlin API account. |
|[Update throughput (RU/s) for a graph](manage-gremlin-with-resource-manager.md#graph-ru-update) | This template updates the throughput for a graph in a Gremlin API account. |

## Table API

|**Template**|**Description**|
|---| ---|
|[Create an Azure Cosmos account, table](manage-table-with-resource-manager.md#create-resource) | This template  creates a Table API account in two regions with multi-master enabled. The Azure Cosmos account will have a single table. |
|[Update throughput (RU/s) for a table](manage-table-with-resource-manager.md#table-ru-update) | This template updates the throughput for a table in a Table API account. |

> [!TIP]
> To enable shared throughput when using Table API, enable account-level throughput in the Azure Portal.

See [ARM reference for Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) page for the reference documentation.
