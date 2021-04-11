---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ab175d122da379e0b0d45465837cdebeb8ec8bb
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450593"
---
## <a name="upload-the-audio"></a>Caricare l'audio

Il primo passaggio per la trascrizione asincrona è inviare l'audio al servizio di trascrizione delle conversazioni usando l'SDK di riconoscimento vocale (versione 1.13.0 o successiva).

Questo esempio di codice illustra come creare un oggetto `ConversationTranscriber` per la modalità solo asincrona. Per trasmettere audio al trascrittore, è necessario aggiungere il codice di streaming audio derivato dalle [conversazioni di trascrizione in tempo reale con l'SDK di riconoscimento vocale](../../../../how-to-use-conversation-transcription.md). 

```csharp
async Task CompleteContinuousRecognition(ConversationTranscriber recognizer, string conversationId)
{
    var finishedTaskCompletionSource = new TaskCompletionSource<int>();

    recognizer.SessionStopped += (s, e) =>
    {
        finishedTaskCompletionSource.TrySetResult(0);
    };
    string canceled = string.Empty;

    recognizer.Canceled += (s, e) => {
        canceled = e.ErrorDetails;
        if (e.Reason == CancellationReason.Error)
        {
            finishedTaskCompletionSource.TrySetResult(0);
        }
    };

    await recognizer.StartTranscribingAsync().ConfigureAwait(false);
    await Task.WhenAny(finishedTaskCompletionSource.Task, Task.Delay(TimeSpan.FromSeconds(10)));
    await recognizer.StopTranscribingAsync().ConfigureAwait(false);
}

async Task<List<string>> GetRecognizerResult(ConversationTranscriber recognizer, string conversationId)
{
    List<string> recognizedText = new List<string>();
    recognizer.Transcribed += (s, e) =>
    {
        if (e.Result.Text.Length > 0)
        {
            recognizedText.Add(e.Result.Text);
        }
    };

    await CompleteContinuousRecognition(recognizer, conversationId);

    recognizer.Dispose();
    return recognizedText;
}

async Task UploadAudio()
{
    // Create the speech config object
    // Substitute real information for "YourSubscriptionKey" and "Region"
    SpeechConfig speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "Region");
    speechConfig.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");

    // Set the property for asynchronous transcription
    speechConfig.SetServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

    // Alternatively: set the property for real-time plus asynchronous transcription
    // speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

    // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
    // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
    PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
    // Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
    AudioStreamFormat audioStreamFormat = AudioStreamFormat.GetWaveFormatPCM(16000, 16, 8);
    // Create an input stream
    AudioInputStream audioStream = AudioInputStream.CreatePullStream(wavfilePullStreamCallback, audioStreamFormat);

    // Ensure the conversationId for a new conversation is a truly unique GUID
    String conversationId = Guid.NewGuid().ToString();

    // Create a Conversation
    using (var conversation = await Conversation.CreateConversationAsync(speechConfig, conversationId))
    {
        using (var conversationTranscriber = new ConversationTranscriber(AudioConfig.FromStreamInput(audioStream)))
        {
            await conversationTranscriber.JoinConversationAsync(conversation);
            // Helper function to get the real time transcription results
            var result = await GetRecognizerResult(conversationTranscriber, conversationId);
        }
    }
}
```

Se si vuole usare in tempo reale _più_ asincrono, commentare e rimuovere il commento dalle righe di codice appropriate come indicato di seguito:

```csharp
// Set the property for asynchronous transcription
// speechConfig.SetServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Alternatively: set the property for real-time plus asynchronous transcription
speechConfig.SetServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Ottenere i risultati della trascrizione

Installare **Microsoft. CognitiveServices. Speech. Remoteconversation versione 1.13.0 o successiva** tramite NuGet.

### <a name="sample-transcription-code"></a>Codice di trascrizione di esempio

Dopo aver creato il `conversationId` , creare un client di trascrizione della conversazione remota **RemoteConversationTranscriptionClient** nell'applicazione client per eseguire una query sullo stato della trascrizione asincrona. Creare un oggetto di  **RemoteConversationTranscriptionOperation** per ottenere un oggetto [operazione](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/core/Azure.Core#consuming-long-running-operations-using-operationt) con esecuzione prolungata. È possibile controllare lo stato dell'operazione o attenderne il completamento. 

```csharp
// Create the speech config
SpeechConfig config = SpeechConfig.FromSubscription("YourSpeechKey", "YourSpeechRegion");
// Create the speech client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(config);
// Create the remote operation
RemoteConversationTranscriptionOperation operation = 
                            new RemoteConversationTranscriptionOperation(conversationId, client);

// Wait for operation to finish
await operation.WaitForCompletionAsync(TimeSpan.FromSeconds(10), CancellationToken.None);
// Get the result of the long running operation
var val = operation.Value.ConversationTranscriptionResults;
// Print the fields from the results
foreach (var item in val)
{
    Console.WriteLine($"{item.Text}, {item.ResultId}, {item.Reason}, {item.UserId}, {item.OffsetInTicks}, {item.Duration}");
    Console.WriteLine($"{item.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)}");
}
Console.WriteLine("Operation completed");

```
