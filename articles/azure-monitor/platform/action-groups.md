---
title: Creare e gestire gruppi di azione nel portale di Azure
description: Informazioni su come creare e gestire gruppi di azione nel portale di Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 4/17/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 339b11664308962962c59b2e9386ff122681293a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116214"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creare e gestire gruppi di azione nel portale di Azure
Un gruppo di azioni è una raccolta delle preferenze di notifica definite dal proprietario di una sottoscrizione di Azure. Gli avvisi di Monitoraggio di Azure e di integrità dei servizi usano gruppi di azioni per notificare agli utenti l'attivazione di un avviso. I vari avvisi possono usare lo stesso gruppo di azioni o gruppi di azioni diversi, a seconda delle esigenze dell'utente. In una sottoscrizione è possibile configurare fino a 2000 gruppi di azioni.

Viene configurata un'azione per inviare una notifica a un utente tramite posta elettronica o SMS, viene ricevuta una conferma che indica che sono stati aggiunti al gruppo di azioni.

Questo articolo illustra come creare e gestire gruppi di azione nel portale di Azure.

Ogni azione è composta dalle seguenti proprietà:

* **Nome**: identificatore univoco all'interno del gruppo di azioni.  
* **Tipo di azione**: l'azione eseguita. Gli esempi includono l'invio di una chiamata vocale, un SMS o un messaggio di posta elettronica oppure l'attivazione di vari tipi di azioni automatizzate. Vedere i tipi più avanti in questo articolo.
* **Dettagli**: i dettagli corrispondenti che variano in base al *tipo di azione*.

Per informazioni sull'uso dei modelli di Azure Resource Manager per configurare i gruppi di azione: [Modelli di Resource Manager per il gruppo di azione](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Creare un gruppo di azione usando il portale di Azure

1. Nella [portale di Azure](https://portal.azure.com)cercare e selezionare **monitoraggio**. Il riquadro **monitoraggio** consolida tutte le impostazioni e i dati di monitoraggio in un'unica visualizzazione.

1. Selezionare **Avvisi** e quindi **Gestisci azioni**.

    ![Pulsante Gestisci azioni](./media/action-groups/manage-action-groups.png)
    
1. Selezionare **Aggiungi gruppo di azione** e compilare i campi.

    ![Comando "Aggiungi gruppo di azione"](./media/action-groups/add-action-group.png)
    
1. Immettere un nome nella casella **nome gruppo di azioni** e immettere un nome nella casella **nome breve** . Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.

      ![Finestra di dialogo "Aggiungi gruppo di azione"](./media/action-groups/action-group-define.png)

1. La casella **sottoscrizione** viene riempita automaticamente con la sottoscrizione corrente. Il gruppo di azione verrà salvato in questa sottoscrizione.

1. Selezionare il **gruppo di risorse** in cui viene salvato il gruppo di azioni.

1. Definire un elenco di azioni. Per ogni azione, specificare quanto segue:

    1. **Nome**: immettere un identificatore univoco per questa azione.

    1. **Tipo di azione**: selezionare Runbook di automazione, funzione di Azure, ruolo Azure Resource Manager di posta elettronica, posta elettronica/SMS/push/voce, ITSM, app per la logica, webhook sicuro, webhook.

    1. **Dettagli**: in base al tipo di azione, immettere un numero di telefono, un indirizzo di posta elettronica, l'URI del webhook, l'app Azure, la connessione ITSM o il runbook di Automazione. Per l'azione ITSM, specificare anche **Elemento di lavoro** e altri campi richiesti dallo strumento ITSM.
    
    1. **Schema di avviso comune**: è possibile scegliere di abilitare lo [schema di avviso comune](https://aka.ms/commonAlertSchemaDocs), che offre il vantaggio di avere un singolo payload di avviso estendibile e unificato in tutti i servizi di avviso di monitoraggio di Azure.

1. Fare clic su **OK** per creare il gruppo di azione.

## <a name="manage-your-action-groups"></a>Gestire i gruppi di azione

Dopo aver creato un gruppo di azione, è possibile visualizzare i **gruppi di azioni** selezionando **Gestisci azioni** dalla pagina di destinazione **avvisi** nel riquadro **monitoraggio** . Selezionare il gruppo di azione da gestire per:

* Aggiungere, modificare o rimuovere azioni.
* Eliminare il gruppo di azione.

## <a name="action-specific-information"></a>Informazioni specifiche delle azioni

> [!NOTE]
> Vedere [limiti del servizio di sottoscrizione per il monitoraggio](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) dei limiti numerici per ognuno degli elementi seguenti.  

### <a name="automation-runbook"></a>Runbook di automazione
Vedere i [limiti del servizio di sottoscrizione di Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) per i limiti sui payload Runbook.

Un gruppo di azioni può contenere un numero limitato di azioni Runbook. 

### <a name="azure-app-push-notifications"></a>Notifiche push dell'app Azure
Un gruppo di azioni può contenere un numero limitato di azioni dell'app di Azure.

### <a name="email"></a>Posta elettronica
I messaggi di posta elettronica verranno inviati dagli indirizzi di posta elettronica seguenti. Verificare che il filtro della posta elettronica sia configurato correttamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Un gruppo di azioni può contenere un numero limitato di azioni di posta elettronica. Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>Ruolo Azure Resource Manager di posta elettronica
Inviare un messaggio di posta elettronica ai membri del ruolo della sottoscrizione. Il messaggio di posta elettronica verrà inviato solo ai membri **Azure ad utente** del ruolo. Il messaggio di posta elettronica non verrà inviato a gruppi Azure AD o entità servizio.

Un gruppo di azioni può contenere un numero limitato di azioni di posta elettronica. Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="function"></a>Funzione
Chiama un endpoint di trigger HTTP esistente in [funzioni di Azure](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Un gruppo di azioni può contenere un numero limitato di azioni di funzione.

### <a name="itsm"></a>Gestione dei servizi IT
L'azione ITSM richiede una connessione ITSM. Informazioni su come creare una [connessione ITSM](../../azure-monitor/platform/itsmc-overview.md).

Un gruppo di azioni può contenere un numero limitato di azioni ITSM. 

### <a name="logic-app"></a>App per la logica
Un gruppo di azioni può contenere un numero limitato di azioni dell'app per la logica.

### <a name="secure-webhook"></a>Webhook sicuro
L'azione webhook dei gruppi di azione consente di sfruttare Azure Active Directory per proteggere la connessione tra il gruppo di azioni e l'API Web protetta (endpoint webhook). Il flusso di lavoro generale per sfruttare questa funzionalità è descritto di seguito. Per una panoramica delle applicazioni Azure AD e delle entità servizio, vedere [Panoramica di Microsoft Identity Platform (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Creare un'applicazione Azure AD per l'API Web protetta. Vedere https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configurare l'API protetta affinché venga chiamata da un'app daemon.
    
1. Abilitare i gruppi di azioni per l'uso dell'applicazione Azure AD.

    > [!NOTE]
    > Per eseguire questo script, è necessario essere un membro del [ruolo di amministratore dell'applicazione Azure ad](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) .
    
    - Modificare la chiamata a Connect-AzureAD dello script di PowerShell per usare l'ID tenant del Azure AD.
    - Modificare la variabile dello script di PowerShell $myAzureADApplicationObjectId per usare l'ID oggetto dell'applicazione Azure AD
    - Eseguire lo script modificato.
    
1. Configurare l'azione del webhook protetta del gruppo di azione.
    - Copiare il valore $myApp. ObjectId dallo script e immetterlo nel campo ID oggetto applicazione nella definizione dell'azione webhook.
    
    ![Azione di Webhook sicura](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Script di PowerShell per il webhook sicuro

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
Per ulteriori informazioni importanti, vedere le [informazioni sulla limitazione della frequenza](./../../azure-monitor/platform/alerts-rate-limiting.md) e il comportamento degli [avvisi SMS](../../azure-monitor/platform/alerts-sms-behavior.md) . 

Un gruppo di azioni può contenere un numero limitato di azioni SMS.

Se l'interfaccia utente del gruppo di azioni portale di Azure non consente di selezionare il codice paese, SMS non è supportato per il proprio paese. I prezzi per i paesi supportati sono elencati nella [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). Se il codice paese non è disponibile, è possibile votare per aggiungere il paese alla [voce utente](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  

  

### <a name="voice"></a>Chiamata vocale
Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./../../azure-monitor/platform/alerts-rate-limiting.md) .

Un gruppo di azioni può contenere un numero limitato di azioni vocali.

Se l'interfaccia utente del gruppo di azioni portale di Azure non consente di selezionare il codice paese, le chiamate vocali non sono supportate per il proprio paese. I prezzi per i paesi supportati sono elencati nella [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). Se il codice paese non è disponibile, è possibile votare per aggiungere il paese alla [voce utente](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  

### <a name="webhook"></a>webhook
I webhook vengono ripetuti usando le regole seguenti. La chiamata al webhook viene ritentata fino a due volte quando vengono restituiti i codici di stato HTTP seguenti: 408, 429, 503, 504 o l'endpoint HTTP non risponde. La prima ripetizione del tentativo avviene dopo 10 secondi. la seconda dopo 100 secondi. Dopo due errori, nessun gruppo di azione chiamerà l'endpoint per 30 minuti. 

Intervalli di indirizzi IP di origine
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Per ricevere gli aggiornamenti relativi alle modifiche apportate a questi indirizzi IP, è consigliabile configurare un avviso di integrità del servizio che monitora le notifiche informative relative al servizio gruppi di azioni.

Un gruppo di azioni può contenere un numero limitato di azioni webhook.



## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [Comportamento degli avvisi SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Leggere le [informazioni sullo schema webhook degli avvisi del log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Altre informazioni sul [connettore ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Altre informazioni sulla [limitazione della frequenza](../../azure-monitor/platform/alerts-rate-limiting.md) degli avvisi.
* Leggere una [panoramica degli avvisi del log attività](../../azure-monitor/platform/alerts-overview.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
