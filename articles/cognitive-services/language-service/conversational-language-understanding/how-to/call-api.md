---
title: Send prediction requests to a conversational language understanding deployment
titleSuffix: Azure Cognitive Services
description: Learn about sending prediction requests for conversational language understanding.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 06/03/2022
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
---

# Send prediction requests to a deployment

After the deployment is added successfully, you can query the deployment for intent and entities predictions from your utterance based on the model you assigned to the deployment.
You can query the deployment programmatically through the [prediction API](https://aka.ms/ct-runtime-swagger) or through the client libraries (Azure SDK). 

## Test deployed model

You can use Language Studio to submit an utterance, get predictions and visualize the results.

[!INCLUDE [Test model](../includes/language-studio/test-model.md)]

---

## Send a conversational language understanding request

# [Language Studio](#tab/language-studio)

[!INCLUDE [Get prediction URL](../includes/language-studio/get-prediction-url.md)]

# [REST APIs](#tab/REST-APIs)

First you will need to get your resource key and endpoint:

[!INCLUDE [Get keys and endpoint Azure Portal](../includes/get-keys-endpoint-azure.md)]

### Query your model

[!INCLUDE [Query model](../includes/rest-api/query-model.md)]

# [Client libraries (Azure SDK)](#tab/azure-sdk)

### Use the client libraries (Azure SDK)

You can also use the client libraries provided by the Azure SDK to send requests to your model. 

> [!NOTE]
> The client library for conversational language understanding is only available for:
> * .NET
> * Python

1. Go to your resource overview page in the [Azure portal](https://portal.azure.com/#home)

2. From the menu on the left side, select **Keys and Endpoint**. Use endpoint for the API requests and you will need the key for `Ocp-Apim-Subscription-Key` header.

    :::image type="content" source="../../custom-text-classification/media/get-endpoint-azure.png" alt-text="A screenshot showing a key and endpoint in the Azure portal." lightbox="../../custom-text-classification/media/get-endpoint-azure.png":::


3. Download and install the client library package for your language of choice:
    
    |Language  |Package version  |
    |---------|---------|
    |.NET     | [1.0.0-beta.3 ](https://www.nuget.org/packages/Azure.AI.Language.Conversations/1.0.0-beta.3)        |
    |Python     | [1.1.0b1](https://pypi.org/project/azure-ai-language-conversations/)         |
    
4. After you've installed the client library, use the following samples on GitHub to start calling the API.
    
    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/cognitivelanguage/Azure.AI.Language.Conversations/samples)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cognitivelanguage/azure-ai-language-conversations/samples)
    
5. See the following reference documentation for more information:
    
    * [C#](/dotnet/api/azure.ai.language.conversations?view=azure-dotnet-preview&preserve-view=true)
    * [Python](/python/api/azure-ai-language-conversations/azure.ai.language.conversations?view=azure-python-preview&preserve-view=true)
    
---

## Next steps

* [Conversational language understanding overview](../overview.md)
