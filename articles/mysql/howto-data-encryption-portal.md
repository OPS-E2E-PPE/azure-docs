---
title: Data Encryption for Azure Database for MySQL using portal
description: Learn how to set up and manage Data Encryption for your Azure Database for MySQL using Azure portal
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/10/2020
---

# Data Encryption for Azure Database for MySQL server using Azure portal

In this article, you will learn how to set up and manage to use the Azure portal to setup Data Encryption for your Azure Database for MySQL.

## Prerequisites for PowerShell

* You must have an Azure subscription and be an administrator on that subscription.
* You must have Azure PowerShell installed and running.
* Create an Azure Key Vault and Key to use for customer-managed key.
* The Key Vault must have the following property to use as a customer-managed key
    * [Soft Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

        ```azurecli-interactive
        az resource update --id $(az keyvault show --name \ <key_valut_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
        ```
    
    * [Purge protected](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

        ```azurecli-interactive
        az keyvault update --name <key_valut_name> --resource-group <resource_group_name>  --enable-purge-protection true
        ```
* The key must have the following attributes to be used for customer-managed key.
    * No expiration date
    * Not disabled
    * Able to perform get, wrap key, unwrap key operations

## Setting the right permissions for key operations

1. On the Azure Key Vault, select the **Access Policies** and, **Add Access Policy** 

   ![Access policy overview](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Select the **Key Permissions** select **Get**, **Wrap**, **Unwrap** and the **Principal** which is the name of the MySQL server.

   ![Access policy overview](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **Save** the settings.

## Setting Data Encryption for Azure Database for MySQL

1. On the **Azure Database for MySQL**, select the **Data Encryption** to set the customer-managed key setup.

   ![Setting Data Encryption](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. You can either select a **Key Vault** and **Key** pair or pass a **Key identifier**.

   ![Setting Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Save** the settings.

4. To ensure all files (including temp files) are full encrypted, a server restart is required.

## Restoring or creating replica of the server which has Data Encryption enabled

Once an Azure Database for MySQL is encrypted with customer's managed key stored in the Key Vault, any newly created copy of the server either though local or geo-restore operation or a replica (local/cross-region) operation. So for an encrypted MySQL server, you can follow the steps below to create an encrypted restored server.

1. On your server, select **Overview**, then select **Restore**.

   ![Initiate-restore](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Or for a replication enabled server, under the **Settings** heading, select **Replication**

   ![Initiate-replica](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Once the restore operation is complete, the new server created is encrypted with the key used to encrypt the primary server. However, the features and options on the server are disabled and the server is marked in an **Inaccessible** state. This is to prevent any data manipulation since the new server's identity has still been not given permission to access the Key Vault.

   ![Mark server inaccessible](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. To fix Inaccessible state, you need to revalidate the key on the restored server.

   ![revalidate server](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   You will have to give access to the new server to the Key Vault. 

4. Once you revalidate the key, the server resumes its normal functionality.

   ![Normal server restored](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## Next steps

 To learn more about Data Encryption, see [what is Azure data encryption](concepts-data-encryption-mysql.md).

