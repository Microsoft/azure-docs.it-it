---
title: Accedere ed esaminare i log di controllo
titleSuffix: Azure AD B2C
description: Come accedere ai log di controllo Azure AD B2C a livello di codice e nel portale di Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 4fc25edb873a2dfe84f6ca716a71cf028c74cb2f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85383938"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Accesso ai log di controllo di Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) crea log di controllo contenenti informazioni sulle attività sulle risorse B2C, i token emessi e l'accesso amministratore. Questo articolo fornisce una breve panoramica delle informazioni disponibili nei log di controllo e istruzioni su come accedere ai dati per il tenant Azure AD B2C.

Gli eventi del log di controllo vengono conservati per **sette giorni**. Se è necessario conservarli per un periodo più lungo, pianificare il download e l'archiviazione dei log con uno dei metodi illustrati sotto.

> [!NOTE]
> Non è possibile visualizzare gli accessi degli utenti per le singole applicazioni di Azure AD B2C nella sezione **utenti** delle pagine **Azure Active Directory** o **Azure ad B2C** della portale di Azure. Gli eventi di accesso mostrano l'attività dell'utente, ma non possono essere correlati all'applicazione B2C a cui l'utente ha effettuato l'accesso. È necessario usare i log di controllo per tale operazione, come illustrato più avanti in questo articolo.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Panoramica delle attività disponibili nella categoria B2C dei log di controllo

La categoria **B2C** nei log di controllo contiene i tipi di attività seguenti:

|Tipo di attività |Descrizione  |
|---------|---------|
|Autorizzazione |Attività relative all'autorizzazione di un utente per accedere alle risorse B2C (ad esempio, un amministratore che accede a un elenco di criteri B2C).         |
|Directory |Attività relative agli attributi di directory recuperate quando un amministratore accede utilizzando il portale di Azure. |
|Applicazione | Operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) sulle applicazioni B2C. |
|Chiave |Operazioni CRUD sulle chiavi archiviate in un contenitore di chiavi B2C. |
|Risorsa |Operazioni CRUD sulle risorse B2C. Ad esempio, i criteri e i provider di identità.
|Authentication |Convalida delle credenziali utente e del rilascio dei token.|

Per le attività CRUD sugli oggetti utente, vedere la categoria **Directory principale**.

## <a name="example-activity"></a>Attività di esempio

Questa immagine di esempio dalla portale di Azure Mostra i dati acquisiti quando un utente accede con un provider di identità esterno, in questo caso Facebook:

![Esempio di pagina dei dettagli dell'attività del log di controllo in portale di Azure](./media/view-audit-logs/audit-logs-example.png)

Il pannello Dettagli attività contiene le informazioni rilevanti seguenti:

|Sezione|Campo|Descrizione|
|-------|-----|-----------|
| Attività | Nome | Quale attività ha avuto luogo. Ad esempio, *emettere un id_token all'applicazione*, che conclude l'accesso utente effettivo. |
| Azione avviata da (attore) | ObjectId | **ID oggetto** dell'applicazione B2C a cui l'utente sta eseguendo l'accesso. Questo identificatore non è visibile nel portale di Azure, ma è accessibile tramite l'API Microsoft Graph. |
| Azione avviata da (attore) | SPN | **ID applicazione** dell'applicazione B2C a cui l'utente sta eseguendo l'accesso. |
| Destinazione/i | ObjectId | **ID oggetto** dell'utente che sta effettuando l'accesso. |
| Altri dettagli | TenantId | **ID tenant** del tenant del Azure ad B2C. |
| Altri dettagli | PolicyId | **ID criterio** del flusso utente (criterio) utilizzato per l'accesso dell'utente. |
| Altri dettagli | ApplicationId | **ID applicazione** dell'applicazione B2C a cui l'utente sta eseguendo l'accesso. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Visualizzare i log di controllo nel portale di Azure

Il portale di Azure fornisce l'accesso agli eventi del log di controllo nel tenant del Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Passare alla directory che contiene il tenant di Azure AD B2C e quindi selezionare **Azure ad B2C**.
1. In **attività** nel menu a sinistra selezionare **log di controllo**.

Viene visualizzato un elenco di eventi di attività registrati negli ultimi sette giorni.

![Filtro di esempio con due eventi attività in portale di Azure](./media/view-audit-logs/audit-logs-example-filter.png)

Sono disponibili diverse opzioni di filtro, tra cui:

* **Tipo di risorsa attività** : filtrare in base ai tipi di attività indicati nella tabella nella sezione [Panoramica delle attività disponibili](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) .
* **Data** : filtrare l'intervallo di date delle attività visualizzate.

Se si seleziona una riga nell'elenco, vengono visualizzati i dettagli dell'attività per l'evento.

Per scaricare l'elenco di eventi di attività in un file con valori delimitati da virgole (CSV), selezionare **Scarica**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Ottenere i log di controllo con l'API per la creazione di report Azure AD

I log di controllo vengono pubblicati nella stessa pipeline delle altre attività di Azure Active Directory e quindi sono accessibili tramite l'[API di creazione di report di Azure Active Directory](https://docs.microsoft.com/graph/api/directoryaudit-list). Per altre informazioni, vedere [Introduzione all'API di creazione report Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Abilitare l'accesso all'API di creazione report

Per consentire l'accesso in base all'applicazione o allo script all'API di creazione report di Azure AD, è necessaria un'applicazione registrata nel tenant Azure AD B2C con le autorizzazioni API seguenti. È possibile abilitare queste autorizzazioni per una registrazione esistente dell'applicazione all'interno del tenant B2C oppure crearne una nuova in modo specifico per l'uso con l'automazione dei log di controllo.

* Microsoft Graph > le autorizzazioni dell'applicazione > AuditLog > AuditLog. Read. All

Attenersi alla procedura descritta nell'articolo seguente per registrare un'applicazione con le autorizzazioni necessarie:

[Gestire Azure AD B2C con Microsoft Graph](microsoft-graph-get-started.md)

Dopo aver registrato un'applicazione con le autorizzazioni appropriate, vedere la sezione script di PowerShell più avanti in questo articolo per un esempio di come è possibile ottenere gli eventi di attività con uno script.

### <a name="access-the-api"></a>Accedere all'API

Per scaricare Azure AD B2C eventi del log di controllo tramite l'API, filtrare i log nella `B2C` categoria. Per filtrare in base alla categoria, usare il `filter` parametro della stringa di query quando si chiama l'endpoint API per la creazione di report Azure ad.

```http
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Script PowerShell

Lo script di PowerShell seguente mostra un esempio di come eseguire una query sull'API di creazione di report Azure AD. Dopo l'esecuzione di una query sull'API, gli eventi registrati vengono stampati nell'output standard, quindi l'output JSON viene scritto in un file.

È possibile provare questo script nella [Azure cloud Shell](overview.md). Assicurarsi di aggiornarlo con l'ID applicazione, il segreto client e il nome del tenant Azure AD B2C.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Ecco la rappresentazione JSON dell'evento di esempio Activity illustrato in precedenza nell'articolo:

```json
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

È possibile automatizzare altre attività amministrative, ad esempio, [gestire Azure ad B2C account utente con Microsoft Graph](manage-user-accounts-graph-api.md).
