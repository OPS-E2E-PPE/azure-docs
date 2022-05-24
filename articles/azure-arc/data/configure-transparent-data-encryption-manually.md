---
title: Turn on transparent data encryption manually in Azure Arc-enabled SQL Managed Instance
description: How-to guide to turn on transparent data encryption in an Azure Arc-enabled SQL Managed Instance
author: patelr3
ms.author: ravpate
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.reviewer: mikeray
ms.topic: how-to
ms.date: 05/22/2022
ms.custom: template-how-to, event-tier1-build-2022
---

# Enable transparent data encryption on Azure Arc-enabled SQL Managed Instance

This article describes how to enable transparent data encryption on a database created in an Azure Arc-enabled SQL Managed Instance.

## Prerequisites

Before you proceed with this article, you must have an Azure Arc-enabled SQL Managed Instance resource created and have connected to it.

- [An Azure Arc-enabled SQL Managed Instance created](./create-sql-managed-instance.md)
- [Connect to Azure Arc-enabled SQL Managed Instance](./connect-managed-instance.md)

## Turn on transparent data encryption on a database in Azure Arc-enabled SQL Managed Instance

Turning on transparent data encryption in Azure Arc-enabled SQL Managed Instance follows the same steps as SQL Server on-premises. Follow the steps described in [SQL Server's transparent data encryption guide](/sql/relational-databases/security/encryption/transparent-data-encryption#enable-tde).

After creating the necessary credentials, it's highly recommended to back up any newly created credentials.

## Back up a transparent data encryption credential from Azure Arc-enabled SQL Managed Instance

When backing up from Azure Arc-enabled SQL Managed Instance, the credentials will be stored within the container. It isn't necessary to store the credentials on a persistent volume, but you may use the mount path for the data volume within the container if you'd like: `/var/opt/mssql/data`. Otherwise, the credentials will be stored in-memory in the container.  Below is an example of backing up a certificate from Azure Arc-enabled SQL Managed Instance.

> [!NOTE]
> If the `kubectl cp` command is run from Windows, the command may fail when using absolute Windows paths. `kubectl` can mistake the drive in the path as a pod name. For example, `kubectl` might mistake `C` to be a pod name in `C:\folder`. Users can avoid this issue by using relative paths or removing the `C:` from the provided path while in the `C:` drive. This issue also applies to environment variables on Windows like `$HOME`.

1. Back up the certificate from the container to `/var/opt/mssql/data`.

   ```sql
   USE master;
   GO

   BACKUP CERTIFICATE <cert-name> TO FILE = '<cert-path>'
   WITH PRIVATE KEY ( FILE = '<private-key-path>',
   ENCRYPTION BY PASSWORD = '<UseStrongPasswordHere>');
   ```

   Example:

   ```sql
   USE master;
   GO

   BACKUP CERTIFICATE MyServerCert TO FILE = '/var/opt/mssql/data/servercert.crt'
   WITH PRIVATE KEY ( FILE = '/var/opt/mssql/data/servercert.key',
   ENCRYPTION BY PASSWORD = '<UseStrongPasswordHere>');
   ```

2. Copy the certificate from the container to your file system.

   ```console
   kubectl cp --namespace <namespace> --container arc-sqlmi <pod-name>:<pod-certificate-path> <local-certificate-path>
   ```

   Example:

   ```console
   kubectl cp --namespace arc-ns --container arc-sqlmi sql-0:/var/opt/mssql/data/servercert.crt ./sqlcerts/servercert.crt
   ```

3. Copy the private key from the container to your file system.

   ```console
   kubectl cp --namespace <namespace> --container arc-sqlmi <pod-name>:<pod-private-key-path> <local-private-key-path>
   ```

   Example:

   ```console
   kubectl cp --namespace arc-ns --container arc-sqlmi sql-0:/var/opt/mssql/data/servercert.key ./sqlcerts/servercert.key
   ```

4. Delete the certificate and private key from the container.

   ```console
   kubectl exec -it --namespace <namespace> --container arc-sqlmi <pod-name> -- bash -c "rm <certificate-path> <private-key-path>
   ```

   Example:

   ```console
   kubectl exec -it --namespace arc-ns --container arc-sqlmi sql-0 -- bash -c "rm /var/opt/mssql/data/servercert.crt /var/opt/mssql/data/servercert.key"
   ```

## Restore a transparent data encryption credential to Azure Arc-enabled SQL Managed Instance

Similar to above, restore the credentials by copying them into the container and running the corresponding T-SQL afterwards.

> [!NOTE]
> If the `kubectl cp` command is run from Windows, the command may fail when using absolute Windows paths. `kubectl` can mistake the drive in the path as a pod name. For example, `kubectl` might mistake `C` to be a pod name in `C:\folder`. Users can avoid this issue by using relative paths or removing the `C:` from the provided path while in the `C:` drive. This issue also applies to environment variables on Windows like `$HOME`.

1. Copy the certificate from your file system to the container.

   ```console
   kubectl cp --namespace <namespace> --container arc-sqlmi <local-certificate-path> <pod-name>:<pod-certificate-path>
   ```

   Example:

   ```console
   kubectl cp --namespace arc-ns --container arc-sqlmi ./sqlcerts/servercert.crt sql-0:/var/opt/mssql/data/servercert.crt
   ```

2. Copy the private key from your file system to the container.

   ```console
   kubectl cp --namespace <namespace> --container arc-sqlmi <local-private-key-path> <pod-name>:<pod-private-key-path>
   ```

   Example:

   ```console
   kubectl cp --namespace arc-ns --container arc-sqlmi ./sqlcerts/servercert.key sql-0:/var/opt/mssql/data/servercert.key
   ```

3. Create the certificate using file paths from `/var/opt/mssql/data`.

   ```sql
   USE master;
   GO

   CREATE CERTIFICATE <certicate-name>
   FROM FILE = '<certificate-path>'
   WITH PRIVATE KEY ( FILE = '<private-key-path>',
       DECRYPTION BY PASSWORD = '<UseStrongPasswordHere>' );
   ```

   Example:

   ```sql
   USE master;
   GO

   CREATE CERTIFICATE MyServerCertRestored
   FROM FILE = '/var/opt/mssql/data/servercert.crt'
   WITH PRIVATE KEY ( FILE = '/var/opt/mssql/data/servercert.key',
       DECRYPTION BY PASSWORD = '<UseStrongPasswordHere>' );
   ```

4. Delete the certificate and private key from the container.

   ```console
   kubectl exec -it --namespace <namespace> --container arc-sqlmi <pod-name> -- bash -c "rm <certificate-path> <private-key-path>
   ```

   Example:

   ```console
   kubectl exec -it --namespace arc-ns --container arc-sqlmi sql-0 -- bash -c "rm /var/opt/mssql/data/servercert.crt /var/opt/mssql/data/servercert.key"
   ```

## Next steps

[Transparent data encryption](/sql/relational-databases/security/encryption/transparent-data-encryption)
