---
title: What is Microsoft Azure Cognitive Services Document Translation?
description: An overview of the cloud-based batch Document Translation service and process.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.custom: event-tier1-build-2022
ms.topic: overview
ms.date: 04/17/2023
ms.author: lajanuar
recommendations: false
---
# What is Document Translation?

Document Translation is a cloud-based feature of the [Azure Translator](../translator-overview.md) service and is part of the Azure Cognitive Service family of REST APIs. The Document Translation API can be used to translate multiple and complex documents across all [supported languages and dialects](../../language-support.md), while preserving original document structure and data format.

## Document Translation key features

| Feature | Description |
| ---------| -------------|
| **Translate large files**| Translate whole documents asynchronously.|
|**Translate numerous files**|Translate multiple files across all supported languages and dialects while preserving document structure and data format.|
|**Preserve source file presentation**| Translate files while preserving the original layout and format.|
|**Apply custom translation**| Translate documents using general and [custom translation](../customization.md#custom-translator) models.|
|**Apply custom glossaries**|Translate documents using custom glossaries.|
|**Automatically detect document language**|Let the Document Translation service determine the language of the document.|
|**Translate documents with content in multiple languages**|Use the autodetect feature to translate documents with content in multiple languages into your target language.|

> [!NOTE]
> When translating documents with content in multiple languages, the feature is intended for complete sentences in a single language. If sentences are composed of more than one language, the content may not all translate into the target language.
> For more information on input requirements, *see* [Document Transaltion request limits](../request-limits.md#document-translation)

## Document Translation development options

You can add Document Translation to your applications using the REST API or a client-library SDK:

* The [**REST API**](reference/rest-api-guide.md). is a language agnostic interface that enables you to create HTTP requests and authorization headers to translate documents.

* The [**client-library SDKs**](client-sdks.md) are language-specific classes, objects, methods, and code that you can quickly use by adding a reference in your project. Currently Document Translation has programming language support for [**C#/.NET**](/dotnet/api/azure.ai.translation.document) and [**Python**](https://pypi.org/project/azure-ai-translation-document/).

## Get started

In our quickstart, you learn how to rapidly get started using Document Translation. To begin, you need an active [Azure account](https://azure.microsoft.com/free/cognitive-services/).  If you don't have one, you can [create a free account](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Start here](get-started-with-document-translation.md "Learn how to use Document Translation with HTTP REST")

## Supported document formats

Document Translation supports the following document file types:

| File type| File extension|Description|
|---|---|--|
|Adobe PDF|`pdf`|Portable document file format. Document Translation uses optical character recognition (OCR) technology to extract and translate text in scanned PDF document while retaining the original layout.|
|Comma-Separated Values |`csv`| A comma-delimited raw-data file used by spreadsheet programs.|
|HTML|`html`, `htm`|Hyper Text Markup Language.|
|Localization Interchange File Format|xlf| A parallel document format, export of Translation Memory systems. The languages used are defined inside the file.|
|Markdown| `markdown`, `mdown`, `mkdn`, `md`, `mkd`, `mdwn`, `mdtxt`, `mdtext`, `rmd`| A lightweight markup language for creating formatted text.|
|M&#8203;HTML|`mthml`, `mht`| A web page archive format used to combine HTML code and its companion resources.|
|Microsoft Excel|`xls`, `xlsx`|A spreadsheet file for data analysis and documentation.|
|Microsoft Outlook|`msg`|An email message created or saved within Microsoft Outlook.|
|Microsoft PowerPoint|`ppt`, `pptx`| A presentation file used to display content in a slideshow format.|
|Microsoft Word|`doc`, `docx`| A text document file.|
|OpenDocument Text|`odt`|An open-source text document file.|
|OpenDocument Presentation|`odp`|An open-source presentation file.|
|OpenDocument Spreadsheet|`ods`|An open-source spreadsheet file.|
|Rich Text Format|`rtf`|A text document containing formatting.|
|Tab Separated Values/TAB|`tsv`/`tab`| A tab-delimited raw-data file used by spreadsheet programs.|
|Text|`txt`| An unformatted text document.|

## Request limits

For detailed information regarding Azure Translator Service request limits, *see* [**Document Translation request limits**](../request-limits.md#document-translation).

### Legacy file types

Source file types are preserved during the document translation with the following **exceptions**:

| Source file extension | Translated file extension|
| --- | --- |
| .doc, .odt, .rtf, | .docx |
| .xls, .ods | .xlsx |
| .ppt, .odp | .pptx |

## Supported glossary formats

Document Translation supports the following glossary file types:

| File type| File extension|Description|
|---|---|--|
|Comma-Separated Values| `csv` |A comma-delimited raw-data file used by spreadsheet programs.|
|Localization Interchange File Format| `xlf` , `xliff`| A parallel document format, export of Translation Memory systems The languages used are defined inside the file.|
|Tab-Separated Values/TAB|`tsv`, `tab`| A tab-delimited raw-data file used by spreadsheet programs.|

## Next steps

> [!div class="nextstepaction"]
> [Get Started with Document Translation](get-started-with-document-translation.md)
