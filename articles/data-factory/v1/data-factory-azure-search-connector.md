---
title: Push dei dati nell'indice di Ricerca con Azure Data Factory | Documentazione Microsoft
description: Informazioni su come eseguire il push dei dati nell'indice di Ricerca di Azure con Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7ad328eec7e16b5368b78a0dfccbf5c09adb5c13
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330008"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Push dei dati in un indice di Ricerca di Azure con Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1](data-factory-azure-search-connector.md)
> * [Versione 2 (corrente)](../connector-azure-search.md)

> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. Se si usa la versione corrente del servizio Data Factory, vedere le informazioni sul [connettore Ricerca di Azure nella versione 2](../connector-azure-search.md).

Questo articolo descrive come usare l'attività di copia per eseguire il push dei dati da un archivio dati di origine supportato nell'indice di Ricerca di Azure. Gli archivi dati di origine supportati sono elencati nella colonna Origine della tabella [Origini e sink supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Questo articolo si basa sull'articolo [Spostamento di dati e attività di copia](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivi dati supportati.

## <a name="enabling-connectivity"></a>Abilitazione della connettività
Per consentire la connessione del servizio Data Factory a un archivio dati locale, installare Gateway di gestione dati nell'ambiente locale. È possibile installare il gateway nello stesso computer che ospita l'archivio dati di origine oppure in un computer diverso per evitare che si verifichi un conflitto con l'archivio dati per le risorse.

Gateway di gestione dati consente di connettere le origini dati locali ai servizi cloud in modo sicuro e gestito. Vedere l’articolo [Spostare dati tra cloud e locale](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate sui Gateway di Gestione dati.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con un'attività di copia che esegue il push dei dati da un archivio dati di origine all'indice di Ricerca di Azure usando diversi strumenti/API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Per istruzioni dettagliate, vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

Per creare una pipeline, è anche possibile usare gli strumenti seguenti: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia.
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output.

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per un esempio con definizioni JSON per entità di Data Factory usate per copiare dati in un indice di Ricerca di Azure, vedere la sezione [Esempio di JSON: Copiare dati da un'istanza di SQL Server locale in un indice di Ricerca di Azure](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) di questo articolo.

Le sezioni seguenti riportano le informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di data factory specifiche dell'indice di Ricerca di Azure:

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

La tabella seguente include le descrizioni degli elementi JSON specifici del servizio collegato Ricerca di Azure.

| Proprietà | DESCRIZIONE | Obbligatoria |
| -------- | ----------- | -------- |
| type | La proprietà type deve essere impostata su: **AzureSearch**. | Yes |
| url | URL del servizio Ricerca di Azure. | Yes |
| key | Chiave amministratore del servizio Ricerca di Azure. | Yes |

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo [Set di dati in Azure Data Factory](data-factory-create-datasets.md) . Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati. La sezione **typeProperties** è diversa per ogni tipo di set di dati. Le proprietà della sezione typeProperties per un set di dati di tipo **AzureSearchIndex** sono le seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
| -------- | ----------- | -------- |
| type | La proprietà type deve essere impostata su **AzureSearchIndex**.| Yes |
| indexName | Nome dell'indice di Ricerca di Azure. Il servizio Data Factory non crea l'indice. L'indice deve essere presente in Ricerca di Azure. | Yes |


## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo relativo alla [creazione di pipeline](data-factory-create-pipelines.md) . Proprietà come nome, descrizione, tabelle di input e output e criteri diversi sono disponibili per tutti i tipi di attività. Le proprietà disponibili nella sezione typeProperties variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Per l'attività di copia, quando il sink è del tipo **AzureSearchIndexSink**, nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Specifica se eseguire un'unione o una sostituzione quando nell'indice esiste già un documento. Vedere la [proprietà WriteBehavior](#writebehavior-property).| Merge (impostazione predefinita)<br/>Caricamento| No  |
| WriteBatchSize | Consente di caricare dati nell'indice di Ricerca di Azure quando le dimensioni del buffer raggiungono il valore indicato da writeBatchSize. Per informazioni dettagliate, vedere la [proprietà WriteBatchSize](#writebatchsize-property). | Da 1 a 1000. Il valore predefinito è 1000. | No  |

### <a name="writebehavior-property"></a>Proprietà WriteBehavior
Durante la scrittura di dati, AzureSearchSink esegue operazioni di upsert. In altre parole, quando si scrive un documento il servizio Ricerca di Azure aggiorna il documento esistente anziché generare un'eccezione di conflitto se la chiave relativa esiste già nell'indice di Ricerca di Azure.

Le operazioni di upsert eseguite da AzureSearchSink sono le seguenti (con AzureSearch SDK):

- **Merge**: le colonne del nuovo documento vengono unite con quelle del documento esistente. Per le colonne del nuovo documento con valore Null, viene mantenuto il valore del documento esistente.
- **Upload**: il nuovo documento sostituisce quello esistente. Per le colonne del nuovo documento non specificate, il valore è impostato su Null indipendentemente dalla presenza o meno di un valore diverso da Null nel documento esistente.

L'operazione predefinita è **Merge**.

### <a name="writebatchsize-property"></a>Proprietà WriteBatchSize
Il servizio Ricerca di Azure supporta la scrittura di documenti come batch. Un batch può contenere da 1 a 1000 azioni e un'azione gestisce un documento per eseguire l'operazione di caricamento/unione.

### <a name="data-type-support"></a>Supporto dei tipi di dati
La tabella seguente indica se un tipo di dati di Ricerca di Azure è supportato o meno.

| Tipo di dati di Ricerca di Azure | Supportato nel sink di Ricerca di Azure |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Double | S |
| boolean | S |
| DataTimeOffset | S |
| String Array | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Esempio di JSON: Copiare dati da un'istanza di SQL Server locale in un indice di Ricerca di Azure

L'esempio seguente mostra:

1. Un servizio collegato di tipo [AzureSearch](#linked-service-properties).
2. Un servizio collegato di tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureSearchIndex](#dataset-properties).
4. Una [pipeline](data-factory-create-pipelines.md) con un'attività di copia che usa [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) e [AzureSearchIndexSink](#copy-activity-properties).

Nell'esempio i dati della serie temporale vengono copiati ogni ora da un database di SQL Server locale in un indice di Ricerca di Azure. Le proprietà JSON usate in questo esempio sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio è necessario configurare Gateway di gestione dati nel computer locale. Le istruzioni sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) .

**Servizio collegato Ricerca di Azure**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Servizio collegato di SQL Server**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**Set di dati input di SQL Server**

L'esempio presuppone che sia stata creata una tabella "MyTable" in SQL Server e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale. È possibile eseguire query su più tabelle all'interno dello stesso database usando un singolo set di dati, ma come typeProperty tableName del set di dati deve essere usata una sola tabella.

Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno a Data Factory e non è prodotto da un'attività al suo interno.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Set di dati di output di Ricerca di Azure**

Nell'esempio i dati vengono copiati in un indice di Ricerca di Azure denominato **products**. Il servizio Data Factory non crea l'indice. Per eseguire il test dell'esempio, creare un indice con questo nome. Creare l'indice di Ricerca di Azure con lo stesso numero di colonne presente nel set di dati di input. Le nuove voci vengono aggiunte all'indice di Ricerca di Azure ogni ora.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**Attività di copia in una pipeline con un'origine SQL e il sink dell'indice di Ricerca di Azure:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo **source** è impostato su **SqlSource** e il tipo **sink** è impostato su **AzureSearchIndexSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Se si copiano dati da un archivio dati cloud a Ricerca di Azure, la proprietà `executionLocation` è obbligatoria. Il frammento JSON seguente mostra la modifica necessaria nell'attività di copia `typeProperties` come esempio. Per informazioni sui valori supportati e altri dettagli, vedere la sezione [Copiare dati tra archivi dati cloud](data-factory-data-movement-activities.md#global).

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Copiare da un'origine cloud
Se si copiano dati da un archivio dati cloud a Ricerca di Azure, la proprietà `executionLocation` è obbligatoria. Il frammento JSON seguente mostra la modifica necessaria nell'attività di copia `typeProperties` come esempio. Per informazioni sui valori supportati e altri dettagli, vedere la sezione [Copiare dati tra archivi dati cloud](data-factory-data-movement-activities.md#global).

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

È anche possibile eseguire il mapping delle colonne del set di dati di origine alle colonne del set di dati sink nella definizione dell'attività di copia. Per altre informazioni, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati (attività di copia) e sui vari modi per ottimizzarle, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

* [Esercitazione dell'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate della creazione di una pipeline con un'attività di copia.
