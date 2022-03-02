---
title: Form Recognizer models
titleSuffix: Azure Applied AI Services
description: Concepts encompassing data extraction and analysis using prebuilt models.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/15/2022
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
---
<!-- markdownlint-disable MD033 -->

# Form Recognizer models

Azure Form Recognizer prebuilt models enable you to add intelligent document processing to your apps and flows without having to train and build your own models. Prebuilt models use optical character recognition (OCR) combined with deep learning models to identify and extract predefined text and data fields common to specific form and document types. Form Recognizer extracts analyzes form and document data then  returns an organized, structured JSON response. Form Recognizer v2.1 supports invoice, receipt, ID document, and business card models.

## Model overview

| **Model**   | **Description**   |
| --- | --- |
| 🆕[Read (preview)](#read-preview) | Extract text lines, words, their locations, detected languages, and handwritten style if detected.  |
| 🆕[General document (preview)](#general-document-preview) | Extract text, tables, structure, key-value pairs, and named entities.  |
| [Layout](#layout)  | Extracts text and layout information from documents.  |
| [Invoice](#invoice)  | Extract key information from English and Spanish invoices.  |
| [Receipt](#receipt)  | Extract key information from English receipts.  |
| [ID document](#id-document)  | Extract key information from US driver licenses and international passports.  |
| 🆕[W-2 (preview)](#w-2-preview) | Extract employee, employer, wage information, etc. from US W-2 forms.  |
| [Business card](#business-card)  | Extract key information from English business cards.  |
| [Custom](#custom) |  Extract data from forms and documents specific to your business. Custom models are trained for your distinct data and use cases. |

### Read (preview)

:::image type="content" source="media/studio/read-card.png" alt-text="Screenshot: Studio read icon.":::

The Read API analyzes and extracts ext lines, words, their locations, detected languages, and handwritten style if detected.

***Sample document processed using the [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/read)***:

:::image type="content" source="media/studio/form-recognizer-studio-read-v3p2.png" alt-text="Screenshot: Screenshot of sample document processed using Form Recognizer studio Read":::

> [!div class="nextstepaction"]
> [Learn more: read model](concept-read.md)

### General document (preview)

:::image type="content" source="media/studio/general-document.png" alt-text="Screenshot: Studio general document icon.":::

* The general document API supports most form types and will analyze your documents and associate values to keys and entries to tables that it discovers. It's ideal for extracting common key-value pairs from documents. You can use the general document model as an alternative to training a custom model without labels.

* The general document is a pre-trained model and can be directly invoked via the REST API.

* The general document model supports named entity recognition (NER) for several entity categories. NER is the ability to identify different entities in text and categorize them into pre-defined classes or types such as: person, location, event, product, and organization. Extracting entities can be useful in scenarios where you want to validate extracted values. The entities are extracted from the entire content.

***Sample document processed using the [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/document)***:

:::image type="content" source="media/studio/general-document-analyze.png" alt-text="Screenshot: general document analysis in the Form Recognizer Studio.":::

> [!div class="nextstepaction"]
> [Learn more: general document model](concept-general-document.md)

### Layout

:::image type="content" source="media/studio/layout.png" alt-text="Screenshot: Studio layout icon.":::

The Layout API analyzes and extracts text, tables and headers, selection marks, and structure information from forms and documents.

***Sample form processed using the [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/layout)***:

:::image type="content" source="media/studio/analyze-layout.png" alt-text="Screenshot: Screenshot of sample document processed using Form Recognizer studio":::

> [!div class="nextstepaction"]
> [Learn more: layout model](concept-layout.md)

### Invoice

:::image type="content" source="media/studio/invoice.png" alt-text="Screenshot: Studio invoice icon.":::

The invoice model analyzes and extracts key information from sales invoices. The API analyzes invoices in various formats and extracts key information such as customer name, billing address, due date, and amount due. Currently, the model supports both English and Spanish invoices. 

***Sample invoice processed using [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)***:

:::image type="content" source="./media/studio/analyze-invoice.png" alt-text="Screenshot of a sample invoice." lightbox="./media/overview-invoices.jpg":::

> [!div class="nextstepaction"]
> [Learn more: invoice model](concept-invoice.md)

### Receipt

:::image type="content" source="media/studio/receipt.png" alt-text="Screenshot: Studio receipt icon.":::

The receipt model analyzes and extracts key information from sales receipts. The API analyzes printed and handwritten receipts and extracts key information such as merchant name, merchant phone number, transaction date, tax, and transaction total. 

***Sample receipt processed using [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)***:

:::image type="content" source="./media/studio/analyze-receipt.png" alt-text="Screenshot of a sample receipt." lightbox="./media/overview-receipt.jpg":::

> [!div class="nextstepaction"]
> [Learn more: receipt model](concept-receipt.md)

### ID document

:::image type="content" source="media/studio/id-document.png" alt-text="Screenshot: Studio identity document icon.":::

The ID document model analyzes and extracts key information from U.S. Driver's Licenses (all 50 states and District of Columbia) and biographical pages from international passports (excluding visa and other travel documents). The API analyzes identity documents and extracts key information such as first name, last name, address, and date of birth.

***Sample U.S. Driver's License processed using [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)***:

:::image type="content" source="./media/studio/analyze-drivers-license.png" alt-text="Screenshot of a sample identification card." lightbox="./media/overview-id.jpg":::

> [!div class="nextstepaction"]
> [Learn more: identity document model](concept-id-document.md)

### W-2 (preview)

:::image type="content" source="media/studio/w2.png" alt-text="Screenshot: Studio W-2 icon.":::

The W-2 model analyzes and extracts key information reported in each box on a W-2 form. The model supports standard and customized forms from 2018 to the present, including both single form and multiple forms (copy A, B, C, D, 1, 2) on one page. 

***Sample W-2 document processed using [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=tax.us.w2)***:

:::image type="content" source="./media/studio/w-2.png" alt-text="Screenshot of a sample W-2.":::

> [!div class="nextstepaction"]
> [Learn more: W-2 model](concept-w2.md)

### Business card

:::image type="content" source="media/studio/business-card.png" alt-text="Screenshot: Studio business card icon.":::

The business card model analyzes and extracts key information from business card images. The API analyzes printed business card images and extracts key information such as first name, last name, company name, email address, and phone number.

***Sample business card processed using [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard)***:

:::image type="content" source="./media/studio/analyze-business-card.png" alt-text="Screenshot of a sample business card." lightbox="./media/overview-business-card.jpg":::

> [!div class="nextstepaction"]
> [Learn more: business card model](concept-business-card.md)

### Custom

 :::image type="content" source="media/studio/custom.png" alt-text="Screenshot: Form Recognizer Studio custom icon.":::

The custom model analyzes and extracts data from forms and documents specific to your business. The API is a machine-learning program trained to recognize form fields within your distinct content and extract key-value pairs and table data. You only need five examples of the same form type to get started and your custom model can be trained with or without labeled datasets.

***Sample custom template processed using [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/customform/projects)***:

:::image type="content" source="media/studio/train-model.png" alt-text="Screenshot: Form Recognizer tool analyze-a-custom-form window.":::

> [!div class="nextstepaction"]
> [Learn more: custom model](concept-custom.md)

## Data extraction

 | **Model**   | **Text extraction** |**Key-Value pairs** |**Fields**|**Selection Marks**   | **Tables**   |**Entities** |
  | --- | :---: |:---:| :---: | :---: |:---: |:---: |
  |🆕Read (preview) | ✓ |   ||   |   |   |
  |🆕General document (preview)  | ✓  |  ✓ || ✓  | ✓  | ✓  |
  | Layout  | ✓  |   || ✓  | ✓  |   |
  | Invoice  | ✓ | ✓  |✓| ✓  | ✓ ||
  |Receipt  | ✓  |   ✓ |✓|   |  ||
  | ID document | ✓  |  ✓ |✓|   |   ||
  |🆕W-2 | ✓  |  ✓  | ✓ | ✓ | ✓ ||
  | Business card    | ✓  |   ✓ | ✓|  |   ||
  | Custom             |✓  |  ✓ || ✓  | ✓  | ✓  |


## Input requirements

* For best results, provide one clear photo or high-quality scan per document.
* Supported file formats: JPEG, PNG, BMP, TIFF, and PDF (text-embedded or scanned). Text-embedded PDFs are best to eliminate the possibility of error in character extraction and location.
* For PDF and TIFF, up to 2000 pages can be processed (with a free tier subscription, only the first two pages are processed).
* The file size must be less than 50 MB.
* Image dimensions must be between 50 x 50 pixels and 10,000 x 10,000 pixels.
* PDF dimensions are up to 17 x 17 inches, corresponding to Legal or A3 paper size, or smaller.
* The total size of the training data is 500 pages or less.
* If your PDFs are password-locked, you must remove the lock before submission.
* For unsupervised learning (without labeled data):
  * Data must contain keys and values.
  * Keys must appear above or to the left of the values; they can't appear below or to the right.

> [!NOTE]
> The [Sample Labeling tool](https://fott-2-1.azurewebsites.net/) does not support the BMP file format. This is a limitation of the tool not the Form Recognizer Service.

## Form Recognizer preview v3.0

  Form Recognizer v3.0 (preview) introduces several new features and capabilities:

* [**Read (preview)**](concept-read.md) model is a new API that extracts text lines, words, their locations, detected languages, and handwrting style if detected.
* [**General document (preview)**](concept-general-document.md) model is a new API that uses a pre-trained model to extract text, tables, structure, key-value pairs, and named entities from forms and documents.
* [**Receipt (preview)**](concept-receipt.md) model supports single-page hotel receipt processing.
* [**ID document (preview)**](concept-id-document.md) model supports endorsements, restrictions, and vehicle classification extraction from US driver's licenses.
* [**W-2 (preview)**](concept-w2.md) model supports employee, employer, wage information, etc. from US W-2 forms.
* [**Custom model API (preview)**](concept-custom.md) supports signature detection for custom forms.

### Version migration

Learn how to use Form Recognizer v3.0 in your applications by following our [**Form Recognizer v3.0 migration guide**](v3-migration-guide.md)

## Next steps

* [Learn how to process your own forms and documents](quickstarts/try-sample-label-tool.md) with our [Form Recognizer sample tool](https://fott-2-1.azurewebsites.net/)

* Complete a [Form Recognizer quickstart](quickstarts/try-sdk-rest-api.md) and get started creating a document processing app in the development language of your choice.
