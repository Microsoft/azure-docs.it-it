---
title: Inviare o ricevere eventi da Hub eventi di Azure con JavaScript (legacy)
description: Questo articolo illustra come creare un'applicazione JavaScript che invia o riceve eventi da Hub eventi di Azure usando il pacchetto azure/event-hubs versione 2.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 548276b4bcbdd514c37a19375154923ad9f3f9a4
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85314507"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Guida introduttiva: Inviare o ricevere eventi da Hub eventi di Azure con JavaScript (@azure/event-hubs versione 2)
Questa guida di avvio rapido illustra come creare applicazioni JavaScript per inviare o ricevere eventi da un hub eventi usando il pacchetto JavaScript azure/event-hubs versione 2. 

> [!WARNING]
> In questa guida di avvio rapido si usa il pacchetto azure/event-hubs versione 2. Per una guida di avvio rapido che usa la **versione 5** del pacchetto, vedere [Inviare e ricevere eventi con azure/eventhubs versione 5](get-started-node-send-v2.md). Per fare in modo che l'applicazione usi il nuovo pacchetto al posto di quello precedente, vedere la [guida alla migrazione dalla versione 1 alla versione 5 di azure/eventhubs](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Prerequisiti

Se non si ha familiarità con Hub eventi di Azure, vedere [Panoramica di Hub eventi](event-hubs-about.md) prima di procedere con questa guida di avvio rapido. 

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- **Sottoscrizione di Microsoft Azure**. Per usare i servizi di Azure, tra cui Hub eventi di Azure, è necessaria una sottoscrizione.  Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/free/) oppure usare i vantaggi della sottoscrizione di MSDN per la [creazione di un account](https://azure.microsoft.com).
- Node.js versione 8.x e successiva. Scaricare la versione LTS più recente da [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (scelta consigliata) o qualsiasi altro ambiente di sviluppo integrato
- **Creare uno spazio dei nomi di Hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md) e quindi continuare con i passaggi seguenti di questa esercitazione. Ottenere quindi la stringa di connessione per lo spazio dei nomi dell'hub eventi seguendo le istruzioni disponibili nell'articolo [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La stringa di connessione sarà necessaria più avanti nell'esercitazione.


### <a name="install-npm-package"></a>Installare un pacchetto npm
Per installare il [pacchetto npm per Hub eventi](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0), aprire un prompt dei comandi il cui percorso comprenda `npm`, passare alla cartella in cui si vogliono archiviare gli esempi e quindi eseguire questo comando

```shell
npm install @azure/event-hubs@2
```

Per installare il [pacchetto npm per l'host processore di eventi](https://www.npmjs.com/package/@azure/event-processor-host), eseguire invece il comando seguente

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Inviare eventi

Questa sezione illustra come creare un'applicazione JavaScript che invia eventi a un hub eventi. 

> [!NOTE]
> È possibile scaricare questa guida introduttiva come esempio da [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi in uso ed eseguirla. In alternativa, è possibile seguire la procedura illustrata in questa esercitazione per creare una soluzione propria.

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com). 
2. Creare un file denominato `send.js` e incollarvi il codice riportato di seguito. Ottenere la stringa di connessione per lo spazio dei nomi dell'hub eventi seguendo le istruzioni disponibili nell'articolo: [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Immettere la stringa di connessione e il nome dell'hub eventi nel codice precedente
4. Eseguire quindi questo file tramite il comando `node send.js` in un prompt dei comandi. Verranno inviati 100 eventi all'hub eventi

Congratulazioni! Sono stati inviati eventi a un hub eventi.


## <a name="receive-events"></a>Ricevere eventi

Questa sezione illustra come creare un'applicazione JavaScript che riceve eventi da una singola partizione del gruppo di consumer predefinito in un hub eventi. 

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com). 
2. Creare un file denominato `receive.js` e incollarvi il codice riportato di seguito.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Immettere la stringa di connessione e il nome dell'hub eventi nel codice precedente.
4. Eseguire quindi questo file tramite il comando `node receive.js` in un prompt dei comandi. Verranno ricevuti gli eventi da una delle partizioni del gruppo di consumer predefinito nell'hub eventi

Congratulazioni! Sono stati ricevuti eventi dall'hub eventi.

## <a name="receive-events-using-event-processor-host"></a>Ricevere eventi con l'host processore di eventi

Questa sezione illustra come ricevere eventi da un hub eventi usando [EventProcessorHost](event-hubs-event-processor-host.md) di Azure in un'applicazione JavaScript. EventProcessorHost (EPH) consente di ricevere in modo efficiente gli eventi da un hub eventi tramite la creazione di ricevitori tra tutte le partizioni nel gruppo di consumer di un hub eventi. Esegue il checkpoint dei metadati sui messaggi ricevuti a intervalli regolari in un BLOB del servizio di archiviazione di Azure. Questo approccio semplifica la possibilità di continuare a ricevere messaggi dal punto in cui si era interrotto in un momento successivo.

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com). 
2. Creare un file denominato `receiveAll.js` e incollarvi il codice riportato di seguito.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Immettere la stringa di connessione e il nome dell'hub eventi nel codice precedente, insieme alla stringa di connessione per un archivio BLOB di Azure
4. Eseguire quindi questo file tramite il comando `node receiveAll.js` in un prompt dei comandi.

Congratulazioni! Sono stati ricevuti eventi da un hub eventi tramite l'host processore di eventi. Verranno ricevuti gli eventi da tutte le partizioni del gruppo di consumer predefinito nell'hub eventi

## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funzionalità e terminologia di Hub eventi di Azure](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)
- Vedere gli altri esempi di JavaScript per [Hub eventi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) e per l'[host processore di eventi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) in GitHub
