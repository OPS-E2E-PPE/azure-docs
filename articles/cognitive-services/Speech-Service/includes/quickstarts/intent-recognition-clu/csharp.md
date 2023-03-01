---
author: eric-urban
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 02/17/2023
ms.topic: include
ms.author: eur
---

[!INCLUDE [Header](../../common/csharp.md)]

[!INCLUDE [Introduction](intro.md)]

## Prerequisites

[!INCLUDE [Prerequisites](../../common/azure-prerequisites-clu.md)]

## Set up the environment
The Speech SDK is available as a [NuGet package](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) and implements .NET Standard 2.0. You install the Speech SDK later in this guide, but first check the [SDK installation guide](../../../quickstarts/setup-platform.md?pivots=programming-language-csharp) for any more requirements.

### Set environment variables

This example requires environment variables named `LANGUAGE_KEY`, `LANGUAGE_ENDPOINT`, `SPEECH_KEY`, and `SPEECH_REGION`.

[!INCLUDE [Environment variables](../../common/environment-variables-clu.md)]

## Create a Conversational Language Understanding project

[!INCLUDE [Deploy CLU model](deploy-clu-model.md)]

You'll use the project name and deployment name in the next section.

## Recognize intents from a microphone

Follow these steps to create a new console application and install the Speech SDK.

1. Open a command prompt where you want the new project, and create a console application with the .NET CLI. The `Program.cs` file should be created in the project directory.
    ```dotnetcli
    dotnet new console
    ```
1. Install the Speech SDK in your new project with the .NET CLI.
    ```dotnetcli
    dotnet add package Microsoft.CognitiveServices.Speech
    ```
1. Replace the contents of `Program.cs` with the following code. 

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Intent;
    
    class Program 
    {
        // This example requires environment variables named:
        // "LANGUAGE_KEY", "LANGUAGE_ENDPOINT", "SPEECH_KEY", and "SPEECH_REGION"
        static string languageKey = Environment.GetEnvironmentVariable("LANGUAGE_KEY");
        static string languageEndpoint = Environment.GetEnvironmentVariable("LANGUAGE_ENDPOINT");
        static string speechKey = Environment.GetEnvironmentVariable("SPEECH_KEY");
        static string speechRegion = Environment.GetEnvironmentVariable("SPEECH_REGION");

        // Your CLU project name and deployment name.
        static string cluProjectName = "YourProjectNameGoesHere";
        static string cluDeploymentName = "YourDeploymentNameGoesHere";
    
        async static Task Main(string[] args)
        {
            var speechConfig = SpeechConfig.FromSubscription(speechKey, speechRegion);        
            speechConfig.SpeechRecognitionLanguage = "en-US";
    
            using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    
            // Creates an intent recognizer in the specified language using microphone as audio input.
            using (var intentRecognizer = new IntentRecognizer(speechConfig, audioConfig))
            {
                var cluModel = new ConversationalLanguageUnderstandingModel(
                    languageKey,
                    languageEndpoint,
                    cluProjectName, 
                    cluDeploymentName);
                var collection = new LanguageUnderstandingModelCollection();
                collection.Add(cluModel);
                intentRecognizer.ApplyLanguageModels(collection);
    
                Console.WriteLine("Speak into your microphone.");
                var recognitionResult = await intentRecognizer.RecognizeOnceAsync().ConfigureAwait(false);
                
                // Checks result.
                if (recognitionResult.Reason == ResultReason.RecognizedIntent)
                {
                    Console.WriteLine($"RECOGNIZED: Text={recognitionResult.Text}");
                    Console.WriteLine($"    Intent Id: {recognitionResult.IntentId}.");
                    
                    // There is a known issue with the LanguageUnderstandingServiceResponse_JsonResult 
                    // property when used with CLU in the Speech SDK version 1.25. 
                    // The following should return JSON in a future release.
                    Console.WriteLine($"    Language Understanding JSON: {recognitionResult.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)}.");
                }
                else if (recognitionResult.Reason == ResultReason.RecognizedSpeech)
                {
                    Console.WriteLine($"RECOGNIZED: Text={recognitionResult.Text}");
                    Console.WriteLine($"    Intent not recognized.");
                }
                else if (recognitionResult.Reason == ResultReason.NoMatch)
                {
                    Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                }
                else if (recognitionResult.Reason == ResultReason.Canceled)
                {
                    var cancellation = CancellationDetails.FromResult(recognitionResult);
                    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
    
                    if (cancellation.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                    }
                }
            }
        }
    }
    ```

1. In `Program.cs` set the `cluProjectName` and `cluDeploymentName` variables to the names of your project and deployment. For information about how to create a CLU project and deployment, see [Create a Conversational Language Understanding project](#create-a-conversational-language-understanding-project).
1. To change the speech recognition language, replace `en-US` with another [supported language](~/articles/cognitive-services/speech-service/supported-languages.md). For example, `es-ES` for Spanish (Spain). The default language is `en-US` if you don't specify a language. For details about how to identify one of multiple languages that might be spoken, see [language identification](~/articles/cognitive-services/speech-service/language-identification.md). 

Run your new console application to start speech recognition from a microphone:

```console
dotnet run
```

> [!IMPORTANT]
> Make sure that you set the `LANGUAGE_KEY`, `LANGUAGE_ENDPOINT`, `SPEECH__KEY`, and `SPEECH__REGION` environment variables as described [above](#set-environment-variables). If you don't set these variables, the sample will fail with an error message.

Speak into your microphone when prompted. What you speak should be output as text: 

```console
Say something ...
RECOGNIZED: Text=Go ahead and delete the e-mail.
    Intent Id: Delete.
    Language Understanding JSON: 
```

> [NOTE]
> There is a known issue with the LanguageUnderstandingServiceResponse_JsonResult property when used with CLU in the Speech SDK version 1.25. You can get detailed JSON output in a future release. Via JSON, the intents are returned in the probability order of most likely to least likely. For example, the `topIntent` might be `Delete` with a confidence score of 0.95413816 (95.41%). The second most likely intent might be `Cancel` with a confidence score of 0.8985081 (89.85%).

## Remarks
Now that you've completed the quickstart, here are some additional considerations:

- This example uses the `RecognizeOnceAsync` operation to transcribe utterances of up to 30 seconds, or until silence is detected. For information about continuous recognition for longer audio, including multi-lingual conversations, see [How to recognize speech](~/articles/cognitive-services/speech-service/how-to-recognize-speech.md).
- To recognize speech from an audio file, use `FromWavFileInput` instead of `FromDefaultMicrophoneInput`:
    ```csharp
    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    ```
- For compressed audio files such as MP4, install GStreamer and use `PullAudioInputStream` or `PushAudioInputStream`. For more information, see [How to use compressed input audio](~/articles/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams.md).

## Clean up resources

[!INCLUDE [Delete resource](../../common/delete-resource-clu.md)]

