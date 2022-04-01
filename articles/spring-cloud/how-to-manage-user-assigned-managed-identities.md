---
title: Manage user-assigned managed identities for an application in Azure Spring Cloud (preview)
description: How to manage user-assigned managed identities for applications.
author: karlerickson
ms.author: jiec
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/31/2022
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: spring-cloud-tier-selection
---

# Manage user-assigned managed identities for an application in Azure Spring Cloud (preview)

**This article applies to:** ✔️ Basic/Standard tier ✔️ Enterprise tier

This article shows you how to assign or remove user-assigned managed identities for an application in Azure Spring Cloud, using the Azure portal and Azure CLI.

Managed identities for Azure resources provide an automatically managed identity in Azure Active Directory (Azure AD) to an Azure resource such as your application in Azure Spring Cloud. You can use this identity to authenticate to any service that supports Azure AD authentication, without having credentials in your code.

## Prerequisites

- If you're unfamiliar with managed identities for Azure resources, see the [Managed identities for Azure resources overview section](../active-directory/managed-identities-azure-resources/overview.md).

::: zone pivot="sc-enterprise-tier"

- An already provisioned Azure Spring Cloud Enterprise tier instance. For more information, see [Quickstart: Provision an Azure Spring Cloud service instance using the Enterprise tier](quickstart-provision-service-instance-enterprise.md).
- [Azure CLI version 3.1.0 or later](/cli/azure/install-azure-cli).
- [!INCLUDE [install-enterprise-extension](includes/install-enterprise-extension.md)]
- At least one already provisioned user-assigned managed identity. For more information, see [Manage user-assigned managed identities](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md).

::: zone-end

::: zone pivot="sc-standard-tier"

- An already provisioned Azure Spring Cloud instance. For more information, see [Quickstart: Deploy your first application to Azure Spring Cloud](./quickstart.md).
- [Azure CLI version 3.1.0 or later](/cli/azure/install-azure-cli).
- At least one already provisioned user-assigned managed identity. For more information, see [Manage user-assigned managed identities](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md).

::: zone-end

## Assign user-assigned managed identities when creating an application

Create an application and assign user-assigned managed identity at the same time by using the following command:

```azurecli
az spring-cloud app create \
    --resource-group <resource-group-name> \
    --name <app-name> \
    --service <service-instance-name> \
    --user-assigned <space-separated user identity resource IDs to assign>
```

## Assign user-assigned managed identities to an existing application

Assigning user-assigned managed identity requires setting an additional property on the application.

### [Azure portal](#tab/azure-portal)

To assign user-assigned managed identity to an existing application in the Azure portal, follow these steps:

1. Navigate to an application in the Azure portal as you normally would.
2. Scroll down to the **Settings** group in the left navigation pane.
3. Select **Identity**.
4. Within the **User assigned** tab, select **Add**.
5. Choose one or more user-assigned managed identities from right panel and then select **Add** from this panel.

### [Azure CLI](#tab/azure-cli)

Use the following command to assign one or more user-assigned managed identities on an existing app:

```azurecli
az spring-cloud app identity assign \
    --resource-group <resource-group-name> \
    --name <app-name> \
    --service <service-instance-name> \
    --user-assigned <space-separated user identity resource IDs to assign>
```

---

## Obtain tokens for Azure resources

An application can use its managed identity to get tokens to access other resources protected by Azure AD, such as Azure Key Vault. These tokens represent the application accessing the resource, not any specific user of the application.

You may need to configure the target resource to allow access from your application. For more information, see [Assign a managed identity access to a resource by using the Azure portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). For example, if you request a token to access Key Vault, be sure you have added an access policy that includes your application's identity. Otherwise, your calls to Key Vault will be rejected, even if they include the token. To learn more about which resources support Azure Active Directory tokens, see [Azure services that support Azure AD authentication](../active-directory/managed-identities-azure-resources/services-azure-active-directory-support.md)

Azure Spring Cloud shares the same endpoint for token acquisition with Azure Virtual Machines. We recommend using Java SDK or Spring Boot starters to acquire a token. For various code and script examples and guidance on important topics such as handling token expiration and HTTP errors, see [How to use managed identities for Azure resources on an Azure VM to acquire an access token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## Remove user-assigned managed identities from an existing app

Removing user-assigned managed identities will remove the assignment between the identities and the application, and will not delete the identities themselves.

### [Azure portal](#tab/azure-portal)

To remove user-assigned managed identities from an application that no longer needs it, follow these steps:

1. Sign in to the Azure portal using an account associated with the Azure subscription that contains the Azure Spring Cloud instance.
1. Navigate to the desired application and select **Identity**.
1. Under **User assigned**, select target identities and then select **Remove**.

### [Azure CLI](#tab/azure-cli)

To remove user-assigned managed identities from an application that no longer needs it, use the following command:

```azurecli
az spring-cloud app identity remove \
    --resource-group <resource-group-name> \
    --name <app-name> \
    --service <service-instance-name> \
    --user-assigned <space-separated user identity resource IDs to remove>
```

## Limitations

For user-assigned managed identity limitations, see [Quotas and service plans for Azure Spring Cloud](./quotas.md).

---

## Next steps

* [Access Azure Key Vault with managed identities in Spring boot starter](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Learn more about managed identities for Azure resources](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [How to use managed identities with Java SDK](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
