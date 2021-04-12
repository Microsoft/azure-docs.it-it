---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 9d6d6ec36d5ab7d29b3050dafda5fd711b01f1f4
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108951"
---
Questo argomento di avvio rapido illustra i modelli di progettazione comuni per eseguire la sintesi vocale con Speech SDK. Si inizia con la configurazione di base e la sintesi e si passa ad esempi più avanzati per lo sviluppo di applicazioni personalizzate, tra cui:

* Recupero di risposte come flussi in memoria
* Personalizzazione della frequenza di campionamento e della velocità in bit dell'output
* Invio di richieste di sintesi tramite SSML (Speech Synthesis Markup Language)
* Uso di voci neurali

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python/text-to-speech) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare Speech SDK.

```Python
pip install azure-cognitiveservices-speech
```

Se si usa macOS e si verificano problemi di installazione, può essere necessario eseguire prima questo comando.

```Python
python3 -m pip install --upgrade pip
```

Dopo l'installazione di Speech SDK, includere le istruzioni import seguenti all'inizio dello script.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig). Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione.

> [!NOTE]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.

Esistono diversi modi per inizializzare [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig):

* Con una sottoscrizione: passare una chiave e l'area associata.
* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

In questo esempio viene creato un oggetto [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) usando una chiave e un'area di sottoscrizione. Per ottenere queste credenziali, seguire la procedura descritta in [Provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizzare la voce in un file

Successivamente, creare un oggetto [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer), che esegue le conversioni di sintesi vocale e invia l'output ad altoparlanti, file o altri flussi. [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer) accetta come parametri l'oggetto [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) creato nel passaggio precedente e un oggetto [`AudioOutputConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig) che specifica come devono essere gestiti i risultati dell'output.

Per iniziare, creare un oggetto `AudioOutputConfig` per scrivere automaticamente l'output in un file `.wav`, usando il parametro del costruttore `filename`.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Successivamente, creare un'istanza di `SpeechSynthesizer` passando l'oggetto `speech_config` e l'oggetto `audio_config` come parametri. Quindi, per eseguire la sintesi vocale e scrivere il risultato in un file, basta eseguire `speak_text_async()` con una stringa di testo.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Eseguire il programma. Un file `.wav` sintetizzato viene scritto nella posizione specificata. Si tratta di un esempio valido dell'utilizzo più semplice, ma in seguito viene illustrato come personalizzare l'output e gestire la relativa risposta in un flusso in memoria per scenari personalizzati.

## <a name="synthesize-to-speaker-output"></a>Sintetizzare l'output dell'altoparlante

In alcuni casi, si può scegliere di indirizzare la voce sintetizzata di output direttamente a un altoparlante. A questo scopo, usare l'esempio della sezione precedente, ma cambiare `AudioOutputConfig` rimuovendo il parametro `filename` e impostare `use_default_speaker=True`. L'output viene così indirizzato al dispositivo di output attivo corrente.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Ottenere il risultato come flusso in memoria

Per molti scenari di sviluppo di applicazioni vocali, i dati audio risultanti potrebbero essere necessari come flusso in memoria invece di essere scritti direttamente in un file. In questo modo è possibile creare un comportamento personalizzato per:

* Astrarre la matrice di byte risultante come flusso ricercabile per i servizi downstream personalizzati.
* Integrare il risultato con altri servizi o API.
* Modificare i dati audio, scrivere intestazioni di `.wav` personalizzate e così via.

È semplice apportare questa modifica dall'esempio precedente. Rimuovere prima di tutto `AudioConfig`, perché il comportamento di output verrà gestito manualmente da questo punto in poi per un maggior controllo. Passare quindi `None` per `AudioConfig` nel costruttore `SpeechSynthesizer`.

> [!NOTE]
> Se si passa `None` per `AudioConfig`, invece di ometterlo come nel precedente esempio di output nell'altoparlante, l'audio non verrà riprodotto per impostazione predefinita nel dispositivo di output attivo corrente.

Questa volta il risultato viene salvato in una variabile [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult). La proprietà `audio_data` contiene un oggetto `bytes` dei dati di output. Questo oggetto può essere gestito manualmente oppure è possibile usare la classe [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) per gestire il flusso in memoria. In questo esempio si usa il costruttore `AudioDataStream` per ottenere un flusso dal risultato.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

Da qui è possibile implementare qualsiasi comportamento personalizzato usando l'oggetto `stream` risultante.

## <a name="customize-audio-format"></a>Personalizzare il formato audio

La sezione seguente illustra come personalizzare gli attributi dell'output audio, tra cui:

* Tipo di file audio
* Frequenza di campionamento
* Profondità di bit

Per cambiare il formato audio, usare la funzione `set_speech_synthesis_output_format()` nell'oggetto `SpeechConfig`. Questa funzione prevede un oggetto `enum` di tipo [`SpeechSynthesisOutputFormat`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat), che è possibile usare per selezionare il formato di output. Per un [elenco di formati audio](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat) disponibili, vedere la documentazione di riferimento.

Sono disponibili varie opzioni per tipi di file diversi a seconda dei requisiti. Si noti che, per definizione, i formati non elaborati come `Raw24Khz16BitMonoPcm` non includono intestazioni audio. Usare formati non elaborati solo quando si è certi che l'implementazione downstream possa decodificare un flusso di bit non elaborato o se si prevede di creare manualmente le intestazioni in base alla profondità di bit, alla frequenza di campionamento, al numero di canali e così via.

In questo esempio si specifica un formato RIFF ad alta fedeltà `Riff24Khz16BitMonoPcm` impostando `SpeechSynthesisOutputFormat` nell'oggetto `SpeechConfig`. Analogamente all'esempio della sezione precedente, è possibile usare [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) per ottenere un flusso in memoria del risultato e quindi scriverlo in un file.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Eseguendo il programma, anche in questo caso verrà scritto un file `.wav` personalizzato nel percorso specificato.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Usare SSML per personalizzare le caratteristiche vocali

Il linguaggio SSML (Speech Synthesis Markup Language) consente di ottimizzare il tono, la pronuncia, la velocità del parlato, il volume e altro ancora dell'output della sintesi vocale inviando le richieste da XML Schema. In questa sezione viene illustrato un esempio di modifica della voce, ma per una guida più dettagliata, vedere l' [articolo sulle procedure relative a SSML](../../../speech-synthesis-markup.md).

Per iniziare a usare SSML per la personalizzazione, è necessario apportare una semplice modifica che cambia la voce.
Creare prima di tutto un nuovo file XML per la configurazione di SSML nella directory radice del progetto, in questo esempio `ssml.xml`. L'elemento radice è sempre `<speak>` e il wrapping del testo in un elemento `<voice>` consente di cambiare la voce usando il parametro `name`. Vedere l' [elenco completo](../../../language-support.md#neural-voices) delle voci **neurali** supportate.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

Successivamente, è necessario cambiare la richiesta di sintesi vocale in modo che faccia riferimento al file XML. La richiesta è essenzialmente la stessa, ma invece di usare la funzione `speak_text_async()` si usa `speak_ssml_async()`. Questa funzione prevede una stringa XML, quindi è prima di tutto necessario leggere la configurazione di SSML come stringa. Da qui, l'oggetto risultato è esattamente lo stesso degli esempi precedenti.

> [!NOTE]
> Se `ssml_string` contiene `ï»¿` all'inizio della stringa, è necessario rimuovere il formato BOM, in caso contrario il servizio restituirà un errore. A questo scopo, impostare il parametro `encoding` come segue: `open("ssml.xml", "r", encoding="utf-8-sig")`.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

> [!NOTE]
> Per modificare la voce senza usare SSML, è possibile impostare la proprietà `SpeechConfig` su usando `SpeechConfig.speech_synthesis_voice_name = "en-US-AriaNeural"`

## <a name="get-facial-pose-events"></a>Ottenere gli eventi di posa facciali

Il riconoscimento vocale può essere un modo efficace per guidare l'animazione di espressioni facciali.
Spesso [visemes](../../../how-to-speech-synthesis-viseme.md) vengono usati per rappresentare la chiave che si pone in un discorso osservato, ad esempio la posizione dei labbri, la mascella e la lingua durante la produzione di un fonema particolare.
È possibile sottoscrivere l'evento viseme in Speech SDK.
Quindi, è possibile applicare gli eventi viseme per animare la faccia di un carattere come riproduzione audio vocale.
Informazioni [su come ottenere gli eventi viseme](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
