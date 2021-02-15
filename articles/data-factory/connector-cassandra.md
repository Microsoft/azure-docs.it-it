---
title: Copiare dati da Cassandra usando Azure Data Factory
description: Informazioni su come copiare dati da Cassandra in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: a3cd3c3ae28ae302e9469a71d00054152a9b5fb5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383705"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Copiare dati da Cassandra usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Versione corrente](connector-cassandra.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database Cassandra. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Cassandra è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da un database Cassandra in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Cassandra supporta:

- Cassandra **versioni 2.x e 3.x**.
- La copia dei dati usando l'autenticazione **Di base** o **Anonima**.

>[!NOTE]
>Per attività in esecuzione nel runtime di integrazione self-hosted, Cassandra 3.x è supportato dalla versione 3.7 del runtime di integrazione e versioni successive.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Il runtime di integrazione offre un driver per Cassandra integrato e non è quindi necessario installare manualmente alcun driver quando si copiano dati da/in Cassandra.

## <a name="getting-started"></a>Guida introduttiva

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Cassandra.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Cassandra sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su: **Cassandra** |Sì |
| host |Uno o più indirizzi IP o nomi host di server Cassandra.<br/>Specificare un elenco delimitato da virgole degli indirizzi IP o nomi host per la connessione a tutti i server contemporaneamente. |Sì |
| port |La porta TCP che il server Cassandra usa per ascoltare le connessioni client. |No (il valore predefinito è 9042) |
| authenticationType | Tipo di autenticazione usato per la connessione al database Cassandra.<br/>I valori consentiti sono: **Basic** e **Anonymous**. |Sì |
| username |Specificare il nome utente per l'account utente. |Sì, se authenticationType è impostato su Basic. |
| password |Specifica la password per l'account utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì, se authenticationType è impostato su Basic. |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

>[!NOTE]
>Attualmente la connessione a Cassandra usando TLS non è supportata.

**Esempio:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
            "username": "<username>",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Cassandra.

Per copiare dati da Cassandra, impostare la proprietà type del set di dati su **CassandraTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **CassandraTable** | Sì |
| keyspace |Nome del keyspace o schema nel database Cassandra. |No (se per "CassandraSource" è specificato "query") |
| tableName |Nome della tabella in un database Cassandra. |No (se per "CassandraSource" è specificato "query") |

**Esempio:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia


Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Cassandra.

### <a name="cassandra-as-source"></a>Cassandra come origine

Per copiare dati da Cassandra, impostare il tipo di origine nell'attività di copia su **CassandraSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **CassandraSource** | Sì |
| query |Usare la query personalizzata per leggere i dati. Query SQL-92 o query CQL. Vedere il [riferimento a CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Quando si usa una query SQL, specificare **nome keyspace.nome tabella** per indicare la tabella su cui eseguire la query. |No (se nel set di dati sono specificati "tableName" e "keyspace"). |
| consistencyLevel |Il livello di coerenza specifica quante repliche devono rispondere a una richiesta di lettura prima della restituzione dei dati all'applicazione client. Cassandra controlla il numero di repliche specificato perché i dati soddisfino la richiesta di lettura. Per informazioni dettagliate, vedere [Configuring data consistency](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) (Configurazione della coerenza dei dati).<br/><br/>I valori consentiti sono: **ONE**, **TWO**, **THREE**, **QUORUM**, **ALL**, **LOCAL_QUORUM**, **EACH_QUORUM** e **LOCAL_ONE**. |No (il valore predefinito è `ONE`) |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Mapping dei tipi di dati per Cassandra

Quando si copiano dati da Cassandra, vengono usati i mapping seguenti tra i tipi di dati di Cassandra e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di Cassandra | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| ASCII |string |
| bigint |Int64 |
| BLOB |Byte[] |
| BOOLEAN |Boolean |
| DECIMAL |Decimal |
| DOUBLE |Double |
| FLOAT |Single |
| INET |string |
| INT |Int32 |
| TEXT |string |
| timestamp |Datetime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |string |
| VARINT |Decimal |

> [!NOTE]
> Per i tipi di raccolta (mappa, set, elenco e così via), vedere la sezione [Uso delle raccolte con una tabella virtuale](#work-with-collections-using-virtual-table) .
>
> I tipi definiti dall'utente non sono supportati.
>
> La lunghezza di una colonna Binary o String non può essere maggiore di 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Uso delle raccolte con una tabella virtuale

Azure Data Factory usa un driver ODBC integrato per connettersi ai dati di un database Cassandra e copiarli. Per i tipi di raccolta, fra cui mappa, set ed elenco, il driver normalizza di nuovo i dati in tabelle virtuali corrispondenti. In particolare, se una tabella contiene colonne della raccolta, il driver genera le tabelle virtuali seguenti:

* Una **tabella di base** che contiene gli stessi dati della tabella reale eccetto le colonne della raccolta. La tabella di base ha lo stesso nome della tabella reale che rappresenta.
* Una **tabella virtuale** per ogni colonna della raccolta che espande i dati nidificati. Alle tabelle virtuali che rappresentano le raccolte vengono assegnati nomi composti dal nome della tabella reale seguito dal separatore "*vt*" e dal nome della colonna.

Le tabelle virtuali fanno riferimento ai dati nella tabella reale, consentendo al driver di accedere ai dati denormalizzati. Per i dettagli vedere la sezione Esempio. È possibile accedere al contenuto delle raccolte Cassandra eseguendo query e join sulle tabelle virtuali.

### <a name="example"></a>Esempio

Ad esempio, "ExampleTable" è una tabella di un database Cassandra contenente una colonna chiave primaria integer denominata "pk_int", una colonna testo denominata value, una colonna elenco, una colonna mappa e una colonna set (denominata "StringSet").

| pk_int | Valore | Elenco | Mappa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valore di esempio 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"valore di esempio 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Il driver genera più tabelle virtuali per rappresentare questa singola tabella. Le colonne chiave esterna nelle tabelle virtuali fanno riferimento alle colonne chiave primaria nella tabella reale e indicano a quale riga della tabella reale corrisponde la riga della tabella virtuale.

La prima tabella virtuale è la tabella di base denominata "ExampleTable", illustrata nell'esempio seguente: 

| pk_int | Valore |
| --- | --- |
| 1 |"valore di esempio 1" |
| 3 |"valore di esempio 3" |

La tabella di base contiene gli stessi dati della tabella di database originale, tranne le raccolte, che vengono omesse da questa tabella ed espanse in altre tabelle virtuali.

Le tabelle seguenti illustrano le tabelle virtuali che normalizzano di nuovo i dati dalle colonne elenco, mappa e StringSet. Le colonne con nomi che terminano con "_index" o "_key" indicano la posizione dei dati all'interno dell'elenco o della mappa originale. Le colonne con nomi che terminano con "_value" contengono i dati espansi dalla raccolta.

**Tabella "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabella "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |u |

**Tabella "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
