---
title: Provisioning della velocità effettiva di scalabilità automatica in Azure Cosmos DB API SQL
description: Informazioni su come effettuare il provisioning della velocità effettiva di scalabilità automatica a livello di contenitore e database in Azure Cosmos DB API SQL usando portale di Azure, CLI, PowerShell e vari altri SDK.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 52904296df77d9097a6180345388e8e702e2bca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357626"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db---sql-api"></a>Eseguire il provisioning della velocità effettiva di ridimensionamento automatico per database o contenitore nell'API Azure Cosmos DB-SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo illustra come eseguire il provisioning della velocità effettiva di scalabilità automatica in un database o un contenitore (raccolta, grafo o tabella) in Azure Cosmos DB API SQL. È possibile abilitare la scalabilità automatica per un singolo contenitore oppure effettuare il provisioning della velocità effettiva per la scalabilità automatica a livello di database e condividerla tra tutti i contenitori nel database.

Se si usa un'API diversa, vedere l'articolo relativo alle API [per MongoDB](how-to-provision-throughput-mongodb.md), [API Cassandra](how-to-provision-throughput-cassandra.md), articoli sull' [API Gremlin](how-to-provision-throughput-gremlin.md) per eseguire il provisioning della velocità effettiva.

## <a name="azure-portal"></a>Portale di Azure

### <a name="create-new-database-or-container-with-autoscale"></a>Creare un nuovo database o contenitore con scalabilità automatica

1. Accedere al [portale di Azure](https://portal.azure.com) o a [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Passare all'account Azure Cosmos DB e aprire la scheda **Esplora dati**.

1. Selezionare **Nuovo contenitore**. Immettere un nome per il database, il contenitore e una chiave di partizione. In **Velocità effettiva** selezionare l'opzione **Scalabilità automatica** e impostare la [velocità effettiva massima (UR/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) per il dimensionamento del database o del contenitore.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="Creazione di un contenitore e configurazione del provisioning della velocità effettiva per la scalabilità automatica":::

1. Selezionare **OK**.

Per effettuare il provisioning della scalabilità automatica per il database con velocità effettiva condivisa, selezionare l'opzione **Provision database throughput** (Effettua il provisioning della velocità effettiva del database) durante la creazione di un nuovo database. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Abilitare la scalabilità automatica per un database o un contenitore esistente

> [!IMPORTANT]
> Nella versione corrente per eseguire la migrazione dal provisioning della velocità effettiva per la scalabilità automatica a quello standard (manuale), è possibile usare solo il portale di Azure. 

1. Accedere al [portale di Azure](https://portal.azure.com) o a [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Passare all'account Azure Cosmos DB e aprire la scheda **Esplora dati**.

1. Selezionare **Scalabilità e impostazioni** per il contenitore oppure **Dimensiona** per il database.

1. In **Dimensiona** selezionare l'opzione **Scalabilità automatica** e fare clic su **Salva**.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="Abilitazione della scalabilità automatica per un contenitore esistente":::

> [!NOTE]
> Quando si abilita la scalabilità automatica per un database o un contenitore esistente, il valore iniziale per il numero massimo di UR/s è determinato dal sistema, in base alle impostazioni correnti del provisioning della velocità effettiva manuale e allo spazio di archiviazione. Al termine dell'operazione, se necessario, è possibile modificare il numero massimo di UR/s. [Altre informazioni.](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk"></a>SDK di Azure Cosmos DB .NET V3

Usare la [versione 3.9 o successiva](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) di Azure Cosmos DB .NET SDK per API SQL per gestire le risorse con scalabilità automatica. 

> [!IMPORTANT]
> È possibile usare .NET SDK per creare nuove risorse con scalabilità automatica. L'SDK non supporta la migrazione tra scalabilità automatica e velocità effettiva standard (manuale). Lo scenario di migrazione è attualmente supportato solo nel portale di Azure. 

### <a name="create-database-with-shared-throughput"></a>Creare il database con velocità effettiva condivisa

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Creare il contenitore con velocità effettiva dedicata

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>Leggere la velocità effettiva corrente (UR/s)

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>Modificare la velocità effettiva massima per la scalabilità automatica (UR/s)

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk"></a>Azure Cosmos DB Java v4 SDK

È possibile usare la [versione 4.0 o successiva](https://mvnrepository.com/artifact/com.azure/azure-cosmos) di Azure Cosmos DB Java SDK per API SQL per gestire le risorse con scalabilità automatica.

> [!IMPORTANT]
> È possibile usare Java SDK per creare nuove risorse con scalabilità automatica. L'SDK non supporta la migrazione tra scalabilità automatica e velocità effettiva standard (manuale). Lo scenario di migrazione è attualmente supportato solo nel portale di Azure.

### <a name="create-database-with-shared-throughput"></a>Creare il database con velocità effettiva condivisa

#### <a name="async"></a>[Asincrono](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[Sincronizza](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Creare il contenitore con velocità effettiva dedicata

#### <a name="async"></a>[Asincrono](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[Sincronizza](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>Leggere la velocità effettiva corrente (UR/s)

#### <a name="async"></a>[Asincrono](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[Sincrono](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>Modificare la velocità effettiva massima per la scalabilità automatica (UR/s)

#### <a name="async"></a>[Asincrono](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[Sincronizza](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

È possibile usare i modelli di Azure Resource Manager per eseguire il provisioning della velocità effettiva di ridimensionamento automatico per tutte le API Azure Cosmos DB di database o a livello di contenitore Per esempi, vedere [Azure Resource Manager Templates for Azure Cosmos DB](./templates-samples-sql.md) .

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure può essere usata per eseguire il provisioning della velocità effettiva di ridimensionamento automatico in un database o in risorse a livello Azure Cosmos DB di Per esempi, vedere [esempi dell'interfaccia della riga di comando di Azure per Azure Cosmos DB](cli-samples.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell può essere usato per eseguire il provisioning della velocità effettiva di ridimensionamento automatico in un database o in risorse a livello di contenitore per tutte le API Azure Cosmos DB Per esempi, vedere [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples.md).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [vantaggi del provisioning della velocità effettiva con la scalabilità automatica](provision-throughput-autoscale.md#benefits-of-autoscale).
* Informazioni su come [scegliere tra la velocità effettiva manuale e per la scalabilità automatica](how-to-choose-offer.md).
* Vedere le [domande frequenti sulla scalabilità automatica](autoscale-faq.md).
