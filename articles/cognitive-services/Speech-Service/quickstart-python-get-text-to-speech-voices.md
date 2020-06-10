---
title: Elencare le voci per la sintesi vocale, Python - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come ottenere l'elenco completo di voci standard e neurali per un'area/endpoint usando Python. L'elenco viene restituito in formato JSON e la disponibilità delle voci varia in base all'area.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.custom: tracking-python
ms.openlocfilehash: ec1b03bf0b3cf95f65013bddbc54e15ab985198e
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84607999"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>Ottenere l'elenco delle voci per la sintesi vocale usando Python

Questo articolo illustra come ottenere l'elenco completo di voci standard e neurali per un'area/endpoint usando Python. L'elenco viene restituito in formato JSON e la disponibilità delle voci varia in base all'area. Per un elenco delle aree supportate, vedere [Aree](regions.md).

Per questo articolo è necessario un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa del servizio Voce. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](get-started.md) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

* Python 2.7.x o 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"></span></a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"></span></a> o l'editor di testo preferito
* Una chiave di sottoscrizione di Azure per il servizio Voce

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Python tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `pip install requests`.

Richieste è usato per le richieste HTTP al servizio di sintesi vocale.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Impostare la chiave di sottoscrizione e creare un prompt per la sintesi vocale

Nelle sezioni successive si creeranno i metodi per gestire l'autorizzazione, chiamare l'API Sintesi vocale e convalidare la risposta. Si potrà quindi iniziare con la creazione di una classe. In questa classe si inseriranno i metodi per lo scambio di token e la chiamata all'API Sintesi vocale.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key` è la chiave univoca ottenuta dal portale di Azure.

## <a name="get-an-access-token"></a>Ottenere un token di accesso

Questo endpoint necessita di un token di accesso per l'autenticazione. Per ottenere un token di accesso, è necessario uno scambio. In questo esempio la chiave di sottoscrizione del servizio Voce viene scambiata con un token di accesso usando l'endpoint `issueToken`.

L'esempio presuppone che la sottoscrizione del servizio Voce si trovi nell'area Stati Uniti occidentali. Se si usa un'area diversa, aggiornare il valore per `fetch_token_url`. Per un elenco completo, vedere [Aree](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiare questo codice nella classe `GetVoices`:

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

In questa sezione si creerà la richiesta e si salverà l'elenco delle voci restituite. In primo luogo, è necessario impostare `base_url` e `path`. Questo esempio presuppone che si usi l'endpoint Stati Uniti occidentali. Se la risorsa è registrata in un'area diversa, assicurarsi di aggiornare il valore di `base_url`. Per altre informazioni, vedere [Aree del servizio Voce](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Successivamente, aggiungere le intestazioni obbligatorie per la richiesta. Si creerà infine una richiesta da inviare al servizio. Se la richiesta ha esito positivo e viene restituito il codice di stato 200, la risposta viene scritta su file.

Copiare questo codice nella classe `GetVoices`:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

La procedura è quasi terminata. L'ultimo passaggio consiste nel creare un'istanza della classe e chiamare le funzioni.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

A questo punto è possibile eseguire l'esempio. Dalla riga di comando, o dalla sessione di terminale, passare alla directory del progetto ed eseguire:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Pulire le risorse

Assicurarsi di rimuovere eventuali informazioni riservate dal codice sorgente dell'app di esempio, ad esempio le chiavi di sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Python su GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Vedere anche

* [Informazioni di riferimento sull'API Sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md)
* [Esempi di campioni vocali per la creazione di una voce personalizzata](record-custom-voice-samples.md)
