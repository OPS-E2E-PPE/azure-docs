---
title:  Azure Video Indexer insights overview
description: This article gives a brief overview of Azure Video Indexer insights.
ms.topic: conceptual
ms.date: 10/19/2022
ms.author: juliako
---

# Azure Video Indexer insights

When a video is indexed, Azure Video Indexer analyzes the video and audio content by running 30+ AI models, generating rich insights. Insights contain an aggregated view of the data: transcripts, optical character recognition elements (OCRs), face, topics, emotions, etc. Once the video is indexed and analyzed, Azure Video Indexer produces a JSON content that contains details of the video insights. For example, each insight type includes instances of time ranges that show when the insight appears in the video. 

Here some common insights:

|**Insight**|**Description**|
|---|---|
|Audio effects|For more information, see [Audio effects detection](/legal/azure-video-indexer/audio-effects-detection-transparency-note?context=/azure/azure-video-indexer/context/context).|
|Scenes, shots, and keyframes|Selects the frame(s) that best represent each shot. Keyframes are the representative frames selected from the entire video based on aesthetic properties (for example, contrast and stableness). Scenes, shots, and keyframes are merged into one insight for easier consumption and navigation. When you select the desired scene you can see what shots and keyframes it consists of.  For more information, see [Scenes, shots, and keyframes](scenes-shots-keyframes.md).|
|Emotions|Identifies emotions based on speech and audio cues.|
|Faces|For more information, see [Faces detection](/legal/azure-video-indexer/face-detection-transparency-note?context=/azure/azure-video-indexer/context/context).|
|Textual logo detection|Matches a specific predefined text using Azure Video Indexer OCR. For example, if a user created a textual logo: "Microsoft", different appearances of the word *Microsoft* will be detected as the "Microsoft" logo. For more information, see [Detect textual logo](detect-textual-logo.md).
|Keywords|For more information, see [Keywords extraction](/legal/azure-video-indexer/keywords-transparency-note?context=/azure/azure-video-indexer/context/context).|
|Labels|For more information, see [Labels identification](/legal/azure-video-indexer/labels-identification-transparency-note?context=/azure/azure-video-indexer/context/context)|
|Named entities|For more information, see [Named entities](/legal/azure-video-indexer/named-entities-transparency-note?context=/azure/azure-video-indexer/context/context).|
|People|For more information, see [Observed people tracking & matched faces](/legal/azure-video-indexer/observed-matched-people-transparency-note?context=/azure/azure-video-indexer/context/context).|
|Topics|For more information, see [Topics inference](/legal/azure-video-indexer/topics-inference-transparency-note?context=/azure/azure-video-indexer/context/context).|
|OCR|For more information, see [OCR](/legal/azure-video-indexer/ocr-transparency-note?context=/azure/azure-video-indexer/context/context).|
|Sentiments|Sentiments are aggregated by their `sentimentType` field (`Positive`, `Neutral`, or `Negative`).|
|Speakers|Maps and understands which speaker spoke which words and when. Sixteen speakers can be detected in a single audio-file.|
|Transcript|For more information, see [Transcription, translation, language](/legal/azure-video-indexer/transcription-translation-lid-transparency-note?context=/azure/azure-video-indexer/context/context).|

For information about features and other insights, see [Azure Video Indexer insights](video-indexer-overview.md#video-models).

Once you [set up](video-indexer-get-started.md) an Azure Video Indexer account (see [account types](accounts-overview.md)) and [upload a video](upload-index-videos.md), you can view insights as described below.

## Get the insights using the website

To visually examine the video's insights, press the **Play** button on the video on the [Azure Video Indexer](https://www.videoindexer.ai/) website. 

![Screenshot of the Insights tab in Azure Video Indexer.](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

To get insights produced on the website or the Azure portal:

1. Browse to the [Azure Video Indexer](https://www.videoindexer.ai/) website and sign in.
1. Find a video whose output you want to examine.
1. Press **Play**.
1. Choose the **Insights** tab.
2. Select which insights you want to view (under the **View** drop-down, on the right-top corner).
3. Go to the **Timeline** tab to see timestamped transcript lines.
4. Select **Download** > **Insights (JSON)** to get the insights output file.
5. If you want to download artifacts, beware of the following:

    [!INCLUDE [artifacts](./includes/artifacts.md)]

## Get insights produced by the API

When indexing with an API and the response status is OK, you get a detailed JSON output as the response content. When calling the [Get Video Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index) API, we recommend passing `&includeSummarizedInsights=false`. 

[!INCLUDE [insights](./includes/insights.md)]

This API returns a URL only with a link to the specific resource type you request. An additional GET request must be made to this URL for the specific artifact. The file types for each artifact type vary depending on the artifact.

[!INCLUDE [artifacts](./includes/artifacts.md)]

## Examine the Azure Video Indexer output

For more information, see [Examine the Azure Video Indexer output]( video-indexer-output-json-v2.md).

## Next steps

[View and edit video insights](video-indexer-view-edit.md).
