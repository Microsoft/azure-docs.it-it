---
title: Introduzione all'archiviazione code di Azure con .NET-archiviazione di Azure
description: Archiviazione code di Azure fornisce messaggistica asincrona e affidabile tra i componenti dell'applicazione. La messaggistica cloud consente di ridimensionare i componenti dell'applicazione in modo indipendente.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 10/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e32779e75480d365ec10e8c7f849fddd27c891c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275992"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Introduzione all'archiviazione code di Azure con .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Panoramica

Archiviazione code di Azure fornisce la messaggistica cloud tra i componenti dell'applicazione. Per la progettazione di applicazioni per la scalabilità, i componenti dell'applicazione vengono spesso separati, in modo da poterli ridimensionare in modo indipendente. L'archiviazione Code fornisce la messaggistica asincrona tra i componenti dell'applicazione, che siano in esecuzione nel cloud, sul desktop, in un server locale o in un dispositivo mobile. L'archiviazione Code supporta anche la gestione di attività asincrone e la compilazione di flussi di lavoro di processo.

### <a name="about-this-tutorial"></a>Informazioni sull'esercitazione

Questa esercitazione illustra come scrivere codice .NET per alcuni scenari comuni usando l'archiviazione code di Azure. Gli scenari presentati includono creazione ed eliminazione di code, nonché aggiunta, lettura ed eliminazione di messaggi nella coda.

**Tempo previsto per il completamento:** 45 minuti

### <a name="prerequisites"></a>Prerequisiti

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- Un [account di archiviazione di Azure](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Configurare quindi l'ambiente di sviluppo in Visual Studio per poter provare gli esempi di codice di questa guida.

### <a name="create-a-windows-console-application-project"></a>Creare un progetto di applicazione console di Windows

In Visual Studio creare una nuova applicazione console di Windows. La procedura seguente illustra come creare un'applicazione console in Visual Studio 2019. La procedura è simile per le altre versioni di Visual Studio.

1. Selezionare **file**  >  **nuovo**  >  **progetto**
2. Selezionare le  >  **finestre** della piattaforma
3. Selezionare l' **app console (.NET Framework)**
4. Selezionare **Avanti**
5. Nel campo **nome progetto** , immettere un nome per l'applicazione
6. Selezionare **Crea**

Tutti gli esempi di codice in questa esercitazione possono essere aggiunti al metodo `Main()` del file `Program.cs` dell'applicazione console.

È possibile usare le librerie client di archiviazione di Azure in qualsiasi tipo di applicazione .NET, tra cui un servizio cloud o un'app Web di Azure, nonché applicazioni desktop e per dispositivi mobili. Per semplicità, in questa guida si usa un'applicazione console.

### <a name="use-nuget-to-install-the-required-packages"></a>Usare NuGet per installare i pacchetti necessari

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Per completare l'esercitazione, è necessario fare riferimento ai quattro pacchetti seguenti nel progetto:

- [Azure. Core Library per .NET](https://www.nuget.org/packages/azure.core/): questo pacchetto fornisce primitive condivise, astrazioni e helper per le moderne librerie client di .NET Azure SDK.
- [Azure. storage. Common Client Library per .NET](https://www.nuget.org/packages/azure.storage.common/): questo pacchetto fornisce l'infrastruttura condivisa dalle altre librerie client di archiviazione di Azure.
- [Azure. storage. Queues client library for .NET](https://www.nuget.org/packages/azure.storage.queues/): questo pacchetto consente di usare l'archiviazione code di Azure per archiviare i messaggi a cui un client può accedere.
- [System.Configuration.Configlibreria urationManager per .NET](https://www.nuget.org/packages/system.configuration.configurationmanager/): questo pacchetto consente di accedere ai file di configurazione per le applicazioni client.

È possibile usare NuGet per ottenere questi pacchetti. A tale scopo, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.
1. Selezionare **Sfoglia**
1. Cercare online per `Azure.Storage.Queues` e selezionare **Installa** per installare la libreria client di archiviazione di Azure e le relative dipendenze. Verranno installate anche le librerie Azure. storage. Common e Azure. Core, che sono dipendenze della libreria di Accodamento.
1. Cercare online per `System.Configuration.ConfigurationManager` e selezionare **Installa** per installare il Configuration Manager.

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Per completare questa esercitazione, è necessario fare riferimento ai tre pacchetti seguenti nel progetto:

- [Microsoft. Azure. storage. Common Client Library per .NET](https://www.nuget.org/packages/microsoft.azure.storage.common/): questo pacchetto fornisce l'accesso a livello di codice alle risorse di dati nell'account di archiviazione.
- [Microsoft Azure libreria client di archiviazione code per .NET](https://www.nuget.org/packages/microsoft.azure.storage.queue/): questa libreria client consente di usare l'archiviazione code di Azure per archiviare i messaggi a cui è possibile accedere da un client.
- [Libreria Gestione configurazione di Microsoft Azure per .NET](https://www.nuget.org/packages/microsoft.azure.configurationmanager/): questo pacchetto fornisce una classe per l'analisi di una stringa di connessione in un file di configurazione, indipendentemente dalla posizione in cui viene eseguita l'applicazione.

È possibile usare NuGet per ottenere questi pacchetti. A tale scopo, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.
1. Selezionare **Sfoglia**
1. Cercare online per `Microsoft.Azure.Storage.Queue` e selezionare **Installa** per installare la libreria client di archiviazione di Azure e le relative dipendenze. Verrà installata anche la libreria Microsoft. Azure. storage. Common, che è una dipendenza della libreria di Accodamento.
1. Cercare online per `Microsoft.Azure.ConfigurationManager` e selezionare **Installa** per installare il Configuration Manager di Azure.

---

### <a name="determine-your-target-environment"></a>Determinare l'ambiente di destinazione

Sono disponibili due opzioni relative all'ambiente per l'esecuzione degli esempi di questa guida:

- È possibile eseguire il codice con un account di archiviazione di Azure nel cloud.
- È possibile eseguire il codice nell'emulatore di archiviazione azzurrite. Azzurrite è un ambiente locale che emula un account di archiviazione di Azure nel cloud. Azzurrite è un'opzione gratuita per il test e il debug del codice mentre l'applicazione è in fase di sviluppo. L'emulatore usa un account e una chiave noti. Per altre informazioni, vedere [usare l'emulatore di azzurrite per lo sviluppo e il test locali di archiviazione di Azure](../common/storage-use-azurite.md).

> [!NOTE]
> È possibile impostare come destinazione l'emulatore di archiviazione per evitare di incorrere negli eventuali costi associati al servizio Archiviazione di Azure. Tuttavia, se si sceglie di fare riferimento a un account di archiviazione di Azure nel cloud, i costi per l'esecuzione di questa esercitazione saranno irrilevanti.

## <a name="get-your-storage-connection-string"></a>Ottenere la stringa di connessione di archiviazione

Le librerie client di archiviazione di Azure per .NET supportano l'uso di una stringa di connessione di archiviazione per configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione. Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../common/storage-account-keys-manage.md).

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiare le credenziali dal portale di Azure

Il codice di esempio deve autorizzare l'accesso all'account di archiviazione. Per eseguire l'autorizzazione, si passano all'applicazione le credenziali dell'account di archiviazione sotto forma di stringa di connessione. Per visualizzare le credenziali dell'account di archiviazione:

1. Passare al [portale di Azure](https://portal.azure.com).
2. Individuare l'account di archiviazione.
3. Nella sezione **Impostazioni** della panoramica dell'account di archiviazione selezionare **Chiavi di accesso**. Verranno visualizzate le chiavi di accesso dell'account, con la stringa di connessione completa per ogni chiave.
4. Trovare il valore **Stringa di connessione** in **key1** e fare clic sul pulsante **Copia** per copiare la stringa di connessione. Il valore della stringa di connessione verrà aggiunto a una variabile di ambiente nel passaggio successivo.

    ![Screenshot che mostra come copiare una stringa di connessione dal portale di Azure](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Per altre informazioni sulle stringhe di connessione, vedere [Configurare le stringhe di connessione di archiviazione di Azure](../common/storage-configure-connection-string.md).

> [!NOTE]
> La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. È consigliabile proteggere sempre la chiave dell'account di archiviazione. Evitare di distribuirla ad altri utenti, impostarla come hardcoded o salvarla in un file di testo normale accessibile ad altri. Rigenerare la chiave tramite il portale di Azure se si ritiene che possa essere stata compromessa.

Per gestire nel modo migliore la stringa di connessione di archiviazione, usare un file di configurazione. Per configurare la stringa di connessione, aprire il file `app.config` da Esplora soluzioni in Visual Studio. Aggiungere il contenuto dell' `<appSettings>` elemento visualizzato qui. Sostituire `connection-string` con il valore copiato dall'account di archiviazione nel portale:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

Ad esempio, l'impostazione di configurazione si presenta simile a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Per fare riferimento all'emulatore di archiviazione azzurrite, è possibile usare un collegamento che esegue il mapping al nome e alla chiave dell'account noto. In questo caso, l'impostazione della stringa di connessione è:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Aggiungere le direttive using

Aggiungere le direttive `using` seguenti all'inizio del file `Program.cs`:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-storage-client"></a>Creare il client di archiviazione di Accodamento

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

La [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) classe consente di recuperare le code archiviate nell'archivio code. Ecco come creare il client del servizio:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

La [`CloudQueueClient`](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true) classe consente di recuperare le code archiviate nell'archivio code. Ecco come creare il client del servizio:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

A questo punto si è pronti per scrivere codice che legge e scrive i dati nell'archivio code.

## <a name="create-a-queue"></a>Creare una coda

Questo esempio illustra come creare una coda:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Inserire un messaggio in una coda

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Per inserire un messaggio in una coda esistente, chiamare il [`SendMessage`](/dotnet/api/azure.storage.queues.queueclient.sendmessage) metodo. Un messaggio può essere una stringa (in formato UTF-8) o una matrice di byte. Il codice seguente crea una coda (se non esiste) e inserisce un messaggio:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Per inserire un messaggio in una coda esistente, creare innanzitutto un nuovo oggetto [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Chiamare quindi il [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) metodo. `CloudQueueMessage`È possibile creare un oggetto da una stringa (in formato UTF-8) o da una matrice di byte. Ecco il codice che crea una coda (se non esiste) e inserisce il messaggio `Hello, World` : per inserire un messaggio in una coda esistente, creare prima un nuovo [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Chiamare quindi il [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) metodo. `CloudQueueMessage`È possibile creare un oggetto da una stringa (in formato UTF-8) o da una matrice di byte. Ecco il codice che crea una coda (se non esiste) e inserisce il messaggio `Hello, World` :

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Visualizzare il messaggio successivo

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

È possibile leggere i messaggi nella coda senza rimuoverli dalla coda chiamando il [`PeekMessages`](/dotnet/api/azure.storage.queues.queueclient.peekmessages) metodo. Se non si passa un valore per il `maxMessages` parametro, l'impostazione predefinita prevede la visualizzazione di un messaggio.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

È possibile visualizzare il messaggio nella parte anteriore di una coda senza rimuoverlo dalla coda chiamando il [`PeekMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) metodo.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Cambiare il contenuto di un messaggio in coda

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. Il codice seguente consente di aggiornare il messaggio in coda con nuovo contenuto e di impostarne il timeout di visibilità per prolungarlo di altri 60 secondi. In questo modo lo stato del lavoro associato al messaggio viene salvato e il client ha a disposizione un altro minuto per continuare l'elaborazione del messaggio. È possibile utilizzare questa tecnica per tenere traccia dei flussi di lavoro in più passaggi nei messaggi in coda, senza dover ricominciare dall'inizio se un passaggio di elaborazione non riesce a causa di un errore hardware o software. In genere, è consigliabile mantenere anche un conteggio dei tentativi, in modo da eliminare i messaggi per cui vengono effettuati più di *n* tentativi. In questo modo è possibile evitare che un messaggio attivi un errore dell'applicazione ogni volta che viene elaborato.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="dequeue-the-next-message"></a>Rimuovere il messaggio successivo dalla coda

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Rimuovere dalla coda un messaggio da una coda in due passaggi. Quando si chiama [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) , si ottiene il messaggio successivo in una coda. Un messaggio restituito da `ReceiveMessages` diventa invisibile a qualsiasi altro elemento di codice che legge i messaggi di questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche [`DeleteMessage`](/dotnet/api/azure.storage.queues.queueclient.deletemessage) . Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama `DeleteMessage` subito dopo l'elaborazione del messaggio.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Quando si chiama [`GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) , si ottiene il messaggio successivo in una coda. Un messaggio restituito da `GetMessage` diventa invisibile a qualsiasi altro elemento di codice che legge i messaggi di questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche [`DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true) . Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama `DeleteMessage` subito dopo l'elaborazione del messaggio.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

<!-- docutune:casing "Async-Await pattern" "Async and Await" -->

## <a name="use-the-async-await-pattern-with-common-queue-storage-apis"></a>Usare il modello di Async-Await con le API comuni di archiviazione delle code

Questo esempio illustra come usare il modello di Async-Await con le API comuni di archiviazione code. Nell'esempio viene chiamata la versione asincrona di ognuno dei metodi specificati, come indicato dal `Async` suffisso di ogni metodo. Quando viene usato un metodo asincrono, il modello di Async-Await sospende l'esecuzione locale fino al completamento della chiamata. Questo comportamento consente al thread corrente di eseguire altre attività per evitare colli di bottiglia delle prestazioni e migliora la velocità di risposta complessiva dell'applicazione. Per ulteriori informazioni sull'utilizzo del modello Async-Await in .NET, vedere [Async e Await (C# e Visual Basic)](/previous-versions/hh191443(v=vs.140))

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="use-additional-options-for-dequeuing-messages"></a>Usare opzioni aggiuntive per la rimozione dalla coda dei messaggi

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Nell'esempio di codice seguente viene usato il [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) metodo per ottenere 20 messaggi in una sola chiamata. Ogni messaggio viene poi elaborato con un ciclo `foreach`. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti. Si noti che i cinque minuti iniziano per tutti i messaggi contemporaneamente, quindi dopo che sono trascorsi cinque minuti dalla chiamata a `ReceiveMessages` , tutti i messaggi che non sono stati eliminati diventeranno nuovamente visibili.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Nell'esempio di codice seguente viene usato il [`GetMessages`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) metodo per ottenere 20 messaggi in una sola chiamata. Ogni messaggio viene poi elaborato con un ciclo `foreach`. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti. Si noti che i cinque minuti iniziano per tutti i messaggi contemporaneamente, quindi dopo che sono trascorsi cinque minuti dalla chiamata a `GetMessages` , tutti i messaggi che non sono stati eliminati diventeranno nuovamente visibili.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>Recuperare la lunghezza della coda

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il [`GetProperties`](/dotnet/api/azure.storage.queues.queueclient.getproperties) metodo restituisce le proprietà della coda, incluso il numero di messaggi. La [`ApproximateMessagesCount`](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) proprietà contiene il numero approssimativo di messaggi nella coda. Questo numero non è inferiore al numero effettivo di messaggi nella coda, ma potrebbe essere superiore.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il [`FetchAttributes`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true) metodo restituisce gli attributi della coda, incluso il numero di messaggi. La [`ApproximateMessageCount`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true) proprietà restituisce l'ultimo valore recuperato dal `FetchAttributes` metodo, senza chiamare l'archivio code.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Eliminare una coda

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Per eliminare una coda e tutti i messaggi che contiene, chiamare il [`Delete`](/dotnet/api/azure.storage.queues.queueclient.delete) metodo sull'oggetto Queue.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Per eliminare una coda e tutti i messaggi che contiene, chiamare il [`Delete`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) metodo sull'oggetto Queue.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese le nozioni di base dell'archiviazione code, seguire i collegamenti seguenti per informazioni sulle attività di archiviazione più complesse.

- Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento per l'archiviazione code:
  - [Informazioni di riferimento sulla libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage)
  - [Riferimento all'API REST di archiviazione di Azure](/rest/api/storageservices/)
- Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
  - [Introduzione all'archiviazione tabelle di Azure con .NET](../../cosmos-db/tutorial-develop-table-dotnet.md) per archiviare dati strutturati.
  - [Introduzione all'archiviazione BLOB di Azure con .NET](../blobs/storage-quickstart-blobs-dotnet.md) per archiviare dati non strutturati.
  - Per archiviare i dati relazionali, vedere [Connettersi al database SQL tramite .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md).
- Per altre informazioni su come semplificare il codice scritto da usare con Archiviazione di Azure, vedere [Informazioni su Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
