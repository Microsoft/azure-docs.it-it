---
title: Copiare e trasformare i dati in Azure Cosmos DB (API SQL)
description: Informazioni su come copiare dati da e verso Azure Cosmos DB (API SQL) e trasformare i dati in Azure Cosmos DB (API SQL) usando Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: d42f30ebd72dca81255ddc02a9440db19979536d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608067"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copiare e trasformare i dati in Azure Cosmos DB (API SQL) usando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versione corrente](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In questo articolo viene illustrato come usare l'attività di copia in Azure Data Factory per copiare dati da e verso Azure Cosmos DB (API SQL) e usare Flusso di dati per trasformare i dati in Azure Cosmos DB (API SQL). Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).



>[!NOTE]
>Questo connettore supporta solo Cosmos DB API SQL. Per l'API MongoDB, fare riferimento al [connettore per l'API di Azure Cosmos DB per MongoDB](connector-azure-cosmos-db-mongodb-api.md). Al momento non sono supportati altri tipi di API.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Azure Cosmos DB (API SQL) è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

Per l'attività di copia, questo connettore Azure Cosmos DB (API SQL) supporta:

- Copiare dati da e verso l'[API SQL](../cosmos-db/introduction.md) di Azure Cosmos DB.
- Scrivere in Azure Cosmos DB tramite **insert** o **upsert**.
- Importare ed esportare documenti JSON come sono o copiare dati da o in un set di dati tabulari, ad esempio un database SQL e un file CSV. Per copiare i documenti così come sono da o verso file JSON o da un altro Azure Cosmos DB raccolta, vedere [importare ed esportare documenti JSON](#import-and-export-json-documents).

Data Factory si integra con la [libreria dell'executor bulk di Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) per offrire le migliori prestazioni durante la scrittura in Azure Cosmos DB.

> [!TIP]
> Il [video sulla migrazione dei dati](https://youtu.be/5-SRNiC_qOU) illustra la procedura di copia dei dati da Archiviazione BLOB di Azure ad Azure Cosmos DB. Il video presenta inoltre alcune considerazioni generali sull'ottimizzazione delle prestazioni per l'inserimento di dati in Azure Cosmos DB.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti presentano informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche per Azure Cosmos DB (API SQL).

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Azure Cosmos DB (API SQL) sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** deve essere impostata su **CosmosDb**. | Sì |
| connectionString |Specificare le informazioni richieste per connettersi al database di Azure Cosmos DB.<br />**Nota**: è necessario specificare le informazioni sul database nella stringa di connessione come illustrato negli esempi seguenti. <br/> È anche possibile inserire la chiave dell'account in Azure Key Vault e rimuovere la configurazione di `accountKey` dalla stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, come valore predefinito viene usato Azure Integration Runtime. |No |

**Esempio**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la chiave dell'account in Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
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

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md).

Per il set di dati Azure Cosmos DB (API SQL) sono supportate le proprietà seguenti: 

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** del set di dati deve essere impostata su **CosmosDbSqlApiCollection**. |Sì |
| collectionName |Nome della raccolta documenti di Azure Cosmos DB. |Sì |

Se si usa il set di dati di tipo "DocumentDbCollection", è ancora supportato così com'è per la compatibilità con le versioni precedenti per l'attività di copia e ricerca, non è supportato per il flusso di dati. Si consiglia di utilizzare il nuovo modello in futuro.

**Esempio**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di Azure Cosmos DB (API SQL). Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (API SQL) come origine

Per copiare dati da Azure Cosmos DB (API SQL), impostare il tipo **source** nell'attività di copia su **DocumentDbCollectionSource**. 

Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** dell'origine dell'attività di copia deve essere impostata su **CosmosDbSqlApiSource**. |Sì |
| query |Specificare la query Azure Cosmos DB per leggere i dati.<br/><br/>Esempio:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Se non specificato, viene eseguita questa istruzione SQL: `select <columns defined in structure> from mycollection` |
| preferredRegions | Elenco preferito di aree a cui connettersi durante il recupero di dati da Cosmos DB. | No |
| pageSize | Numero di documenti per pagina del risultato della query. Il valore predefinito è "-1", che indica l'utilizzo delle dimensioni di pagina dinamiche del lato servizio fino a 1000. | No |
| detectDatetime | Indica se rilevare DateTime dai valori stringa nei documenti. I valori consentiti sono: **true** (predefinito), **false**. | No |

Se si usa l'origine del tipo "DocumentDbCollectionSource", è ancora supportata così com'è per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello in futuro, che offre funzionalità più avanzate per la copia dei dati da Cosmos DB.

**Esempio**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Quando si copiano dati da Cosmos DB, a meno che non si desideri [esportare documenti JSON così come sono](#import-and-export-json-documents), la procedura consigliata consiste nel specificare il mapping nell'attività di copia. Data Factory rispetta il mapping specificato nell'attività: se una riga non contiene un valore per una colonna, viene fornito un valore null per il valore della colonna. Se non si specifica un mapping, Data Factory deduce lo schema usando la prima riga nei dati. Se la prima riga non contiene lo schema completo, alcune colonne non saranno presenti nel risultato dell'operazione di attività.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (API SQL) come sink

Per copiare dati in Azure Cosmos DB (API SQL), impostare il tipo **sink** nell'attività di copia su **DocumentDbCollectionSink**. 

Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** del sink dell'attività di copia deve essere impostata su **CosmosDbSqlApiSink**. |Sì |
| writeBehavior |Descrive come scrivere i dati in Azure Cosmos DB. Valori consentiti: **insert** e **upsert**.<br/><br/>Il comportamento di **upsert** consiste nella sostituzione del documento se esiste già un documento con lo stesso ID. In caso contrario, il documento viene inserito.<br /><br />**Nota**: Data Factory genera automaticamente un ID per un documento se non è specificato nel documento originale o tramite il mapping di colonna. È quindi necessario assicurarsi che il documento contenga un ID in modo che **upsert** funzioni come previsto. |No<br />(il valore predefinito è **Insert**) |
| writeBatchSize | Data Factory usa la [libreria dell'executor bulk di Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) per scrivere dati in Azure Cosmos DB. La proprietà **writeBatchSize** controlla la dimensione dei documenti forniti da ADF alla libreria. È possibile provare ad aumentare il valore per **writeBatchSize** per migliorare le prestazioni e a ridurre il valore se le dimensioni dei documenti diventano grandi. Vedere i suggerimenti di seguito. |No<br />(il valore predefinito è **10.000**) |
| disableMetricsCollection | Data Factory raccoglie le metriche, ad esempio Cosmos DB ur per l'ottimizzazione delle prestazioni di copia e consigli. Se questo comportamento non è desiderato, specificare `true` per disattivarlo. | No (il valore predefinito è `false`) |
| maxConcurrentConnections |Limite massimo di connessioni simultanee stabilite all'archivio dati durante l'esecuzione dell'attività. Specificare un valore solo quando si desidera limitare le connessioni simultanee.| No |


>[!TIP]
>Per importare documenti JSON così come sono, vedere la sezione [importare o esportare documenti JSON](#import-and-export-json-documents) . per eseguire la copia da dati a forma di tabulazione, vedere [eseguire la migrazione da un database relazionale a un Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB limita le dimensioni per una singola richiesta a 2 MB. La formula è Dimensioni richiesta = Dimensioni singolo documento * Dimensioni batch di scrittura. Se viene generato l'errore **"Dimensioni della richiesta troppo grandi."**, **ridurre il valore `writeBatchSize`** nella configurazione del sink di copia.

Se si usa l'origine del tipo "DocumentDbCollectionSink", è ancora supportata così com'è per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello in futuro, che offre funzionalità più avanzate per la copia dei dati da Cosmos DB.

**Esempio**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Mapping dello schema

Per copiare dati da Azure Cosmos DB a sink tabulare o invertiti, fare riferimento al [mapping dello schema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Quando si trasformano i dati nel flusso di dati di mapping, è possibile leggere e scrivere nelle raccolte in Cosmos DB. Per altre informazioni, vedere la [trasformazione origine](data-flow-source.md) e la [trasformazione sink](data-flow-sink.md) nei flussi di dati per mapping.

### <a name="source-transformation"></a>Trasformazione origine

Le impostazioni specifiche per Azure Cosmos DB sono disponibili nella scheda **Opzioni di origine** della trasformazione origine. 

**Includi colonne di sistema:** Se true, ```id``` , ```_ts``` e altre colonne di sistema verranno incluse nei metadati del flusso di dati da CosmosDB. Quando si aggiornano le raccolte, è importante includerlo in modo che sia possibile acquisire l'ID di riga esistente.

**Dimensioni pagina:** Numero di documenti per pagina del risultato della query. Il valore predefinito è "-1" che usa la pagina dinamica del servizio fino a 1000.

**Velocità effettiva:** Impostare un valore facoltativo per il numero di ur da applicare alla raccolta CosmosDB per ogni esecuzione di questo flusso di dati durante l'operazione di lettura. Il valore minimo è 400.

**Aree preferite:** Scegliere le aree di lettura preferite per questo processo.

#### <a name="json-settings"></a>Impostazioni JSON

**Documento singolo:** Selezionare questa opzione se ADF deve trattare l'intero file come singolo documento JSON.

**Nomi di colonna non racchiusi tra virgolette:** Selezionare questa opzione se i nomi di colonna in JSON non sono racchiusi tra virgolette.

**Contiene commenti:** Usare questa opzione se i documenti JSON includono commenti nei dati.

**Singolo racchiuso tra virgolette:** Questa opzione deve essere selezionata se le colonne e i valori del documento sono racchiusi tra virgolette singole.

**Barra rovesciata con Escape:** Se si usano le barre rovesciate per eseguire il escape dei caratteri nel file JSON, scegliere questa opzione.

### <a name="sink-transformation"></a>Trasformazione sink

Le impostazioni specifiche per Azure Cosmos DB sono disponibili nella scheda **Impostazioni** della trasformazione sink.

**Update method** (Metodo di aggiornamento): determina le operazioni consentite nella destinazione del database. Per impostazione predefinita, sono consentiti solo gli inserimenti. Per eseguire operazioni di aggiornamento, upsert o eliminazione di righe, è necessaria una trasformazione alter-row che applichi alle righe i tag corrispondenti alle azioni. Per le operazioni di aggiornamento, upsert ed eliminazione è necessario impostare una o più colonne chiave per determinare quale riga modificare.

**Azione di raccolta:** Determina se ricreare la raccolta di destinazione prima della scrittura.
* None: nessuna azione verrà eseguita nella raccolta.
* Ricrea: la raccolta viene eliminata e ricreata

**Dimensioni batch**: numero intero che rappresenta il numero di oggetti scritti in Cosmos DB raccolta in ogni batch. In genere, è sufficiente iniziare con le dimensioni del batch predefinite. Per ottimizzare ulteriormente questo valore, tenere presente quanto segue:

- Cosmos DB limita le dimensioni per una singola richiesta a 2 MB. La formula è "dimensioni richiesta = singolo documento dimensioni * dimensioni batch". Se viene segnalato un errore indicante che la dimensione della richiesta è troppo grande, ridurre il valore delle dimensioni del batch.
- Maggiore è la dimensione del batch, migliore sarà la velocità effettiva di ADF, assicurandosi di allocare un numero sufficiente di ur per consentire il carico di lavoro.

**Chiave di partizione:** Immettere una stringa che rappresenta la chiave di partizione per la raccolta. Esempio: ```/movies/title```

**Velocità effettiva:** Impostare un valore facoltativo per il numero di ur da applicare alla raccolta CosmosDB per ogni esecuzione del flusso di dati. Il valore minimo è 400.

**Budget della velocità effettiva di scrittura:** Intero che rappresenta le UR che si desidera allocare per questa operazione di scrittura del flusso di dati, dalla velocità effettiva totale allocata alla raccolta.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importare ed esportare documenti JSON

È possibile usare questo connettore di Azure Cosmos DB (API SQL) per eseguire facilmente le operazioni seguenti:

* Copiare documenti come sono da una raccolta di Azure Cosmos DB a un'altra.
* Importare in Azure Cosmos DB documenti JSON da varie origini, tra cui Archiviazione BLOB di Azure, Azure Data Lake Storage e altri archivi basati su file supportati da Azure Data Factory.
* Esportare documenti JSON da una raccolta di Azure Cosmos DB in diversi archivi basati su file.

Per ottenere la copia senza schema:

* Quando si usa lo strumento Copia dati, selezionare l'opzione **Export as-is to JSON files or Cosmos DB collection** (Esportare così come sono nel file JSON o in una raccolta di Cosmos DB).
* Quando si usa la creazione di attività, scegliere il formato JSON con l'archivio file corrispondente per origine o sink.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Eseguire la migrazione da un database relazionale a un Cosmos DB

Quando si esegue la migrazione da un database relazionale, ad esempio SQL Server Azure Cosmos DB, l'attività di copia può eseguire facilmente il mapping dei dati tabulari dall'origine ai documenti JSON appiattiti in Cosmos DB. In alcuni casi, potrebbe essere necessario riprogettare il modello di dati per ottimizzarlo per i casi d'uso di NoSQL in base alla [modellazione dei dati in Azure Cosmos DB](../cosmos-db/modeling-data.md), ad esempio, per denormalizzare i dati incorporando tutti gli elementi secondari correlati all'interno di un documento JSON. Per questo caso, fare riferimento a [questo articolo](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) con una procedura dettagliata su come ottenere il risultato usando Azure Data Factory attività di copia.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).