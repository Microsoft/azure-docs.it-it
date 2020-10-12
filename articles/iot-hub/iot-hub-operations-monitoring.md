---
title: Monitoraggio delle operazioni dell'hub IoT di Azure (deprecata) | Microsoft Docs
description: Come usare il monitoraggio delle operazioni dell'hub IoT di Azure per monitorare lo stato delle operazioni nell'hub IoT in tempo reale.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: nberdy
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 9d9824be536ca657d9213a47898ad19b0c39d8b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022021"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Monitoraggio delle operazioni dell’hub IoT (deprecata)

Il monitoraggio delle operazioni dell'hub IoT consente di monitorare lo stato delle operazioni nel proprio hub IoT in tempo reale. L'hub IoT tiene traccia degli eventi nelle diverse categorie di operazioni. È possibile scegliere di impostare l'invio di eventi da una o più categorie a un endpoint del proprio hub IoT per l'elaborazione. È possibile monitorare i dati per individuare gli errori o configurare un'elaborazione più complessa in base ai modelli di dati.

>[!NOTE]
>Il monitoraggio delle **operazioni dell'hub IoT è deprecato ed è stato rimosso dall'hub IoT il 10 marzo 2019**. Per monitorare le operazioni e l'integrità dell'hub IoT, vedere [Monitor the health of Azure IoT Hub and diagnose problems quickly](iot-hub-monitor-resource-health.md) (Monitorare lo stato dell'hub IoT di Azure e diagnosticare i problemi rapidamente). Per altre informazioni sulla sequenza temporale relativa alla funzionalità deprecata, vedere [Monitorare le soluzioni IoT di Azure con Monitoraggio di Azure e Integrità risorse di Azure](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health).

L'hub IoT monitora sei categorie di eventi:

* Operazioni relative alle identità dei dispositivi
* Telemetria dei dispositivi
* Messaggi da cloud a dispositivo
* Connessioni
* Caricamenti di file
* Routing dei messaggi

> [!IMPORTANT]
> Il monitoraggio delle operazioni dell'hub IoT non garantisce il recapito affidabile o ordinato di eventi. A seconda dell'infrastruttura sottostante dell'hub IoT alcuni eventi potrebbero essere persi o recapitati nell'ordine errato. Usare le operazioni di monitoraggio per generare avvisi in base ai segnali di errore, ad esempio i tentativi di connessione non riuscita o disconnessioni ad alta frequenza di dispositivi specifici. Non basarsi sulle operazioni di monitoraggio di eventi per creare un archivio coerente per lo stato del dispositivo, ad esempio un archivio che rileva lo stato di connessione o disconnessione di un dispositivo. 

## <a name="how-to-enable-operations-monitoring"></a>Come abilitare il monitoraggio delle operazioni

1. Creare un hub IoT. Le istruzioni sulla creazione di un hub IoT sono disponibili nella [Guida introduttiva](quickstart-send-telemetry-dotnet.md).

2. Aprire il pannello dell'hub IoT. Da qui, fare clic su **Monitoraggio operazioni**.

    ![Accedere alla configurazione del monitoraggio delle operazioni nel portale](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Selezionare le categorie di monitoraggio da controllare e fare clic su **Salva**. Gli eventi sono disponibili per la lettura nell'endpoint compatibile con l'hub eventi elencato in **Impostazioni di monitoraggio**. L'endpoint dell'hub IoT è chiamato `messages/operationsmonitoringevents`.

    ![Configurare il monitoraggio delle operazioni sull'hub IoT](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> La selezione del monitoraggio **Dettagliato** per la categoria **Connessioni** consente all'hub IoT di generare messaggi di diagnostica aggiuntivi. Per tutte le altre categorie, l'impostazione **Dettagliato** modifica la quantità di informazioni che l'hub IoT include in ogni messaggio di errore.

## <a name="event-categories-and-how-to-use-them"></a>Categorie di eventi e modalità d'uso

Ogni categoria di monitoraggio delle operazioni tiene traccia di un diverso tipo di interazione con l'hub IoT e ogni categoria di monitoraggio ha uno schema che definisce come sono strutturati gli eventi nella categoria stessa.

### <a name="device-identity-operations"></a>Operazioni relative alle identità dei dispositivi

La categoria di operazioni di identità del dispositivo tiene traccia degli errori che si verificano quando si prova a creare, aggiornare o eliminare una voce nel registro delle identità dell'hub IoT. Il rilevamento di questa categoria è utile per gli scenari di provisioning.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Telemetria dei dispositivi

La categoria di telemetria dei dispositivi tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di telemetria. Questa categoria include gli errori che si verificano durante l'invio di eventi di telemetria, ad esempio la limitazione, e la ricezione di eventi di telemetria, ad esempio un lettore non autorizzato. Questa categoria non può intercettare gli errori causati da codice in esecuzione nel dispositivo stesso.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Comandi da cloud a dispositivo

La categoria di comandi da cloud a dispositivo tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di messaggi da cloud a dispositivo. Questa categoria include gli errori che si verificano durante l'invio di messaggi da cloud a dispositivo, ad esempio un mittente non autorizzato, la ricezione di messaggi da cloud a dispositivo, ad esempio il superamento del numero di recapiti, e la ricezione di commenti sui messaggi da cloud a dispositivo, ad esempio commenti scaduti. Questa categoria non intercetta gli errori da un dispositivo che gestisce in modo non corretto un messaggio da cloud a dispositivo, se questo è stato recapitato correttamente.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Connessioni

La categoria Connessioni tiene traccia degli errori che si verificano quando i dispositivi si connettono o disconnettono da un hub IoT. Il rilevamento di questa categoria è utile per identificare i tentativi di connessione non autorizzati e per rilevare quando una connessione viene persa dai dispositivi in aree di scarsa connettività.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Caricamenti di file

La categoria di caricamenti dei file tiene traccia degli errori che si verificano nell'hub IoT e correlati alla funzionalità di caricamento dei file. Questa categoria include:

* Errori che si verificano con l'URI di firma di accesso condiviso, ad esempio quando l'URI scade prima che un dispositivo notifichi all'hub un caricamento completato.

* Caricamenti non riusciti segnalati dal dispositivo.

* Errori che si verificano quando un file non viene trovato nell'archivio durante la creazione del messaggio di notifica dell'hub IoT.

Questa categoria non può intercettare errori che si verificano direttamente mentre il dispositivo sta caricando un file in memoria.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Routing dei messaggi

La categoria del routing dei messaggi tiene traccia degli errori che si verificano durante la valutazione del routing dei messaggi e dell'integrità dell'endpoint percepiti dall'hub IoT. Questa categoria include eventi come ad esempio quando una regola viene valutata come "non definita", quando l'hub IoT contrassegna un endpoint come inattivo ed eventuali altri errori ricevuti da un endpoint. Questa categoria non include errori specifici sui messaggi stessi, ad esempio gli errori di limitazione sui dispositivi, che vengono segnalati nella categoria "telemetria del dispositivo".

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="connect-to-the-monitoring-endpoint"></a>Connettersi all'endpoint di monitoraggio

L'endpoint di monitoraggio sull'hub IoT è un endpoint compatibile con Hub eventi. Per leggere i messaggi di monitoraggio da questo endpoint, è possibile usare qualsiasi meccanismo che funzioni con l'Hub eventi. L'esempio seguente crea un lettore di base non adatto per una distribuzione con velocità effettiva elevata. Per altre informazioni su come elaborare i messaggi da Hub eventi, vedere l'esercitazione [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md) .

Per connettersi all'endpoint di monitoraggio, è necessaria una stringa di connessione e il nome dell'endpoint. La procedura seguente mostra come trovare i valori necessari nel portale:

1. Nel portale, passare al pannello di risorse dell'hub IoT.

2. Scegliere **Monitoraggio operazioni** e prendere nota dei valori in **Nome compatibile con Hub eventi** e in **Endpoint compatibile con Hub eventi**:

    ![Valori di Endpoint compatibile con Hub eventi](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Scegliere **Criteri di accesso condiviso**, quindi scegliere **servizio**. Prendere nota del valore presente in **Chiave primaria**:

    ![Chiave primaria del servizio dei criteri di accesso condiviso](./media/iot-hub-operations-monitoring/service-key.png)

L'esempio di codice C# seguente viene preso da un'app console C# per **Desktop classico di Windows** di Visual Studio. Il progetto ha il pacchetto **WindowsAzure.ServiceBus** NuGet installato.

* Sostituire il placeholder della stringa di connessione con una stringa di connessione che usa i valori precedentemente annotati per l'**endpoint compatibile con Hub eventi** e il servizio **Chiave primaria** come illustrato nell'esempio seguente:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Sostituire il placeholder del nome dell'endpoint di monitoraggio con il valore del **nome Hub eventi compatibile** annotato in precedenza.

```csharp
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();

        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }

        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }

            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));

            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)