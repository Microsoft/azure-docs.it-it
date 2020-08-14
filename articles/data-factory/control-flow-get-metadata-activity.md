---
title: Ottenere l'attività dei metadati in Azure Data Factory
description: Informazioni su come usare l'attività Ottieni metadati in una pipeline Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: jingwang
ms.openlocfilehash: 26d52eed02c9d25ed2f18afa3a5262ba9224b0ba
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224867"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Ottenere l'attività dei metadati in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

È possibile utilizzare l'attività Recupera metadati per recuperare i metadati di tutti i dati in Azure Data Factory. Questa attività può essere utilizzata negli scenari seguenti:

- Convalidare i metadati di tutti i dati.
- Attivare una pipeline quando i dati sono pronti/disponibili.

La funzionalità seguente è disponibile nel flusso di controllo:

- Per eseguire la convalida, è possibile utilizzare l'output dell'attività Ottieni metadati nelle espressioni condizionali.
- È possibile attivare una pipeline quando una condizione viene soddisfatta tramite do until loop.

## <a name="capabilities"></a>Funzionalità

L'attività Ottieni metadati accetta un set di dati come input e restituisce informazioni sui metadati come output. Attualmente sono supportati i seguenti connettori e i metadati recuperabili corrispondenti. Le dimensioni massime dei metadati restituiti sono pari a 2 MB.

>[!NOTE]
>Se si esegue l'attività Ottieni metadati in un runtime di integrazione self-hosted, le funzionalità più recenti sono supportate nella versione 3,6 o successive.

### <a name="supported-connectors"></a>Connettori supportati

**Archiviazione file**

| Connettore/Metadati | itemName<br>(file/cartella) | itemType<br>(file/cartella) | size<br>(file) | created<br>(file/cartella) | LastModified<br>(file/cartella) |childItems<br>(cartella) |contentMD5<br>(file) | structure<br/>(file) | columnCount<br>(file) | esiste<br>(file/cartella) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Archiviazione BLOB di Azure](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [File di Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [File system](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Quando si usa l'attività Ottieni metadati in una cartella, assicurarsi di disporre dell'autorizzazione di elenco/esecuzione per la cartella specificata.
- Per Amazon S3 e Google Cloud Storage, `lastModified` si applica al bucket e alla chiave ma non alla cartella virtuale e `exists` si applica al bucket e alla chiave, ma non al prefisso o alla cartella virtuale.
- Per l'archiviazione BLOB di Azure, `lastModified` si applica al contenitore e al BLOB, ma non alla cartella virtuale.
- `lastModified` Filter attualmente si applica per filtrare gli elementi figlio ma non la cartella o il file specificato.
- Il filtro con caratteri jolly per le cartelle o i file non è supportato per l'attività Ottieni metadati.
- `structure` e `columnCount` non sono supportati per il recupero di metadati da file binari, JSON o XML.

**Database relazionale**

| Connettore/Metadati | structure | columnCount | esiste |
|:--- |:--- |:--- |:--- |
| [Database SQL di Azure](connector-azure-sql-database.md) | √ | √ | √ |
| [Istanza gestita di database SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opzioni dei metadati

È possibile specificare i tipi di metadati seguenti nell'elenco dei campi attività Recupera metadati per recuperare le informazioni corrispondenti:

| Tipo di metadati | Descrizione |
|:--- |:--- |
| itemName | Nome del file o della cartella. |
| itemType | Tipo di file o di cartella. Il valore restituito è `File` o `Folder` . |
| size | Dimensioni del file, in byte. Applicabile solo ai file. |
| created | Data/ora di creazione del file o della cartella. |
| LastModified | Data/ora dell'ultima modifica del file o della cartella. |
| childItems | Elenco di sottocartelle e file nella cartella specificata. Applicabile solo alle cartelle. Il valore restituito è un elenco del nome e del tipo di ogni elemento figlio. |
| contentMD5 | MD5 del file. Applicabile solo ai file. |
| structure | Struttura dei dati del file o della tabella di database relazionale. Il valore restituito è un elenco di nomi di colonna e di colonne. |
| columnCount | Numero di colonne nel file o nella tabella relazionale. |
| esiste| Indica se esiste un file, una cartella o una tabella. Si noti che se `exists` è specificato nell'elenco dei campi Get Metadata, l'attività non avrà esito negativo anche se il file, la cartella o la tabella non esiste. Viene invece `exists: false` restituito nell'output. |

>[!TIP]
>Quando si desidera convalidare l'esistenza di un file, una cartella o una tabella, specificare `exists` nell'elenco dei campi attività Ottieni metadati. È quindi possibile controllare il `exists: true/false` risultato nell'output dell'attività. Se `exists` non è specificato nell'elenco dei campi, l'attività Ottieni metadati avrà esito negativo se l'oggetto non viene trovato.

>[!NOTE]
>Quando si ottengono metadati da archivi di file e si configura `modifiedDatetimeStart` o `modifiedDatetimeEnd` , nell' `childItems` output includerà solo i file nel percorso specificato con l'ora dell'Ultima modifica nell'intervallo specificato. In non includerà gli elementi nelle sottocartelle.

## <a name="syntax"></a>Sintassi

**Attività Ottieni metadati**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**Set di dati**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>Proprietà del tipo

Attualmente, l'attività Ottieni metadati può restituire i seguenti tipi di informazioni sui metadati:

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
fieldList | Tipi di informazioni sui metadati necessari. Per informazioni dettagliate sui metadati supportati, vedere la sezione opzioni per i [metadati](#metadata-options) di questo articolo. | Sì 
dataset | Set di dati di riferimento i cui metadati devono essere recuperati dall'attività Recupera metadati. Vedere la sezione [funzionalità](#capabilities) per informazioni sui connettori supportati. Per informazioni dettagliate sulla sintassi dei set di dati, vedere gli argomenti del connettore specifici. | Sì
formatSettings | Applicare quando si usa il tipo di formato DataSet. | No
storeSettings | Applicare quando si usa il tipo di formato DataSet. | No

## <a name="sample-output"></a>Output di esempio

I risultati di Get Metadata vengono visualizzati nell'output dell'attività. Di seguito sono riportati due esempi che mostrano opzioni di metadati estese. Per usare i risultati in un'attività successiva, usare questo modello: `@{activity('MyGetMetadataActivity').output.itemName}` .

### <a name="get-a-files-metadata"></a>Ottenere i metadati di un file

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Ottenere i metadati di una cartella

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi
Informazioni sulle altre attività del flusso di controllo supportate da Data Factory:

- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
