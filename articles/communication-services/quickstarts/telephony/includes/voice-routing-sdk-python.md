---
title: include file
description: A quickstart on how to use Number Management Python SDK to configure direct routing.
services: azure-communication-services
author: boris-bazilevskiy

ms.service: azure-communication-services
ms.subservice: pstn
ms.date: 03/11/2023
ms.topic: include
ms.custom: include file
ms.author: nikuklic
---

> [!NOTE]
> Find the finalized code for this quickstart on [GitHub](https://github.com/link)

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 3.7+.
- A deployed Communication Services resource and a connection string. [Create a Communication Services resource](../../create-communication-resource.md).
- Fully Qualified Domain Name (FQDN) and port number of a Session Border Controller (SBC) in operational telephony system.
- [Verified domain name](../../../how-tos/telephony/domain-validation.md) of the SBC FQDN.

## Setting up

### Create a new Python application

Open your terminal or command window and create a new directory for your app, then navigate to it.

```console
mkdir direct-routing-quickstart && cd direct-routing-quickstart
```

Use a text editor to create a file called **direct_routing_sample.py** in the project root directory and add the following code. We're adding the remaining quickstart code in the following sections.

```python
import os
from azure.communication.phonenumbers.siprouting import SipRoutingClient, SipTrunk, SipTrunkRoute

try:
   print('Azure Communication Services - Direct Routing Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### Install the package

While still in the application directory, install the Azure Communication Services Administration client library for Python package by using the `pip install` command.

```console
pip install azure-communication-phonenumbers==1.1.0
```

## Authenticate the client

The `SipRoutingClient` is enabled to use Azure Active Directory Authentication. Using the `DefaultAzureCredential` object is the easiest way to get started with Azure Active Directory and you can install it using the `pip install` command.

```console
pip install azure-identity
```

Creating a `DefaultAzureCredential` object requires you to have `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`, and `AZURE_TENANT_ID` already set as environment variables with their corresponding values from your registered Azure AD application.

For a quick ramp-up on how to get these environment variables, you can follow the [Set up service principals from CLI quickstart](../../identity/service-principal-from-cli.md).

Once you've installed the `azure-identity` library, we can continue authenticating the client.

```python
import os
from azure.communication.phonenumbers.siprouting import SipRoutingClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Direct Routing Quickstart')
    credential = DefaultAzureCredential()
    sip_routing_client = SipRoutingClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

Alternatively, you can use the endpoint and access key from the communication resource to authenticate.

```python
import os
from azure.communication.phonenumbers.siprouting import SipRoutingClient

# You can find your connection string from your resource in the Azure portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Direct Routing Quickstart')
    sip_routing_client = SipRoutingClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## Setup direct routing configuration

- Domain ownership verification - see [prerequisites](#prerequisites)
- Creating trunks (adding SBCs)
- Creating voice routes

### Create or Update Trunks

Register your SBCs by providing their fully qualified domain names and port numbers.

```python
new_trunks = [SipTrunk(fqdn="sbc.us.contoso.com", sip_signaling_port=1234), SipTrunk(fqdn="sbc.eu.contoso.com", sip_signaling_port=1234)]
sip_routing_client.set_trunks(new_trunks)
```

### Create or Update Routes

> [!NOTE]
> Order of routes does matter, as it determines priority of routes. The first route that matches the regex will be picked for a call.

For outbound calling routing rules should be provided. Each rule consists of two parts: regex pattern that should match dialed phone number and FQDN of a registered trunk where call is routed. In this example, we create one route for numbers that start with `+1` and a second route for numbers that start with just `+`.

```python
us_route = SipTrunkRoute(name="UsRoute", description="Handle US numbers '+1'", number_pattern="^\\+1(\\d{10})$", trunks=["sbc.us.contoso.com"])
def_route = SipTrunkRoute(name="DefaultRoute", description="Handle all numbers", number_pattern="^\\+\\d+$", trunks=["sbc.us.contoso.com","sbc.eu.contoso.com"])
new_routes = [us_route, def_route]
sip_routing_client.set_routes(new_routes)
```

### Updating existing configuration

Properties of specific Trunk can be updated by overriding the record with the same FQDN. For example, you can set new SBC Port value.

``` python
new_trunk = SipTrunk(fqdn="sbc.us.contoso.com", sip_signaling_port=5063)
sip_routing_client.set_trunk(new_trunk)
```

> [!IMPORTANT]
>The same method is used to create and update routing rules. When updating routes, all routes should be sent in single update and routing configuration will be fully overwritten by the new one. 

### Removing a direct routing configuration

You can't edit or remove single voice route. Entire voice routing configuration should be overwritten. Here's the example of an empty list that removes all the routes and trunks:

``` python
#delete all configured voice routes
print('Deleting all routes...')
sip_routing_client.set_routes([])

#delete all trunks
print('Deleting all trunks...')
sip_routing_client.set_trunks([])
``` 

You can delete a single trunk (SBC), if it isn't used in any voice route. If SBC is listed in any voice route, that route should be deleted first.

``` python
sip_routing_client.delete_trunk("sbc.us.contoso.com")
```

<!-- All Direct Routing configuration can be deleted by overriding routes and trunks configudation with new configuration or empty lists. Same methods are used as in "Create or Update Trunks" and "Create or Update Routes" sections. -->

## Run the code

From a console prompt, navigate to the directory containing the **direct_routing_sample.py** file, then execute the following Python command to run the app.

```console
python direct_routing_sample.py
```

> [!NOTE]
> More usage examples for SipRoutingClient can be found [here](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/communication/azure-communication-phonenumbers/README.md).