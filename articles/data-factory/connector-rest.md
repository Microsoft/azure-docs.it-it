---
title: Copiare dati da e in un endpoint REST utilizzando Azure Data Factory
description: Informazioni su come copiare dati da un'origine REST cloud o locale in archivi dati sink supportati o da un archivio dati di origine supportato a un sink REST usando un'attività di copia in una pipeline di Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: jingwang
ms.openlocfilehash: 972a7b32e6308c3aa8a3b42705038838dae9b2be
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369884"
---
# <a name="copy-data-from-and-to-a-rest-endpoint-by-using-azure-data-factory"></a>Copiare dati da e in un endpoint REST utilizzando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in un endpoint REST. L'articolo è basato su [Attività di copia in Azure Data Factory](copy-activity-overview.md), dove viene presentata una panoramica generale dell'attività di copia.

La differenza tra questo connettore REST, il [connettore http](connector-http.md)e il [connettore della tabella Web](connector-web-table.md) è:

- Il **connettore Rest** supporta specificamente la copia di dati da API RESTful; 
- Il **connettore http** è generico per recuperare i dati da qualsiasi endpoint HTTP, ad esempio per scaricare il file. Prima di questo connettore REST è possibile che si verifichi l'uso del connettore HTTP per copiare i dati dall'API RESTful, che è supportata ma meno funzionale rispetto al connettore REST.
- Il **connettore Tabella Web** estrae il contenuto della tabella da una pagina Web HTML.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un'origine REST in qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio dati di origine supportato a un sink REST. Per un elenco degli archivi dati supportati dall'attività di copia come origini e sink, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, questo connettore REST generico supporta:

- Copiare i dati da un endpoint REST usando i metodi **Get** o **post** e copiando i dati in un endpoint REST usando i metodi **post**, **put** o **patch** .
- Copia dei dati con una delle seguenti autenticazioni: **anonima**, di **base**, **entità servizio AAD** e **identità gestite per le risorse di Azure**.
- La **[paginazione](#pagination-support)** nelle API REST.
- Per REST come origine, copiare la risposta JSON REST [così com'è](#export-json-response-as-is) o analizzarla usando il [mapping dello schema](copy-activity-schema-and-type-mapping.md#schema-mapping). È supportato solo il payload della risposta in **JSON**.

> [!TIP]
> Per testare una richiesta di recupero dei dati prima di configurare il connettore REST in Data Factory, fare riferimento alla specifica dell'API per i requisiti relativi a intestazione e corpo. È possibile usare strumenti come Postman o un Web browser per la convalida.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti offrono informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche del connettore REST.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato REST sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** deve essere impostata su **RestService**. | Sì |
| url | URL di base del servizio REST. | Sì |
| enableServerCertificateValidation | Indica se convalidare il certificato TLS/SSL sul lato server per la connessione all'endpoint. | No<br /> (il valore predefinito è **true**) |
| authenticationType | Tipo di autenticazione usato per connettersi al servizio REST. I valori consentiti sono **Anonymous**, **Basic**, **AadServicePrincipal** e **ManagedServiceIdentity**. Per altre proprietà ed esempi su ogni valore, vedere le sezioni corrispondenti di seguito. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites). Se non è specificata, questa proprietà usa il tipo Azure Integration Runtime predefinito. |No |

### <a name="use-basic-authentication"></a>Usare l'autenticazione di base

Impostare la proprietà **authenticationType** su **Basic**. Oltre alle proprietà generiche descritte nella sezione precedente, specificare le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| userName | Nome utente da usare per accedere all'endpoint REST. | Sì |
| password | Password per l'utente (valore di **userName**). Contrassegnare questo campo come di tipo **SecureString** per l'archiviazione sicura in Data Factory. È anche possibile [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |

**Esempio**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>Usare l'autenticazione basata sull'entità servizio AAD

Impostare la proprietà **authenticationType** su **AadServicePrincipal**. Oltre alle proprietà generiche descritte nella sezione precedente, specificare le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| servicePrincipalId | Specificare l'ID client. dell'applicazione Azure Active Directory. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione Azure Active Directory. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Recuperarle passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |
| aadResourceId | Specificare la risorsa AAD richiesta per l'autorizzazione, ad esempio `https://management.core.windows.net` .| Sì |
| azureCloudType | Per l'autenticazione dell'entità servizio, specificare il tipo di ambiente cloud di Azure in cui è registrata l'applicazione AAD. <br/> I valori consentiti sono **AzurePublic**, **AzureChina**, **AzureUsGovernment** e **AzureGermany**. Per impostazione predefinita, viene usato l'ambiente cloud del data factory. | No |

**Esempio**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Usare le entità gestite per l'autenticazione delle risorse di Azure

Impostare la proprietà **authenticationType** su **ManagedServiceIdentity**. Oltre alle proprietà generiche descritte nella sezione precedente, specificare le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| aadResourceId | Specificare la risorsa AAD richiesta per l'autorizzazione, ad esempio `https://management.core.windows.net` .| Sì |

**Esempio**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Questa sezione contiene un elenco delle proprietà supportate dal set di dati REST. 

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md). 

Per copiare dati da REST, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del set di dati deve essere impostata su **RestResource**. | Sì |
| relativeUrl | URL relativo della risorsa che contiene i dati. Quando questa proprietà non è specificata, viene usato solo l'URL indicato nella definizione del servizio collegato. Il connettore HTTP copia i dati dall'URL combinato: `[URL specified in linked service]/[relative URL specified in dataset]` . | No |

Se si imposta `requestMethod` , `additionalHeaders` `requestBody` e `paginationRules` nel set di dati, è ancora supportata così com'è, mentre si consiglia di usare il nuovo modello nell'attività in futuro.

**Esempio:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink REST.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST come origine

Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **RestSource**. | Sì |
| requestMethod | Metodo HTTP. I valori consentiti sono **Get** (impostazione predefinita) e **post**. | No |
| additionalHeaders | Intestazioni richiesta HTTP aggiuntive. | No |
| requestBody | Corpo della richiesta HTTP. | No |
| paginationRules | Regole di paginazione per comporre le richieste di pagina successive. Per informazioni dettagliate, vedere la sezione [Supporto della paginazione](#pagination-support). | No |
| httpRequestTimeout | Timeout (valore di **TimeSpan**) durante il quale la richiesta HTTP attende una risposta. Si tratta del timeout per ottenere una risposta, non per leggere i dati della risposta. Il valore predefinito è **00:01:40**.  | No |
| requestInterval | Periodo di attesa prima di inviare la richiesta per la pagina successiva. Il valore predefinito è **00:00:01** |  No |

>[!NOTE]
>REST Connector ignora qualsiasi intestazione "Accept" specificata in `additionalHeaders` . Poiché REST Connector supporta solo la risposta in JSON, viene generata automaticamente un'intestazione di `Accept: application/json` .

**Esempio 1: uso del metodo Get con l'impaginazione**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Esempio 2: Uso del metodo POST**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="rest-as-sink"></a>REST come sink

Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** del sink dell'attività di copia deve essere impostata su **RestSink**. | Sì |
| requestMethod | Metodo HTTP. I valori consentiti sono **post** (impostazione predefinita), **put** e **patch**. | No |
| additionalHeaders | Intestazioni richiesta HTTP aggiuntive. | No |
| httpRequestTimeout | Timeout (valore di **TimeSpan**) durante il quale la richiesta HTTP attende una risposta. Questo valore è il timeout per ottenere una risposta, non il timeout per la scrittura dei dati. Il valore predefinito è **00:01:40**.  | No |
| requestInterval | Intervallo di tempo tra le diverse richieste in millisecondi. Il valore dell'intervallo di richiesta deve essere un numero compreso tra [10, 60000]. |  No |
| httpCompressionType | Tipo di compressione HTTP da utilizzare durante l'invio di dati con un livello di compressione ottimale. I valori consentiti sono **None** e **gzip**. | No |
| writeBatchSize | Numero di record da scrivere nel sink REST per batch. Il valore predefinito è 10000. | No |

REST Connector As sink funziona con le API REST che accettano JSON. I dati verranno inviati in JSON con il modello seguente. Se necessario, è possibile usare il [mapping dello schema](copy-activity-schema-and-type-mapping.md#schema-mapping) dell'attività di copia per modificare la forma dei dati di origine in modo che siano conformi al payload previsto dall'API REST.

```json
[
    { <data object> },
    { <data object> },
    ...
]
```

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<REST output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "RestSink",
                "requestMethod": "POST",
                "httpRequestTimeout": "00:01:40",
                "requestInterval": 10,
                "writeBatchSize": 10000,
                "httpCompressionType": "none",
            },
        }
    }
]
```

## <a name="pagination-support"></a>Supporto della paginazione

Quando si copiano dati dalle API REST, normalmente l'API REST limita le dimensioni del payload di risposta di una singola richiesta con un numero ragionevole. mentre per restituire una grande quantità di dati, suddivide il risultato in più pagine e richiede ai chiamanti di inviare richieste consecutive per ottenere la pagina successiva del risultato. In genere la richiesta di una sola pagina è dinamica ed è composta dalle informazioni restituite dalla risposta della pagina precedente.

Questo connettore REST generico supporta i modelli di paginazione seguenti: 

* URL assoluto o relativo della richiesta successiva = valore della proprietà nel corpo della risposta corrente
* URL assoluto o relativo della richiesta successiva = valore di intestazione nelle intestazioni di risposta correnti
* Parametro di query della richiesta successiva = valore della proprietà nel corpo della risposta corrente
* Parametro di query della richiesta successiva = valore dell'intestazione nelle intestazioni della risposta corrente
* Intestazione della richiesta successiva = valore della proprietà nel corpo della risposta corrente
* Intestazione della richiesta successiva = valore dell'intestazione nelle intestazioni della risposta corrente

**Le regole di impaginazione** sono definite come dizionario nel set di dati, che contiene una o più coppie chiave-valore con distinzione tra maiuscole e minuscole. La configurazione verrà usata per generare la richiesta a partire dalla seconda pagina. Il connettore interrompe l'iterazione quando ottiene il codice di stato HTTP 204 (nessun contenuto) o qualsiasi espressione JSONPath in "paginationRules" restituisce null.

**Chiavi supportate** nelle regole di paginazione:

| Chiave | Descrizione |
|:--- |:--- |
| AbsoluteUrl | Indica l'URL per l'invio della richiesta successiva. Può essere un URL **assoluto o relativo**. |
| QueryParameters.*parametro_query_richiesta* o QueryParameters['parametro_query_richiesta'] | "request_query_parameter" è definito dall'utente, che fa riferimento a un nome di parametro di query nell'URL della richiesta HTTP successivo. |
| Headers.*intestazione_richiesta* o Headers['intestazione_richiesta'] | "request_header" è definito dall'utente, che fa riferimento a un nome di intestazione nella richiesta HTTP successiva. |

**Valori supportati** nelle regole di paginazione:

| Valore | Descrizione |
|:--- |:--- |
| Headers.*intestazione_risposta* o Headers['intestazione_risposta'] | "response_header" è definito dall'utente, che fa riferimento a un nome di intestazione nella risposta HTTP corrente, il cui valore verrà usato per emettere la richiesta successiva. |
| Espressione JSONPath che inizia con "$" (che rappresenta la radice del corpo della risposta) | Il corpo della risposta deve contenere un solo oggetto JSON. L'espressione JSONPath deve restituire un singolo valore primitivo, che verrà usato per inviare la richiesta successiva. |

**Esempio:**

L'API Viso di Facebook restituisce la risposta nella struttura seguente, nel qual caso l'URL della pagina successiva è rappresentato in ***paging.next***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

La configurazione dell'origine dell'attività di copia REST corrispondente `paginationRules` , in particolare, è la seguente:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>Usare OAuth
Questa sezione descrive come usare un modello di soluzione per copiare dati dal connettore REST in Azure Data Lake Storage in formato JSON tramite OAuth. 

### <a name="about-the-solution-template"></a>Informazioni sul modello di soluzione

Il modello contiene due attività:
- L'attività **Web** recupera il Bearer token e quindi lo passa all'attività di copia successiva come autorizzazione.
- L'attività **Copy** copia i dati da REST a Azure Data Lake storage.

Il modello definisce due parametri:
- **SinkContainer** è il percorso della cartella radice in cui vengono copiati i dati nel Azure Data Lake storage. 
- **SinkDirectory** è il percorso della directory sotto la radice in cui vengono copiati i dati nel Azure Data Lake storage. 

### <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Passare a **copia da Rest o http usando** il modello OAuth. Crea una nuova connessione per la connessione di origine. 
    ![Crea nuove connessioni](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Di seguito sono riportati i passaggi chiave per le nuove impostazioni del servizio collegato (REST):
    
     1. In **URL di base** specificare il parametro URL per il servizio REST di origine. 
     2. Per **tipo di autenticazione** scegliere *Anonimo*.
        ![Nuova connessione REST](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Crea una nuova connessione per la connessione di destinazione.  
    ![Nuova connessione Gen2](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Selezionare **Usa questo modello**.
    ![Usa questo modello](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Si noterà che la pipeline è stata creata come illustrato nell'esempio seguente:  ![ screenshot mostra la pipeline creata dal modello.](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Selezionare attività **Web** . In **Impostazioni** specificare l' **URL**, il **Metodo**, le **intestazioni** e il **corpo** corrispondenti per recuperare i Bearer token OAuth dall'API di accesso del servizio da cui si desidera copiare i dati. Il segnaposto nel modello presenta un esempio di Azure Active Directory OAuth (AAD). Nota l'autenticazione AAD è supportata in modo nativo dal connettore REST. di seguito è riportato solo un esempio di flusso OAuth. 

    | Proprietà | Descrizione |
    |:--- |:--- |:--- |
    | URL |Specificare l'URL da cui recuperare il bearer token OAuth. Nell'esempio seguente, ad esempio, https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Metodo | Metodo HTTP. I valori consentiti sono **post** e **Get**. | 
    | Intestazioni | L'intestazione è definita dall'utente, che fa riferimento a un nome di intestazione nella richiesta HTTP. | 
    | Corpo | Corpo della richiesta HTTP. | 

    ![Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. In attività **copia dati** selezionare scheda *origine* . si noterà che il Bearer token (access_token) recuperato dal passaggio precedente verrebbe passato all'attività copia dati come **autorizzazione** in intestazioni aggiuntive. Confermare le impostazioni per le proprietà seguenti prima di avviare un'esecuzione della pipeline.

    | Proprietà | Descrizione |
    |:--- |:--- |:--- | 
    | Metodo richiesta | Metodo HTTP. I valori consentiti sono **Get** (predefinito) e **Post**. | 
    | Intestazioni aggiuntive | Intestazioni richiesta HTTP aggiuntive.| 

   ![Copia autenticazione origine](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Selezionare **Debug**, immettere i valori in **Parametri**, quindi selezionare **Fine**.
   ![Esecuzione della pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Quando l'esecuzione della pipeline viene completata correttamente, viene visualizzato il risultato simile all'esempio seguente: ![ risultato dell'esecuzione della pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Fare clic sull'icona "output" di webactivity nella colonna **azioni** per visualizzare le access_token restituite dal servizio.

   ![Output token](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Fare clic sull'icona "input" di CopyActivity nella colonna **azioni** . si noterà che il access_token recuperato da webactivity viene passato a CopyActivity per l'autenticazione. 

    ![Input token](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Per evitare che il token venga registrato come testo normale, abilitare l'"output sicuro" nell'attività Web e "input protetto" nell'attività di copia.


## <a name="export-json-response-as-is"></a>Esportare la risposta JSON così com'è

È possibile usare questo connettore REST per esportare la risposta JSON dell'API REST in vari archivi basati su file. Per ottenere una copia priva di schema, ignorare la sezione "structure" (chiamata anche *schema*) nel set di dati e il mapping dello schema nell'attività di copia.

## <a name="schema-mapping"></a>Mapping dello schema

Per copiare dati dall'endpoint REST al sink in formato tabulare, vedere [Mapping dello schema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
