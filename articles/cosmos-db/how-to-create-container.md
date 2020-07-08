---
title: Creare un contenitore in Azure Cosmos DB
description: Informazioni su come creare un contenitore in Azure Cosmos DB usando portale di Azure, .NET, Java, Python, Node.js e altri SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/24/2020
ms.author: mjbrown
ms.openlocfilehash: 22c51497a9c9a331f1337134fbaf7c781b9c8ba7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390891"
---
# <a name="create-an-azure-cosmos-container"></a>Creare un contenitore in Azure Cosmos

Questo articolo illustra i diversi modi disponibili per creare un contenitore di Azure Cosmos (raccolta, tabella o grafo). È possibile usare il portale di Azure, l'interfaccia della riga di comando di Azure o gli SDK supportati. Questo articolo spiega come creare un contenitore, specificare la chiave di partizione ed effettuare il provisioning della velocità effettiva.

> [!NOTE]
> Quando si creano i contenitori, assicurarsi di non creare due contenitori con lo stesso nome ma con una combinazione diversa di maiuscole/minuscole. Questo perché alcune parti della piattaforma Azure non fanno distinzione tra maiuscole e minuscole e ciò può comportare confusione/conflitti dei dati di telemetria e delle azioni sui contenitori con tali nomi.

## <a name="create-a-container-using-azure-portal"></a>Creare un contenitore tramite il portale di Azure

### <a name="sql-api"></a><a id="portal-sql"></a>API SQL

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-sql-api-dotnet.md#create-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuovo contenitore**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente.
   * Immettere un ID contenitore.
   * Immettere una chiave di partizione.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Screenshot del riquadro Esplora dati con il nuovo contenitore evidenziato":::

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>API Azure Cosmos DB per MongoDB

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)oppure selezionare un account esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuovo contenitore**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente.
   * Immettere un ID contenitore.
   * Immettere una chiave di partizione.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Screenshot dell'API Azure Cosmos DB per MongoDB, finestra di dialogo Aggiungi contenitore":::

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>API Cassandra

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account)oppure selezionare un account esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuova tabella**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo keyspace o usarne uno esistente.
   * Immettere un nome per la tabella.
   * Immettere le proprietà e specificare una chiave primaria.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Screenshot dell'API Cassandra con la finestra di dialogo Aggiungi tabella":::

> [!NOTE]
> Per l'API Cassandra, la chiave primaria viene usata come chiave di partizione.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>API Gremlin

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-graph-dotnet.md#create-a-database-account)oppure selezionare un account esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuovo grafico**. Specificare quindi i dettagli seguenti:

   * Indicare se si intende creare un nuovo database o usarne uno esistente.
   * Immettere un ID grafo.
   * Selezionare una capacità di archiviazione **Illimitata**.
   * Immettere una chiave di partizione per i vertici.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Screenshot dell'API Gremlin con la finestra di dialogo Aggiungi Graph":::

### <a name="table-api"></a><a id="portal-table"></a>API di tabella

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-table-dotnet.md#create-a-database-account)oppure selezionare un account esistente.

1. Aprire il riquadro **Esplora dati** e selezionare **nuova tabella**. Specificare quindi i dettagli seguenti:

   * Immettere un ID tabella.
   * Immettere una velocità effettiva da sottoporre a provisioning (ad esempio, 1000 UR).
   * Selezionare **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Screenshot dell'API Tabella con la finestra di dialogo Aggiungi tabella":::

> [!Note]
> Per l'API Tabella, la chiave di partizione viene specificata ogni volta che si aggiunge una nuova riga.

## <a name="create-a-container-using-azure-cli"></a>Creare un contenitore usando l'interfaccia della riga di comando di Azure<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

I collegamenti seguenti illustrano come creare risorse contenitore per Azure Cosmos DB usando l'interfaccia della riga di comando di Azure.

Per un elenco di tutti gli esempi dell'interfaccia della riga di comando di Azure in tutte le API di Azure Cosmos DB, vedere API [SQL](cli-samples.md), [API Cassandra](cli-samples-cassandra.md), [API MongoDB](cli-samples-mongodb.md), [API Gremlin](cli-samples-gremlin.md)e [API tabella](cli-samples-table.md)

* [Creare un contenitore con l'interfaccia della riga di comando di Azure](manage-with-cli.md#create-a-container)
* [Creare una raccolta per Azure Cosmos DB API MongoDB con l'interfaccia della riga di comando di Azure](./scripts/cli/mongodb/create.md)
* [Creare una tabella Cassandra con interfaccia della riga di comando di Azure](./scripts/cli/cassandra/create.md)
* [Creare un grafo Gremlin con interfaccia della riga di comando di Azure](./scripts/cli/gremlin/create.md)
* [Creare una tabella di API Tabella con l'interfaccia della riga di comando](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershella-idps-mongodba-idps-gremlin"></a>Creare un contenitore con PowerShell<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

I collegamenti seguenti illustrano come creare risorse contenitore per Azure Cosmos DB usando PowerShell.

Per un elenco di tutti gli esempi dell'interfaccia della riga di comando di Azure in tutte le API di Azure Cosmos DB, vedere API [SQL](powershell-samples-sql.md), [API Cassandra](powershell-samples-cassandra.md), [API MongoDB](powershell-samples-mongodb.md), [API Gremlin](powershell-samples-gremlin.md)e [API tabella](powershell-samples-table.md)

* [Creare un contenitore con PowerShell](manage-with-powershell.md#create-container)
* [Creare una raccolta per Azure Cosmos DB API MongoDB con PowerShell](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [Creare una tabella Cassandra con PowerShell](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [Creare un grafo Gremlin con PowerShell](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [Creare una tabella API Tabella con PowerShell](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>Creare un contenitore tramite .NET SDK

Se si verifica un'eccezione di timeout durante la creazione di una raccolta, eseguire un'operazione di lettura per verificare se la raccolta è stata creata correttamente. L'operazione di lettura genera un'eccezione fino al completamento dell'operazione di creazione della raccolta. Per l'elenco dei codici di stato supportati dall'operazione di creazione, vedere l'articolo relativo ai [codici di stato HTTP per Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>API SQL e API Gremlin

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>API Azure Cosmos DB per MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Il protocollo di collegamento MongoDB non comprende il concetto di [unità richiesta](request-units.md). Per creare una nuova raccolta con velocità effettiva con provisioning, usare il portale di Azure o gli SDK di Cosmos DB per l'API SQL.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Passaggi successivi

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](account-overview.md)
