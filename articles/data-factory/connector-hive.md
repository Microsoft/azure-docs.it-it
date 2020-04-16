---
title: Copiare dati da Hive usando Azure Data Factory
description: Informazioni su come copiare dati da Hive in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 3720d917d71fa4e8c5a14bb60fdc7c405be4bfdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410445"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Copiare dati da Hive usando Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Hive. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Hive è supportato per le attività seguenti:This Hive connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare dati da Hive a qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire le entità di Data Factory specifiche per il connettore Hive.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Hive sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Hive** | Sì |
| host | Indirizzo IP o nome host del server Hive, separato da ';' per più host (solo quando serviceDiscoveryMode è abilitato).  | Sì |
| port | Porta TCP che il server Hive usa per l'ascolto delle connessioni client. Se ci si connette a Azure HDInsights, specificare la porta come 443. | Sì |
| serverType | Tipo di server Hive. <br/>I valori consentiti sono: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | No |
| thriftTransportProtocol | Protocollo di trasporto da usare nel livello Thrift. <br/>I valori consentiti sono **Binary**, **SASL**, **HTTP** | No |
| authenticationType | Metodo di autenticazione usato per accedere al server Hive. <br/>I valori consentiti sono **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Sì |
| serviceDiscoveryMode | true per indicare l'uso del servizio ZooKeeper; in caso contrario, false.  | No |
| zooKeeperNameSpace | Spazio dei nomi in ZooKeeper nel quale vengono aggiunti i nodi del server Hive 2.  | No |
| useNativeQuery | Specifica se il driver utilizza query HiveQL native o le converte in un formato equivalente in HiveQL.  | No |
| username | Nome utente usato per accedere al server Hive.  | No |
| password | Password corrispondente all'utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| httpPath | URL parziale corrispondente al server Hive.  | No |
| enableSsl | Specifica se le connessioni al server vengono crittografate tramite TLS. Il valore predefinito è false.  | No |
| trustedCertPath | Percorso completo del file con estensione pem contenente certificati CA attendibili per la verifica del server durante la connessione tramite TLS. Questa proprietà può essere impostata solo quando si utilizza TLS su un iR self-hosted. Il valore predefinito è il file cacerts.pem installato con il runtime di integrazione.  | No |
| useSystemTrustStore | Specifica se usare o meno un certificato della CA dall'archivio di scopi consentiti o da un file .pem specificato. Il valore predefinito è false.  | No |
| allowHostNameCNMismatch | Specifica se richiedere che un nome di certificato TLS/SSL emesso dalla CA corrisponda al nome host del server durante la connessione tramite TLS. Il valore predefinito è false.  | No |
| allowSelfSignedServerCert | Specifica se consentire o meno i certificati autofirmati dal server. Il valore predefinito è false.  | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per ulteriori informazioni, vedere la sezione [Prerequisiti.](#prerequisites) Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Hive.

Per copiare dati da Hive, impostare la proprietà type del set di dati su **HiveObject**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **HiveObject** | Sì |
| schema | Nome dello schema. |No (se nell'origine dell'attività è specificato "query")  |
| tabella | Nome della tabella. |No (se nell'origine dell'attività è specificato "query")  |
| tableName | Nome della tabella, inclusa la parte dello schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Per un nuovo `schema` `table`carico di lavoro, utilizzare e . | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Hive.

### <a name="hivesource-as-source"></a>HiveSource come origine

Per copiare dati da Hive, impostare il tipo di origine nell'attività di copia su **HiveSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **HiveSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
