---
title: Estrazione dei dati - LUIS
description: Estrarre dati dal testo dell'espressione con finalità ed entità. Informazioni sul tipo di dati che è possibile estrarre da Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: dd7d113b1c23a0afec82a346e0f7baa1254ebbed
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500142"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Estrarre dati da testo di un'espressione con finalità ed entità
LUIS consente di ottenere informazioni da espressioni in linguaggio naturale dell'utente. Le informazioni vengono estratte in modo che possano essere usate da un programma, applicazione o chatbot per intervenire. Le sezioni seguenti spiegano quali dati vengono restituiti da finalità ed entità con esempi di JSON.

I dati più difficili da estrarre sono i dati di Machine Learning perché non si tratta di una corrispondenza esatta del testo. L'estrazione dei [](luis-concept-entity-types.md) dati delle entità di [](luis-concept-app-iteration.md) Machine Learning deve far parte del ciclo di creazione fino a quando non si è certi di ricevere i dati previsti.

## <a name="data-location-and-key-usage"></a>Posizione dei dati e uso di chiavi
LUIS estrae i dati dall'espressione dell'utente [nell'endpoint pubblicato.](luis-glossary.md#endpoint) La **richiesta HTTPS** (POST o GET) contiene l'espressione e alcune configurazioni facoltative, ad esempio ambienti di staging o produzione.

**Richiesta dell'endpoint di previsione V2**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**Richiesta dell'endpoint di previsione V3**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

`appID` è disponibile nella pagina **Settings** (Impostazioni) dell'app LUIS e in parte dell'URL (dopo `/apps/`) quando si modifica quell'app LUIS. `subscription-key` è la chiave endpoint usata per eseguire query nell'app. Sebbene sia possibile usare la chiave di creazione/avvio durante l'apprendimento di LUIS, è importante modificare la chiave endpoint in una chiave che supporti l'[uso previsto di LUIS](luis-limits.md#key-limits). L'unità `timezoneOffset` è in minuti.

La **risposta HTTPS** contiene tutte le informazioni su finalità ed entità che LUIS è in grado di determinare in base al modello attualmente pubblicato dell'endpoint di staging o produzione. L'URL dell'endpoint è disponibile nel sito Web [LUIS](luis-reference-regions.md), nella sezione **Manage** (Gestisci) della pagina **Keys and endpoints** (Chiavi ed endpoint).

## <a name="data-from-intents"></a>Dati da finalità
I dati principali sono dati dal **nome della finalità** con il punteggio superiore. La risposta dell'endpoint è:

#### <a name="v2-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

* * *

|Oggetto dati|Tipo di dati|Posizione dei dati|Valore|
|--|--|--|--|
|Finalità|string|topScoringIntent.intent|"GetStoreInfo"|

Se il chatbot o l'app che chiama LUIS prende una decisione in base a più di un punteggio di finalità, restituire tutti i punteggi delle finalità.


#### <a name="v2-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

Impostare il parametro querystring, `verbose=true` . La risposta dell'endpoint è:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)

Impostare il parametro querystring, `show-all-intents=true` . La risposta dell'endpoint è:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

* * *

Le finalità vengono ordinate dal punteggio più alto al più basso.

|Oggetto dati|Tipo di dati|Posizione dei dati|Valore|Punteggio|
|--|--|--|--|:--|
|Finalità|string|intents[0].intent|"GetStoreInfo"|0,984749258|
|Finalità|string|intents[0].intent|"None"|0,0168218873|

Se si aggiungono domini predefiniti, il nome della finalità indica il dominio, ad esempio `Utilties` o `Communication` e la finalità:

#### <a name="v2-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

* * *

|Dominio|Oggetto dati|Tipo di dati|Posizione dei dati|Valore|
|--|--|--|--|--|
|Utilità|Finalità|string|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Comunicazione|Finalità|string|intents[0].intent|<b>Communication</b>.StartOver"|
||Finalità|string|intents[2].intent|"None"|


## <a name="data-from-entities"></a>Dati da entità
La maggior parte dei chat bot e delle applicazioni ha bisogno di più del nome della finalità. Questi dati aggiuntivi e facoltativi provengono da entità trovate nell'espressione. Ogni tipo di entità restituisce informazioni diverse sulla corrispondenza.

Una singola parola o frase dell'espressione può corrispondere a più di un'entità. In questo caso, ogni entità corrispondente viene restituita con il relativo punteggio.

Tutte le entità vengono restituite nella matrice **di** entità della risposta dall'endpoint

## <a name="tokenized-entity-returned"></a>Entità in formato token restituita

Esaminare il [supporto dei token](luis-language-support.md#tokenization) in LUIS.


## <a name="prebuilt-entity-data"></a>Dati entità predefinita
Vengono trovate le entità [predefinite](luis-concept-entity-types.md) in base alla corrispondenza con espressione regolare usando il progetto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) open-source. Le entità predefinite vengono restituite nella matrice di entità e usano il nome tipo con prefisso `builtin::`.

## <a name="list-entity-data"></a>Dati entità elenco

[Le entità elenco](reference-entity-list.md) rappresentano un set fisso e chiuso di parole correlate insieme ai relativi sinonimi. LUIS non individua valori aggiuntivi per le entità elenco. Usare la funzione **consigliata** per visualizzare i suggerimenti per le nuove parole in base all'elenco corrente. Se sono presenti più entità elenco con lo stesso valore, ogni entità viene restituita nella query endpoint.

## <a name="regular-expression-entity-data"></a>Dati entità espressione regolare

[Un'entità di espressione regolare](reference-entity-regular-expression.md) estrae un'entità in base a un'espressione regolare specificata.

## <a name="extracting-names"></a>Estrazione di nomi
Ottenere i nomi da un'espressione è difficile perché un nome può essere qualsiasi combinazione di lettere e parole. Le opzioni disponibili variano a seconda del tipo di nome estratto. I suggerimenti seguenti non sono regole, ma linee guida.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Aggiungere le entità PersonName e GeographyV2 predefinite

Le entità [PersonName](luis-reference-prebuilt-person.md) e [GeographyV2](luis-reference-prebuilt-geographyV2.md) sono disponibili in alcune [impostazioni cultura della lingua](luis-reference-prebuilt-entities.md).

### <a name="names-of-people"></a>Nomi di persone

I nomi delle persone possono presentare un formato a seconda della lingua e delle impostazioni cultura. Usare un'entità **[personName](luis-reference-prebuilt-person.md)** precompilato o **[un'entità semplice](luis-concept-entity-types.md)** con ruoli di nome e cognome.

Se si usa l'entità semplice, assicurarsi di fornire esempi che usano il nome e il cognome in parti diverse dell'espressione, in espressioni di lunghezze diverse e espressioni in tutte le finalità, inclusa la finalità Nessuna. [Rivedere](./luis-how-to-review-endpoint-utterances.md) regolarmente le espressioni endpoint per etichettare qualsiasi nome non stimato correttamente.

### <a name="names-of-places"></a>Nomi di località

I nomi delle località sono impostati e noti, ad esempio città, contee, stati, province e paesi/aree geografiche. Usare l'entità predefinita **[geographyV2](luis-reference-prebuilt-geographyv2.md)** per estrarre le informazioni sulla posizione.

### <a name="new-and-emerging-names"></a>Nomi nuovi ed emergenti

Alcune app devono essere in grado di trovare nomi nuovi ed emergenti, ad esempio prodotti o aziende. Questi tipi di nomi sono il tipo più difficile di estrazione dei dati. Iniziare con **[un'entità semplice e](luis-concept-entity-types.md)** aggiungere un elenco di [frasi.](luis-concept-feature.md) [Rivedere](./luis-how-to-review-endpoint-utterances.md) regolarmente le espressioni endpoint per etichettare qualsiasi nome non stimato correttamente.

## <a name="patternany-entity-data"></a>Dati entità Pattern.any

[Pattern.any è](reference-entity-pattern-any.md) un segnaposto a lunghezza variabile usato solo nell'espressione modello di un criterio per contrassegnare l'inizio e la fine dell'entità. L'entità usata nel criterio deve essere trovata per poter applicare il criterio.

## <a name="sentiment-analysis"></a>Analisi del sentiment
Se l'analisi del sentiment è [configurata durante la](luis-how-to-publish-app.md#sentiment-analysis)pubblicazione di , la risposta JSON di LUIS include l'analisi del sentiment. Per ulteriori informazioni sull'analisi del sentiment, vedere la documentazione [Analisi del testo](../text-analytics/index.yml).

## <a name="key-phrase-extraction-entity-data"></a>Dati entità estrazione frasi chiave
[L'entità di estrazione frasi](luis-reference-prebuilt-keyphrase.md) chiave restituisce le frasi chiave nell'espressione, fornite [Analisi del testo](../text-analytics/index.yml).

## <a name="data-matching-multiple-entities"></a>Dati corrispondenti a più entità

LUIS restituisce tutte le entità individuate nell'espressione. Di conseguenza, il chatbot potrebbe dover prendere una decisione in base ai risultati.

## <a name="data-matching-multiple-list-entities"></a>Dati corrispondenti a più entità elenco

Se una parola o frase corrisponde a più entità elenco, la query di endpoint restituisce ciascuna entità elenco.

Per la query e l'app contiene la parola in più elenchi, LUIS riconosce tutte le entità e restituisce una matrice di entità come parte della risposta `when is the best time to go to red rock?` `red` dell'endpoint JSON.

## <a name="next-steps"></a>Passaggi successivi

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per ulteriori informazioni sull'aggiunta di entità all'app LUIS.
