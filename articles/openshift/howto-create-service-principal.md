---
title: Creating and using a service principal with an Azure Red Hat OpenShift cluster
description: In this how-to article, learn how to create and use a service principal with an Azure Red Hat OpenShift cluster using Azure CLI or the Azure portal.
author: rahulm23
ms.service: azure-redhat-openshift
ms.topic: how-to
ms.author: rahulmehta
ms.date: 03/21/2022
topic: how-to
keywords: azure, openshift, aro, red hat, azure CLI, azure portal
#Customer intent: I need to create and use an Azure service principal to restrict permissions to my Azure Red Hat OpenShift cluster.
zone_pivot_groups: azure-red-hat-openshift-service-principal
---

# Create and use a service principal to deploy an Azure Red Hat OpenShift cluster

To interact with Azure APIs, an Azure Red Hat OpenShift cluster requires an Azure Active Directory (AD) service principal. This service principal is used to dynamically create, manage, or access other Azure resources, such as an Azure load balancer or an Azure Container Registry (ACR). For more information, see [Application and service principal objects in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

This article explains how to create and use a service principal to deploy your Azure Red Hat OpenShift clusters using the Azure command-line interface (Azure CLI) or the Azure portal.  

> [!NOTE]
> Service principals expire in one year unless configured for longer periods. For information on extending your service principal expiration period, see [Rotate service principal credentials for your Azure Red Hat OpenShift (ARO) Cluster](howto-service-principal-credential-rotation.md).

::: zone pivot="aro-azurecli"

## Create and use a service principal

The following sections explain how to create and use a service principal to deploy an Azure Red Hat OpenShift cluster. 

## Prerequisites - Azure CLI

If you’re using the Azure CLI, you’ll need Azure CLI version 2.0.59 or later installed and configured. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli).

## Create a resource group - Azure CLI

Run the following Azure CLI command to create a resource group.

```azurecli-interactive
AZ_RG=$(az group create -n test-aro-rg -l eastus2 --query name -o tsv)
```

## Create a service principal and assign role-based access control (RBAC) - Azure CLI

 To assign the contributor role and scope the service principal to the Azure Red Hat OpenShift resource group, run the following command.

```azurecli-interactive
# Get Azure subscription ID
AZ_SUB_ID=$(az account show --query id -o tsv) 
# Create a service principal with contributor role and scoped to the Azure Red Hat OpenShift resource group 
az ad sp create-for-rbac -n "test-aro-SP" --role contributor --scopes "/subscriptions/${AZ_SUB_ID}/resourceGroups/${AZ_RG}"
```

The output is similar to the following example.

```
{ 

  "appId": "", 

  "displayName": "myAROClusterServicePrincipal", 

  "name": "http://myAROClusterServicePrincipal", 

  "password": "yourpassword", 

  "tenant": "yourtenantname"

}
``` 
 
> [!NOTE]
> This service principal only allows a contributor over the resource group the Azure Red Hat OpenShift cluster is located in. If your VNet is in another resource group, you need to assign the service principal contributor role to that resource group as well. 

To grant permissions to an existing service principal with the Azure portal, see [Create an Azure AD app and service principal in the portal](../active-directory/develop/howto-create-service-principal-portal.md#configure-access-policies-on-resources).

## Use the service principal to deploy an Azure Red Hat OpenShift cluster - Azure CLI

Using the service principal that you created when you created the Azure Red Hat OpenShift cluster, use the `az aro create` command to deploy the Azure Red Hat OpenShift cluster. Use the `--client-id` and `--client-secret` parameters to specify the appId and password from the output of the `az ad sp create-for-rbac` command, as shown in the following command. 

```azure-cli 
az aro create \ 

    --resource-group myResourceGroup \ 

    --name myAROCluster \ 

    --client-id <appID> \ 

    --client-secret <password> 
```

> [!IMPORTANT] 
> If you're using an existing service principal with a customized secret, ensure the secret doesn't exceed 190 bytes. 

::: zone-end

::: zone pivot="aro-azureportal"

## Create a service principal with the Azure portal

The following sections explain how to use the Azure portal to create a service principal for your Azure Red Hat OpenShift cluster.

## Prerequisite - Azure portal

Create a service principal, as explained in [Use the portal to create an Azure AD application and service principal that can access resources](../active-directory/develop/howto-create-service-principal-portal.md). **Be sure to save the client ID and the appID.**

## To use the service principal to deploy an Azure Red Hat OpenShift cluster - Azure portal 

To use the service principal you created to deploy a cluster, complete the following steps.

1. On the Create Azure Red Hat OpenShift **Basics** tab, create a resource group for your subscription, as shown in the following example.

   :::image type="content" source="./media/basics-openshift-sp.png" alt-text="Screenshot that shows how to use the Azure Red Hat service principal with Azure portal to create a cluster." lightbox="./media/basics-openshift-sp.png":::

2. Select **Next: Authentication** to configure the service principal on the **Authentication** page of the **Azure Red Hat OpenShift** dialog.

     :::image type="content" source="./media/openshift-service-principal-portal.png" alt-text="Screenshot that shows how to use the Authentication tab with Azure portal to create a service principal." lightbox="./media/openshift-service-principal-portal.png":::

In the **Service principal information** section:

- **Service principal client ID** is your appId. 
- **Service principal client secret** is the service principal's decrypted Secret value.

In the **Cluster pull secret** section:

- **Pull secret** is your cluster's pull secret's decrypted value. If you don't have a pull secret, leave this field blank.

After completing this tab, select **Next: Networking** to continue deploying your cluster. Select **Review + Create** when you complete the remaining tabs.

> [!NOTE]
> This service principal only allows a contributor over the resource group the Azure Red Hat OpenShift cluster is located in. If your VNet is in another resource group, you need to assign the service principal contributor role to that resource group as well.

## Grant permissions to the service principal - Azure portal

To grant permissions to an existing service principal with the Azure portal, see [Create an Azure AD app and service principal in the portal](../active-directory/develop/howto-create-service-principal-portal.md#configure-access-policies-on-resources).

::: zone-end