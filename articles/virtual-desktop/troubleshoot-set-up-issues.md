---
title: Creazione del pool host dell'ambiente desktop virtuale Windows-Azure
description: Come risolvere i problemi e risolvere i problemi relativi ai pool di tenant e host durante l'installazione di un ambiente desktop virtuale di Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0a5439a9d1fd43154379c1dc1a95a6e98b6e877b
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539636"
---
# <a name="host-pool-creation"></a>Creazione di pool di host

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md).

In questo articolo vengono illustrati i problemi durante la configurazione iniziale del tenant di desktop virtuale Windows e l'infrastruttura del pool di host sessione correlata.

## <a name="provide-feedback"></a>Fornire il feedback

Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Acquisizione dell'immagine multisessione Enterprise di Windows 10

Per usare l'immagine multisessione Enterprise di Windows 10, passare a Azure Marketplace, selezionare **Introduzione** a  >  **Microsoft Windows 10** > e [Windows 10 Enterprise multisessione, versione 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Problemi relativi all'utilizzo del portale di Azure per creare pool host

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Errore: l'opzione "crea un account gratuito" viene visualizzata quando si accede al servizio

> [!div class="mx-imgBorder"]
> ![Immagine che mostra la portale di Azure che Visualizza il messaggio "crea un account gratuito"](media/create-new-account.png)

**Causa**: non sono presenti sottoscrizioni attive nell'account con cui è stato effettuato l'accesso ad Azure oppure l'account non dispone delle autorizzazioni per visualizzare le sottoscrizioni.

**Correzione**: accedere alla sottoscrizione in cui verranno distribuite le macchine virtuali (VM) host della sessione con un account che dispone almeno dell'accesso a livello di collaboratore.

### <a name="error-exceeding-quota-limit"></a>Errore: "superamento del limite di quota"

Se l'operazione supera il limite di quota, è possibile eseguire una delle operazioni seguenti:

- Creare un nuovo pool di host con gli stessi parametri, ma un minor numero di VM e Core VM.

- Aprire il collegamento visualizzato nel campo statusMessage in un browser per inviare una richiesta di aumento della quota per la sottoscrizione di Azure per lo SKU di VM specificato.

### <a name="error-cant-see-user-assignments-in-app-groups"></a>Errore: non è possibile visualizzare le assegnazioni utente nei gruppi di app.

Motivo: questo errore si verifica in genere dopo lo spostamento della sottoscrizione da 1 tenant Azure Active Directory (AD) a un altro. Se le assegnazioni precedenti sono ancora legate al tenant Azure AD precedente, il portale di Azure ne rileverà la perdita.

Correzione: è necessario riassegnare gli utenti ai gruppi di app.

## <a name="azure-resource-manager-template-errors"></a>Errori del modello di Azure Resource Manager

Seguire queste istruzioni per risolvere i problemi relativi alle distribuzioni non riuscite dei modelli di Azure Resource Manager e di PowerShell DSC.

1. Esaminare gli errori nella distribuzione usando [Visualizza operazioni di distribuzione con Azure Resource Manager](../azure-resource-manager/templates/deployment-history.md).
2. Se non sono presenti errori nella distribuzione, esaminare gli errori nel log attività usando [Visualizza log attività per controllare le azioni sulle risorse](../azure-resource-manager/management/view-activity-logs.md).
3. Una volta identificato l'errore, usare il messaggio di errore e le risorse in [risolvere gli errori comuni di distribuzione di Azure con Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md) per risolvere il problema.
4. Eliminare tutte le risorse create durante la distribuzione precedente e riprovare a distribuire il modello.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Errore: la distribuzione non è riuscita... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![Screenshot della distribuzione non riuscita.](media/failure-joindomain.png)

Esempio di errore non elaborato:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Cause 1:** Le credenziali specificate per l'aggiunta di macchine virtuali al dominio non sono corrette.

**Correzione 1:** Vedere l'errore "credenziali non corrette" per le macchine virtuali non appartenenti al dominio nella [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).

**Motivo 2:** Il nome di dominio non viene risolto.

**Correzione 2:** Vedere [errore: il nome di dominio non viene risolto](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) nella [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).

**Motivo 3:** La configurazione DNS della rete virtuale (VNET) è impostata sul **valore predefinito**.

Per risolvere il problema, eseguire le operazioni seguenti:

1. Aprire il portale di Azure e passare alla scheda **reti virtuali** .
2. Individuare il VNET, quindi selezionare **server DNS**.
3. Il menu server DNS verrà visualizzato sul lato destro dello schermo. Nel menu selezionare **personalizzato**.
4. Verificare che i server DNS elencati in personalizzato corrispondano al controller di dominio o al dominio Active Directory. Se il server DNS non è visibile, è possibile aggiungerlo immettendo il relativo valore nel campo **Aggiungi server DNS** .

### <a name="error-your-deployment-failedunauthorized"></a>Errore: La distribuzione non è riuscita...\Non autorizzato

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Motivo:** La sottoscrizione in uso è un tipo che non può accedere alle funzionalità necessarie nell'area in cui il cliente sta provando a eseguire la distribuzione. Ad esempio, gli abbonamenti MSDN, Free o Education possono visualizzare questo errore.

**Correzione:** Modificare il tipo di sottoscrizione o l'area geografica in modo da poter accedere alle funzionalità necessarie.

### <a name="error-vmextensionprovisioningerror"></a>Errore: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![Lo screenshot della distribuzione non è riuscito con lo stato di provisioning del terminale non riuscito.](media/failure-vmextensionprovisioning.png)

**Cause 1:** Errore temporaneo con l'ambiente desktop virtuale di Windows.

**Motivo 2:** Errore temporaneo con connessione.

**Correzione:** Verificare che l'ambiente desktop virtuale Windows sia integro eseguendo l'accesso con PowerShell. Completare manualmente la registrazione della macchina virtuale in [creare un pool di host con PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Errore: Il nome utente amministratore specificato non è consentito

> [!div class="mx-imgBorder"]
> ![Screenshot della distribuzione non riuscita. l'amministratore specificato non è consentito.](media/failure-username.png)

Esempio di errore non elaborato:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Motivo:** La password specificata contiene sottostringhe non consentite (admin, Administrator, root).

**Correzione:** Aggiornare il nome utente o usare utenti diversi.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Errore: La macchina virtuale ha segnalato un errore durante l'elaborazione dell'estensione 

> [!div class="mx-imgBorder"]
> ![Screenshot dell'operazione della risorsa completata con lo stato di provisioning terminal nella distribuzione non riuscita.](media/failure-processing.png)

Esempio di errore non elaborato:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Motivo:** PowerShell DSC Extension non è stato in grado di ottenere l'accesso amministrativo alla macchina virtuale.

**Correzione:** Verificare che il nome utente e la password dispongano dell'accesso amministrativo alla macchina virtuale ed eseguire di nuovo il modello di Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Errore: DeploymentFailed-la configurazione DSC di PowerShell ' FirstSessionHost ' è stata completata con errori

> [!div class="mx-imgBorder"]
> ![Screenshot della distribuzione non riuscito con la configurazione DSC di PowerShell ' FirstSessionHost ' completata con errori.](media/failure-dsc.png)

Esempio di errore non elaborato:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Motivo:** PowerShell DSC Extension non è stato in grado di ottenere l'accesso amministrativo alla macchina virtuale.

**Correzione:** Verificare che il nome utente e la password specificati dispongano dell'accesso amministrativo alla macchina virtuale ed eseguire di nuovo il modello di Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Errore: DeploymentFailed-InvalidResourceReference

Esempio di errore non elaborato:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Motivo:** Parte del nome del gruppo di risorse viene usata per determinate risorse create dal modello. A causa del nome che corrisponde alle risorse esistenti, il modello può selezionare una risorsa esistente da un gruppo diverso.

**Correzione:** Quando si esegue il modello di Azure Resource Manager per distribuire le VM host sessione, rendere i primi due caratteri univoci per il nome del gruppo di risorse di sottoscrizione.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Errore: DeploymentFailed-InvalidResourceReference

Esempio di errore non elaborato:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Motivo:** Questo errore è dovuto al fatto che la scheda di interfaccia di rete creata con il modello di Azure Resource Manager ha lo stesso nome di un'altra NIC già presente in VNET.

**Correzione:** Usare un prefisso host diverso.

### <a name="error-deploymentfailed--error-downloading"></a>Errore: DeploymentFailed – errore durante il download

Esempio di errore non elaborato:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Motivo:** Questo errore è dovuto a una route statica, a una regola del firewall o a un NSG che blocca il download del file zip associato al modello di Azure Resource Manager.

**Correzione:** Rimuovere la route statica di blocco, la regola del firewall o NSG. Facoltativamente, aprire il file JSON del modello di Azure Resource Manager in un editor di testo, fare il collegamento al file zip e scaricare la risorsa in un percorso consentito.

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>Errore: non è possibile eliminare un host sessione dal pool host dopo aver eliminato la macchina virtuale

**Motivo:** Prima di eliminare la macchina virtuale, è necessario eliminare l'host sessione.

**Correzione:** Posizionare l'host sessione in modalità di svuotamento, disconnettersi tutti gli utenti dall'host sessione, quindi eliminare l'host.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a Desktop virtuale Windows e alle tracce di escalation, consultare [Panoramica della risoluzione dei problemi, feedback e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Desktop virtuale Windows, consultare [Configurazione di macchine virtuali nell'host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alla connettività della sessione o dell'agente desktop virtuale di Windows, vedere risolvere i problemi comuni relativi all' [agente desktop virtuale di Windows](troubleshoot-agent.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [connessioni al servizio desktop virtuale di Windows](troubleshoot-service-connection.md).
- Per risolvere i problemi relativi ai client di Desktop remoto, vedere [risoluzione dei problemi del client di desktop remoto](troubleshoot-client.md)
- Per risolvere i problemi relativi all'uso di PowerShell con Desktop virtuale di Windows, consultare [PowerShell con Desktop virtuale Windows](troubleshoot-powershell.md).
- Per ulteriori informazioni sul servizio, vedere [ambiente desktop virtuale di Windows](environment-setup.md).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi delle distribuzioni dei modelli di Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](../azure-resource-manager/templates/deployment-history.md).
