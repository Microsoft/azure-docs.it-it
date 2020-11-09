---
title: "Esercitazione: Estrarre i dettagli dell'immagine con l'API REST e C# - Ricerca immagini Bing"
titleSuffix: Azure Cognitive Services
description: Questa esercitazione illustra come creare un'applicazione C# che estrae i dettagli delle immagini usando l'API Ricerca immagini Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 12/06/2019
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 76670c6a061ce3f257d9df8cfe61b1cc020194c6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100219"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>Esercitazione: Estrarre i dettagli dell'immagine usando l'API Ricerca immagini Bing e C#

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](https://aka.ms/cogsvcs/bingmove).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](https://aka.ms/cogsvcs/bingmigration).

Nell'API Ricerca immagini Bing sono disponibili più [endpoint](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint). L'endpoint `/details` accetta una richiesta POST con un'immagine e può restituire un'ampia gamma di dettagli relativi all'immagine. L'applicazione C# invia un'immagine tramite l'API e visualizza i dettagli restituiti da Bing, costituiti da oggetti JSON, come illustrato di seguito:

![[Risultati JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

In questa esercitazione viene illustrato come:

> [!div class="checklist"]
> * Usare l'endpoint `/details` di Ricerca immagini in una richiesta `POST`
> * Specificare le intestazioni per la richiesta
> * Usare i parametri dell'URL per specificare i risultati
> * Caricare i dati dell'immagine e inviare la richiesta `POST`
> * Stampare i risultati del file JSON nella console

Il codice sorgente di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs).

## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di [Visual Studio 2017 o versioni successive](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>Costruire una richiesta di ricerca dei dettagli di un'immagine

Di seguito è riportato l'endpoint `/details` che accetta richieste POST con i dati di un'immagine nel corpo della richiesta. È possibile usare l'endpoint globale seguente o l'endpoint [sottodominio personalizzato](../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

Quando si costruisce l'URL della richiesta di ricerca, il parametro `modules` segue l'endpoint sopra riportato e specifica i tipi di dettagli che dovranno essere contenuti nei risultati:

* `modules=All`
* `modules=RecognizedEntities` (persone o località visibili nell'immagine)

Specificare `modules=All` nella richiesta POST per ottenere il testo JSON che include l'elenco seguente:

* `bestRepresentativeQuery`: query Bing che restituisce immagini simili a quella caricata
* `detectedObjects`: oggetti trovati nell'immagine
* `image`: metadati dell'immagine
* `imageInsightsToken`: token per richieste GET successive che ottengono `RecognizedEntities` (persone o località visibili nell'immagine) dall'immagine.
* `imageTags`: tag dell'immagine
* `pagesIncluding`: pagine Web che includono l'immagine
* `relatedSearches`: ricerche basate sui dettagli dell'immagine.
* `visuallySimilarImages`: immagini simili sul Web.

Specificare `modules=RecognizedEntities` nella richiesta POST per ottenere solo `imageInsightsToken`, che può essere usato in una richiesta GET successiva per identificare persone o località presenti nell'immagine.

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>Creare un oggetto WebClient e impostare le intestazioni per la richiesta API

Creare un oggetto `WebClient` e impostare le intestazioni. Tutte le richieste all'API di ricerca Bing richiedono un elemento `Ocp-Apim-Subscription-Key`. Una richiesta `POST` di caricamento di un'immagine deve specificare anche `ContentType: multipart/form-data`.

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>Caricare l'immagine e visualizzare i risultati

Il metodo `UpLoadFile()` della classe `WebClient` formatta i dati per la richiesta `POST`, oltre a formattare `RequestStream` e chiamare `HttpWebRequest`.

Chiamare `WebClient.UpLoadFile()` con l'endpoint `/details` e il file dell'immagine da caricare. Usare la risposta JSON per inizializzare un'istanza della struttura `SearchResult` e memorizzare la risposta.

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
La risposta JSON può essere stampata nella console.

## <a name="use-an-image-insights-token-in-a-request"></a>Usare il token imageInsightsToken in una richiesta

Per usare il token `ImageInsightsToken` restituito con i risultati di una richiesta `POST`, è possibile aggiungerlo a una richiesta `GET`. Ad esempio:

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

Se nell'immagine sono presenti persone o località identificabili, questa richiesta restituirà informazioni su di esse.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Visualizzare immagini e opzioni di ricerca in un'app Web a pagina singola ](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche

* [Informazioni di riferimento per l'API Ricerca immagini Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
