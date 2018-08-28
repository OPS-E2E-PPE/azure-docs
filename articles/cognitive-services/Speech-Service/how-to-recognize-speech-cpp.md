---
title: Recognize speech by using the Speech SDK for C++
titleSuffix: Microsoft Cognitive Services
description: |
  Learn how to recognize speech (from a file, from a microphone, with a customized model, continuously or one-shot) by using the Speech SDK for C++.
services: cognitive-services
author: wolfma61

ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
---

# Recognize speech by using the Speech SDK for C++

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

[!include[Introduction](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-intro.md)]

[!include[Introduction for top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#toplevel)]

[!include[Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-microphone.md)]

[!code-cpp[Speech recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

[!include[Introduction to using customized recognition](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-customized.md)]

[!code-cpp[Speech recognition by using a customized model](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

[!include[Introduction to using a continuous file](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-continuous.md)]

[!code-cpp[Continuous speech recognition](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Look for the code that's used in this article in the samples/cpp/windows/console folder.

## Next steps

- [How to recognize intents from speech](how-to-recognize-intents-from-speech-cpp.md)
- [How to translate speech](how-to-translate-speech-cpp.md)

