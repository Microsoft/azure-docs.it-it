---
title: Copiare dati da e verso SQL Server
description: Informazioni su come spostare i dati da e verso SQL Server database locale o in una VM di Azure usando Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/18/2020
ms.openlocfilehash: 5d35d0434f65f28b58a6d81172ade12aceb02987
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384997"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Copiare dati da e verso SQL Server tramite Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-sqlserver-connector.md)
> * [Versione corrente](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in un database SQL Server. Si basa sull'articolo [Panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale dell'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore SQL Server è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

È possibile copiare dati da un database di SQL Server in qualsiasi archivio dati di sink supportato. In alternativa, è possibile copiare dati da qualsiasi archivio dati di origine supportato in un database di SQL Server. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SQL Server supporta:

- SQL Server versione 2005 e successive.
- La copia dei dati tramite l'autenticazione di SQL o di Windows.
- Come origine, il recupero dei dati tramite una query SQL o un stored procedure. Per informazioni dettagliate, è anche possibile scegliere di eseguire la copia parallela da SQL Server origine, vedere la sezione [copia parallela dal database SQL](#parallel-copy-from-sql-database) .
- Come sink, creazione automatica della tabella di destinazione se non esiste in base allo schema di origine. Aggiunta di dati a una tabella o richiamo di una stored procedure con la logica personalizzata durante la copia. 

[SQL Server Express database locale](/sql/database-engine/configure-windows/sql-server-express-localdb) non è supportato.

>[!NOTE]
>SQL Server [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) non è attualmente supportata da questo connettore. Per aggirare il lavoro, è possibile utilizzare un [connettore ODBC generico](connector-odbc.md) e un driver ODBC SQL Server. Attenersi a [queste linee guida per il](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver) download del driver ODBC e le configurazioni della stringa di connessione.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire Data Factory entità specifiche del connettore di database SQL Server.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato SQL Server sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su **SqlServer**. | Sì |
| connectionString |Specificare le informazioni di **ConnectionString** necessarie per connettersi al database di SQL Server usando l'autenticazione SQL o l'autenticazione di Windows. Vedere gli esempi seguenti.<br/>È anche possibile inserire una password in Azure Key Vault. Se si tratta dell'autenticazione SQL, estrarre la `password` configurazione dalla stringa di connessione. Per ulteriori informazioni, vedere l'esempio JSON che segue la tabella e [archivia le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| userName |Specificare un nome utente se si usa l'autenticazione di Windows. Ad esempio, **nomedominio\\nomeutente**. |No |
| password |Specificare una password per l'account utente specificato per il nome utente. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Azure Data Factory. In alternativa, [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |No |
| connectVia | Questo [runtime di integrazione](concepts-integration-runtime.md) viene usato per connettersi all'archivio dati. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

>[!TIP]
>Se viene raggiunto un errore con il codice di errore "UserErrorFailedToConnectToSqlServer" e un messaggio simile a "il limite della sessione per il database è XXX ed è stato raggiunto", aggiungere `Pooling=false` alla stringa di connessione e riprovare.

**Esempio 1: usare l'autenticazione SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 2: usare l'autenticazione SQL con una password in Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 3: utilizzo dell'autenticazione di Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati SQL Server.

Per copiare dati da e in un database di SQL Server, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su **SqlServerTable**. | Sì |
| schema | Nome dello schema. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella/vista. |No per l'origine, Sì per il sink  |
| tableName | Nome della tabella/vista con schema. Questa proprietà è supportata per garantire la compatibilità con le versioni precedenti. Per i nuovi carichi di lavoro, usare `schema` e `table`. | No per l'origine, Sì per il sink |

**Esempio**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per definire le attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink SQL Server.

### <a name="sql-server-as-a-source"></a>SQL Server come origine

>[!TIP]
>Per caricare i dati da SQL Server in modo efficiente usando il partizionamento dei dati, vedere la [copia parallela dal database SQL](#parallel-copy-from-sql-database).

Per copiare dati da un database SQL Server, impostare il tipo di origine nell'attività di copia su **SqlSource**. Nella sezione source dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **sqlSource**. | Sì |
| sqlReaderQuery |Usare la query SQL personalizzata per leggere i dati. Un esempio è `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Questa proprietà definisce il nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No |
| storedProcedureParameters |Questi parametri sono relativi alla stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri stored procedure. |No |
| isolationLevel | Specifica il comportamento di blocco della transazione per l'origine SQL. I valori consentiti sono: **ReadCommitted**, **READUNCOMMITTED**, **RepeatableRead**, **Serializable**, **snapshot**. Se non specificato, viene usato il livello di isolamento predefinito del database. Per altre informazioni dettagliate, vedere [questo documento](/dotnet/api/system.data.isolationlevel). | No |
| partitionOptions | Specifica le opzioni di partizionamento dei dati utilizzate per caricare i dati da SQL Server. <br>I valori consentiti sono: **None** (impostazione predefinita), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando è abilitata un'opzione di partizione (ovvero non `None` ), il grado di parallelismo per caricare simultaneamente i dati da SQL Server è controllato dall' [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) impostazione dell'attività di copia. | No |
| partitionSettings | Specifica il gruppo di impostazioni per il partizionamento dei dati. <br>Applicare quando l'opzione partition non è `None` . | No |
| ***In `partitionSettings` :*** | | |
| partitionColumnName | Specificare il nome della colonna di origine **in un tipo Integer o data/ora** ( `int` ,, `smallint` `bigint` , `date` , `smalldatetime` , `datetime` , `datetime2` o `datetimeoffset` ) che verrà utilizzato dal partizionamento dell'intervallo per la copia parallela. Se non è specificato, l'indice o la chiave primaria della tabella vengono rilevati automaticamente e utilizzati come colonna della partizione.<br>Si applica quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per recuperare i dati di origine, associare  `?AdfDynamicRangePartitionCondition ` la clausola WHERE. Per un esempio, vedere la sezione [copia parallela da database SQL](#parallel-copy-from-sql-database) . | No |
| partitionUpperBound | Valore massimo della colonna di partizione per la suddivisione dell'intervallo di partizioni. Questo valore viene usato per decidere lo stride della partizione, non per filtrare le righe nella tabella. Tutte le righe della tabella o del risultato della query verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente il valore.  <br>Si applica quando l'opzione di partizione è `DynamicRange`. Per un esempio, vedere la sezione [copia parallela da database SQL](#parallel-copy-from-sql-database) . | No |
| partitionLowerBound | Valore minimo della colonna di partizione per la suddivisione dell'intervallo di partizioni. Questo valore viene usato per decidere lo stride della partizione, non per filtrare le righe nella tabella. Tutte le righe della tabella o del risultato della query verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente il valore.<br>Si applica quando l'opzione di partizione è `DynamicRange`. Per un esempio, vedere la sezione [copia parallela da database SQL](#parallel-copy-from-sql-database) . | No |

**Tenere presente quanto segue:**

- Se per **sqlSource** viene specificato **sqlReaderQuery** , l'attività di copia esegue la query sull'origine SQL Server per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters**, se la stored procedure accetta parametri.
- Quando si usa stored procedure nell'origine per recuperare i dati, tenere presente che se il stored procedure è stato progettato per restituire uno schema diverso quando viene passato un valore di parametro diverso, potrebbe verificarsi un errore o un risultato imprevisto durante l'importazione dello schema dall'interfaccia utente o durante la copia dei dati nel database SQL con la creazione automatica della tabella.

**Esempio: usare query SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Esempio: usare un stored procedure**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Definizione della stored procedure**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server come sink

> [!TIP]
> Scopri di più sui comportamenti di scrittura supportati, sulle configurazioni e sulle procedure consigliate, dalla [procedura consigliata per il caricamento dei dati in SQL Server](#best-practice-for-loading-data-into-sql-server).

Per copiare dati da SQL Server, impostare il tipo di sink nell'attività di copia su **SqlSink**. Nella sezione sink dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su **sqlsink**. | Sì |
| preCopyScript |Questa proprietà specifica una query SQL per l'attività di copia da eseguire prima di scrivere i dati in SQL Server. Viene richiamata solo una volta per ogni esecuzione della copia. È possibile usare questa proprietà per pulire i dati precaricati. |No |
| tableOption | Specifica se [creare automaticamente la tabella di sink](copy-activity-overview.md#auto-create-sink-tables) se non esiste in base allo schema di origine. La creazione automatica della tabella non è supportata quando il sink specifica stored procedure. I valori consentiti sono: `none` (impostazione predefinita), `autoCreate`. |No |
| sqlWriterStoredProcedureName | Il nome della stored procedure che definisce come applicare i dati di origine in una tabella di destinazione. <br/>Questa stored procedure viene *richiamata per batch*. Per le operazioni che vengono eseguite una sola volta e non hanno nulla a che fare con i dati di origine, ad esempio DELETE o TRUNCATE, usare la `preCopyScript` Proprietà.<br>Vedere l'esempio da [richiamare un stored procedure da un sink SQL](#invoke-a-stored-procedure-from-a-sql-sink). | No |
| storedProcedureTableTypeParameterName |Nome del parametro del tipo di tabella specificato nell'stored procedure.  |No |
| sqlWriterTableType |Nome del tipo di tabella da utilizzare nel stored procedure. Nel corso dell'attività di copia, i dati spostati vengono resi disponibili in una tabella temporanea di questo tipo. Il codice della stored procedure può quindi unire i dati di cui è in corso la copia con i dati esistenti. |No |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |
| writeBatchSize |Numero di righe da inserire nella tabella SQL *per batch*.<br/>I valori consentiti sono integer per il numero di righe. Per impostazione predefinita, Azure Data Factory determina in modo dinamico le dimensioni del batch appropriate in base alle dimensioni della riga. |No |
| writeBatchTimeout |Questa proprietà specifica il tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono per l'intervallo di tempo. Un esempio è "00:30:00" per 30 minuti. Se non viene specificato alcun valore, il valore predefinito per il timeout è "02:00:00". |No |

**Esempio 1: accodare i dati**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Esempio 2: richiamare un stored procedure durante la copia**

Per altre informazioni, vedere [Richiamare una stored procedure da un sink SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>Copia parallela dal database SQL

Il connettore SQL Server nell'attività di copia fornisce il partizionamento dei dati incorporato per la copia dei dati in parallelo. È possibile trovare le opzioni di partizionamento dei dati nella scheda **origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-sql-server/connector-sql-partition-options.png)

Quando si Abilita la copia partizionata, l'attività di copia esegue query parallele sull'origine del SQL Server per caricare i dati in base alle partizioni. Il grado di parallelismo è controllato dall'impostazione [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) sull'attività di copia. Se, ad esempio, si imposta `parallelCopies` su quattro, data factory genera ed esegue contemporaneamente quattro query in base all'opzione di partizione specificata e alle impostazioni e ogni query recupera una porzione di dati dal SQL Server.

Si consiglia di abilitare la copia parallela con il partizionamento dei dati, specialmente quando si caricano grandi quantità di dati dal SQL Server. Di seguito sono riportate le configurazioni consigliate per i diversi scenari: Quando si copiano dati in un archivio dati basato su file, è consigliabile scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                                     | Impostazioni consigliate                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Caricamento completo da tabelle di grandi dimensioni, con partizioni fisiche.        | **Opzione partition**: partizioni fisiche della tabella. <br><br/>Durante l'esecuzione, Data Factory rileva automaticamente le partizioni fisiche e copia i dati in base alle partizioni. <br><br/>Per verificare se la tabella contiene o meno una partizione fisica, è possibile fare riferimento a [questa query](#sample-query-to-check-physical-partition). |
| Caricamento completo da tabelle di grandi dimensioni, senza partizioni fisiche, con una colonna integer o DateTime per il partizionamento dei dati. | **Opzioni di partizione**: Partizione a intervalli dinamici.<br>**Colonna partizione** (facoltativo): specificare la colonna utilizzata per partizionare i dati. Se non è specificato, viene utilizzata la colonna di chiave primaria o di indice.<br/>Limite **superiore** della partizione e **limite inferiore della partizione** (facoltativo): specificare se si vuole determinare lo stride della partizione. Questa operazione non è relativa al filtraggio delle righe nella tabella. tutte le righe della tabella verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente i valori.<br><br>Se, ad esempio, la colonna di partizione "ID" ha valori compresi tra 1 e 100 e si imposta il limite inferiore su 20 e il limite superiore come 80, con la copia parallela 4, Data Factory recupera i dati di 4 partizioni-ID nell'intervallo <= 20, [21, 50], [51, 80] e >= 81 rispettivamente. |
| Caricare una grande quantità di dati tramite una query personalizzata, senza partizioni fisiche, con una colonna integer o date/DateTime per il partizionamento dei dati. | **Opzioni di partizione**: Partizione a intervalli dinamici.<br>**Query**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Colonna di partizione**: Specificare la colonna usata per partizionare i dati.<br>Limite **superiore** della partizione e **limite inferiore della partizione** (facoltativo): specificare se si vuole determinare lo stride della partizione. Non per filtrare le righe nella tabella, tutte le righe nel risultato della query verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente il valore.<br><br>Durante l'esecuzione, Data Factory sostituisce `?AdfRangePartitionColumnName` con il nome della colonna e gli intervalli di valori effettivi per ogni partizione e invia al SQL Server. <br>Se, ad esempio, la colonna di partizione "ID" ha valori compresi tra 1 e 100 e si imposta il limite inferiore su 20 e il limite superiore come 80, con la copia parallela 4, Data Factory recupera i dati di 4 partizioni-ID nell'intervallo <= 20, [21, 50], [51, 80] e >= 81 rispettivamente. <br><br>Di seguito sono riportate altre query di esempio per scenari diversi:<br> 1. eseguire una query sull'intera tabella: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. eseguire una query da una tabella con la selezione delle colonne e i filtri aggiuntivi della clausola WHERE: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. eseguire una query con sottoquery: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. query con partizione in sottoquery: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Procedure consigliate per caricare i dati con l'opzione di partizione:

1. Scegliere una colonna distinta come colonna di partizione, ad esempio chiave primaria o chiave univoca, per evitare l'asimmetria dei dati. 
2. Se la tabella include una partizione predefinita, usare l'opzione di partizione "partizioni fisiche della tabella" per ottenere prestazioni migliori.    
3. Se si usa Azure Integration Runtime per copiare i dati, è possibile impostare "[Data Integration Unit (DIU)](copy-activity-performance-features.md#data-integration-units)" di dimensioni maggiori (>4) per usare più risorse di elaborazione. Controllare gli scenari applicabili.
4. "[Degree of copy parallelism](copy-activity-performance-features.md#parallel-copy)" controlla i numeri di partizione, impostando questo numero su un valore troppo grande a volte, è consigliabile impostare questo numero come (Diu o numero di nodi IR self-hosted) * (da 2 a 4).

**Esempio: caricamento completo da tabelle di grandi dimensioni con partizioni fisiche**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Esempio: query con partizione a intervalli dinamici**

```json
"source": {
    "type": "SqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Query di esempio per controllare la partizione fisica

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Se la tabella contiene una partizione fisica, verrà visualizzato "HasPartition" come "Yes" come indicato di seguito.

![Risultato della query SQL](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-sql-server"></a>Procedura consigliata per il caricamento di dati in SQL Server

Quando si copiano dati in SQL Server, potrebbe essere necessario un comportamento di scrittura diverso:

- [Accoda](#append-data): i dati di origine hanno solo i nuovi record.
- [Upsert](#upsert-data): i dati di origine sono inserimenti e aggiornamenti.
- [Overwrite](#overwrite-the-entire-table): si desidera ricaricare ogni volta l'intera tabella della dimensione.
- [Scrivi con logica personalizzata](#write-data-with-custom-logic): ho bisogno di ulteriore elaborazione prima dell'inserimento finale nella tabella di destinazione.

Vedere le rispettive sezioni per la configurazione in Azure Data Factory e procedure consigliate.

### <a name="append-data"></a>Accodare dati

L'accodamento dei dati è il comportamento predefinito di questo connettore di sink SQL Server. Azure Data Factory esegue un inserimento bulk per scrivere in modo efficiente nella tabella. È possibile configurare l'origine e il sink di conseguenza nell'attività di copia.

### <a name="upsert-data"></a>Eseguire l'upsert dei dati

**Opzione 1:** Quando si dispone di una grande quantità di dati da copiare, è possibile eseguire il caricamento bulk di tutti i record in una tabella di staging usando l'attività di copia, quindi eseguire un'attività stored procedure per applicare un'istruzione [merge](/sql/t-sql/statements/merge-transact-sql) o Insert/Update in un'unica immagine. 

L'attività di copia attualmente non supporta in modo nativo il caricamento dei dati in una tabella temporanea di database. Esiste una modalità avanzata per configurarla con una combinazione di più attività. vedere ottimizzare gli [scenari di Upsert bulk del database SQL](https://github.com/scoriani/azuresqlbulkupsert). Di seguito viene illustrato un esempio di utilizzo di una tabella permanente come gestione temporanea.

In Azure Data Factory, ad esempio, è possibile creare una pipeline con un' **attività di copia** concatenata a un' **attività di stored procedure**. Il primo copia i dati dall'archivio di origine in una tabella di staging SQL Server, ad esempio **UpsertStagingTable**, come nome della tabella nel set di dati. Il secondo richiama quindi un stored procedure per unire i dati di origine dalla tabella di staging alla tabella di destinazione e pulire la tabella di staging.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Nel database definire un stored procedure con la logica di MERGE, come nell'esempio seguente, a cui fa riferimento l'attività stored procedure precedente. Si supponga che la destinazione sia la tabella **Marketing** con tre colonne: **profileid**, **stato** e **categoria**. Eseguire Upsert in base alla colonna **profileid** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**Opzione 2:** È possibile scegliere di [richiamare un stored procedure all'interno dell'attività di copia](#invoke-a-stored-procedure-from-a-sql-sink). Questo approccio esegue ogni batch, come regolato dalla `writeBatchSize` proprietà, nella tabella di origine anziché utilizzare BULK INSERT come approccio predefinito nell'attività di copia.

### <a name="overwrite-the-entire-table"></a>Sovrascrivi l'intera tabella

È possibile configurare la proprietà **preCopyScript** in un sink dell'attività di copia. In questo caso, per ogni attività di copia eseguita, Azure Data Factory esegue prima lo script. Esegue quindi la copia per inserire i dati. Per sovrascrivere l'intera tabella con i dati più recenti, ad esempio, specificare uno script per eliminare prima tutti i record prima di eseguire il caricamento bulk dei nuovi dati dall'origine.

### <a name="write-data-with-custom-logic"></a>Scrivere dati con logica personalizzata

I passaggi per scrivere i dati con la logica personalizzata sono simili a quelli descritti nella sezione relativa ai [dati di Upsert](#upsert-data) . Quando è necessario applicare un'elaborazione aggiuntiva prima dell'inserimento finale dei dati di origine nella tabella di destinazione, è possibile caricare in una tabella di staging, quindi richiamare stored procedure attività o richiamare una stored procedure nel sink dell'attività di copia per applicare i dati.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Richiamare una stored procedure da un sink SQL

Quando si copiano dati in SQL Server database, è anche possibile configurare e richiamare una stored procedure specificata dall'utente con parametri aggiuntivi in ogni batch della tabella di origine. La funzionalità stored procedure sfrutta i [parametri con valori di tabella](/dotnet/framework/data/adonet/sql/table-valued-parameters).

È possibile usare una stored procedure quando non si possono usare i meccanismi di copia predefiniti. Un esempio è quando si desidera applicare un'ulteriore elaborazione prima dell'inserimento finale dei dati di origine nella tabella di destinazione. Alcuni esempi di elaborazione aggiuntivi sono quando si desidera unire le colonne, cercare valori aggiuntivi e inserirli in più di una tabella.

Nell'esempio seguente viene illustrato come usare una stored procedure per eseguire un'operazione upsert in una tabella del database SQL Server. Si supponga che i dati di input e la tabella di **Marketing** sink includano tre colonne: **profileid**, **state** e **Category**. Eseguire Upsert in base alla colonna **profileid** e applicarla solo a una categoria specifica denominata "producta".

1. Nel database definire il tipo di tabella con lo stesso nome di **sqlWriterTableType**. Lo schema del tipo di tabella è identico allo schema restituito dai dati di input.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Nel database definire il stored procedure con lo stesso nome di **sqlWriterStoredProcedureName**. che gestisce i dati di input dell'origine specificata e li unisce nella tabella di output. Il nome del parametro del tipo di tabella nella stored procedure corrisponde a **TableName** definito nel set di dati.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. In Azure Data Factory definire la sezione del **sink SQL** nell'attività di copia come indicato di seguito:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Mapping dei tipi di dati per SQL Server

Quando si copiano dati da e in SQL Server, i mapping seguenti vengono utilizzati da SQL Server tipi di dati per Azure Data Factory tipi di dati provvisori. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di SQL Server | Tipo di dati provvisorio di Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| Data |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Oggetto |
| text |String, Char[] |
| time |TimeSpan |
|  timestamp |Byte[] |
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |string |

>[!NOTE]
> Per i tipi di dati che vengono mappati al tipo provvisorio decimale, attualmente l'attività di copia supporta la precisione fino a 28. Se si hanno dati che richiedono una precisione maggiore di 28, è consigliabile convertirli in una stringa in una query SQL.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per altre informazioni sulle proprietà, vedere [Attività GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="using-always-encrypted"></a>Uso di Always Encrypted

Quando si copiano dati da/a SQL Server con [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), usare il [connettore odbc generico](connector-odbc.md) e SQL Server driver ODBC tramite Integration Runtime self-hosted. Questo connettore SQL Server non supporta ora Always Encrypted. 

Più in particolare:

1. Configurare un Integration Runtime self-hosted se non è disponibile. Per informazioni dettagliate, vedere l'articolo relativo alla [Integration Runtime self-hosted](create-self-hosted-integration-runtime.md) .

2. Scaricare il driver ODBC a 64 bit per SQL Server da [qui](/sql/connect/odbc/download-odbc-driver-for-sql-server)e installarlo nel computer Integration Runtime. Per ulteriori informazioni sul funzionamento di questo driver, [utilizzare always Encrypted con ODBC driver for SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Creare un servizio collegato con tipo ODBC per connettersi al database SQL. Per utilizzare l'autenticazione SQL, specificare la stringa di connessione ODBC come indicato di seguito e selezionare autenticazione di **base** per impostare il nome utente e la password.

    ```
    Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
    ```

4. Creare il set di dati e l'attività di copia con il tipo ODBC di conseguenza. Per altre informazioni, vedere [connettore ODBC](connector-odbc.md) .

## <a name="troubleshoot-connection-issues"></a>Risolvere i problemi di connessione

1. Configurare l'istanza di SQL Server in modo che accetti le connessioni remote. Avviare **SQL Server Management Studio**, fare clic con il pulsante destro del mouse su **Server** e scegliere **proprietà**. Selezionare **connessioni** dall'elenco e selezionare la casella **di controllo Consenti connessioni remote al server** .

    ![Abilitare le connessioni remote](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Per i passaggi dettagliati, vedere [configurare l'opzione di configurazione del server accesso remoto](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option).

2. Avviare **Gestione configurazione SQL Server**. Espandere **Configurazione di rete SQL Server** per l'istanza prevista e selezionare **Protocolli per MSSQLSERVER**. I protocolli vengono visualizzati nel riquadro di destra. Per abilitare TCP/IP, fare clic con il pulsante destro del mouse su **TCP/IP** e selezionare **Abilita**.

    ![Abilitare TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Per ulteriori informazioni e modalità alternative di abilitazione del protocollo TCP/IP, vedere [abilitare o disabilitare un protocollo di rete del server](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).

3. Nella stessa finestra, fare doppio clic su **TCP/IP** per avviare la finestra **proprietà TCP/IP** .
4. Passare alla scheda **indirizzi IP** . Scorrere verso il basso per visualizzare la sezione **IPAll** . Prendere nota della **porta TCP**. Il valore predefinito è **1433**.
5. Creare una **regola per Windows Firewall** nel computer per consentire il traffico in ingresso attraverso questa porta. 
6. **Verificare la connessione**: per connettersi a SQL Server usando un nome completo, usare SQL Server Management Studio da un computer diverso. Un esempio è `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).