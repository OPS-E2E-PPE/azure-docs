---
title: include file
description: A quickstart on how to use Number Management C# SDK to configure direct routing.
services: azure-communication-services
author: boris-bazilevskiy

ms.service: azure-communication-services
ms.subservice: pstn
ms.date: 03/11/2023
ms.topic: include
ms.custom: include file
ms.author: nikuklic
---

<!-- ## Sample code

You can download the sample app from [GitHub](https://github.com/link.  -->

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- An active Communication Services resource and connection string. [Create a Communication Services resource](../../create-communication-resource.md).
- The latest version [.NET Core client library](https://dotnet.microsoft.com/download/dotnet-core) for your operating system.
- Fully Qualified Domain Name (FQDN) and port number of a Session Border Controller (SBC) in operational telephony system.
- [Verified domain name](../../../how-tos/telephony/domain-validation.md) of the SBC FQDN.

## Create a new C# application

In a console window (such as cmd, PowerShell, or Bash), use the `dotnet new` command to create a new console app.

```console
    dotnet new console -o DirectRoutingQuickstart
```

This command creates a simple "Hello World" C# project with a single source file: **Program.cs**.
Change your directory to the newly created app folder and use the dotnet build command to compile your application.

``` console
    cd DirectRoutingQuickstart
    dotnet build
```

## Install the package

While still in the application directory, install the Azure Communication PhoneNumbers client library for .NET package by using the dotnet add package command.

``` console
    dotnet add package Azure.Communication.PhoneNumbers --version 1.1.0
```

Add a using directive to the top of **Program.cs** to include the namespaces.

``` csharp
using Azure.Communication.PhoneNumbers.SipRouting;
```

## Authenticate the client

Phone Number clients can be authenticated using [connection string from an Azure Communication Services resource](../../create-communication-resource.md#access-your-connection-strings-and-service-endpoints). 

``` csharp
// Get a connection string to our Azure Communication Services resource.
var connectionString = "<connection_string>";
var client = new SipRoutingClient(connectionString);
```

## Setup direct routing configuration

Direct routing configuration consists of:

1. Domain ownership verification - see [prerequisites](#prerequisites)
1. Creating trunks (adding SBCs)
1. Creating voice routes

### Create or update Trunks

Azure Communication Services direct routing allows communication with registered SBCs only. To register an SBC you need its FQDN and port.

``` csharp
// Register your SBCs by providing their fully qualified domain names and port numbers.
var usSbcFqdn = "sbc.us.contoso.com";
var euSbcFqdn = "sbc.eu.contoso.com";
var sbcPort = 5061;

var usTrunk = new SipTrunk(usSbcFqdn, sbcPort);
var euTrunk = new SipTrunk(euSbcFqdn, sbcPort);

await client.SetTrunksAsync(new List<SipTrunk> { usTrunk, euTrunk });
```

### Create or update routes

> [!NOTE]
> Order of routes does matter, as it determines priority of routes. The first route that matches the regex will be picked for a call.

For outbound calling routing rules should be provided. Each rule consists of two parts: regex pattern that should match dialed phone number and FQDN of a registered trunk where call is routed. In this example, we create one route for numbers that start with `+1` and a second route for numbers that start with just `+`

``` csharp
var usRoute = new SipTrunkRoute("UsRoute", "^\\+1(\\d{10})$", trunks: new List<string> { usSbcFqdn });
var defaultRoute = new SipTrunkRoute("DefaultRoute", "^\\+\\d+$", trunks: new List<string> { usSbcFqdn, euSbcFqdn });

await client.SetRoutesAsync(new List<SipTrunkRoute> { usRoute, defaultRoute });
```

### Updating existing configuration

Properties of specific Trunk can be updated by overwriting the record with the same FQDN. For example, you can set new SBC Port value.

``` csharp
var usTrunk = new SipTrunk("sbc.us.contoso.com", 5063);
await client.SetTrunkAsync(usTrunk);
```

> [!IMPORTANT]
>The same method is used to create and update routing rules. When updating routes, all routes should be sent in single update and routing configuration will be fully overwritten by the new one. 

### Removing a direct routing configuration

You can't edit or remove single voice route. Entire voice routing configuration should be overwritten. Here's the example of an empty list that removes all the routes and trunks:

``` csharp
//delete all configured voice routes
await client.SetRoutesAsync(new List<SipTrunkRoute>());

//delete all trunks
await client.SetTrunksAsync(new List<SipTrunk>());
``` 

You can delete a single trunk (SBC), if it isn't used in any voice route. If SBC is listed in any voice route, that route should be deleted first.

``` csharp
await client.DeleteTrunkAsync("sbc.us.contoso.com");
```

<!-- All direct routing configuration can be deleted by overriding routes and trunks configuration with a new configuration or an empty list. Same methods are used in "Create or Update Trunks" and "Create or Update Routes" sections. -->

> [!NOTE]
> More usage examples for SipRoutingClient can be found [here](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/communication/Azure.Communication.PhoneNumbers/README.md#siproutingclient).