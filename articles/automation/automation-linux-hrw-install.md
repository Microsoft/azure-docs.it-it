---
title: Ruolo di lavoro ibrido per runbook Linux di Automazione di Azure
description: Questo articolo fornisce informazioni sull'installazione di un ruolo di lavoro ibrido per runbook di Automazione di Azure che consente di eseguire i runbook sui computer Linux nel data center locale o nell'ambiente cloud.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 82f6d9e56e5d5745077ef512cb3392c16b95961f
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872176"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Linux

È possibile usare la funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire runbook direttamente nel computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. Il ruolo di lavoro ibrido per runbook di Linux esegue i runbook come un utente speciale a cui possono essere garantiti privilegi elevati per eseguire i comandi che richiedono l'elevazione. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer designati.

Questo articolo descrive come installare il ruolo di lavoro ibrido per Runbook in un computer Linux, come rimuovere il ruolo di lavoro e come rimuovere un gruppo di ruolo di lavoro ibrido per Runbook.

## <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati

La funzionalità ruolo di lavoro ibrido per runbook supporta le distribuzioni seguenti:

* Amazon Linux da 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS e 18,04 (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

## <a name="supported-runbook-types"></a>Tipi di runbook supportati

I ruoli di lavoro ibridi per runbook di Linux non supportano il set completo di tipi di runbook in Automazione di Azure.

I tipi di runbook seguenti possono essere usati in un ruolo di lavoro ibrido di Linux:

* Python 2
* PowerShell

  > [!NOTE]
  > I runbook di PowerShell richiedono che PowerShell Core sia installato nel computer Linux. Vedere [Installazione di PowerShell Core in Linux](/powershell/scripting/install/installing-powershell-core-on-linux) per informazioni su come installarlo.

I tipi di runbook seguenti non possono essere usati in un ruolo di lavoro ibrido di Linux:

* Flusso di lavoro PowerShell
* Grafico
* Grafico del flusso di lavoro di PowerShell

## <a name="deployment-requirements"></a>Requisiti di distribuzione

I requisiti minimi per un ruolo di lavoro ibrido per runbook di Linux sono i seguenti:

* Due core
* 4 GB di RAM
* Porta 443 (in uscita)

### <a name="package-requirements"></a>Requisiti dei pacchetti

| **Pacchetto necessario** | **Descrizione** | **Versione minima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Libreria GNU C| 2.5-12 |
|Openssl| Librerie OpenSSL | 1,0 (sono supportati TLS 1,1 e TLS 1,2)|
|Curl | Client Web cURL | 7.15.5|
|Python-ctypes | Python 2. x è obbligatorio |
|PAM | Moduli di autenticazione modulare|
| **Pacchetto facoltativo** | **Descrizione** | **Versione minima**|
| PowerShell Core | Per eseguire i runbook di PowerShell, PowerShell deve essere installato. Vedere [installazione di PowerShell Core in Linux](/powershell/scripting/install/installing-powershell-core-on-linux) per informazioni su come installarlo.  | 6.0.0 |

## <a name="install-a-linux-hybrid-runbook-worker"></a>Installare un ruolo di lavoro ibrido per Runbook Linux

Per installare e configurare un ruolo di lavoro ibrido per Runbook nel computer Linux, seguire una procedura manuale semplice. È necessario abilitare la soluzione Ruolo di lavoro ibrido per runbook di Automazione nell'area di lavoro Log Analytics di Azure e quindi eseguire un set di comandi per registrare il computer come ruolo di lavoro e aggiungerlo a un gruppo.

Prima di procedere, prendere nota dell'area di lavoro Log Analytics a cui è collegato l'account di Automazione. Prendere anche nota della chiave primaria per l'account di Automazione. È possibile trovare entrambi nel portale di Azure, selezionando l'account di Automazione, **Area di lavoro** per l'ID dell'area di lavoro e selezionando **Chiavi** per la chiave primaria. Per informazioni sulle porte e sugli indirizzi necessari per il ruolo di lavoro ibrido per runbook, vedere [Configurazione della rete](automation-hybrid-runbook-worker.md#network-planning).

>[!NOTE]
> L' [account nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) con le autorizzazioni sudo corrispondenti deve essere presente durante l'installazione del ruolo di lavoro ibrido per Linux. Se si tenta di installare il ruolo di lavoro e l'account non è presente o non dispone delle autorizzazioni appropriate, l'installazione non riesce.

1. Abilitare la soluzione Ruolo di lavoro ibrido per runbook di Automazione in Azure usando uno dei metodi seguenti:

   * Aggiungere la soluzione ruolo di lavoro ibrido per runbook di Automazione alla sottoscrizione usando la procedura descritta in [aggiungere soluzioni di log di monitoraggio di Azure all'area di lavoro](../log-analytics/log-analytics-add-solutions.md).
   * Eseguire il cmdlet seguente:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installare l'agente di Log Analytics per Linux usando il comando seguente. Sostituire \<WorkspaceID\> e \<WorkspaceKey\> con i valori appropriati dall'area di lavoro.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Eseguire il comando seguente modificando i valori per i parametri *-w*, *-k*, *-g* e *-e*. Per il parametro *-g* sostituire il valore con il nome del gruppo del ruolo di lavoro ibrido per runbook a cui deve essere aggiunto il nuovo ruolo di lavoro ibrido per runbook di Linux. Se il nome non esiste già nell'account di Automazione, viene eseguito un nuovo ruolo di lavoro ibrido per runbook con lo stesso nome.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Dopo il completamento del comando, nella pagina Gruppi di ruoli di lavoro ibridi nel portale di Azure vengono visualizzati il nuovo gruppo e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo nell'elenco della pagina Gruppi di ruoli di lavoro ibridi e selezionare il riquadro **Ruoli di lavoro per runbook**. Nella pagina Ruoli di lavoro per runbook sono elencati i membri del gruppo.

> [!NOTE]
> Se si usa l'estensione della macchina virtuale di monitoraggio di Azure per Linux per una macchina virtuale di `autoUpgradeMinorVersion` Azure, è consigliabile impostare su false perché le versioni con aggiornamento automatico possono causare problemi al ruolo di lavoro ibrido per Runbook. Per informazioni su come aggiornare manualmente l'estensione, vedere [distribuzione dell'interfaccia](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)della riga di comando di Azure.

## <a name="turn-off-signature-validation"></a>Disattivare la convalida della firma

Per impostazione predefinita, i ruoli di lavoro ibridi per runbook di Linux richiedono la convalida della firma. Se si esegue un Runbook non firmato in un ruolo di lavoro, viene visualizzato `Signature validation failed` un errore. Per disattivare la convalida della firma, eseguire il comando seguente. Sostituire il secondo parametro con l'ID dell'area di lavoro Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-computer"></a><a name="remove-linux-hybrid-runbook-worker"></a>Rimuovere il ruolo di lavoro ibrido per Runbook da un computer Linux locale

È possibile usare il comando `ls /var/opt/microsoft/omsagent` sul ruolo di lavoro ibrido per Runbook per ottenere l'ID dell'area di lavoro. Viene creata una cartella denominata con l'ID dell'area di lavoro.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Questo codice non rimuove l'agente di Log Analytics per Linux dal computer. Rimuove solo la funzionalità e la configurazione del ruolo di lavoro ibrido per Runbook.

## <a name="remove-a-hybrid-worker-group"></a>Rimuovere un gruppo di ruoli di lavoro ibridi

Per rimuovere un gruppo ruolo di lavoro ibrido per Runbook di computer Linux, è possibile usare gli stessi passaggi di un gruppo di lavoro ibrido di Windows. Vedere [rimuovere un gruppo di lavoro ibrido](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).
* Per informazioni su come risolvere i problemi del ruolo di lavoro ibrido per runbook, vedere [Risoluzione dei problemi dei ruoli di lavoro ibridi per runbook](troubleshoot/hybrid-runbook-worker.md#linux)
