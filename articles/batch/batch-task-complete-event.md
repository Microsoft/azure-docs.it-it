---
title: Evento di completamento dell'attività Azure Batch
description: Riferimento per l'evento di completamento dell'attività batch. Questo evento viene generato al completamento di un'attività, indipendentemente dal codice di uscita.
ms.topic: article
ms.date: 04/20/2017
ms.openlocfilehash: 9e11eac04009def2bce4476ba2d77c798f25ca15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116503"
---
# <a name="task-complete-event"></a>Evento di completamento attività

 Questo evento viene generato al completamento di un'attività, indipendentemente dal codice di uscita. Può essere usato per determinare la durata di un'attività, la posizione in cui è stata eseguita e se è stata tentata più volte.


 L'esempio seguente illustra il corpo di un evento di completamento attività.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`jobId`|Stringa|ID del processo che contiene l'attività.|
|`id`|Stringa|ID dell'attività.|
|`taskType`|Stringa|Tipo dell'attività. Il valore può essere "JobManager" per indicare che si tratta di un'attività del gestore di processi oppure 'User' per indicare che non si tratta di un'attività del gestore di processi. Questo evento non viene generato per le attività di preparazione del processo, le attività di rilascio del processo o le attività di avvio.|
|`systemTaskVersion`|Int32|Contatore dei tentativi interni di esecuzione di un'attività. Il servizio Batch può ritentare internamente l'esecuzione di un'attività in funzione di problemi transitori. Questi problemi possono includere errori interni di pianificazione o tentativi di ripristino a seguito di nodi di calcolo in uno stato non valido.|
|[`nodeInfo`](#nodeInfo)|Tipo complesso|Contiene informazioni sul nodo di calcolo in cui è stata eseguita l'attività.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo complesso|Specifica che l'attività è un'attività con istanze multiple che richiede più nodi di calcolo.  Per [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) informazioni dettagliate, vedere.|
|[`constraints`](#constraints)|Tipo complesso|Vincoli di esecuzione che si applicano a questa attività.|
|[`executionInfo`](#executionInfo)|Tipo complesso|Contiene informazioni sull'esecuzione dell'attività.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`poolId`|Stringa|ID del pool in cui è stata eseguita l'attività.|
|`nodeId`|Stringa|ID del nodo in cui è stata eseguita l'attività.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a>multiInstanceSettings

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|Numero di nodi di calcolo richiesti dall'attività.|

###  <a name="constraints"></a><a name="constraints"></a>vincoli

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Numero massimo di tentativi consentiti per l'attività. Il servizio Batch ripete un'attività se il relativo codice di uscita è diverso da zero.<br /><br /> Si noti che questo valore controlla specificamente il numero di tentativi. Il servizio Batch eseguirà l'attività una volta e quindi ripeterà l'esecuzione fino al limite di tentativi specificato. Ad esempio, se il numero massimo di tentativi è 3, il servizio Batch eseguirà l'attività 4 volte, ovvero una iniziale e 3 ulteriori tentativi.<br /><br /> Se il numero massimo di tentativi è 0, il servizio Batch non eseguirà ulteriori tentativi.<br /><br /> Se il numero massimo di tentativi è -1, il servizio Batch continuerà a eseguire tentativi senza limiti.<br /><br /> Il valore predefinito è 0, ovvero nessun tentativo.|

###  <a name="executioninfo"></a><a name="executionInfo"></a>executionInfo

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`startTime`|Datetime|Data e ora in cui è iniziata l'esecuzione dell'attività. "Running" corrisponde allo stato **in esecuzione** e pertanto, se l'attività specifica file di risorse o pacchetti dell'applicazione, la data e l'ora di inizio rispecchiano quelle in cui l'attività ne ha iniziato il download o la distribuzione.  Se l'attività è stata riavviata o ritentata, la data e l'ora indicate saranno quelle di inizio dell'ultima esecuzione dell'attività.|
|`endTime`|Datetime|Data e ora in cui è stata completata l'esecuzione dell'attività.|
|`exitCode`|Int32|Codice di uscita dell'attività.|
|`retryCount`|Int32|Numero di tentativi di esecuzione dell'attività da parte del servizio Batch. L'attività viene ritentata se si conclude con un codice di uscita diverso da zero, fino al limite specificato in MaxTaskRetryCount.|
|`requeueCount`|Int32|Numero di volte in cui l'attività è stata reinserita nella coda dal servizio Batch a seguito di una richiesta dell'utente.<br /><br /> Quando si rimuovono nodi da un pool (tramite ridimensionamento o riduzione del pool) o quando il processo viene disabilitato, l'utente può specificare che le attività in esecuzione sui nodi siano reinserite nella coda per l'esecuzione. Questo conteggio tiene traccia del numero di volte in cui l'attività è stata reinserita nella coda per questi motivi.|
