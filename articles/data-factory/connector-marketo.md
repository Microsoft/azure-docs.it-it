---
title: Copiare dati da Marketo tramite Azure Data Factory (anteprima)
description: Informazioni su come copiare dati da Marketo in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: efb450f4da58c73c134d9f6b6aad6193f786912d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415013"
---
# <a name="copy-data-from-marketo-using-azure-data-factory-preview"></a>Copiare dati da Marketo tramite Azure Data Factory (anteprima)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Marketo. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!IMPORTANT]
> Questo connettore è attualmente disponibile in anteprima. È possibile provarlo e inviare commenti e suggerimenti. Se si vuole accettare una dipendenza dai connettori in versione di anteprima nella propria soluzione, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Marketo è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Marketo a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

>[!NOTE]
>Il connettore Marketo è basato sull'API REST di Marketo. Tenere presente che Marketo presenta un [limite di richieste simultanee](https://developers.marketo.com/rest-api/) sul lato servizio. Se si verificano errori, ad esempio durante il tentativo di usare l'API REST con il superamento di 20 secondi del limite di velocità massimo pari a 100 (errore 606) oppure con il raggiungimento del limite di accessi simultanei pari a 10 (errore 615), è consigliabile ridurre le esecuzioni delle attività di copia simultanee per diminuire il numero di richieste inviate al servizio.

## <a name="getting-started"></a>Guida introduttiva

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di data factory specifiche per il connettore Marketo.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Marketo sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Marketo** | Sì |
| endpoint | Endpoint del server Marketo (ad esempio 123-ABC-321.mktorest.com).  | Sì |
| clientId | ID client del servizio Marketo.  | Sì |
| clientSecret | Segreto client del servizio Marketo. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| useEncryptedEndpoints | Specifica se gli endpoint dell'origine dati vengono crittografati tramite HTTPS. Il valore predefinito è true.  | No |
| useHostVerification | Specifica se è necessario che il nome host nel certificato del server corrisponda al nome host del server durante la connessione tramite TLS. Il valore predefinito è true.  | No |
| usePeerVerification | Specifica se verificare l'identità del server durante la connessione tramite TLS. Il valore predefinito è true.  | No |

**Esempio:**

```json
{
    "name": "MarketoLinkedService",
    "properties": {
        "type": "Marketo",
        "typeProperties": {
            "endpoint" : "123-ABC-321.mktorest.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di Marketo.

Per copiare dati da Marketo, impostare la proprietà type del set di dati su **MarketoObject**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **MarketoObject** | Sì |
| tableName | Nome della tabella. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "MarketoDataset",
    "properties": {
        "type": "MarketoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Marketo linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Marketo.

### <a name="marketo-as-source"></a>Marketo come origine

Per copiare dati da Marketo, impostare il tipo di origine nell'attività di copia su **MarketoSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **MarketoSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM Activitiy_Types"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromMarketo",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Marketo input dataset name>",
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
                "type": "MarketoSource",
                "query": "SELECT top 1000 * FROM Activitiy_Types"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
