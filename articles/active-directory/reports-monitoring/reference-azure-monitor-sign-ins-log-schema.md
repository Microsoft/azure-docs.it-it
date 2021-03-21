---
title: Schema del log di accesso in monitoraggio di Azure | Microsoft Docs
description: Descrivere lo schema del log di accesso Azure AD per l'uso in monitoraggio di Azure
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
ms.date: 03/12/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: bad8ae86827144269e816a6c2e01d6af3f4d88ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225419"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interpretare lo schema dei log di accesso Azure AD in monitoraggio di Azure

Questo articolo illustra lo schema del log di accesso di Azure Active Directory (Azure AD) in Monitoraggio di Azure. La maggior parte delle informazioni relative agli accessi viene fornita nell'attributo *Properties* dell'oggetto `records`.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Descrizioni dei campi

| Nome del campo | Chiave | Descrizione |
| --- | --- | --- | 
| Tempo |  - | Data e ora in formato UTC. |
| ResourceId | - | Questo valore non è mappato, è quindi possibile ignorare questo campo.  |
| OperationName | - | Per gli accessi, questo valore è sempre *Attività di accesso*. |
| OperationVersion | - | Versione dell'API REST richiesta dal client. |
| Category | - | Per gli accessi, questo valore è sempre *Accesso*. | 
| TenantId | - | GUID del tenant associato ai log. |
| ResultType | - | Il risultato dell'operazione di accesso può essere *Operazione completata* o *Errore*. | 
| ResultSignature | - | Contiene il codice di errore, se esistente, per l'operazione di accesso. |
| ResultDescription | N/o o vuoto | Fornisce la descrizione dell'errore per l'operazione di accesso. |
| riskDetail | riskDetail | Fornisce il "motivo" dietro uno stato specifico di un utente rischioso, un accesso o un rilevamento dei rischi. I valori possibili sono: `none` , `adminGeneratedTemporaryPassword` , `userPerformedSecuredPasswordChange` , `userPerformedSecuredPasswordReset` , `adminConfirmedSigninSafe` , `aiConfirmedSigninSafe` , `userPassedMFADrivenByRiskBasedPolicy` , `adminDismissedAllRiskForUser` , `adminConfirmedSigninCompromised` , `unknownFutureValue` . Il valore `none` indica che non è stata eseguita alcuna azione sull'utente o sull'accesso fino a questo momento. <br>**Nota:** Per informazioni dettagliate su questa proprietà, è necessaria una licenza Azure AD Premium P2. Altre licenze restituiscono il valore `hidden` . |
| riskEventTypes | riskEventTypes | Tipi di rilevamento dei rischi associati all'accesso. I valori possibili sono: `unlikelyTravel` ,,, `anonymizedIPAddress` `maliciousIPAddress` `unfamiliarFeatures` , `malwareInfectedIPAddress` , `suspiciousIPAddress` , `leakedCredentials` , `investigationsThreatIntelligence` ,  `generic` e `unknownFutureValue` . |
| authProcessingDetails | Azure AD libreria di autenticazione app | Contiene informazioni sulla famiglia, sulla libreria e sulla piattaforma nel formato: "famiglia: ADAL Library: ADAL.JS 1.0.0 Platform: JS" |
| authProcessingDetails | IsCAEToken | I valori sono true o false |
| riskLevelAggregated | riskLevel | Livello di rischio aggregato. I valori possibili sono: `none` , `low` , `medium` , `high` , `hidden` e `unknownFutureValue` . Il valore `hidden` indica che l'utente o l'accesso non è stato abilitato per Azure ad Identity Protection. **Nota:** I dettagli di questa proprietà sono disponibili solo per i clienti Azure AD Premium P2. Verranno restituiti tutti gli altri clienti `hidden` . |
| riskLevelDuringSignIn | riskLevel | Livello di rischio durante l'accesso. I valori possibili sono: `none` , `low` , `medium` , `high` , `hidden` e `unknownFutureValue` . Il valore `hidden` indica che l'utente o l'accesso non è stato abilitato per Azure ad Identity Protection. **Nota:** I dettagli di questa proprietà sono disponibili solo per i clienti Azure AD Premium P2. Verranno restituiti tutti gli altri clienti `hidden` . |
| riskState | riskState | Segnala lo stato dell'utente rischioso, l'accesso o il rilevamento dei rischi. I valori possibili sono: `none` , `confirmedSafe` , `remediated` , `dismissed` , `atRisk` , `confirmedCompromised` , `unknownFutureValue` . |
| DurationMs | - | Questo valore non è mappato, è quindi possibile ignorare questo campo. |
| CallerIpAddress | - | Indirizzo IP del client che ha eseguito la richiesta. | 
| CorrelationId | - | GUID facoltativo passato dal client. Questo valore consente di correlare le operazioni lato client con le operazioni lato server ed è utile durante l'analisi dei log che si estendono tra i servizi. |
| Identità | - | Identità del token presentato al momento dell'esecuzione della richiesta. Può essere un account utente, un account di sistema o un'entità servizio. |
| Level | - | Fornisce il tipo di messaggio. Per il controllo, è sempre *Informativo*. |
| Location | - | Fornisce il percorso dell'attività di accesso. |
| Proprietà | - | Elenca tutte le proprietà associate agli accessi.|

## <a name="next-steps"></a>Passaggi successivi

* [Interpretare lo schema dei log di controllo in Monitoraggio di Azure](reference-azure-monitor-audit-log-schema.md)
* [Scopri di più sui log della piattaforma Azure](../../azure-monitor/essentials/platform-logs-overview.md)