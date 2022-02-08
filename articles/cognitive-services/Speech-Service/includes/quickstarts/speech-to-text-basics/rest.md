---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
---

[!INCLUDE [Introduction](intro.md)]

## Prerequisites

[!INCLUDE [Prerequisites](../common/azure-prerequisites.md)]

## Convert speech to text

At a command prompt, run the following command. Insert the following values into the command:
- Your subscription key for the Speech service.
- Your Speech service region.
- The path for input audio files. You can generate audio files by using [text-to-speech](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

You should receive a response like the following one:

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

For more information, see the [speech-to-text REST API reference](../../../rest-speech-to-text.md).
