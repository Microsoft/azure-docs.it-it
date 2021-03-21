---
title: 'Avvio rapido: Libreria client di Archiviazione code di Azure v12 - Java'
description: Informazioni su come usare la libreria client di Archiviazione code di Azure v12 per Java per creare una coda e aggiungervi messaggi, oltre che su come leggere ed eliminare i messaggi dalla coda. e come eliminare una coda.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/01/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 814531adc9dafa524797d0c2674a1e600e407bed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588217"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Avvio rapido: Libreria client di Archiviazione code di Azure v12 per Java

Questa guida di avvio rapido illustra come iniziare a usare la libreria client di Archiviazione code di Azure v12 per Java. Archiviazione code di Azure è un servizio che consente di archiviare un numero elevato di messaggi per recuperali ed elaborarli successivamente. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

Usare la libreria client di Archiviazione code di Azure v12 per Java per:

- Creare una coda
- Aggiungere messaggi a una coda
- Visualizzare in anteprima i messaggi in una coda
- Aggiornare un messaggio in una coda
- Ricevere ed eliminare messaggi da una coda
- Eliminare una coda

Risorse aggiuntive:

- [Documentazione di riferimento delle API](/java/api/overview/azure/storage-queue-readme)
- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [Pacchetto (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [Esempi](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Prerequisiti

- [Java Development Kit (JDK)](/java/azure/jdk/) versione 8 o successiva
- [Apache Maven](https://maven.apache.org/download.cgi)
- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- Account di archiviazione di Azure: [creare un account di archiviazione](../common/storage-account-create.md)

## <a name="setting-up"></a>Configurazione

Questa sezione illustra la procedura per preparare un progetto da usare con la libreria client di Archiviazione code di Azure v12 per Java.

### <a name="create-the-project"></a>Creare il progetto

Creare un'applicazione Java denominata `queues-quickstart-v12`.

1. Nella finestra di una console (ad esempio cmd, PowerShell o Bash) usare Maven `queues-quickstart-v12`blob-quickstart-v12. Digitare il comando `mvn` seguente per creare un progetto Java "hello world".

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.quickstart `
        --define artifactId=queues-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.quickstart \
        --define artifactId=queues-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

1. L'output della generazione del progetto avrà un aspetto simile al seguente:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. Passare alla directory `queues-quickstart-v12` appena creata.

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Installare il pacchetto

Aprire il file `pom.xml` nell'editor di testo. Aggiungere l'elemento di dipendenza seguente al gruppo di dipendenze.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Passare alla directory `/src/main/java/com/queues/quickstart`
1. Aprire il file `App.java` nell'editor
1. Eliminare l'istruzione `System.out.println("Hello, world");`
1. Aggiungere le direttive `import`

Ecco il codice:

```java
package com.queues.quickstart;

/**
 * Azure Queue Storage client library v12 quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modello a oggetti

Archiviazione code di Azure è un servizio per l'archiviazione di un numero elevato di messaggi. Un messaggio in coda avere dimensioni fino a 64 KB. Una coda può contenere milioni di messaggi, fino al limite di capacità totale dell'account di archiviazione. Le code vengono in genere usate per creare un backlog di lavoro da elaborare in modo asincrono. Archiviazione code offre tre tipi di risorse:

- L'account di archiviazione
- Una coda nell'account di archiviazione
- Messaggi all'interno della coda

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura di Archiviazione code](./media/storage-queues-introduction/queue1.png)

Per interagire con queste risorse, usare le classi Java seguenti:

- [`QueueClientBuilder`](/java/api/com.azure.storage.queue.queueclientbuilder): la classe `QueueClientBuilder` configura e crea un'istanza di un oggetto `QueueClient`.
- [`QueueServiceClient`](/java/api/com.azure.storage.queue.queueserviceclient): la classe `QueueServiceClient` consente di gestire tutte le code nell'account di archiviazione.
- [`QueueClient`](/java/api/com.azure.storage.queue.queueclient): la classe `QueueClient` consente di gestire e modificare una singola coda e i relativi messaggi.
- [`QueueMessageItem`](/java/api/com.azure.storage.queue.models.queuemessageitem): la classe `QueueMessageItem` rappresenta i singoli oggetti restituiti quando si chiama [`ReceiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) su una coda.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice di esempio mostrano come eseguire le azioni seguenti con la libreria client di Archiviazione code di Azure per Java:

- [Ottenere la stringa di connessione](#get-the-connection-string)
- [Creare una coda](#create-a-queue)
- [Aggiungere messaggi a una coda](#add-messages-to-a-queue)
- [Visualizzare in anteprima i messaggi in una coda](#peek-at-messages-in-a-queue)
- [Aggiornare un messaggio in una coda](#update-a-message-in-a-queue)
- [Ricevere ed eliminare messaggi da una coda](#receive-and-delete-messages-from-a-queue)
- [Eliminare una coda](#delete-a-queue)

### <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il codice seguente recupera la stringa di connessione dell'account di archiviazione. La stringa di connessione è archiviata nella variabile di ambiente creata nella sezione [Configurare la stringa di connessione di archiviazione](#configure-your-storage-connection-string).

Aggiungere questo codice all'interno del metodo `main`:

```java
System.out.println("Azure Queue Storage client library v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Creare una coda

Decidere un nome per la nuova coda. Il codice seguente aggiunge un valore GUID in fondo al nome della coda per garantirne l'univocità.

> [!IMPORTANT]
> I nomi di coda possono contenere solo lettere minuscole, numeri e segni meno e devono iniziare con una lettera o un numero. Ogni trattino deve essere preceduto e seguito da un carattere diverso da un trattino. Il nome deve inoltre avere una lunghezza compresa fra 3 e 63 caratteri. Per altre informazioni sull'assegnazione di nomi alle code, vedere [Denominazione di code e metadati](/rest/api/storageservices/naming-queues-and-metadata).

Creare un'istanza della classe [`QueueClient`](/java/api/com.azure.storage.queue.queueclient). Chiamare quindi il metodo [`Create`](/java/api/com.azure.storage.queue.queueclient.create) per creare la coda nell'account di archiviazione.

Aggiungere questo codice alla fine del metodo `main`:

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>Aggiungere messaggi a una coda

Il frammento di codice seguente aggiunge messaggi alla coda chiamando il metodo [`sendMessage`](/java/api/com.azure.storage.queue.queueclient.sendmessage). Salva anche un [`SendMessageResult`](/java/api/com.azure.storage.queue.models.sendmessageresult) restituito da una chiamata a `sendMessage`. Il risultato viene usato per aggiornare il messaggio in un secondo momento nel programma.

Aggiungere questo codice alla fine del metodo `main`:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Visualizzare in anteprima i messaggi in una coda

Per visualizzare in anteprima i messaggi nella coda, chiamare il metodo [`peekMessages`](/java/api/com.azure.storage.queue.queueclient.peekmessages). Questo metodo recupera uno o più messaggi dall'inizio della coda, senza modificare la visibilità del messaggio.

Aggiungere questo codice alla fine del metodo `main`:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Aggiornare un messaggio in una coda

Per aggiornare il contenuto di un messaggio, chiamare il metodo [`updateMessage`](/java/api/com.azure.storage.queue.queueclient.updatemessage). Questo metodo può modificare il timeout di visibilità e il contenuto di un messaggio. Il contenuto del messaggio deve essere una stringa con codifica UTF-8 di dimensioni non superiori a 64 KB. Insieme al nuovo contenuto del messaggio, passare l'ID del messaggio e la ricevuta di rimozione usando il `SendMessageResult` salvato in precedenza nel codice. L'ID del messaggio e la ricevuta di rimozione identificano il messaggio da aggiornare.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(),
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Ricevere ed eliminare messaggi da una coda

Scaricare i messaggi aggiunti precedentemente chiamando il metodo [`receiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages). Il codice di esempio elimina anche i messaggi dalla coda dopo che sono stati ricevuti ed elaborati. In questo caso, l'elaborazione consiste semplicemente nella visualizzazione del messaggio nella console.

L'app viene sospesa per l'input dell'utente chiamando `System.console().readLine();` prima della ricezione ed eliminazione dei messaggi. Verificare nel [portale di Azure](https://portal.azure.com) che le risorse siano state create correttamente, prima di eliminarle. Gli eventuali messaggi che non vengono eliminati in modo esplicito diventeranno nuovamente visibili nella coda per poter essere elaborati di nuovo.

Aggiungere questo codice alla fine del metodo `main`:

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>Eliminare una coda

Il codice seguente pulisce le risorse create dall'app eliminando la coda tramite il metodo [`Delete`](/java/api/com.azure.storage.queue.queueclient.delete).

Aggiungere questo codice alla fine del metodo `main`:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Eseguire il codice

Questa app crea e aggiunge tre messaggi a una coda di Azure. Il codice elenca i messaggi nella coda, quindi li recupera e li elimina, prima di eliminare definitivamente la coda.

Nella finestra della console passare alla directory dell'applicazione, quindi compilarla ed eseguirla.

```console
mvn compile
```

Compilare il pacchetto.

```console
mvn package
```

Usare il comando `mvn` seguente per eseguire l'app.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

L'output dell'app è simile all'esempio seguente:

```output
Azure Queue Storage client library v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

Quando l'app viene sospesa prima della ricezione dei messaggi, controllare l'account di archiviazione nel [portale di Azure](https://portal.azure.com). Verificare che i messaggi siano nella coda.

Premere `Enter` per ricevere ed eliminare i messaggi. Quando richiesto, premere di nuovo `Enter` per eliminare la coda e terminare la demo.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare una coda e aggiungervi messaggi usando codice Java. Si è quindi appreso come visualizzare in anteprima, recuperare ed eliminare i messaggi. Infine, si è appreso come eliminare una coda di messaggi.

Per esercitazioni, esempi, guide di avvio rapido e altra documentazione, vedere:

> [!div class="nextstepaction"]
> [Azure per sviluppatori cloud Java](/azure/developer/java/)

- Per altre app di esempio di Archiviazione code di Azure, vedere gli [esempi della libreria client di Archiviazione code di Azure v12 per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).
