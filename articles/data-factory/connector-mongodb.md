---
title: Copiare dati da MongoDB
description: Informazioni su come copiare dati da MongoDB in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2021
ms.openlocfilehash: eae2d2adfe2cfdd7e47f2ace9ede9253003aa5bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368779"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiare i dati da MongoDB con Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database MongoDB. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

>[!IMPORTANT]
>ADF include questa nuova versione di connettore MongoDB che offre un migliore supporto nativo per MongoDB. Se nella propria soluzione si usa la versione precedente del connettore MongoDB che è supportata così com'è per compatibilità con le versioni precedenti, vedere l'articolo sulla [versione legacy del connettore MongoDB](connector-mongodb-legacy.md).


## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database MongoDB in un qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, questo connettore MongoDB supporta **versioni fino a 4,2**.


## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di data factory specifiche per il connettore MongoDB.


## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di MongoDB sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà Type deve essere impostata su: **MongoDbV2** |Sì |
| connectionString |Specificare la stringa di connessione di MongoDB, ad esempio `mongodb://[username:password@]host[:port][/[database][?options]]`. Per altri dettagli, vedere la sezione sulla [stringa di connessione nel manuale di MongoDB](https://docs.mongodb.com/manual/reference/connection-string/). <br/><br /> È anche possibile inserire una stringa di connessione in Azure Key Vault. Per informazioni dettagliate, vedere [archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) . |Sì |
| database | Nome del database a cui si vuole accedere. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md). Per il set di dati MongoDB sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **MongoDbV2Collection** | Sì |
| collectionName |Nome della raccolta nel database MongoDB. |Sì |

**Esempio:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```


## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine di MongoDB.

### <a name="mongodb-as-source"></a>MongoDB come origine

Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su: **MongoDbV2Source** | Sì |
| filter | Specifica il filtro di selezione usando gli operatori di query. Per restituire tutti i documenti in una raccolta, omettere questo parametro o passare un documento vuoto ({}). | No |
| cursorMethods.project | Specifica i campi da restituire nei documenti per la proiezione. Per restituire tutti i campi nei documenti corrispondenti, omettere questo parametro. | No |
| cursorMethods.sort | Specifica l'ordine in cui la query restituisce i documenti corrispondenti. Fare riferimento a [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No |
| cursorMethods.limit | Specifica il numero massimo di documenti restituiti dal server. Fare riferimento a [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No |
| cursorMethods.skip | Specifica il numero di documenti da ignorare e la posizione da cui MongoDB inizia a restituire i risultati. Fare riferimento a [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No |
| batchSize | Specifica il numero di documenti da restituire in ogni batch di risposta dall'istanza di MongoDB. Nella maggior parte dei casi, la modifica della dimensione del batch non influisce sull'utente o sull'applicazione. Cosmos DB limiti ogni batch non può superare le dimensioni di 40 MB, ovvero la somma del numero batchSize di dimensioni dei documenti, quindi ridurre questo valore se le dimensioni del documento sono elevate. | No<br/>(il valore predefinito è **100**) |

>[!TIP]
>Azure Data Factory supporta l'utilizzo di documenti BSON in **modalità strict**. Assicurarsi che la query di filtro sia in modalità strict anziché in modalità shell. Per altre informazioni consultare il [manuale di MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="export-json-documents-as-is"></a>Esportare documenti JSON così come sono

È possibile usare questo connettore MongoDB per esportare documenti JSON così come sono da una raccolta MongoDB a diversi archivi basati su file o ad Azure Cosmos DB. Per ottenere una copia priva di schema, ignorare la sezione "structure" (chiamata anche *schema*) nel set di dati e il mapping dello schema nell'attività di copia.


## <a name="schema-mapping"></a>Mapping dello schema

Per copiare dati da MongoDB al sink in formato tabulare, vedere [Mapping dello schema](copy-activity-schema-and-type-mapping.md#schema-mapping).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
