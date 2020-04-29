---
title: Gestione di errori ed eccezioni nei flussi di lavoro
description: Informazioni su come gestire gli errori e le eccezioni che si verificano nelle attività e nei flussi di lavoro automatizzati creati usando app per la logica di Azure
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79284031"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Gestire errori ed eccezioni in App per la logica di Azure

Il modo adottato da qualsiasi architettura di integrazione per gestire in maniera appropriata i tempi di inattività o i problemi causati da sistemi dipendenti può costituire una difficoltà. Per contribuire a creare integrazioni solide e resilienti che consentano di gestire correttamente problemi ed errori, App per la logica offre un'esperienza all'avanguardia per la gestione degli errori e delle eccezioni.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Criteri di ripetizione dei tentativi

Per la gestione degli errori e delle eccezioni di base, è possibile usare un *criterio di ripetizione* in qualsiasi azione o trigger, se supportato, ad esempio, vedere [azione http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). I criteri di ripetizione specificano se e come l'azione o il trigger ripete la richiesta in caso di timeout o esito negativo della richiesta originale, cioè qualsiasi richiesta con una risposta 408, 429 o 5xx. Se non vengono usati altri criteri di ripetizione, si usano quelli predefiniti.

Ecco i tipi di criteri di ripetizione:

| Type | Descrizione |
|------|-------------|
| **Predefinito** | Questi criteri inviano fino a quattro richieste di ripetizione a intervalli con *crescita esponenziale* di 7,5 secondi ma con un limite massimo compreso tra 5 e 45 secondi. |
| **Intervallo esponenziale**  | Questi criteri attendono un intervallo casuale selezionato da un intervallo con crescita esponenziale prima di inviare la richiesta successiva. |
| **Intervallo fisso**  | Questi criteri attendono l'intervallo specificato prima di inviare la richiesta successiva. |
| **Nessuno**  | Questi criteri non ripetono la richiesta. |
|||

Per informazioni sulle restrizioni dei criteri di ripetizione, vedere [Limiti e configurazione per App per la logica](../logic-apps/logic-apps-limits-and-config.md#request-limits).

### <a name="change-retry-policy"></a>Modificare i criteri di ripetizione

Per selezionare criteri di ripetizione diversi, seguire questa procedura:

1. Aprire l'app per la logica in Logic App Designer.

1. Aprire le **impostazioni** per un'azione o un trigger.

1. Se l'azione o il trigger supporta i criteri di ripetizione, selezionare il tipo desiderato in **Criteri di ripetizione**.

In alternativa, è possibile specificare manualmente i criteri di ripetizione nella sezione `inputs` per un'azione o un trigger che supporta i criteri di ripetizione. Se non si specificano criteri di ripetizione, l'azione usa i criteri predefiniti.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Richiesto*

| valore | Type | Descrizione |
|-------|------|-------------|
| <*tipo di criteri di ripetizione*> | Stringa | Il tipo di criteri di ripetizione da usare: `default`, `none`, `fixed` o `exponential` |
| <*intervallo tra tentativi*> | Stringa | L'intervallo di ripetizione in cui il valore deve usare il [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). L'intervallo minimo predefinito è `PT5S`, l'intervallo massimo è `PT1D`. Quando si usano i criteri a intervallo esponenziale, è possibile specificare valori minimi e massimi diversi. |
| <*tentativi-tentativi*> | Integer | Numero di tentativi di ripetizione, che deve essere compresi tra 1 e 90 |
||||

*Facoltativo*

| valore | Type | Descrizione |
|-------|------|-------------|
| <*intervallo minimo*> | Stringa | Per i criteri a intervallo esponenziale, l'intervallo più piccolo per l'intervallo selezionato casualmente in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*intervallo massimo*> | Stringa | Per i criteri a intervallo esponenziale, l'intervallo più grande per l'intervallo selezionato casualmente in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Di seguito sono riportate altre informazioni sui diversi tipi di criteri.

<a name="default-retry"></a>

### <a name="default"></a>Predefinito

Se non vengono specificati i criteri di ripetizione, l'azione usa i criteri predefiniti, che in realtà sono [criteri a intervallo esponenziale](#exponential-interval) che inviano fino a quattro richieste di ripetizione a intervalli con crescita esponenziale scalati di 7,5 secondi. L'intervallo è compreso tra 5 e 45 secondi.

Anche se non è espressamente definito nell'azione o nel trigger, qui sotto viene mostrato il comportamento dei criteri predefiniti in un'azione HTTP di esempio:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Nessuno

Per specificare che l'azione o il trigger non ripete richieste con esito negativo, impostare <*retry-policy-type*> su `none`.

### <a name="fixed-interval"></a>Intervallo fisso

Per specificare che l'azione o il trigger attende l'intervallo specificato prima di inviare la richiesta successiva, impostare <*retry-policy-type*> su `fixed`.

*Esempio*

Dopo una prima richiesta con esito negativo, questi criteri di ripetizione provano altre due volte a ricevere le ultime notizie con un intervallo di 30 secondo tra ciascun tentativo:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Intervallo esponenziale

Per specificare che l'azione o il trigger attende un intervallo causale prima di inviare la richiesta successiva, impostare <*retry-policy-type*> su `exponential`. L'intervallo casuale viene selezionato da un intervallo con crescita esponenziale. Facoltativamente, è inoltre possibile sostituire gli intervalli minimo e massimo predefiniti specificando i propri intervalli minimo e massimo.

**Intervalli variabili casuali**

Questa tabella illustra come App per la logica genera una variabile casuale uniforme nell'intervallo specificato per ogni nuovo tentativo fino al numero di tentativi (incluso):

| Numero di ripetizioni | Intervallo minimo | Intervallo massimo |
|--------------|------------------|------------------|
| 1 | Max (0, <> *intervallo minimo* ) | min (intervallo, <> *intervallo massimo* ) |
| 2 | massimo (intervallo, <> *intervallo minimo* ) | min (2 * intervallo, <> *intervallo massimo* ) |
| 3 | massimo (2 * intervallo, <> *intervallo minimo* ) | min (4 * intervallo, <> *intervallo massimo* ) |
| 4 | massimo (4 * intervallo, <> *intervallo minimo* ) | min (8 * intervallo, <> *intervallo massimo* ) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Rilevare e gestire gli errori modificando il comportamento di esecuzione dopo

Quando si aggiungono azioni nella finestra di progettazione dell'app per la logica, si dichiara in modo implicito l'ordine da usare per l'esecuzione di tali azioni. Al termine dell'esecuzione di un'azione, l'azione viene contrassegnata con uno `Succeeded`stato `Failed`, `Skipped`ad esempio `TimedOut`,, o. In ogni definizione di azione, `runAfter` la proprietà specifica l'azione predecessore che deve essere completata per prima e gli stati consentiti per tale predecessore prima di poter eseguire l'azione successore. Per impostazione predefinita, un'azione aggiunta nella finestra di progettazione viene eseguita solo dopo il completamento del predecessore con `Succeeded` lo stato.

Quando un'azione genera un errore o un'eccezione non gestita, l'azione viene contrassegnata `Failed`e qualsiasi azione successiva è contrassegnata `Skipped`. Se questo comportamento si verifica per un'azione con rami paralleli, il motore delle app per la logica segue gli altri rami per determinare gli Stati di completamento. Ad esempio, se un ramo termina con un' `Skipped` azione, lo stato di completamento di tale ramo è basato sullo stato predecessore dell'azione ignorata. Al termine dell'esecuzione dell'app per la logica, il motore determina lo stato dell'intera esecuzione valutando tutti gli Stati del ramo. Se un ramo termina in errore, viene contrassegnata `Failed`l'intera esecuzione dell'app per la logica.

![Esempi che illustrano come vengono valutati gli Stati di esecuzione](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Per assicurarsi che un'azione possa ancora essere eseguita nonostante lo stato del predecessore, [personalizzare il comportamento "Esegui dopo" di un'azione](#customize-run-after) per gestire gli Stati non riusciti del predecessore.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Personalizzare il comportamento di esecuzione dopo

È possibile personalizzare il comportamento "Esegui dopo" di un'azione in modo che l'azione venga eseguita quando lo stato del `Succeeded`predecessore `Skipped`è `TimedOut`, `Failed`,, o uno qualsiasi di questi Stati. Ad esempio, per inviare un messaggio di posta elettronica dopo `Add_a_row_into_a_table` che l'azione predecessore di Excel online è stata contrassegnata `Failed`, anziché `Succeeded`modificare il comportamento "Esegui dopo", eseguire uno dei passaggi seguenti:

* Nella visualizzazione Progettazione selezionare il pulsante con i puntini di sospensione (**...**) e quindi selezionare **Configura esecuzione successiva**.

  ![Configurare il comportamento "Esegui dopo" per un'azione](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  La forma azione Mostra lo stato predefinito richiesto per l'azione predecessore, che **aggiunge una riga in una tabella** in questo esempio:

  ![Comportamento predefinito "Esegui dopo" per un'azione](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Modificare il comportamento "Esegui dopo" con lo stato desiderato, che **non è riuscito** in questo esempio:

  ![Modificare il comportamento di "esecuzione dopo" in "non riuscito"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Per specificare che l'azione viene eseguita se l'azione predecessore è `Failed`contrassegnata come `Skipped` o `TimedOut`, selezionare gli altri Stati:

  ![Modificare il comportamento di "Esegui dopo" con qualsiasi altro stato](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* Nella visualizzazione codice, nella definizione JSON dell'azione, modificare la `runAfter` proprietà che segue questa sintassi:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  Per questo esempio, modificare la `runAfter` proprietà da `Succeeded` a `Failed`:

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Per specificare che l'azione viene eseguita se l'azione predecessore è `Failed`contrassegnata come `Skipped` o `TimedOut`, aggiungere gli altri Stati:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Valutare le azioni con ambiti e i relativi risultati

Analogamente ai passaggi eseguiti dopo le singole azioni `runAfter` con la proprietà, è possibile raggruppare le azioni all'interno di un [ambito](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Quando si vuole raggruppare le azioni logicamente, valutare lo stato di aggregazione dell'ambito ed eseguire azioni in base a tale stato, è possibile usare gli ambiti. Al termine dell'esecuzione di tutte le azioni in un ambito, l'ambito stesso ottiene il proprio stato.

Per controllare lo stato di un ambito, è possibile usare gli stessi criteri usati per controllare lo stato di esecuzione di un'app per la logica `Succeeded`, `Failed`ad esempio, e così via.

Per impostazione predefinita, quando tutte le azioni dell'ambito hanno esito positivo, lo stato `Succeeded`dell'ambito è contrassegnato. Se l'azione finale in un ambito risulta come `Failed` o `Aborted`, lo stato dell'ambito è contrassegnato `Failed`come.

Per intercettare le eccezioni `Failed` in un ambito ed eseguire azioni che gestiscono tali errori, è `runAfter` possibile usare la `Failed` proprietà per tale ambito. In questo modo, se *le azioni nell'* ambito hanno esito negativo e si `runAfter` usa la proprietà per tale ambito, è possibile creare una singola azione per rilevare gli errori.

Per i limiti degli ambiti, vedere [Limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Ottenere il contesto e i risultati per gli errori

Rilevare gli errori è molto utile, ma può essere opportuno anche il contesto per comprendere esattamente quali azioni hanno avuto esito negativo e tutti gli errori o i codici di stato restituiti.

La [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) funzione fornisce il contesto sui risultati di tutte le azioni in un ambito. La `result()` funzione accetta un solo parametro, ovvero il nome dell'ambito, e restituisce una matrice che contiene tutti i risultati dell'azione all'interno di tale ambito. Questi oggetti azione includono gli stessi attributi dell' `actions()` oggetto, ad esempio l'ora di inizio, l'ora di fine, lo stato, gli input, gli ID di correlazione e gli output dell'azione. Per inviare il `runAfter` contesto per qualsiasi azione non riuscita all'interno di un ambito, è possibile `@result()` associare facilmente un'espressione alla proprietà.

Per eseguire `Failed` un'azione per ogni azione in un ambito con un risultato e per filtrare la matrice dei risultati fino alle azioni non riuscite, è possibile associare un' `@result()` espressione a un'azione di matrice di [**filtro**](logic-apps-perform-data-operations.md#filter-array-action) e a un ciclo [**for each**](../logic-apps/logic-apps-control-flow-loops.md) . È possibile utilizzare la matrice dei risultati filtrata ed eseguire un'azione per ogni errore `For_each` utilizzando il ciclo.

Di seguito è riportato un esempio, con una spiegazione dettagliata, che invia una richiesta HTTP POST con il corpo della risposta di qualsiasi azione non riuscita all'interno dell'ambito "My_Scope":

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Ecco la procedura dettagliata che descrive quanto accaduto in questo esempio:

1. Per ottenere il risultato di tutte le azioni nell'ambito "My_Scope", l'azione **Filter Array** usa questa espressione filtro: `@result('My_Scope')`

1. La condizione per la **matrice** di filtri `@result()` è qualsiasi elemento con uno stato uguale `Failed`a. Questa condizione filtra la matrice che ha tutti i risultati dell'azione da "My_Scope" fino a una matrice con i soli risultati delle azioni non riuscite.

1. Eseguire un' `For_each` azione di ciclo sugli output della *matrice filtrata* . Questo passaggio esegue un'azione per ogni risultato di azione non riuscita precedentemente filtrato.

   Se una singola azione nell'ambito ha esito negativo, le azioni `For_each` nel ciclo vengono eseguite una sola volta. Molte azioni non riuscite determinano un'azione per errore.

1. Inviare un HTTP POST sul corpo `For_each` della risposta dell'elemento, che è `@item()['outputs']['body']` l'espressione.

   La forma dell'elemento `@result()` è uguale alla forma di `@actions()` e può essere analizzata nello stesso modo.

1. Inclusione di due intestazioni personalizzate con il nome dell'azione non riuscita (`@item()['name']`) e l'ID rilevamento client dell'esecuzione non riuscita (`@item()['clientTrackingId']`).

Come riferimento, di seguito è riportato un esempio di un singolo elemento `@result()`, che mostra le proprietà `name`, `body`, e `clientTrackingId` analizzate nell'esempio precedente. Al di `For_each` fuori di `@result()` un'azione, restituisce una matrice di questi oggetti.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

Le espressioni descritte in precedenza in questo articolo possono essere usate per eseguire diversi modelli di gestione delle eccezioni. È possibile scegliere di eseguire una singola azione di gestione delle eccezioni al di fuori dell'ambito che accetta l'intera matrice filtrata di `For_each` errori e di rimuovere l'azione. È anche possibile includere altre proprietà utili della `\@result()` risposta, come descritto in precedenza.

## <a name="set-up-azure-monitor-logs"></a>Configurare i log di Monitoraggio di Azure

I modelli precedenti sono un ottimo modo per gestire gli errori e le eccezioni in un'esecuzione, ma è possibile anche identificare e rispondere agli errori indipendentemente dall'esecuzione. [Monitoraggio di Azure](../azure-monitor/overview.md) offre un modo semplice per inviare tutti gli eventi del flusso di lavoro, inclusi tutti gli Stati di esecuzione e azione, a un' [area di lavoro log Analytics](../azure-monitor/platform/data-platform-logs.md), a un [account di archiviazione di Azure](../storage/blobs/storage-blobs-overview.md)o a [Hub eventi di Azure](../event-hubs/event-hubs-about.md).

Per valutare gli stati delle esecuzioni è possibile monitorare i log e le metriche o pubblicarli nello strumento di monitoraggio preferito. Una possibile opzione consiste nel trasmettere tutti gli eventi tramite Hub eventi ad [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/). In Analisi di flusso è possibile scrivere query dinamiche in base a un'anomalia, una media o un errore dei log di diagnostica. È possibile usare Analisi di flusso per inviare informazioni ad altre origini dati, ad esempio a code, argomenti, SQL, Azure Cosmos DB o Power BI.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come un cliente ha implementato una solida gestione delle eccezioni con App per la logica di Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Trova altri esempi e scenari di app per la logica](../logic-apps/logic-apps-examples-and-scenarios.md)
