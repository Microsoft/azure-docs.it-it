---
title: Copiare dati da Spark
description: Informazioni su come copiare dati da Spark in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 0a31ed7a8df080c0e1186ed75f325e36aff32920
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388822"
---
# <a name="copy-data-from-spark-using-azure-data-factory"></a>Copiare dati da Spark usando Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Spark. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Spark è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Spark a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Spark.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Spark sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Spark** | Sì |
| host | Indirizzo IP o nome host del server Spark.  | Sì |
| port | Porta TCP che il server Spark usa per l'ascolto delle connessioni client. Se ci si connette a Azure HDInsights, specificare la porta come 443. | Sì |
| serverType | Tipo del server Spark. <br/>I valori consentiti sono **SharkServer**, **SharkServer2**, **SparkThriftServer** | No |
| thriftTransportProtocol | Protocollo di trasporto da usare nel livello Thrift. <br/>I valori consentiti sono **Binary**, **SASL**, **HTTP** | No |
| authenticationType | Metodo di autenticazione usato per accedere al server Spark. <br/>I valori consentiti sono **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Sì |
| username | Nome utente usato per accedere al server Spark.  | No |
| password | Password corrispondente all'utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| httpPath | URL parziale corrispondente al server Spark.  | No |
| enableSsl | Specifica se le connessioni al server sono crittografate con TLS. Il valore predefinito è false.  | No |
| trustedCertPath | Percorso completo del file con estensione PEM contenente i certificati CA attendibili per la verifica del server durante la connessione tramite TLS. Questa proprietà può essere impostata solo quando si usa TLS sul runtime di integrazione self-hosted. Il valore predefinito è il file cacerts.pem installato con il runtime di integrazione.  | No |
| useSystemTrustStore | Specifica se usare o meno un certificato della CA dall'archivio di scopi consentiti o da un file .pem specificato. Il valore predefinito è false.  | No |
| allowHostNameCNMismatch | Specifica se è necessario che il nome del certificato TLS/SSL emesso dall'autorità di certificazione corrisponda al nome host del server durante la connessione tramite TLS. Il valore predefinito è false.  | No |
| allowSelfSignedServerCert | Specifica se consentire o meno i certificati autofirmati dal server. Il valore predefinito è false.  | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "SparkLinkedService",
    "properties": {
        "type": "Spark",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Spark.

Per copiare dati da Spark, impostare la proprietà type del set di dati su **SparkObject**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **SparkObject** | Sì |
| schema | Nome dello schema. |No (se nell'origine dell'attività è specificato "query")  |
| tabella | Nome della tabella. |No (se nell'origine dell'attività è specificato "query")  |
| tableName | Nome della tabella con schema. Questa proprietà è supportata per garantire la compatibilità con le versioni precedenti. Per i nuovi carichi di lavoro, usare `schema` e `table`. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "SparkDataset",
    "properties": {
        "type": "SparkObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Spark linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Spark.

### <a name="spark-as-source"></a>Spark come origine

Per copiare dati da Spark, impostare il tipo di origine nell'attività di copia su **SparkSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **SparkSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSpark",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Spark input dataset name>",
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
                "type": "SparkSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
