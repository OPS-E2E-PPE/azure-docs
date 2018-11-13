---
title: Distribute data globally with Azure Cosmos DB | Microsoft Docs
description: Learn about planet-scale geo-replication, multi-master, failover, and data recovery using global databases from Azure Cosmos DB, a globally distributed, multi-model database service.
services: cosmos-db
author: SnehaGunda
manager: kfile

ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown

---
# Global data distribution with Azure Cosmos DB

Many of today’s applications run at planet scale. These applications are always on and accessible to users all around the globe. Managing the global distribution of data that is used by these applications while providing high performance and high availability is a hard problem. Azure Cosmos DB is a globally distributed database service that is designed to provide high performance and high availability. Due to these reasons, Azure Cosmos DB is best suited for these real-time applications.

Cosmos DB is a foundational Azure service and it is available in all [Azure regions](https://azure.microsoft.com/global-infrastructure/regions/) by default. Microsoft operates Azure data centers in 54+ regions around the world and continues to expand to meet the growing needs of the customers. When you create a Cosmos DB account, you decide which region(s) it should be deployed in. Microsoft operates the Cosmos DB service 24/7, so you can focus on your applications.

You can configure your databases to be globally distributed and available in any of the 50+ Azure regions. To lower the latency, you should place the data closer to your user’s location. Choosing the required regions depends on the global reach of your application and where your users are located. Cosmos DB transparently replicates the data within your account to all the configured regions. It provides a single system image of your Cosmos database and containers by which your application can read and write locally. With Cosmos DB, you can add or remove the regions associated with your account at any time. Your application does not need to be paused or redeployed to add or remove a region. It continues to be highly available all the time because of the multi-homing capabilities that the service provides.

## Key benefits of global distribution

**Build global active-active apps**: With the multi-master capability, every region is a write region (in addition to being readable). The multi-master feature also guarantees- unlimited elastic write scalability, 99.999% read and write availability all around the world, and guaranteed reads/writes served in less than 10 milliseconds at the 99th percentile.  

By using Azure Cosmos DB's multi-homing APIs, your application is aware of the nearest region and it can send requests to that region. Nearest region is identified without any configuration changes. As you add and remove regions from your Cosmos DB account, your application does not need to be redeployed and it continues to be highly available.

**Build highly responsive apps**: Your application can be easily designed to perform near real-time reads and writes, with single-digit millisecond latencies against all the regions you have chosen for your database.  Azure Cosmos DB internally handles the data replication between regions such that the consistency level chosen for the Cosmos account is guaranteed.

Many applications will benefit from the performance enhancements that come with the ability to perform multi-region (local) writes. Some applications that require strong consistency prefer to funnel all writes to a single region. To support these application, Cosmos DB supports both single region and multi-region configurations.

**Build highly available apps**: Running a database in multiple regions increases the availability of the database. If one region is unavailable, other regions will automatically handle application requests. Azure Cosmos DB offers 99.999% read and write availability for multi-region databases.

**Business continuity during regional outages**: Azure Cosmos DB supports [automatic failover](how-to-manage-database-account.md#automatic-failover) during a regional outage. Moreover, during a regional outage, Cosmos DB continues to maintain its latency, availability, consistency, and throughput SLAs. To help ensure your entire application is highly available, Azure Cosmos DB offers manual failover API to simulate a regional outage. By using this API, you can perform regular business continuity drills.

**Global read and write scalability**: With multi-master capability, you can elastically scale read and write throughput all around the world. Multi-master capability guarantees that the throughput that your application configures on an Azure Cosmos DB database or a container is delivered across all regions and protected by [financially backed SLAs](https://aka.ms/acdbsla).

**Multiple, well-defined consistency models**: Azure Cosmos DB’s replication protocol is designed to offer five well-defined, practical, and intuitive consistency models. Each consistency model has a tradeoff between consistency and performance. These consistency models enable you to build globally distributed applications with ease.

## <a id="Next Steps"></a>Next steps

Read more about global distribution in the following articles:

* [Global distribution - under the hood](global-dist-under-the-hood.md)
* [How to configure clients for multi-homing](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [How to add/remove regions from your Cosmos account](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [How to create a custom conflict resolution policy for SQL API accounts](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)