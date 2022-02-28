---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 02/12/2022
ms.author: eur
---

[!INCLUDE [Header](../../common/python.md)]

[!INCLUDE [Introduction](intro.md)]

## Prerequisites

[!INCLUDE [Prerequisites](../../common/azure-prerequisites.md)]

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=PYTHON&Pillar=Speech&Product=speech-to-text&Page=quickstart&Section=Prerequisites" target="_target">I ran into an issue</a>

## Set up the environment

The Speech SDK for Python is available as a [Python Package Index (PyPI) module](https://pypi.org/project/azure-cognitiveservices-speech/). The Speech SDK for Python is compatible with Windows, Linux, and macOS. Install a version of [Python from 3.7 to 3.10](https://www.python.org/downloads/). You install the Speech SDK in the next section of this article, but first check the [platform-specific installation instructions](../../../quickstarts/setup-platform.md?pivots=programming-language-python) for any more requirements.

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=PYTHON&Pillar=Speech&Product=speech-to-text&Page=quickstart&Section=Prerequisites" target="_target">I ran into an issue</a>

## Recognize speech from a microphone

Follow these steps to create a new console application and install the Speech SDK.

1. Open a command prompt where you want the new project, and create a new file named `speech-recognition.py`.
1. Install the Speech SDK.
    ```console
    pip install azure-cognitiveservices-speech
    ```
1. Replace the contents of `speech_recognition.py` with the following code. 

    ```Python
    import azure.cognitiveservices.speech as speechsdk
    
    def recognize_from_microphone():
        speech_config = speechsdk.SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
        speech_config.speech_recognition_language="en-US"

        #To recognize speech from an audio file, use `filename` instead of `use_default_microphone`:
        #audio_config = speechsdk.audio.AudioConfig(filename="YourAudioFile.wav")
        audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
        speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
        
        print("Speak into your microphone.")
        speech_recognition_result = speech_recognizer.recognize_once_async().get()
    
        if speech_recognition_result.reason == speechsdk.ResultReason.RecognizedSpeech:
        print("Recognized: {}".format(speech_recognition_result.text))
        elif speech_recognition_result.reason == speechsdk.ResultReason.NoMatch:
            print("No speech could be recognized: {}".format(speech_recognition_result.no_match_details))
        elif speech_recognition_result.reason == speechsdk.ResultReason.Canceled:
            cancellation_details = speech_recognition_result.cancellation_details
            print("Speech Recognition canceled: {}".format(cancellation_details.reason))
            if cancellation_details.reason == speechsdk.CancellationReason.Error:
                print("Error details: {}".format(cancellation_details.error_details))
    
    recognize_from_microphone()
    ```
1. In `speech_recognition.py`, replace `YourSubscriptionKey` with your Speech resource key, and replace `YourServiceRegion` with your Speech resource region.
1. To change the speech recognition language, replace `en-US` with another [supported language](~/articles/cognitive-services/speech-service/supported-languages.md). For example, `es-ES` for Spanish (Spain). The default language is `en-us` if you don't specify a language. For details about how to identify one of multiple languages that might be spoken, see [language identification](~/articles/cognitive-services/speech-service/supported-languages.md). 

Run your new console application to start speech recognition from a microphone:

```console
python speech_recognition.py
```

Speak into your microphone when prompted. What you speak should be output as text: 

```console
Speak into your microphone.
RECOGNIZED: Text=I'm excited to try speech to text.
```

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=PYTHON&Pillar=Speech&Product=speech-to-text&Page=quickstart&Section=Prerequisites" target="_target">I ran into an issue</a>

## Clean up resources

[!INCLUDE [Delete resource](../../common/delete-resource.md)]
