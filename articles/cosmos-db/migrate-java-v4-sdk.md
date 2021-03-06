---
title: Eseguire la migrazione dell'applicazione per usare Azure Cosmos DB Java SDK v4 (com.azure.cosmos)
description: Informazioni su come aggiornare l'applicazione Java esistente basata sul precedente Azure Cosmos DB Java SDK al nuovo Java SDK 4.0 (pacchetto com.azure.cosmos) per l'API Core (SQL).
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.reviewer: sngun
ms.openlocfilehash: 92a9abec36bd75c594c67843286bf8fa067d7dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658538"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Eseguire la migrazione dell'applicazione per usare Azure Cosmos DB Java SDK v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> Per altre informazioni su questo SDK, vedere le [note sulla versione](sql-api-sdk-java-v4.md) di Azure Cosmos DB Java SDK v4, il [repository Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), i [suggerimenti sulle prestazioni ](performance-tips-java-sdk-v4-sql.md) di Azure Cosmos DB Java SDK v4 e la [guida alla risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md) di Azure Cosmos DB Java SDK v4.
>

Questo articolo illustra come aggiornare l'applicazione Java esistente basata sul precedente Azure Cosmos DB Java SDK al nuovo Azure Cosmos DB Java SDK 4.0 per API Core (SQL). Azure Cosmos DB Java SDK v4 corrisponde al pacchetto `com.azure.cosmos`. È possibile usare le istruzioni riportate in questo documento si esegue la migrazione dell'applicazione da una delle edizioni di Azure Cosmos DB Java SDK seguenti: 

* Sync Java SDK 2.x.x
* Async Java SDK 2.x.x
* Java SDK 3.x.x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Mapping di Azure Cosmos DB Java SDK e dei pacchetti

La tabella seguente elenca i diversi Azure Cosmos DB Java SDK, il nome del pacchetto corrispondente e informazioni sulla versione:

| SDK per Java| Data di rilascio | API in bundle   | Jar Maven  | Nome pacchetto Java  |Informazioni di riferimento sulle API   | Note sulla versione  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2.x.x  | Giugno 2018    | Async(RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Note sulla versione](sql-api-sdk-async-java.md) |
| Sync 2.x.x     | Settembre 2018    | Sincronizzazione   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Note sulla versione](sql-api-sdk-java.md)  |
| 3.x.x    | Luglio 2019    | Async(Reactor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4.0   | Giugno 2020   | Async(Reactor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | [API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1/index.html)  |

## <a name="sdk-level-implementation-changes"></a>Modifiche di implementazione a livello di SDK

Di seguito sono illustrate le principali differenze di implementazione tra i vari SDK:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava è stato sostituito con Reactor in Azure Cosmos DB Java SDK versioni 3.x.x e 4.0

Se non si ha familiarità con la programmazione asincrona o reattiva, vedere la [guida introduttiva ai modelli di Reactor](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) per un'introduzione alla programmazione asincrona e a Project Reactor. Questa guida può essere utile se in passato si è usata l'API Sync per Azure Cosmos DB Sync Java SDK 2.x.x o Azure Cosmos DB Java SDK 3.x.x.

Se si usa Azure Cosmos DB Async Java SDK 2.x.x e si prevede di eseguire la migrazione a 4.0 SDK, vedere la [guida relativa al confronto tra Reactor e RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) per istruzioni su come convertire codice RxJava per poter usare Reactor.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 integra la modalità di connettività diretta sia nell'API asincrona che nell'API sincrona

Se si usa Azure Cosmos DB Sync Java SDK 2.x.x, tenere presente che la modalità di connessione diretta basata su TCP (anziché HTTP) viene implementata in Azure Cosmos DB Java SDK 4.0 sia per l'API asincrona che per l'API sincrona.

## <a name="api-level-changes"></a>Modifiche al livello di API

Di seguito sono illustrate le modifiche a livello di API introdotte in Azure Cosmos DB Java SDK 4.x.x rispetto ai precedenti SDK (Java SDK 3.x.x, Async Java SDK 2.x.x e Sync Java SDK 2.x.x):

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="Convenzioni di denominazione di Azure Cosmos DB Java SDK":::

* Azure Cosmos DB Java SDK 3.x.x e 4.0 fanno riferimento alle risorse client come `Cosmos<resourceName>`. Ad esempio `CosmosClient`, `CosmosDatabase`, `CosmosContainer`. Nella versione 2.x.x, invece, Azure Cosmos DB Java SDK non aveva uno schema di denominazione uniforme.

* Azure Cosmos DB Java SDK 3.x.x e 4.0 offrono sia API asincrone sia API sincrone.

  * **Java SDK 4.0**: tutte le classi appartengono all'API sincrona, a meno che nel nome della classe non sia stato aggiunto `Async` dopo `Cosmos`.

  * **Java SDK 3.x.x**: tutte le classi appartengono all'API asincrona, a meno che nel nome della classe non sia stato aggiunto `Async` dopo `Cosmos`.

  * **Async Java SDK 2.x.x**: i nomi delle classi sono simili a Sync Java SDK 2.x.x, ma iniziano con *Async*.

### <a name="hierarchical-api-structure"></a>Struttura dell'API gerarchica

Azure Cosmos DB Java SDK 4.0 e 3.x.x introducono una struttura dell'API gerarchica che organizza i client, i database e i contenitori in modo annidato, come illustrato nel frammento di codice 4.0 SDK seguente:

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

Nella versione 2.x.x di Azure Cosmos DB Java SDK, tutte le operazioni su risorse e documenti vengono eseguite tramite l'istanza del client.

### <a name="representing-documents"></a>Rappresentazione di documenti

In Azure Cosmos DB Java SDK 4.0, i POJO personalizzati e `JsonNodes` sono le due opzioni disponibili per leggere e scrivere documenti da Azure Cosmos DB.

In Azure Cosmos DB Java SDK 3.x.x, l'oggetto `CosmosItemProperties` viene esposto dall'API pubblica e restituito come rappresentazione del documento. Questa classe non è più esposta pubblicamente nella versione 4.0.

### <a name="imports"></a>Importazioni

* I pacchetti Azure Cosmos DB Java SDK 4.0 iniziano con `com.azure.cosmos`
* I pacchetti Azure Cosmos DB Java SDK 3.x.x iniziano con `com.azure.data.cosmos`
* Azure Cosmos DB i pacchetti dell'API di sincronizzazione Java SDK 2. x iniziano con `com.microsoft.azure.documentdb`

* Azure Cosmos DB Java SDK 4.0 inserisce più classi in un pacchetto annidato `com.azure.cosmos.models`. Alcuni di questi pacchetti includono:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Tutti i pacchetti precedenti si applicano anche all'API asincrona
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` ...e così via.

### <a name="accessors"></a>Funzioni di accesso

Azure Cosmos DB Java SDK 4.0 espone i metodi `get` e `set` per accedere ai membri dell'istanza. L'istanza `CosmosContainer`, ad esempio, include i metodi `container.getId()` e `container.setId()`.

Questo è un aspetto che differisce da Azure Cosmos DB Java SDK 3.x.x, che espone un'interfaccia Fluent. Un'istanza `CosmosSyncContainer`, ad esempio, include `container.id()`, che viene sottoposto a overload per ottenere o impostare il valore di `id`.

## <a name="code-snippet-comparisons"></a>Confronti tra frammenti di codice

### <a name="create-resources"></a>Creare le risorse

Il frammento di codice seguente mostra le differenze nella modalità di creazione delle risorse tra le API asincrone 4,0, 3. x. x e le API di sincronizzazione 2. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona per Java SDK 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API asincrona per Java SDK 3.x.x](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[API di sincronizzazione Java SDK 2. x](#tab/java-v2-sync)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.GetDefault();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));

//  Create document client
//  <CreateDocumentClient>
client = new DocumentClient("your.hostname", "your.masterkey", defaultPolicy, ConsistencyLevel.Eventual)

// Create database with specified name
Database databaseDefinition = new Database();
databaseDefinition.setId("YourDatabaseName");
ResourceResponse<Database> databaseResourceResponse = client.createDatabase(databaseDefinition, new RequestOptions());

// Read database with specified name
String databaseLink = "dbs/YourDatabaseName";
databaseResourceResponse = client.readDatabase(databaseLink, new RequestOptions());
Database database = databaseResourceResponse.getResource();

// Create container with specified name
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-operations"></a>Operazioni sugli elementi

Il frammento di codice seguente illustra le differenze nella modalità di esecuzione delle operazioni sugli elementi tra le API asincrone 4,0, 3. x. x e le API di sincronizzazione 2. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona per Java SDK 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API asincrona per Java SDK 3.x.x](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-2xx-sync-api"></a>[API di sincronizzazione Java SDK 2. x](#tab/java-v2-sync)

```java
//  Container is created. Generate documents to insert.
Document document = new Document();
document.setId("YourDocumentId");
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

### <a name="indexing"></a>Indicizzazione

Il frammento di codice seguente illustra le differenze nella modalità di creazione dell'indicizzazione tra le API asincrone 4,0, 3. x. x e le API di sincronizzazione 2. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona per Java SDK 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API asincrona per Java SDK 3.x.x](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.includedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```

# <a name="java-sdk-2xx-sync-api"></a>[API di sincronizzazione Java SDK 2. x](#tab/java-v2-sync)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="stored-procedures"></a>Stored procedure

Il frammento di codice seguente illustra le differenze nella modalità di creazione delle stored procedure tra le API asincrone 4,0, 3. x. x e le API di sincronizzazione 2. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona per Java SDK 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API asincrona per Java SDK 3.x.x](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-2xx-sync-api"></a>[API di sincronizzazione Java SDK 2. x](#tab/java-v2-sync)

```java
logger.info("Creating stored procedure...\n");

String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client.createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
                                        .getResource();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null);
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```
---

### <a name="change-feed"></a>Feed delle modifiche

Il frammento di codice seguente mostra le differenze nella modalità di esecuzione delle operazioni sui feed di modifiche tra le API asincrone 3.x.x e 4.0:

# <a name="java-sdk-40-async-api"></a>[API asincrona per Java SDK 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API asincrona per Java SDK 3.x.x](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[API di sincronizzazione Java SDK 2. x](#tab/java-v2-sync)

* Questa funzionalità non è supportata a partire da Java SDK v2 Sync. 
---

### <a name="container-level-time-to-livettl"></a>Durata (TTL) predefinita a livello di contenitore

Il frammento di codice seguente illustra le differenze in come creare una durata per i dati nel contenitore usando le API asincrone 4,0, 3. x. x e le API di sincronizzazione 2. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona per Java SDK 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API asincrona per Java SDK 3.x.x](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

# <a name="java-sdk-2xx-sync-api"></a>[API di sincronizzazione Java SDK 2. x](#tab/java-v2-sync)

```java
DocumentCollection documentCollection;

// Create a new container with TTL enabled with default expiration value
documentCollection.setDefaultTimeToLive(90 * 60 * 60 * 24);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-level-time-to-livettl"></a>Durata (TTL) predefinita a livello di elemento

Il frammento di codice seguente illustra le differenze in come creare una durata per un elemento usando le API asincrone 4,0, 3. x. x e le API di sincronizzazione 2. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona per Java SDK 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API asincrona per Java SDK 3.x.x](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-2xx-sync-api"></a>[API di sincronizzazione Java SDK 2. x](#tab/java-v2-sync)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

## <a name="next-steps"></a>Passaggi successivi

* [Compilare un'app Java](create-sql-api-java.md) per gestire i dati dell'API SQL di Azure Cosmos DB tramite V4 SDK
* Informazioni sugli [SDK Java basati su Reactor](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)
* Informazioni sulla conversione di codice asincrono RxJava in codice asincrono Reactor con la [guida relativa al confronto tra Reactor e RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md)
