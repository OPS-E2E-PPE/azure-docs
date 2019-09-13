---
title: Azure SQL Database Release Notes| Microsoft Docs
description: Learn about the new features and improvements in the Azure SQL Database service and in the Azure SQL Database documentation
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: 
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
---
# SQL Database release notes

This article lists SQL Database features that are currently in public preview. For SQL Database updates and improvements, see [SQL Database service updates](https://azure.microsoft.com/updates/?product=sql-database). For updates and improvements to other Azure services, see [Service updates](https://azure.microsoft.com/updates).

## Features in public preview

### [Single database](#tab/single-database)

| Feature | Details |
| ---| --- |
| Accelerated database recovery with single databases and elastic pools | For information, see [Accelerated Database Recovery](sql-database-accelerated-database-recovery.md).|
|Approximate Count Distinct|For information, see [Approximate Count Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Batch Mode on Rowstore (under compatibility level 150)|For information, see [Batch Mode on Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Data discovery & classification  |For information, see [Azure SQL Database and SQL Data Warehouse data discovery & classification](sql-database-data-discovery-and-classification.md).|
| Elastic database jobs | For information, see [Create, configure, and manage elastic jobs](elastic-jobs-overview.md). |
| Elastic queries | For information, see [Elastic query overview](sql-database-elastic-query-overview.md). |
| Elastic transactions | [Distributed transactions across cloud databases](sql-database-elastic-transactions-overview.md). |
|Memory Grant Feedback (Row Mode) (under compatibility level 150)|For information, see [Memory Grant Feedback (Row Mode)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Query editor in the Azure portal |For information, see [Use the Azure portal's SQL query editor to connect and query data](sql-database-connect-query-portal.md).|
| R services / machine learning with single databases and elastic pools |For information, see [Machine Learning Services in Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Recreate dropped databases with managed instances |For information, see [Re-create dropped databases in Azure SQL Managed Instance](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| Replication with managed instances |For information, see [Configure replication in an Azure SQL Database managed instance database](replication-with-sql-database-managed-instance.md).|
| Serverless compute tier | For information, see [SQL Database serverless (preview)](sql-database-serverless.md).|
|SQL Analytics|For information, see [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Table Variable Deferred Compilation (under compatibility level 150)|For information, see [Table Variable Deferred Compilation](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| Threat detection with managed instances |For information, see [Configure threat detection in Azure SQL Database managed instance](sql-database-managed-instance-threat-detection.md).|
| Transparent data encryption (TDE) with Bring Your Own Key (BYOK) with managed instances |For information, see [Azure SQL Transparent Data Encryption with customer-managed keys in Azure Key Vault: Bring Your Own Key support](transparent-data-encryption-byok-azure-sql.md).|
| &nbsp; |

### [Managed Instance](#tab/managed-instance)

| Feature | Details |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Instance pools</a> | A convenient and cost-efficient way to migrate smaller SQL instances to the cloud. |
| <a href="https://aka.ms/managed-instance-tde-byok">Bring your own encryption keys</a> | Migrate database from on-premises that already have enabled Transparent Data Encryption (TDE) with existing TDE keys. |
| <a href="https://aka.ms/managed-instance-failover-groups">Geo-distributed failover groups</a> | Keep a copy of the instance in another region and ensure that your data will be available even in the regional disaster scenario. |
| <a href="https://aka.ms/managed-instance-aadlogins">Instance-level Azure AD server principals (logins)</a> | Create server-level logins using <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a> statement. |
| [Transactional Replication](sql-database-managed-instance-transactional-replication.md) | Replicate the changes from your tables into other databases placed on Managed Instances, Single Databases, or SQL Server instances, or update your tables when some rows are changed in other Managed Instances or SQL Server instance. |
| &nbsp; |

---

## Updates

For a list of SQL Database updates and improvements, see [SQL Database service updates](https://azure.microsoft.com/updates/?product=sql-database).

For updates and improvements to all Azure services, see [Service updates](https://azure.microsoft.com/updates).

## Contribute to content

To contribute to the Azure SQL Database documentation, see the [Docs Contributor Guide](https://docs.microsoft.com/contribute/).
