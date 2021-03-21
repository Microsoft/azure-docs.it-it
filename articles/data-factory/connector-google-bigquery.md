---
title: Copiare dati da Google BigQuery usando Azure Data Factory
description: Informazioni su come copiare dati da Google BigQuery in archivi dati di sink supportati usando un'attività di copia in una pipeline di data factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: e3fcaa6c1542578d983461623da743724a3114d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389689"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Copiare dati da Google BigQuery usando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Google BigQuery. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Google BigQuery è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Google BigQuery a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Data Factory fornisce un driver predefinito per abilitare la connettività. Non è pertanto necessario installare manualmente un driver per usare questo connettore.

>[!NOTE]
>Questo connettore Google BigQuery si basa sulle API BigQuery. Tenere presente che BigQuery limita la velocità massima delle richieste in arrivo e applica le quote appropriate in base al progetto: vedere la sezione [Quotas & Limits - API requests](https://cloud.google.com/bigquery/quotas#api_requests) (Limiti e quote - Richieste API). Assicurarsi di non attivare troppe richieste simultanee verso l'account.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore Google BigQuery.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Google BigQuery sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **GoogleBigQuery**. | Sì |
| project | ID di progetto del progetto BigQuery su cui eseguire query.  | Sì |
| additionalProjects | A elenco delimitato da virgole di ID di progetto dei progetti BigQuery a cui accedere.  | No |
| requestGoogleDriveScope | Indica se richiedere l'accesso a Google Drive. L'abilitazione dell'accesso a Google Drive consente di abilitare il supporto per le tabelle federate che combinano dati di BigQuery con dati da Google Drive. Il valore predefinito è **false**.  | No |
| authenticationType | Meccanismo di autenticazione OAuth 2.0 usato per l'autenticazione. È possibile usare ServiceAuthentication solo sul runtime di integrazione self-hosted. <br/>I valori consentiti sono **UserAuthentication** e **ServiceAuthentication**. Fare riferimento alle sezioni sotto questa tabella per altre proprietà e altri esempi JSON per questi tipi di autenticazione. | Sì |

### <a name="using-user-authentication"></a>Uso dell'autenticazione utente

Impostare la proprietà "authenticationType" su **UserAuthentication** e specificare le proprietà seguenti insieme alle proprietà generiche descritte nella precedente sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| clientId | ID dell'applicazione usata per generare il token di aggiornamento. | No |
| clientSecret | Segreto dell'applicazione usata per generare il token di aggiornamento. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| refreshToken | Token di aggiornamento ottenuto da Google e usato per autorizzare l'accesso a BigQuery. Per informazioni su come ottenerne uno, vedere [Obtaining OAuth 2.0 access tokens](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) (Ottenere token di accesso OAuth 2.0) e questo [blog della community](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |

**Esempio:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Uso dell'autenticazione del servizio

Impostare la proprietà "authenticationType" su **ServiceAuthentication** e specificare le proprietà seguenti insieme alle proprietà generiche descritte nella precedente sezione. È possibile usare questo tipo di autenticazione solo sul runtime di integrazione self-hosted.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| email | ID di posta elettronica dell'account del servizio usato per ServiceAuthentication. Può essere usato solo sul runtime di integrazione self-hosted.  | No |
| keyFilePath | Percorso completo per il file di chiave con estensione p12 usato per autenticare l'indirizzo di posta elettronica dell'account del servizio. | No |
| trustedCertPath | Percorso completo del file con estensione PEM contenente i certificati CA attendibili utilizzati per verificare il server quando si esegue la connessione tramite TLS. Questa proprietà può essere impostata solo quando si utilizza TLS in Integration Runtime indipendenti. Il valore predefinito è il file cacerts.pem installato con il runtime di integrazione.  | No |
| useSystemTrustStore | Specifica se usare o meno un certificato CA dall'archivio di attendibilità di sistema o da un file PEM specificato. Il valore predefinito è **false**.  | No |

**Esempio:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di Google BigQuery.

Per copiare dati da Google BigQuery, impostare la proprietà type del set di dati su **GoogleBigQueryObject**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **GoogleBigQueryObject** | Sì |
| dataset | Nome del set di dati di Google BigQuery. |No (se nell'origine dell'attività è specificato "query")  |
| tabella | Nome della tabella. |No (se nell'origine dell'attività è specificato "query")  |
| tableName | Nome della tabella. Questa proprietà è supportata per garantire la compatibilità con le versioni precedenti. Per i nuovi carichi di lavoro, usare `dataset` e `table`. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dal tipo di origine Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource come tipo di origine

Per copiare dati da Google BigQuery, impostare il tipo di origine nell'attività di copia su **GoogleBigQuerySource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **GoogleBigQuerySource**. | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Un esempio è `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
