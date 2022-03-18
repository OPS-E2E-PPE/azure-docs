---
title: What's New in Azure Cache for Redis
description: Recent updates for Azure Cache for Redis
author: flang-msft
ms.author: franlanglois
ms.service: cache
ms.topic: reference
ms.date: 02/25/2022

---

# What's New in Azure Cache for Redis

## February 2022

### TLS Certificate Change

As of May 2022, Azure Cache for Redis rolls over to TLS certificates issued by DigiCert Global G2 CA Root. The current Baltimore CyberTrust Root expires in May 2025, requiring this change.

We expect that most Azure Cache for Redis customers won't be affected. However, your application might be affected if you explicitly specify a list of acceptable certificate authorities (CAs), which is known as *certificate pinning*.

For more information, read this blog that contains instructions on [how to check whether your client application is affected](https://techcommunity.microsoft.com/t5/azure-developer-community-blog/azure-cache-for-redis-tls-upcoming-migration-to-digicert-global/ba-p/3171086). We recommend taking the actions recommended in the blog to avoid cache connectivity loss.

### Active geo-replication for Azure Cache For Redis Enterprise GA

Active geo-replication for Azure Cache for Redis Enterprise is now generally available (GA).

Active geo-replication is a powerful tool that enables Azure Cache for Redis clusters to be linked together for seamless active-active replication of data. Your applications can write to one Redis cluster and your data is automatically copied to the other linked clusters, and vice versa. For more information, see this [post](https://aka.ms/ActiveGeoGA) in the *Azure Developer Community Blog*.

## January 2022

### Support for managed identity in Azure Cache for Redis

Azure Cache for Redis now supports authenticating storage account connections using managed identity. Identity is established through Azure Active Directory, and both system-assigned and user-assigned identities are supported. Support for managed identity further allows the service to establish trusted access to storage for uses including data persistence and importing/exporting cache data.

For more information, see [Managed identity with Azure Cache for Redis (Preview)](cache-managed-identity.md).

## October 2021

### Azure Cache for Redis 6.0 GA

Azure Cache for Redis 6.0 is now generally available. The new version includes:

- Redis Streams, a new data type
- Performance enhancements
- Enhanced developer productivity
- Boosts security

You can now use an append-only data structure, Redis Streams, to ingest, manage, and make sense of data that is continuously being generated.

Additionally, Azure Cache for Redis 6.0 introduces new commands: `STRALGO`, `ZPOPMIN`, `ZPOPMAX`, and `HELP` for performance and ease of use.

Get started with Azure Cache for Redis 6.0, today, and select Redis 6.0 during cache creation. Also, you can upgrade your existing Redis 4.0 cache instances. For more information, see [Set Redis version for Azure Cache for Redis](cache-how-to-version.md).

### Diagnostics for connected clients

Azure Cache for Redis now integrates with Azure diagnostic settings to log information on all client connections to your cache. Logging and then analyzing this diagnostic setting helps you understand who is connecting to your caches and the timestamp of those connections. This data could be used to identify the scope of a security breach and for security auditing purposes. Users can route these logs to a destination of their choice, such as a storage account or Event Hubs.

For more information, see [Monitor Azure Cache for Redis data using diagnostic settings](cache-monitor-diagnostic-settings.md).

### Azure Cache for Redis Enterprise update

Active geo-replication public preview now supports:

- RediSearch Module: Deploy RediSearch with active geo-replication
- Five caches in a replication group. Previously, supported two caches.
- OSS clustering policy - suitable for high-performance workloads and provides better scalability.

## October 2020

### Azure TLS Certificate Change

Microsoft is updating Azure services to use TLS certificates from a different set of Root Certificate Authorities (CAs). This change is being made because the current CA certificates don't comply with one of the CA/Browser Forum Baseline requirements. For full details, see [Azure TLS Certificate Changes](../security/fundamentals/tls-certificate-changes.md).

For more information on the effect to Azure Cache for Redis, see [Azure TLS Certificate Change](cache-best-practices-development.md#azure-tls-certificate-change).

## Next steps

If you have more questions, contact us through [support](https://azure.microsoft.com/support/options/).