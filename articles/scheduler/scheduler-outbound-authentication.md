---
title: Autenticazione in uscita
description: Informazioni su come configurare o rimuovere l'autenticazione in uscita per l'Utilità di pianificazione di Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 0a8d79af9f45731971cb1be1f39fc193f9d0f0d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80878970"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Autenticazione in uscita per l'Utilità di pianificazione di Azure

> [!IMPORTANT]
> [App per la logica di Azure](../logic-apps/logic-apps-overview.md) sostituirà Utilità di pianificazione di Azure di cui è [in corso il ritiro](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Per continuare a usare i processi configurati nell'utilità di pianificazione, [eseguire la migrazione alle app per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) il prima possibile. 
>
> Utilità di pianificazione non è più disponibile nel portale di Azure, ma l'[API REST](/rest/api/scheduler) e i [cmdlet di PowerShell per Utilità di pianificazione di Azure](scheduler-powershell-reference.md) rimangono attualmente disponibili, quindi è possibile gestire processi e raccolte di processi.

I processi dell'Utilità di pianificazione di Azure potrebbero dover chiamare servizi che richiedono l'autenticazione, ad esempio altri servizi di Azure, Salesforce.com, Facebook e siti Web personalizzati protetti. Il servizio chiamato può determinare se il processo dell'Utilità di pianificazione può accedere alle risorse richieste. 

L'Utilità di pianificazione supporta questi modelli di autenticazione: 

* Autenticazione con *certificato client* quando si usano certificati client SSL/TLS
* Autenticazione di *base*
* Autenticazione *Active Directory OAuth*

## <a name="add-or-remove-authentication"></a>Aggiungere o rimuovere un'autenticazione

* Per aggiungere un'autenticazione a un processo dell'Utilità di pianificazione, aggiungere l'elemento figlio JSON (JavaScript Object Notation) `authentication` all'elemento `request` quando si crea o aggiorna il processo. 

  Le risposte non restituiscono mai i segreti passati al servizio Utilità di pianificazione in una richiesta PUT, PATCH o POST nell'oggetto `authentication`. 
  Le risposte impostano le informazioni segrete su Null o potrebbero usare un token pubblico che rappresenti l'entità autenticata. 

* Per rimuovere un'autenticazione da un processo dell'Utilità di pianificazione, eseguire in modo esplicito una richiesta PUT o PATCH nel processo e impostare l'oggetto `authentication` su Null. La risposta non includerà le proprietà di autenticazione.

## <a name="client-certificate"></a>Certificato client

### <a name="request-body---client-certificate"></a>Corpo della richiesta - Certificato client

Quando si aggiunge un'autenticazione usando il modello `ClientCertificate`, specificare i seguenti elementi aggiuntivi nel corpo della richiesta.  

| Elemento | Obbligatoria | Descrizione |
|---------|----------|-------------|
| **authentication** (elemento padre) | Oggetto di autenticazione per l'utilizzo di un certificato client SSL/TLS |
| **type** | Sì | Il tipo di autenticazione. Per i certificati client SSL/TLS, il valore è `ClientCertificate` . |
| **PFX** | Sì | Contenuto del file PFX in codifica base64. |
| **password** | Sì | Password per accedere al file PFX. |
||| 

### <a name="response-body---client-certificate"></a>Corpo della risposta - Certificato client 

Quando viene inviata una richiesta con le informazioni di autenticazione, la risposta contiene i seguenti elementi di autenticazione.

| Elemento | Descrizione | 
|---------|-------------| 
| **authentication** (elemento padre) | Oggetto di autenticazione per l'utilizzo di un certificato client SSL/TLS |
| **type** | Il tipo di autenticazione. Per i certificati client SSL/TLS, il valore è `ClientCertificate` . |
| **certificateThumbprint** |Identificazione personale del certificato. |
| **certificateSubjectName** |Nome distintivo del soggetto del certificato. |
| **certificateExpiration** | Data di scadenza del certificato. |
||| 

### <a name="sample-rest-request---client-certificate"></a>Richiesta REST di esempio - Certificato client

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>Risposta REST di esempio - Certificato client

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Basic

### <a name="request-body---basic"></a>Corpo della richiesta - Autenticazione di base

Quando si aggiunge un'autenticazione usando il modello `Basic`, specificare i seguenti elementi aggiuntivi nel corpo della richiesta.

| Elemento | Obbligatoria | Descrizione |
|---------|----------|-------------|
| **authentication** (elemento padre) | Oggetto autenticazione per l'uso dell'autenticazione di base. | 
| **type** | Sì | Il tipo di autenticazione. Per l'autenticazione di Base, il valore è `Basic`. | 
| **nome utente** | Sì | Nome utente per eseguire l'autenticazione. | 
| **password** | Sì | Password per eseguire l'autenticazione. |
|||| 

### <a name="response-body---basic"></a>Corpo della risposta - Autenticazione di base

Quando viene inviata una richiesta con le informazioni di autenticazione, la risposta contiene i seguenti elementi di autenticazione.

| Elemento | Descrizione | 
|---------|-------------|
| **authentication** (elemento padre) | Oggetto autenticazione per l'uso dell'autenticazione di base. |
| **type** | Il tipo di autenticazione. Per l'autenticazione di base il valore è `Basic`. |
| **nome utente** | Nome utente autenticato. |
||| 

### <a name="sample-rest-request---basic"></a>Richiesta REST di esempio - Autenticazione di base

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>Risposta REST di esempio - Autenticazione di base

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Autenticazione OAuth Active Directory

### <a name="request-body---active-directory-oauth"></a>Corpo della richiesta - Autenticazione OAuth Active Directory 

Quando si aggiunge un'autenticazione usando il modello `ActiveDirectoryOAuth`, specificare i seguenti elementi aggiuntivi nel corpo della richiesta.

| Elemento | Obbligatoria | Descrizione |
|---------|----------|-------------|
| **authentication** (elemento padre) | Sì | Oggetto autenticazione per l'autenticazione OAuth Active Directory. |
| **type** | Sì | Il tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore è `ActiveDirectoryOAuth`. |
| **tenant** | Sì | L'identificatore del tenant di Azure AD. Per trovare l'identificatore del tenant di Azure AD, eseguire `Get-AzureAccount` in Azure PowerShell. |
| **pubblico** | Sì | Questo valore è impostato su `https://management.core.windows.net/`. | 
| **clientId** | Sì | Identificatore client per l'applicazione Azure AD | 
| **segreto** | Sì | Segreto del client che richiede il token | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Corpo della risposta - Autenticazione OAuth Active Directory

Quando viene inviata una richiesta con le informazioni di autenticazione, la risposta contiene i seguenti elementi di autenticazione.

| Elemento | Descrizione |
|---------|-------------|
| **authentication** (elemento padre) | Oggetto autenticazione per l'autenticazione OAuth Active Directory. |
| **type** | Il tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore è `ActiveDirectoryOAuth`. | 
| **tenant** | L'identificatore del tenant di Azure AD |
| **pubblico** | Questo valore è impostato su `https://management.core.windows.net/`. |
| **clientId** | Identificatore client per l'applicazione Azure AD |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Richiesta REST di esempio - Autenticazione OAuth Active Directory

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>Risposta REST di esempio - Autenticazione OAuth Active Directory

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)
* [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)
* [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](/rest/api/scheduler)
* [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)