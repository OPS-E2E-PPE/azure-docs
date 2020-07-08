---
title: Frequently asked questions (FAQ)
titleSuffix: Azure SQL Managed Instance 
description: Azure SQL Managed Instance frequently asked questions (FAQ)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: 
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
---
# Azure SQL Managed Instance frequently asked questions (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

This article contains the most common questions about [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).

## Supported features

**Where can I find a list of features supported on SQL Managed Instance?**

For a list of supported features in SQL Managed Instance, see [Azure SQL Managed Instance features](../database/features-comparison.md).

For differences in syntax and behavior between Azure SQL Managed Instance and SQL Server, see [T-SQL differences from SQL Server](transact-sql-tsql-differences-sql-server.md).


## Tech spec & resource limits
 
**Where can I find technical characteristics and resource limits for SQL Managed Instance?**

For available hardware generation characteristics, see [Technical differences in hardware generations](resource-limits.md#hardware-generation-characteristics).
For available service tiers and their characteristics, see [Technical differences between service tiers](resource-limits.md#service-tier-characteristics).

## Known issues & bugs

**Where can I find known issues and bugs?**

For bugs and known issues, see [known issues](../database/doc-changes-updates-release-notes.md#known-issues).

## New features

**Where can I find latest features and the features in public preview?**

For new and preview features, see [Release notes](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## Deployment times 

**How much time does it take to create or update instance, or to restore a database?**

Expected time to create a managed instance or change service tier (vCores, storage) depends on several factors. Take a look at the [management operations](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations). 

## Naming conventions

**Can a managed instance have the same name as a SQL Server on-premises instance?**

Changing a managed instance name is not supported.

The default DNS zone *.database.windows.net* for a managed instance could be changed. 

To use another DNS zone instead of the default, for example, *.contoso.com*: 
- Use CliConfig to define an alias. The tool is just a registry settings wrapper, so it could be done using group policy or a script as well.
- Use *CNAME* with the *TrustServerCertificate=true* option.

## Move a database from SQL Managed Instance 

**How can I move a database from SQL Managed Instance back to SQL Server or Azure SQL Database?**

You can [export a database to BACPAC](../database/database-export.md) and then [import the BACPAC file](../database/database-import.md). This is the recommended approach if your database is smaller than 100 GB.

Transactional replication can be used if all tables in the database have primary keys.

Native `COPY_ONLY` backups taken from SQL Managed Instance cannot be restored to SQL Server because SQL Managed Instance has a higher database version compared to SQL Server.

## Migrate an instance database

**How can I migrate my instance database to Azure SQL Database?**

One option is to [export the database to a BACPAC](../database/database-export.md) and then [import the BACPAC file](../database/database-import.md). 

This is the recommended approach if your database is smaller than 100 GB. Transactional replication can be used if all tables in the database have primary keys.

## Switch hardware generation 

**Can I switch my SQL Managed Instance hardware generation between Gen 4 and Gen 5 online?**

Automated online switching between hardware generations is possible if both hardware generations are available in the region where SQL Managed Instance is provisioned. In this case, you can check the [vCore model overview page](../database/service-tiers-vcore.md), which explains how to switch between hardware generations.

This is a long-running operation, as a new managed instance will be provisioned in the background and databases automatically transferred between the old and new instances, with a quick failover at the end of the process. 

**What if both hardware generations are not supported in the same region?**

If both hardware generations are not supported in the same region, changing the hardware generation is possible but must be done manually. This requires you to provision a new instance in the region where the wanted hardware generation is available, and manually back up and restore data between the old and new instances.

**What if there are not enough IP addresses for performing update operation?**

In case there are not enough IP addresses in the subnet where your managed instance is provisioned, you will have to create a new subnet and a new managed instance inside it. We also suggest that the new subnet is created with more IP addresses allocated so future update operations will avoid similar situations. (For proper subnet size, check [how to determine the size of a VNet subnet](vnet-subnet-determine-size.md).) After the new instance is provisioned, you can manually back up and restore data between the old and new instances or perform cross-instance [point-in-time restore](point-in-time-restore.md?tabs=azure-powershell). 


## Tune performance

**How do I tune performance of SQL Managed Instance?**

SQL Managed Instance in the General Purpose tier uses remote storage, so the size of data and log files matters to performance. For more information, see [Impact of log file size on General Purpose SQL Managed Instance performance](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

If your workload consists of lots of small transactions, consider switching the connection type from proxy to redirect mode.

## Maximum storage size

**What is the maximum storage size for SQL Managed Instance?**

Storage size for SQL Managed Instance depends on the selected service tier (General Purpose or Business Critical). For storage limitations of these service tiers, see [Service tier characteristics](../database/service-tiers-general-purpose-business-critical.md).

  
## Networking requirements 

**What are the current inbound/outbound NSG constraints on the Managed Instance subnet?**

The required NSG and UDR rules are documented [here](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration), and automatically set by the service.
Please keep in mind that these rules are just the ones we need for maintaining the service. To connect to managed instance and use different features you will need to set additional, feature specific rules, that you need to maintain.

**How can I set inbound NSG rules on management ports?**

SQL Managed Instance is responsible for setting rules on management ports. This is achieved through functionality named [service-aided subnet configuration](connectivity-architecture-overview.md#service-aided-subnet-configuration).
This is to ensure uninterrupted flow of management traffic in order to fulfill an SLA.

**Can I get the source IP ranges that are used for the inbound management traffic?**

Yes. You could analyze traffic coming through your networks security group by [configuring Network Watcher flow logs](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group).

**Can I set NSG to control access to the data endpoint (port 1433)?**

Yes. After a Managed Instance is provisioned you can set NSG that controls inbound access to the port 1433. It is advised to narrow its IP range as much as possible.

**Can I set the NVA or on-premises firewall to filter the outbound management traffic based on FQDNs?**

No. This is not supported for several reasons:
-	Routing traffic that represent response to inbound management request would be asymmetric and could not work.
-	Routing traffic that goes to storage would be affected by throughput constraints and latency so this way we won’t be able to provide expected service quality and availability.
-	Based on experience, these configurations are error prone and not supportable.

**Can I set the NVA or firewall for the outbound non-management traffic?**

Yes. The simplest way to achieve this is to add 0/0 rule to a UDR associated with managed instance subnet to route traffic through NVA.
 
**How many IP addresses do I need for a Managed Instance?**

Subnet must have sufficient number of available [IP addresses](connectivity-architecture-overview.md#network-requirements). To determine VNet subnet size for SQL Managed Instance, see [Determine required subnet size and range for Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet). 

**What if there are not enough IP addresses for performing instance update operation?**

In case there are not enough [IP addresses](connectivity-architecture-overview.md#network-requirements) in the subnet where your managed instance is provisioned, you will have to create a new subnet and a new managed instance inside it. We also suggest that the new subnet is created with more IP addresses allocated so future update operations will avoid similar situations. After the new instance is provisioned, you can manually back up and restore data between the old and new instances or perform cross-instance [point-in-time restore](point-in-time-restore.md?tabs=azure-powershell).

**Do I need an empty subnet to create a Managed Instance?**

No. You can use either an empty subnet or a subnet that already contains Managed Instance(s). 

**Can I change the subnet address range?**

Not if there are Managed Instances inside. This is an Azure networking infrastructure limitation. You are only allowed to [add additional address space to an empty subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings). 

**Can I move my managed instance to another subnet?**

No. This is a current Managed Instance design limitation. However, you can provision a new instance in another subnet and manually back up and restore data between the old and the new instance or perform cross-instance [point-in-time restore](point-in-time-restore.md?tabs=azure-powershell).

**Do I need an empty virtual network to create a Managed Instance?**

This is not required. You can either [Create a virtual network for Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet) or [Configure an existing virtual network for Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet).

**Can I place a Managed Instance with other services in a subnet?**

No. Currently we do not support placing Managed Instance in a subnet that already contains other resource types.

## Connectivity 

**Can I connect to my managed instance using IP address?**

No, this is not supported. A Managed Instance's host name maps to the load balancer in front of the Managed Instance's virtual cluster. As one virtual cluster can host multiple Managed Instances, a connection cannot be routed to the proper Managed Instance without specifying its name.
For more information on SQL Managed Instance virtual cluster architecture, see [Virtual cluster connectivity architecture](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Can a managed instance have a static IP address?**

This is currently not supported.

In rare but necessary situations, we might need to do an online migration of a managed instance to a new virtual cluster. If needed, this migration is because of changes in our technology stack aimed to improve security and reliability of the service. Migrating to a new virtual cluster results in changing the IP address that is mapped to the managed instance host name. The managed instance service doesn't claim static IP address support and reserves the right to change it without notice as a part of regular maintenance cycles.

For this reason, we strongly discourage relying on immutability of the IP address as it could cause unnecessary downtime.

**Does Managed Instance have a public endpoint?**

Yes. Managed Instance has a public endpoint that is by default used only for service management, but a customer may enable it for data access as well. For more details, see [Use SQL Managed Instance with public endpoints](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-securely). To configure public endpoint, go to [Configure public endpoint in SQL Managed Instance](public-endpoint-configure.md).

**How does Managed Instance control access to the public endpoint?**

Managed Instance controls access to the public endpoint at both the network and application level.

Management and deployment services connect to a managed instance by using a [management endpoint](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#management-endpoint) that maps to an external load balancer. Traffic is routed to the nodes only if it's received on a predefined set of ports that only the managed instance's management components use. A built-in firewall on the nodes is set up to allow traffic only from Microsoft IP ranges. Certificates mutually authenticate all communication between management components and the management plane. For more details, see [Connectivity architecture for SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#virtual-cluster-connectivity-architecture).

**Could I use the public endpoint to access the data in Managed Instance databases?**

Yes. The customer will need to enable public endpoint data access from [Azure Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal) / [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) / ARM and configure NSG to lock down access to the data port (port number 3342). For more information, see [Configure public endpoint in Azure SQL Managed Instance](public-endpoint-configure.md) and [Use Azure SQL Managed Instance securely with public endpoint](public-endpoint-overview.md). 

**Can I specify a custom port for SQL data endpoint(s)?**

No, this option is not available.  For private data endpoint, Managed Instance uses default port number 1433 and for public data endpoint, Managed Instance uses default port number 3342.

**What is the recommended way to connect Managed Instances placed in different regions?**

Express Route circuit peering is the preferred way to do that. This is not to be mixed with the cross-region virtual network peering that is not supported due to internal load balancer related [constraint](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

If Express Route circuit peering is not possible, the only other option is to create Site-to-Site VPN connection ([Azure portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal), [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell), [Azure CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)).


## Mitigate data exfiltration risks  

**How can I mitigate data exfiltration risks?**

To mitigate any data exfiltration risks, customers are recommended to apply a set of security settings and controls:

- Turn on [Transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) on all databases.
- Turn off Common Language Runtime (CLR). This is recommended on-premises as well.
- Use Azure Active Directory (Azure AD) authentication only.
- Access the instance with a low-privileged DBA account.
- Configure JIT jumpbox access for the sysadmin account.
- Turn on [SQL auditing](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine), and integrate it with alerting mechanisms.
- Turn on [Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) from the [advanced data security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) suite.


## Cost-saving use cases

**Where can I find use cases and resulting cost savings with SQL Managed Instance?**

SQL Managed Instance case studies:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

To get a better understanding of the benefits, costs, and risks associated with deploying Azure SQL Managed Instance, there's also a Forrester study: [The Total Economic Impact of Microsoft Azure SQL Database Managed Instance](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## DNS

**Can I configure a custom DNS for SQL Managed Instance?**

Yes. See [How to configure a Custom DNS for Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

**Can I do DNS refresh?**

Currently, we don't provide a feature to refresh DNS server configuration for SQL Managed Instance.

DNS configuration is eventually refreshed:

- When DHCP lease expires.
- On platform upgrade.

As a workaround, downgrade SQL Managed Instance to 4 vCores and upgrade it again afterward. This has a side effect of refreshing the DNS configuration.


## Change time zone

**Can I change the time zone for an existing managed instance?**

Time zone configuration can be set when a managed instance is provisioned for the first time. Changing the time zone of an existing managed instance isn't supported. For details, see [Time zone limitations](timezones-overview.md#limitations).

Workarounds include creating a new managed instance with the proper time zone and then either performing a manual backup and restore, or what we recommend, performing a [cross-instance point-in-time restore](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## Resolve performance issues

**How do I resolve performance issues with SQL Managed Instance?**

For a performance comparison between SQL Managed Instance and SQL Server, a good starting point is [Best practices for performance comparison between Azure SQL Managed Instance and SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Data loading is often slower on SQL Managed Instance than in SQL Server due to the mandatory full recovery model and [limits](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) on transaction log write throughput. Sometimes, this can be worked around by loading transient data into tempdb instead of the user database, or by using clustered columnstore or memory-optimized tables.


## Restore encrypted backup

**Can I restore my encrypted database to SQL Managed Instance?**

Yes, you don't need to decrypt your database to restore it to SQL Managed Instance. You do need to provide a certificate/key used as the encryption key protector on the source system to SQL Managed Instance to be able to read data from the encrypted backup file. There are two possible ways to do it:

- *Upload certificate-protector to SQL Managed Instance*. It can be done using PowerShell only. The [sample script](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) describes the whole process.
- *Upload asymmetric key-protector to Azure Key Vault and point SQL Managed Instance to it*. This approach resembles bring-your-own-key (BYOK) TDE use case that also uses Key Vault integration to store the encryption key. If you don't want to use the key as an encryption key protector, and just want to make the key available for SQL Managed Instance to restore encrypted database(s), follow instructions for [setting up BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption), and don’t check the checkbox **Make the selected key the default TDE protector**.

Once you make the encryption protector available to SQL Managed Instance, you can proceed with the standard database restore procedure.

## Purchasing models and benefits

**What purchasing models are available for SQL Managed Instance?**

SQL Managed Instance offers [vCore-based purchasing model](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**What cost benefits are available for SQL Managed Instance?**

You can save costs with the Azure SQL benefits in the following ways:
-	Maximize existing investments in on-premises licenses and save up to 55 percent with [Azure Hybrid Benefit](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell). 
-	Commit to a reservation for compute resources and save up to 33 percent with [Reserved Instance Benefit](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). Combine this with Azure Hybrid benefit for savings up to 82 percent. 
-	Save up to 55 percent versus list prices with [Azure Dev/Test Pricing Benefit](https://azure.microsoft.com/pricing/dev-test/) that offers discounted rates for your ongoing development and testing workloads.

**Who is eligible for Reserved Instance benefit?**

To be eligible for reserved Instance benefit, your subscription type must be an enterprise agreement (offer numbers: MS-AZR-0017P or MS-AZR-0148P) or an individual agreement with pay-as-you-go pricing (offer numbers: MS-AZR-0003P or MS-AZR-0023P). For more information about reservations, see [Reserved Instance Benefit](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). 

**Is it possible to cancel, exchange or refund reservations?**

You can cancel, exchange or refund reservations with certain limitations. For more information, see [Self-service exchanges and refunds for Azure Reservations](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## Billing for Managed Instance and backup storage

**What are the SQL Managed Instance pricing options?**

To explore Managed Instance pricing options, see [Pricing page](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**How can I track billing cost for my managed instance?**

You can do so using the [Azure Cost Management solution](https://docs.microsoft.com/azure/cost-management-billing/). Navigate to **Subscriptions** in the [Azure portal](https://portal.azure.com) and select **Cost Analysis**. 

Use the **Accumulated costs** option and then filter by the **Resource type** as `microsoft.sql/managedinstances`.

**How much automated backups cost?**

You get the equal amount of free backup storage space as the reserved data storage space purchased, regardless of the backup retention period set. If your backup storage consumption is within the allocated free backup storage space, automated backups on managed instance will have no additional cost for you, therefore will be free of charge. Exceeding the use of backup storage above the free space will result in costs of about $0.20 - $0.24 per GB/month in US regions, or see the pricing page for details for your region. For more details, see [Backup storage consumption explained](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**How can I monitor billing cost for my backup storage consumption?**

You can monitor cost for backup storage via Azure Portal. For instructions, see [Monitor costs for automated backups](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs). 

**How can I optimize my backup storage costs on the managed instance?**

To optimize your backup storage costs, see [Fine backup tuning on SQL Managed Instance](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## Password policy 

**What password policies are applied for SQL Managed Instance SQL logins?**

SQL Managed Instance password policy for SQL logins inherits Azure platform policies that are applied to the VMs forming virtual cluster holding the managed instance. At the moment it is not possible to change any of these settings as these settings are defined by Azure and inherited by managed instance.

 > [!IMPORTANT]
 > Azure platform can change policy requirements without notifying services relying on that policies.

**What are current Azure platform policies?**

Each login must set its password upon login and change its password after it reaches maximum age.

| **Policy** | **Security Setting** |
| --- | --- |
| Maximum password age | 42 days |
| Minimum password age | 1 day |
| Minimum password length | 10 characters |
| Password must meet complexity requirements | Enabled |

**Is it possible to disable password complexity and expiration in SQL Managed Instance on login level?**

Yes, it is possible to control CHECK_POLICY and CHECK_EXPIRATION fields on login level. You can check current settings by executing following T-SQL command:

```sql
SELECT *
FROM sys.sql_logins
```

After that, you can modify specified login settings by executing :

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(replace 'test' with desired login name and adjust policy and expiration values)

## Azure feedback and support

**Where can I leave my ideas for SQL Managed Instance improvements?**

You can vote for a new Managed Instance feature or put a new improvement idea on voting on [SQL Managed Instance Feedback Forum](https://feedback.azure.com/forums/915676-sql-managed-instance). This way you can contribute to the product development and help us prioritize our potential improvements.

**How can I create Azure support request?**

To learn how to create Azure support request, see [How to create Azure support request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

