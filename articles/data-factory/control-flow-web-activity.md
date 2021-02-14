---
title: Attività Web in Azure Data Factory
description: Informazioni su come usare l'attività Web, una delle attività del flusso di controllo supportate da Data Factory, per richiamare un endpoint REST da una pipeline.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: e4578b41e5cbb62c8a1bfa0c48d4fd60d042a506
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361520"
---
# <a name="web-activity-in-azure-data-factory"></a>Attività Web in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


L'attività Web può essere usata per chiamare un endpoint REST personalizzato da una pipeline di Data Factory. È possibile passare set di dati e servizi collegati in modo che l'attività possa usarli e accedervi.

> [!NOTE]
> L'attività Web è supportata per chiamare gli URL ospitati in una rete virtuale privata nonché per sfruttare il runtime di integrazione self-hosted. Il runtime di integrazione deve avere una linea di visibilità per l'endpoint dell'URL. 

> [!NOTE]
> La dimensione massima del payload di risposta di output supportata è 4 MB.  

## <a name="syntax"></a>Sintassi

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "connectVia": {
          "referenceName": "<integrationRuntimeName>",
          "type": "IntegrationRuntimeReference"
      }
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
name | Nome dell'attività Web | string | Sì
type | Deve essere impostato su **WebActivity**. | string | Sì
method | Metodo API REST per l'endpoint di destinazione. | Stringa. <br/><br/>Tipi supportati: "GET", "POST", "PUT" | Sì
url | Endpoint e percorso di destinazione | Stringa (o espressione con l'elemento resultType della stringa). L'attività raggiungerà il timeout a 1 minuto con un errore se non riceve una risposta dall'endpoint. | Sì
headers | Intestazioni che vengono inviate alla richiesta. Ad esempio, per impostare il linguaggio e il tipo in una richiesta: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Stringa (o un'espressione con l'elemento resultType della stringa) | Sì, l'intestazione Content-type è obbligatoria. `"headers":{ "Content-Type":"application/json"}`
Corpo | Rappresenta il payload inviato all'endpoint.  | Stringa (o espressione con l'elemento resultType della stringa). <br/><br/>Vedere lo schema del payload della richiesta nella sezione [Schema del payload della richiesta](#request-payload-schema). | Obbligatoria per i metodi POST e PUT.
autenticazione | Metodo di autenticazione usato per chiamare l'endpoint. I tipi supportati sono "Basic" o "ClientCertificate". Per altre informazioni, vedere la sezione [Autenticazione](#authentication). Se l'autenticazione non è necessaria, escludere questa proprietà. | Stringa (o un'espressione con l'elemento resultType della stringa) | No
set di dati | Elenco di set di dati passato all'endpoint. | Matrice di riferimenti a set di dati. Può essere una matrice vuota. | Sì
linkedServices | Elenco dei servizi collegati passato all'endpoint. | Matrice di riferimenti a servizi collegati. Può essere una matrice vuota. | Sì
connectVia | [Runtime di integrazione](./concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, il servizio usa il runtime di integrazione di Azure predefinito. | Riferimento al runtime di integrazione. | No 

> [!NOTE]
> Gli endpoint REST che l'attività Web richiama devono restituire una risposta di tipo JSON. L'attività raggiungerà il timeout a 1 minuto con un errore se non riceve una risposta dall'endpoint.

La tabella seguente indica i requisiti per il contenuto JSON:

| Tipo di valore | Testo della richiesta | Corpo della risposta |
|---|---|---|
|Oggetto JSON | Supportato | Supportato |
|Matrice JSON | Supportato <br/>Al momento, le matrici JSON non funzionano per via di un bug. È in corso una correzione. | Non supportato |
| Valore JSON | Supportato | Non supportato |
| Tipo non JSON | Non supportato | Non supportato |
||||

## <a name="authentication"></a>Authentication

Di seguito sono riportati i tipi di autenticazione supportati nell'attività Web.

### <a name="none"></a>nessuno

Se l'autenticazione non è necessaria, non includere la proprietà "authentication".

### <a name="basic"></a>Basic

Specificare il nome utente e la password da usare per l'autenticazione di base.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificato client

Specificare il contenuto con codifica Base64 di un file PFX e la password.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identità gestita

Specificare l'URI di risorsa per cui verrà richiesto il token di accesso usando l'identità gestita per la data factory. Per chiamare l'API di gestione delle risorse di Azure, usare `https://management.azure.com/`. Per altre informazioni sul funzionamento delle identità gestite, consultare la [pagina di panoramica sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se la data factory è configurata con un repository git, è necessario archiviare le credenziali in Azure Key Vault per usare l'autenticazione di base o del certificato client. Azure Data Factory non archivia le password in git.

## <a name="request-payload-schema"></a>Schema del payload della richiesta
Quando si usa il metodo POST o PUT, la proprietà body rappresenta il payload che viene inviato all'endpoint. È possibile passare i servizi collegati e i set di dati come parte del payload. Di seguito è riportato lo schema per il payload:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Esempio
In questo esempio, l'attività Web della pipeline chiama un endpoint REST. Passa all'endpoint un servizio collegato SQL di Azure e un set di dati SQL di Azure. L'endpoint REST usa la stringa di connessione SQL di Azure per connettersi al server SQL logico e restituisce il nome dell'istanza di SQL Server.

### <a name="pipeline-definition"></a>Definizione della pipeline

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Valori dei parametri della pipeline

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Codice endpoint del servizio Web

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory:

- [Attività Esegui pipeline](control-flow-execute-pipeline-activity.md)
- [Per ogni attività](control-flow-for-each-activity.md)
- [Ottenere attività di metadati](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
