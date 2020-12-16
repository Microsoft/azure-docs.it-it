---
title: Usare il riconoscimento di entità con l'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Informazioni su come identificare e risolvere l'ambiguità dell'identità di un'entità trovata nel testo con l'API REST di Analisi del testo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/15/2020
ms.author: aahi
ms.openlocfilehash: 9b90f177432de11f8281d03021b38bae647dadf2
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562532"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Come usare il riconoscimento delle entità denominate in Analisi del testo

Il API Analisi del testo consente di usare un testo non strutturato e restituisce un elenco di entità ambiguità, con collegamenti ad altre informazioni sul Web. L'API supporta il riconoscimento delle entità denominate (NER) per diverse categorie di entità e il collegamento di entità.

## <a name="entity-linking"></a>Collegamento delle entità

Il collegamento di entità è la possibilità di identificare e risolvere l'ambiguità dell'identità di un'entità trovata nel testo (ad esempio, determinare se un'occorrenza della parola "Mars" si riferisce al pianeta o al Dio di guerra romano). Questo processo richiede la presenza di una Knowledge base in una lingua appropriata per collegare entità riconosciute nel testo. Il collegamento di entità USA [Wikipedia](https://www.wikipedia.org/) come questa Knowledge base.

## <a name="named-entity-recognition-ner"></a>Riconoscimento delle entità denominate (NER)

Il riconoscimento delle entità denominate è la possibilità di identificare entità diverse nel testo e di classificarle in classi o tipi predefiniti quali: persona, posizione, evento, prodotto e organizzazione.  

## <a name="personally-identifiable-information-pii"></a>Informazioni personali

La funzionalità informazioni personali fa parte di NER e consente di identificare e offuscare le entità riservate nel testo associate a un singolo utente, ad esempio il numero di telefono, l'indirizzo di posta elettronica, l'indirizzo postale, il numero di Passport.

## <a name="named-entity-recognition-features-and-versions"></a>Funzionalità e versioni di riconoscimento entità denominate

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Funzionalità                                                         | NER v 3.0 | NER v 3.1-Preview. 3 |
|-----------------------------------------------------------------|--------|----------|
| Metodi per richieste singole e batch                          | X      | X        |
| Riconoscimento delle entità espanso tra diverse categorie           | X      | X        |
| Separare gli endpoint per l'invio di entità di collegamento e richieste NER. | X      | X        |
| Riconoscimento di entità di `PII` informazioni personali () e di integrità ( `PHI` )        |        | X        |
| Redazione di `PII`        |        | X        |

Per informazioni, vedere Supporto per le [lingue](../language-support.md) .

Il riconoscimento delle entità denominate V3 fornisce il rilevamento espanso tra più tipi. Attualmente, NER v 3.0 è in grado di riconoscere le entità nella [categoria di entità generale](../named-entity-types.md).

Il riconoscimento delle entità denominato v 3.1-Preview. 3 include le funzionalità di rilevamento di v 3.0 e: 
* Possibilità di rilevare le informazioni personali ( `PII` ) utilizzando l' `v3.1-preview.3/entities/recognition/pii` endpoint. 
* Parametro facoltativo `domain=phi` per rilevare le informazioni riservate sull'integrità ( `PHI` ).
* [Operazione asincrona](text-analytics-how-to-call-api.md) che usa l' `/analyze` endpoint.

Per altre informazioni, vedere l'articolo relativo alle [categorie di entità](../named-entity-types.md) e la sezione relativa agli [endpoint di richiesta](#request-endpoints) di seguito. 

## <a name="sending-a-rest-api-request"></a>Invio di una richiesta all'API REST

### <a name="preparation"></a>Preparazione

È necessario disporre di documenti JSON nel formato seguente: ID, testo, lingua.

Ogni documento deve essere composto da un massimo di 5.120 caratteri ed è possibile avere fino a 1.000 elementi (ID) per raccolta. La raccolta viene inviata nel corpo della richiesta.

### <a name="structure-the-request"></a>Strutturare la richiesta

Creare una richiesta POST. È possibile usare la **console di testing API** o di [posting](text-analytics-how-to-call-api.md) nei collegamenti seguenti per strutturare rapidamente e inviarne una. 

> [!NOTE]
> È possibile trovare la chiave e l'endpoint per la risorsa Analisi del testo nel portale di Azure. Si trovano in **Gestione risorse** nella pagina **Avvio rapido** della risorsa. 


### <a name="request-endpoints"></a>Endpoint di richiesta

#### <a name="version-31-preview3"></a>[Versione 3,1-Preview. 3](#tab/version-3-preview)

Il riconoscimento delle entità denominate `v3.1-preview.3` utilizza endpoint distinti per le richieste ner, pii e di collegamento di entità. Usare un formato URL riportato di seguito in base alla richiesta.

**Collegamento di entità**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/linking`

[Informazioni di riferimento sul riconoscimento entità denominato versione 3,1-Preview per `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesLinking)

**Riconoscimento di entità denominate**
* Entità generali- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/general`

[Informazioni di riferimento sul riconoscimento entità denominato versione 3,1-Preview per `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionGeneral)

**Informazioni personali**
* Informazioni personali ( `PII` )- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii`

È anche possibile usare il `domain=phi` parametro facoltativo per rilevare `PHI` le informazioni sull'integrità () nel testo. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi`

A partire da `v3.1-preview.3` , la risposta JSON include una `redactedText` proprietà che contiene il testo di input modificato in cui le entità pii rilevate vengono sostituite da un `*` per ogni carattere nelle entità.

[Informazioni di riferimento sul riconoscimento entità denominato versione 3,1-Preview per `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionPii)

**Operazione asincrona**

A partire da `v3.1-preview.3` , è possibile inviare richieste ner in modo asincrono usando l' `/analyze` endpoint.

* Operazione asincrona- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze`

Per informazioni sull'invio di richieste asincrone, vedere [come chiamare il API analisi del testo](text-analytics-how-to-call-api.md) .

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Il riconoscimento delle entità denominate V3 Usa endpoint distinti per le richieste NER e per il collegamento di entità. Usare un formato URL seguente in base alla richiesta:

**Collegamento di entità**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Riferimento per il riconoscimento delle entità denominato versione 3,0 per `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**Riconoscimento di entità denominate**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Riferimento per il riconoscimento delle entità denominato versione 3,0 per `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Impostare un'intestazione della richiesta per includere la chiave dell'API Analisi del testo. Nel corpo della richiesta fornire i documenti JSON preparati.

## <a name="example-requests"></a>Richieste di esempio

#### <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-preview)

### <a name="example-synchronous-ner-request"></a>Esempio di richiesta NER sincrona 

Il codice JSON seguente è un esempio di contenuto che è possibile inviare all'API. Il formato della richiesta è identico per entrambe le versioni dell'API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

### <a name="example-asynchronous-ner-request"></a>Richiesta NER di esempio asincrona

Se si usa l' `/analyze` endpoint per l' [operazione asincrona](text-analytics-how-to-call-api.md), si otterrà una risposta contenente le attività inviate all'API.

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}
```

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

### <a name="example-synchronous-ner-request"></a>Esempio di richiesta NER sincrona 

La versione 3,0 include solo operazioni sincrone. Il codice JSON seguente è un esempio di contenuto che è possibile inviare all'API. Il formato della richiesta è identico per entrambe le versioni dell'API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

---

## <a name="post-the-request"></a>Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Vedere la sezione relativa ai [limiti dei data](../overview.md#data-limits) nella panoramica per informazioni sulle dimensioni e il numero di richieste che è possibile inviare al minuto e al secondo.

L'API Analisi del testo è senza stato. Non vengono archiviati dati nell'account e i risultati vengono restituiti immediatamente nella risposta.

## <a name="view-results"></a>Visualizzazione dei risultati

Tutte le richieste POST restituiscono una risposta in formato JSON con gli ID e le proprietà dell'entità rilevata.

L'output viene restituito immediatamente. Si possono trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale e quindi importarlo in un'applicazione che consente di ordinare, cercare e modificare i dati. A causa del supporto multilingue e emoji, la risposta può contenere offset di testo. Per ulteriori informazioni, vedere [come elaborare gli offset di testo](../concepts/text-offsets.md).

### <a name="example-responses"></a>Risposte di esempio

La versione 3 fornisce endpoint distinti per il collegamento generale di NER, PII ed entità. La versione 3,1-pareview include una modalità di analisi asincrona. Di seguito sono riportate le risposte per queste operazioni. 

#### <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-preview)

### <a name="synchronous-example-results"></a>Risultati di esempio sincroni

Esempio di risposta NER generale:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

Esempio di risposta PII:

```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Esempio di risposta di collegamento di un'entità:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```

### <a name="example-asynchronous-result"></a>Risultato asincrono di esempio

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Esempio di risposta NER generale:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

---


## <a name="summary"></a>Summary

In questo articolo si sono appresi i concetti e il flusso di lavoro per il collegamento delle entità usando Analisi del testo nei Servizi cognitivi. In sintesi:

* I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
* Le richieste POST vengono inviate a uno o più endpoint, usando una [chiave di accesso personalizzata e un endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) valido per la sottoscrizione.
* L'output di risposta, che consiste di entità collegate (inclusi punteggi di attendibilità, offset e collegamenti Web per ogni ID di documento) può essere usato in qualsiasi applicazione

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Analisi del testo](../overview.md)
* [Uso della libreria client di Analisi del testo](../quickstarts/client-libraries-rest-api.md)
* [Novità](../whats-new.md)
