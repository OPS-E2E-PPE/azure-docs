---
title: Generate embeddings
titleSuffix: Azure Cognitive Search
description: Learn how to generate embeddings for downstream indexing into an Azure Cognitive Search index.

author: farzad528
ms.author: fsunavala
ms.service: cognitive-search
ms.topic: how-to
ms.date: 06/29/2023
---

# Create and use embeddings for search queries and documents

> [!IMPORTANT]
> Vector search is in public preview under [supplemental terms of use](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). It's available through the Azure portal, preview REST API, and [alpha SDKs](https://github.com/Azure/cognitive-search-vector-pr#readme).

Cognitive Search doesn't host vectorization models, so one of your challenges is creating embeddings for query inputs and outputs. You can use any embedding model, but this article assumes Azure OpenAI embeddings models. Demos in the [sample repository](https://github.com/Azure/cognitive-search-vector-pr/tree/main) tap the [similarity embedding models](/azure/cognitive-services/openai/concepts/models#embeddings-models) of Azure OpenAI.

Dimension attributes have a minimum of 2 and a maximum of 2048 dimensions per vector field.

## How models are used

+ Query inputs require that you submit user-provided input to an embedding model that quickly converts human readable text into a vector. Optimizing for speed is the objective. 

  + We used **text-embedding-ada-002** to generate text embeddings and [Image Retrieval REST API](/rest/api/computervision/2023-02-01-preview/image-retrieval/vectorize-image) for image embeddings.

  + To increase the success rate of generation, we slowed the rate at which calls to the model are made. For the Python demo, we used [tenacity](https://pypi.org/project/tenacity/).

+ Query outputs are any matching documents found in a search index. Your search index must have been previously loaded with documents having one or more vector fields with embeddings. Whatever model you used for indexing, use the same model for queries.

## Create resources in the same region

If you want resources in the same region, start with:

1. [A region for the similarity embedding model](/azure/cognitive-services/openai/concepts/models#embeddings-models-1), currently in Europe and the United States.

1. [A region for Cognitive Search](https://azure.microsoft.com/explore/global-infrastructure/products-by-region/?products=cognitive-search). 

1. To support hybrid queries that include [semantic ranking](semantic-how-to-query-request.md), or if you want to try machine learning model integration using a [custom skill](cognitive-search-custom-skill-interface.md) in an [AI enrichment pipeline](cognitive-search-concept-intro.md), note the regions that provide those features.

## Generate an embedding for an improvised query

The Postman collection assumes that you already have a vector query. Here's some Python code for generating an embedding that you can paste into the "values" property of a vector query.

```python
! pip install openai

import openai

openai.api_type = "azure"
openai.api_key = "YOUR-API-KEY"
openai.api_base = "https://YOUR-OPENAI-RESOURCE.openai.azure.com"
openai.api_version = "2022-12-01"

response = openai.Embedding.create(
    input="How do I use Python in VSCode?",
    engine="text-embedding-ada-002"
)
embeddings = response['data'][0]['embedding']
print(embeddings)
```

## Tips and recommendations for embedding model integration

<!-- + Python and JavaScript demos offer more scalability than the REST APIs for generating embeddings. As of this writing, the REST API doesn't currently support batching. -->
<!-- 
+ We've done proof-of-concept testing with indexers and skillsets, where a custom skill calls a machine learning model to generate embeddings. There's currently no tutorial or walkthrough, but we intend to provide this content as part of the public preview launch, if not sooner. -->
<!-- 
+ We've done proof-of-concept testing of embeddings for a thousand images using [image retrieval vectorization in Cognitive Services](/azure/cognitive-services/computer-vision/how-to/image-retrieval). We hope to provide a demo of this soon. -->

+ Similarity search expands your options for searchable content, for example by matching image content with text content, or matching across multiple languages. But not every query is improved with vector search. Keyword matching with BM25 is cheaper, faster, and easier, so integrate vector search only where it adds value.

## Next steps

+ [Understanding embeddings in Azure OpenAI Service](/azure/cognitive-services/openai/concepts/understand-embeddings)
+ [Learn how to generate embeddings](/azure/cognitive-services/openai/how-to/embeddings?tabs=console)
+ [Tutorial: Explore Azure OpenAI Service embeddings and document search](/azure/cognitive-services/openai/tutorials/embeddings?tabs=command-line)