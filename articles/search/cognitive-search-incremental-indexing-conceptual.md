---
title: Introduction to Incremental Indexing (preview) - Azure Search
description: Configure your AI enrichment pipeline to drive your data to eventual consistency to handle any updates to skills, skillsets, indexers, or data sources
manager: nitinme
author: Vkurpad
services: search
ms.service: cognitive-search
ms.topic: overview
ms.date: 10/11/2019
ms.author: vikurpad

---

# What is incremental indexing in Azure Search?

> [!Note]
> Incremental indexing is in preview and not intended for production use. The [REST API version 2019-05-06-Preview](search-api-preview.md) provides this feature. There is no .NET SDK support at this time.
>

Incremental indexing is a feature of Azure Search that brings a declarative approach to indexing your data. Indexers in Azure Search add documents to your search index from a data source. Indexers track updates to the documents in your data sources and update the index with the new or updated documents from the data source. Indexers can be executed on a recurring schedule ensuring the data source and the index are eventually consistent. Incremental indexing is a new feature that extends change tracking from only the data source to all aspects of the enrichment pipeline. With incremental indexing, the indexer will drive your documents to eventual consistency with your data source, the current version of your skillset, and the indexer.

Indexers have a few key characteristics:

* Data source specific
* State aware
* Can be configured to drive eventual consistency between your data source and index.

Previously, you were responsible for determining the appropriate action when a skill was modified. When you added or update a skill, you had to choose between rerunning all the skills on the entire corpus or tolerating version drift. Rerunning all the skills essentially reset your indexer. Version drift could lead to inconsistencies in which skillset versions incremented your documents.

With the latest update to the preview release of the API (version 2019-05-06-Preview), the indexer's state management has improved. Now, state management includes not only the data source and indexer field mappings but also the skillset, output field mappings, and projections. Incremental indexing vastly improves the efficiency of your enrichment pipeline. It eliminates the painful choice between the cost of a complete rerun and the potential confusion of version drift.

Indexers now track and respond to changes across your enrichment pipeline. Indexers now can determine which skills have changed and run only those that have been modified and their downstream dependent skills.

With incremental indexing, the current version of the enrichment pipeline does the least amount of work to guarantee consistency for all documents in your index. For scenarios where you want full control, you can use fine-grained controls to determine how a change is handled.

## Indexer cache

Incremental indexing adds an indexer cache to the enrichment pipeline. The indexer caches the results from document cracking and the outputs of each skill for every document. When a skillset is updated, only the changed, or downstream, skills are rerun. The updated results are written to the cache and the document is updated in the index and the knowledge store.

Physically, the cache is a storage account. All indexes within a search service may share the same storage account for the indexer cache. Each indexer is assigned a unique and immutable cache identifier.

### Cache configuration

You'll need to set the cache property on the indexer to start benefitting from incremental indexing. Setting this property for the first time will require you to also reset your indexer, `which will result in all documents in your data source being processed again`. The goal of incremental indexing is to make the documents in your index consistent with your data source and the current version of your skillset. Resetting the index is the first step toward this consistency as it eliminates any documents enriched by previous versions of the skillset. The indexer needs to be reset to start with a consistent baseline.

### Cache lifecycle

The lifecycle of the cache is managed by the indexer. If the cache property in the indexer is set to null or the connection string changed, the existing cache is deleted. The cache lifecycle is also tied to the indexer lifecycle. If an indexer is deleted, the associated cache is also deleted.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters":{}
}

```

## Indexer cache mode

The indexer cache can operate in modes where data is only written to the cache or data is written to the cache and used to re-enrich documents.  You can temporarily suspend incremental enrichment by setting the `enableReprocessing` property in the cache to `false`, and later resume incremental enrichment and drive eventual consistency by setting it to `true`. This control is particularly useful when you want to prioritize indexing new documents over ensuring consistency across your corpus of documents.

## Change-detection override

Incremental indexing gives you granular control over all aspects of the enrichment pipeline. This control allows you to deal with situations where a change might have unintended consequences. For example, editing a skillset and updating the URL for a custom skill will result in the indexer invalidating the cached results for that skill. If you're only moving the endpoint to a different VM or redeploying your skill with a new access key, you really don’t want any existing documents reprocessed.

To ensure that the indexer only does enrichments you explicitly require, updates to the skillset can optionally set `disableCacheReprocessingChangeDetection` query string parameter to `true`. When set, this parameter will ensure that only updates to the skillset are committed and the change isn't evaluated for effects on the existing corpus.

## Cache invalidation

The converse of that scenario is one where you may deploy a new version of a custom skill, nothing within the enrichment pipeline changes, but you need a specific skill invalidated and all affected documents reprocessed to reflect the benefits of an updated model. In such instances, you can call the invalidate skills operation on the skillset. The reset skills API accepts a POST request with the list of skill outputs in the cache that should be invalidated. For more information on the reset skills API, see the documentation ["Reset Indexer (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## Change detection

Indexers not only move forward and process new documents but are now able to move backwards and drive previously processed documents to consistency. With this new capability, it's important to understand how changes to your enrichment pipeline components result in indexer work. The indexer will queue work to be done when it identifies a change that is either invalidating or inconsistent.

### Invalidating changes

Invalidating changes are rare but have a significant effect on the state of your enrichment pipeline. An invalidating change is one where the entire cache is no longer valid. An example of an invalidating change is one where your data source is updated. For scenarios when you know that the change should not invalidate the cache, like rotating the key on the storage account, the `ignoreResetRequirement` query string parameter should be set to `true` on the update operation of the specific resource to ensure that the operation is not rejected.

Here is the complete list of changes that would invalidate your cache:

* Change to your data source type
* Change to data source container
* Data source credentials
* Data source change detection policy
* Data sources delete detection policy
* Indexer field mappings
* Indexer parameters
    * Parsing Mode
    * Excluded File Name Extensions
    * Indexed File Name Extensions
    * Index storage metadata only for oversized documents
    * Delimited text headers
    * Delimited text delimiter
    * Document Root
    * Image Action (Changes to how images are extracted)

### Inconsistent changes

An example of inconsistent change is an update to your skillset that modifies a skill. The modification can make a portion of the cache inconsistent. The indexer will identify the work to make things consistent again.  

The complete list of changes resulting in cache inconsistency:

* Skill in the skillset has different type. The odata type of the skill is updated
* Skill-specific parameters updated, for example the url, defaults or other parameters
* Skill outputs changes, the skill returns additional or different outputs
* Skill updates resulting is different ancestry, skill chaining has changed i.e skill inputs
* Any upstream skill invalidation, if a skill that provides an input to this skill is updated
* Updates to the knowledge store projection location, results in reprojecting documents
* Changes to the knowledge store projections, results in reprojecting documents
* Output field mappings changed on an indexer results in reprojecting documents to the index

## Updates To existing APIs

Introducing incremental indexing and enrichment will result in an update to some existing APIs.

### Indexers

Indexers will now expose new properties relating to the cache:

* StorageAccountConnectionString: The connection string to the storage account that will be used to cache the intermediate results.

* CacheId: The `cacheId` is the identifier of the container within the annotationCache storage account that will be used as the cache for this indexer. This cache will be unique to this indexer and if the indexer is deleted and recreated with the same name, the `cacheId` will be regenerated. The `cacheId` cannot be set, it is always generated by the service.

* EnableReprocessing: Set to `true` by default, when set to `false`, documents will continue to be written to the cache, but no existing documents will be reprocessed based on the cache data.

Indexers will also support a new querystring parameter: `ignoreResetRequirement` should be set to `true` when your update action should not invalidate the cache.

### Skillsets

Skillsets will not support any new operations, but will support a new querystring parameter: `disableCacheReprocessingChangeDetection` should be set to `true` when you want no updates to existing documents based on the current action.

### Datasources

Datasources will not support any new operations, but will support a new querystring parameter: `ignoreResetRequirement` should be set to `true` when your update action should not invalidate the cache.

## Best practices

The recommended approach to using incremental indexing is to configure incremental indexing by setting the cache property on a new indexer or reset an existing indexer and set the cache property.

Use the ignoreResetRequirement sparingly as it could lead to unintended inconsistency in your data that will not be detected easily.

## Takeaways

Incremental indexing is a powerful feature in which you declaratively ensure your document and search data are always consistent. As your skills, skillsets, or enrichments evolve, the enrichment pipeline ensures the least possible work is done while still driving your documents to eventual consistency.

## Next steps

Get started with incremental indexing by adding a cache to an existing indexer or add the cache when defining a new indexer.

> [!div class="nextstepaction"]
> [Reference: Create Indexer](cognitive-search-quickstart-blob.md)
