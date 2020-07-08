---
title: includere il file
description: includere file
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80117158"
---
Impostazioni di configurazione per [Funzioni permanenti](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
  }
}

```

I nomi degli hub attività devono iniziare con una lettera e contenere solo lettere e numeri. Se non specificato, il nome dell'hub attività predefinito per un'app per le funzioni è **DurableFunctionsHub**. Per altre informazioni, vedere [Hub attività](../articles/azure-functions/durable-functions-task-hubs.md).

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|hubName|DurableFunctionsHub|I nomi alternativi dell'[hub attività](../articles/azure-functions/durable-functions-task-hubs.md) possono essere usati per separare le applicazioni di Durable Functions, anche se usano lo stesso back-end di archiviazione.|
|controlQueueBatchSize|32|Numero di messaggi di cui eseguire il pull dalla coda di controllo contemporaneamente.|
|controlQueueBufferThreshold|256|Il numero di messaggi della coda di controllo che possono essere memorizzati nel buffer in memoria alla volta, a quel punto il dispatcher resterà in attesa prima di rimuovere eventuali altri messaggi.|
|partitionCount |4|Numero di partizioni per la coda di controllo. Può essere un numero intero positivo compreso tra 1 e 16.|
|controlQueueVisibilityTimeout |5 minuti|Timeout di visibilità dei messaggi rimossi dalla coda di controllo.|
|workItemQueueVisibilityTimeout |5 minuti|Timeout di visibilità dei messaggi rimossi dalla coda degli elementi di lavoro.|
|maxConcurrentActivityFunctions |10 volte il numero di processori sul computer corrente|Numero massimo di funzioni di attività che possono essere elaborate contemporaneamente in una singola istanza host.|
|maxConcurrentOrchestratorFunctions |10 volte il numero di processori sul computer corrente|Numero massimo di funzioni dell'agente di orchestrazione che possono essere elaborate contemporaneamente in una singola istanza host.|
|maxQueuePollingInterval|30 secondi|Il controllo massimo e l'intervallo di polling della coda di elementi di lavoro nel formato *hh: mm: SS* . I valori superiori possono comportare latenze di elaborazione dei messaggi più elevate. I valori inferiori possono comportare costi di archiviazione più elevati a causa di un aumento delle transazioni di archiviazione.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Nome dell'impostazione dell'app che include la stringa di connessione di Archiviazione di Azure usata per gestire le risorse di Archiviazione di Azure sottostanti.|
|trackingStoreConnectionStringName||Nome di una stringa di connessione da utilizzare per le tabelle cronologia e istanze. Se non è specificato, `azureStorageConnectionStringName` viene utilizzata la connessione.|
|trackingStoreNamePrefix||Prefisso da utilizzare per la cronologia e le tabelle di istanze quando `trackingStoreConnectionStringName` si specifica. Se non è impostato, il valore predefinito del prefisso sarà `DurableTask` . Se `trackingStoreConnectionStringName` non si specifica, le tabelle cronologia e istanze utilizzeranno il `hubName` valore come prefisso e tutte le impostazioni per `trackingStoreNamePrefix` verranno ignorate.|
|traceInputsAndOutputs |false|Valore che indica se tenere traccia degli input e degli output di chiamate di funzione. Quando si tiene traccia degli eventi di esecuzione delle funzioni, il comportamento predefinito prevede di includere il numero di byte degli input e output serializzati per le chiamate di funzione. Questo comportamento fornisce informazioni minime sull'aspetto degli input e degli output senza gonfiare i log o esporre inavvertitamente informazioni riservate. Se questa proprietà viene impostata su true, per impostazione predefinita viene registrato l'intero contenuto degli input e output della funzione.|
|logReplayEvents|false|Un valore che indica se scrivere eventi di riproduzione di orchestrazione in Application Insights.|
|eventGridTopicEndpoint ||URL di un endpoint di un argomento personalizzato di Griglia di eventi di Azure. Quando questa proprietà è impostata, gli eventi di notifica del ciclo di vita dell'orchestrazione vengono pubblicati in questo endpoint. Questa proprietà supporta la risoluzione delle impostazioni dell'app.|
|eventGridKeySettingName ||Nome dell'impostazione dell'app che contiene la chiave usata per l'autenticazione con l'argomento personalizzato di Griglia di eventi di Azure in `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Il numero di tentativi se la pubblicazione nell'argomento di Griglia di eventi ha esito negativo.|
|eventGridPublishRetryInterval|5 minuti|Intervallo fra i tentativi di pubblicazione in Griglia di eventi nel formato *hh:mm:ss*.|
|eventGridPublishEventTypes||Elenco di tipi di evento da pubblicare in griglia di eventi. Se non specificato, verranno pubblicati tutti i tipi di evento. I valori consentiti sono `Started` ,, `Completed` `Failed` , `Terminated` .|
|useGracefulShutdown|false|Anteprima Abilitare la chiusura normale per ridurre la possibilità di arresti dell'host che non riescono a eseguire le esecuzioni di funzioni in-process.|

Molte di queste impostazioni sono per l'ottimizzazione delle prestazioni. Per altre informazioni, vedere [Prestazioni e scalabilità](../articles/azure-functions/durable-functions-perf-and-scale.md).
