---
title: Hub Azure Internet come origine griglia di eventi
description: Questo articolo illustra le proprietà e lo schema per gli eventi dell'hub IoT di Azure. Vengono elencati i tipi di evento disponibili, un evento di esempio e le proprietà dell'evento.
services: iot-hub
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: f9bf807884ab5592fa320532f3ca10a223081263
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393319"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Hub Azure Internet come origine di griglia di eventi
Questo articolo illustra le proprietà e lo schema per gli eventi dell'hub IoT di Azure. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). 

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="available-event-types"></a>Tipi di evento disponibili

L'hub IoT genera i tipi di eventi seguenti:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Pubblicato quando un dispositivo viene registrato in un hub IoT. |
| Microsoft.Devices.DeviceDeleted | Pubblicato quando un dispositivo viene eliminato da un hub IoT. | 
| Microsoft.Devices.DeviceConnected | Pubblicato quando un dispositivo è connesso a un hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Pubblicato quando un dispositivo è disconnesso da un hub IoT. | 
| Microsoft. Devices. DeviceTelemetry | Pubblicato quando un messaggio di telemetria viene inviato a un hub Internet. |

Tutti gli eventi dispositivo tranne gli eventi di telemetria del dispositivo sono disponibili a livello generale in tutte le aree supportate da griglia di eventi. L'evento di telemetria del dispositivo è in anteprima pubblica ed è disponibile in tutte le aree, ad eccezione degli Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale, [Azure per enti pubblici](../azure-government/documentation-government-welcome.md), [Azure Cina 21ViaNet](/azure/china/china-welcome)e [Azure Germania](https://azure.microsoft.com/global-infrastructure/germany/).

### <a name="example-event"></a>Evento di esempio

Lo schema per gli eventi DeviceConnected e DeviceDisconnected ha la stessa struttura. Questo evento di esempio illustra lo schema di un evento generato quando un dispositivo viene connesso a un hub IoT:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

L'evento DeviceTelemetry viene generato quando un evento di telemetria viene inviato a un hub Internet. Di seguito è riportato uno schema di esempio per questo evento.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Lo schema per gli eventi DeviceCreated e DeviceDeleted ha la stessa struttura. Questo evento di esempio illustra lo schema di un evento generato quando un dispositivo viene registrato in un hub IoT:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Proprietà degli eventi

Tutti gli eventi contengono gli stessi dati di livello principale: 

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| id | stringa | Identificatore univoco dell'evento. |
| argomento | stringa | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | stringa | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | stringa | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | stringa | Ora di generazione dell'evento in base all'ora UTC del provider. |
| data | oggetto | Dati dell'evento dell'hub IoT.  |
| dataVersion | stringa | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | stringa | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Per tutti gli eventi dell'hub IoT, l'oggetto dati contiene le proprietà seguenti:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| hubName | stringa | Nome dell'hub IoT in cui il dispositivo è stato creato o eliminato. |
| deviceId | stringa | Identificatore univoco del dispositivo. Questa stringa con distinzione tra maiuscole e minuscole può avere una lunghezza di massimo 128 caratteri e supporta i caratteri alfanumerici ASCII a 7 bit e i caratteri speciali seguenti: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Il contenuto dell'oggetto dati è diverso per ogni autore di eventi. 

Per gli eventi **Device Connected** e **Device Disconnected** dell'hub IoT, l'oggetto dati contiene le proprietà seguenti:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| moduleId | stringa | Identificatore univoco del modulo. Questo è un campo di output solo per i dispositivi di modulo. Questa stringa con distinzione tra maiuscole e minuscole può avere una lunghezza di massimo 128 caratteri e supporta i caratteri alfanumerici ASCII a 7 bit e i caratteri speciali seguenti: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | oggetto | Informazioni sugli eventi dello stato di connessione del dispositivo
| sequenceNumber | stringa | Numero che consente di indicare l'ordine degli eventi correlati a dispositivi connessi o disconnessi. All'evento più recente è associato un numero di sequenza maggiore di quello dell'evento precedente. Questo numero potrebbe cambiare in incrementi maggiori di 1, ma è strettamente in aumento. Vedere [come usare il numero di sequenza](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Per l'evento dell'hub Internet per la **telemetria del dispositivo** , l'oggetto dati contiene il messaggio da dispositivo a cloud nel [formato dei messaggi dell'hub](../iot-hub/iot-hub-devguide-messages-construct.md) Internet e presenta le proprietà seguenti:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| Corpo | stringa | Contenuto del messaggio dal dispositivo. |
| properties | stringa | Le proprietà dell'applicazione sono stringhe definite dall'utente che è possibile aggiungere al messaggio. Questi campi sono facoltativi. |
| Proprietà di sistema | stringa | Le [proprietà di sistema](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) consentono di identificare il contenuto e l'origine dei messaggi. Il messaggio di telemetria del dispositivo deve essere in un formato JSON valido con contentType impostato su JSON e contentEncoding impostato su UTF-8 nelle proprietà del sistema del messaggio. Se questo non è impostato, l'hub Internet scriverà i messaggi nel formato con codifica base 64.  |

Per gli eventi **Device Created** e **Device Deleted** dell'hub IoT, l'oggetto dati contiene le proprietà seguenti:

| Proprietà | Type | Description |
| -------- | ---- | ----------- |
| twin | oggetto | Informazioni sul dispositivo gemello, ovvero la rappresentazione cloud dei metadati del dispositivo dell'applicazione. | 
| deviceID | stringa | Identificatore univoco del dispositivo gemello. | 
| etag | stringa | Validator per garantire la coerenza degli aggiornamenti per un dispositivo gemello. Ogni etag è sicuramente univoco per ogni dispositivo gemello. |  
| deviceEtag| stringa | Validator per garantire la coerenza degli aggiornamenti a un registro dei dispositivi. Ogni deviceEtag è univoco per ogni registro dei dispositivi. |
| status | stringa | Indica se il dispositivo gemello è abilitato o disabilitato. | 
| statusUpdateTime | stringa | Timestamp ISO8601 dell'ultimo aggiornamento di stato del dispositivo gemello. |
| connectionState | stringa | Indica se il dispositivo è connesso o disconnesso. | 
| lastActivityTime | stringa | Timestamp ISO8601 dell'ultima attività. | 
| cloudToDeviceMessageCount | integer | Conteggio dei messaggi da cloud a dispositivo inviati al dispositivo. | 
| authenticationType | stringa | Tipo di autenticazione usato per questo dispositivo: `SAS`, `SelfSigned` o `CertificateAuthority`. |
| x509Thumbprint | stringa | L'identificazione personale è un valore univoco per il certificato x509, usato in genere per trovare un certificato specifico in un archivio certificati. L'identificazione personale viene generata in modo dinamico mediante l'algoritmo SHA1 e non esiste fisicamente nel certificato. | 
| primaryThumbprint | stringa | Identificazione personale primaria per il certificato x509. |
| secondaryThumbprint | stringa | Identificazione personale secondaria per il certificato x509. | 
| version | integer | Valore intero che viene incrementato di un'unità a ogni aggiornamento del dispositivo gemello. |
| desired | oggetto | Parte delle proprietà che può essere scritta solo dal back-end dell'applicazione e letta dal dispositivo. | 
| reported | oggetto | Parte delle proprietà che può essere scritta solo dal dispositivo e letta dal back-end dell'applicazione. |
| lastUpdated | stringa | Timestamp ISO8601 dell'ultimo aggiornamento delle proprietà del dispositivo gemello. | 

## <a name="tutorials-and-how-tos"></a>Esercitazioni e procedure
|Titolo  |Descrizione  |
|---------|---------|
| [Inviare notifiche di posta elettronica sugli eventi dell'hub IoT di Azure usando App per la logica](publish-iot-hub-events-to-logic-apps.md) | Un'app per la logica invia una notifica tramite posta elettronica ogni volta che un dispositivo viene aggiunto all'hub IoT. |
| [Rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare le azioni](../iot-hub/iot-hub-event-grid.md) | Panoramica dell'integrazione dell'hub IoT con Griglia di eventi. |
| [Ordinare eventi correlati a dispositivi connessi e disconnessi](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Illustra come ordinare gli eventi dello stato di connessione del dispositivo. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per informazioni sulle interazioni tra l'hub IoT e Griglia di eventi, vedere [React to IoT Hub events by using Event Grid to trigger actions](../iot-hub/iot-hub-event-grid.md) (Rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare azioni).