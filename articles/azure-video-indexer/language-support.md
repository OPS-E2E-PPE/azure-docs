---
title: Language support in Azure Video Indexer
description: This article provides a comprehensive list of language support by service features in Azure Video Indexer.
author: Juliako
manager: femila
ms.topic: conceptual
ms.custom: ignite-2022
ms.author: juliako
ms.date: 01/06/2023
---

# Language support in Azure Video Indexer

This article provides a comprehensive list of language support by service features in Azure Video Indexer. For the list and definitions of all the features, see [Overview](video-indexer-overview.md).

Some languages are supported only through the API (see [Get Supported Languages](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Supported-Languages)) and not through the Video Indexer website or widgets. To make sure a language is supported for search, transcription, or translation by the Azure Video Indexer website and widgets, see the [front end language 
support table](#language-support-in-front-end-experiences) further below.

## API language support

The API returns a list of supported languages with the following values:

```json
"name": "Language",
"languageCode": "Code",
"isRightToLeft": true/false,
"isSourceLanguage": true/false,
"isAutoDetect": true/false
```

Some notes for the above values are:

- Supported source language:

   If `isSourceLanguage` is `false`, the language is supported for translation only.  
   If `isSourceLanguage` is `true`, the language is supported as source for transcription, translation, and search. 
- Language identification (auto detection): 
 
   If `isAutoDetect` set to `true`, the language is supported for language identification (LID) and multi-language identification  (MLID).   
- The following insights are translated, otherwise will remain in English:
  
     - Transcript
     - Keywords
     - Topics
     - Labels
     - Frame patterns (Only to Hebrew as of now)

| **Language**   | **Code**       | **Supported source language** | **Language identification**  | **Customization** (language model)  |
|:--------------:|:--------------:|:-----------------------------:|:----------------------------:|:-----------:|
| Afrikaans                        | `af-ZA`      |   |   |  |
| Arabic (Israel)                  | `ar-IL`      | ✔ |   | ✔ |
| Arabic (Iraq)                    | `ar-IQ`      | ✔ | ✔ |   |
| Arabic (Jordan)                  | `ar-JO`      | ✔ | ✔ | ✔ |
| Arabic (Kuwait)                  | `ar-KW`      | ✔ | ✔ | ✔ |
| Arabic (Lebanon)                 | `ar-LB`      | ✔ |   | ✔ |
| Arabic (Oman)                    | `ar-OM`      | ✔ | ✔ | ✔ |
| Arabic (Paestinian Authority)   | `ar-PS`      | ✔ |   | ✔ |
| Arabic (Qatar)                   | `ar-QA`      | ✔ | ✔ | ✔ |
| Arabic (Saudi Arabia)            | `ar-SA`      | ✔ | ✔ | ✔ |
| Arabic (United Arab Emirates)    | `ar-AE`      | ✔ | ✔ | ✔ |
| Arabic Egypt                     | `ar-EG`      | ✔ | ✔ | ✔ |
| Arabic Modern Standard (Bahrain) | `ar-BH`      | ✔ | ✔ | ✔ |
| Arabic Syrian Arab Republic      | `ar-SY`      | ✔ | ✔|  ✔ |
| Armenian                         | `hy-AM`      | ✔ |   |    |              
| Bangla                           | `bn-BD`      |   |   |   |
| Bosnian                          | `bs-Latn`    |   |   |   |
| Bulgarian                        | `bg-BG`      | ✔ | ✔ |   |
| Catalan                          | `ca-ES`      | ✔ | ✔ |   |
| Chinese (Cantonese Traditional)  | `zh-HK`      | ✔ | ✔ | ✔ |
| Chinese (Simplified)             | `zh-Hans`    | ✔ | ✔ | ✔ |
| Chinese (Simplified)             | `zh-CK`      | ✔ | ✔ | ✔ |
| Chinese (Traditional)            | `zh-Hant`    |   |    |   |
| Croatian                         | `hr-HR`      | ✔ | ✔  |   |
| Czech                            | `cs-CZ`      | ✔ |✔  |  ✔ |
| Danish                           | `da-DK`      | ✔ |✔  |  ✔ |
| Dutch                            | `nl-NL`      | ✔ |✔ | ✔ |
| English Australia                | `en-AU`      | ✔ |✔ | ✔ |
| English United Kingdom           | `en-GB`      | ✔ |✔ | ✔ |
| English United States            | `en-US`      | ✔ |✔ | ✔ |
| Estonian                         | `et-EE`      | ✔ |✔  |   |
| Fijian                           | `en-FJ`      |   |   |   |
| Filipino                         | `fil-PH`     |   |   |  |
| Finnish                          | `fi-FI`      | ✔ |✔ | ✔ |
| French                           | `fr-FR`      | ✔ |✔ | ✔ |
| French (Canada)                  | `fr-CA`      | ✔ |✔ | ✔ |
| German                           | `de-DE`      | ✔ |✔ | ✔ |
| Greek                            | `el-GR`      | ✔ |✔ |   |
| Gujarati                         | `gu-IN`      | ✔ |✔ |   |
| Haitian                          | `fr-HT`      |   |   |  |
| Hebrew                           | `he-IL`      | ✔ |✔ | ✔ |
| Hindi                            | `hi-IN`      | ✔ |✔ | ✔ |
| Hungarian                        | `hu-HU`      |   | ✔ |  |
| Icelandic                        | `is-IS`      | ✔ |   |  |
| Indonesian                       | `id-ID`      |   |   |  |
| Irish                            | `ga-IE`      | ✔ | ✔ |  |
| Italian                          | `it-IT`      | ✔ | ✔ | ✔ |
| Japanese                         | `ja-JP`      | ✔ | ✔ | ✔ |
| Kannada                          | `kn-IN`      | ✔ | ✔ |   |
| Kiswahili                        | `sw-KE`      |   |   |  |
| Korean                           | `ko-KR`      | ✔ | ✔| ✔ |
| Latvian                          | `lv-LV`      | ✔ | ✔ |  |
| Lithuanian                       | `lt-LT`      |   |   |  |
| Malagasy                         | `mg-MG`      |   |   |  |
| Malay                            | `ms-MY`      | ✔ |   |  |
| Malayalam                        | `ml-IN`      |✔  |✔ |   |
| Maltese                          | `mt-MT`      |   |   |  |
| Norwegian                        | `nb-NO`      | ✔ |✔ | ✔ |
| Persian                          | `fa-IR`      | ✔ |  | ✔ |
| Polish                           | `pl-PL`      | ✔ |✔ | ✔ |
| Portuguese                       | `pt-BR`      | ✔ | ✔ | ✔ |
| Portuguese (Portugal)            | `pt-PT`      | ✔ | ✔ | ✔ |
| Romanian                         | `ro-RO`      | ✔ | ✔ |  |
| Russian                          | `ru-RU`      | ✔ | ✔ |  ✔ |
| Samoan                           | `en-WS`      |   |   |   |
| Serbian (Cyrillic)               | `sr-Cyrl-RS` |   |   |   |
| Serbian (Latin)                  | `sr-Latn-RS` |   |   |   |
| Slovak                           | `sk-SK`      | ✔ | ✔ |  |
| Slovenian                        | `sl-SI`      | ✔ | ✔ |  |
| Spanish                          | `es-ES`      | ✔ | ✔ | ✔ |
| Spanish (Mexico)                 | `es-MX`      | ✔ | ✔ | ✔ |
| Swedish                          | `sv-SE`      | ✔ |✔ | ✔ |
| Tamil                            | `ta-IN`      | ✔ | ✔ |  |
| Telugu                           | `te-IN`      | ✔ | ✔ |  |
| Thai                             | `th-TH`      | ✔ |✔ | ✔ |
| Tongan                           | `to-TO`      |    |   |  |
| Turkish                          | `tr-TR`      | ✔  | ✔| ✔ |
| Ukrainian                        | `uk-UA`      | ✔  | ✔ |  |
| Urdu                             | `ur-PK`      |    |   |  |
| Vietnamese                       | `vi-VN`      | ✔  |✔|  |

**Default languages supported by Language identification (LID)**: German (de-DE) , English United States (en-US) , Spanish (es-ES) , French (fr-FR), Italian (it-IT) , Japanese (ja-JP), Portuguese (pt-BR), Russian (ru-RU), Chinese (Simplified) (zh-Hans).

**Default languages supported by Multi-language identification (MLID)**: German (de-DE) , English United States (en-US) , Spanish (es-ES) , French (fr-FR).

### Change default languages supported by LID and MLID 

When [uploading a video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) through an API, you can specify to use other supported languages (listed in the table above) for LID and MLID by passing the `customLanguages` parameter. The `customLanguages` parameter allows up to 10 languages to be identified by LID or MLID.

> [!NOTE]
> Language identification (LID) and Multi-language identification (MLID) compares speech at the language level, such as English and German. 
> Do not include multiple locales of the same language in the custom languages list.

## Language support in front end experiences

The following table describes language support in the Azure Video Indexer front end experiences.

* website - the website column lists supported languages for the [Azure Video Indexer website](https://aka.ms/vi-portal-link). For for more information, see [Get started](video-indexer-get-started.md).
* widgets - the [widgets](video-indexer-embed-widgets.md) column lists supported languages for translating the index file. For for more information, see [Get started](video-indexer-embed-widgets.md).

| **Language**                      | **Code**       | **Website** | **Widgets**   | 
|:------------:|:------------:|:--------------------:|:-------:|
| Afrikaans                        | `af-ZA`      |           | ✔ |  
| Arabic (Iraq)                    | `ar-IQ`      |           |  |
| Arabic (Israel)                  | `ar-IL`      |           |  | 
| Arabic (Jordan)                  | `ar-JO`      |           |  | 
| Arabic (Kuwait)                  | `ar-KW`      |           |  |  
| Arabic (Lebanon)                 | `ar-LB`      |           |  |
| Arabic (Oman)                    | `ar-OM`      |           |  | 
| Arabic (Palestinian Authority)   | `ar-PS`      |           |  | 
| Arabic (Qatar)                   | `ar-QA`      |           |  |
| Arabic (Saudi Arabia)            | `ar-SA`      |           |  | 
| Arabic (United Arab Emirates)    | `ar-AE`      |           |  | 
| Arabic Egypt                     | `ar-EG`      |           | ✔ |
| Arabic Modern Standard (Bahrain) | `ar-BH`      |           |  | 
| Arabic Syrian Arab Republic      | `ar-SY`      |           |  | 
| Bangla                           | `bn-BD`      |           |✔  |
| Bosnian                          | `bs-Latn`    |           | ✔ |  
| Bulgarian                        | `bg-BG`      |           |  ✔|  
| Catalan                          | `ca-ES`      |           | ✔ | 
| Chinese (Cantonese Traditional)  | `zh-HK`      |           | ✔ | 
| Chinese (Simplified)             | `zh-Hans`    |      ✔    |✔  |
| Chinese (Simplified)             | `zh-CK`      |      ✔    |✔  |
| Chinese (Traditional)            | `zh-Hant`    |           |✔  |  
| Croatian                         | `hr-HR`      |           |  |
| Czech                            | `cs-CZ`      |    ✔      | ✔ | 
| Danish                           | `da-DK`      |           | ✔ | 
| Dutch                            | `nl-NL`      |   ✔       | ✔ | 
| English Australia                | `en-AU`      |           | ✔ |  
| English United Kingdom           | `en-GB`      |           |  ✔| 
| English United States            | `en-US`      |   ✔       |  ✔ | 
| Estonian                         | `et-EE`      |           |  ✔|  
| Fijian                           | `en-FJ`      |           |  ✔| 
| Filipino                         | `fil-PH`     |           |✔  | 
| Finnish                          | `fi-FI`      |           | ✔ | 
| French                           | `fr-FR`      |           | ✔  | 
| French (Canada)                  | `fr-CA`      | ✔         |✔  | 
| German                           | `de-DE`      | ✔         |  |
| Greek                            | `el-GR`      |           |✔  | 
| Haitian                          | `fr-HT`      |           | ✔ |
| Hebrew                           | `he-IL`      |           | ✔ |
| Hindi                            | `hi-IN`      | ✔         |✔  | 
| Hungarian                        | `hu-HU`      |  ✔        | ✔ | 
| Indonesian                       | `id-ID`      |           |  | 
| Italian                          | `it-IT`      |           |  ✔ | 
| Japanese                         | `ja-JP`      | ✔         | ✔  | 
| Kiswahili                        | `sw-KE`      | ✔         | ✔ | 
| Korean                           | `ko-KR`      |✔          |  ✔|  
| Latvian                          | `lv-LV`      |           |✔  |  
| Lithuanian                       | `lt-LT`      |           | ✔ |
| Malagasy                         | `mg-MG`      |           | ✔ | 
| Malay                            | `ms-MY`      |           |✔  | 
| Maltese                          | `mt-MT`      |           |  |
| Norwegian                        | `nb-NO`      |           | ✔ |
| Persian                          | `fa-IR`      |           |  |
| Polish                           | `pl-PL`      |  ✔        | ✔ | 
| Portuguese                       | `pt-BR`      | ✔         | ✔  | 
| Portuguese (Portugal)            | `pt-PT`      |           |✔  |  
| Romanian                         | `ro-RO`      |           |  ✔| 
| Russian                          | `ru-RU`      | ✔         | ✔  |
| Samoan                           | `en-WS`      |           |  | 
| Serbian (Cyrillic)               | `sr-Cyrl-RS` |           | ✔ | 
| Serbian (Latin)                  | `sr-Latn-RS` |           |  | 
| Slovak                           | `sk-SK`      |           | ✔ |
| Slovenian                        | `sl-SI`      |           | ✔ | 
| Spanish                          | `es-ES`      | ✔         |  ✔ |
| Spanish (Mexico)                 | `es-MX`      |           |  ✔| 
| Swedish                          | `sv-SE`      |  ✔        | ✔ | 
| Tamil                            | `ta-IN`      |           | ✔ | 
| Thai                             | `th-TH`      |           |✔  | 
| Tongan                           | `to-TO`      |           | ✔ |
| Turkish                          | `tr-TR`      | ✔         | ✔ | 
| Ukrainian                        | `uk-UA`      | ✔         |✔  |  
| Urdu                             | `ur-PK`      |           | ✔ |  
| Vietnamese                       | `vi-VN`      | ✔         | ✔ |

## Next steps

- [Overview](video-indexer-overview.md)
- [Release notes](release-notes.md)
