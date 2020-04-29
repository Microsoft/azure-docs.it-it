---
title: Abilitare monitoraggio di Azure per un ambiente ibrido | Microsoft Docs
description: Questo articolo descrive come abilitare Monitoraggio di Azure per le macchine virtuali per un ambiente cloud ibrido che contiene una o più macchine virtuali.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480743"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Abilitare Monitoraggio di Azure per le macchine virtuali per un ambiente ibrido

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Questo articolo illustra come abilitare Monitoraggio di Azure per le macchine virtuali per le macchine virtuali o i computer fisici ospitati nel Data Center o in un altro ambiente cloud. Al termine di questo processo, è stato avviato il monitoraggio delle macchine virtuali nell'ambiente in uso e viene illustrato se si verificano problemi di prestazioni o di disponibilità.

Prima di iniziare, esaminare i [prerequisiti](vminsights-enable-overview.md) e verificare che la sottoscrizione e le risorse soddisfino i requisiti. Esaminare i requisiti e i metodi di distribuzione per l'[agente di Log Analytics per Linux e Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali non trasmette dati e non richiede modifiche ai firewall o alle porte. I dati della mappa vengono sempre trasmessi dall'agente Log Analytics al servizio monitoraggio di Azure, direttamente o tramite il [gateway di Operations Management Suite](../../azure-monitor/platform/gateway.md) se i criteri di sicurezza IT non consentono ai computer della rete di connettersi a Internet.

I passaggi per completare questa attività sono riepilogati come segue:

1. Installare Log Analytics Agent per Windows o Linux. Prima di installare l'agente, vedere l'articolo [Panoramica dell'agente log Analytics](../platform/log-analytics-agent.md) per informazioni sui prerequisiti di sistema e sui metodi di distribuzione.

2. Scaricare e installare Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali per [Windows](https://aka.ms/dependencyagentwindows) o [Linux](https://aka.ms/dependencyagentlinux).

3. Abilitare la raccolta dei contatori delle prestazioni.

4. Distribuire Monitoraggio di Azure per le macchine virtuali.

>[!NOTE]
>Le informazioni descritte in questo articolo per la distribuzione di Dependency Agent sono applicabili anche alla [soluzione mapping dei servizi](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>Installare Dependency Agent in Windows

Dependency Agent può essere installato manualmente nei computer Windows eseguendo `InstallDependencyAgent-Windows.exe`. Se si esegue questo file eseguibile senza opzioni, si avvia una procedura guidata che consente di completare l'installazione dell'agente in modo interattivo.

>[!NOTE]
>Per installare o disinstallare l'agente sono necessari i privilegi di *amministratore* .

La tabella seguente illustra i parametri supportati dal programma di installazione per l'agente dalla riga di comando.

| Parametro | Descrizione |
|:--|:--|
| /? | Restituisce un elenco delle opzioni della riga di comando. |
| /S | Esegue un'installazione invisibile all'utente, senza interazione da parte dell'utente. |

Ad esempio, per eseguire il programma di installazione con `/?` il parametro, immettere **installdependencyagent-Windows. exe/?**.

Per impostazione predefinita, i file di Dependency Agent per Windows si trovano in *C:\Programmi\Microsoft Dependency Agent*. Se non è possibile avviare l'agente di dipendenza dopo aver completato l'installazione, controllare i registri per informazioni dettagliate sull'errore. La directory dei log è *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Installare Dependency Agent in Linux

Dependency Agent viene installato nei server Linux con *InstallDependencyAgent-Linux64.bin*, uno script della shell con un file binario autoestraente. È possibile eseguire il file con `sh` oppure aggiungere autorizzazioni di esecuzione al file stesso.

>[!NOTE]
> Per installare o configurare l'agente è necessario l'accesso alla radice.
>

| Parametro | Descrizione |
|:--|:--|
| -help | Ottenere un elenco delle opzioni della riga di comando. |
| -S | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |
| --check | Controllare le autorizzazioni e il sistema operativo senza installare l'agente. |

Ad esempio, per eseguire il programma di installazione con `-help` il parametro, immettere **installdependencyagent-linux64. bin-Help**.

Installare Linux Dependency Agent come root eseguendo il comando `sh InstallDependencyAgent-Linux64.bin`.

Se Dependency Agent non si avvia, controllare i log per vedere le informazioni dettagliate sull'errore. Negli agenti Linux la directory di log è */var/opt/Microsoft/Dependency-Agent/log*.

I file relativi a Dependency Agent sono memorizzati nelle directory seguenti:

| File | Percorso |
|:--|:--|
| File core | /opt/microsoft/dependency-agent |
| File di registro | /var/opt/microsoft/dependency-agent/log |
| File di configurazione | /etc/opt/microsoft/dependency-agent/config |
| File eseguibili del servizio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| File binary di archiviazione | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Esempi di script di installazione

Per distribuire facilmente Dependency Agent in più server contemporaneamente, viene fornito lo script di esempio seguente per scaricare e installare Dependency Agent in Windows o Linux.

### <a name="powershell-script-for-windows"></a>Script di PowerShell per Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script della shell per Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

Per distribuire Dependency Agent tramite Desired State Configuration (DSC), è possibile usare il modulo xPSDesiredStateConfiguration con il codice di esempio seguente:

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Abilitare i contatori delle prestazioni

Se l'area di lavoro Log Analytics a cui fa riferimento la soluzione non è già configurata per la raccolta dei contatori delle prestazioni richiesti dalla soluzione, sarà necessario abilitare i contatori. Questa operazione può essere eseguita in uno dei due modi seguenti:
* Manualmente, come descritto in [Origini dati per le prestazioni di Windows e Linux in Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Scaricando ed eseguendo uno script di PowerShell disponibile dalla raccolta di [Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Distribuire Monitoraggio di Azure per le macchine virtuali

Questo metodo include un modello JSON che specifica la configurazione per abilitare i componenti della soluzione nell'area di lavoro Log Analytics.

Se non si sa come distribuire le risorse usando un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Per usare l'interfaccia della riga di comando di Azure, è prima necessario installare e usare l'interfaccia della riga di comando in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Per installare o aggiornare l'interfaccia della riga di comando di Azure, vedere [installare l'interfaccia della](https://docs.microsoft.com/cli/azure/install-azure-cli)riga di comando.

### <a name="create-and-execute-a-template"></a>Creare ed eseguire un modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Salvare il file come *installsolutionsforvminsights.json* in una cartella locale.

1. Acquisire i valori di *WorkspaceName*, *ResourceGroupName* e *WorkspaceLocation*. Il valore per *WorkspaceName* è il nome dell'area di lavoro Log Analytics. Il valore per *WorkspaceLocation* è l'area in cui è definita l'area di lavoro.

1. È possibile distribuire il modello usando il comando di PowerShell seguente:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente e include il risultato:

    ```powershell
    provisioningState       : Succeeded
    ```
   Dopo avere abilitato il monitoraggio, possono essere necessari circa 10 minuti prima di poter visualizzare lo stato di integrità e le metriche per il computer ibrido.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="vm-doesnt-appear-on-the-map"></a>La macchina virtuale non viene visualizzata nella mappa

Se l'installazione dell'agente di dipendenza ha avuto esito positivo, ma il computer non è visualizzato sulla mappa, diagnosticare il problema attenendosi alla procedura seguente.

1. Dependency Agent è stato installato correttamente? È possibile verificarlo controllando se il servizio è installato ed è in esecuzione.

    **Windows**: cercare il servizio denominato "Microsoft Dependency Agent".

    **Linux**: cercare il processo in esecuzione "Microsoft-Dependency-Agent".

2. Il piano [tariffario gratuito di log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Il piano gratuito consente fino a cinque computer univoci. Eventuali computer successivi non verranno visualizzati sulla mappa, anche se i cinque precedenti non inviano più dati.

3. Il computer invia i dati di log e delle prestazioni ai log di monitoraggio di Azure? Eseguire la query seguente per il computer:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Sono stati restituiti uno o più risultati? I dati sono aggiornati? In tal caso, l'agente di Log Analytics funziona correttamente e comunica con il servizio. In caso contrario, controllare l'agente sul server: [Risoluzione dei problemi dell'agente di Log Analytics per Windows](../platform/agent-windows-troubleshoot.md) o [Risoluzione dei problemi dell'agente di Log Analytics per Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Il computer viene visualizzato nella mappa ma non ha processi

Se il server è visualizzato sulla mappa, ma non contiene dati di processo o di connessione, questo indica che l'agente di dipendenza è stato installato e in esecuzione, ma il driver del kernel non è stato caricato.

Controllare il file C:\Programmi\Microsoft Dependency Agent\logs\wrapper.log per Windows o il file /var/opt/microsoft/dependency-agent/log/service.log per Linux. Le ultime righe del file dovrebbero indicare il motivo per cui il kernel non è stato caricato. Ad esempio, il kernel potrebbe non essere supportato in Linux se è stato aggiornato.


## <a name="next-steps"></a>Passaggi successivi

Ora che il monitoraggio è abilitato per le macchine virtuali, queste informazioni sono disponibili per l'analisi con Monitoraggio di Azure per le macchine virtuali.

- Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).

- Per identificare i colli di bottiglia e l'utilizzo complessivo delle prestazioni della VM, vedere [visualizzare le prestazioni delle VM di Azure](vminsights-performance.md).
