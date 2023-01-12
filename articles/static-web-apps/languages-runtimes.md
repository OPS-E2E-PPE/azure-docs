---
title: Supported languages and runtimes in Azure Static Web Apps
description: Supported languages and runtimes in Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 08/30/2022
ms.author: cshoe
---

# Supported languages and runtimes in Azure Static Web Apps

Azure Static Web Apps features two different places where runtime and language versions are important, on the front end and for the API.

| Runtime type | Description |
|--|--|
| [Front end](#front-end) | The version responsible for running the website's build steps that build the front end application. |
| [API](#api) | The version and runtime of Azure Functions used in your web application. |

## Front end

You can specify the version used to build the front end of your static web app. Configuring a non-default version is often only necessary if you need to target older versions.

You can specify the runtime version that builds the front end of your static web app in the _package.json_ file in the `engines` section of the file.

```json
{
  ...
  "engines": {
   "node": ">=14.0.0"
  }
}
```

## API

The APIs in Azure Static Web Apps are supported by Azure Functions. Refer to the [Azure Functions supported languages and runtimes](../azure-functions/supported-languages.md) for details.

The following versions are supported for managed functions in Static Web Apps. If your application requires a version not listed, considering [bringing your own functions](./functions-bring-your-own.md).

[!INCLUDE [Languages and runtimes](../../includes/static-web-apps-languages-runtimes.md)]

## Deprecations

> [!NOTE]
> Now that Azure Functions v3 is retired, Static Web Apps uses Azure Functions v4 for API runtime support for Python 3.8. Redeploy your app to enable this change. While not recommended, you can revert back to v3 by setting the environment variable `USEV3_FOR_PYTHON38` to `true`. 

The following runtimes are deprecated in Azure Static Web Apps. For more information about changing your runtime, see [Specify API language runtime version in Azure Static Web Apps](https://azure.microsoft.com/updates/generally-available-specify-api-language-runtime-version-in-azure-static-web-apps/) and [Migrate apps from Azure Functions version 3.x to version 4.x](../azure-functions/migrate-version-3-version-4.md).

- .NET Core 3.1
- Node.js 12.x
