---
title: Copiare dati da e in Salesforce
description: Informazioni su come copiare dati da Salesforce in archivi dati di sink supportati o da archivi dati di origine supportati in Salesforce usando un'attività di copia in una pipeline di data factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/13/2020
ms.openlocfilehash: 292d80f7fad796b2ee4f80478c55099148d7f855
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086694"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Copiare dati da e in Salesforce usando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-salesforce-connector.md)
> * [Versione corrente](connector-salesforce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Salesforce. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Salesforce è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Salesforce in qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio di dati di origine supportato in Salesforce. Per un elenco di archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats) .

In particolare, il connettore Salesforce supporta:

- Le edizioni Developer, Professional, Enterprise o Unlimited di Salesforce.
- La copia di dati da e nell'ambiente di produzione, nella sandbox e nel dominio personalizzato di Salesforce.

Il connettore Salesforce è basato sull'API REST/bulk di Salesforce. Per impostazione predefinita, il connettore USA [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) per copiare i dati da Salesforce e USA [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) per copiare i dati in Salesforce. È anche possibile impostare in modo esplicito la versione dell'API usata per leggere/scrivere dati tramite la [ `apiVersion` Proprietà](#linked-service-properties) nel servizio collegato.

## <a name="prerequisites"></a>Prerequisiti

In Salesforce deve essere abilitata l'autorizzazione API. Per altre informazioni, vedere [Enable API access in Salesforce by permission set](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Abilitare l'accesso al'API in Salesforce in base al set di autorizzazioni).

## <a name="salesforce-request-limits"></a>Limiti delle richieste Salesforce

Salesforce presenta limiti per le richieste API totali e per le richieste API simultanee. Tenere presente quanto segue:

- Se il numero di richieste simultanee supera il limite, si verifica una limitazione e vengono visualizzati errori casuali.
- Se il numero totale di richieste supera il limite, l'account di Salesforce viene bloccato per 24 ore.

In entrambi gli scenari è anche possibile che venga visualizzato il messaggio di errore "REQUEST_LIMIT_EXCEEDED" ("LIMITE_RICHIESTE_SUPERATO"). Per altre informazioni, vedere la sezione "API Request Limits" (Limiti delle richieste API) in [Salesforce developer limits](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm) (Limiti per sviluppatori di Salesforce).

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore Salesforce.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Salesforce sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su **Salesforce**. |Sì |
| environmentUrl | Specificare l'URL dell'istanza di Salesforce. <br> - Il valore predefinito è `"https://login.salesforce.com"`. <br> - Per copiare dati dalla sandbox, specificare `"https://test.salesforce.com"`. <br> - Per copiare dati dal dominio personalizzato, specificare ad esempio `"https://[domain].my.salesforce.com"`. |No |
| username |Specificare un nome utente per l'account utente. |Sì |
| password |Specificare la password per l'account utente.<br/><br/>Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| securityToken |Specificare un token di sicurezza per l'account utente. <br/><br/>Per informazioni generali sui token di sicurezza, vedere [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sicurezza e API). Il token di sicurezza può essere ignorato solo se si aggiunge l'IP del Integration Runtime all' [elenco di indirizzi IP attendibili](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) in Salesforce. Quando si usa Azure IR, fare riferimento a [Azure Integration Runtime indirizzi IP](azure-integration-runtime-ip-addresses.md).<br/><br/>Per istruzioni su come ottenere e reimpostare un token di sicurezza, vedere [ottenere un token di sicurezza](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |No |
| apiVersion | Specificare la versione REST/API bulk di Salesforce da usare, ad esempio `48.0` . Per impostazione predefinita, il connettore USA [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) per copiare i dati da Salesforce e USA [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) per copiare i dati in Salesforce. | No |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink se il servizio collegato all'origine non dispone di un runtime di integrazione |

>[!IMPORTANT]
>Quando si copiano dati in Salesforce, non è possibile usare il runtime di integrazione di Azure predefinito per eseguire l'attività di copia. In altre parole, se per il servizio collegato all'origine non viene specificato un runtime di integrazione, [creare un Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con una posizione vicina all'istanza di Salesforce. Associare il servizio collegato di Salesforce come nell'esempio seguente.

**Esempio: Archiviare le credenziali in Data Factory**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: Archiviare le credenziali in Key Vault**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Salesforce.

Per copiare dati da e in Salesforce, impostare la proprietà type del set di dati su **SalesforceObject**. Sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **SalesforceObject**.  | Sì |
| objectApiName | Nome dell'oggetto di Salesforce da cui recuperare i dati. | No per l'origine, Sì per il sink |

> [!IMPORTANT]
> La parte "__c" del**nome dell'API** è necessaria per qualsiasi oggetto personalizzato.

![Nome API della connessione Salesforce di Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Esempio:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>Per compatibilità con le versioni precedenti, quando si copiano dati da Salesforce, l'uso del set di dati di tipo "RelationalTable" precedente continua a funzionare, anche se viene consigliato di passare all'uso del nuovo tipo "SalesforceObject".

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **RelationalTable**. | Sì |
| tableName | Nome della tabella in Salesforce. | No (se nell'origine dell'attività è specificato "query") |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Salesforce.

### <a name="salesforce-as-a-source-type"></a>Salesforce come tipo di origine

Per copiare dati da Salesforce, impostare il tipo di origine nell'attività di copia su **SalesforceSource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **SalesforceSource**. | Sì |
| query |Usare la query personalizzata per leggere i dati. È possibile usare una query SQL-92 o una query [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Vedere altri suggerimenti nella sezione [Suggerimenti di query](#query-tips). Se la proprietà query non viene specificata, tutti i dati dell'oggetto Salesforce specificato in "objectApiName" nel set di dati verranno recuperati. | No (se "objectApiName" è specificato nel set di dati) |
| readBehavior | Indica se eseguire query sui record esistenti o su tutti i record inclusi quelli eliminati. Se non specificato, il comportamento predefinito è quello indicato per primo. <br>Valori consentiti: **query** (predefinito), **queryAll**.  | No |

> [!IMPORTANT]
> La parte "__c" del**nome dell'API** è necessaria per qualsiasi oggetto personalizzato.

![Elenco dei nomi API della connessione Salesforce di Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Per compatibilità con le versioni precedenti, quando si copiano dati da Salesforce, se si usa il tipo "RelationalSource" precedente, l'origine continua a funzionare anche se viene consigliato di passare all'uso del nuovo tipo "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce come tipo di sink

Per copiare dati in Salesforce, impostare il tipo di sink nell'attività di copia su **SalesforceSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **SalesforceSink**. | Sì |
| writeBehavior | Comportamento dell'azione di scrittura per l'operazione.<br/>I valori consentiti sono: **Insert** e **Upsert**. | No (il valore predefinito è Insert) |
| externalIdFieldName | Nome del campo ID esterno per l'operazione upsert. Il campo specificato deve essere definito come "campo ID esterno" nell'oggetto Salesforce. Non può includere valori NULL nei dati di input corrispondenti. | Sì per "Upsert" |
| writeBatchSize | Conteggio delle righe di dati scritti da Salesforce in ogni batch. | No (il valore predefinito è 5.000) |
| ignoreNullValues | Indica se ignorare i valori NULL dai dati di input durante un'operazione di scrittura.<br/>I valori consentiti sono **true** e **false**.<br>- **True**: i dati nell'oggetto di destinazione rimangono invariati quando si esegue un'operazione di upsert o aggiornamento. Inserire un valore predefinito definito quando si esegue un'operazione di inserimento.<br/>- **False**: i dati nell'oggetto di destinazione vengono aggiornati a NULL quando si esegue un'operazione di upsert o aggiornamento. Inserire un valore NULL quando si esegue un'operazione di inserimento. | No (il valore predefinito è false) |

**Esempio: Sink Salesforce in un'attività di copia**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Suggerimenti di query

### <a name="retrieve-data-from-a-salesforce-report"></a>Recuperare dati da un report di Salesforce

È possibile recuperare dati dai report di Salesforce specificando una query come `{call "<report name>"}`. Un esempio è `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Recuperare i record eliminati dal Cestino di Salesforce

Per recuperare i record eliminati temporaneamente dal Cestino di Salesforce, è possibile specificare `readBehavior` come `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Differenza tra la sintassi di query SOQL e SQL

Quando si copiano dati da Salesforce, è possibile usare una query SOQL o SQL. Si noti che queste due query hanno una sintassi e un supporto di funzionalità diversi. Non mischiarli. Si consiglia di usare la query SOQL, supportata in modo nativo da Salesforce. Nella tabella seguente sono elencate le differenze principali:

| Sintassi | Modalità SOQL | Modalità SQL |
|:--- |:--- |:--- |
| Selezione di colonne | È necessario enumerare i campi da copiare nella query, ad esempio`SELECT field1, filed2 FROM objectname` | `SELECT *` è supportata oltre alla selezione della colonna. |
| Virgolette | I nomi di campo/oggetto non possono essere racchiusi tra virgolette. | I nomi di campo/oggetto non possono essere racchiusi tra virgolette, ad es. `SELECT "id" FROM "Account"` |
| Formato Datetime |  Fare riferimento a informazioni dettagliate [qui](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) ed esempi nella sezione successiva. | Fare riferimento a informazioni dettagliate [qui](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) ed esempi nella sezione successiva. |
| Valori booleani | Rappresentati come `False` e `True`, ad esempio `SELECT … WHERE IsDeleted=True`. | Rappresentati come 0 o 1, ad esempio `SELECT … WHERE IsDeleted=1`. |
| Ridenominazione delle colonne | Non supportato. | Supportata, ad es. `SELECT a AS b FROM …`. |
| Relazione | Supportata, ad es. `Account_vod__r.nvs_Country__c`. | Non supportato. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Recuperare i dati usando una clausola where nella colonna DateTime

Quando si specifica la query SOQL o SQL, prestare attenzione alla differenza di formato di DateTime. Ad esempio:

* **Esempio SOQL**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Esempio SQL**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>Errore di MALFORMED_QUERY: troncato

Se si è verificato un errore di "MALFORMED_QUERY: troncato", in genere è dovuto a una colonna di tipo JunctionIdList nei dati e a Salesforce è possibile limitare il supporto di tali dati con un numero elevato di righe. Per attenuare, provare a escludere la colonna JunctionIdList o a limitare il numero di righe da copiare (è possibile partizionare in più esecuzioni di attività di copia).

## <a name="data-type-mapping-for-salesforce"></a>Mapping dei tipi di dati per Salesforce

Quando si copiano dati da Salesforce, vengono usati i mapping seguenti tra i tipi di dati di Salesforce e i tipi di dati provvisori di Data Factory. Per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipo di dati di Salesforce | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| Numero automatico |Stringa |
| Casella di controllo |Boolean |
| Valuta |Decimal |
| Data |Datetime |
| Data/ora |Datetime |
| E-mail |Stringa |
| ID |Stringa |
| Relazione di ricerca |Stringa |
| Elenco a discesa seleziona multipla |Stringa |
| Number |Decimal |
| Percentuale |Decimal |
| Telefono |Stringa |
| Elenco a discesa |Stringa |
| Testo |Stringa |
| Area di testo |Stringa |
| Area di testo (Long) |Stringa |
| Area di testo (Rich) |Stringa |
| Testo (Crittografato) |Stringa |
| URL |Stringa |

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
