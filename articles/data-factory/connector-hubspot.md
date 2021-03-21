---
title: Copiare dati da HubSpot usando Azure Data Factory
description: Informazioni su come copiare dati da HubSpot in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: 7f2ab069be7985376c44f2cd2ae3ccdd728d4e2a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385660"
---
# <a name="copy-data-from-hubspot-using-azure-data-factory"></a>Copiare dati da HubSpot usando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da HubSpot. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore HubSpot è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)


È possibile copiare dati da HubSpot a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di data factory specifiche per il connettore HubSpot.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di HubSpot sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Hubspot** | Sì |
| clientId | ID client associato all'applicazione HubSpot. Informazioni su come creare un'app in HubSpot da [qui](https://developers.hubspot.com/docs/faq/how-do-i-create-an-app-in-hubspot). | Sì |
| clientSecret | Segreto client associato all'applicazione HubSpot. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| accessToken | Il token di accesso ottenuto durante l'autenticazione iniziale dell'integrazione OAuth. Informazioni su come ottenere un token di accesso con l'ID client e il segreto da [qui](https://developers.hubspot.com/docs/methods/oauth2/get-access-and-refresh-tokens). Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| refreshToken | Il token di aggiornamento ottenuto durante l'autenticazione iniziale dell'integrazione OAuth. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| useEncryptedEndpoints | Specifica se gli endpoint dell'origine dati vengono crittografati tramite HTTPS. Il valore predefinito è true.  | No |
| useHostVerification | Specifica se è necessario che il nome host nel certificato del server corrisponda al nome host del server durante la connessione tramite TLS. Il valore predefinito è true.  | No |
| usePeerVerification | Specifica se verificare l'identità del server durante la connessione tramite TLS. Il valore predefinito è true.  | No |

**Esempio:**

```json
{
    "name": "HubSpotLinkedService",
    "properties": {
        "type": "Hubspot",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "accessToken": {
                "type": "SecureString",
                "value": "<accessToken>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di HubSpot.

Per copiare dati da HubSpot, impostare la proprietà type del set di dati su **HubspotObject**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **HubspotObject** | Sì |
| tableName | Nome della tabella. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "HubSpotDataset",
    "properties": {
        "type": "HubspotObject",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<HubSpot linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine di HubSpot.

### <a name="hubspotsource-as-source"></a>HubspotSource come origine

Per copiare dati da HubSpot, impostare il tipo di origine nell'attività di copia su **HubspotSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **HubspotSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM Companies where Company_Id = xxx"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromHubspot",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HubSpot input dataset name>",
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
                "type": "HubspotSource",
                "query": "SELECT * FROM Companies where Company_Id = xxx"
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
