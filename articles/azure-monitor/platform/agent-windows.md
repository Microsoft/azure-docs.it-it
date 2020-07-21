---
title: Connettere computer Windows a Monitoraggio di Azure | Microsoft Docs
description: Questo articolo descrive come connettere i computer Windows ospitati in altri cloud o in locale a Monitoraggio di Azure con l'agente di Log Analytics per Windows.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 80ece5b0704869c31ab0656eed922b3f21ba9928
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505755"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Connettere computer Windows a Monitoraggio di Azure

Per monitorare e gestire macchine virtuali o computer fisici nel centro dati locale o in un altro ambiente cloud con Monitoraggio di Azure, è necessario distribuire l'agente di Log Analytics (denominato anche Microsoft Monitoring Agent (MMA)) e configurarlo per fare riferimento a una o più aree di lavoro di Log Analytics. L'agente supporta anche il ruolo di lavoro ibrido per runbook per Automazione di Azure.  

In un computer Windows monitorato, l'agente viene elencato come il servizio Microsoft Monitoring Agent. Il servizio Microsoft Monitoring Agent raccoglie gli eventi dal file di log e dal registro eventi di Windows, i dati sulle prestazioni e altri dati di telemetria. Anche quando l'agente non è in grado di comunicare con il servizio di Monitoraggio di Azure al quale fa riferimento, l'agente continua a essere eseguito e accoda i dati raccolti sul disco del computer monitorato. Quando la connessione viene ripristinata, il servizio Microsoft Monitoring Agent invia i dati raccolti al servizio.

L'agente può essere installato usando uno dei metodi seguenti. La maggior parte delle installazioni usa una combinazione di questi metodi per installare diversi set di computer, se necessario.  I dettagli sull'uso di ogni metodo sono disponibili più avanti nell'articolo.

* Installazione manuale. Il programma di installazione viene eseguito manualmente nel computer mediante la procedura di installazione guidata, dalla riga di comando o distribuito tramite uno strumento di distribuzione software esistente.
* Configurazione dello stato desiderato (DSC) di Automazione di Azure. Uso di DSC in Automazione di Azure con uno script per i computer Windows già distribuiti nell'ambiente in uso.  
* Script di PowerShell.
* Modello di Resource Manager per le macchine virtuali con Windows in esecuzione in locale in Azure Stack. 

>[!NOTE]
>Il Centro sicurezza di Azure dipende da Microsoft Monitoring Agent (noto anche come agente Windows di Log Analytics) e lo installerà e configurerà in modo da creare un report in un'area di lavoro di Log Analytics come parte della distribuzione. ASC include un'opzione di provisioning automatico che consente l'installazione automatica dell'agente Windows di Log Analytics in tutte le macchine virtuali nella sottoscrizione e la configura in modo da creare un report in un'area di lavoro specifica. Per altre informazioni su questa opzione, vedere [Abilitare il provisioning automatico dell'agente di Log Analytics](../../security-center/security-center-enable-data-collection.md#auto-provision-mma).
>

Se è necessario configurare l'agente per la segnalazione a più di un'area di lavoro, questa operazione non può essere eseguita durante l'installazione iniziale, solo successivamente aggiornando le impostazioni dal pannello di controllo o da PowerShell come descritto in [Aggiunta o rimozione di un'area di lavoro](agent-manage.md#adding-or-removing-a-workspace).  

Per comprendere la configurazione supportata, vedere i [sistemi operativi Windows supportati](log-analytics-agent.md#supported-windows-operating-systems) e la [configurazione del firewall di rete](log-analytics-agent.md#network-requirements).

## <a name="obtain-workspace-id-and-key"></a>Ottenere l'ID e la chiave dell'area di lavoro
Prima di installare l'agente di Log Analytics per Windows, sono necessari l'ID e la chiave dell'area di lavoro per l'area di lavoro Log Analytics.  Queste informazioni sono richieste durante l'installazione da ogni metodo di installazione per configurare correttamente l'agente e verificare che possa comunicare correttamente con Monitoraggio di Azure nel cloud Azure Commercial e US Government. 

1. Nel portale di Azure cercare e selezionare **Aree di lavoro di Log Analytics**.
2. Nell'elenco delle aree di lavoro di Log Analytics selezionare quella che si intende configurare per l'uso come riferimento da parte dell'agente.
3. Selezionare **Impostazioni avanzate**.<br><br> ![Impostazioni avanzate di Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Selezionare **Origini connesse** e quindi **Server Windows**.   
5. Copiare e incollare nell'editor preferito l'**ID area di lavoro** e la **chiave primaria**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Configurare l'agente per usare TLS 1.2
Per configurare, usare il protocollo [TLS 1.2](/windows-server/security/tls/tls-registry-settings#tls-12) per la comunicazione tra l'agente di Windows e il servizio Log Analytics, è possibile seguire la procedura seguente per abilitare prima che l'agente venga installato nella macchina virtuale o in un secondo momento.

>[!NOTE]
>Se si sta configurando una macchina virtuale che esegue Windows Server 2008 SP2 x64 per l'uso di TLS 1.2, prima di eseguire i passaggi seguenti è necessario installare l'[aggiornamento del supporto della firma del codice SHA-2](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) seguente. 
>

1. Individuare la seguente sottochiave del registro di sistema: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Creare una sottochiave in **Protocolli** per TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Creare una sottochiave **Client** sotto la sottochiave della versione di protocollo TLS 1.2 creata in precedenza. Ad esempio, **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Creare i valori DWORD seguenti in **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Enabled** [Value = 1]
    * **DisabledByDefault** [Value = 0]  

Configurare .NET Framework 4.6 o versione successiva per supportare la crittografia sicura, dal momento che è disabilitata per impostazione predefinita. La [crittografia avanzata](/dotnet/framework/network-programming/tls#schusestrongcrypto) usa protocolli di rete più sicuri, come TLS 1.2 che blocca i protocolli non sicuri. 

1. Individuare la seguente sottochiave del registro di sistema: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Creare il valore DWORD **SchUseStrongCrypto** sotto questa sottochiave con valore **1**.  
3. Individuare la seguente sottochiave del registro di sistema: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Creare il valore DWORD **SchUseStrongCrypto** sotto questa sottochiave con valore **1**. 
5. Riavviare il sistema per rendere effettive le impostazioni. 

## <a name="install-the-agent-using-setup-wizard"></a>Installare l'agente con l'installazione guidata
I passaggi seguenti consentono di installare e configurare l'agente di Log Analytics nel cloud di Azure e di Azure per enti pubblici, usando l'installazione guidata per l'agente nel computer. Per altre informazioni su come configurare l'agente in modo che inoltri segnalazioni a un gruppo di gestione di System Center Operations Manager, vedere [distribuire l'agente di Operations Manager con l'installazione guidata dell'agente](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. Nell'area di lavoro Log Analytics, dalla pagina **Server Windows** a cui si è passati in precedenza, selezionare la versione appropriata da scaricare in **Scarica agente Windows** a seconda dell'architettura del processore del sistema operativo Windows.   
2. Eseguire il programma di installazione per installare l'agente nel computer in uso.
2. Nella pagina di **benvenuto** fare clic su **Avanti**.
3. Nella pagina **Condizioni di licenza** leggere la licenza e quindi fare clic su **Accetto**.
4. Nella pagina **Cartella di destinazione** modificare o mantenere la cartella di installazione predefinita e quindi fare clic su **Avanti**.
5. Nella pagina **Opzioni di installazione dell'agente** scegliere di connettere l'agente ad Azure Log Analytics e quindi fare clic su **Avanti**.   
6. Nella pagina **Azure Log Analytics** eseguire le operazioni seguenti:
   1. Incollare **ID area di lavoro** e **Chiave dell'area di lavoro (Chiave primaria)** copiati in precedenza.  Se il computer deve fare riferimento a un'area di lavoro Log Analytics nel cloud di Azure per enti pubblici, selezionare **Azure US Gov** nell'elenco a discesa **Cloud di Azure**.  
   2. Se il computer deve comunicare tramite un server proxy con il servizio Log Analytics, fare clic su **Avanzate** e specificare l'URL e il numero di porta del server proxy.  Se il server proxy richiede l'autenticazione, digitare il nome utente e la password per l'autenticazione nel server proxy, quindi fare clic su **Avanti**.  
7. Fare clic su **Avanti** dopo aver specificato le impostazioni di configurazione necessarie.<br><br> ![incollare ID area di lavoro e chiave primaria](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Nella pagina **Pronto per l'installazione** rivedere le scelte effettuate e quindi fare clic su **Installa**.
9. Nella pagina **Configurazione completata** fare clic su **Fine**.

Al termine, verrà visualizzato **Microsoft Monitoring Agent** nel **Pannello di controllo**. Per confermare che fa riferimento a Log Analytics, rivedere [Verificare la connettività dell'agente a Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Installare l'agente usando la riga di comando
Il file scaricato per l'agente è un pacchetto di installazione autonomo.  Il programma di installazione per l'agente e i file di supporto sono contenuti nel pacchetto e devono essere estratti per eseguire correttamente l'installazione tramite la riga di comando mostrata negli esempi seguenti.    

>[!NOTE]
>Per aggiornare un agente è necessario usare l'API di scripting di Log Analytics. Per altre informazioni, vedere l'argomento [Gestione e manutenzione dell'agente di Log Analytics per Windows e Linux](agent-manage.md).

La tabella seguente illustra i parametri specifici supportati dal programma di installazione per l'agente, inclusi quelli distribuiti tramite Automation DSC.

|Opzioni specifiche di MMA                   |Note         |
|---------------------------------------|--------------|
| NOAPM=1                               | Parametro facoltativo. Installa l'agente senza .NET Application Performance Monitoring.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Configura l'agente per fare riferimento a un'area di lavoro                |
|OPINSIGHTS_WORKSPACE_ID                | ID dell'area di lavoro (guid) per l'area di lavoro da aggiungere                    |
|OPINSIGHTS_WORKSPACE_KEY               | Chiave dell'area di lavoro usata per autenticarsi inizialmente nell'area di lavoro |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Specificare l'ambiente cloud in cui si trova l'area di lavoro <br> 0 = Cloud commerciale di Azure (impostazione predefinita) <br> 1 = Azure per enti pubblici |
|OPINSIGHTS_PROXY_URL               | URI per il proxy da usare |
|OPINSIGHTS_PROXY_USERNAME               | Nome utente per accedere a un proxy autenticato |
|OPINSIGHTS_PROXY_PASSWORD               | Password per accedere a un proxy autenticato |

1. Per estrarre i file di installazione dell'agente, da un prompt dei comandi con privilegi elevati eseguire `MMASetup-<platform>.exe /c` e verrà chiesto il percorso in cui estrarre i file.  In alternativa, è possibile specificare il percorso passando gli argomenti `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Per installare in modo invisibile l'agente e configurarlo in modo da fare riferimento a un'area di lavoro nel cloud commerciale di Azure, dalla cartella da cui sono stati estratti i file di installazione, digitare: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   o per configurare l'uso come riferimento del cloud per US Gov di Azure, digitare: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >I valori stringa per i parametri *OPINSIGHTS_WORKSPACE_ID* e *OPINSIGHTS_WORKSPACE_KEY* devono essere racchiusi tra virgolette doppie per indicare a Windows Installer di interpretarli come opzioni valide per il pacchetto. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Installare l'agente usando DSC in Automazione di Azure

È possibile usare lo script di esempio riportato di seguito per installare l'agente usando DSC in Automazione di Azure.   Se non si dispone di un account di Automazione, vedere [Guida introduttiva di automazione di Azure](../../automation/index.yml) per comprendere i requisiti e la procedura per la creazione di un account di Automazione richiesto per usare Automation DSC.  Se non si ha familiarità con Automation DSC, esaminare [Introduzione ad Automation DSC](../../automation/automation-dsc-getting-started.md).

L'esempio seguente illustra come installare l'agente a 64 bit, identificato dal valore `URI`. È possibile utilizzare anche la versione a 32 bit sostituendo il valore dell'URI. Gli URI per entrambe le versioni sono:

- Agente Windows a 64 bit: https://go.microsoft.com/fwlink/?LinkId=828603
- Agente Windows a 32 bit: https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Questo esempio di procedura e script non supporta l'aggiornamento dell'agente già distribuito in un computer Windows.

Le versioni a 32 e 64 bit del pacchetto agente dispongono di un codice prodotto diverso e le nuove versioni rilasciate dispongono inoltre di un valore univoco.  Il codice prodotto è un GUID che rappresenta l'identificazione principale di un'applicazione o di un prodotto e viene rappresentato dalla proprietà **Codice prodotto** di Windows Installer.  Il valore `ProductId` nello script **MMAgent.ps1** deve corrispondere al codice prodotto dal pacchetto di installazione dell'agente a 32 bit o 64 bit.

Per recuperare direttamente il codice prodotto dal pacchetto di installazione dell'agente, è possibile usare Orca.exe dal [Componenti di Windows SDK per sviluppatori di Windows Installer](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers) che è un componente del Windows Software Development Kit o usare PowerShell seguendo uno [script di esempio](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) scritto da un Microsoft Valuable Professional (MVP).  Per entrambi gli approcci, è necessario innanzitutto estrarre il file **MOMagent.msi** dal pacchetto di installazione MMASetup.  Questa operazione è illustrata in precedenza nel primo passaggio nella sezione [Installare l'agente usando la riga di comando](#install-the-agent-using-the-command-line).  

1. Importare il modulo DSC xPSDesiredStateConfiguration da [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) in Automazione di Azure.  
2.    Creare gli asset variabili di Automazione di Azure per *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Impostare *OPSINSIGHTS_WS_ID* sull'ID dell'area di lavoro Log Analytics e impostare *OPSINSIGHTS_WS_KEY* sulla chiave primaria dell'area di lavoro.
3.    Copiare lo script e salvarlo come MMAgent.ps1.

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. Aggiornare il valore `ProductId` nello script con il codice prodotto estratto dalla versione più recente del pacchetto di installazione dell'agente usando i metodi consigliati in precedenza. 
5. [Importare lo script di configurazione MMAgent.ps1](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation) nell'account di Automazione. 
6. [Assegnare un computer Windows o un nodo](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration) alla configurazione. Entro 15 minuti il nodo controlla la configurazione e viene effettuato il push dell'agente al nodo.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Verificare la connettività dell'agente a Log Analytics

Dopo aver completato l'installazione dell'agente, la verifica dell'avvenuta connessione e il riferimento possono essere eseguiti in due modi.  

Dal **Pannello di controllo** del computer, trovare l'elemento **Microsoft Monitoring Agent**.  Selezionarlo e, nella scheda **Azure Log Analytics** l'agente dovrebbe visualizzare un messaggio per segnalare che **Microsoft Monitoring Agent ha eseguito la connessione al servizio Microsoft Operations Management Suite.**<br><br> ![Stato della connessione di MMA a Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

È anche possibile eseguire una query di log semplice nel portale di Azure.  

1. Nel portale di Azure cercare e selezionare **Monitoraggio**.
1. Selezionare **Log** nel menu.
1. Nel riquadro **Log**, nel tipo di campo query:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Nei risultati della ricerca restituiti, devono venire visualizzati i record di heartbeat per il computer indicanti che è connesso e che invia report al servizio.   

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle modalità di riconfigurazione, aggiornamento o rimozione dell'agente dalla macchina virtuale, rivedere [Gestione e manutenzione dell'agente di Log Analytics per Windows e Linux](agent-manage.md).

- Se si verificano problemi di installazione o gestione dell'agente, vedere [Risoluzione dei problemi dell'agente Windows](agent-windows-troubleshoot.md).
