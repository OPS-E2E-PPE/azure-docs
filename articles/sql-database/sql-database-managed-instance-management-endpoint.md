---
title: Discover Azure SQL Database Managed Instance management endpoint | Microsoft Docs
description: Learn how to get Azure SQL Database Managed Instance management endpoint public IP address and verify its built-in firewall protection
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: 
ms.devlang: 
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/03/2018
---

# Discover Azure SQL Database Managed Instance management endpoint

The Azure SQL Database Managed Instance [virtual cluster](sql-database-managed-instance-connectivity-architecture.md) contains a management endpoint that Microsoft uses for managing the Managed Instance. The management endpoint is protected with built-in firewall on network level and mutual certificate verification on application level.

When connections are initiated from inside the Managed Instance (backup, audit log) it appears that traffic originates from management endpoint public IP address. You could limit access to public services from Managed Instance by setting firewall rules to allow the Managed Instance IP address only.

> [!NOTE]
> This doesn’t apply to setting firewall rules for Azure services that are in the same region as Managed Instance as the Azure platform has an optimization for the traffic that goes between the services that are collocated.

This article explains how you could get the management endpoint public IP address as well as how to verify its built-in firewall protection.

## Finding the management endpoint public IP address

Let’s assume that Managed Instance host is `mi-demo.xxxxxx.database.windows.net`. Run `nslookup` using the host name.

![Resolving internal host name](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Now do another `nslookup` for highlighted name removing the `.vnet.` segment. You’ll get the public IP address as a result of executing this command.

![Resolving public IP address](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## Verifying the Managed Instance built-in firewall

The Managed Instance [mandatory inbound security rules](sql-database-managed-instance-vnet-configuration.md#mandatory-inbound-security-rules) require management ports 9000, 9003, 1438, 1440, 1452 to be open from **Any source** on the Network Security Group (NSG) that protects the Managed Instance. Although these ports are open at the NSG level, they are protected at the network level by the built-in firewall.

To verify these ports, use any security scanner tool to test these ports. The following screenshot shows how to use one of these tools.

![Verifying built-in firewall](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## Next steps

For more information about Managed Instances and connectivity, see [Azure SQL Database Managed Instance Connectivity Architecture](sql-database-managed-instance-connectivity-architecture.md).