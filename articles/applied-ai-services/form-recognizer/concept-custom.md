---
title: Form Recognizer custom and composed models
titleSuffix: Azure Applied AI Services
description: Learn to create, use, and manage Form Recognizer custom and composed models.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/15/2022
ms.author: vikurpad
recommendations: false
---
# Form Recognizer custom models

Form Recognizer uses advanced machine learning technology to detect and extract information from forms and documents and returns the extracted data in a structured JSON output. With Form Recognizer, you can use pre-built or pre-trained models or you can train standalone custom models. Standalone custom models can be combined to create composed models.

To create a custom model, you label a dataset of documents with the values you want extracted and train the model on the labeled dataset. You only need five examples of the same form or document type to get started.

## Custom model types

Custom models can be one of two types, [**custom template**](concept-custom-template.md ) or [**custom neural**](concept-custom-neural.md) models. The labeling and training process for both models is identical, but the models differ as follows:

### Custom template model

 The custom template model relies on a consistent visual template to extract the labeled data. The accuracy of your model is affected by variances in the visual structure of your documents. Questionnaires or application forms are examples of consistent visual templates.Your training set will consist of structured documents where the formatting and layout are static and constant from one document instance to the next. Custom template  models support key-value pairs, selection marks, tables, signature fields and regions and can be trained on documents in any of the [supported languages](language-support.md). For more information, *see* [custom template models](concept-custom-template.md ).

> [!TIP]
>
>To confirm that your training documents present a consistent visual template, remove all the user-entered data from each form in the set. If the blank forms are identical in appearance, they represent a consistent visual template.
>
> For more information, *see* [Interpret and improve accuracy and confidence for custom models](concept-accuracy-confidence.md).

### Custom neural model

The custom neural model is a deep learning model type relies on a base model trained on a large collection of labeled documents using key-value pairs. This model is then fine-tuned or adapted to your data when you train the model with a labeled dataset. Custom neural models support structured, semi-structured, and unstructured documents to extract fields. Custom neural models currently support English-language documents. When choosing between the two model types, start with a neural model if it meets your functional needs. See [neural models](concept-custom-neural.md) to learn more about custom document models.

## Model features

The table below compares custom template and custom neural features:

## Custom model tools

The following tools are supported by Form Recognizer v2.1:

| Feature | Resources |
|----------|-------------------------|
|Custom model| <ul><li>[Form Recognizer labeling tool](https://fott-2-1.azurewebsites.net)</li><li>[REST API](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-forms-with-a-custom-model)</li><li>[Client library SDK](quickstarts/try-sdk-rest-api.md)</li><li>[Form Recognizer Docker container](containers/form-recognizer-container-install-run.md?tabs=custom#run-the-container-with-the-docker-compose-up-command)</li></ul>|

The following tools are supported by Form Recognizer v3.0:

| Feature | Resources |
|----------|-------------|
|Custom model| <ul><li>[Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/customform/projects)</li><li>[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[C# SDK](quickstarts/try-v3-csharp-sdk.md)</li><li>[Python SDK](quickstarts/try-v3-python-sdk.md)</li></ul>|

### Try Form Recognizer

See how data is extracted from your specific or unique documents by using custom models. You need the following resources:

* An Azure subscription. You can [create one for free](https://azure.microsoft.com/free/cognitive-services/).
* A [Form Recognizer instance](https://portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) in the Azure portal. You can use the free pricing tier (`F0`) to try the service. After your resource deploys, select **Go to resource** to get your API key and endpoint.

  :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot that shows the keys and endpoint location in the Azure portal.":::

#### Form Recognizer Studio (preview)

> [!NOTE]
> Form Recognizer Studio is available with the preview (v3.0) API.

1. On the **Form Recognizer Studio** home page, select **Custom form**.

1. Under **My Projects**, select **Create a project**.

1. Complete the project details fields.

1. Configure the service resource by adding your **Storage account** and **Blob container** to **Connect your training data source**.

1. Review and create your project.

1. Use the sample documents to build and test your custom model.

    > [!div class="nextstepaction"]
    > [Try Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/customform/projects)

#### Sample Labeling tool


|Feature    |Custom Template | Custom Neural |
|-----------|-----------------|-----------------------|
|Document structure |Template, fixed form, and structured documents.| Structured, semi-structured, and unstructured documents.|
|Training time | 1 - 5 minutes | 20 - 60 minutes |
|Data extraction| Key-value pairs, tables, selection marks, signatures, and regions| Key-value pairs and selections marks.|
|Models per Document type | Requires one model per each document-type variation| Supports a single model for all document-type variations.|
|Language support| See [custom template model language support](language-support.md)| The custom neural model currently supports English-language documents only.|

## Model capabilities

This table compares the supported data extraction areas:

|Model| Form fields | Selection marks | Structured fields (Tables) | Signature | Region labeling |
|--|:--:|:--:|:--:|:--:|:--:|
|Custom template| ✔ | ✔ | ✔ |&#10033; | ✔ |
|Custom neural| ✔| ✔ |**n/a**| **n/a** | **n/a** |

**Table symbols**: ✔ — supported; &#10033; — preview; **n/a** — currently unavailable

> [!TIP]
> When choosing between the two model types, start with a custom neural model if it meets your functional needs. See [custom neural](concept-custom-neural.md ) to learn more about custom neural models.

## Custom model development options

The following table describes the features available with the associated tools and SDKs. As a best practice, ensure that you use the compatible tools listed here.

| Document type | REST API | SDK | Label and Test Models|
|--|--|--|--|
| Custom form 2.1 | [Form Recognizer 2.1 GA API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) | [Form Recognizer SDK](quickstarts/get-started-sdk-rest-api.md?pivots=programming-language-python)| [Sample labeling tool](https://fott-2-1.azurewebsites.net/)|
| Custom template 3.0 | [Form Recognizer 3.0 (preview)](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)| [Form Recognizer Preview SDK](quickstarts/try-v3-python-sdk.md)| [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio)|
| Custom neural | [Form Recognizer 3.0 (preview)](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)| [Form Recognizer Preview SDK](quickstarts/try-v3-python-sdk.md)| [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio)


> [!NOTE]
> Custom template models trained with the 3.0 API will have a few improvements over the 2.1 API stemming from improvements to the OCR engine. Datasets used to train a custom template model using the 2.1 API can still be used to train a new model using the 3.0 API.

* For best results, provide one clear photo or high-quality scan per document.
* Supported file formats are JPEG, PNG, BMP, TIFF, and PDF (text-embedded or scanned). Text-embedded PDFs are best to eliminate the possibility of error in character extraction and location.
* For PDF and TIFF files, up to 2,000 pages can be processed. With a free tier subscription, only the first two pages are processed.
* The file size must be less than 50 MB.
* Image dimensions must be between 50 x 50 pixels and 10,000 x 10,000 pixels.
* PDF dimensions are up to 17 x 17 inches, corresponding to Legal or A3 paper size, or smaller.
* The total size of the training data is 500 pages or less.
* If your PDFs are password-locked, you must remove the lock before submission.
* For unsupervised learning (without labeled data):
  * Data must contain keys and values.
  * Keys must appear above or to the left of the values. They can't appear below or to the right.

  > [!TIP]
  > Training data:
  >
  >* If possible, use text-based PDF documents instead of image-based documents. Scanned PDFs are handled as images.
  > * For filled-in forms, use examples that have all their fields filled in.
  > * Use forms with different values in each field.
  >* If your form images are of lower quality, use a larger dataset. For example, use 10 to 15 images.

The [Sample Labeling tool](https://fott-2-1.azurewebsites.net/) doesn't support the BMP file format. This limitation relates to the tool, not the Form Recognizer service.

## Supported languages and locales

 The Form Recognizer preview version introduces more language support for custom models. For a list of supported handwritten and printed text, see [Language support](language-support.md).

## Form Recognizer v3.0 (preview)

 Form Recognizer v3.0 (preview) introduces several new features and capabilities:

* **Custom model API (v3.0)**: This version supports signature detection for custom forms. When you train custom models, you can specify certain fields as signatures. When a document is analyzed with your custom model, it indicates whether a signature was detected or not.
* [Form Recognizer v3.0 migration guide](v3-migration-guide.md): This guide shows you how to use the preview version in your applications and workflows.
* [REST API (preview)](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument): This API shows you more about the preview version and new capabilities.

### Try signature detection

1. Build your training dataset.

1. Go to [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio). Under **Custom models**, select **Custom form**.

    :::image type="content" source="media/label-tool/select-custom-form.png" alt-text="Screenshot that shows selecting the Form Recognizer Studio Custom form page.":::

1. Follow the workflow to create a new project:

   1. Follow the **Custom model** input requirements.

   1. Label your documents. For signature fields, use **Region** labeling for better accuracy.

      :::image type="content" source="media/label-tool/signature-label-region-too.png" alt-text="Screenshot that shows the Label signature field.":::

After your training set is labeled, you can train your custom model and use it to analyze documents. The signature fields specify whether a signature was detected or not.


## Next steps

Explore Form Recognizer quickstarts and REST APIs:

| Quickstart | REST API|
|--|--|
|[v3.0 Studio quickstart](quickstarts/try-v3-form-recognizer-studio.md) |[Form Recognizer v3.0 API 2022-01-30-preview](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-2/operations/AnalyzeDocument)|
| [v2.1 quickstart](quickstarts/get-started-sdk-rest-api.md) | [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-2/operations/BuildDocumentModel) |