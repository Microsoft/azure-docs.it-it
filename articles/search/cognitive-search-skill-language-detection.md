---
title: Competenza cognitiva di rilevamento lingua
titleSuffix: Azure Cognitive Search
description: Valuta il testo non strutturato e, per ogni record, restituisce un identificatore di lingua con un punteggio che indica il livello di attendibilità dell'analisi in una pipeline di arricchimento AI in Azure ricerca cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 078a9312a7ee1b3b0eafd000928ed74348a540c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548054"
---
#   <a name="language-detection-cognitive-skill"></a>Competenza cognitiva di rilevamento lingua

Il **rilevamento lingua** skill rileva la lingua del testo di input e segnala un singolo codice di lingua per ogni documento inviato nella richiesta. Il codice lingua è associato a un punteggio che indica il livello di attendibilità dell'analisi. Questa competenza usa i modelli di Machine Learning forniti da [Analisi del testo](../cognitive-services/text-analytics/overview.md) in Servizi cognitivi.

Questa funzionalità è particolarmente utile quando è necessario specificare la lingua del testo come input per altre competenze (ad esempio, la competenza [Analisi del sentiment](cognitive-search-skill-sentiment.md) o la competenza [Divisione del testo](cognitive-search-skill-textsplit.md)).

Il rilevamento della lingua sfrutta le librerie di elaborazione del linguaggio naturale di Bing, che supera il numero di [lingue e aree supportate](../cognitive-services/text-analytics/language-support.md) elencate per analisi del testo. L'elenco esatto delle lingue non è pubblicato, ma include tutte le lingue diffuse, oltre a varianti, dialetti e alcune lingue regionali e culturali. Se il contenuto è espresso in un linguaggio usato meno di frequente, è possibile [provare l'API rilevamento lingua](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) per verificare se restituisce un codice. La risposta per le lingue che non è possibile rilevare è `(Unknown)`.

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di cracking dei documenti in Ricerca cognitiva di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione di immagini sono descritti nella [pagina dei prezzi di Ricerca cognitiva di Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limiti dei dati
Le dimensioni massime di un record devono essere di 50.000 caratteri in base alla misurazione di [`String.Length`](/dotnet/api/system.string.length). Se è necessario suddividere i dati prima di inviarli alla competenza di rilevamento della lingua, è possibile usare la [capacità di suddivisione del testo](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Input | Descrizione |
|---------------------|-------------|
| `defaultCountryHint` | Opzionale È possibile specificare un codice paese della lettera ISO 3166-1 Alpha-2 2 da usare come hint per il modello di rilevamento della lingua se non è possibile evitare ambiguità nel linguaggio. Per ulteriori informazioni, vedere [la documentazione analisi del testo](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) in questo argomento. In particolare, il `defaultCountryHint` parametro viene utilizzato con documenti che non specificano l' `countryHint` input in modo esplicito.  |
| `modelVersion`   | Opzionale Versione del modello da utilizzare quando si chiama il servizio Analisi del testo. Per impostazione predefinita, la versione più recente disponibile quando non viene specificata. Si consiglia di non specificare questo valore a meno che non sia assolutamente necessario. Per ulteriori informazioni, vedere [controllo delle versioni dei modelli nella API analisi del testo](../cognitive-services/text-analytics/concepts/model-versioning.md) . |

## <a name="skill-inputs"></a>Input competenze

I parametri fanno distinzione tra maiuscole e minuscole.

| Input     | Descrizione |
|--------------------|-------------|
| `text` | Testo da analizzare.|
| `countryHint` | Un codice paese della lettera ISO 3166-1 Alpha-2 2 da usare come hint per il modello di rilevamento della lingua se non è in grado di distinguere il linguaggio. Per ulteriori informazioni, vedere [la documentazione analisi del testo](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) in questo argomento. |

## <a name="skill-outputs"></a>Output competenze

| Nome output    | Descrizione |
|--------------------|-------------|
| `languageCode` | Il codice di lingua ISO 6391 per la lingua identificata. Ad esempio, "en". |
| `languageName` | Il nome della lingua. Ad esempio, "Inglese". |
| `score` | Immettere un valore compreso tra 0 e 1. La probabilità che lingua sia identificata correttamente. Il punteggio può essere inferiore a 1 se la frase contiene lingue miste.  |

##  <a name="sample-definition"></a>Definizione di esempio

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Input di esempio

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>Output di esempio

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)