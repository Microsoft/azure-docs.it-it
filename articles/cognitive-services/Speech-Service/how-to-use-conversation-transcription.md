---
title: Trascrizione conversazione in tempo reale (anteprima) - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Scopri come usare la trascrizione delle conversazioni in tempo reale con Speech SDK. Disponibile per C'è, C ' e Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 64a9e11cec7164fb4421dd018238de9f0670382b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76263728"
---
# <a name="real-time-conversation-transcription-preview"></a>Trascrizione conversazione in tempo reale (anteprima)

L'API **ConversationTranscriber** di Speech SDK consente di trascrivere riunioni e altre conversazioni con la possibilità di aggiungere, rimuovere e identificare più partecipanti trasmettendo audio al servizio di riconoscimento vocale utilizzando `PullStream` o `PushStream`. Questo argomento richiede di sapere come usare la sintesi vocale con Speech SDK (versione 1.8.0 o successiva). Per ulteriori informazioni, vedere [Che cosa sono i servizi di riconoscimento vocale](overview.md).

## <a name="limitations"></a>Limitazioni

- L'API ConversationTranscriber è supportata per C'è, C 'e Java su Windows, Linux, e Android.
- Attualmente disponibile nelle lingue "en-US" e "zh-CN" nelle seguenti regioni: _centralus_ e _eastasia_.
- Richiede un array multimicrofono circolare a 7 microfoni con un flusso di riferimento di riproduzione. L'array del microfono deve soddisfare [le nostre specifiche.](https://aka.ms/sdsdk-microphone)
- [L'SDK dei dispositivi di riconoscimento vocale](speech-devices-sdk.md) fornisce dispositivi adatti e un'app di esempio che illustra la trascrizione delle conversazioni.

## <a name="optional-sample-code-resources"></a>Risorse di codice di esempio facoltativeOptional sample code resources

Speech Device SDK fornisce codice di esempio in Java per l'acquisizione audio in tempo reale usando 8 canali.

- [Codice di esempio del dispositivo ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Codice di esempio del kit di sviluppo di Azure KinectAzure Kinect Dev Kit code](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Prerequisiti

Una sottoscrizione al servizio di riconoscimento vocale. È possibile ottenere una sottoscrizione di [valutazione del riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/) se non ne hai una.

## <a name="create-voice-signatures"></a>Creare firme vocali

Il primo passo è quello di creare firme vocali per i partecipanti alla conversazione per un'identificazione efficiente degli oratori.

### <a name="audio-input-requirements"></a>Requisiti di ingresso audio

- Il file wave audio di input per la creazione di firme vocali deve essere in campioni a 16 bit, una frequenza di campionamento di 16 kHz e un formato a canale singolo (mono).
- La durata consigliata per ogni campione audio è compresa tra trenta secondi e due minuti.

### <a name="sample-code"></a>Codice di esempio

Nell'esempio seguente vengono illustrati due modi diversi per creare la firma vocale [usando l'API REST](https://aka.ms/cts/signaturegenservice) in C. Si noti che è necessario sostituire le informazioni reali per "YourSubscriptionKey", il nome del `{region}` file wave per "speakerVoice.wav" e l'area per e "YourServiceRegion" (_centralus_ o _eastasia_).

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Trascrizione di conversazioni

Il codice di esempio seguente illustra come trascrivere le conversazioni in tempo reale per tre relatori. Si presuppone che tu abbia già creato firme vocali per ogni altoparlante come mostrato sopra. Sostituire le informazioni reali con "YourSubscriptionKey" e "YourServiceRegion" durante la creazione dell'oggetto SpeechConfig.

Le evidenziazioni del codice di esempio includono:Sample code highlights include:

- Creazione `Conversation` di un `SpeechConfig` oggetto dall'oggetto utilizzando un identificatore di riunione generato`Guid.NewGuid()`
- Creazione `ConversationTranscriber` di un oggetto `JoinConversationAsync()` e partecipazione alla conversazione per avviare la trascrizione
- Registrazione degli eventi di interesse
- Aggiunta o rimozione di partecipanti alla conversazione utilizzando l'oggetto Conversazione
- Streaming dell'audio
- In Speech SDK versione 1.9.0 `int` `string` e successive entrambi i tipi di valore e sono supportati nel campo della versione della firma vocale.

La trascrizione e l'identificatore del relatore tornano negli eventi registrati.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Trascrizione conversazione asincrona](how-to-async-conversation-transcription.md)
