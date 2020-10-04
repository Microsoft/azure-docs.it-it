---
title: Estrazione di frasi chiave con l'API REST Analisi del testo
titleSuffix: Azure Cognitive Services
description: Come estrarre frasi chiave usando l'API REST Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: e4a652b146286965c68154bd362525861158ecb2
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704379"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Esempio: come estrarre frasi chiave usando Analisi del testo

L'API [Estrazione frasi chiave](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) valuta il testo non strutturato e restituisce un elenco di espressioni chiave per ogni documento JSON.

Questa funzionalità è utile se è necessario identificare rapidamente i punti rilevanti in una raccolta di documenti. Ad esempio, dato il testo di input "Il cibo era delizioso e il personale era meraviglioso", il servizio restituisce i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso".

Per altre informazioni, vedere [Linguaggi supportati](../text-analytics-supported-languages.md).

> [!TIP]
> Analisi del testo offre anche un'immagine del contenitore Docker basata su Linux per l'estrazione delle frasi chiave, di conseguenza è possibile [installare ed eseguire il contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) vicino ai dati.

## <a name="preparation"></a>Preparazione

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

L'estrazione di frasi chiave risulta più efficiente quando si elaborano grandi quantità di testo, diversamente dall'analisi del sentiment, che offre prestazioni migliori con quantità minori di testo. Per ottenere risultati ottimali da entrambe le operazioni, provare a ristrutturare gli input di conseguenza.

È necessario disporre di documenti JSON nel formato seguente: ID, testo, lingua

Le dimensioni dei documenti devono essere inferiori a 5.120 caratteri per documento e ogni raccolta può contenere fino a 1.000 elementi (ID). La raccolta viene inviata nel corpo della richiesta. L'esempio seguente illustra il contenuto che è possibile inviare per l'estrazione di espressioni chiave.

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Passaggio 1: Strutturare la richiesta

Per informazioni sulla definizione della richiesta, vedere [Come chiamare l'API Analisi del testo](text-analytics-how-to-call-api.md). Per comodità si ridefiniscono i punti seguenti:

+ Creare una richiesta **post** . Vedere la documentazione dell'API per questa richiesta: [API frasi chiave](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases).

+ Impostare l'endpoint HTTP per l'estrazione delle frasi chiave usando una risorsa di Analisi del testo in Azure oppure un'istanza di un [contenitore di Analisi del testo](text-analytics-how-to-install-containers.md). È necessario includere `/text/analytics/v3.0/keyPhrases` nell'URL. Ad esempio: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

+ Impostare un'intestazione della richiesta in modo da includere la [chiave di accesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) per le operazioni di Analisi del testo.

+ Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

> [!Tip]
> Usare [Postman](text-analytics-how-to-call-api.md) oppure aprire la **console di test dell'API** nella [documentazione](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) per strutturare una richiesta e inviarla tramite POST al servizio.

## <a name="step-2-post-the-request"></a>Passaggio 2: Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Per informazioni sulle dimensioni e sul numero delle richieste che è possibile inviare al minuto e al secondo, vedere la sezione [Limiti dei dati](../overview.md#data-limits) nella panoramica.

Tenere presente che il servizio è senza stato. Nessun dato viene archiviato nell'account. I risultati vengono restituiti immediatamente nella risposta.

## <a name="step-3-view-results"></a>Passaggio 3: Visualizzare i risultati

Tutte le richieste POST restituiscono una risposta JSON formattata con gli ID e le proprietà rilevate. L'ordine delle frasi chiave restituite viene determinato internamente dal modello.

L'output viene restituito immediatamente. Si possono trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale e quindi importarlo in un'applicazione che consente di ordinare, cercare e modificare i dati.

Di seguito è riportato un esempio dell'output per l'estrazione di frasi chiave dall'endpoint v 3.1-Preview. 2:

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
Come indicato, l'analizzatore trova e rimuove le parole non essenziali e mantiene singoli termini o frasi che sembrano essere il soggetto o l'oggetto di una frase.

## <a name="summary"></a>Riepilogo

In questo articolo si sono appresi i concetti e il flusso di lavoro per l'estrazione di frasi chiave tramite Analisi del testo in Servizi cognitivi. In sintesi:

+ L'[API Estrazione frasi chiave](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) è disponibile per le lingue selezionate.
+ I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
+ La richiesta POST è a un endpoint `/keyphrases`, usando [una chiave di accesso e un endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personalizzati validi per la sottoscrizione.
+ L'output di risposta, costituito da parole o frasi chiave per ogni ID documento, può essere trasmesso a qualsiasi app che accetta JSON, tra cui Microsoft Office Excel e Power BI, solo per citarne alcune.

## <a name="see-also"></a>Vedere anche

 [Panoramica di Analisi del testo](../overview.md) [Domande frequenti](../text-analytics-resource-faq.md)</br>
 [Pagina del prodotto Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Analisi del testo](../overview.md)
* [Uso della libreria client di Analisi del testo](../quickstarts/text-analytics-sdk.md)
* [Novità](../whats-new.md)
