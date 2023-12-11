---
title: Vision SDK Overview
titleSuffix: Azure AI services
description: This page gives you an overview of the Azure AI Vision SDK for Image Analysis.
#services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: azure-ai-vision
ms.topic: overview
ms.date: 08/01/2023
ms.author: pafarley
ms.custom: devx-track-csharp
---

# Vision SDK overview

The Vision SDK (preview) provides a convenient way to access the Image Analysis service using [version 4.0 of the REST APIs](https://aka.ms/vision-4-0-ref).

[!INCLUDE [License Notice](../includes/setup-sdk/license-notice-sdk.md)]

## Supported languages

The Vision SDK supports the following languages and platforms:

| Programming language | Quickstart | API Reference | Platform support |
|----------------------|------------|-----------|------------------|
| C# <sup>1</sup> | [quickstart](../quickstarts-sdk/image-analysis-client-library-40.md?pivots=programming-language-csharp)  | [reference](/dotnet/api/azure.ai.vision.imageanalysis) | Windows, UWP, Linux |
| C++ <sup>2</sup> | [quickstart](../quickstarts-sdk/image-analysis-client-library-40.md?pivots=programming-language-cpp)  | [reference](/cpp/cognitive-services/vision) | Windows, Linux |
| Python | [quickstart](../quickstarts-sdk/image-analysis-client-library-40.md?pivots=programming-language-python) | [reference](/python/api/azure-ai-vision) | Windows, Linux |
| Java | [quickstart](../quickstarts-sdk/image-analysis-client-library-40.md?pivots=programming-language-java) | [reference](/java/api/com.azure.ai.vision.imageanalysis) | Windows, Linux |


<sup>1 The Vision SDK for C# is based on .NET Standard 2.0. See [.NET Standard](/dotnet/standard/net-standard?tabs=net-standard-2-0#net-implementation-support) documentation.</sup>

<sup>2 ANSI-C isn't a supported programming language for the Vision SDK.</sup>

## GitHub samples

Numerous samples are available in the [Azure-Samples/azure-ai-vision-sdk](https://github.com/Azure-Samples/azure-ai-vision-sdk) repository on GitHub.

## Getting help

If you need assistance using the Vision SDK or would like to report a bug or suggest new features, open a [GitHub issue in the samples repository](https://github.com/Azure-Samples/azure-ai-vision-sdk/issues). The SDK development team monitors these issues.

Before you create a new issue:
* Make sure you first scan to see if a similar issue already exists.
* Find the sample closest to your scenario and run it to see if you see the same issue in the sample code.

## Release notes

See the [release notes on GitHub](https://github.com/Azure-Samples/azure-ai-vision-sdk/blob/main/README.md#news).



## Next steps

- [Install the SDK](./install-sdk.md)
- [Try the Image Analysis Quickstart](../quickstarts-sdk/image-analysis-client-library-40.md)
