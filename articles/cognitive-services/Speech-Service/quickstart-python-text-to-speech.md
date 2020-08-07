---
title: Eseguire la sintesi vocale, Python - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come eseguire la sintesi vocale usando Python e l'API REST Sintesi vocale. Il testo di esempio incluso in questo articolo è strutturato come SSML (Speech Synthesis Markup Language). Questo formato consente di scegliere la voce e la lingua della risposta di sintesi vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-python
ms.openlocfilehash: 2c5469aba87fe06afc7308d6bd1e52cd4d87f22a
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873484"
---
# <a name="convert-text-to-speech-using-python"></a>Eseguire la sintesi vocale con Python

Questo articolo illustra come eseguire la sintesi vocale usando Python e l'API REST Sintesi vocale. In questo articolo il corpo della richiesta è strutturato nel formato [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md), che consente di scegliere la voce e la lingua della risposta.

Per questo articolo è necessario un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa del servizio Voce. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](get-started.md) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

* Python 2.7.x o 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"></span></a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"></span></a> o l'editor di testo preferito
* Una chiave di sottoscrizione di Azure per il servizio Voce

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Python tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `tts.py`.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `pip install requests`.

Questi moduli vengono usati per scrivere la risposta di sintesi vocale in un file con un timestamp, costruire la richiesta HTTP e chiamare l'API Sintesi vocale.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Impostare la chiave di sottoscrizione e creare un prompt per la sintesi vocale

Nelle sezioni successive si creeranno i metodi per gestire l'autorizzazione, chiamare l'API Sintesi vocale e convalidare la risposta. Per iniziare, aggiungere il codice necessario affinché questo esempio funzioni con Python 2.7.x e 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Creare quindi una classe. In questa classe si inseriranno i metodi per lo scambio di token e la chiamata all'API Sintesi vocale.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` è la chiave univoca ottenuta dal portale di Azure. `tts` chiede all'utente di immettere il testo di cui eseguire la sintesi vocale. Questo input è un valore letterale di tipo stringa e pertanto i caratteri non devono essere preceduti da caratteri di escape. `timestr` ottiene infine l'ora corrente, che verrà usata per definire il nome del file.

## <a name="get-an-access-token"></a>Ottenere un token di accesso

L'API REST Sintesi vocale necessita di un token di accesso per l'autenticazione. Per ottenere un token di accesso, è necessario uno scambio. In questo esempio la chiave di sottoscrizione del servizio Voce viene scambiata con un token di accesso usando l'endpoint `issueToken`.

L'esempio presuppone che la sottoscrizione del servizio Voce si trovi nell'area Stati Uniti occidentali. Se si usa un'area diversa, aggiornare il valore per `fetch_token_url`. Per un elenco completo, vedere [Aree](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiare questo codice nella classe `TextToSpeech`:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Per altre informazioni sull'autenticazione, vedere [Eseguire l'autenticazione con un token di autenticazione](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Eseguire una richiesta e salvare la risposta

In questa sezione si creerà la richiesta e si salverà la risposta di sintesi vocale. In primo luogo, è necessario impostare `base_url` e `path`. Questo esempio presuppone che si usi l'endpoint Stati Uniti occidentali. Se la risorsa è registrata in un'area diversa, assicurarsi di aggiornare il valore di `base_url`. Per altre informazioni, vedere [Aree del servizio Voce](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

È quindi necessario aggiungere le intestazioni obbligatorie per la richiesta. Assicurarsi di aggiornare `User-Agent` con il nome della risorsa, che si trova nel portale di Azure, e impostare `X-Microsoft-OutputFormat` sul formato di output audio preferito. Per un elenco completo dei formati di output, vedere [Output audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Creare quindi il corpo della richiesta nel formato SSML (Speech Synthesis Markup Language). Questo esempio definisce la struttura e usa l'input `tts` creato in precedenza.

>[!NOTE]
> Questo esempio usa il carattere voce `Guy24kRUS`. Per un elenco completo di voci e lingue fornite da Microsoft, vedere [Supporto per le lingue](language-support.md).
> Se si vuole creare una voce unica, riconoscibile per il proprio marchio, vedere [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md).

Si creerà infine una richiesta da inviare al servizio. Se la richiesta ha esito positivo e viene restituito un codice di stato 200, la risposta di sintesi vocale viene scritta in un file con timestamp.

Copiare questo codice nella classe `TextToSpeech`:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

La procedura è quasi terminata. L'ultimo passaggio consiste nel creare un'istanza della classe e chiamare le funzioni.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

A questo punto è possibile eseguire l'app di sintesi vocale di esempio. Dalla riga di comando, o dalla sessione di terminale, passare alla directory del progetto ed eseguire:

```console
python tts.py
```

Quando richiesto, digitare un testo qualsiasi di cui eseguire la sintesi vocale. Se l'operazione ha esito positivo, il file della sintesi vocale viene salvato nella cartella del progetto. Riprodurlo con il lettore multimediale preferito.

## <a name="clean-up-resources"></a>Pulire le risorse

Assicurarsi di rimuovere eventuali informazioni riservate dal codice sorgente dell'app di esempio, ad esempio le chiavi di sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Python su GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Vedere anche

* [Informazioni di riferimento sull'API Sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Uso di Python e Speech SDK per la sintesi vocale](quickstarts/speech-to-text-from-microphone.md)
* [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md)
* [Esempi di campioni vocali per la creazione di una voce personalizzata](record-custom-voice-samples.md)
