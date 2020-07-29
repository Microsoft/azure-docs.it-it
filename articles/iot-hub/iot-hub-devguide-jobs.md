---
title: Informazioni sui processi hub IoT di Azure | Microsoft Docs
description: Guida per sviluppatori - Pianificazione di processi da eseguire in più dispositivi connessi all'hub di IoT. I processi possono aggiornare i tag e le proprietà desiderate e richiamare metodi diretti su più dispositivi.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81730100"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Pianificare processi in più dispositivi

L'hub IoT di Azure abilita un numero di blocchi predefiniti, ad esempio [tag e proprietà di dispositivi gemelli](iot-hub-devguide-device-twins.md) e [metodi diretti](iot-hub-devguide-direct-methods.md). In genere, le app back-end consentono agli amministratori dei dispositivi e agli operatori di aggiornare i dispositivi IoT in blocco e a un'ora pianificata e di interagirvi. I processi eseguono l'aggiornamento di dispositivi gemelli e metodi diretti rispetto a un set di dispositivi a un'ora pianificata. Ad esempio, un operatore usa un'app back-end avviata e tiene traccia di un processo per riavviare un set di dispositivi al terzo piano dell'edificio 43 in un orario opportuno per non interrompere le attività dell'edificio.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

È consigliabile usare i processi quando è necessario pianificare e tenere traccia dell'avanzamento di una qualsiasi delle attività seguenti in un set di dispositivi:

* Aggiornare le proprietà desiderate
* Aggiornare i tag
* Richiamare metodi diretti

## <a name="job-lifecycle"></a>Ciclo di vita dei processi

I processi vengono avviati dal back-end della soluzione e mantenuti dall'hub IoT. È possibile avviare un processo tramite un URI del servizio (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) ed eseguire query riguardo allo stato di avanzamento in un processo in esecuzione tramite un URI del servizio (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`). Per aggiornare lo stato dei processi in esecuzione dopo l'avvio, eseguire query sui processi.

> [!NOTE]
> Quando si avvia un processo, i valori e i nomi di proprietà possono contenere solo caratteri alfanumerici US-ASCII stampabili, ad eccezione dei seguenti: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Processi per eseguire metodi diretti

Nel frammento di codice seguente sono riportati i dettagli della richiesta HTTPS 1.1 per l'esecuzione di un [metodo diretto](iot-hub-devguide-direct-methods.md) in un set di dispositivi tramite un processo:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

La condizione di query può anche trovarsi in un ID dispositivo singolo o in un elenco di ID dispositivo, come illustrato negli esempi seguenti:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[Linguaggio di query dell'hub IoT](iot-hub-devguide-query-language.md) illustra il linguaggio di query dell'hub IoT in maggiore dettaglio.

Il frammento di codice seguente mostra la richiesta e la risposta di un processo pianificato per chiamare un metodo diretto denominato testMethod su tutti i dispositivi in Contoso-hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Processi per aggiornare le proprietà dei dispositivi gemelli

Nel frammento di codice seguente sono riportati i dettagli della richiesta HTTPS 1.1 per l'aggiornamento delle proprietà dei dispositivi gemelli tramite un processo:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> Per la proprietà *updateTwin* è richiesta una corrispondenza ETag valida. ad esempio, `etag="*"` .

Il frammento di codice seguente mostra la richiesta e la risposta di un processo pianificato per aggiornare le proprietà del dispositivo gemello per test-device in Contoso-hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>Esecuzione di query sull'avanzamento dei processi

Nel frammento di codice seguente sono riportati i dettagli della richiesta HTTPS 1.1 per l'esecuzione di query sui processi:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

La risposta fornisce continuationToken.

Usando il [linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi](iot-hub-devguide-query-language.md), è possibile eseguire query sullo stato di esecuzione dei processi su ogni dispositivo.

## <a name="jobs-properties"></a>Proprietà dei processi

Di seguito è riportato un elenco di proprietà e corrispondenti descrizioni che è possibile usare quando si eseguono query sui processi o sui relativi risultati.

| Proprietà | Descrizione |
| --- | --- |
| **jobId** |ID fornito dall'applicazione per il processo. |
| **startTime** |Ora di inizio fornita dall'applicazione (ISO 8601) per il processo. |
| **endTime** |Data fornita dall'hub IoT (ISO 8601) per il completamento del processo. È valida solo quando il processo raggiunge lo stato di completamento. |
| **type** |Tipi di processi: |
| | **scheduleUpdateTwin**: un processo usato per aggiornare un set di proprietà o tag desiderati. |
| | **scheduleDeviceMethod**: un processo usato per richiamare un metodo del dispositivo su un set di dispositivi gemelli. |
| **Stato** |Stato corrente del processo. Valori possibili per lo stato: |
| | **pending**: processo pianificato e in attesa del prelievo da parte del servizio del processo. |
| | **scheduled**: processo pianificato per un'ora futura. |
| | **running**: processo attualmente attivo. |
| | **canceled**: il processo è stato annullato. |
| | **failed**: processo non riuscito. |
| | **completed**: processo completato. |
| **deviceJobStatistics** |Statistiche sull'esecuzione del processo. |
| | Proprietà di **deviceJobStatistics** : |
| | **deviceJobStatistics.deviceCount**: numero di dispositivi nel processo. |
| | **deviceJobStatistics.deviceCount**: numero di dispositivi in cui il processo non è riuscito. |
| | **deviceJobStatistics.succeededCount**: numero di dispositivi in cui il processo è riuscito. |
| | **deviceJobStatistics.runningCount**: numero di dispositivi in cui il processo è in esecuzione. |
| | **deviceJobStatistics.pendingCount**: numero di dispositivi in cui il processo è in sospeso. |

### <a name="additional-reference-material"></a>Materiale di riferimento

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT](iot-hub-devguide-endpoints.md) illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.

* [Quote e limitazioni](iot-hub-devguide-quotas-throttling.md) descrive le quote applicabili al servizio Hub IoT e il comportamento di limitazione previsto quando si usa il servizio.

* [Azure IoT SDK per dispositivi e servizi](iot-hub-devguide-sdks.md) elenca gli SDK nei diversi linguaggi che è possibile usare quando si sviluppano app per dispositivi e servizi che interagiscono con l'hub IoT.

* [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi](iot-hub-devguide-query-language.md) descrive il linguaggio di query di hub IoT. Usare questo linguaggio di query per recuperare dall'hub IoT informazioni sui dispositivi gemelli e sui processi.

* [Supporto di MQTT nell'hub IoT](iot-hub-mqtt-support.md) offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

Per provare alcuni dei concetti descritti in questo articolo, vedere l'esercitazione sull'hub IoT seguente:

* [Pianificare e trasmettere processi](iot-hub-node-node-schedule-jobs.md)