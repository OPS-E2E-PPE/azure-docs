---
title: Receipt data extraction - Document Intelligence
titleSuffix: Azure AI services
description: Use machine learning powered receipt data extraction model to digitize receipts.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/18/2023
ms.author: lajanuar
monikerRange: '<=doc-intel-3.0.0'
---

<!-- markdownlint-disable MD033 -->

# Document Intelligence receipt model

::: moniker range="doc-intel-3.0.0"
[!INCLUDE [applies to v3.0](includes/applies-to-v3-0.md)]
::: moniker-end

::: moniker range="doc-intel-2.1.0"
[!INCLUDE [applies to v2.1](includes/applies-to-v2-1.md)]
::: moniker-end

The Document Intelligence receipt model combines powerful Optical Character Recognition (OCR) capabilities with deep learning models to analyze and extract key information from sales receipts. Receipts can be of various formats and quality including printed and handwritten receipts. The API extracts key information such as merchant name, merchant phone number, transaction date, tax, and transaction total and returns structured JSON data.

## Receipt data extraction

Receipt digitization is the process of converting scanned receipts into digital form for downstream processing. Document Intelligence OCR-powered receipt data extraction helps to automate the conversion and save time and effort.

::: moniker range="doc-intel-3.0.0"

***Sample receipt processed with [Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)***:

:::image type="content" source="media/studio/overview-receipt.png" alt-text="Screenshot of a sample receipt processed in the Document Intelligence Studio." lightbox="media/overview-receipt.jpg":::

::: moniker-end

::: moniker range="doc-intel-2.1.0"

**Sample receipt processed with [Document Intelligence Sample Labeling tool](https://fott-2-1.azurewebsites.net/connection)**:

:::image type="content" source="media/analyze-receipt-fott.png" alt-text="Screenshot of a sample receipt processed with the Form Sample Labeling tool.":::

::: moniker-end

## Development options

::: moniker range="doc-intel-3.0.0"
Document Intelligence v3.0 Supports the following tools:

| Feature | Resources | Model ID |
|----------|-------------|-----------|
|**Receipt model**| <ul><li>[**Document Intelligence Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-2022-08-31/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true)</li><li>[**Python SDK**](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true)</li></ul>|**prebuilt-receipt**|

::: moniker-end

::: moniker range="doc-intel-2.1.0"

Document Intelligence v2.1 supports the following tools:

| Feature | Resources |
|----------|-------------------------|
|**Receipt model**| <ul><li>[**Document Intelligence labeling tool**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](./how-to-guides/use-sdk-rest-api.md?pivots=programming-language-rest-api&preserve-view=true&tabs=windows&view=doc-intel-2.1.0#analyze-receipts)</li><li>[**Client-library SDK**](~/articles/ai-services/document-intelligence/how-to-guides/use-sdk-rest-api.md?view=doc-intel-2.1.0&preserve-view=true)</li><li>[**Document Intelligence Docker container**](containers/install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|

::: moniker-end

## Input requirements

::: moniker range="doc-intel-3.0.0"

[!INCLUDE [input requirements](./includes/input-requirements.md)]

::: moniker-end

::: moniker range="doc-intel-2.1.0"

* Supported file formats: JPEG, PNG, PDF, and TIFF
* For PDF and TIFF, Document Intelligence can process up to 2000 pages for standard tier subscribers or only the first two pages for free-tier subscribers.
* The file size must be less than 50 MB and dimensions at least 50 x 50 pixels and at most 10,000 x 10,000 pixels.

::: moniker-end

### Try receipt data extraction

See how Document Intelligence extracts data, including time and date of transactions, merchant information, and amount totals from receipts. You need the following resources:

* An Azure subscription—you can [create one for free](https://azure.microsoft.com/free/cognitive-services/)

* An [Form Recognizer instance (Document Intelligence forthcoming)](https://portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) in the Azure portal. You can use the free pricing tier (`F0`) to try the service. After your resource deploys, select **Go to resource** to get your key and endpoint.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: keys and endpoint location in the Azure portal.":::

::: moniker range="doc-intel-3.0.0"

#### Document Intelligence Studio

> [!NOTE]
> Document Intelligence Studio is available with the v3.0 API.

1. On the Document Intelligence Studio home page, select **Receipts**

1. You can analyze the sample receipt or select the **+ Add** button to upload your own sample.

1. Select the **Analyze** button:

    :::image type="content" source="media/studio/receipt-analyze.png" alt-text="Screenshot: analyze receipt menu.":::

    > [!div class="nextstepaction"]
    > [Try Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

::: moniker-end

::: moniker range="doc-intel-2.1.0"

## Document Intelligence Sample Labeling tool

1. Navigate to the [Document Intelligence Sample Tool](https://fott-2-1.azurewebsites.net/).

1. On the sample tool home page, select the **Use prebuilt model to get data** tile.

    :::image type="content" source="media/label-tool/prebuilt-1.jpg" alt-text="Screenshot of the layout model analyze results process.":::

1. Select the **Form Type**  to analyze from the dropdown menu.

1. Choose a URL for the file you would like to analyze from the below options:

    * [**Sample invoice document**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/invoice_sample.jpg).
    * [**Sample ID document**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png).
    * [**Sample receipt image**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg).
    * [**Sample business card image**](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg).

1. In the **Source** field, select **URL** from the dropdown menu, paste the selected URL, and select the **Fetch** button.

    :::image type="content" source="media/label-tool/fott-select-url.png" alt-text="Screenshot of source location dropdown menu.":::

1. In the **Document Intelligence service endpoint** field, paste the endpoint that you obtained with your Document Intelligence subscription.

1. In the **key** field, paste  the key you obtained from your Document Intelligence resource.

    :::image type="content" source="media/fott-select-form-type.png" alt-text="Screenshot of the select-form-type dropdown menu.":::

1. Select **Run analysis**. The Document Intelligence Sample Labeling tool calls the Analyze Prebuilt API and analyze the document.

1. View the results - see the key-value pairs extracted, line items, highlighted text extracted and tables detected.

    :::image type="content" source="media/receipts-example.jpg" alt-text="Screenshot of the layout model analyze results operation.":::

> [!NOTE]
> The [Sample Labeling tool](https://fott-2-1.azurewebsites.net/) does not support the BMP file format. This is a limitation of the tool not the Document Intelligence Service.

::: moniker-end

::: moniker range="doc-intel-3.0.0"

## Supported languages and locales

>[!NOTE]
> Document Intelligence auto-detects language and locale data.

### [2023-02-28-preview](#tab/2023-02-28-preview)

#### Thermal receipts (retail, meal, parking, etc.)

| Supported Languages | Details |
|:--------------------|:-------:|
|English|United States (`en-US`), Australia (`en-AU`), Canada (`en-CA`), United Kingdom (`en-GB`), India (`en-IN`), United Arab Emirates (`en-AE`)|
|Croatian|Croatia (`hr-HR`)|
|Czech|Czechia (`cs-CZ`)|
|Danish|Denmark (`da-DK`)|
|Dutch|Netherlands (`nl-NL`)|
|Finnish|Finland (`fi-FI`)|
|French|Canada (`fr-CA`), France (`fr-FR`)|
|German|Germany (`de-DE`)|
|Hungarian|Hungary (`hu-HU`)|
|Italian|Italy (`it-IT`)|
|Japanese|Japan (`ja-JP`)|
|Latvian|Latvia (`lv-LV`)|
|Lithuanian|Lithuania (`lt-LT`)|
|Norwegian|Norway (`no-NO`)|
|Portuguese|Brazil (`pt-BR`), Portugal (`pt-PT`)|
|Spanish|Spain (`es-ES`)|
|Swedish|Sweden (`sv-SE`)|
|Vietnamese|Vietnam (`vi-VN`)|

#### Hotel receipts

| Supported Languages | Details |
|:--------------------|:-------:|
|English|United States (`en-US`)|
|French|France (`fr-FR`)|
|German|Germany (`de-DE`)|
|Italian|Italy (`it-IT`)|
|Japanese|Japan (`ja-JP`)|
|Portuguese|Portugal (`pt-PT`)|
|Spanish|Spain (`es-ES`)|

### [**2022-08-31 (GA)**](#tab/2022-08-31)

#### Thermal receipts (retail, meal, parking, etc.)

| Supported Languages | Details |
|:--------------------|:-------:|
|English|United States (`en-US`), Australia (`en-AU`), Canada (`en-CA`), United Kingdom (`en-GB`), India (`en-IN`), United Arab Emirates (`en-AE`)|
|Croatian|Croatia (`hr-HR`)|
|Czech|Czechia (`cs-CZ`)|
|Danish|Denmark (`da-DK`)|
|Dutch|Netherlands (`nl-NL`)|
|Finnish|Finland (`fi-FI`)|
|French|Canada (`fr-CA`), France (`fr-FR`)|
|German|Germany (`de-DE`)|
|Hungarian|Hungary (`hu-HU`)|
|Italian|Italy (`it-IT`)|
|Japanese|Japan (`ja-JP`)|
|Latvian|Latvia (`lv-LV`)|
|Lithuanian|Lithuania (`lt-LT`)|
|Norwegian|Norway (`no-NO`)|
|Portuguese|Brazil (`pt-BR`), Portugal (`pt-PT`)|
|Spanish|Spain (`es-ES`)|
|Swedish|Sweden (`sv-SE`)|
|Vietnamese|Vietnam (`vi-VN`)|

#### Hotel receipts

| Supported Languages | Details |
|:--------------------|:-------:|
|English|United States (`en-US`)|
|French|France (`fr-FR`)|
|German|Germany (`de-DE`)|
|Italian|Italy (`it-IT`)|
|Japanese|Japan (`ja-JP`)|
|Portuguese|Portugal (`pt-PT`)|
|Spanish|Spain (`es-ES`)|

::: moniker-end

::: moniker range="doc-intel-2.1.0"

## Supported languages and locales v2.1

>[!NOTE]
 > It's not necessary to specify a locale. This is an optional parameter. The Document Intelligence deep-learning technology will auto-detect the language of the text in your image.

| Model | Language—Locale code | Default |
|--------|:----------------------|:---------|
|Receipt| <ul><li>English (United States)—en-US</li><li> English (Australia)—en-AU</li><li>English (Canada)—en-CA</li><li>English (United Kingdom)—en-GB</li><li>English (India)—en-IN</li></ul>  | Autodetected |

::: moniker-end

## Field extraction

::: moniker range="doc-intel-2.1.0"

|Name| Type | Description | Standardized output |
|:-----|:----|:----|:----|
| ReceiptType | String | Type of sales receipt |  Itemized |
| MerchantName | String | Name of the merchant issuing the receipt |  |
| MerchantPhoneNumber | phoneNumber | Listed phone number of merchant | +1 xxx xxx xxxx |
| MerchantAddress | String | Listed address of merchant |   |
| TransactionDate | Date | Date the receipt was issued | yyyy-mm-dd |
| TransactionTime | Time | Time the receipt was issued | hh-mm-ss (24-hour)  |
| Total | Number (USD)| Full transaction total of receipt | Two-decimal float|
| Subtotal | Number (USD) | Subtotal of receipt, often before taxes are applied | Two-decimal float|
 | Tax | Number (USD) | Total tax on receipt (often sales tax or equivalent). **Renamed to "TotalTax" in 2022-06-30 version**. | Two-decimal float |
| Tip | Number (USD) | Tip included by buyer | Two-decimal float|
| Items | Array of objects | Extracted line items, with name, quantity, unit price, and total price extracted | |
| Name | String | Item description. **Renamed to "Description" in 2022-06-30 version**. | |
| Quantity | Number | Quantity of each item | Two-decimal float |
| Price | Number | Individual price of each item unit| Two-decimal float |
| TotalPrice | Number | Total price of line item | Two-decimal float |

::: moniker-end

::: moniker range="doc-intel-3.0.0"

 Document Intelligence v3.0 introduces several new features and capabilities. In addition to thermal receipts, the **Receipt** model supports single-page hotel receipt processing and tax detail extraction for all receipt types.

### [**2022-08-31 (GA)**](#tab/2022-08-31)

#### Thermal receipts (receipt, receipt.retailMeal, receipt.creditCard, receipt.gas, receipt.parking)

| Field | Type | Description | Example |
|:------|:-----|:------------|:--------|
|`MerchantName`|`string`|Name of the merchant issuing the receipt|Contoso|
|`MerchantPhoneNumber`|`phoneNumber`|Listed phone number of merchant|987-654-3210|
|`MerchantAddress`|`address`|Listed address of merchant|123 Main St. Redmond WA 98052|
|`Total`|`number`|Full transaction total of receipt|$14.34|
|`TransactionDate`|`date`|Date the receipt was issued|June 06, 2019|
|`TransactionTime`|`time`|Time the receipt was issued|4:49 PM|
|`Subtotal`|`number`|Subtotal of receipt, often before taxes are applied|$12.34|
|`TotalTax`|`number`|Tax on receipt, often sales tax or equivalent|$2.00|
|`Tip`|`number`|Tip included by buyer|$1.00|
|`Items`|`array`|||
|`Items.*`|`object`|Extracted line item|1<br>Surface Pro 6<br>$999.00<br>$999.00|
|`Items.*.TotalPrice`|`number`|Total price of line item|$999.00|
|`Items.*.Description`|`string`|Item description|Surface Pro 6|
|`Items.*.Quantity`|`number`|Quantity of each item|1|
|`Items.*.Price`|`number`|Individual price of each item unit|$999.00|
|`Items.*.ProductCode`|`string`|Product code, product number, or SKU associated with the specific line item|A123|
|`Items.*.QuantityUnit`|`string`|Quantity unit of each item||
|`TaxDetails`|`array`|||
|`TaxDetails.*`|`object`|Extracted line item|1<br>Surface Pro 6<br>$999.00<br>$999.00|
|`TaxDetails.*.Amount`|`currency`|The amount of the tax detail|$999.00|

#### Hotel receipts (receipt.hotel)

| Field | Type | Description | Example |
|:------|:-----|:------------|:--------|
|`MerchantName`|`string`|Name of the merchant issuing the receipt|Contoso|
|`MerchantPhoneNumber`|`phoneNumber`|Listed phone number of merchant|987-654-3210|
|`MerchantAddress`|`address`|Listed address of merchant|123 Main St. Redmond WA 98052|
|`Total`|`number`|Full transaction total of receipt|$14.34|
|`ArrivalDate`|`date`|Date of arrival|27Mar21|
|`DepartureDate`|`date`|Date of departure|28Mar21|
|`Currency`|`string`|Currency unit of receipt amounts (ISO 4217), or 'MIXED' if multiple values are found|USD|
|`MerchantAliases`|`array`|||
|`MerchantAliases.*`|`string`|Alternative name of merchant|Contoso (R)|
|`Items`|`array`|||
|`Items.*`|`object`|Extracted line item|1<br>Surface Pro 6<br>$999.00<br>$999.00|
|`Items.*.TotalPrice`|`number`|Total price of line item|$999.00|
|`Items.*.Description`|`string`|Item description|Room Charge|
|`Items.*.Date`|`date`|Item date|27Mar21|
|`Items.*.Category`|`string`|Item category|Room|

### [2023-02-28-preview](#tab/2023-02-28-preview)

#### Thermal receipts (receipt, receipt.retailMeal, receipt.creditCard, receipt.gas, receipt.parking)
| Field | Type | Description | Example |
|:------|:-----|:------------|:--------|
|`MerchantName`|`string`|Name of the merchant issuing the receipt|Contoso|
|`MerchantPhoneNumber`|`phoneNumber`|Listed phone number of merchant|987-654-3210|
|`MerchantAddress`|`address`|Listed address of merchant|123 Main St. Redmond WA 98052|
|`Total`|`number`|Full transaction total of receipt|$14.34|
|`TransactionDate`|`date`|Date the receipt was issued|June 06, 2019|
|`TransactionTime`|`time`|Time the receipt was issued|4:49 PM|
|`Subtotal`|`number`|Subtotal of receipt, often before taxes are applied|$12.34|
|`TotalTax`|`number`|Tax on receipt, often sales tax or equivalent|$2.00|
|`Tip`|`number`|Tip included by buyer|$1.00|
|`Items`|`array`|||
|`Items.*`|`object`|Extracted line item|1<br>Surface Pro 6<br>$999.00<br>$999.00|
|`Items.*.TotalPrice`|`number`|Total price of line item|$999.00|
|`Items.*.Description`|`string`|Item description|Surface Pro 6|
|`Items.*.Quantity`|`number`|Quantity of each item|1|
|`Items.*.Price`|`number`|Individual price of each item unit|$999.00|
|`Items.*.ProductCode`|`string`|Product code, product number, or SKU associated with the specific line item|A123|
|`Items.*.QuantityUnit`|`string`|Quantity unit of each item||
|`TaxDetails`|`array`|||
|`TaxDetails.*`|`object`|Extracted line item|1<br>Surface Pro 6<br>$999.00<br>$999.00|
|`TaxDetails.*.Amount`|`currency`|The amount of the tax detail|$999.00|

#### Hotel receipts (receipt.hotel)

| Field | Type | Description | Example |
|:------|:-----|:------------|:--------|
|`MerchantName`|`string`|Name of the merchant issuing the receipt|Contoso|
|`MerchantPhoneNumber`|`phoneNumber`|Listed phone number of merchant|987-654-3210|
|`MerchantAddress`|`address`|Listed address of merchant|123 Main St. Redmond WA 98052|
|`Total`|`number`|Full transaction total of receipt|$14.34|
|`ArrivalDate`|`date`|Date of arrival|27Mar21|
|`DepartureDate`|`date`|Date of departure|28Mar21|
|`Currency`|`string`|Currency unit of receipt amounts (ISO 4217), or 'MIXED' if multiple values are found|USD|
|`MerchantAliases`|`array`|||
|`MerchantAliases.*`|`string`|Alternative name of merchant|Contoso (R)|
|`Items`|`array`|||
|`Items.*`|`object`|Extracted line item|1<br>Surface Pro 6<br>$999.00<br>$999.00|
|`Items.*.TotalPrice`|`number`|Total price of line item|$999.00|
|`Items.*.Description`|`string`|Item description|Room Charge|
|`Items.*.Date`|`date`|Item date|27Mar21|
|`Items.*.Category`|`string`|Item category|Room|

---


::: moniker-end

::: moniker range="doc-intel-2.1.0"

### Migration guide and REST API v3.0

* Follow our [**Document Intelligence v3.0 migration guide**](v3-migration-guide.md) to learn how to use the v3.0 version in your applications and workflows.

::: moniker-end

## Next steps

::: moniker range="doc-intel-3.0.0"

* Try processing your own forms and documents with the [Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/studio)

* Complete a [Document Intelligence quickstart](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true) and get started creating a document processing app in the development language of your choice.

::: moniker-end

::: moniker range="doc-intel-2.1.0"

* Try processing your own forms and documents with the [Document Intelligence Sample Labeling tool](https://fott-2-1.azurewebsites.net/)

* Complete a [Document Intelligence quickstart](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-2.1.0&preserve-view=true) and get started creating a document processing app in the development language of your choice.

::: moniker-end
