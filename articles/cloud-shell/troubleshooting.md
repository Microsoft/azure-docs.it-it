---
title: Risoluzione dei problemi di Azure Cloud Shell | Microsoft Docs
description: Risoluzione dei problemi di Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: b06deadae15a8176a49bed88a53884df2b71e473
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189463"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Risoluzione dei problemi e limitazioni di Azure Cloud Shell

Le soluzioni note per i problemi in Azure Cloud Shell includono:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Risoluzione dei problemi generali

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Errore durante l'esecuzione di cmdlet AzureAD in PowerShell

- **Dettagli**: quando si eseguono cmdlet AzureAD come `Get-AzureADUser` in cloud Shell, è possibile che venga visualizzato un errore `You must call the Connect-AzureAD cmdlet before calling any other cmdlets`:. 
- **Soluzione**: eseguire il `Connect-AzureAD` cmdlet. In precedenza Cloud Shell questo cmdlet veniva eseguito automaticamente durante l'avvio di PowerShell. Per velocizzare l'ora di avvio, il cmdlet non viene più eseguito automaticamente. È possibile scegliere di ripristinare il comportamento precedente aggiungendo `Connect-AzureAD` al file di $profile in PowerShell.

### <a name="early-timeouts-in-firefox"></a>Timeout anticipati in FireFox

- **Dettagli**: Cloud Shell utilizza una connessione WebSocket aperta per passare input/output al browser. FireFox ha criteri predefiniti che possono chiudere la connessione WebSocket in modo anomalo causando timeout anticipati in Cloud Shell.
- **Risoluzione**: aprire FireFox e digitare "about:config" nella casella URL per accedere alla schermata corrispondente. Cercare "network.websocket.timeout.ping.request" e modificare il valore da 0 a 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Disabilitazione di Cloud Shell in un ambiente di rete bloccato

- **Dettagli**: gli amministratori possono decidere di disabilitare l'accesso a Cloud Shell per gli utenti. Cloud Shell usa l'accesso al `ux.console.azure.com` dominio, che può essere negato, che impedisce l'accesso ai entrypoints di cloud Shell, tra cui portal.azure.com, shell.azure.com, Visual Studio Code estensione account azure e docs.Microsoft.com. Nel cloud del governo degli Stati Uniti, il `ux.console.azure.us`EntryPoint è; non esiste alcun shell.azure.us corrispondente.
- **Soluzione**: limitare l'accesso `ux.console.azure.com` o `ux.console.azure.us` tramite le impostazioni di rete all'ambiente. L'icona Cloud Shell continuerà a esistere nell'portale di Azure, ma non si connetterà correttamente al servizio.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Finestra di dialogo archiviazione - Errore: 403 RequestDisallowedByPolicy

- **Dettagli**: quando si crea un account di archiviazione tramite cloud Shell, l'operazione ha esito negativo a causa di un'assegnazione di criteri di Azure effettuata dall'amministratore. Il messaggio di errore includerà:`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Soluzione**: contattare l'amministratore di Azure per rimuovere o aggiornare l'assegnazione di criteri di Azure negando la creazione dell'archiviazione.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Finestra di dialogo archiviazione - Errore: 400 DisallowedOperation

- **Dettagli**: quando si usa una sottoscrizione di Azure Active Directory, non è possibile creare la risorsa di archiviazione.
- **Risoluzione**: usare una sottoscrizione di Azure in grado di creare le risorse di archiviazione. Le sottoscrizioni di Azure Active Directory non sono in grado di creare le risorse di Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Output del terminale - Errore: impossibile effettuare la connessione del terminale: non è possibile stabilire una connessione websocket. Premere `Enter` per ristabilire la connessione.
- **Dettagli**: Cloud Shell richiede la possibilità di stabilire una connessione websocket all'infrastruttura di Cloud Shell.
- **Risoluzione**: verificare di aver configurato le impostazioni di rete per abilitare l'invio di richieste https e di richieste websocket ai domini in *.console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Impostare la connessione di Cloud Shell per supportare l'uso di TLS 1.2
 - **Dettagli**: per definire la versione di TLS per la connessione cloud Shell, è necessario impostare le impostazioni specifiche del browser.
 - **Risoluzione**: passare alle impostazioni di sicurezza del browser e selezionare la casella di controllo accanto a "Usa TLS 1.2".

## <a name="bash-troubleshooting"></a>Risoluzione dei problemi di Bash

### <a name="cannot-run-the-docker-daemon"></a>Non è possibile eseguire il daemon Docker

- **Dettagli**: Cloud Shell prevede l'uso di un contenitore per ospitare l'ambiente della shell, pertanto l'esecuzione del daemon non è consentita.
- **Risoluzione**: usare [docker-machine](https://docs.docker.com/machine/overview/), installato per impostazione predefinita, per gestire i contenitori Docker da un host Docker remoto.

## <a name="powershell-troubleshooting"></a>Risoluzione dei problemi di PowerShell

### <a name="gui-applications-are-not-supported"></a>Le applicazioni GUI non sono supportate

- **Dettagli**: se un utente avvia un'applicazione GUI, il prompt non ricompare. Ad esempio, quando un utente clona un repository GitHub privato abilitato a due fattori di autenticazione, viene visualizzata una finestra di dialogo per completare l'autenticazione a due fattori.
- **Risoluzione**: chiudere e riaprire la shell.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Risoluzione dei problemi di gestione remota delle macchine virtuali di Azure
> [!NOTE]
> Le macchine virtuali di Azure devono avere un indirizzo IP pubblico.

- **Dettagli**: a causa delle impostazioni predefinite di Windows Firewall per la gestione remota Windows, all'utente può comparire l'errore seguente: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Risoluzione**: eseguire `Enable-AzVMPSRemoting` per abilitare tutti gli aspetti della comunicazione remota di PowerShell nel computer di destinazione.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` non memorizza il risultato nell'unità Azure

- **Dettagli**: per impostazione predefinita, i risultati di `dir` vengono memorizzati nell'unità Azure per ottimizzare l'esperienza utente.
- **Risoluzione**: dopo aver creato, aggiornato o rimosso una risorsa di Azure, eseguire `dir -force` per aggiornare i risultati nell'unità Azure.

## <a name="general-limitations"></a>Limitazioni generali

Di seguito vengono descritte le limitazioni note di Azure Cloud Shell:

### <a name="quota-limitations"></a>Limitazioni della quota

Azure Cloud Shell ha un limite di 20 utenti simultanei per ogni tenant per area. Se si tenta di aprire più sessioni simultanee rispetto al limite, verrà visualizzato l'errore "tenant utente Over quota". Se è necessario disporre di più sessioni aperte (ad esempio per le sessioni di training), contattare il supporto tecnico prima dell'utilizzo previsto per richiedere un aumento della quota.

Cloud Shell viene fornito come servizio gratuito ed è progettato per essere usato per configurare l'ambiente di Azure, non come piattaforma di calcolo per utilizzo generico. Un utilizzo automatizzato eccessivo può essere considerato in violazione delle condizioni di servizio di Azure e può comportare il blocco dell'accesso Cloud Shell.

### <a name="system-state-and-persistence"></a>Persistenza e stato del sistema

Il computer che distribuisce la sessione Cloud Shell è temporaneo e viene riciclato dopo 20 minuti di inattività della sessione. Cloud Shell richiede che sia montata una condivisione file di Azure. La sottoscrizione, quindi, deve essere in grado di configurare le risorse di archiviazione per accedere a Cloud Shell. Altre considerazioni di cui tenere conto:

- Con l'archiviazione montata vengono rese persistenti soltanto le modifiche apportate all'interno della directory `clouddrive`. In Bash anche la directory `$HOME` è permanente.
- Le condivisioni file di Azure possono essere implementate solo dall'interno dell'[area assegnata](persisting-shell-storage.md#mount-a-new-clouddrive).
  - In Bash, eseguire `env` per trovare l'area geografica impostata come `ACC_LOCATION`.
- File di Azure supporta solo account di archiviazione con ridondanza locale e account di archiviazione con ridondanza geografica.

### <a name="browser-support"></a>Supporto browser

Cloud Shell supporta le versioni più recenti dei browser seguenti:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari in modalità privata non è supportato.

### <a name="copy-and-paste"></a>Copiare e incollare

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limiti di consumo

Cloud Shell è pensato per l'uso interattivo e qualsiasi sessione non interattiva in esecuzione prolungata viene quindi interrotta senza preavviso.

### <a name="user-permissions"></a>Autorizzazioni utente

Le autorizzazioni sono impostate come utenti normali senza accesso SUDO. Qualsiasi installazione esterna alla directory `$Home` non è permanente.

## <a name="bash-limitations"></a>Limitazioni Bash

### <a name="editing-bashrc"></a>Modifica di .bashrc

Fare attenzione quando si modifica il file con estensione bashrc, poiché questa operazione può provocare errori imprevisti in Cloud Shell.

## <a name="powershell-limitations"></a>Limitazioni PowerShell

### <a name="preview-version-of-azuread-module"></a>Versione di anteprima del modulo AzureAD

Attualmente è disponibile `AzureAD.Standard.Preview`, una versione di anteprima del modulo basato su .NET Standard. Questo modulo offre la stessa funzionalità di `AzureAD`.

### <a name="sqlserver-module-functionality"></a>Funzionalità del modulo `SqlServer`

Il modulo `SqlServer` incluso in Cloud Shell include solo il supporto delle versioni precedenti di PowerShell Core. In particolare, `Invoke-SqlCmd` non è ancora disponibile.

### <a name="default-file-location-when-created-from-azure-drive"></a>Percorso file predefinito quando creato dall'unità Azure

Usando dei cmdlet di PowerShell, gli utenti non possono creare i file sotto l'unità Azure. Quando gli utenti creano nuovi file con altri strumenti, ad esempio vim o nano, i file vengono salvati nella cartella `$HOME` per impostazione predefinita.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Il completamento della scheda può dare luogo a un'eccezione PSReadline

Se la proprietà EditMode dell'utente in PSReadline è impostata su Emacs, l'utente tenta di visualizzare tutte le possibilità tramite il completamento scheda e le dimensioni della finestra sono troppo piccole per visualizzare tutte le possibilità, per cui PSReadline indicherà un'eccezione non gestita.

### <a name="large-gap-after-displaying-progress-bar"></a>Ampio spazio vuoto dopo aver visualizzato l'indicatore di stato

Se l'utente esegue un'azione o un comando che visualizza un indicatore di stato, come il completamento di una scheda nell'unità `Azure:`, è possibile che il cursore non sia impostato correttamente e che venga visualizzato uno spazio vuoto dove prima era presente l'indicatore di stato.

### <a name="random-characters-appear-inline"></a>Inline vengono visualizzati caratteri casuali

La sequenza di posizione del cursore, ad esempio `5;13R`, può essere visualizzata nell'input dell'utente. Questi caratteri possono essere rimossi manualmente.

## <a name="personal-data-in-cloud-shell"></a>Dati personali in Cloud Shell

Azure Cloud Shell tratta i dati personali dell'utente con molta cautela: i dati acquisiti e archiviati mediante il servizio Azure Cloud Shell vengono usati per offrire un'esperienza predefinita all'utente, considerando ad esempio l'ultima shell usata, le dimensioni e il tipo di carattere preferiti e i dettagli della condivisione dei file che supportano il clouddrive. Se si desidera esportare o eliminare i dati è possibile seguire le istruzioni seguenti.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Export
Al fine di **esportare** le impostazioni dell'utente che Cloud Shell salva, ad esempio shell preferita, dimensione e tipo di carattere, eseguire i comandi seguenti.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Eseguire questi comandi in Bash o PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Elimina
Al fine di **eliminare** le impostazioni dell'utente che Cloud Shell salva, ad esempio shell preferita, dimensione e tipo di carattere, eseguire i comandi seguenti. Al successivo avvio di Cloud Shell verrà richiesto di caricare di nuovo una condivisione file. 

>[!Note]
> L'attuale condivisione di File di Azure non verrà eliminata se si eliminano le impostazioni dell'utente. Aprire File di Azure per completare l'operazione.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Eseguire questi comandi in Bash o PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Limitazioni di Azure per enti pubblici
Azure Cloud Shell in Azure per enti pubblici è accessibile solo tramite l'portale di Azure.

>[!Note]
> La connessione a cloud DoD-High o Government DoD per Exchange Online non è attualmente supportata.
