---
title: Usare API di Microsoft Graph per configurare il provisioning-Azure Active Directory | Microsoft Docs
description: È necessario impostare il provisioning per più istanze di un'applicazione? Informazioni su come risparmiare tempo usando le API Microsoft Graph per automatizzare la configurazione del provisioning automatico.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 17660df34c8039ae96440c417aef051d51a5c91c
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88234754"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Configurare il provisioning usando le API di Microsoft Graph

Il portale di Azure è un modo pratico per configurare il provisioning per le singole app una alla volta. Tuttavia, se si creano diverse istanze di un'applicazione, o addirittura centinaia, può essere più facile automatizzare la creazione e la configurazione delle app con le API Microsoft Graph. Questo articolo illustra come automatizzare la configurazione del provisioning tramite le API. Questo metodo viene comunemente usato per applicazioni come [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso).

**Panoramica dei passaggi per l'uso delle API di Microsoft Graph per automatizzare la configurazione del provisioning**


|Passaggio  |Dettagli  |
|---------|---------|
|[Passaggio 1. Creare l'applicazione della raccolta](#step-1-create-the-gallery-application)     |Accedere al client API <br> Recuperare il modello di applicazione della raccolta <br> Creare l'applicazione della raccolta         |
|[Passaggio 2. Crea processo di provisioning in base al modello](#step-2-create-the-provisioning-job-based-on-the-template)     |Recuperare il modello per il connettore di provisioning <br> Creare il processo di provisioning         |
|[Passaggio 3. Autorizzare l'accesso](#step-3-authorize-access)     |Testare la connessione all'applicazione <br> Salva le credenziali         |
|[Passaggio 4. Avviare il processo di provisioning](#step-4-start-the-provisioning-job)     |Avviare il processo         |
|[Passaggio 5. Monitorare il provisioning](#step-5-monitor-provisioning)     |Verificare lo stato del processo di provisioning <br> Recuperare i log di provisioning         |

> [!NOTE]
> Gli oggetti risposta illustrati in questo articolo potrebbero essere stati abbreviati per migliorare la leggibilità. Tutte le proprietà verranno restituite da una chiamata effettiva.

## <a name="step-1-create-the-gallery-application"></a>Passaggio 1: Creare l'applicazione della raccolta

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Accedere a Microsoft Graph Explorer (scelta consigliata), Postman o un altro client API preferito

1. Avviare [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Selezionare il pulsante "Accedi con Microsoft" e accedere usando Azure AD amministratore globale o le credenziali di amministratore dell'app.

    ![Accedere a Graph](./media/application-provisioning-configure-api/wd_export_02.png)

1. Una volta eseguito l'accesso, verranno visualizzati i dettagli dell'account utente nel riquadro sinistro.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Recuperare l'identificatore del modello di applicazione della raccolta
Le applicazioni nella raccolta di applicazioni di Azure AD presentano un [modello di applicazione](/graph/api/applicationtemplate-list?tabs=http&view=graph-rest-beta) che descrive i metadati per l'applicazione in questione. Usando questo modello, è possibile creare un'istanza dell'applicazione e un'entità servizio nel tenant per la gestione.

#### <a name="request"></a>*Richiesta*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*Risposta*

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Creare l'applicazione della raccolta

Usare l'ID modello recuperato per l'applicazione nell'ultimo passaggio per [creare un'istanza](/graph/api/applicationtemplate-instantiate?tabs=http&view=graph-rest-beta) dell'applicazione e dell'entità servizio nel tenant.

#### <a name="request"></a>*Richiesta*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*Risposta*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>Passaggio 2: creare il processo di provisioning in base al modello

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>Recuperare il modello per il connettore di provisioning

Le applicazioni nella raccolta abilitate per il provisioning hanno modelli per semplificare la configurazione. Usare la richiesta seguente per [recuperare il modello per la configurazione del provisioning](/graph/api/synchronization-synchronizationtemplate-list?tabs=http&view=graph-rest-beta). Si noti che sarà necessario fornire l'ID. L'ID fa riferimento alla risorsa precedente, che in questo caso è ServicePrincipal. 

#### <a name="request"></a>*Richiesta*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*Risposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>Creare il processo di provisioning
Per abilitare il provisioning, è prima di tutto necessario [creare un processo](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta). Usare la richiesta riportata di seguito per creare un processo di provisioning. Usare templateId del passaggio precedente quando si specifica il modello da usare per il processo.

#### <a name="request"></a>*Richiesta*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*Risposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>Passaggio 3: autorizzare l'accesso

### <a name="test-the-connection-to-the-application"></a>Testare la connessione all'applicazione

Testare la connessione con l'applicazione di terze parti. L'esempio seguente è relativo a un'applicazione che richiede clientSecret e secretToken. Ogni applicazione presenta requisiti specifici. Le applicazioni spesso usano BaseAddress al posto di ClientSecret. Per determinare le credenziali richieste dall'app, passare alla pagina di configurazione del provisioning per l'applicazione e in modalità sviluppatore fare clic su Test connessione. Il traffico di rete visualizzerà i parametri usati per le credenziali. L'elenco completo delle credenziali è disponibile [qui](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta). 

#### <a name="request"></a>*Richiesta*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*Risposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Salva le credenziali

Per configurare il provisioning, è necessario stabilire una relazione di trust tra Azure AD e l'applicazione. Autorizzare l'accesso all'applicazione di terze parti. L'esempio seguente è relativo a un'applicazione che richiede clientSecret e secretToken. Ogni applicazione presenta requisiti specifici. Esaminare la [documentazione dell'API](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta) per visualizzare le opzioni disponibili. 

#### <a name="request"></a>*Richiesta*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*Risposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>Passaggio 4: avviare il processo di provisioning
Ora che il processo di provisioning è configurato, usare il comando seguente per [avviare il processo](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta). 


#### <a name="request"></a>*Richiesta*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*Risposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>Passaggio 5: monitorare il provisioning

### <a name="monitor-the-provisioning-job-status"></a>Monitorare lo stato del processo di provisioning

Ora che il processo di provisioning è in esecuzione, usare il comando seguente per tenere traccia dello stato di avanzamento del ciclo di provisioning corrente e delle statistiche fino alla data, ad esempio il numero di utenti e gruppi creati nel sistema di destinazione. 

#### <a name="request"></a>*Richiesta*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*Risposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>Monitorare gli eventi di provisioning usando i log di provisioning
Oltre a monitorare lo stato del processo di provisioning, è possibile usare i [log di provisioning](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta) per eseguire una query per tutti gli eventi che si verificano (ad esempio, eseguire una query per un determinato utente e determinare se ne è stato effettuato correttamente il provisioning).

#### <a name="request"></a>*Richiesta*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*Risposta*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>Articoli correlati

- [Esaminare la documentazione relativa alla sincronizzazione Microsoft Graph](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrazione di un'app SCIM personalizzata con Azure AD](use-scim-to-provision-users-and-groups.md)