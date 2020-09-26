---
title: Copiare dati da MongoDB usando legacy
description: Informazioni su come copiare dati da Mongo DB in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory legacy.
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 7cf4be078a7bee0bedbeac4326acb9ca290cde88
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331982"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory-legacy"></a>Copiare dati da MongoDB usando Azure Data Factory (legacy)

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Versione corrente](connector-mongodb.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database MongoDB. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

>[!IMPORTANT]
>Azure Data Factory ha rilasciato un nuovo connettore MongoDB che offre un migliore supporto di MongoDB nativo rispetto a questa implementazione basata su ODBC. Per conoscere i dettagli, fare riferimento all'articolo sul [connettore MongoDB](connector-mongodb.md). Questo connettore MongoDB legacy continua a essere supportato così com'è per garantire compatibilità con le versioni precedenti, mentre per i nuovi carichi di lavoro è necessario usare il nuovo connettore.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database MongoDB in un qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore MongoDB supporta:

- MongoDB **versioni 2.4, 2.6, 3.0, 3.2, 3.4 e 3.6**.
- La copia dei dati usando l'autenticazione **Di base** o **Anonima**.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Il runtime di integrazione offre un driver per MongoDB predefinito e non è quindi necessario installare manualmente alcun driver quando si copiano dati da MongoDB.

## <a name="getting-started"></a>Guida introduttiva

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di data factory specifiche per il connettore MongoDB.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di MongoDB sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su **MongoDb** |Sì |
| server |Indirizzo IP o nome host del server MongoDB. |Sì |
| port |Porta TCP che il server MongoDB usa per ascoltare le connessioni client. |No (il valore predefinito è 27017) |
| databaseName |Nome del database MongoDB a cui si vuole accedere. |Sì |
| authenticationType | Tipo di autenticazione usato per connettersi al database MongoDB.<br/>I valori consentiti sono: **Basic**e **Anonymous**. |Sì |
| username |Account utente per accedere a MongoDB. |Sì (se si usa l'autenticazione di base). |
| password |Password per l'utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì (se si usa l'autenticazione di base). |
| authSource |Nome del database MongoDB che si vuole usare per controllare le credenziali di autenticazione. |No. Per l'autenticazione di base il valore predefinito usa l'account di amministrazione e il database specificati usando la proprietà databaseName. |
| enableSsl | Specifica se le connessioni al server sono crittografate con TLS. Il valore predefinito è false.  | No |
| allowSelfSignedServerCert | Specifica se consentire o meno i certificati autofirmati dal server. Il valore predefinito è false.  | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md). Per il set di dati MongoDB sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **MongoDbCollection** | Sì |
| collectionName |Nome della raccolta nel database MongoDB. |Sì |

**Esempio:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
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
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **MongoDbSource** | Sì |
| query |Usare la query SQL-92 personalizzata per leggere i dati. Ad esempio: selezionare * da MyTable. |No (se "collectionName" nel set di dati è specificato) |

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
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Quando si specifica la query SQL, prestare attenzione al formato di DateTime. Ad esempio: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` o per usare il parametro `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schema da Data Factory

Azure Data Factory servizio deduce lo schema da una raccolta MongoDB usando gli **ultimi 100 documenti** nella raccolta. Se questi 100 documenti non contengono lo schema completo, alcune colonne possono essere ignorate durante l'operazione di copia.

## <a name="data-type-mapping-for-mongodb"></a>Mapping del tipo di dati per MongoDB

Quando si copiano dati da MongoDB, vengono usati i mapping seguenti tra i tipi di dati MongoDB e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di MongoDB | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| Binary |Byte[] |
| Boolean |Boolean |
| Data |Datetime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |string |
| string |string |
| UUID |Guid |
| Oggetto |Rinormalizzato in colonne rese flat con "_" come separatore annidato |

> [!NOTE]
> Per informazioni sul supporto di matrici che usano tabelle virtuali, vedere la sezione [Supporto per tipi complessi con tabelle virtuali](#support-for-complex-types-using-virtual-tables).
>
> I tipi di dati MongoDB seguenti non sono attualmente supportati: DBPointer, JavaScript, chiave Max/Min, Espressione regolare, Simbolo, Timestamp, Undefined.

## <a name="support-for-complex-types-using-virtual-tables"></a>Supporto per tipi complessi con tabelle virtuali

Azure Data Factory usa un driver ODBC integrato per connettersi ai dati di un database MongoDB e copiarli. Per i tipi complessi come le matrici o gli oggetti con tipi diversi tra i documenti, il driver rinormalizza i dati nelle tabelle virtuali corrispondenti. In particolare, se una tabella contiene tali colonne, il driver genera le tabelle virtuali seguenti:

* Una **tabella di base**che contiene gli stessi dati della tabella reale eccetto le colonne di tipo complesso. La tabella di base ha lo stesso nome della tabella reale che rappresenta.
* Una **tabella virtuale** per ogni colonna di tipo complesso che espande i dati annidati. Alle tabelle virtuali vengono assegnati nomi composti dal nome della tabella reale seguito dal separatore "_" e dal nome della matrice o dell'oggetto.

Le tabelle virtuali fanno riferimento ai dati nella tabella reale, consentendo al driver di accedere ai dati denormalizzati. È possibile accedere al contenuto delle matrici MongoDB eseguendo query e join sulle tabelle virtuali.

### <a name="example"></a>Esempio

Ad esempio, ExampleTable di seguito è una tabella MongoDB che contiene una colonna con una matrice di oggetti in ogni cella, Fatture, e una colonna con una matrice di tipi scalari, Classificazioni.

| _id | Nome del cliente | Fatture | Livello di servizio | Classificazioni |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", elemento:"tostapane", prezzo:"456", sconto:"0,2"}, {invoice_id:"124", elemento:"forno", prezzo: "1235", sconto: "0,2"}] |Argento |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", elemento:"frigorifero", prezzo: "12543", sconto: "0,0"}] |Oro |[1,2] |

Il driver genera più tabelle virtuali per rappresentare questa singola tabella. La prima tabella virtuale è la tabella di base denominata "ExampleTable", illustrata nell'esempio. La tabella di base contiene tutti i dati della tabella originale, ma i dati dalle matrici sono stati omessi e vengono espansi nelle tabelle virtuali.

| _id | Nome del cliente | Livello di servizio |
| --- | --- | --- |
| 1111 |ABC |Argento |
| 2222 |XYZ |Oro |

Le tabelle seguenti illustrano le tabelle virtuali che rappresentano le matrici originali nell'esempio. In queste tabelle è possibile vedere:

* Un riferimento alla colonna della chiave primaria originale corrispondente alla riga della matrice originale (con la colonna _id)
* Un'indicazione della posizione dei dati all'interno della matrice originale
* I dati espansi per ogni elemento all'interno della matrice

**Tabella "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Discount |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |toaster |456 |0,2 |
| 1111 |1 |124 |oven |1235 |0,2 |
| 2222 |0 |135 |fridge |12543 |0,0 |

**Tabella "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
