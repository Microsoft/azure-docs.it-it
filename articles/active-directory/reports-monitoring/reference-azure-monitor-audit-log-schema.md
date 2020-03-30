---
title: Interpretare lo schema del log di controllo di Azure Active Directory in Monitoraggio di Azure Documenti Microsoft
description: Descrivere lo schema del log di controllo di Azure AD da usare in Monitoraggio di AzureDescribe the Azure AD audit log schema for use in Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9f58b213e50a021651f35112a48d8f74ae59571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68987948"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Interpretare lo schema dei log di controllo di Azure AD in Monitoraggio di Azure (anteprima)

Questo articolo illustra lo schema del log di controllo di Azure Active Directory (Azure AD) in Monitoraggio di Azure. Ogni singola voce del registro è archiviata come testo e formattata come BLOB JSON, come illustrato nei due esempi seguenti: 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Descrizioni di campi e proprietà

| Nome campo | Descrizione |
|------------|-------------|
| time       | Data e ora (in formato UTC). |
| operationName | Nome dell'operazione. |
| operationVersion | Versione dell'API REST richiesta dal client. |
| category | Attualmente, *Controllo* è l'unico valore supportato. |
| TenantId | GUID del tenant associato ai log. |
| resultType | Risultato dell'operazione. Il risultato può essere *Operazione riuscita* oppure *Errore*. |
| resultSignature |  Questo campo non è mappato ed è quindi possibile ignorarlo. | 
| resultDescription | Descrizione aggiuntiva del risultato, se disponibile. | 
| durationMs |  Questo campo non è mappato ed è quindi possibile ignorarlo. |
| callerIpAddress | Indirizzo IP del client che ha eseguito la richiesta. | 
| correlationId | GUID facoltativo passato dal client. Questo valore consente di correlare le operazioni lato client con le operazioni lato server ed è utile durante l'analisi dei log relativi a più servizi. |
| identity | Identità del token presentato al momento dell'esecuzione della richiesta. L'identità può essere un account utente, un account di sistema o un'entità servizio. |
| level | Tipo di messaggio. Per i log di controllo, il livello è sempre *informativo*. |
| posizione | Posizione del datacenter. |
| properties | Elenco delle proprietà supportate relative a un log di controllo. Per altre informazioni, vedere la tabella seguente. | 

<br>

| Nome proprietà | Descrizione |
|---------------|-------------|
| AuditEventCategory | Tipo di evento di controllo. Può essere *Gestione utenti*, *Gestione applicazioni* o un altro tipo.|
| Tipo di identità | Il tipo può essere *Applicazione* o *Utente*. |
| Tipo di operazione | Il tipo può essere *Aggiungi*, *Aggiorna*, *Elimina* o *Altro*. |
| Tipo di risorsa di destinazione | Specifica il tipo di risorsa di destinazione in cui è stata eseguita l'operazione. Il tipo può essere *Applicazione*, *Utente*, *Ruolo* o *Criteri*. | 
| Nome della risorsa di destinazione | Nome della risorsa di destinazione. Può trattarsi di un nome di applicazione, un nome di ruolo, un nome dell'entità utente o un nome dell'entità servizio. |
| additionalTargets | Elenca le proprietà aggiuntive per operazioni specifiche. Ad esempio, per un'operazione di aggiornamento, i valori precedenti e i nuovi valori sono elencati in *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Passaggi successivi

* [Interpretare lo schema dei log di accesso in Monitoraggio di AzureInterpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Log di diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Domande frequenti e problemi noti](concept-activity-logs-azure-monitor.md#frequently-asked-questions)