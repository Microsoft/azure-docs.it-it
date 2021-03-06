---
title: Esportare i dati da Azure IoT Central | Microsoft Docs
description: Come usare la nuova esportazione dei dati per esportare i dati di Internet delle cose in Azure e nelle destinazioni cloud personalizzate.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/24/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: 7d57f24f8cb4b59ce9b9cd5853be11fb2d104d75
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277896"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>Esportare i dati delle cose nelle destinazioni cloud usando l'esportazione dei dati

> [!Note]
> Questo articolo descrive le funzionalità di esportazione dei dati in IoT Central. Per informazioni sulle funzionalità di esportazione dei dati legacy, vedere [esportare i dati dell'area dati nelle destinazioni cloud usando l'esportazione dei dati (legacy)](./howto-export-data-legacy.md).

Questo articolo descrive come usare la nuova funzionalità di esportazione dei dati in Azure IoT Central. Usare questa funzionalità per esportare in modo continuativo i dati delle cose filtrate e arricchiti dall'applicazione IoT Central. L'esportazione dei dati consente di effettuare il push delle modifiche quasi in tempo reale ad altre parti della soluzione cloud per informazioni dettagliate sul percorso a caldo, analisi e archiviazione.

Ad esempio, è possibile:

- Esporta continuamente dati di telemetria, modifiche delle proprietà, ciclo di vita del dispositivo e dati del ciclo di vita del modello di dispositivo in formato JSON quasi in tempo reale.
- Filtrare i flussi di dati per esportare i dati che corrispondono alle condizioni personalizzate.
- Arricchire i flussi di dati con valori personalizzati e valori di proprietà dal dispositivo.
- Inviare i dati a destinazioni quali hub eventi di Azure, il bus di servizio di Azure, l'archiviazione BLOB di Azure e gli endpoint del webhook.

> [!Tip]
> Quando si attiva l'esportazione dei dati, si ottengono solo i dati da quel momento in poi. Attualmente non è possibile recuperare i dati per un periodo di tempo in cui l'esportazione dei dati è disattivata. Per conservare dati più cronologici, attivare prima l'esportazione dei dati.

## <a name="prerequisites"></a>Prerequisiti

Per utilizzare le funzionalità di esportazione dei dati, è necessario disporre di un' [applicazione v3](howto-get-app-info.md)ed è necessario disporre dell'autorizzazione [esportazione dati](howto-manage-users-roles.md) .

Se si dispone di un'applicazione V2, vedere [eseguire la migrazione dell'applicazione IoT Central V2 alla versione V3](howto-migrate.md).

## <a name="set-up-export-destination"></a>Configurare la destinazione di esportazione

La destinazione di esportazione deve esistere prima di configurare l'esportazione dei dati. Sono attualmente disponibili i seguenti tipi di destinazione:

- Hub eventi di Azure
- Coda del bus di servizio di Azure
- Argomento del bus di servizio di Azure
- Archiviazione BLOB di Azure
- webhook

### <a name="create-an-event-hubs-destination"></a>Creare una destinazione di hub eventi

Se non si dispone di uno spazio dei nomi di hub eventi esistente da esportare in, attenersi alla seguente procedura:

1. Creare [un nuovo spazio dei nomi di Hub eventi nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](../../event-hubs/event-hubs-create.md).

1. Creare un hub eventi nello spazio dei nomi di Hub eventi. Passare allo spazio dei nomi e selezionare **+ Hub eventi** in alto per creare un'istanza di hub eventi.

1. Generare una chiave da usare quando si configura l'esportazione dei dati in IoT Central:

    - Selezionare l'istanza di hub eventi creata.
    - Selezionare **impostazioni > criteri di accesso condiviso**.
    - Creare una nuova chiave o scegliere una chiave esistente con autorizzazioni di **invio** .
    - Copiare la stringa di connessione primaria o secondaria. Questa stringa di connessione viene utilizzata per configurare una nuova destinazione in IoT Central.
    - In alternativa, è possibile generare una stringa di connessione per l'intero spazio dei nomi di hub eventi:
        1. Passare allo spazio dei nomi di hub eventi nel portale di Azure.
        2. In **Impostazioni** selezionare **criteri di accesso condiviso**
        3. Creare una nuova chiave o scegliere una chiave esistente con autorizzazioni di **invio** .
        4. Copiare la stringa di connessione primaria o secondaria.
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Creare una destinazione di coda o argomento del bus di servizio

Se non si dispone di uno spazio dei nomi del bus di servizio esistente in cui eseguire l'esportazione, attenersi alla procedura seguente:

1. Creare un [nuovo spazio dei nomi del bus di servizio nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Per altre informazioni, vedere la [documentazione del bus di servizio di Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Per creare una coda o un argomento in cui eseguire l'esportazione, passare allo spazio dei nomi del bus di servizio e selezionare **+ coda** o **+ argomento**.

1. Generare una chiave da usare quando si configura l'esportazione dei dati in IoT Central:

    - Selezionare la coda o l'argomento creato.
    - Selezionare **Impostazioni/criteri di accesso condiviso**.
    - Creare una nuova chiave o scegliere una chiave esistente con autorizzazioni di **invio** .
    - Copiare la stringa di connessione primaria o secondaria. Questa stringa di connessione viene utilizzata per configurare una nuova destinazione in IoT Central.
    - In alternativa, è possibile generare una stringa di connessione per l'intero spazio dei nomi del bus di servizio:
        1. Passare allo spazio dei nomi del bus di servizio nel portale di Azure.
        2. In **Impostazioni** selezionare **criteri di accesso condiviso**
        3. Creare una nuova chiave o scegliere una chiave esistente con autorizzazioni di **invio** .
        4. Copiare la stringa di connessione primaria o secondaria.

### <a name="create-an-azure-blob-storage-destination"></a>Creare una destinazione di archiviazione BLOB di Azure

Se non si ha un account di archiviazione di Azure esistente da esportare, seguire questa procedura:

1. Creare un [nuovo account di archiviazione nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). È possibile ottenere altre informazioni sulla creazione di nuovi [account di archiviazione BLOB di Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) o [Azure Data Lake storage V2](../../storage/common/storage-account-create.md). L'esportazione dei dati può scrivere solo i dati negli account di archiviazione che supportano i BLOB in blocchi. L'elenco seguente illustra i tipi di account di archiviazione compatibili noti:

    |Livello di prestazioni|Tipo di account|
    |-|-|
    |Standard|per utilizzo generico V2|
    |Standard|per utilizzo generico V1|
    |Standard|Archiviazione BLOB|
    |Premium|Archiviazione BLOB in blocchi|

1. Per creare un contenitore nell'account di archiviazione, passare all'account di archiviazione. In **Servizio BLOB** selezionare **Esplora BLOB**. Selezionare **+ Contenitore** in alto per creare un nuovo contenitore.

1. Per generare una stringa di connessione per l'account di archiviazione, passare a **impostazioni > chiavi di accesso**. Copiare una delle due stringhe di connessione.

### <a name="create-a-webhook-endpoint"></a>Creare un endpoint del webhook

È possibile esportare i dati in un endpoint di Webhook HTTP disponibile pubblicamente. È possibile creare un endpoint del webhook di test usando [RequestBin](https://requestbin.net/). RequestBin limita la richiesta quando viene raggiunto il limite di richieste:

1. Aprire [RequestBin](https://requestbin.net/).
2. Creare un nuovo RequestBin e copiare l'**URL del contenitore**. Questo URL viene usato quando si esegue il test dell'esportazione dei dati.

## <a name="set-up-data-export"></a>Configurare l'esportazione dei dati

Ora che si dispone di una destinazione in cui esportare i dati, configurare l'esportazione dei dati nell'applicazione IoT Central:

1. Accedere all'applicazione IoT Central.

1. Nel riquadro sinistro selezionare **esportazione dati**.

    > [!Tip]
    > Se non viene visualizzata l' **esportazione dei dati** nel riquadro sinistro, non si dispone delle autorizzazioni per configurare l'esportazione dei dati nell'app. Chiedere a un amministratore di configurare l'esportazione dei dati.

1. Selezionare **+ nuova esportazione**.

1. Immettere un nome visualizzato per la nuova esportazione e verificare che l'esportazione dei dati sia **abilitata**.

1. Scegliere il tipo di dati da esportare. Nella tabella seguente sono elencati i tipi di esportazione dei dati supportati:

    | Tipo di dati | Descrizione | Formato dati |
    | :------------- | :---------- | :----------- |
    |  Telemetria | Esporta messaggi di telemetria dai dispositivi quasi in tempo reale. Ogni messaggio esportato contiene il contenuto completo del messaggio originale del dispositivo, normalizzato.   |  [Formato del messaggio di telemetria](#telemetry-format)   |
    | Modifiche delle proprietà | Consente di esportare le modifiche apportate alle proprietà del dispositivo e del cloud in tempo quasi reale. Per le proprietà del dispositivo di sola lettura, le modifiche ai valori segnalati vengono esportate. Per le proprietà di lettura e scrittura, vengono esportati sia i valori segnalati che quelli desiderati. | [Formato del messaggio di modifica delle proprietà](#property-changes-format) |
    | Ciclo di vita del dispositivo | Esportare gli eventi registrati ed eliminati del dispositivo. | [Formato del messaggio modifiche del ciclo di vita del dispositivo](#device-lifecycle-changes-format) |
    | Ciclo di vita del modello di dispositivo | Esportare le modifiche del modello di dispositivo pubblicate, incluse quelle create, aggiornate ed eliminate. | [Formato del messaggio modifiche del ciclo di vita del modello di dispositivo](#device-template-lifecycle-changes-format) | 

1. Facoltativamente, aggiungere filtri per ridurre la quantità di dati esportati. Sono disponibili diversi tipi di filtro per ogni tipo di esportazione dei dati: <a name="DataExportFilters"></a>
    
    | Tipo di dati | Filtri disponibili| 
    |--------------|------------------|
    |Telemetria|<ul><li>Filtrare in base al nome del dispositivo, all'ID del dispositivo e al modello di dispositivo</li><li>Filtrare il flusso per contenere solo i dati di telemetria che soddisfano le condizioni di filtro</li><li>Filtrare il flusso per contenere solo i dati di telemetria dai dispositivi con proprietà corrispondenti alle condizioni di filtro</li><li>Filtrare il flusso per contenere solo i dati di telemetria con *proprietà del messaggio* che soddisfano la condizione di filtro. Le *proprietà del messaggio* , note anche come *proprietà dell'applicazione*, vengono inviate in un contenitore di coppie chiave-valore in ogni messaggio di telemetria inviato facoltativamente dai dispositivi che usano gli SDK per dispositivi. Per creare un filtro di proprietà del messaggio, immettere la chiave della proprietà del messaggio che si sta cercando e specificare una condizione. Vengono esportati solo i messaggi di telemetria con proprietà che corrispondono alla condizione di filtro specificata. [Altre informazioni sulle proprietà dell'applicazione da docs Hub](../../iot-hub/iot-hub-devguide-messages-construct.md) </li></ul>|
    |Modifiche delle proprietà|<ul><li>Filtrare in base al nome del dispositivo, all'ID del dispositivo e al modello di dispositivo</li><li>Filtrare il flusso per contenere solo le modifiche alle proprietà che soddisfano le condizioni di filtro</li></ul>|
    |Ciclo di vita del dispositivo|<ul><li>Filtrare in base al nome del dispositivo, all'ID del dispositivo e al modello di dispositivo</li><li>Filtrare il flusso per contenere solo le modifiche dai dispositivi con proprietà corrispondenti alle condizioni di filtro</li></ul>|
    |Ciclo di vita del modello di dispositivo|<ul><li>Filtra per modello di dispositivo</li></ul>|
    
1. Facoltativamente, arricchire i messaggi esportati con metadati aggiuntivi della coppia chiave-valore. Per i tipi di esportazione dei dati di telemetria e delle modifiche delle proprietà sono disponibili le seguenti funzionalità: <a name="DataExportEnrichmnents"></a>
    - **Stringa personalizzata**: aggiunge una stringa statica personalizzata a ogni messaggio. Immettere una chiave qualsiasi e immettere qualsiasi valore stringa.
    - **Property**: aggiunge la proprietà del dispositivo corrente segnalata o il valore della proprietà cloud a ogni messaggio. Immettere una chiave qualsiasi e scegliere una proprietà del dispositivo o del cloud. Se il messaggio esportato è da un dispositivo che non dispone della proprietà specificata, il messaggio esportato non ottiene l'arricchimento.

1. Aggiungere una nuova destinazione o aggiungere una destinazione già creata. Selezionare il collegamento **crea uno nuovo** e aggiungere le informazioni seguenti:

    - **Nome destinazione**: il nome visualizzato della destinazione in IOT Central.
    - **Tipo destinazione**: scegliere il tipo di destinazione. Se la destinazione non è già stata configurata, vedere [configurare la destinazione di esportazione](#set-up-export-destination).
    - Per hub eventi di Azure, coda o argomento del bus di servizio di Azure, incollare la stringa di connessione per la risorsa e immettere il nome dell'hub eventi, della coda o dell'argomento con distinzione tra maiuscole e minuscole, se necessario.
    - Per archiviazione BLOB di Azure, incollare la stringa di connessione per la risorsa e immettere il nome del contenitore con distinzione tra maiuscole e minuscole, se necessario.
    - Per webhook incollare l'URL di callback per l'endpoint del webhook. Facoltativamente, è possibile configurare l'autorizzazione webhook (OAuth 2,0 e token di autorizzazione) e aggiungere intestazioni personalizzate. 
        - Per OAuth 2,0, è supportato solo il flusso di credenziali client. Quando la destinazione viene salvata, IoT Central comunicherà con il provider OAuth per recuperare un token di autorizzazione. Questo token verrà allegato all'intestazione "Authorization" per ogni messaggio inviato a questa destinazione.
        - Per il token di autorizzazione, è possibile specificare un valore di token che verrà collegato direttamente all'intestazione "Authorization" per ogni messaggio inviato a questa destinazione.
    - Selezionare **Crea**.

1. Selezionare **+ destinazione** e scegliere una destinazione dall'elenco a discesa. È possibile aggiungere fino a cinque destinazioni a un'unica esportazione.

1. Al termine della configurazione dell'esportazione, selezionare **Salva**. Dopo alcuni minuti, i dati verranno visualizzati nelle destinazioni.

## <a name="monitor-your-export"></a>Monitorare l'esportazione

Oltre a visualizzare lo stato delle esportazioni in IoT Central, è possibile usare [monitoraggio di Azure](../../azure-monitor/overview.md) per visualizzare la quantità di dati che si sta esportando e gli eventuali errori di esportazione. È possibile accedere alle metriche di integrità dei dispositivi e delle esportazioni nei grafici nel portale di Azure, con un'API REST o con query in PowerShell o nell'interfaccia della riga di comando di Azure. Attualmente, è possibile monitorare le metriche di esportazione dei dati seguenti in monitoraggio di Azure:

- Numero di messaggi in ingresso da esportare prima che vengano applicati i filtri.
- Numero di messaggi che passano attraverso i filtri.
- Numero di messaggi esportati correttamente nelle destinazioni.
- Numero di errori.

Per altre informazioni, vedere [monitorare l'integrità complessiva di un'applicazione IoT Central](howto-monitor-application-health.md).

## <a name="destinations"></a>Destinazioni

### <a name="azure-blob-storage-destination"></a>Destinazione di archiviazione BLOB di Azure

I dati vengono esportati una volta al minuto, con ogni file contenente il batch di modifiche rispetto all'esportazione precedente. I dati esportati vengono salvati in formato JSON. I percorsi predefiniti per i dati esportati nell'account di archiviazione sono:

- Telemetria: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Modifiche alle proprietà: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Per esplorare i file esportati nella portale di Azure, passare al file e selezionare **modifica BLOB**.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Hub eventi di Azure e destinazioni del bus di servizio di Azure

I dati vengono esportati quasi in tempo reale. I dati sono nel corpo del messaggio ed è in formato JSON codificato come UTF-8.

Il contenitore delle annotazioni o delle proprietà di sistema del messaggio contiene i `iotcentral-device-id` `iotcentral-application-id` campi,, `iotcentral-message-source` e `iotcentral-message-type` che presentano gli stessi valori dei campi corrispondenti nel corpo del messaggio.

### <a name="webhook-destination"></a>Destinazione webhook

Per le destinazioni webhook, i dati vengono anche esportati quasi in tempo reale. I dati nel corpo del messaggio hanno lo stesso formato degli hub eventi e del bus di servizio.

## <a name="telemetry-format"></a>Formato di telemetria

Ogni messaggio esportato contiene una forma normalizzata del messaggio completo del dispositivo inviato nel corpo del messaggio. Il messaggio è in formato JSON e codificato come UTF-8. Le informazioni contenute in ogni messaggio includono:

- `applicationId`: ID dell'applicazione IoT Central.
- `messageSource`: Origine del messaggio `telemetry` .
- `deviceId`: ID del dispositivo che ha inviato il messaggio di telemetria.
- `schema`: Il nome e la versione dello schema del payload.
- `templateId`: ID del modello di dispositivo associato al dispositivo.
- `enqueuedTime`: Ora in cui il messaggio è stato ricevuto da IoT Central.
- `enrichments`: Eventuali arricchimenti impostati nell'esportazione.
- `messageProperties`: Proprietà aggiuntive inviate dal dispositivo al messaggio. Queste proprietà sono talvolta denominate *proprietà dell'applicazione*. [Altre informazioni sono disponibili nella documentazione dell'hub](../../iot-hub/iot-hub-devguide-messages-construct.md)Internet.

Per gli hub eventi e il bus di servizio, IoT Central Esporta rapidamente un nuovo messaggio dopo la ricezione del messaggio da un dispositivo. Nelle proprietà utente (definite anche proprietà dell'applicazione) di ogni messaggio,, `iotcentral-device-id` `iotcentral-application-id` e `iotcentral-message-source` vengono inclusi automaticamente.

Per l'archiviazione BLOB, i messaggi vengono inviati in batch ed esportati una volta al minuto.

L'esempio seguente mostra un messaggio di telemetria esportato:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
### <a name="message-properties"></a>Proprietà del messaggio

I messaggi di telemetria hanno proprietà per i metadati oltre al payload di telemetria. Il frammento di codice precedente mostra esempi di messaggi di sistema, ad esempio `deviceId` e `enqueuedTime` . Per altre informazioni sulle proprietà dei messaggi di sistema, vedere [proprietà di sistema dei messaggi dell'hub D2C](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages).

È possibile aggiungere proprietà ai messaggi di telemetria se è necessario aggiungere metadati personalizzati ai messaggi di telemetria. Ad esempio, è necessario aggiungere un timestamp quando il dispositivo crea il messaggio.

Il frammento di codice seguente mostra come aggiungere la `iothub-creation-time-utc` proprietà al messaggio quando lo si crea nel dispositivo:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

Il frammento di codice seguente Mostra questa proprietà nel messaggio esportato nell'archivio BLOB:

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>Formato modifiche proprietà

Ogni messaggio o record rappresenta una modifica a una proprietà del dispositivo o del cloud. Per le proprietà del dispositivo, solo le modifiche apportate al valore restituito vengono esportate come un messaggio separato. Le informazioni nel messaggio esportato includono:

- `applicationId`: ID dell'applicazione IoT Central.
- `messageSource`: Origine del messaggio `properties` .
- `messageType`: `cloudPropertyChange` , `devicePropertyDesiredChange` O `devicePropertyReportedChange` .
- `deviceId`: ID del dispositivo che ha inviato il messaggio di telemetria.
- `schema`: Il nome e la versione dello schema del payload.
- `enqueuedTime`: Ora in cui è stata rilevata la modifica da IoT Central.
- `templateId`: ID del modello di dispositivo associato al dispositivo.
- `enrichments`: Eventuali arricchimenti impostati nell'esportazione.

Per gli hub eventi e il bus di servizio, IoT Central Esporta i nuovi dati dei messaggi nell'hub eventi o nella coda o nell'argomento del bus di servizio quasi in tempo reale. Nelle proprietà utente (definite anche proprietà dell'applicazione) di ogni messaggio,,, `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` e `iotcentral-message-type` vengono incluse automaticamente.

Per l'archiviazione BLOB, i messaggi vengono inviati in batch ed esportati una volta al minuto.

L'esempio seguente mostra un messaggio di modifica delle proprietà esportato ricevuto nell'archivio BLOB di Azure.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="device-lifecycle-changes-format"></a>Formato delle modifiche del ciclo di vita del dispositivo

Ogni messaggio o record rappresenta una modifica a un singolo dispositivo. Le informazioni nel messaggio esportato includono:

- `applicationId`: ID dell'applicazione IoT Central.
- `messageSource`: Origine del messaggio `deviceLifecycle` .
- `messageType`: `registered` O `deleted` .
- `deviceId`: ID del dispositivo che è stato modificato.
- `schema`: Il nome e la versione dello schema del payload.
- `templateId`: ID del modello di dispositivo associato al dispositivo.
- `enqueuedTime`: Ora in cui si è verificata la modifica in IoT Central.
- `enrichments`: Eventuali arricchimenti impostati nell'esportazione.

Per gli hub eventi e il bus di servizio, IoT Central Esporta i nuovi dati dei messaggi nell'hub eventi o nella coda o nell'argomento del bus di servizio quasi in tempo reale. Nelle proprietà utente (definite anche proprietà dell'applicazione) di ogni messaggio,,, `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` e `iotcentral-message-type` vengono incluse automaticamente.

Per l'archiviazione BLOB, i messaggi vengono inviati in batch ed esportati una volta al minuto.

L'esempio seguente mostra un messaggio del ciclo di vita del dispositivo esportato ricevuto nell'archivio BLOB di Azure.

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceLifecycle",
  "messageType": "registered",
  "deviceId": "1vzb5ghlsg1",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```
## <a name="device-template-lifecycle-changes-format"></a>Formato delle modifiche del ciclo di vita del modello di dispositivo

Ogni messaggio o record rappresenta una modifica a un singolo modello di dispositivo pubblicato. Le informazioni nel messaggio esportato includono:

- `applicationId`: ID dell'applicazione IoT Central.
- `messageSource`: Origine del messaggio `deviceTemplateLifecycle` .
- `messageType`: `created` , `updated` O `deleted` .
- `schema`: Il nome e la versione dello schema del payload.
- `templateId`: ID del modello di dispositivo associato al dispositivo.
- `enqueuedTime`: Ora in cui si è verificata la modifica in IoT Central.
- `enrichments`: Eventuali arricchimenti impostati nell'esportazione.

Per gli hub eventi e il bus di servizio, IoT Central Esporta i nuovi dati dei messaggi nell'hub eventi o nella coda o nell'argomento del bus di servizio quasi in tempo reale. Nelle proprietà utente (definite anche proprietà dell'applicazione) di ogni messaggio,,, `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` e `iotcentral-message-type` vengono incluse automaticamente.

Per l'archiviazione BLOB, i messaggi vengono inviati in batch ed esportati una volta al minuto.

L'esempio seguente mostra un messaggio del ciclo di vita del dispositivo esportato ricevuto nell'archivio BLOB di Azure.

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceTemplateLifecycle",
  "messageType": "created",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>Confronto tra esportazione e esportazione dei dati legacy

Nella tabella seguente vengono illustrate le differenze tra l' [esportazione dei dati legacy](howto-export-data-legacy.md) e le nuove funzionalità di esportazione dei dati:

| Funzionalità  | Esportazione dei dati legacy | Nuova esportazione dei dati |
| :------------- | :---------- | :----------- |
| Tipi di dati disponibili | Telemetria, dispositivi, modelli di dispositivo | Telemetria, modifiche alle proprietà, modifiche del ciclo di vita del dispositivo, modifiche del ciclo di vita |
| Filtro | nessuno | Dipende dal tipo di dati esportato. Per la telemetria, filtra per telemetria, proprietà del messaggio, valori delle proprietà |
| Arricchimenti | nessuno | Arricchire con una stringa personalizzata o un valore di proprietà nel dispositivo |
| Destinazioni | Hub eventi di Azure, code e argomenti del bus di servizio di Azure, archiviazione BLOB di Azure | Come per l'esportazione di dati legacy e i webhook|
| Versioni applicazione supportate | V2, V3 | Solo V3 |
| Limiti rilevanti | 5 esportazioni per app, 1 destinazione per esportazione | 10 esportazioni-connessioni di destinazione per app |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare la nuova esportazione dei dati, un passaggio successivo suggerito consiste nell'apprendere [come usare Analytics in IOT Central](./howto-create-analytics.md)
