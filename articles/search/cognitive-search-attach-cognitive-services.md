---
title: Attach Cognitive Services to a skillset
titleSuffix: Azure Cognitive Search
description: Learn how to attach a multi-service Cognitive Services resource to an AI enrichment pipeline in Azure Cognitive Search.

author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2021

---

# Attach a Cognitive Services resource to a skillset in Azure Cognitive Search

When configuring an optional [AI enrichment pipeline](cognitive-search-concept-intro.md) in Azure Cognitive Search, you can enrich a limited number of documents free of charge. For larger and more frequent workloads, you should attach a billable [**multi-service Cognitive Services resource**](../cognitive-services/cognitive-services-apis-create-account.md). A multi-service resource references "Cognitive Services" as the offering, rather than individual services, with access granted through a single API key.

A multi-service resource key is specified in a skillset and allows Microsoft to charge you for using these APIs:

+ [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) for image analysis and optical character recognition (OCR)
+ [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) for language detection, entity recognition, sentiment analysis, and key phrase extraction
+ [Text Translation](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

> [!NOTE]
> AI enrichment offers a small quantity of free processing so that you can complete quickstarts, tutorials, or limited tests without having to attach a Cognitive Services resource. Free enrichments are restricted to 20 documents per day, per indexer. You can [reset the indexer](search-howto-run-reset-indexers.md) to reset the counter if you want to repeat an exercise.

## [**Azure portal**](#tab/cogkey-portal)

1. Create a [multi-service Cognitive Services resource](../cognitive-services/cognitive-services-apis-create-account.md) in the [same region](#same-region-requirement) as your search service.

1. Add the key to a skillset definition:

   + If using the [Import data wizard](search-import-data-portal.md), enter the key in the second step, "Add AI enrichments".

   + If adding the key to a new or existing skillset, provide the key in the **Cognitive Services** tab.

   :::image type="content" source="media/cognitive-search-attach-cognitive-services/attach-existing2.png" alt-text="Screenshot of the key page" border="true":::

## [**REST**](#tab/cogkey-rest)

1. Create a [multi-service Cognitive Services resource](../cognitive-services/cognitive-services-apis-create-account.md) in the [same region](#same-region-requirement) as your search service.

1. Create or update a skillset, specifying `cognitiveServices` section in the body of the [skillset request](/rest/api/searchservice/create-skillset):

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## [**.NET SDK**](#tab/cogkey-dotnet)

The following code snippet is from [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/tutorial-ai-enrichment/v11/Program.cs), trimmed for brevity.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();

string searchServiceUri = configuration["SearchServiceUri"];
string adminApiKey = configuration["SearchServiceAdminApiKey"];
string cognitiveServicesKey = configuration["CognitiveServicesKey"];

SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));

// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

---

## How the key is used

During AI enrichment, Cognitive Search calls the Cognitive Services APIs for [built-in skills](cognitive-search-predefined-skills.md) that are based on Computer Vision, Text Translation, and Text Analytics. Built-in skills that make backend calls to Cognitive Services include Entity Linking, Entity Recognition, Image Analysis, Key Phrase Extraction, Language Detection, OCR, PII Detection, Sentiment, or Text Translation.

You can omit the key and the Cognitive Services section for skillsets that consist solely of custom skills or utility skills (Conditional, Document Extraction, Shaper, Text Merge, Text Split). You can also omit the section if your usage of billable skills is under 20 transactions per indexer per day.

The key that you provide in a skillset definition is used for billing, but not connections. Internally, a search service connects over the internal network to a Cognitive Services resource that's co-located in the [same physical region](https://azure.microsoft.com/global-infrastructure/services/?products=search). A key is required for skillsets that exceed 20 API calls per day, per indexer.

### Exceptions and special cases

+ Skills that do not call Cognitive Services (namely, Conditional, Shaper, Text Merge, and Text Split skills) are not billable. 

+ [Custom Entity Lookup](cognitive-search-skill-custom-entity-lookup.md) is metered by Azure Cognitive Search, not Cognitive Services, but it requires a Cognitive Services resource key to unlock transactions beyond 20 per indexer, per day. For this skill only, the resource key unblocks the number of transactions, but is unrelated to billing.

### Other costs of AI enrichment

Image extraction is an Azure Cognitive Search operation that occurs when documents are cracked prior to enrichment. Image extraction is billable on all tiers, with the exception of 20 free daily extractions on the free tier. Image extraction costs apply to image files inside blobs, embedded images in other files (PDF and other app files), and for images extracted using [Document Extraction](cognitive-search-skill-document-extraction.md). For image extraction pricing, see the [Azure Cognitive Search pricing page](https://azure.microsoft.com/pricing/details/search/).

Text extraction also occurs during the [document cracking](search-indexer-overview.md#document-cracking) phrase. It is not billable.

> [!TIP]
> To lower the cost of skillset processing, enable [incremental enrichment (preview)](cognitive-search-incremental-indexing-conceptual.md) to cache and reuse any enrichments that are unaffected by changes made to a skillset. Caching requires Azure Storage (see [pricing](https://azure.microsoft.com/pricing/details/storage/blobs/) but the cumulative cost of skillset execution is lower if existing enrichments can be reused, especially for skillsets that use image extraction and analysis.

## Same-region requirement

Both Cognitive Search and Cognitive Services must exist within the same physical region, as indicated on the [product availability](https://azure.microsoft.com/global-infrastructure/services/?products=search) page. Most regions that offer Cognitive Search also offer Cognitive Services.

If you attempt AI enrichment in region that doesn't have both services, you'll see this message: "Provided key is not a valid CognitiveServices type key for the region of your search service."

> [!NOTE]
> Some built-in skills are based on non-regional Cognitive Services (for example, the [Text Translation Skill](cognitive-search-skill-text-translation.md)). Using a non-regional skill means that your request might be serviced in a region other than the Azure Cognitive Search region. For more information non-regional services, see the [Cognitive Services product by region](https://aka.ms/allinoneregioninfo) page.

## Example: Estimate costs

To estimate the costs associated with Cognitive Search indexing, start with an idea of what an average document looks like so you can run some numbers. For example, you might approximate:

+ 1,000 PDFs.
+ Six pages each.
+ One image per page (6,000 images).
+ 3,000 characters per page.

Assume a pipeline that consists of document cracking of each PDF, image and text extraction, optical character recognition (OCR) of images, and entity recognition of organizations.

The prices shown in this article are hypothetical. They're used to illustrate the estimation process. Your costs could be lower. For the actual prices of transactions, see See [Cognitive Services pricing](https://azure.microsoft.com/pricing/details/cognitive-services).

1. For document cracking with text and image content, text extraction is currently free. For 6,000 images, assume $1 for every 1,000 images extracted. That's a cost of $6.00 for this step.

2. For OCR of 6,000 images in English, the OCR cognitive skill uses the best algorithm (DescribeText). Assuming a cost of $2.50 per 1,000 images to be analyzed, you would pay $15.00 for this step.

3. For entity extraction, you'd have a total of three text records per page. Each record is 1,000 characters. Three text records per page multiplied by 6,000 pages equals 18,000 text records. Assuming $2.00 per 1,000 text records, this step would cost $36.00.

Putting it all together, you'd pay about $57.00 to ingest 1,000 PDF documents of this type with the described skillset.

## Next steps

+ [Azure Cognitive Search pricing page](https://azure.microsoft.com/pricing/details/search/)
+ [How to define a skillset](cognitive-search-defining-skillset.md)
+ [Create Skillset (REST)](/rest/api/searchservice/create-skillset)
+ [How to map enriched fields](cognitive-search-output-field-mapping.md)
