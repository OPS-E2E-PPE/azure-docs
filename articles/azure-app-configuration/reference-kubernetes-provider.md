---
title: Azure App Configuration Kubernetes Provider reference (preview) | Microsoft Docs
description: "It describes the supported properties of AzureAppConfigurationProvider object in the Azure App Configuration Kubernetes Provider."
services: azure-app-configuration
author: junbchen
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 04/12/2023
ms.author: junbchen
#Customer intent: As an Azure Kubernetes Service user, I want to manage all my app settings in one place using Azure App Configuration.
---

# Azure App Configuration Kubernetes Provider reference (preview)

The following reference outlines the properties supported by the Azure App Configuration Kubernetes Provider.

## Properties

An `AzureAppConfigurationProvider` resource has the following top-level child properties under the `spec`. Either `endpoint` or `connectionStringReference` has to be specified.

|Name|Description|Required|Type|
|---|---|---|---|
|endpoint|The endpoint of Azure App Configuration, which you would like to retrieve the key-values from|alternative|string|
|connectionStringReference|The name of the Kubernetes Secret that contains Azure App Configuration connection string|alternative|string|
|target|The destination of the retrieved key-values in Kubernetes|true|object|
|auth|The authentication method to access Azure App Configuration|false|object|
|keyValues|The settings for querying and processing key-values|false|object|

The `spec.target` property has the following child property.

|Name|Description|Required|Type|
|---|---|---|---|
|configMapName|The name of the ConfigMap to be created|true|string|
|configMapData|The setting that specifies how the retrieved data should be populated in the generated ConfigMap|false|object|

If the `spec.target.configMapData` property is not set, the generated ConfigMap will be populated with the list of key-values retrieved from Azure App Configuration, which allows the ConfigMap to be consumed as environment variables. Update this property if you wish to consume the ConfigMap as a mounted file. This property has the following child properties.

|Name|Description|Required|Type|
|---|---|---|---|
|type|The setting that indicates how the retrieved data is constructed in the generated ConfigMap. The allowed values include `default`, `json`, `yaml` and `properties`|optional|string|
|key|The key name of the retrieved data when the `type` is set to `json`, `yaml` or `properties`. Set it to the file name if the ConfigMap is set up to be consumed as a mounted file|conditional|string|

The `spec.auth` property isn't required if the connection string of your App Configuration store is provided by setting the `spec.connectionStringReference` property. Otherwise, one of the identities, service principal, workload identity, or managed identity, will be used for authentication. The `spec.auth` has the following child properties. Only one of them should be specified. If none of them are set, the system-assigned managed identity of the virtual machine scale set will be used.

|Name|Description|Required|Type|
|---|---|---|---|
|servicePrincipalReference|The name of the Kubernetes Secret that contains the credentials of a service principal|false|string|
|workloadIdentity|The settings for using workload identity|false|object|
|managedIdentityClientId|The Client ID of user-assigned managed identity of virtual machine scale set|false|string|

The `spec.auth.workloadIdentity` property has the following child property.

|Name|Description|Required|Type|
|---|---|---|---|
|managedIdentityClientId|The Client ID of the user-assigned managed identity associated with the workload identity|true|string|

The `spec.keyValues` has the following child properties. The `spec.keyValues.keyVaults` property is required if any Key Vault references are expected to be downloaded.
  
|Name|Description|Required|Type|
|---|---|---|---|
|selectors|The list of selectors for key-value filtering|false|object array|
|trimKeyPrefixes|The list of key prefixes to be trimmed|false|string array|
|keyVaults|The settings for Key Vault references|conditional|object|
|refresh|The settings for refreshing the key-values in ConfigMap or Secret|false|object|

If the `spec.keyValues.selectors` property isn't set, all key-values with no label will be downloaded. It contains an array of *selector* objects, which have the following child properties.

|Name|Description|Required|Type|
|---|---|---|---|
|keyFilter|The key filter for querying key-values|true|string|
|labelFilter|The label filter for querying key-values|false|string|

The `spec.keyValues.keyVaults` property has the following child properties.

|Name|Description|Required|Type|
|---|---|---|---|
|target|The destination of resolved Key Vault references in Kubernetes|true|object|
|auth|The authentication method to access Key Vaults|false|object|

The `spec.keyValues.keyVaults.target` property has the following child property.

|Name|Description|Required|Type|
|---|---|---|---|
|secretName|The name of the Kubernetes Secret to be created|true|string|

If the `spec.keyValues.keyVaults.auth` property isn't set, the system-assigned managed identity is used. It has the following child properties.

|Name|Description|Required|Type|
|---|---|---|---|
|servicePrincipalReference|The name of the Kubernetes Secret that contains the credentials of a service principal used for authentication with vaults that don't have individual authentication methods specified|false|string|
|workloadIdentity|The settings of the workload identity used for authentication with vaults that don't have individual authentication methods specified. It has the same child properties as `spec.auth.workloadIdentity`|false|object|
|managedIdentityClientId|The client ID of a user-assigned managed identity of virtual machine scale set used for authentication with vaults that don't have individual authentication methods specified|false|string|
|vaults|The authentication methods for individual vaults|false|object array|

The authentication method of each *vault* can be specified with the following properties. One of `managedIdentityClientId`, `servicePrincipalReference` or `workloadIdentity` must be provided.

|Name|Description|Required|Type|
|---|---|---|---|
|uri|The URI of a vault|true|string|
|servicePrincipalReference|The name of the Kubernetes Secret that contains the credentials of a service principal used for authentication with a vault|false|string|
|workloadIdentity|The settings of the workload identity used for authentication with a vault. It has the same child properties as `spec.auth.workloadIdentity`|false|object|
|managedIdentityClientId|The client ID of a user-assigned managed identity of virtual machine scale set used for authentication with a vault|false|string|

The `spec.keyValues.refresh` property has the following child properties.

|Name|Description|Required|Type|
|---|---|---|---|
|monitoring|The key-values that are monitored by the provider, provider automatically refreshes the ConfigMap or Secret if value change in any designated key-value|true|object|
|interval|The interval for refreshing, default value is 30 seconds, must be greater than 1 second|false|duration string|

The `spec.keyValues.refresh.monitoring.keyValues` is an array of objects, which have the following child properties.

|Name|Description|Required|Type|
|---|---|---|---|
|key|The key of a key-value|true|string|
|label|The label of a key-value|false|string|

## Examples

### Authentication

#### Use system-assigned managed identity of virtual machine scale set

1. [Enable the system-assigned managed identity in the virtual machine scale set](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vmss#enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set) used by the Azure Kubernetes Service (AKS) cluster.

1. [Grant the system-assigned managed identity **App Configuration Data Reader** role](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) in Azure App Configuration.

1. Deploy the following sample `AzureAppConfigurationProvider` resource to the AKS cluster.

    ``` yaml
    apiVersion: azconfig.io/v1beta1
    kind: AzureAppConfigurationProvider
    metadata:
      name: appconfigurationprovider-sample
    spec:
      endpoint: <your-app-configuration-store-endpoint>
      target:
        configMapName: configmap-created-by-appconfig-provider
    ```

#### Use user-assigned managed identity of virtual machine scale set

1. [Create a user-assigned managed identity](/azure/active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities#create-a-user-assigned-managed-identity) and note down its client ID after creation.

1. [Assign the user-assigned managed identity to the virtual machine scale set](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vmss#user-assigned-managed-identity) used by the Azure Kubernetes Service (AKS) cluster.

1. [Grant the user-assigned managed identity **App Configuration Data Reader** role](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vmss#user-assigned-managed-identity) in Azure App Configuration.

1. Set the `spec.auth.managedIdentityClientId` property to the client ID of the user-assigned managed identity in the following sample `AzureAppConfigurationProvider` resource and deploy it to the AKS cluster.

    ``` yaml
    apiVersion: azconfig.io/v1beta1
    kind: AzureAppConfigurationProvider
    metadata:
      name: appconfigurationprovider-sample
    spec:
      endpoint: <your-app-configuration-store-endpoint>
      target:
        configMapName: configmap-created-by-appconfig-provider
      auth:
        managedIdentityClientId: <your-managed-identity-client-id>
    ```

#### Use service principal

1. [Create a Service Principal](/azure/active-directory/develop/howto-create-service-principal-portal)

1. [Grant the service principal **App Configuration Data Reader** role](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) in Azure App Configuration.

1. Create a Kubernetes Secret in the same namespace as the `AzureAppConfigurationProvider` resource and add *azure_client_id*, *azure_client_secret*, and *azure_tenant_id* of the service principal to the Secret.

1. Set the `spec.auth.servicePrincipalReference` property to the name of the Secret in the following sample `AzureAppConfigurationProvider` resource and deploy it to the Kubernetes cluster.

    ``` yaml
    apiVersion: azconfig.io/v1beta1
    kind: AzureAppConfigurationProvider
    metadata:
      name: appconfigurationprovider-sample
    spec:
      endpoint: <your-app-configuration-store-endpoint>
      target:
        configMapName: configmap-created-by-appconfig-provider
      auth:
        servicePrincipalReference: <your-service-principal-secret-name>
    ```

#### Use workload identity

1. [Enable Workload Identity](/azure/aks/workload-identity-deploy-cluster#update-an-existing-aks-cluster) on the Azure Kubernetes Service (AKS) cluster.

1. [Get the OIDC issuer URL](/azure/aks/workload-identity-deploy-cluster#retrieve-the-oidc-issuer-url) of the AKS cluster.

1. [Create a user-assigned managed identity](/azure/active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities#create-a-user-assigned-managed-identity) and note down its client ID after creation.

1. Create the federated identity credential between the managed identity, OIDC issuer, and subject using the Azure CLI.

   ``` azurecli
   az identity federated-credential create --name "${FEDERATED_IDENTITY_CREDENTIAL_NAME}" --identity-name "${USER_ASSIGNED_IDENTITY_NAME}" --resource-group "${RESOURCE_GROUP}" --issuer "${AKS_OIDC_ISSUER}" --subject system:serviceaccount:azappconfig-system:az-appconfig-k8s-provider --audience api://AzureADTokenExchange
   ```

1. [Grant the user-assigned managed identity **App Configuration Data Reader** role](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vmss#user-assigned-managed-identity) in Azure App Configuration.

1. Set the `spec.auth.workloadIdentity.managedIdentityClientId` property to the client ID of the user-assigned managed identity in the following sample `AzureAppConfigurationProvider` resource and deploy it to the AKS cluster.

    ``` yaml
    apiVersion: azconfig.io/v1beta1
    kind: AzureAppConfigurationProvider
    metadata:
      name: appconfigurationprovider-sample
    spec:
      endpoint: <your-app-configuration-store-endpoint>
      target:
        configMapName: configmap-created-by-appconfig-provider
      auth:
        workloadIdentity:
          managedIdentityClientId: <your-managed-identity-client-id>
    ```

#### Use connection string

1. Create a Kubernetes Secret in the same namespace as the `AzureAppConfigurationProvider` resource and add Azure App Configuration connection string with key *azure_app_configuration_connection_string* in the Secret.

1. Set the `spec.connectionStringReference` property to the name of the Secret in the following sample `AzureAppConfigurationProvider` resource and deploy it to the Kubernetes cluster.

    ``` yaml
    apiVersion: azconfig.io/v1beta1
    kind: AzureAppConfigurationProvider
    metadata:
      name: appconfigurationprovider-sample
    spec:
      connectionStringReference: <your-connection-string-secret-name>
      target:
        configMapName: configmap-created-by-appconfig-provider
    ```

### Key-value selection

Use the `selectors` property to filter the key-values to be downloaded from Azure App Configuration.

The following sample downloads all key-values with no label.

``` yaml
apiVersion: azconfig.io/v1beta1
kind: AzureAppConfigurationProvider
metadata:
  name: appconfigurationprovider-sample
spec:
  endpoint: <your-app-configuration-store-endpoint>
  target:
    configMapName: configmap-created-by-appconfig-provider
```

In following example, two selectors are used to retrieve two sets of key-values, each with unique labels. It's important to note that the values of the last selector take precedence and override any overlapping keys from the previous selectors.

``` yaml
apiVersion: azconfig.io/v1beta1
kind: AzureAppConfigurationProvider
metadata:
  name: appconfigurationprovider-sample
spec:
  endpoint: <your-app-configuration-store-endpoint>
  target:
    configMapName: configmap-created-by-appconfig-provider
  keyValues:
    selectors:
      - keyFilter: app1*
        labelFilter: common
      - keyFilter: app1*
        labelFilter: development
```

### Key prefix trimming

The following sample uses the `trimKeyPrefixes` property to trim two prefixes from key names before adding them to the generated ConfigMap.

``` yaml
apiVersion: azconfig.io/v1beta1
kind: AzureAppConfigurationProvider
metadata:
  name: appconfigurationprovider-sample
spec:
  endpoint: <your-app-configuration-store-endpoint>
  target:
    configMapName: configmap-created-by-appconfig-provider
  keyValues:
    trimKeyPrefixes: [prefix1, prefix2]
```

### Key Vault references

The following sample instructs using a service principal to authenticate with a specific vault and a user-assigned managed identity for all other vaults.

``` yaml
apiVersion: azconfig.io/v1beta1
kind: AzureAppConfigurationProvider
metadata:
  name: appconfigurationprovider-sample
spec:
  endpoint: <your-app-configuration-store-endpoint>
  target:
    configMapName: configmap-created-by-appconfig-provider
  keyValues:
    selectors:
      - keyFilter: app1*
    keyVaults:
      target:
        secretName: secret-created-by-appconfig-provider
      auth:
        managedIdentityClientId: <your-user-assigned-managed-identity-client-id>
        vaults:
          - uri: <your-key-vault-uri>
            servicePrincipalReference: <name-of-secret-containing-service-principal-credentials>
```

### Dynamically refresh ConfigMap and Secret

Setting the `spec.keyValues.refresh` property enables dynamic configuration data refresh in ConfigMap and Secret by monitoring designated key-values. The provider periodically polls the key-values, if there is any value change, provider triggers ConfigMap and Secret refresh in accordance with the present data in Azure App Configuration.

The following sample instructs monitoring two key-values with 1 minute polling interval.

``` yaml
apiVersion: azconfig.io/v1beta1
kind: AzureAppConfigurationProvider
metadata:
  name: appconfigurationprovider-sample
spec:
  endpoint: <your-app-configuration-store-endpoint>
  target:
    configMapName: configmap-created-by-appconfig-provider
  keyValues:
    selectors:
      - keyFilter: app1*
        labelFilter: common
      - keyFilter: app1*
        labelFilter: development
    refresh:
      interval: 1m
      monitoring:
        keyValues:
          - key: sentinelKey
            label: common
          - key: sentinelKey
            label: development
```

### Consume ConfigMap

Applications running in Kubernetes typically consume the ConfigMap either as environment variables or as configuration files. If the `configMapData.type` property is absent or is set to default, the ConfigMap is populated with the itemized list of data retrieved from Azure App Configuration, which can be easily consumed as environment variables. If the `configMapData.type` property is set to json, yaml or properties, data retrieved from Azure App Configuration is grouped into one item with key name specified by the `configMapData.key` property in the generated ConfigMap, which can be consumed as a mounted file.

The following examples show how the data is populated in the generated ConfigMap with different settings of the `configMapData.type` property.

Assuming an App Configuration store has these key-values:

|key|value|
|---|---|
|key1|value1|
|key2|value2|
|key3|value3|

#### [default](#tab/default)

and the `configMapData.type` property is absent or set to `default`,

``` yaml
apiVersion: azconfig.io/v1beta1
kind: AzureAppConfigurationProvider
metadata:
  name: appconfigurationprovider-sample
spec:
  endpoint: <your-app-configuration-store-endpoint>
  target:
    configMapName: configmap-created-by-appconfig-provider
```

the generated ConfigMap will be populated with the following data:

``` yaml
data:
  key1: value1
  key2: value2
  key3: value3
```

#### [json](#tab/json)

and the `configMapData.type` property is set to `json`,

``` yaml
apiVersion: azconfig.io/v1beta1
kind: AzureAppConfigurationProvider
metadata:
  name: appconfigurationprovider-sample
spec:
  endpoint: <your-app-configuration-store-endpoint>
  target:
    configMapName: configmap-created-by-appconfig-provider
    configMapData:
      type: json
      key: appSettings.json
```

the generated ConfigMap will be populated with the following data:

``` yaml
data:
  appSettings.json: >-
    {"key1":"value1","key2":"value2","key3":"value3"}
```

#### [yaml](#tab/yaml)

and the `configMapData.type` property is set to `yaml`,

``` yaml
apiVersion: azconfig.io/v1beta1
kind: AzureAppConfigurationProvider
metadata:
  name: appconfigurationprovider-sample
spec:
  endpoint: <your-app-configuration-store-endpoint>
  target:
    configMapName: configmap-created-by-appconfig-provider
    configMapData:
      type: yaml
      key: appSettings.yaml
```

the generated ConfigMap will be populated with the following data:

``` yaml
data:
  appSettings.yaml: >-
    key1: value1
    key2: value2
    key3: value3
```

#### [properties](#tab/properties)

and the `configMapData.type` property is set to `properties`,

``` yaml
apiVersion: azconfig.io/v1beta1
kind: AzureAppConfigurationProvider
metadata:
  name: appconfigurationprovider-sample
spec:
  endpoint: <your-app-configuration-store-endpoint>
  target:
    configMapName: configmap-created-by-appconfig-provider
    configMapData:
      type: properties
      key: app.properties
```

the generated ConfigMap will be populated with the following data:

``` yaml
data:
  app.properties: >-
    key1=value1
    key2=value2
    key3=value3
```