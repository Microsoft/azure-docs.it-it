---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: trbye
ms.openlocfilehash: 2e6be94a7fc83c6da8bf7a46fde6ea0f5ef7c764
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400566"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Creare un'applicazione LUIS e ottenere una chiave dell'endpoint](../../../../quickstarts/create-luis.md)
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="support-and-updates"></a>Supporto e aggiornamenti

Gli aggiornamenti del pacchetto Speech SDK per Python sono distribuiti tramite PyPI e annunciati nelle [Note sulla versione](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Se è disponibile una nuova versione, è possibile eseguire l'aggiornamento ad essa con il comando `pip install --upgrade azure-cognitiveservices-speech`.
Controllare la versione attualmente installata esaminando la variabile `azure.cognitiveservices.speech.__version__`.

Se si verifica un problema o manca una funzionalità, vedere le [opzioni di supporto e assistenza](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Creare un'applicazione Python che usi Speech SDK

### <a name="run-the-sample"></a>Eseguire l'esempio

È possibile copiare il [codice di esempio](#sample-code) da questa guida introduttiva in un file di origine `quickstart.py` ed eseguirlo nell'ambiente di sviluppo integrato o nella console:

```Bash
python quickstart.py
```

In alternativa è possibile scaricare questa esercitazione della guida introduttiva come notebook [Jupyter](https://jupyter.org) dal [repository degli esempi di Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) ed eseguirla come notebook.

### <a name="sample-code"></a>Codice di esempio

> [!NOTE]
> Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).

```python
import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.audio.AudioConfig(filename=audio_filename)

# Creates a recognizer with the given settings
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)

print("Recognizing first result...")

# Starts speech recognition, and returns after a single utterance is recognized. The end of a
# single utterance is determined by listening for silence at the end or until a maximum of 15
# seconds of audio is processed.  The task returns the recognition text as result. 
# Note: Since recognize_once() returns only a single utterance, it is suitable only for single
# shot recognition like command or query. 
# For long-running multi-utterance recognition, use start_continuous_recognition() instead.
result = speech_recognizer.recognize_once()

# Checks result.
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installare e usare Speech SDK con Visual Studio Code

1. Scaricare e installare nel computer una versione a 64 bit di [Python](https://www.python.org/downloads/), dalla 3.5 alla 3.8.
1. Scaricare e installare [Visual Studio Code](https://code.visualstudio.com/Download).
1. Aprire Visual Studio Code e installare l'estensione Python. Selezionare **File** > **Preferenze** > **Estensioni** dal menu. Cercare **Python**.

   ![Installare l'estensione Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Creare una cartella in cui archiviare il progetto. Un esempio consiste nell'usare Esplora risorse.
1. In Visual Studio Code selezionare l'icona **File**. Quindi, aprire la cartella creata.

   ![Aprire una cartella](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Creare un nuovo file di origine Python `speechsdk.py`, selezionando l'icona Nuovo file.

   ![Creare un file](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copiare, incollare e salvare il [codice Python](#sample-code) nel file appena creato.
1. Inserire le informazioni sulla sottoscrizione del servizio Voce.
1. Se selezionato, un interprete Python verrà visualizzato sul lato sinistro della barra di stato nella parte inferiore della finestra.
   In caso contrario, visualizzare un elenco di interpreti Python disponibili. Aprire il riquadro comandi <kbd>CTRL+MAIUSC+P</kbd> e immettere **Python: Select Interpreter** (Python: Seleziona interprete). Sceglierne uno appropriato.
1. È possibile installare il pacchetto Speech SDK per Python da Visual Studio Code. È possibile farlo se non è stato ancora installato per l'interprete Python selezionato.
   Per installare il pacchetto Speech SDK, aprire un terminale. Aprire di nuovo il riquadro comandi <kbd>CTRL+MAIUSC+P</kbd> e immettere **Terminal: Create New Integrated Terminal** (Crea nuovo terminale integrato).
   Nel terminale visualizzato immettere il comando `python -m pip install azure-cognitiveservices-speech` oppure il comando appropriato per il sistema.
1. Per eseguire il codice di esempio, fare doppio clic su un punto qualsiasi all'interno dell'editor. Selezionare **Run Python File in Terminal** (Esegui file Python nel terminale).
   I primi 15 secondi di input vocale dal file audio verranno riconosciuti e registrati nella finestra della console.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

In caso di problemi durante l'uso di queste istruzioni, consultare la più completa [esercitazione su Python in Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]