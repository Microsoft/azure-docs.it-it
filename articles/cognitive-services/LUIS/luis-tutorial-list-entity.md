---
title: Estrarre entità di corrispondenza esatta del testo - LUIS
titleSuffix: Azure Cognitive Services
description: Informazioni su come aggiungere un'entità elenco per aiutare LUIS a etichettare le varianti di una parola o una frase.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73499488"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Usare un'entità elenco per migliorare il rilevamento di entità 
Questo articolo illustra l'uso di un' [entità elenco](luis-concept-entity-types.md) per aumentare il rilevamento delle entità. Non è necessario etichettare le entità elenco, in quanto rappresentano una corrispondenza esatta di termini.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare un'entità elenco 
> * Aggiungere sinonimi e valori normalizzati
> * Convalidare l'identificazione di entità migliorata

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Versione più recente di [Node.js](https://nodejs.org)
> * [App LUIS HomeAutomation](luis-get-started-create-app.md). Se non si ha l'app di domotica, creare una nuova app e aggiungere il dominio predefinito **HomeAutomation**. Eseguire il training dell'app e pubblicarla. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (se si eseguono query più volte), ID app, ID versione e [area](luis-reference-regions.md) per l'app LUIS.

> [!Tip]
> Se non si ha già una sottoscrizione, è possibile registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/).

Tutto il codice in questo articolo è disponibile nel [repository GitHub Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Usare l'app HomeAutomation
L'app HomeAutomation consente di controllare i dispositivi, ad esempio luci, sistemi di intrattenimento e controlli dell'ambiente, come riscaldamento e aria condizionata. Questi sistemi hanno numerosi nomi diversi che possono includere nomi di produttori, nomi alternativi, acronimi e parole gergali. 

Un sistema che ha molti nomi nei diversi paesi e nelle diverse culture è il termostato. Un termostato può controllare sia il sistema di raffreddamento che quello di riscaldamento per una casa o un edificio.

Idealmente, le espressioni seguenti dovrebbero risolversi nell'entità predefinita **HomeAutomation.Device**:

|#|Espressione|Entità identificata|score|
|--|--|--|--|
|1|turn on the ac|HomeAutomation.Device - "ac"|0.8748562|
|2|turn up the heat|HomeAutomation.Device - "heat"|0.784990132|
|3|make it colder|||

Le prime due espressioni sono mappate a dispositivi diversi. La terza espressione, "make it colder", non è mappata a un dispositivo, bensì richiede un risultato. LUIS non sa che il termine "colder" indica che il dispositivo richiesto è il termostato. Idealmente, LUIS dovrebbe risolvere tutte queste espressioni nello stesso dispositivo. 

## <a name="use-a-list-entity"></a>Usare un'entità elenco
L'entità HomeAutomation.Device è ideale per un numero ridotto di dispositivi o con poche varianti nei nomi. Per un palazzo di uffici o un campus, i nomi dei dispositivi sono molti e l'entità HomeAutomation.Device non è più utile. 

Un'**entità elenco** è una buona scelta per questo scenario, perché il set di termini per un dispositivo in un palazzo o in un campus è un set noto, anche se ha dimensioni molto grandi. Usando un'entità elenco, LUIS può ricevere tutti i valori possibili del set per il termostato e risolverli nel singolo dispositivo termostato. 

In questo articolo verrà creato un elenco di entità con il termostato. I nomi alternativi per un termostato in questo articolo sono i seguenti: 

|Nomi alternativi per thermostat|
|--|
| ac |
| a/c|
| a-c|
|heater|
|hot|
|hotter|
|cold|
|colder|

Se LUIS deve determinare frequentemente una nuova alternativa, un [elenco frasi](luis-concept-feature.md#how-to-use-phrase-lists) è un'opzione migliore.

## <a name="create-a-list-entity"></a>Creare un'entità elenco
Creare un file Node.js e copiare al suo interno il codice seguente. Modificare i valori authoringKey, appId, versionId e region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Usare il comando seguente per installare le dipendenze NPM ed eseguire il codice per creare l'entità elenco:

```console
npm install && node add-entity-list.js
```

L'output dell'esecuzione è l'ID dell'entità elenco:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Eseguire il training del modello
Eseguire il training di LUIS affinché il nuovo elenco influisca sui risultati di query. Il training è un processo in due parti, ovvero il training vero e proprio e quindi il controllo dello stato al termine del training. Per il training di un'app con molti modelli, potrebbero essere necessari alcuni minuti. Il codice seguente esegue il training dell'app e quindi ne attende il completamento. Il codice usa una strategia di attesa e nuovo tentativo per evitare l'errore 429 relativo a un eccessivo numero di richieste. 

Creare un file Node.js e copiare al suo interno il codice seguente. Modificare i valori authoringKey, appId, versionId e region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Usare il comando seguente per eseguire il codice per il training dell'app:

```console
node train.js
```

L'output dell'esecuzione è lo stato di ogni iterazione del training dei modelli LUIS. L'esecuzione seguente ha richiesto un solo controllo del training:

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>Pubblicare il modello
Eseguire la pubblicazione in modo che l'entità elenco sia disponibile dall'endpoint.

Creare un file Node.js e copiare al suo interno il codice seguente. Modificare i valori endpointKey, appId e region. È possibile usare authoringKey se non si prevede di chiamare questo file oltre il limite di quota.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Usare il comando seguente per eseguire il codice per la query sull'app:

```console
node publish.js
```

L'output seguente include l'URL dell'endpoint per le query. I risultati JSON reali includerebbero il valore appID reale. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Eseguire una query sull'app 
Eseguire una query sull'app dall'endpoint per dimostrare che l'entità elenco aiuta LUIS a determinare il tipo di dispositivo.

Creare un file Node.js e copiare al suo interno il codice seguente. Modificare i valori endpointKey, appId e region. È possibile usare authoringKey se non si prevede di chiamare questo file oltre il limite di quota.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Usare il comando seguente per eseguire il codice e la query sull'app:

```console
node train.js
```

L'output contiene i risultati della query. Poiché il codice ha aggiunto la coppia nome/valore **verbose** alla stringa di query, l'output include tutte le finalità e i relativi punteggi:

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

Il dispositivo specifico **Thermostat** viene identificato con una query orientata ai risultati "turn up the heat". Poiché l'entità HomeAutomation.Device originale è ancora nell'app, è possibile visualizzare anche i relativi risultati. 

Provare le altre due espressioni per vedere che anche per esse viene restituito il valore thermostat. 

|#|Espressione|Entità|type|Valore|
|--|--|--|--|--|
|1|turn on the ac| ac | DevicesList | Thermostat|
|2|turn up the heat|heat| DevicesList |Thermostat|
|3|make it colder|colder|DevicesList|Thermostat|

## <a name="next-steps"></a>Passaggi successivi

È possibile creare un'altra entità elenco per ampliare le posizioni dei dispositivi includendo stanze, piani o edifici. 
