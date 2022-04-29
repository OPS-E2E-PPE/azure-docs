---
title: Configure the minimum TLS version for a Service Bus namespace using ARM
titleSuffix: Service Bus
description: Configure an Azure Service Bus namespace to use a minimum version of Transport Layer Security (TLS).
services: service-bus
author: EldertGrootenboer

ms.service: service-bus-messaging
ms.topic: article
ms.date: 04/22/2022
ms.author: egrootenboer
---

# Configure the minimum TLS version for a Service Bus namespace using ARM (Preview)

To configure the minimum TLS version for a Service Bus namespace, set the  `MinimumTlsVersion`  version property. When you create a Service Bus namespace with an Azure Resource Manager template, the `MinimumTlsVersion` property is set to 1.2 by default, unless explicitly set to another version.

> [!NOTE]
> Namespaces created using an api-version prior to 2022-01-01-preview will have 1.0 as the value for `MinimumTlsVersion`. This behavior was the prior default, and is still there for backwards compatibility.

## Create a template to configure the minimum TLS version

To configure the minimum TLS version for a Service Bus namespace with a template, create a template with the  `MinimumTlsVersion`  property set to 1.0, 1.1, or 1.2. The following steps describe how to create a template in the Azure portal.

1. In the Azure portal, choose  **Create a resource**.
2. In  **Search the Marketplace** , type  **custom deployment** , and then press  **ENTER**.
3. Choose **Custom deployment (deploy using custom templates) (preview)**, choose  **Create** , and then choose  **Build your own template in the editor**.
4. In the template editor, paste in the following JSON to create a new namespace and set the minimum TLS version to TLS 1.2. Remember to replace the placeholders in angle brackets with your own values.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "serviceBusNamespaceName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "apiVersion": "2022-01-01-preview",
            "location": "westeurope",
            "properties": {
                "minimumTlsVersion": "1.2"
            },
            "dependsOn": [],
            "tags": {}
            }
        ]
    }
    ```

5. Save the template.
6. Specify resource group parameter, then choose the  **Review + create**  button to deploy the template and create a namespace with the  `MinimumTlsVersion`  property configured.

> [!NOTE]
> After you update the minimum TLS version for the Service Bus namespace, it may take up to 30 seconds before the change is fully propagated.

Configuring the minimum TLS version requires api-version 2022-01-01-preview or later of the Azure Service Bus resource provider.

## Check the minimum required TLS version for multiple namespaces

To check the minimum required TLS version across a set of Service Bus namespaces with optimal performance, you can use the Azure Resource Graph Explorer in the Azure portal. To learn more about using the Resource Graph Explorer, see [Quickstart: Run your first Resource Graph query using Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md).

Running the following query in the Resource Graph Explorer returns a list of Service Bus namespaces and displays the minimum TLS version for each namespace:

```kusto
resources 
| where type =~ 'Microsoft.ServiceBus/namespaces'
| extend minimumTlsVersion = parse\_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

## Test the minimum TLS version from a client

To test that the minimum required TLS version for a Service Bus namespace forbids calls made with an older version, you can configure a client to use an older version of TLS. For more information about configuring a client to use a specific version of TLS, see [Configure Transport Layer Security (TLS) for a client application](transport-layer-security-configure-client-version.md).

When a client accesses a Service Bus namespace using a TLS version that does not meet the minimum TLS version configured for the namespace, Azure Service Bus returns error code 400 error (Bad Request) and a message indicating that the TLS version that was used is not permitted for making requests against this Service Bus namespace.

> [!NOTE]
> When you configure a minimum TLS version for a Service Bus namespace, that minimum version is enforced at the application layer. Tools that attempt to determine TLS support at the protocol layer may return TLS versions in addition to the minimum required version when run directly against the Service Bus namespace endpoint.

## Next steps

See the following documentation for more information.

- [Enforce a minimum required version of Transport Layer Security (TLS) for requests to a Service Bus namespace](transport-layer-security-enforce-minimum-version.md)
- [Configure Transport Layer Security (TLS) for a Service Bus client application](transport-layer-security-configure-client-version.md)
- [Use Azure Policy to audit for compliance of minimum TLS version for a Service Bus namespace](transport-layer-security-audit-minimum-version.md)