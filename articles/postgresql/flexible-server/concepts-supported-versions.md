---
title: Supported PostgreSQL versions in Azure Database for PostgreSQL - Flexible Server
description: Learn about the supported PostgreSQL major and minor versions in Azure Database for PostgreSQL - Flexible Server.
ms.author: sunila
author: sunilagarwal
ms.service: postgresql
ms.subservice: flexible-server
ms.custom:
  - ignite-2023
ms.topic: conceptual
ms.date: 3/14/2023
---

# Supported PostgreSQL versions in Azure Database for PostgreSQL - Flexible Server

[!INCLUDE [applies-to-postgresql-flexible-server](../includes/applies-to-postgresql-flexible-server.md)]

Azure Database for PostgreSQL flexible server currently supports the following major versions.

## PostgreSQL version 16

PostgreSQL version 16 is generally available in all Azure regions. The current minor release is **16.1**. To learn more about improvements and fixes in this release, refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/release/16.1/). New servers are created with this minor version.

## PostgreSQL version 15

The current minor release is **15.5**. To learn more about improvements and fixes in this release, refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/release/15.5/). New servers are created with this minor version.

## PostgreSQL version 14

The current minor release is **14.10**. To learn more about improvements and fixes in this release, refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/release/14.10/). New servers are created with this minor version.

## PostgreSQL version 13

The current minor release is **13.13**. To learn more about improvements and fixes in this release, refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/release/13.13/). New servers are created with this minor version.

## PostgreSQL version 12

The current minor release is **12.17**. To learn more about improvements and fixes in this release, refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/release/12.17/). New servers are created with this minor version. Your existing servers are automatically upgraded to the latest supported minor version in your future scheduled maintenance window.

## PostgreSQL version 11

The current minor release is **11.22**. To learn more about improvements and fixes in this release, refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/release/11.22/). New servers are created with this minor version. Your existing servers are automatically upgraded to the latest supported minor version in your future scheduled maintenance window.

## PostgreSQL version 10 and older

We don't support PostgreSQL version 10 and older for Azure Database for PostgreSQL flexible server. Use the [Azure Database for PostgreSQL single server](../concepts-supported-versions.md) deployment option if you require older versions.

## Managing upgrades

The PostgreSQL project regularly issues minor releases to fix reported bugs. Azure Database for PostgreSQL flexible server automatically patches servers with minor releases during the service's monthly deployments.

It's also possible to do in-place major version upgrades by using the [major version upgrade](./concepts-major-version-upgrade.md) feature. This feature greatly simplifies the upgrade process of an instance from a major version (PostgreSQL 11, for example) to any higher supported version (like PostgreSQL 16).

## Supportability and retirement policy of the underlying operating system

Azure Database for PostgreSQL flexible server is a fully managed open-source database. The underlying operating system is an integral part of the service. Microsoft continually works to ensure ongoing security updates and maintenance for security compliance and vulnerability mitigation, whether a partner or an internal vendor provides them. Automatic upgrades during scheduled maintenance help keep your managed database secure, stable, and up to date.

## Managing PostgreSQL engine defects

Microsoft has a team of committers and contributors who work full time on the open-source Postgres project and are long-term members of the community. Our contributions include features, performance enhancements, bug fixes, and security patches, among other things. Our open-source team also incorporates feedback from our Azure fleet (and customers) when prioritizing work. But keep in mind that the Postgres project has its own independent contribution guidelines, review process, and release schedule.

When we identify a defect with PostgreSQL engine, we take immediate action to mitigate the problem. If it requires code change, we fix the defect to address the production issue, if possible. We work with the community to incorporate the fix as quickly as possible.

## Next steps

Learn about [PostgreSQL extensions](concepts-extensions.md).
