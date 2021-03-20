---
title: 'Esercitazione: Strumento di migrazione del database per Azure Cosmos DB'
description: "Esercitazione: Informazioni sull'uso degli strumenti open source di migrazione dati di Azure Cosmos DB per importare dati in Azure Cosmos DB da varie origini, tra cui file JSON, CSV, MongoDB, SQL Server, archiviazione tabelle e Amazon DynamoDB. Conversione da CSV a JSON."
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: dech
ms.openlocfilehash: 1cee4d2ad1bc7f362a045a5991624ec43521b8d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341649"
---
# <a name="tutorial-use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Esercitazione: Usare l'utilità di migrazione dati per la migrazione dei dati in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questa esercitazione fornisce istruzioni sull'uso dell'Utilità di migrazione dati di Azure Cosmos DB, che consente di importare dati da diverse origini nei contenitori e nelle tabelle di Azure Cosmos. È possibile importare da file JSON, file CSV, SQL, MongoDB, archiviazione tabelle di Azure, Amazon DynamoDB e anche da raccolte di API SQL per Azure Cosmos DB. È possibile eseguire la migrazione di tali dati in raccolte e tabelle da usare con Azure Cosmos DB. L'utilità di migrazione dati può essere usata anche per la migrazione da una raccolta con singola partizione a una raccolta con più partizioni per l'API SQL.

> [!NOTE]
> L'utilità di migrazione dati di Azure Cosmos DB è uno strumento open source da usare per migrazioni di piccole dimensioni. Per le migrazioni più grandi, vedere la [guida per l'inserimento di dati](cosmosdb-migrationchoices.md).

* **[API SQL](./introduction.md)** : è possibile usare una delle opzioni di origine disponibili nell'utilità di migrazione dati per importare dati su scala ridotta. [Informazioni sulle opzioni di migrazione per l'importazione di dati su larga scala](cosmosdb-migrationchoices.md).
* **[API Tabella](table-introduction.md)** : è possibile usare l'utilità di migrazione dati o [AzCopy](table-import.md#migrate-data-by-using-azcopy) per importare dati. Per altre informazioni, vedere [Importare dati da usare con l'API Tabelle di Azure Cosmos DB](table-import.md).
* **[API di Azure Cosmos DB per MongoDB](mongodb-introduction.md)** : l'utilità di migrazione dati non supporta l'API di Azure Cosmos DB per MongoDB né come origine né come destinazione. Per istruzioni su come eseguire la migrazione dei dati in o da raccolte in Azure Cosmos DB, vedere [Come eseguire la migrazione dei dati di MongoDB a un database Cosmos con l'API di Azure Cosmos DB per MongoDB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json). È comunque possibile usare l'utilità di migrazione dati per esportare i dati da MongoDB alle raccolte di API SQL di Azure Cosmos DB per l'uso con l'API SQL.
* **[API Cassandra](graph-introduction.md)** : l'utilità di migrazione dati non è uno strumento di importazione attualmente supportato per gli account dell'API Cassandra. [Informazioni sulle opzioni di migrazione per l'importazione nell'API Cassandra](cosmosdb-migrationchoices.md#azure-cosmos-db-cassandra-api)
* **[API Gremlin](graph-introduction.md)** : l'utilità di migrazione dati non è uno strumento di importazione attualmente supportato per gli account dell'API Gremlin. [Informazioni sulle opzioni di migrazione per l'importazione nell'API Gremlin](cosmosdb-migrationchoices.md#other-apis) 

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Installazione dello strumento di migrazione dati
> * Importazione di dati da diverse origini dati
> * Esportazione da Azure Cosmos DB a JSON

## <a name="prerequisites"></a><a id="Prerequisites"></a>Prerequisiti

Prima di seguire le istruzioni di questo articolo, assicurarsi di eseguire i passaggi seguenti:

* **Installare** [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) o versione successiva.

* **Aumentare la velocità effettiva:** la durata della migrazione dei dati dipende dalla velocità effettiva configurata per una singola raccolta o un set di raccolte. Assicurarsi di aumentare la velocità effettiva per le migrazioni dei dati di dimensioni più grandi. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi. Per altre informazioni sull'aumento della velocità effettiva nel portale di Azure, vedere [livelli di prestazioni](performance-levels.md) e [piani tariffari](https://azure.microsoft.com/pricing/details/cosmos-db/) in Azure Cosmos DB.

* **Creare risorse di Azure Cosmos DB:** prima di iniziare la migrazione dei dati, creare tutte le raccolte dal portale di Azure. Per eseguire la migrazione a un account Azure Cosmos DB con velocità effettiva a livello di database, fornire una chiave di partizione quando si creano i contenitori Azure Cosmos.

> [!IMPORTANT]
> Per assicurarsi che lo strumento di migrazione dei dati usi Transport Layer Security (TLS) 1.2 per la connessione agli account Azure Cosmos, usare la versione 4.7 di .NET Framework o seguire le istruzioni disponibili in [questo articolo](/dotnet/framework/network-programming/tls).

## <a name="overview"></a><a id="Overviewl"></a>Panoramica

Lo strumento di migrazione dati è una soluzione open source che importa dati in Azure Cosmos DB da diverse origini, tra cui:

* File JSON
* MongoDB
* SQL Server
* File CSV
* Archiviazione tabelle di Azure
* DynamoDB Amazon
* hbase
* Contenitori Azure Cosmos DB

Lo strumento di importazione, anche se include un'interfaccia utente grafica (dtui.exe), può essere gestito anche dalla riga di comando (dt.exe). Infatti, un'opzione specifica consente di inviare il comando associato dopo aver configurato un'operazione di importazione nell'interfaccia utente. È possibile trasformare i dati di origine tabulari (ad esempio, file CSV o SQL Server) in modo da creare relazioni gerarchiche (documenti secondari) durante l'importazione. Continuare a leggere per scoprire di più sulle opzioni di origine, sui comandi di esempio per l'importazione da ogni origine, sulle opzioni di destinazione e sulla visualizzazione dei risultati di importazione.

> [!NOTE]
> È consigliabile usare l'utilità di migrazione di Azure Cosmos DB solo per migrazioni di piccole dimensioni. Per le migrazioni più grandi, vedere la [guida per l'inserimento di dati](cosmosdb-migrationchoices.md).

## <a name="installation"></a><a id="Install"></a>Installazione

Il codice sorgente dell'utilità di migrazione è disponibile in GitHub in [questo repository](https://github.com/azure/azure-documentdb-datamigrationtool). È possibile scaricare e compilare la soluzione in locale o [scaricare un binario precompilato](https://aka.ms/csdmtool), quindi eseguire uno degli eseguibili seguenti:

* **Dtui.exe**: versione con interfaccia grafica dello strumento
* **Dt.exe**: versione con riga di comando dello strumento

## <a name="select-data-source"></a>Selezionare l'origine dati

Dopo aver installato lo strumento, è necessario importare i dati. È possibile importare le tipologie di dati seguenti:

* [File JSON](#JSON)
* [MongoDB](#MongoDB)
* [File di esportazione MongoDB](#MongoDBExport)
* [SQL Server](#SQL)
* [File CSV](#CSV)
* [Archivio tabelle di Azure](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [BLOB](#BlobImport)
* [Contenitori Azure Cosmos](#SQLSource)
* [HBase](#HBaseSource)
* [Importazione in blocco di Azure Cosmos DB](#SQLBulkTarget)
* [Importazione di record sequenziali di Azure Cosmos DB](#SQLSeqTarget)

## <a name="import-json-files"></a><a id="JSON"></a>Importare file JSON

L'opzione dell'utilità di importazione dell'origine file JSON consente di importare uno o più file JSON di singoli documenti o file JSON contenenti ciascuno una matrice di documenti JSON. Quando si aggiungono le cartelle contenenti i file JSON da importare, è possibile eseguire una ricerca ricorsiva dei file nelle sottocartelle.

:::image type="content" source="./media/import-data/jsonsource.png" alt-text="Screenshot delle opzioni relative all'origine per i file JSON - Strumenti di migrazione del database":::

La stringa di connessione è nel formato seguente:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* `<CosmosDB Endpoint>` è l'URI dell'endpoint. È possibile ottenere questo valore dal portale di Azure. Accedere all'account Azure Cosmos. Aprire il riquadro **Overview** e copiare il valore **URI**.
* `<AccountKey>` è "Password" oppure **CHIAVE PRIMARIA**. È possibile ottenere questo valore dal portale di Azure. Accedere all'account Azure Cosmos. Aprire il riquadro **Stringhe di connessione** oppure **Chiavi**, quindi copiare la "Password" o il valore **CHIAVE PRIMARIA**.
* `<CosmosDB Database>` è il nome del database CosmosDB.

Esempio: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Usare il comando Verifica per assicurarsi che l'account di Cosmos DB specificata nel campo della stringa di connessione sia accessibile.

Ecco alcuni esempi di riga di comando per importare file JSON:

```console
#Import a single JSON file
dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory of JSON files
dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory (including sub-directories) of JSON files
dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

#Import a directory (single), directory (recursive), and individual JSON files
dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

#Import a single JSON file and partition the data across 4 collections
dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500
```

## <a name="import-from-mongodb"></a><a id="MongoDB"></a>Importare da MongoDB

> [!IMPORTANT]
> Se si esegue l'importazione in un account Cosmos configurato con l'API di Azure Cosmos DB per MongoDB, seguire queste [istruzioni](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json).

Con l'opzione dell'utilità di importazione dell'origine MongoDB è possibile importare da una singola raccolta MongoDB, di filtrare facoltativamente i documenti usando una query e di modificare la struttura di documenti usando una proiezione.  

:::image type="content" source="./media/import-data/mongodbsource.png" alt-text="Screenshot delle opzioni relative all'origine per MongoDB":::

La stringa di connessione è nel formato standard di MongoDB:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Usare il comando Verify per assicurarsi che l'istanza di MongoDB specificata nel campo della stringa di connessione sia accessibile.

Immettere il nome della raccolta da cui verranno importati i dati. Se si preferisce, si può specificare o fornire un file per una query come `{pop: {$gt:5000}}` o una proiezione come `{loc:0}` sia per filtrare che per determinare i dati da importare.

Ecco alcuni esempi di riga di comando per importare da MongoDB:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a name="import-mongodb-export-files"></a><a id="MongoDBExport"></a>Importare file di esportazione MongoDB

> [!IMPORTANT]
> Se si esegue l'importazione in un account Azure Cosmos DB con supporto per MongoDB, seguire queste [istruzioni](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json).

L'opzione dell'utilità di importazione dell'origine file JSON di esportazione MongoDB consente di importare uno o più file JSON prodotti dall'utilità mongoexport.  

:::image type="content" source="./media/import-data/mongodbexportsource.png" alt-text="Screenshot delle opzioni relative all'origine per file di esportazione MongoDB":::

Quando si aggiungono le cartelle contenenti i file JSON di esportazione MongoDB da importare, è possibile eseguire una ricerca ricorsiva dei file nelle sottocartelle.

Ecco un esempio di riga di comando per importare dai file JSON di esportazione MongoDB:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a name="import-from-sql-server"></a><a id="SQL"></a>Importare da SQL Server

L'opzione dell'utilità di importazione dell'origine SQL consente di importare da un singolo database SQL Server e, facoltativamente, di filtrare i record da importare usando una query. Inoltre, è possibile modificare la struttura di documenti specificando un separatore di annidamento, di cui si parlerà tra poco.  

:::image type="content" source="./media/import-data/sqlexportsource.png" alt-text="Screenshot delle opzioni relative all'origine per SQL - Strumenti di migrazione del database":::

Il formato della stringa di connessione è il formato della stringa di connessione SQL standard.

> [!NOTE]
> Usare il comando Verify per assicurarsi che l'istanza di SQL Server specificata nel campo della stringa di connessione sia accessibile.

La proprietà del separatore di annidamento viene usata per creare relazioni gerarchiche (documenti secondari) durante l'importazione. Considerare la query SQL seguente:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

Che restituisce i risultati (parziali) seguenti:

:::image type="content" source="./media/import-data/sqlqueryresults.png" alt-text="Screenshot dei risultati della query SQL":::

Si notino gli alias come Address.AddressType e Address.Location.StateProvinceName. Specificando un separatore di annidamento '.', lo strumento di importazione crea i documenti secondari Address e Address.Location durante l'importazione. Ecco un esempio di documento risultante in Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Ecco alcuni esempi di riga di comando per importare da SQL Server:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a name="import-csv-files-and-convert-csv-to-json"></a><a id="CSV"></a>Importare file CSV ed eseguire la conversione da CSV a JSON

L'opzione dell'utilità di importazione dell'origine file CSV consente di importare uno o più file CSV. Quando si aggiungono le cartelle contenenti i file CSV da importare, è possibile eseguire una ricerca ricorsiva dei file nelle sottocartelle.

:::image type="content" source="media/import-data/csvsource.png" alt-text="Screenshot delle opzioni relative all'origine CSV - Da CSV a JSON":::

Come per l'origine SQL, la proprietà del separatore di annidamento può essere usata per creare relazioni gerarchiche (documenti secondari) durante l'importazione. Si consideri la seguente riga di intestazione CSV e le righe di dati:

:::image type="content" source="./media/import-data/csvsample.png" alt-text="Screenshot dei record di esempio CSV - Da CSV a JSON":::

Si notino gli alias come DomainInfo.Domain_Name e RedirectInfo.Redirecting. Specificando un separatore di annidamento '.', lo strumento di importazione creerà i documenti secondari DomainInfo e RedirectInfo durante l'importazione. Ecco un esempio di documento risultante in Azure Cosmos DB:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "https:\//www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the Stati Uniti", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

Lo strumento di importazione prova a dedurre le informazioni sul tipo per i valori non racchiusi tra virgolette nei file CSV. I valori tra virgolette vengono sempre considerati come stringhe.  I tipi vengono identificati nell'ordine seguente: tipo numerico, datetime, booleano.  

Esistono altri due aspetti da considerare per l'importazione CSV:

1. Per impostazione predefinita, nei valori non racchiusi tra virgolette vengono sempre rimossi spazi e tabulazioni, mentre i valori tra virgolette vengono mantenuti così come sono. È possibile eseguire l'override di questo comportamento con la casella di controllo Taglia valori tra virgolette o con l'opzione della riga di comando /s.TrimQuoted.
2. Per impostazione predefinita, un valore Null non racchiuso tra virgolette viene considerato come valore Null. È possibile eseguire l'override di questo comportamento (ad esempio considerando un valore Null non racchiuso tra virgolette come stringa "Null") con la casella di controllo Considera valore NULL non racchiuso tra virgolette come stringa o con l'opzione della riga di comando /s.NoUnquotedNulls.

Ecco un esempio di riga di comando per l'importazione CSV:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a name="import-from-azure-table-storage"></a><a id="AzureTableSource"></a>Importare da Archiviazione tabelle di Azure

L'opzione dell'utilità di importazione dell'origine Archiviazione tabelle di Azure consente di importare da una singola tabella di Archiviazione tabelle di Azure. È facoltativamente possibile filtrare le entità tabelle da importare.

È possibile restituire come output i dati che sono stati importati da Archiviazione tabelle di Azure nelle tabelle e nelle entità di Azure Cosmos DB per l'uso con l'API Tabella. I dati importati possono anche essere restituiti in raccolte e documenti per l'uso con l'API SQL. Tuttavia, l'API Tabella è disponibile solo come destinazione nell'utilità della riga di comando. Non è possibile esportare nell'API Tabella usando l'interfaccia utente dell'utilità di migrazione dati. Per altre informazioni, vedere [Importare dati da usare con l'API Tabelle di Azure Cosmos DB](table-import.md).

:::image type="content" source="./media/import-data/azuretablesource.png" alt-text="Screenshot delle opzioni relative all'origine per Archiviazione tabelle di Azure":::

Il formato della stringa di connessione di Archiviazione tabelle di Azure è:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Usare il comando Verify per assicurarsi che l'istanza di Archiviazione tabelle di Azure specificata nel campo della stringa di connessione sia accessibile.

Immettere il nome della tabella di Azure da cui eseguire l'importazione. Se si preferisce, si può specificare un [filtro](/visualstudio/azure/vs-azure-tools-table-designer-construct-filter-strings).

L'opzione dell'utilità di importazione dell'origine Archiviazione tabelle di Azure presenta le seguenti opzioni aggiuntive:

1. Include Internal Fields
   1. All: include tutti i campi interni (PartitionKey, RowKey e Timestamp)
   2. None: esclude tutti i campi interni
   3. RowKey: include solo il campo RowKey
2. Select Columns
   1. I filtri di Archiviazione tabelle di Azure non supportano le proiezioni. Per importare solo specifiche proprietà delle entità tabelle di Azure, aggiungerle all'elenco Select Columns. Tutte le altre proprietà delle entità vengono ignorate.

Ecco un esempio di riga di comando per importare da Archiviazione tabelle di Azure:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a name="import-from-amazon-dynamodb"></a><a id="DynamoDBSource"></a>Importa da Amazon DynamoDB

L'opzione dell'utilità di importazione dell'origine Amazon DynamoDB consente di importare da una singola tabella Amazon DynamoDB. È facoltativamente possibile filtrare le entità da importare. Sono disponibili vari modelli in modo che l'impostazione di un'importazione è più semplice possibile.

:::image type="content" source="./media/import-data/dynamodbsource1.png" alt-text="Screenshot delle opzioni relative all'origine per Amazon DynamoDB - Strumenti di migrazione del database.":::

:::image type="content" source="./media/import-data/dynamodbsource2.png" alt-text="Screenshot delle opzioni relative all'origine per Amazon DynamoDB con modello - Strumenti di migrazione del database.":::

Il formato della stringa di connessione DynamoDB di Amazon è:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Usare il comando Verify per assicurarsi che l'istanza di MongoDB specificata nel campo della stringa di connessione sia accessibile.

Ecco un esempio di riga di comando per importare da Amazon DynamoDB:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a name="import-from-azure-blob-storage"></a><a id="BlobImport"></a>Importare dall'Archiviazione BLOB di Azure

Il file JSON, i file di esportazione MongoDB e le opzioni dell'utilità di importazione di codice sorgente file CSV consentono di importare uno o più file dall'archiviazione Blob di Azure. Dopo avere specificato un URL di contenitore BLOB e una chiave dell'account, fornire un'espressione regolare per selezionare i file da importare.

:::image type="content" source="./media/import-data/blobsource.png" alt-text="Screenshot delle opzioni relative all'origine per i file JSON":::

Ecco un esempio di riga di comando per importare file JSON dall'archiviazione BLOB di Azure:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a name="import-from-a-sql-api-collection"></a><a id="SQLSource"></a>Importare da una raccolta di API SQL

L'opzione dell'utilità di importazione per un'origine Azure Cosmos DB consente di importare dati da uno o più contenitori Azure Cosmos e, facoltativamente, di filtrare i documenti usando una query.  

:::image type="content" source="./media/import-data/documentdbsource.png" alt-text="Screenshot delle opzioni relative all'origine per Azure Cosmos DB":::

Il formato della stringa di connessione di Azure Cosmos DB è il seguente:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

È possibile recuperare la stringa di connessione dell'account Azure Cosmos DB dalla pagina Chiavi del portale di Azure, come descritto in [Come gestire un account Azure Cosmos DB](./how-to-manage-database-account.md). Tuttavia, il nome del database deve essere aggiunto alla stringa di connessione nel formato seguente:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Usare il comando Verifica per assicurarsi che l'istanza di Azure Cosmos DB specificata nel campo della stringa di connessione sia accessibile.

Per eseguire l'importazione da un singolo contenitore Azure Cosmos, immettere il nome della raccolta da cui importare i dati. Per eseguire l'importazione da più contenitori Azure Cosmos, specificare un'espressione regolare corrispondente a uno o più nomi di raccolta, ad esempio collection01 | collection02 | collection03. Se si preferisce, si può specificare o fornire un file per una query sia per filtrare che per determinare i dati da importare.

> [!NOTE]
> Poiché il campo della raccolta accetta le espressioni regolari, se si importa da un'unica raccolta il cui nome contiene caratteri di espressioni regolari, tali caratteri dovranno essere preceduti da un carattere di escape.

L'opzione dell'utilità di importazione per un'origine Azure Cosmos DB offre le opzioni avanzate seguenti.

1. Include Internal Fields (Includi campi interni): specifica se includere o meno le proprietà di sistema dei documenti di Azure Cosmos DB, ad esempio, _rid, _ts, nell'esportazione.
2. Number of Retries on Failure (Numero di tentativi in caso di errore): specifica il numero di tentativi di connessione ad Azure Cosmos DB da effettuare in caso di errori temporanei, ad esempio un'interruzione della connettività di rete.
3. Retry Interval (Intervallo tentativi): specifica l'intervallo di attesa tra i tentativi di connessione ad Azure Cosmos DB in caso di errori temporanei, ad esempio un'interruzione della connettività di rete.
4. Connection Mode (Modalità connessione): specifica la modalità di connessione da usare con Azure Cosmos DB. Le scelte disponibili sono DirectTcp, DirectHttps e Gateway. Le modalità di connessione diretta sono più veloci, mentre la modalità gateway si integra più facilmente con il firewall perché usa solo la porta 443.

:::image type="content" source="./media/import-data/documentdbsourceoptions.png" alt-text="Screenshot delle opzioni avanzate per origini Azure Cosmos DB":::

> [!TIP]
> Per impostazione predefinita, la modalità di connessione dello strumento di importazione è DirectTcp. Se si verificano problemi con il firewall, passare alla modalità di connessione Gateway, che richiede solo la porta 443.

Ecco alcuni esempi di riga di comando per l'importazione da Azure Cosmos DB:

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite
```

> [!TIP]
> Lo strumento di importazione dati di Azure Cosmos DB supporta anche l'importazione di dati dall'[emulatore di Azure Cosmos DB](local-emulator.md). Quando si importano dati da un emulatore locale, impostare l'endpoint `https://localhost:<port>`.

## <a name="import-from-hbase"></a><a id="HBaseSource"></a>Importa da HBase

L'opzione dell'utilità di importazione HBase origine consente di importare dati da una tabella HBase e filtrare i dati. Sono disponibili vari modelli in modo che l'impostazione di un'importazione è più semplice possibile.

:::image type="content" source="./media/import-data/hbasesource1.png" alt-text="Screenshot delle opzioni relative all'origine per HBase.":::

:::image type="content" source="./media/import-data/hbasesource2.png" alt-text="Screenshot delle opzioni di origine di HBase con il menu di scelta rapida del filtro espanso.":::

Il formato della stringa di connessione HBase Stargate è:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Utilizzare il comando verifica per garantire che l'istanza di HBase specificato nel campo della stringa di connessione sia accessibile.ssione sia accessibile.

Ecco un esempio di riga di comando per l'importazione da HBase:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a name="import-to-the-sql-api-bulk-import"></a><a id="SQLBulkTarget"></a>Importare nell'API SQL (importazione in blocco)

L'utilità di importazione in blocco di Azure Cosmos DB consente di eseguire l'importazione da qualsiasi opzione di origine disponibile, usando una stored procedure di Azure Cosmos DB per una maggiore efficienza. Lo strumento supporta l'importazione in contenitore Azure Cosmos con partizione singola. Supporta inoltre l'importazione partizionata con partizionamento dei dati in più contenitori Azure Cosmos con partizione singola. Per altre informazioni sul partizionamento dei dati, vedere l'articolo relativo a [partizionamento e ridimensionamento in Azure Cosmos DB](partitioning-overview.md). Lo strumento crea, esegue e quindi elimina la stored procedure dalla raccolta o dalle raccolte di destinazione.  

:::image type="content" source="./media/import-data/documentdbbulk.png" alt-text="Screenshot delle opzioni relative all'importazione in blocco di Azure Cosmos DB":::

Il formato della stringa di connessione di Azure Cosmos DB è il seguente:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

La stringa di connessione dell'account Azure Cosmos DB può essere recuperata dalla pagina Chiavi del portale di Azure, come descritto in [Come gestire un account Azure Cosmos DB](./how-to-manage-database-account.md), ma è necessario aggiungere il nome del database alla stringa di connessione nel formato seguente:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Usare il comando Verifica per assicurarsi che l'istanza di Azure Cosmos DB specificata nel campo della stringa di connessione sia accessibile.

Per importare in un'unica raccolta, immettere il nome della raccolta da cui importare i dati e fare clic sul pulsante Aggiungi. Per importare in più raccolte, immettere il nome di ogni raccolta singolarmente o usare la sintassi seguente per specificare più raccolte: *prefisso_raccolta*[indice iniziale - indice finale]. Quando si specificano più raccolte tramite la sintassi menzionata in precedenza, tenere presente le indicazioni seguenti:

1. Sono supportati solo criteri di denominazione con intervalli interi. Ad esempio, se si specifica collection[0-3], vengono create le raccolte seguenti: collection0, collection1, collection2, collection3.
2. È possibile usare una sintassi abbreviata: collection[3] crea lo stesso set di raccolte citato nel passaggio 1.
3. È possibile specificare più di una sostituzione. Ad esempio, collection[0-1] [0-9] genera 20 nomi di raccolte con zeri iniziali (collection01, ..02, ..03).

Dopo aver specificato il nome della raccolta, scegliere la velocità effettiva desiderata della raccolta, da 400 UR a 10.000 UR. Per ottimizzare le prestazioni di importazione, scegliere una velocità effettiva superiore. Per altre informazioni sui livelli di prestazioni, vedere l'articolo relativo ai [livelli di prestazioni in Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> L'impostazione della velocità effettiva delle prestazioni si applica solo alla creazione di raccolte. Se la raccolta specificata esiste già, la velocità effettiva non viene modificata.

Quando si importa in più raccolte, lo strumento di importazione supporta il partizionamento orizzontale basato su hash. In questo scenario, specificare la proprietà di documento da usare come chiave di partizione. Se la chiave di partizione viene lasciata vuota, i documenti vengono partizionati in modo casuale tra le raccolte di destinazione.

È possibile specificare facoltativamente quale campo dell'origine di importazione deve essere usato come proprietà ID del documento di Azure Cosmos DB durante l'importazione. Se i documenti non hanno questa proprietà, lo strumento di importazione genera un GUID come valore della proprietà ID.

Durante l'importazione sono disponibili numerose opzioni avanzate. Innanzitutto, anche se lo strumento include una stored procedure di importazione in blocco predefinita (BulkInsert.js), è possibile scegliere di specificare la propria stored procedure di importazione:

 :::image type="content" source="./media/import-data/bulkinsertsp.png" alt-text="Screenshot dell'opzione relativa alla stored procedure di inserimento in blocco per Azure Cosmos DB":::

Inoltre, quando si importano i tipi di data, ad esempio da SQL Server o MongoDB, è possibile scegliere tra tre opzioni di importazione:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Screenshot delle opzioni di importazione relative a data e ora per Azure Cosmos DB":::

* String: persiste come valore di stringa.
* Epoch: persiste come valore numerico di periodo.
* Both: persiste come valore di stringa e numerico di periodo. Questa opzione crea un documento secondario, ad esempio: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

L'utilità di importazione in blocco di Azure Cosmos DB offre le opzioni avanzate aggiuntive seguenti.

1. Batch Size (Dimensioni batch): per impostazione predefinita, le dimensioni batch dello strumento sono pari a 50.  Se i documenti da importare sono di grandi dimensioni, provare a ridurre le dimensioni batch. Se invece i documenti da importare sono di piccole dimensioni, provare ad aumentare le dimensioni batch.
2. Max Script Size (bytes) (Dimensioni massime script - Byte): per impostazione predefinita lo strumento usa dimensioni massime dello script pari a 512 KB.
3. Disable Automatic Id Generation (Disabilita generazione ID automatica): se ogni documento da importare contiene un campo ID, selezionando questa opzione le prestazioni possono migliorare. I documenti privi di un campo ID univoco non vengono importati.
4. Update Existing Documents (Aggiorna documenti esistenti): per impostazione predefinita, lo strumento non sostituisce i documenti esistenti con conflitti tra ID. Questa opzione consente di sovrascrivere i documenti esistenti con ID corrispondenti. Questa funzionalità è utile per le migrazioni dei dati pianificate che aggiornano i documenti esistenti.
5. Number of Retries on Failure (Numero di tentativi in caso di errore): specifica il numero di tentativi di connessione ad Azure Cosmos DB da effettuare in caso di errori temporanei, ad esempio un'interruzione della connettività di rete.
6. Retry Interval (Intervallo tentativi): specifica l'intervallo di attesa tra i tentativi di connessione ad Azure Cosmos DB in caso di errori temporanei, ad esempio un'interruzione della connettività di rete.
7. Connection Mode (Modalità connessione): specifica la modalità di connessione da usare con Azure Cosmos DB. Le scelte disponibili sono DirectTcp, DirectHttps e Gateway. Le modalità di connessione diretta sono più veloci, mentre la modalità gateway si integra più facilmente con il firewall perché usa solo la porta 443.

:::image type="content" source="./media/import-data/docdbbulkoptions.png" alt-text="Screenshot delle opzioni avanzate di importazione in blocco di Azure Cosmos DB":::

> [!TIP]
> Per impostazione predefinita, la modalità di connessione dello strumento di importazione è DirectTcp. Se si verificano problemi con il firewall, passare alla modalità di connessione Gateway, che richiede solo la porta 443.

## <a name="import-to-the-sql-api-sequential-record-import"></a><a id="SQLSeqTarget"></a>Importare nell'API SQL (importazione di record sequenziali)

L'utilità di importazione di record sequenziali di Azure Cosmos DB consente di importare un record alla volta da un'opzione di origine disponibile. È possibile scegliere questa opzione se si sta importando in una raccolta esistente che ha raggiunto la quota di stored procedure. Lo strumento supporta l'importazione in un singolo contenitore Azure Cosmos, sia con partizione singola che con più partizioni. Supporta inoltre l'importazione partizionata con partizionamento dei dati in più contenitori Azure Cosmos con partizione singola o con più partizioni. Per altre informazioni sul partizionamento dei dati, vedere l'articolo relativo a [partizionamento e ridimensionamento in Azure Cosmos DB](partitioning-overview.md).

:::image type="content" source="./media/import-data/documentdbsequential.png" alt-text="Screenshot delle opzioni di importazione di record sequenziali per Azure Cosmos DB":::

Il formato della stringa di connessione di Azure Cosmos DB è il seguente:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

È possibile recuperare la stringa di connessione dell'account Azure Cosmos DB dalla pagina Chiavi del portale di Azure, come descritto in [Come gestire un account Azure Cosmos DB](./how-to-manage-database-account.md). Tuttavia, il nome del database deve essere aggiunto alla stringa di connessione nel formato seguente:

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Usare il comando Verifica per assicurarsi che l'istanza di Azure Cosmos DB specificata nel campo della stringa di connessione sia accessibile.

Per importare in un'unica raccolta, immettere il nome della raccolta in cui importare i dati e quindi fare clic sul pulsante Aggiungi. Per importare in più raccolte, immettere il nome di ogni raccolta singolarmente. È anche possibile usare la sintassi seguente per specificare più raccolte: *prefisso_raccolta*[indice iniziale - indice finale]. Quando si specificano più raccolte tramite la sintassi menzionata in precedenza, tenere presente le indicazioni seguenti:

1. Sono supportati solo criteri di denominazione con intervalli interi. Ad esempio, se si specifica collection[0-3], vengono create le raccolte seguenti: collection0, collection1, collection2, collection3.
2. È possibile usare una sintassi abbreviata: collection[3] crea lo stesso set di raccolte citato nel passaggio 1.
3. È possibile specificare più di una sostituzione. Ad esempio, collection[0-1] [0-9] crea 20 nomi di raccolta con zeri iniziali (collection01, ..02, ..03).

Dopo aver specificato il nome della raccolta, scegliere la velocità effettiva desiderata della raccolta, da 400 UR a 250.000 UR. Per ottimizzare le prestazioni di importazione, scegliere una velocità effettiva superiore. Per altre informazioni sui livelli di prestazioni, vedere l'articolo relativo ai [livelli di prestazioni in Azure Cosmos DB](performance-levels.md). Eventuali importazioni nelle raccolte con una velocità effettiva > 10.000 UR richiedono una chiave di partizione. Se si sceglie di avere più di 250.000 UR, è necessario inviare una richiesta di nel portale per incrementare l'account.

> [!NOTE]
> L'impostazione della velocità effettiva si applica solo alla creazione di raccolte o del database. Se la raccolta specificata esiste già, la velocità effettiva non viene modificata.

Quando si importa in più raccolte, lo strumento di importazione supporta il partizionamento orizzontale basato su hash. In questo scenario, specificare la proprietà di documento da usare come chiave di partizione. Se la chiave di partizione viene lasciata vuota, i documenti vengono partizionati in modo casuale tra le raccolte di destinazione.

È possibile specificare facoltativamente quale campo dell'origine di importazione deve essere usato come proprietà ID del documento di Azure Cosmos DB durante l'importazione. Se i documenti non hanno questa proprietà, lo strumento di importazione genera un GUID come valore della proprietà ID.

Durante l'importazione sono disponibili numerose opzioni avanzate. Quando si importano i tipi di data, ad esempio da SQL Server o MongoDB, è possibile scegliere prima di tutto tra tre opzioni di importazione:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Screenshot delle opzioni di importazione relative a data e ora per Azure Cosmos DB":::

* String: persiste come valore di stringa.
* Epoch: persiste come valore numerico di periodo.
* Both: persiste come valore di stringa e numerico di periodo. Questa opzione crea un documento secondario, ad esempio: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

L'utilità di importazione di record sequenziali di Azure Cosmos DB offre le opzioni avanzate aggiuntive seguenti.

1. Number of Parallel Requests (Numero di richieste parallele): per impostazione predefinita, lo strumento include due richieste parallele. Se i documenti da importare sono di piccole dimensioni, provare ad aumentare il numero di richieste parallele. Se questo numero è troppo elevato, durante l'importazione potrebbe venire applicata la limitazione della velocità.
2. Disable Automatic Id Generation (Disabilita generazione ID automatica): se ogni documento da importare contiene un campo ID, selezionando questa opzione le prestazioni possono migliorare. I documenti privi di un campo ID univoco non vengono importati.
3. Update Existing Documents (Aggiorna documenti esistenti): per impostazione predefinita, lo strumento non sostituisce i documenti esistenti con conflitti tra ID. Questa opzione consente di sovrascrivere i documenti esistenti con ID corrispondenti. Questa funzionalità è utile per le migrazioni dei dati pianificate che aggiornano i documenti esistenti.
4. Number of Retries on Failure (Numero di tentativi in caso di errore): specifica il numero di tentativi di connessione ad Azure Cosmos DB da effettuare in caso di errori temporanei, ad esempio un'interruzione della connettività di rete.
5. Retry Interval (Intervallo tentativi): specifica l'intervallo di attesa tra i tentativi di connessione ad Azure Cosmos DB in caso di errori temporanei, ad esempio un'interruzione della connettività di rete.
6. Connection Mode (Modalità connessione): specifica la modalità di connessione da usare con Azure Cosmos DB. Le scelte disponibili sono DirectTcp, DirectHttps e Gateway. Le modalità di connessione diretta sono più veloci, mentre la modalità gateway si integra più facilmente con il firewall perché usa solo la porta 443.

:::image type="content" source="./media/import-data/documentdbsequentialoptions.png" alt-text="Screenshot delle opzioni avanzate di importazione di record sequenziali di Azure Cosmos DB":::

> [!TIP]
> Per impostazione predefinita, la modalità di connessione dello strumento di importazione è DirectTcp. Se si verificano problemi con il firewall, passare alla modalità di connessione Gateway, che richiede solo la porta 443.

## <a name="specify-an-indexing-policy"></a><a id="IndexingPolicy"></a>Specificare un criterio di indicizzazione

Quando si consente all'utilità di migrazione di creare raccolte di API SQL di Azure Cosmos DB durante l'importazione, è possibile specificare i criteri di indicizzazione delle raccolte. Nella sezione delle opzioni avanzate per l'importazione in blocco e l'importazione di record sequenziali di Azure Cosmos DB, passare alla sezione relativa ai criteri di indicizzazione.

:::image type="content" source="./media/import-data/indexingpolicy1.png" alt-text="Screenshot delle opzioni avanzate relative ai criteri di indicizzazione di Azure Cosmos DB.":::

Usando l'opzione avanzata Criteri di indicizzazione, è possibile selezionare un file di criteri di indicizzazione, immettere manualmente un criterio di indicizzazione oppure selezionarne uno da un set di modelli predefiniti facendo clic con il pulsante destro del mouse sulla casella di testo relativa al criterio di indicizzazione.

I modelli dei criteri che lo strumento fornisce sono:

* Valore predefinito. Questo criterio risulta ottimale quando si eseguono query di uguaglianza su stringhe. È utile anche se si usano query di tipo ORDER BY, di intervallo e di uguaglianza per i numeri. Questo criterio ha un overhead di archiviazione indice inferiore rispetto a intervallo.
* Intervallo. Questo criterio risulta ottimale quando si usano query di tipo ORDER BY, di intervallo e di uguaglianza su numeri e stringhe. Questo criterio ha un overhead di archiviazione indice superiore rispetto a predefinito o Hash.

:::image type="content" source="./media/import-data/indexingpolicy2.png" alt-text="Screenshot delle opzioni avanzate relative ai criteri di indicizzazione di Azure Cosmos DB che specifica le informazioni di destinazione.":::

> [!NOTE]
> Se non si specifica un criterio di indicizzazione, viene applicato il criterio predefinito. Per altre informazioni sui criteri di indicizzazione, vedere l'articolo relativo ai [criteri di indicizzazione di Azure Cosmos DB](index-policy.md).

## <a name="export-to-json-file"></a>Esportare in file JSON

L'utilità di esportazione JSON di Azure Cosmos DB consente di esportare qualsiasi opzione di origine disponibile in un file JSON contenente una matrice di documenti JSON. Lo strumento gestisce l'esportazione. In alternativa, è possibile scegliere di visualizzare il comando di migrazione risultante ed eseguire il comando manualmente. Il file JSON risultante può essere archiviato in locale o nel servizio di archiviazione Blob di Azure.

:::image type="content" source="./media/import-data/jsontarget.png" alt-text="Screenshot dell'opzione di esportazione in file JSON locale di Azure Cosmos DB":::

:::image type="content" source="./media/import-data/jsontarget2.png" alt-text="Screenshot dell'opzione di esportazione in file JSON in una risorsa di Archiviazione BLOB di Azure di Azure Cosmos DB":::

È possibile scegliere facoltativamente di modificare il file JSON risultante. Questa azione fa aumentare le dimensioni del documento risultante rendendone il contenuto più leggibile.

* Esportazione JSON standard

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Esportazione JSON modificata

  ```JSON
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]
  ```

Ecco un esempio di riga di comando per esportare il file JSON nell'archiviazione BLOB di Azure:

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>Configurazione avanzata

Nella schermata Configurazione avanzata specificare il percorso del file di log in cui scrivere gli errori. In questa pagina vengono applicate le regole seguenti:

1. Se non viene fornito un nome di file, tutti gli errori vengono restituiti nella pagina dei risultati.
2. Se viene fornito un nome di file senza una directory, il file viene creato (o sovrascritto) nella directory dell'ambiente corrente.
3. Se si seleziona un file esistente, il file viene sovrascritto. Non è prevista l'opzione di accodamento.
4. Quindi, scegliere se registrare tutti i messaggi di errore, quelli critici o nessuno. Infine, definire la frequenza con cui il messaggio di trasferimento su schermo viene aggiornato con lo stato di avanzamento.

   :::image type="content" source="./media/import-data/AdvancedConfiguration.png" alt-text="Screenshot della schermata di configurazione avanzata":::

## <a name="confirm-import-settings-and-view-command-line"></a>Confermare le impostazioni di importazione e visualizzare la riga di comando

1. Dopo avere specificato le informazioni di origine e di destinazione e la configurazione avanzata, esaminare il riepilogo della migrazione e, se si vuole, visualizzare o copiare il comando di migrazione risultante. Copiare il comando è utile per automatizzare le operazioni di importazione.

    :::image type="content" source="./media/import-data/summary.png" alt-text="Screenshot della schermata di riepilogo.":::

    :::image type="content" source="./media/import-data/summarycommand.png" alt-text="Screenshot della schermata di riepilogo con l'anteprima della riga di comando.":::

2. Una volta verificate le opzioni di origine e di destinazione, fare clic su **Import**. Il tempo trascorso, il conteggio degli elementi trasferiti e le informazioni sugli errori (se non è stato fornito un nome file nella pagina Configurazione avanzata) vengono aggiornati mentre l'importazione è in corso. Al termine dell'importazione è possibile esportare i risultati, ad esempio per gestire gli eventuali errori di importazione.

    :::image type="content" source="./media/import-data/viewresults.png" alt-text="Screenshot dell'opzione di esportazione in JSON di Azure Cosmos DB.":::

3. È anche possibile avviare una nuova importazione reimpostando tutti i valori o mantenendo le impostazioni esistenti. Ad esempio, si potrebbe scegliere di mantenere le informazioni della stringa di connessione, la scelta dell'origine e della destinazione e altro ancora.

    :::image type="content" source="./media/import-data/newimport.png" alt-text="Screenshot dell'opzione di esportazione in JSON di Azure Cosmos DB con finestra di dialogo di conferma della nuova importazione.":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione verranno effettuate le attività seguenti:

> [!div class="checklist"]
> * Installazione dello strumento di migrazione dati
> * Importazione di dati da diverse origini dati
> * Esportazione da Azure Cosmos DB a JSON

È ora possibile passare all'esercitazione successiva per ottenere informazioni su come eseguire query sui dati usando Azure Cosmos DB.

> [!div class="nextstepaction"]
>[Come eseguire query sui dati](../cosmos-db/tutorial-query-sql-api.md)
