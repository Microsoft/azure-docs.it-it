---
title: Sviluppare in locale con l'emulatore Azure Cosmos
description: Usando l'emulatore Azure Cosmos, è possibile sviluppare e testare gratuitamente l'applicazione in locale, senza creare una sottoscrizione di Azure.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 01/31/2020
ms.openlocfilehash: cd7f1ba856cb2c58d14f7a2560a7716beff92076
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849009"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Usare l'emulatore Azure Cosmos per sviluppo e test locali

L'emulatore Azure Cosmos fornisce un ambiente locale che emula il servizio Azure Cosmos DB a fini di sviluppo. Usando l'emulatore Azure Cosmos, è possibile sviluppare e testare l'applicazione in locale, senza creare una sottoscrizione di Azure né sostenere costi. Quando si è soddisfatti del funzionamento dell'applicazione nell'emulatore Azure Cosmos, è possibile iniziare a usare un account Azure Cosmos nel cloud.

È possibile sviluppare applicazioni usando l'emulatore Azure Cosmos con account delle API [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api) e [Tabella](local-emulator.md#table-api). Tuttavia, in questo momento, la vista Esplora dati nell'emulatore supporta completamente i client solo per l'API SQL. 

## <a name="how-the-emulator-works"></a>Come funziona l'emulatore

L'emulatore Azure Cosmos offre un'emulazione ultra fedele del servizio Azure Cosmos DB. Supporta le stesse funzionalità di Azure Cosmos DB, incluso il supporto per la creazione e l'esecuzione di query sui dati, il provisioning e la scalabilità dei contenitori e l'esecuzione di stored procedure e trigger. È possibile sviluppare e testare le applicazioni usando l'emulatore Azure Cosmos e distribuirle in Azure su scala globale semplicemente apportando una singola modifica di configurazione all'endpoint di connessione per Azure Cosmos DB.

Anche se l'emulazione del servizio Azure Cosmos DB è fedele, l'implementazione dell'emulatore è diversa da quella del servizio. Ad esempio, l'emulatore usa i componenti del sistema operativo standard, come il file system locale per la persistenza e lo stack di protocolli HTTPS per la connettività. Le funzionalità basate sull'infrastruttura di Azure, ad esempio la replica globale, la latenza in millisecondi a cifra singola per le operazioni di lettura/scrittura e i livelli di coerenza regolabili, non sono applicabili.

È possibile eseguire la migrazione dei dati tra l'emulatore Azure Cosmos e il servizio Azure Cosmos DB usando lo [strumento per la migrazione dei dati di Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

È possibile eseguire l'emulatore Azure Cosmos nel contenitore Docker di Windows. Accedere all'[hub Docker](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) per informazioni sul comando docker pull e a [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) per informazioni su `Dockerfile` e altro ancora.

## <a name="differences-between-the-emulator-and-the-service"></a>Differenze tra l'emulatore e il servizio

Poiché l'emulatore Azure Cosmos fornisce un ambiente emulato eseguito nella workstation di sviluppo locale, esistono alcune differenze a livello di funzionalità tra l'emulatore e un account Azure Cosmos nel cloud:

* Esplora dati nell'emulatore attualmente supporta i client per l'API SQL. La vista Esplora dati e le operazioni per le API di Azure Cosmos DB, tra cui le API MongoDB, Tabella, Graph e Cassandra, non sono supportate completamente.
* L'emulatore Azure Cosmos supporta solo un singolo account fisso e una chiave master nota. La rigenerazione della chiave non è possibile nell'emulatore Cosmos Azure, tuttavia la chiave predefinita può essere modificata usando l'opzione della riga di comando.
* L'emulatore Azure Cosmos non è un servizio scalabile e non supporta un numero elevato di contenitori.
* L'emulatore Azure Cosmos non offre [livelli di coerenza di Azure Cosmos DB](consistency-levels.md) diversi.
* L'emulatore Azure Cosmos non offre la [replica multiarea](distribute-data-globally.md).
* Poiché la copia dell'emulatore Azure Cosmos potrebbe non essere sempre aggiornata con le modifiche più recenti del servizio Azure Cosmos DB, fare riferimento allo [strumento di pianificazione della capacità di Azure Cosmos DB](https://www.documentdb.com/capacityplanner) per stimare con precisione le esigenze di velocità effettiva (UR) dell'applicazione.
* Quando si usa l'emulatore Azure Cosmos, per impostazione predefinita è possibile creare fino a 25 contenitori di dimensione fissa (supportati solo con Azure Cosmos DB SDK) o 5 contenitori senza limiti con l'emulatore Azure Cosmos. Per altre informazioni sulla modifica di questo valore, vedere la sezione su come [impostare il valore PartitionCount](#set-partitioncount).
* L'emulatore supporta dimensioni massime della proprietà ID di 254 caratteri.

## <a name="system-requirements"></a>Requisiti di sistema

L'emulatore Azure Cosmos presenta i requisiti hardware e software seguenti:

* Requisiti software
  * Windows Server 2012 R2, Windows Server 2016 o Windows Server 10
  * Sistema operativo a 64 bit
* Requisiti hardware minimi
  * 2 GB DI RAM
  * 10 GB di spazio disponibile su disco rigido

## <a name="installation"></a>Installazione

È possibile scaricare e installare l'emulatore Azure Cosmos dall'[Area download Microsoft](https://aka.ms/cosmosdb-emulator) o eseguirlo in Docker per Windows. Per istruzioni sull'uso dell'emulatore in Docker per Windows, vedere [Esecuzione in Docker](#running-on-docker).

> [!NOTE]
> Per installare, configurare ed eseguire l'emulatore Azure Cosmos, è necessario avere i privilegi di amministratore nel computer. L'emulatore creerà o aggiungerà un certificato e imposterà le regole del firewall per eseguire i relativi servizi. È pertanto necessario che l'emulatore sia in grado di eseguire queste operazioni.

## <a name="running-on-windows"></a>Esecuzione in Windows

Per avviare l'emulatore Azure Cosmos, fare clic sul pulsante Start o premere WINDOWS sulla tastiera. Iniziare a digitare **Azure Cosmos Emulator** e selezionare l'emulatore dall'elenco di applicazioni.

![Fare clic sul pulsante Start o premere WINDOWS sulla tastiera, iniziare a digitare **Azure Cosmos Emulator** e selezionare l'emulatore dall'elenco di applicazioni](./media/local-emulator/database-local-emulator-start.png)

Quando l'emulatore è in esecuzione, verrà visualizzata un'icona nell'area di notifica della barra delle applicazioni di Windows. ![Notifica della barra delle applicazioni dell'emulatore locale di Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

Per impostazione predefinita, l'emulatore Azure Cosmos viene eseguito nel computer locale ("localhost") in ascolto sulla porta 8081.

Per impostazione predefinita, l'emulatore Azure Cosmos viene installato in `C:\Program Files\Azure Cosmos DB Emulator`. È anche possibile avviare e arrestare l'emulatore dalla riga di comando. Per altre informazioni, vedere le [informazioni di riferimento sullo strumento da riga di comando](#command-line).

## <a name="start-data-explorer"></a>Avviare Esplora dati

Quando l'emulatore Azure Cosmos viene avviato, apre automaticamente Esplora dati di Azure Cosmos nel browser. L'indirizzo viene visualizzato come `https://localhost:8081/_explorer/index.html`. Se si chiude lo strumento di esplorazione e lo si vuole riaprire in seguito, è possibile aprire l'URL nel browser o avviarlo dall'emulatore Azure Cosmos usando l'icona dell'area di notifica di Windows, come illustrato sotto.

![Utilità di avvio di Esplora dati dell'emulatore locale Azure Cosmos](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Preparazione per gli aggiornamenti

Esplora dati indica se è disponibile un nuovo aggiornamento per il download.

> [!NOTE]
> Non è detto che i dati creati in una versione dell'emulatore Azure Cosmos (vedere %LOCALAPPDATA%\CosmosDBEmulator o le impostazioni facoltative per il percorso dati) siano accessibili quando si usa una versione diversa. Se è necessario rendere persistenti i dati a lungo termine, si consiglia di archiviarli in un account Azure Cosmos invece che nell'emulatore Azure Cosmos.

## <a name="authenticating-requests"></a>Autenticazione delle richieste

Come per Azure Cosmos DB nel cloud, tutte le richieste effettuate nell'emulatore Azure Cosmos devono essere autenticate. L'emulatore Azure Cosmos supporta un singolo account fisso e una chiave di autenticazione nota per l'autenticazione della chiave master. Questo account e questa chiave sono le uniche credenziali consentite per l'uso con l'emulatore Azure Cosmos. ovvero:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> La chiave master supportata dall'emulatore Azure Cosmos deve essere usata solo con l'emulatore. Non è possibile usare l'account Azure Cosmos DB di produzione e la chiave con l'emulatore Azure Cosmos.

> [!NOTE]
> Se l'emulatore è stato avviato con l'opzione /Key, usare la chiave generata anziché `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. Per altre informazioni sull'opzione /Key, vedere [Informazioni di riferimento sullo strumento da riga di comando](#command-line).

Proprio come Azure Cosmos DB, anche l'emulatore Azure Cosmos supporta solo la comunicazione sicura tramite TLS.

## <a name="running-on-a-local-network"></a>Esecuzione in una rete locale

È possibile eseguire l'emulatore in una rete locale. Per abilitare l'accesso tramite rete, specificare l'opzione `/AllowNetworkAccess` nella [riga di comando](#command-line-syntax), che richiede di specificare anche `/Key=key_string` o `/KeyFile=file_name`. È possibile usare `/GenKeyFile=file_name` per generare un file con una chiave casuale sin dall'inizio, per poi passarli a `/KeyFile=file_name` o `/Key=contents_of_file`.

Per abilitare l'accesso tramite rete per la prima volta, l'utente deve arrestare l'emulatore ed eliminare la relativa directory dei dati (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Sviluppo con l'emulatore

### <a name="sql-api"></a>API SQL

Quando l'emulatore Azure Cosmos è in esecuzione sul desktop, è possibile usare qualsiasi [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) supportato o l'[API REST di Azure Cosmos DB](/rest/api/cosmos-db/) per interagire con l'emulatore. L'emulatore Azure Cosmos include anche una funzionalità Esplora dati che consente di creare contenitori per l'API SQL o l'API di Cosmos DB per MongoDB e di visualizzare e modificare elementi senza scrivere codice.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API Azure Cosmos DB per MongoDB

Quando l'emulatore Azure Cosmos è in esecuzione sul desktop, è possibile usare l'[API di Azure Cosmos DB per MongoDB](mongodb-introduction.md) per interagire. Avviare l'emulatore dal prompt dei comandi come amministratore con "/EnableTableEndpoint". Usare quindi la stringa di connessione seguente per connettersi all'account dell'API MongoDB:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>API di tabella

Quando l'emulatore Azure Cosmos è in esecuzione sul desktop, è possibile usare [Azure Cosmos DB Table API SDK](table-storage-how-to-use-dotnet.md) per interagire con l'emulatore. Avviare l'emulatore dal prompt dei comandi come amministratore con "/EnableTableEndpoint", quindi eseguire il codice seguente per connettersi all'account dell'API Tabella:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>API Cassandra

Avviare l'emulatore dal prompt dei comandi come amministratore con "/EnableCassandraEndpoint". In alternativa, è anche possibile impostare la variabile di ambiente `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

* [Installare Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Installare Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

* Eseguire i comandi seguenti in una normale finestra del prompt dei comandi:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* Nella shell CQLSH eseguire i comandi seguenti per connettersi all'endpoint Cassandra:

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>API Gremlin

Avviare l'emulatore dal prompt dei comandi come amministratore con "/EnableGremlinEndpoint". In alternativa, è anche possibile impostare la variabile di ambiente `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Installare apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

* In Esplora dati dell'emulatore creare un database "db1" e una raccolta "coll1"; per la chiave di partizione scegliere "/name"

* Eseguire i comandi seguenti in una normale finestra del prompt dei comandi:

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* Nella shell Gremlin eseguire i comandi seguenti per connettersi all'endpoint Gremlin:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-tlsssl-certificate"></a>Esportare il certificato TLS/SSL

I linguaggi e il runtime .NET usano l'archivio certificati Windows per connettersi in modo sicuro all'emulatore locale di Azure Cosmos DB. Gli altri linguaggi hanno il proprio metodo di gestione e uso dei certificati. Java usa il proprio [archivio certificati](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), mentre Python usa i [wrapper per i socket](https://docs.python.org/2/library/ssl.html).

Per ottenere un certificato da usare con i linguaggi e i runtime che non si integrano con l'archivio certificati Windows, sarà necessario esportarlo usando Gestione certificati di Windows. Per avviarlo, è possibile eseguire certlm.msc o seguire le istruzioni dettagliate contenute in [Esportare i certificati dell'emulatore Azure Cosmos](./local-emulator-export-ssl-certificates.md). Quando Gestione certificati è in esecuzione, aprire i certificati personali, come illustrato sotto, ed esportare il certificato con il nome descrittivo "DocumentDBEmulatorCertificate" come file Codificato Base 64 X.509 (.CER).

![Certificato TLS/SSL dell'emulatore locale di Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Per importare il certificato X.509 nell'archivio certificati Java, seguire le istruzioni disponibili in [Aggiunta di un certificato all'archivio certificati CA Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Dopo l'importazione del certificato nell'archivio certificati, i client per l'API SQL e l'API di Cosmos DB per MongoDB potranno connettersi all'emulatore Azure Cosmos.

Quando ci si connette all'emulatore da Python e Node.js SDK, la verifica TLS è disabilitata.

## <a name="command-line-tool-reference"></a><a id="command-line"></a>Riferimenti allo strumento da riga di comando
Dal percorso di installazione è possibile usare la riga di comando per avviare e arrestare l'emulatore, configurare le opzioni ed eseguire altre operazioni.

### <a name="command-line-syntax"></a>Sintassi della riga di comando

    Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Per visualizzare l'elenco di opzioni, digitare `Microsoft.Azure.Cosmos.Emulator.exe /?` al prompt dei comandi.

|**Opzione** | **Descrizione** | **Comando**| **Argomenti**|
|---|---|---|---|
|[Nessun argomento] | Avvia l'emulatore Azure Cosmos con le impostazioni predefinite. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Help] |Visualizza l'elenco di argomenti della riga di comando supportati.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Ottiene lo stato dell'emulatore Azure Cosmos. Lo stato è indicato dal codice di uscita: 1 = Avvio, 2 = In esecuzione, 3 = Arrestato. Un codice di uscita negativo indica che si è verificato un errore. Non viene prodotto altro output. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Shutdown| Arresta l'emulatore Azure Cosmos.| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|DataPath | Specifica il percorso in cui archiviare i file di dati. Il valore predefinito è %LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: Un percorso accessibile |
|Porta | Specifica il numero di porta da usare per l'emulatore. Il valore predefinito è 8081. |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<porta\>: numero di porta singolo |
| ComputePort | Specifica il numero di porta da usare per il servizio Gateway di interoperabilità di calcolo. La porta probe dell'endpoint HTTP del gateway viene calcolata come ComputePort + 79. ComputePort e ComputePort + 79 devono pertanto essere aperte e disponibili. Il valore predefinito è 8900. | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<portacalcolo\>: numero di porta singolo |
| EnableMongoDbEndpoint=3.2 | Abilita l'API MongoDB 3.2 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | Abilita l'API MongoDB 3.6 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Specifica il numero di porta da usare per l'API di compatibilità MongoDB. Il valore predefinito è 10255. |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>: numero di porta singolo|
| EnableCassandraEndpoint | Abilita l'API Cassandra | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Specifica il numero di porta da usare per l'endpoint Cassandra. Il valore predefinito è 10350. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<portacassandra\>: numero di porta singolo |
| EnableGremlinEndpoint | Abilita l'API Gremlin | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Numero di porta da usare per l'endpoint Gremlin. Il valore predefinito è 8901. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<porta\>: numero di porta singolo |
|EnableTableEndpoint | Abilita l'API Tabella di Azure | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | Numero di porta da usare per l'endpoint Tabella di Azure. Il valore predefinito è 8902. | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<porta\>: numero di porta singolo|
| KeyFile | Legge la chiave di autorizzazione dal file specificato. Usare l'opzione /GenKeyFile per generare un file di chiave | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<nome_file\>: percorso del file |
| ResetDataPath | Rimuove in modo ricorsivo tutti i file nel percorso specificato. Se non si specifica un percorso, per impostazione predefinita viene usato %LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<path> | \<percorso\>: Percorso del file  |
| StartTraces  |  Avvia la raccolta dei log di traccia di debug tramite LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | Arresta la raccolta dei log di traccia di debug tramite LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Avvia la raccolta dei log di traccia di debug tramite lo strumento di registrazione delle prestazioni di Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Arresta la raccolta dei log di traccia di debug tramite lo strumento di registrazione delle prestazioni di Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Per impostazione predefinita, l'emulatore rigenera il relativo certificato TLS/SSL autofirmato, se il nome alternativo del soggetto del certificato non include il nome di dominio e l'indirizzo IPv4 locale dell'host dell'emulatore, ovvero 'localhost' e '127.0.0.1'. Con questa opzione, l'emulatore invece avrà problemi all'avvio. Sarà quindi necessario usare l'opzione /GenCert per creare e installare un nuovo certificato TLS/SSL autofirmato. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Genera e installa un nuovo certificato TLS/SSL autofirmato, includendo facoltativamente un elenco di nomi DNS aggiuntivi separati da virgole per l'accesso all'emulatore tramite la rete. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<nomi-DNS\>: elenco facoltativo di nomi DNS aggiuntivi delimitati da virgole  |
| DirectPorts |Specifica le porte da usare per la connettività diretta. I valori predefiniti sono 10251, 10252, 10253 e 10254. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<portedirette\>: elenco delimitato da virgole di 4 porte |
| Chiave |Chiave di autorizzazione per l'emulatore. La chiave deve essere la codifica Base 64 di un vettore a 64 byte. | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<key\> | \<chiave\>: La chiave deve essere la codifica Base 64 di un vettore a 64 byte.|
| EnableRateLimiting | Specifica che il comportamento di limitazione della frequenza è abilitato. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Specifica che il comportamento di limitazione della frequenza è disabilitato. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | Non mostra l'interfaccia utente dell'emulatore. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Non mostra Esplora dati all'avvio. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Specifica il numero massimo di contenitori partizionati. Per altre informazioni, vedere [Modificare il numero di contenitori](#set-partitioncount). | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: numero massimo di contenitori con partizione singola consentiti. Il valore predefinito è 25. Il valore massimo consentito è 250.|
| DefaultPartitionCount| Specifica il numero predefinito di partizioni per un contenitore partizionato. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<numeropredefinitopartizioni\> Il valore predefinito è 25.|
| AllowNetworkAccess | Consente l'accesso all'emulatore tramite una rete. È necessario passare anche /Key=\<stringa_chiave\> o /KeyFile=\<nome_file\> per abilitare l'accesso alla rete. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> o Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Non modifica le regole del firewall quando viene usata l'opzione /AllowNetworkAccess. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Genera una nuova chiave di autorizzazione e la salva nel file specificato. La chiave generata può essere usata con le opzioni /Key o /KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<path to key file\> | |
| Consistenza | Imposta il livello di coerenza per l'account. | Microsoft.Azure.Cosmos.Emulator.exe /Consistency=\<consistency\> | \<coerenza\>: Il valore deve essere uno dei [livelli di coerenza](consistency-levels.md) seguenti: Di sessione, Assoluta, Finale, o BoundedStaleness. Il valore predefinito è Session. |
| ? | Mostra il messaggio della Guida.| | |

## <a name="change-the-number-of-containers"></a><a id="set-partitioncount"></a>Modificare il numero di contenitori

Per impostazione predefinita, è possibile creare fino a 25 contenitori di dimensione fissa (supportati solo con Azure Cosmos DB SDK) o 5 contenitori senza limiti con l'emulatore Azure Cosmos. Modificando il valore **PartitionCount**, è possibile creare fino a 250 contenitori di dimensione fissa o 50 contenitori senza limiti oppure qualsiasi combinazione di elementi di questi due tipi, purché non si superino 250 contenitori di dimensione fissa (dove un contenitore senza limiti = 5 contenitori di dimensione fissa). Non è tuttavia consigliabile configurare l'emulatore per l'esecuzione con più di 200 contenitori di dimensione fissa. Ciò comporta infatti un sovraccarico delle operazioni di I/O su disco e può quindi causare timeout imprevedibili quando si usano le API per endpoint.

Se si tenta di creare un contenitore dopo il superamento del conteggio corrente delle partizioni, l'emulatore genera un'eccezione ServiceUnavailable con il messaggio seguente.

"Sorry, we are currently experiencing high demand in this region, and cannot fulfill your request at this time. We work continuously to bring more and more capacity online, and encourage you to try again.
ActivityId: 12345678-1234-1234-1234-123456789abc" (È stata attualmente riscontrata una richiesta elevata in questa area. Non è possibile soddisfare la richiesta in questo momento. Microsoft è costantemente impegnata a garantire sempre più capacità online e invita gli utenti a riprovare. Non esitare a inviare un messaggio di posta elettronica in qualsiasi momento o per qualunque motivo. ID attività: 12345678-1234-1234-1234-123456789abc)

Per modificare il numero di contenitori disponibili nell'emulatore Azure Cosmos, procedere come segue:

1. Eliminare tutti i dati locali dell'emulatore Azure Cosmos facendo clic con il pulsante destro del mouse sull'icona dell'**emulatore di Azure Cosmos DB** nell'area di notifica e quindi scegliendo **Reset Data** (Reimposta dati).
2. Eliminare tutti i dati dell'emulatore in questa cartella: `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Chiudere tutte le istanze aperte facendo clic con il pulsante destro del mouse sull'icona dell'**emulatore di Azure Cosmos DB** sull'area di notifica, quindi fare clic su **Esci**. La chiusura di tutte le istanze può richiedere qualche minuto.
4. Installare la versione più recente dell'[emulatore Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Avviare l'emulatore con il flag PartitionCount impostando un valore <= 250. Ad esempio: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Controllo dell'emulatore

L'emulatore include un modulo di PowerShell per avviare, arrestare, disinstallare e recuperare lo stato del servizio. Eseguire il cmdlet seguente per usare il modulo di PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

o collocare la directory `PSModules` in `PSModulesPath` e importare il modulo come illustrato nel comando seguente:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Ecco un riepilogo dei comandi per il controllo dell'emulatore da PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Sintassi**

`Get-CosmosDbEmulatorStatus`

**Osservazioni:**

Restituisce uno di questi valori per ServiceControllerStatus: ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running o ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Sintassi**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Osservazioni:**

Avvia l'emulatore. Per impostazione predefinita, il comando attende fino a quando l'emulatore è pronto per accettare richieste. Usare l'opzione -NoWait se si vuole che il cmdlet restituisca valori subito dopo avere avviato l'emulatore.

### `Stop-CosmosDbEmulator`

**Sintassi**

 `Stop-CosmosDbEmulator [-NoWait]`

**Osservazioni:**

Attesta l'emulatore. Per impostazione predefinita, questo comando attende fino all'arresto completo dell'emulatore. Usare l'opzione -NoWait se si vuole che il cmdlet restituisca valori subito dopo l'inizio dell'arresto dell'emulatore.

### `Uninstall-CosmosDbEmulator`

**Sintassi**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Osservazioni:**

Disinstalla l'emulatore e facoltativamente rimuove i contenuti completi di $env:LOCALAPPDATA\CosmosDbEmulator.
Il cmdlet verifica che l'emulatore sia arrestato prima di disinstallarlo.

## <a name="running-on-docker"></a>Esecuzione in Docker

L'emulatore Azure Cosmos può essere eseguito in Docker per Windows. L'emulatore non funziona su Docker per Oracle Linux.

Dopo aver installato [Docker per Windows](https://www.docker.com/docker-windows), passare ai contenitori Windows facendo clic con il pulsante destro del mouse sull'icona di Docker sulla barra degli strumenti e selezionando **Switch to Windows containers** (Passa a contenitori Windows).

Eseguire quindi il pull dell'immagine dell'emulatore dall'hub Docker eseguendo il comando seguente dalla shell preferita.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Per avviare l'immagine, eseguire i comandi seguenti.

Dalla riga di comando:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```

> [!NOTE]
> Se viene visualizzato un errore di conflitto della porta (la porta specificata è già in uso) quando si esegue il comando docker run, è possibile passare una porta personalizzata modificando i numeri di porta. Ad esempio, è possibile modificare la porta "-p 8081:8081" in "-p 443:8081"

Da PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

La risposta sarà simile a quanto riportato di seguito:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Usare ora nel client l'endpoint e la chiave master presenti nella risposta e importare il certificato TLS/SSL nell'host. Per importare il certificato TLS/SSL, eseguire le operazioni seguenti da un prompt dei comandi di amministratore:

Dalla riga di comando:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

Da PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Se si chiude la shell interattiva una volta avviato l'emulatore, il contenitore dell'emulatore verrà arrestato.

Per aprire Esplora dati, passare all'URL seguente nel browser. L'endpoint dell'emulatore è specificato nel messaggio di risposta illustrato in precedenza.

    https://<emulator endpoint provided in response>/_explorer/index.html

Se un'applicazione client .NET è in esecuzione in un contenitore Docker Linux e se si esegue l'emulatore di Azure Cosmos in un computer host, vedere la sezione seguente per Linux per importare il certificato nel contenitore Docker Linux.

## <a name="running-on-mac-or-linux"></a>Esecuzione su Mac o Linux<a id="mac"></a>

Attualmente l'emulatore di Cosmos può essere eseguito solo su Windows. Gli utenti Mac o Linux possono eseguire l'emulatore in una macchina virtuale Windows ospitata in un hypervisor come Parallels o VirtualBox. Ecco la procedura per abilitarla:

Nella VM di Windows eseguire il comando seguente e prendere nota dell'indirizzo IPv4.

```cmd
ipconfig.exe
```

All'interno dell'applicazione è necessario modificare l'URI usato come endpoint affinché usi l'indirizzo IPv4 restituito da `ipconfig.exe` anziché `localhost`.

Nel passaggio successivo, dall'interno della macchina virtuale Windows avviare l'emulatore Cosmos dalla riga di comando usando le opzioni seguenti.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

Infine, è necessario importare il certificato della CA dell'emulatore nell'ambiente Linux o Mac.

### <a name="linux"></a>Linux

Se si usa Linux, .NET si basa su OpenSSL per eseguire la convalida:

1. [Esportare il certificato nel formato PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (PFX è disponibile quando si sceglie di esportare la chiave privata). 

1. Copiare il file PFX nell'ambiente Linux.

1. Convertire il file PFX in un file CRT

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Copiare il file CRT nella cartella che contiene i certificati personalizzati nella distribuzione Linux. In genere, nelle distribuzioni Debian si trova in `/usr/local/share/ca-certificates/`.

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Aggiornare i certificati della CA, che aggiorneranno la cartella `/etc/ssl/certs/`.

   ```bash
   update-ca-certificates
   ```

### <a name="mac-os"></a>Mac OS

Se si usa Mac, seguire questa procedura:

1. [Esportare il certificato nel formato PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (PFX è disponibile quando si sceglie di esportare la chiave privata).

1. Copiare il file PFX nell'ambiente Mac.

1. Aprire l'applicazione *Keychain Access* (Accesso keychain) e importare il file PFX.

1. Aprire l'elenco dei certificati e identificare quello con il nome `localhost`.

1. Aprire il menu di scelta rapida per quell'elemento specifico, scegliere *Get Item*(Ottieni elemento) e nell'opzione *Trust* > *When using this certificate* (Attendibilità > Quando si usa questo certificato) selezionare *Always Trust* (Considera sempre attendibile). 

   ![Aprire il menu di scelta rapida per quell'elemento specifico, scegliere Get Item (Ottieni elemento) e nell'opzione Trust - When using this certificate (Attendibilità - Quando si usa questo certificato) selezionare Always Trust (Considera sempre attendibile)](./media/local-emulator/mac-trust-certificate.png)

Dopo aver eseguito questi passaggi, l'ambiente considererà attendibile il certificato usato dall'emulatore quando si effettua la connessione all'indirizzo IP esposto da `/AllowNetworkAccess`.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Usare i suggerimenti seguenti per risolvere i problemi relativi all'emulatore Azure Cosmos:

- Se è stata installata una nuova versione dell'emulatore e si verificano errori, provare a ripristinare i dati. A tale scopo, è possibile fare clic con il pulsante destro del mouse sull'icona dell'emulatore Azure Cosmos nell'area di notifica e quindi scegliere Reset Data (Reimposta dati). Se gli errori non scompaiono, è possibile disinstallare l'emulatore e tutte le relative versioni precedenti eventualmente trovate, rimuovere la directory "C:\Programmi\Azure Cosmos DB Emulator" e reinstallare l'emulatore. Per istruzioni, vedere [Disinstallare l'emulatore locale](#uninstall).

- In caso di arresto anomalo dell'emulatore Azure Cosmos, raccogliere i file di dump dalla cartella '%LOCALAPPDATA%\CrashDumps', comprimerli e aprire un ticket di supporto nel [portale di Azure](https://portal.azure.com).

- Se si verificano arresti anomali in `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, potrebbe trattarsi di un sintomo della presenza di contatori delle prestazioni danneggiati. L'esecuzione del comando seguente dal prompt dei comandi come amministratore in genere consente di risolvere il problema:

  ```cmd
  lodctr /R
   ```

- In caso di problemi di connettività, [raccogliere i file di traccia](#trace-files), comprimerli e aprire un ticket di supporto nel [portale di Azure](https://portal.azure.com).

- Se si riceve un messaggio di tipo **Servizio non disponibile**, è possibile che l'emulatore non riesca a inizializzare lo stack di rete. Controllare per verificare se è stato installato il client sicuro Pulse o il client di rete Juniper, perché è possibile che i rispettivi driver del filtro di rete provochino il problema. La disinstallazione dei driver filtro di rete di terze parti consente in genere di risolvere il problema. In alternativa, avviare l'emulatore con /DisableRIO. In questo modo, per la comunicazione di rete dell'emulatore, si passerà ai Winsock normali. 

- Mentre l'emulatore è in esecuzione, se il computer passa in modalità sospensione o esegue eventuali aggiornamenti del sistema operativo, potrebbe essere visualizzato il messaggio **Il servizio non è attualmente disponibile**. Reimpostare i dati dell'emulatore facendo clic con il pulsante destro del mouse sull'icona visualizzata nell'area di notifica di Windows e scegliendo **Reset Data** (Reimposta dati).

### <a name="collect-trace-files"></a><a id="trace-files"></a>Raccogliere i file di traccia

Per raccogliere le tracce di debug, eseguire i comandi seguenti da un prompt dei comandi amministrativi:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown`. Verificare l'area di notifica per assicurarsi che il programma sia stato arrestato. Potrebbe essere necessario qualche minuto. È anche possibile fare semplicemente clic su **Esci** nell'interfaccia utente dell'emulatore Azure Cosmos.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Riprodurre il problema. Se Esplora dati non funziona, è sufficiente attendere qualche secondo per l'apertura del browser e il rilevamento dell'errore.
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. Passare a `%ProgramFiles%\Azure Cosmos DB Emulator` e individuare il file docdbemulator_000001.etl.
8. Aprire un ticket di supporto nel [portale di Azure](https://portal.azure.com) e includere il file ETL insieme ai passaggi di ripetizione.

### <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Disinstallare l'emulatore locale

1. Chiudere tutte le istanze aperte dell'emulatore locale facendo clic con il pulsante destro del mouse sull'icona dell'emulatore Azure Cosmos nell'area di notifica e quindi scegliendo Esci. La chiusura di tutte le istanze può richiedere qualche minuto.
2. Nella casella di ricerca di Windows digitare **Apps & features** (App e funzionalità) e fare clic sul risultato **Apps & features (System settings)** (App e funzionalità - Impostazioni di sistema).
3. Nell'elenco di app scorrere fino a **Azure Cosmos DB Emulator** (Emulatore di Azure Cosmos DB), selezionarla, fare clic su **Disinstalla**, confermare e quindi fare clic nuovamente su **Disinstalla**.
4. Quando l'app viene disinstallata, passare a `%LOCALAPPDATA%\CosmosDBEmulator` ed eliminare la cartella.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare l'emulatore locale per lo sviluppo locale gratuito. È ora possibile passare all'esercitazione successiva e apprendere come esportare i certificati TLS/SSL dell'emulatore.

> [!div class="nextstepaction"]
> [Esportare i certificati dell'emulatore Azure Cosmos](local-emulator-export-ssl-certificates.md)
