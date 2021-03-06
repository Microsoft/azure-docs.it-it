---
title: Connettere macchine virtuali ibride ad Azure dal portale di Azure
description: Questo articolo illustra come installare l'agente e connettere i computer ad Azure usando i server abilitati per Azure Arc dal portale di Azure.
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: d7a89db7b8a42476a312a8f9a96c5ad230b140a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183149"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Connettere macchine virtuali ibride ad Azure dal portale di Azure

È possibile abilitare i server abilitati per Azure Arc per uno o un numero ridotto di computer Windows o Linux nell'ambiente eseguendo manualmente un set di passaggi. Si può anche usare un metodo automatico eseguendo uno script di modello fornito da Microsoft. Lo script automatizza il download e l'installazione di entrambi gli agenti.

Con questo metodo sono necessarie le autorizzazioni di amministratore sulla macchina virtuale per installare e configurare l'agente. In Linux, tramite l'account radice, e in Windows, si è membri del gruppo Administrators locale.

Prima di iniziare, esaminare i [prerequisiti](agent-overview.md#prerequisites) e verificare che la sottoscrizione e le risorse soddisfino i requisiti. Per informazioni sulle aree supportate e altre considerazioni correlate, vedere [aree di Azure supportate](overview.md#supported-regions).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generare lo script di installazione dal portale di Azure

Lo script per automatizzare il download e l'installazione e stabilire la connessione con Azure Arc è disponibile nel portale di Azure. Per completare il processo, seguire questa procedura:

1. Nel browser passare al [portale di Azure](https://portal.azure.com).

1. In alto a sinistra nella pagina **Server - Azure Arc** selezionare **Aggiungi**.

1. Nella pagina **Selezionare un metodo** selezionare il riquadro **Aggiungi server tramite script interattivo** e quindi **Genera script**.

1. Nella pagina **Genera script** selezionare la sottoscrizione e il gruppo di risorse in cui si vuole che la macchina virtuale venga gestita in Azure. Selezionare una località di Azure in cui verranno archiviati i metadati della macchina virtuale. La località può essere uguale o diversa da quella del gruppo di risorse.

1. Nella pagina **Prerequisiti** esaminare le informazioni e quindi selezionare **Avanti: Dettagli risorsa**.

1. Nella pagina **Dettagli risorsa** specificare quanto segue:

    1. Nell'elenco a discesa **Gruppo di risorse** selezionare il gruppo di risorse da cui verrà gestito il computer.
    1. Nell'elenco a discesa **Area** selezionare l'area di Azure in cui archiviare i metadati dei server.
    1. Nell'elenco a discesa **sistema operativo** selezionare il sistema operativo in cui è configurato lo script per l'esecuzione.
    1. Se il computer comunica tramite un server proxy per connettersi a Internet, specificare l'indirizzo IP del server proxy o il nome e il numero di porta che il computer userà per le comunicazioni. Immettere il valore nel formato `http://<proxyURL>:<proxyport>`.
    1. Selezionare **Avanti: Tag**.

1. Nella pagina **Tag** esaminare l'impostazione predefinita e suggerita di **Tag di località fisica** e immettere un valore oppure specificare una o più opzioni per **Tag personalizzati** in base ai propri standard.

1. Selezionare **Avanti: Scarica ed esegui script**.

1. Nella pagina **Scarica ed esegui script** esaminare le informazioni di riepilogo e quindi selezionare **Scarica**. Se occorre ancora apportare modifiche, selezionare **Indietro**.

## <a name="install-and-validate-the-agent-on-windows"></a>Installare e convalidare l'agente in Windows

### <a name="install-manually"></a>Eseguire l'installazione manuale

È possibile installare manualmente l'agente Connected Machine eseguendo il pacchetto di Windows Installer *AzureConnectedMachineAgent.msi*. È possibile scaricare la versione più recente del [Pacchetto Windows Installer dell'agente Windows](https://aka.ms/AzureConnectedMachineAgent) dall'Area download Microsoft.

>[!NOTE]
>* Per installare o disinstallare l'agente, sono necessarie le autorizzazioni di *Amministratore*.
>* Occorre pima di tutto scaricare e copiare il pacchetto di installazione in una cartella nel server di destinazione o da una cartella di rete condivisa. Se si esegue il pacchetto di installazione senza opzioni, si avvia una procedura guidata che consente di completare l'installazione dell'agente in modo interattivo.

Se il computer deve comunicare tramite un server proxy al servizio, dopo l'installazione dell'agente è necessario eseguire un comando descritto nella procedura seguente. Questo comando imposta la variabile di ambiente del sistema del server proxy `https_proxy` .

Se non si ha familiarità con le opzioni della riga di comando per i pacchetti Windows Installer, vedere [Opzioni della riga di comando di Msiexec standard](/windows/win32/msi/standard-installer-command-line-options) e [Opzioni della riga di comando di Msiexec](/windows/win32/msi/command-line-options).

Ad esempio, eseguire il programma di installazione con il parametro `/?` per esaminare la Guida e opzione per l'accesso a informazioni di riferimento rapido.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. Per installare l'agente in modo invisibile all'utente e creare un file di log del programma di installazione nella cartella `C:\Support\Logs` esistente, eseguire il comando seguente.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. La directory di log è *%ProgramData%\AzureConnectedMachineAgent\log*.

2. Se il computer deve comunicare tramite un server proxy, per impostare la variabile di ambiente del server proxy, eseguire il comando seguente:

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >L'agente non supporta l'impostazione dell'autenticazione proxy in questa versione di anteprima.
    >

3. Dopo aver installato l'agente, è necessario configurarlo per la comunicazione con il servizio Azure Arc eseguendo il comando seguente:

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>Eseguire l'installazione con il metodo con script

1. Accedere al server.

1. Aprire un prompt dei comandi di PowerShell con privilegi elevati.

    >[!NOTE]
    >Lo script supporta solo l'esecuzione da una versione a 64 bit di Windows PowerShell.
    >

1. Passare alla cartella o alla condivisione in cui è stato copiato lo script ed eseguirlo sul server eseguendo lo script `./OnboardingScript.ps1`.

Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. La directory di log è *%ProgramData%\AzureConnectedMachineAgent\log*.

## <a name="install-and-validate-the-agent-on-linux"></a>Installare e convalidare l'agente in Linux

L'agente Connected Machine per Linux viene fornito nel formato di pacchetto preferito per la distribuzione (RPM o DEB) ospitata nel [repository dei pacchetti](https://packages.microsoft.com/) di Microsoft. Il [bundle di script della shell `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) esegue le azioni seguenti:

* Configura il computer host per scaricare il pacchetto dell'agente da packages.microsoft.com.

* Installa il pacchetto del provider di risorse ibride.

Facoltativamente, è possibile configurare l'agente con le informazioni del proxy includendo il parametro `--proxy "{proxy-url}:{proxy-port}"`.

Lo script contiene anche la logica per identificare le distribuzioni supportate e non supportate e verifica le autorizzazioni necessarie per eseguire l'installazione.

L'esempio seguente scarica l'agente e lo installa:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

1. Per scaricare e installare l'agente, incluso il parametro `--proxy` per configurare l'agente per la comunicazione tramite il server proxy, eseguire i comandi seguenti:

    ```bash
    # Download the installation package.
    wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

    # Install the connected machine agent.
    bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
    ```

2. Dopo aver installato l'agente, è necessario configurarlo per la comunicazione con il servizio Azure Arc eseguendo il comando seguente:

    ```bash
    azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --cloud "cloudName"
    if [ $? = 0 ]; then echo "\033[33mTo view your onboarded server(s), navigate to https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2Fmachines\033[m"; fi
    ```

### <a name="install-with-the-scripted-method"></a>Eseguire l'installazione con il metodo con script

1. Accedere al server con un account con accesso alla radice.

1. Passare alla cartella o alla condivisione in cui è stato copiato lo script ed eseguirlo sul server eseguendo lo script `./OnboardingScript.sh`.

Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. La directory dei log è *var/opt/azcmagent/log*.

## <a name="verify-the-connection-with-azure-arc"></a>Verificare la connessione con Azure Arc

Dopo aver installato l'agente e averlo configurato per la connessione ai server abilitati per Azure Arc, passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare le proprie macchine virtuali nel [portale di Azure](https://aka.ms/hybridmachineportal).

![Una connessione server riuscita](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passaggi successivi

- Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).

- Informazioni su come gestire il computer usando [criteri di Azure](../../governance/policy/overview.md), ad esempio la [configurazione Guest](../../governance/policy/concepts/guest-configuration.md)della VM, verificare che il computer stia segnalando l'area di lavoro log Analytics prevista, abilitare il monitoraggio con [monitoraggio di Azure con le macchine virtuali](../../azure-monitor/vm/vminsights-enable-policy.md)e molto altro ancora.

- Altre informazioni sull'[agente Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si desidera raccogliere dati di monitoraggio del carico di lavoro e del sistema operativo con Monitoraggio di Azure per le macchine virtuali, gestirli usando manuali operativi di automazione o funzionalità come Gestione aggiornamenti oppure usare altri servizi di Azure come il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).