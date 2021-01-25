---
title: Risolvere i problemi relativi all'agente di Linux in Automazione di Azure
description: Questo articolo descrive come risolvere i problemi relativi all'agente di Windows Linux in Gestione aggiornamenti.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/25/2021
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: a7ac5e8324d9979b17ee93d16b3e007fe7916a8a
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762630"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Risolvere i problemi relativi all'agente di aggiornamento Linux

I motivi per cui il computer non risulta pronto (integro) in Gestione aggiornamenti possono essere diversi. In Gestione aggiornamenti è possibile controllare l'integrità dell'agente di un ruolo di lavoro ibrido per runbook di Linux per determinare il problema sottostante. I seguenti sono i tre stati di idoneità per un computer:

* Preparazione - il ruolo di lavoro ibrido per runbook è stato distribuito ed è stato osservato meno di un'ora fa.
* Disconnesso: il ruolo di lavoro ibrido per runbook è stato distribuito ed è stato osservato più di un'ora fa.
* Non configurato: Il ruolo di lavoro ibrido per runbook non è stato trovato o non ha completato la distribuzione.

> [!NOTE]
> È possibile che si verifichi un lieve ritardo tra quanto visualizzato dal portale di Azure e lo stato corrente di un computer.

Questo articolo illustra come eseguire la risoluzione dei problemi per i computer Azure dal portale di Azure e per i computer non Azure nello [scenario offline](#troubleshoot-offline).

> [!NOTE]
> Lo script dello strumento di risoluzione dei problemi attualmente non instrada il traffico tramite un server proxy se ne è configurato uno.

## <a name="start-the-troubleshooter"></a>Avviare la risoluzione dei problemi

Nel caso di computer Azure, selezionando il collegamento **Risoluzione dei problemi** nella colonna **Idoneità agente di aggiornamento** nel portale, si apre la pagina Risoluzione dei problemi dell'agente di aggiornamento. Nel caso di computer non Azure, facendo clic sul collegamento, si accede a questo articolo. Per risolvere i problemi relativi a un computer non Azure, vedere le istruzioni nella sezione "Risoluzione dei problemi offline".

![Pagina con l'elenco delle macchine virtuali](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> I controlli richiedono che la macchina virtuale sia in esecuzione. Se la macchina virtuale non è in esecuzione, viene visualizzato **Avvia macchina virtuale**.

Nella pagina Risoluzione dei problemi dell'agente di aggiornamento fare clic su **Esegui controlli** per avviare la risoluzione dei problemi. La risoluzione dei problemi usa [Esegui comando](../../virtual-machines/linux/run-command.md) per eseguire uno script nel computer al fine di verificare le dipendenze. Una volta completata la risoluzione dei problemi, restituisce il risultato dei controlli.

![Pagina della risoluzione dei problemi](../media/update-agent-issues-linux/troubleshoot-page.png)

Al termine dei controlli, i risultati vengono restituiti nella finestra. Le sezioni di controllo forniscono informazioni su ciò che ogni controllo cerca.

![Pagina dei controlli dell'agente di aggiornamento](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controlli dei prerequisiti

### <a name="operating-system"></a>Sistema operativo

Il controllo del sistema operativo verifica se il ruolo di lavoro ibrido per runbook sta eseguendo uno dei sistemi operativi seguenti.

|Sistema operativo  |Note  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti. L'applicazione di patch basata sulla classificazione richiede "yum" per restituire i dati sulla sicurezza che non sono predefiniti in CentOS.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.         |

## <a name="monitoring-agent-service-health-checks"></a>Controlli di integrità dei servizi dell'agente di monitoraggio

### <a name="log-analytics-agent"></a>Agente di Log Analytics

Questo controllo verifica che l'agente di Log Analytics per Linux sia installato. Per istruzioni su come installarlo, vedere [Installare l'agente per Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Stato agente Log Analytics

Questo controllo verifica che l'agente di Log Analytics per Linux sia in esecuzione. Se l'agente non è in esecuzione, è possibile eseguire il comando seguente per tentare di riavviarlo. Per altre informazioni sulla risoluzione dei problemi dell'agente, vedere [Linux - Risolvere i problemi di un ruolo di lavoro ibrido per runbook](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Questo controllo determina se l'agente invia report a più aree di lavoro. Gestione aggiornamenti non supporta il multihoming.

### <a name="hybrid-runbook-worker"></a>ruolo di lavoro ibrido per runbook

Questo controllo verifica che l'agente di Log Analytics per Linux abbia il pacchetto del ruolo di lavoro ibrido per runbook. Questo pacchetto è necessario per il funzionamento di Gestione aggiornamenti. Per altre informazioni, vedere [Agente di Log Analytics per Linux non è in esecuzione](hybrid-runbook-worker.md#oms-agent-not-running).

Gestione aggiornamenti scarica i pacchetti del ruolo di lavoro ibrido per runbook dall'endpoint delle operazioni. Pertanto, se il ruolo di lavoro ibrido per runbook non è in esecuzione e il [controllo dell'endpoint delle operazioni](#operations-endpoint) ha esito negativo, l'aggiornamento potrebbe non riuscire.

### <a name="hybrid-runbook-worker-status"></a>Stato del ruolo di lavoro ibrido per runbook

Questo controllo garantisce che il ruolo di lavoro ibrido per runbook sia in esecuzione nel computer. I processi nell'esempio seguente devono essere presenti se il ruolo di lavoro ibrido per runbook è correttamente in esecuzione.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Controlli della connettività

### <a name="general-internet-connectivity"></a>Connettività Internet generale

Questo controllo verifica che il computer abbia accesso a Internet.

### <a name="registration-endpoint"></a>Endpoint di registrazione

Questo controllo determina se il ruolo di lavoro ibrido per runbook può comunicare correttamente con Automazione di Azure nell'area di lavoro Log Analytics.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con l'endpoint di registrazione. Per un elenco degli indirizzi e delle porte da aprire, vedere [Pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Endpoint delle operazioni

Questo controllo determina se l'agente di Log Analytics può comunicare correttamente con il servizio dati del processo di runtime.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con il servizio dati del processo di runtime. Per un elenco degli indirizzi e delle porte da aprire, vedere [Pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Endpoint di Log Analytics 1

Questo controllo verifica che il computer abbia accesso agli endpoint necessari per l'agente di Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Endpoint di Log Analytics 2

Questo controllo verifica che il computer abbia accesso agli endpoint necessari per l'agente di Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Endpoint di Log Analytics 3

Questo controllo verifica che il computer abbia accesso agli endpoint necessari per l'agente di Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Risolvere i problemi offline

È possibile usare la risoluzione dei problemi offline per i ruoli di lavoro ibridi per runbook eseguendo lo script in locale. Lo script Python, [UM_Linux_Troubleshooter_Offline. py](https://github.com/Azure/updatemanagement/blob/main/UM_Linux_Troubleshooter_Offline.py), è reperibile in GitHub. Di seguito è riportato un esempio dell'output di questo script:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Passaggi successivi

[Risolvere i problemi relativi al ruolo di lavoro ibrido per runbook](hybrid-runbook-worker.md).
