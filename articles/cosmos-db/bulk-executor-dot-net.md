---
title: Utilizzare la libreria .NET Executor in blocco Azure Cosmos DB per operazioni di importazione e aggiornamento bulk
description: Eseguire l'importazione e l'aggiornamento bulk dei documenti di Azure Cosmos DB utilizzando la libreria .NET Executor in blocco.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 34aef5bd880e3ef080676fb9e90e62796d499e7b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429817"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Utilizzare la libreria .NET Executor in blocco per eseguire operazioni bulk in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> Questa libreria dell'esecutore bulk descritta in questo articolo viene mantenuta per le applicazioni che usano .NET SDK versione 2. x. Per le nuove applicazioni, è possibile usare il **supporto bulk** disponibile direttamente con [.NET SDK versione 3. x](tutorial-sql-api-dotnet-bulk-import.md) e non richiede alcuna libreria esterna. 

> Se si sta utilizzando la libreria dell'executor bulk e si intende eseguire la migrazione al supporto bulk nell'SDK più recente, attenersi alla procedura descritta nella [Guida alla migrazione](how-to-migrate-from-bulk-executor-library.md) per eseguire la migrazione dell'applicazione.

Questa esercitazione fornisce le istruzioni per importare e aggiornare i documenti nei contenitori di Azure Cosmos DB usando la libreria .NET dell'executor in blocco. Per informazioni sulla libreria di esecutori bulk e sul modo in cui è possibile sfruttare la velocità effettiva e l'archiviazione di massa, vedere l'articolo [Panoramica della libreria dell'executor in blocco](bulk-executor-overview.md) . In questa esercitazione verrà visualizzata un'applicazione .NET di esempio che esegue l'importazione bulk di documenti generati in modo casuale in un contenitore di Azure Cosmos. Dopo l'importazione illustra come è possibile aggiornare in blocco i dati importati specificando le patch come operazioni da eseguire su campi di documenti specifici.

Attualmente, la libreria di esecuzioni bulk è supportata solo dagli account Azure Cosmos DB API SQL e Gremlin. Questo articolo descrive come usare la libreria .NET Executor in blocco con gli account API SQL. Per informazioni sull'uso della libreria .NET Executor in blocco con account API Gremlin, vedere [eseguire operazioni bulk nell'api Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Prerequisiti

* Se Visual Studio 2019 non è ancora installato, è possibile scaricare e usare [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Assicurarsi di abilitare "sviluppo Azure" durante l'installazione di Visual Studio.

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

* È possibile [provare Microsoft Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure, gratuitamente e senza impegno. In alternativa, è possibile usare l' [emulatore Azure Cosmos DB](./local-emulator.md) con l' `https://localhost:8081` endpoint. La chiave primaria viene fornita in [Autenticazione delle richieste](local-emulator.md#authenticate-requests).

* Creare un account API SQL di Azure Cosmos DB tramite la procedura descritta nella sezione [Creare un account di database](create-sql-api-dotnet.md#create-account) dell'articolo sulla guida introduttiva per .NET.

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

A questo punto, è possibile passare all'uso del codice scaricando un'applicazione .NET di esempio da GitHub. Questa applicazione esegue operazioni bulk sui dati archiviati nell'account Azure Cosmos. Per clonare l'applicazione, aprire un prompt dei comandi, passare alla directory in cui si vuole copiare ed eseguire il comando seguente:

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Il repository clonato contiene due esempi "BulkImportSample" e "BulkUpdateSample". È possibile aprire una delle applicazioni di esempio, aggiornare le stringhe di connessione in App.config file con le stringhe di connessione dell'account Azure Cosmos DB, compilare la soluzione ed eseguirla.

L'applicazione "BulkImportSample" genera documenti casuali ed esegue l'importazione bulk nell'account Azure Cosmos. L'applicazione "BulkUpdateSample" aggiorna in blocco i documenti importati, specificando le patch come operazioni da eseguire su campi di documenti specifici. Nelle sezioni successive verrà esaminato il codice in ognuna di queste app di esempio.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Eseguire l'importazione bulk di dati in un account Azure Cosmos

1. Passare alla cartella "BulkImportSample" e aprire il file "BulkImportSample.sln".  

2. Le stringhe di connessione del Azure Cosmos DB vengono recuperate dal file di App.config, come illustrato nel codice seguente:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   L'utilità di importazione bulk crea un nuovo database e un contenitore con il nome del database, il nome del contenitore e i valori di velocità effettiva specificati nel file di App.config.

3. L'oggetto DocumentClient viene quindi inizializzato con la modalità di connessione TCP diretto:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. L'oggetto BulkExecutor viene inizializzato con un valore di ripetizione elevato per il tempo di attesa e le richieste limitate. Questi vengono quindi impostati su 0 per passare il controllo della congestione a BulkExecutor per la relativa durata.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. L'applicazione richiama l'API BulkImportAsync. La libreria .NET fornisce due overload dell'API di importazione bulk, uno che accetta un elenco di documenti JSON serializzati e l'altro che accetta un elenco di documenti POCO deserializzati. Per altre informazioni sulle definizioni di ognuno di questi metodi di overload, vedere la [documentazione dell'API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Di seguito sono illustrati i parametri accettati dal metodo BulkImportAsync**.
   
   |**Parametro**  |**Descrizione** |
   |---------|---------|
   |enableUpsert    |   Flag per abilitare le operazioni Upsert sui documenti. Se un documento con l'ID specificato esiste già, viene aggiornato. Per impostazione predefinita, il valore è impostato su false.      |
   |disableAutomaticIdGeneration    |    Flag per disabilitare la generazione automatica dell'ID. Per impostazione predefinita, è impostato su true.     |
   |maxConcurrencyPerPartitionKeyRange    | Il grado massimo di concorrenza per ogni intervallo di chiavi di partizione; impostando il valore null la libreria userà il valore predefinito 20. |
   |maxInMemorySortingBatchSize     |  Numero massimo di documenti estratti dall'enumeratore del documento, che viene passato alla chiamata API in ogni fase. Per la fase di ordinamento in memoria che si verifica prima dell'importazione bulk, impostando questo parametro su null, la libreria utilizzerà il valore minimo predefinito (Documents. Count, 1 milione).       |
   |cancellationToken    |    Il token di annullamento per uscire normalmente dall'operazione di importazione bulk.     |

   **Definizione dell'oggetto risposta di importazione in blocco** Il risultato della chiamata API di importazione in blocco contiene gli attributi seguenti:

   |**Parametro**  |**Descrizione**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  Il numero totale di documenti che sono stati importati correttamente dal totale dei documenti forniti alla chiamata all'API di importazione bulk.       |
   |TotalRequestUnitsConsumed (double)   |   Le unità richiesta (UR) totali usate dalla chiamata API di importazione in blocco.      |
   |TotalTimeTaken (TimeSpan)    |   Tempo totale impiegato dalla chiamata dell'API di importazione bulk per completare l'esecuzione.      |
   |BadInputDocuments (List\<object>)   |     L'elenco di documenti con formato non valido che non sono stati importati correttamente nella chiamata API di importazione in blocco. Correggere i documenti restituiti e ripetere l'importazione. I documenti con formato non valido includono i documenti con un valore ID diverso da stringa, ad esempio con un valore null o con qualsiasi altro tipo di dati.    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Aggiornare in blocco i dati nell'account Azure Cosmos

È possibile aggiornare i documenti esistenti tramite l'API BulkUpdateAsync. In questo esempio il campo viene impostato `Name` su un nuovo valore e il campo viene rimosso `Description` dai documenti esistenti. Per il set completo di operazioni di aggiornamento supportate, vedere la [documentazione dell'API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate).

1. Passare alla cartella "BulkImportSample" e aprire il file "BulkImportSample.sln".  

2. Definire gli elementi di aggiornamento insieme alle operazioni di aggiornamento dei campi corrispondenti. In questo esempio verrà usato `SetUpdateOperation` per aggiornare il `Name` campo e `UnsetUpdateOperation` per rimuovere il `Description` campo da tutti i documenti. È possibile anche eseguire altre operazioni, ad esempio incrementare un campo del documento per un valore specifico, eseguire il push di valori specifici in un campo di matrice o rimuovere un valore specifico da un campo di matrice. Per informazioni sui diversi metodi forniti dall'API di aggiornamento in blocco, vedere la [documentazione dell'API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. L'applicazione richiama l'API BulkUpdateAsync. Per informazioni sulla definizione del metodo BulkUpdateAsync, vedere la [documentazione dell'API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **Di seguito sono illustrati i parametri accettati dal metodo BulkUpdateAsync**.

   |**Parametro**  |**Descrizione** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Il livello massimo di concorrenza per ogni intervallo di chiavi di partizione. Se si imposta questo parametro su null, la libreria utilizzerà il valore predefinito (20).   |
   |maxInMemorySortingBatchSize    |    Numero massimo di elementi di aggiornamento estratti dall'enumeratore Update Items passati alla chiamata API in ogni fase. Per la fase di ordinamento in memoria che si verifica prima dell'aggiornamento bulk, impostando questo parametro su null si farà in modo che la libreria usi il valore minimo predefinito (updateItems. Count, 1 milione).     |
   | cancellationToken|Il token di annullamento per uscire normalmente dall'operazione di aggiornamento in blocco. |

   **Definizione dell'oggetto risposta di aggiornamento in blocco** Il risultato della chiamata API di aggiornamento in blocco contiene gli attributi seguenti:

   |**Parametro**  |**Descrizione** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   Il numero di documenti che sono stati aggiornati correttamente dai documenti totali forniti alla chiamata API per l'aggiornamento bulk.      |
   |TotalRequestUnitsConsumed (double)   |    Unità richiesta totali (UR) utilizzate dalla chiamata API per l'aggiornamento bulk.    |
   |TotalTimeTaken (TimeSpan)   | Tempo totale impiegato dalla chiamata dell'API per l'aggiornamento bulk per completare l'esecuzione. |
    
## <a name="performance-tips"></a>Suggerimenti per incrementare le prestazioni 

Quando si usa la libreria Executor bulk, considerare i punti seguenti per ottenere prestazioni migliori:

* Per ottenere prestazioni ottimali, eseguire l'applicazione da una macchina virtuale di Azure che si trova nella stessa area dell'area di scrittura dell'account Azure Cosmos.  

* Si consiglia di creare un'istanza di un singolo `BulkExecutor` oggetto per l'intera applicazione all'interno di una singola macchina virtuale che corrisponde a un contenitore Azure Cosmos specifico.  

* Poiché una singola esecuzione dell'API per operazioni bulk utilizza un gran numero di operazioni di i/o di rete e CPU del computer client (ciò avviene generando più attività internamente). Evitare la generazione di più attività simultanee nel processo dell'applicazione che eseguono chiamate API per operazioni bulk. Se una singola chiamata API per operazioni bulk in esecuzione in una singola macchina virtuale non è in grado di utilizzare la velocità effettiva dell'intero contenitore (se la velocità effettiva del contenitore > 1 milione ur/sec), è preferibile creare macchine virtuali separate per eseguire simultaneamente le chiamate API per operazioni bulk.  

* Verificare `InitializeAsync()` che il metodo venga richiamato dopo avere creato un'istanza di un oggetto BulkExecutor per recuperare la mappa di partizione del contenitore Cosmos di destinazione.  

* In App.Config dell'applicazione verificare che **gcServer** sia abilitato per assicurare prestazioni migliori.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* La libreria genera tracce che possono essere raccolte in un file di log o nella console. Per abilitare entrambi, aggiungere il codice seguente al file di App.Config dell'applicazione.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui dettagli del pacchetto NuGet e sulle note sulla versione, vedere i [Dettagli di bulk Executor SDK](sql-api-sdk-bulk-executor-dot-net.md).
