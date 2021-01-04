---
title: 'Avvio rapido: Libreria client di Archiviazione code di Azure v12 - JavaScript'
description: Informazioni su come usare la libreria client di Archiviazione code di Azure v12 per JavaScript per creare una coda e aggiungervi messaggi, oltre che su come leggere ed eliminare i messaggi dalla coda. e come eliminare una coda.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-js
ms.openlocfilehash: 23c58526ba481a56b371bd077661d8d0bc7d45c7
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586534"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Avvio rapido: Libreria client di Archiviazione code di Azure v12 per JavaScript

Questa guida di avvio rapido illustra come iniziare a usare la libreria client di Archiviazione code di Azure v12 per JavaScript. Archiviazione code di Azure è un servizio che consente di archiviare un numero elevato di messaggi per recuperali ed elaborarli successivamente. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

Usare la libreria client di Archiviazione code di Azure v12 per JavaScript per:

- Creare una coda
- Aggiungere messaggi a una coda
- Visualizzare in anteprima i messaggi in una coda
- Aggiornare un messaggio in una coda
- Ricevere messaggi da una coda
- Eliminare messaggi da una coda
- Eliminare una coda

Risorse aggiuntive:

- [Documentazione di riferimento delle API](/javascript/api/@azure/storage-queue/)
- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- [Pacchetto (npm)](https://www.npmjs.com/package/@azure/storage-queue)
- [Esempi](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- Account di archiviazione di Azure: [creare un account di archiviazione](../common/storage-account-create.md)
- Versione aggiornata di [Node.js](https://nodejs.org/en/download/) per il sistema operativo in uso.

## <a name="setting-up"></a>Configurazione

Questa sezione illustra come preparare un progetto da usare con la libreria client di Archiviazione code di Azure v12 per JavaScript.

### <a name="create-the-project"></a>Creare il progetto

Creare un'applicazione Node.js denominata `queues-quickstart-v12`

1. In una finestra della console, ad esempio cmd, PowerShell o Bash, creare una nuova directory per il progetto.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Passare alla directory `queues-quickstart-v12` appena creata.

    ```console
    cd queues-quickstart-v12
    ```

1. Creare un nuovo file di testo denominato `package.json`. Questo file definisce il progetto node.js. Salvare questo file nella directory `queues-quickstart-v12`. Ecco il contenuto del file:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Se si vuole, è possibile inserire il proprio nome in per il campo `author`.

### <a name="install-the-package"></a>Installare il pacchetto

Sempre nella directory `queues-quickstart-v12`, installare il pacchetto della libreria client di Archiviazione code di Azure per JavaScript usando il comando `npm install`.

```console
npm install
```

Questo comando legge il file `package.json` e installa il pacchetto della libreria client di Archiviazione code di Azure v12 per JavaScript e tutte le librerie da cui dipende.

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Aprire un altro nuovo file di testo nell'editor del codice
1. Aggiungere chiamate `require` per caricare i moduli di Azure e node.js
1. Creare la struttura per il programma, inclusa la gestione delle eccezioni di base

    Ecco il codice:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue Storage client library v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Salvare il nuovo file come `queues-quickstart-v12.js` nella directory `queues-quickstart-v12`.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modello a oggetti

Archiviazione code di Azure è un servizio per l'archiviazione di un numero elevato di messaggi. Un messaggio in coda avere dimensioni fino a 64 KB. Una coda può contenere milioni di messaggi, fino al limite di capacità totale dell'account di archiviazione. Le code vengono in genere usate per creare un backlog di lavoro da elaborare in modo asincrono. Archiviazione code offre tre tipi di risorse:

- L'account di archiviazione
- Una coda nell'account di archiviazione
- Messaggi all'interno della coda

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura di Archiviazione code](./media/storage-queues-introduction/queue1.png)

Per interagire con queste risorse, usare le classi JavaScript seguenti:

- [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient): la classe `QueueServiceClient` consente di gestire tutte le code nell'account di archiviazione.
- [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient): la classe `QueueClient` consente di gestire e modificare una singola coda e i relativi messaggi.
- [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage): la classe `QueueMessage` rappresenta i singoli oggetti restituiti quando si chiama [`ReceiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) su una coda.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice di esempio illustrano come eseguire le azioni seguenti con la libreria client di Archiviazione code di Azure per JavaScript:

- [Ottenere la stringa di connessione](#get-the-connection-string)
- [Creare una coda](#create-a-queue)
- [Aggiungere messaggi a una coda](#add-messages-to-a-queue)
- [Visualizzare in anteprima i messaggi in una coda](#peek-at-messages-in-a-queue)
- [Aggiornare un messaggio in una coda](#update-a-message-in-a-queue)
- [Ricevere messaggi da una coda](#receive-messages-from-a-queue)
- [Eliminare messaggi da una coda](#delete-messages-from-a-queue)
- [Eliminare una coda](#delete-a-queue)

### <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il codice seguente recupera la stringa di connessione per l'account di archiviazione dalla variabile di ambiente creata nella sezione [Configurare la stringa di connessione di archiviazione](#configure-your-storage-connection-string).

Aggiungere questo codice all'interno della funzione `main`:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Creare una coda

Decidere un nome per la nuova coda. Il codice seguente aggiunge un valore UUID in fondo al nome della coda per garantirne l'univocità.

> [!IMPORTANT]
> I nomi di coda possono contenere solo lettere minuscole, numeri e segni meno e devono iniziare con una lettera o un numero. Ogni trattino deve essere preceduto e seguito da un carattere diverso da un trattino. Il nome deve inoltre avere una lunghezza compresa fra 3 e 63 caratteri. Per altre informazioni, vedere [Denominazione di code e metadati](/rest/api/storageservices/naming-queues-and-metadata).

Creare un'istanza della classe [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient). Chiamare quindi il metodo [`create`](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) per creare la coda nell'account di archiviazione.

Aggiungere questo codice alla fine della funzione `main`:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Aggiungere messaggi a una coda

Il frammento di codice seguente aggiunge messaggi alla coda chiamando il metodo [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-). Salva anche il [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage) restituito dalla terza chiamata a `sendMessage`. Il `sendMessageResponse` restituito viene usato per aggiornare il contenuto del messaggio in un secondo momento nel programma.

Aggiungere questo codice alla fine della funzione `main`:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Visualizzare in anteprima i messaggi in una coda

Per visualizzare in anteprima i messaggi nella coda, chiamare il metodo [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-). Questo metodo recupera uno o più messaggi dall'inizio della coda, senza modificare la visibilità del messaggio.

Aggiungere questo codice alla fine della funzione `main`:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Aggiornare un messaggio in una coda

Per aggiornare il contenuto di un messaggio, chiamare il metodo [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-). Questo metodo può modificare il timeout di visibilità e il contenuto di un messaggio. Il contenuto del messaggio deve essere una stringa con codifica UTF-8 di dimensioni non superiori a 64 KB. Insieme al nuovo contenuto, passare `messageId` e `popReceipt` dalla risposta salvata in precedenza nel codice. Le proprietà `sendMessageResponse` identificano il messaggio da aggiornare.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda

Scaricare i messaggi aggiunti precedentemente chiamando il metodo [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-). Nel campo `numberOfMessages` passare il numero massimo di messaggi da ricevere per questa chiamata.

Aggiungere questo codice alla fine della funzione `main`:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Eliminare messaggi da una coda

Eliminare i messaggi dalla coda dopo che sono stati ricevuti ed elaborati. In questo caso, l'elaborazione consiste semplicemente nella visualizzazione del messaggio nella console.

Per eliminare i messaggi, chiamare il metodo [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-). Gli eventuali messaggi che non vengono eliminati in modo esplicito diventeranno nuovamente visibili nella coda per poter essere elaborati di nuovo.

Aggiungere questo codice alla fine della funzione `main`:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Eliminare una coda

Il codice seguente pulisce le risorse create dall'app eliminando la coda tramite il metodo [`delete`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-).

Aggiungere questo codice alla fine della funzione `main` e salvare il file:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Eseguire il codice

Questa app crea e aggiunge tre messaggi a una coda di Azure. Il codice elenca i messaggi nella coda, quindi li recupera e li elimina, prima di eliminare definitivamente la coda.

Nella finestra della console passare alla directory contenente il file `queues-quickstart-v12.js`, quindi usare il comando `node` seguente per eseguire l'app.

```console
node queues-quickstart-v12.js
```

L'output dell'app è simile all'esempio seguente:

```output
Azure Queue Storage client library v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Scorrere il codice nel debugger e controllare il [portale di Azure](https://portal.azure.com) durante il processo. Controllare l'account di archiviazione per verificare che i messaggi nella coda vengano creati ed eliminati.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare una coda e aggiungervi messaggi usando codice JavaScript. Si è quindi appreso come visualizzare in anteprima, recuperare ed eliminare i messaggi. Infine, si è appreso come eliminare una coda di messaggi.

Per esercitazioni, esempi, guide di avvio rapido e altra documentazione, vedere:

> [!div class="nextstepaction"]
> [Documentazione di Azure per JavaScript](/azure/developer/javascript/)

- Per altre informazioni, vedere la [libreria client di Archiviazione code di Azure per JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
- Per altre app di esempio di Archiviazione code di Azure, vedere gli [esempi della libreria client di Archiviazione code di Azure v12 per JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
