---
title: Copiare dati da origini OData tramite Azure Data FactoryCopy data from OData sources by using Azure Data Factory
description: Informazioni su come copiare dati da origini OData in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
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
ms.openlocfilehash: c2fe6b6cc7b52dda9f2beffa444f1965723ea92a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416919"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Copiare dati da un'origine OData tramite Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-odata-connector.md)
> * [Versione corrente](connector-odata.md)

Questo articolo descrive come usare l'attività di copia in Azure Data Factory per copiare dati da un'origine OData. L'articolo è basato su [Attività di copia in Azure Data Factory](copy-activity-overview.md), dove viene presentata una panoramica generale dell'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore OData è supportato per le attività seguenti:This OData connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare dati da un'origine OData a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati dall'attività di copia come origini e sink, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore OData supporta:

- OData versioni 3.0 e 4.0.
- Copia dei dati utilizzando una delle seguenti autenticazioni: **Anonymous**, **Basic**, **Windows**e **AAD service principal**.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti presentano informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche per un connettore OData.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato OData sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **OData**. |Sì |
| url | URL radice del servizio OData. |Sì |
| authenticationType | Tipo di autenticazione usato per la connessione all'origine OData. I valori consentiti sono **Anonymous**, **Basic**, **Windows**e **AadServicePrincipal**. OAuth basato sull'utente non è supportato. | Sì |
| userName | Specificare **userName** se si usa l'autenticazione di base o di Windows. | No |
| password | Specificare la proprietà **password** per l'account utente indicato per **userName**. Contrassegnare questo campo come di tipo **SecureString** per l'archiviazione sicura in Data Factory. È anche possibile [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| servicePrincipalId | Specificare l'ID client. dell'applicazione Azure Active Directory. | No |
| aadServicePrincipalCredentialType | Specificare il tipo di credenziale da usare per l'autenticazione dell'entità servizio. I valori consentiti sono: `ServicePrincipalKey` o `ServicePrincipalCert`. | No |
| servicePrincipalKey | Specificare la chiave dell'applicazione Azure Active Directory. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| servicePrincipalEmbeddedCert | Specificare il certificato con codificata base64 dell'applicazione registrata in Azure Active Directory. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| servicePrincipalEmbeddedCertPassword | Specificare la password del certificato se il certificato è protetto con una password. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md).  | No|
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Recuperarle passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | No |
| aadResourceId | Specificare la risorsa AAD per cui si sta richiedendo l'autorizzazione.| No |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per ulteriori informazioni, vedere la sezione [Prerequisiti.](#prerequisites) Se questa proprietà non è specificata, viene usato il tipo Azure Integration Runtime predefinito. |No |

**Esempio 1: Uso dell'autenticazione anonima**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 2: Uso dell'autenticazione di base**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**Esempio 3: Uso dell'autenticazione di Windows**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**Esempio 4: Utilizzo dell'autenticazione con chiave dell'entità servizioExample 4: Using service principal key authentication**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Esempio 5: Utilizzo dell'autenticazione del certificato dell'entità servizioExample 5: Using service principal certificate authentication**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Questa sezione presenta un elenco delle proprietà supportate dal set di dati OData.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md). 

Per copiare dati da OData, impostare la proprietà**type** del set di dati su **ODataResource**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del set di dati deve essere impostata su **ODataResource**. | Sì |
| path | Percorso della risorsa OData. | Sì |

**Esempio**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione presenta un elenco delle proprietà supportate dall'origine OData.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData come origine

Per copiare i dati da OData, nella sezione Copia origine attività sono supportate le proprietà seguenti:To copy data from OData, the following properties are supported in the Copy Activity **source** section:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **ODataSource**. | Sì |
| query | Opzioni di query OData per filtrare i dati. Esempio: `"$select=Name,Description&$top=5"`.<br/><br/>**Nota**: il connettore OData copia dati dall'URL combinato: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`. Per altre informazioni, vedere [OData URL components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/) (Componenti dell'URL di OData). | No |

**Esempio**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se si `RelationalSource` utilizza l'origine digitata, questa è comunque supportata così com'è, mentre viene consigliato di utilizzare quella nuova in futuro.

## <a name="data-type-mapping-for-odata"></a>Mapping dei tipi di dati per OData

Quando si copiano dati da OData, vengono usati i mapping seguenti tra i tipi di dati di OData e i tipi di dati provvisori di Azure Data Factory. Per informazioni sul modo in cui l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipo di dati di OData | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | string |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> I tipi di dati complessi di OData (come **Object**) non sono supportati.


## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).