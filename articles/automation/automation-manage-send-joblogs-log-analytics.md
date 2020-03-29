---
title: Inoltrare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure
description: Questo articolo illustra come inviare flussi di lavoro e lo stato dei processi dei processi dei processi ai log di Monitoraggio di Azure per fornire informazioni dettagliate e gestione aggiuntive.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: beb69edc57b5a13db0f6d2e5e1536804f3472aff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421907"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Inoltrare lo stato dei processi e i flussi di lavoro dall'automazione ai log di Monitoraggio di AzureForward job status and job streams from Automation to Azure Monitor logs

Automazione può inviare lo stato e i flussi del processo del runbook all'area di lavoro Log Analytics. Questo processo non comporta il collegamento dell'area di lavoro ed è completamente indipendente. I log e i flussi di processo sono visibili nel portale di Azure o con PowerShell per i singoli processi e ciò consente di eseguire analisi semplici. Ora con i log di Monitoraggio di Azure è possibile:Now with Azure Monitor logs you can:

* Ottenere informazioni dettagliate sui processi di Automazione.
* Attivare un messaggio di posta elettronica o un avviso in base allo stato del processo del runbook, ad esempio non riuscito o sospeso.
* Scrivere query avanzate nei flussi del processo.
* Correlare i processi tra account di Automazione.
* Visualizzare la cronologia dei processi nel tempo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Prerequisiti e considerazioni sulla distribuzione

Per iniziare a inviare i log di Automazione ai log di Monitoraggio di Azure, è necessario:To start sending your Automation logs to Azure Monitor logs, you need:

* L'ultima versione di [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Un'area di lavoro Log Analytics. Per altre informazioni, vedere [Introduzione ai log](../log-analytics/log-analytics-get-started.md)di Monitoraggio di Azure.For more information, see Get started with Azure Monitor logs .
* Il valore ResourceId dell'account di Automazione di Azure.

Per trovare il valore ResourceId dell'account di Automazione di Azure:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Per trovare il valore ResourceId dell'area di lavoro Log Analytics, eseguire questo comando PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se si ha più di un account di automazione o area di lavoro, nell'output dei comandi precedenti trovare il *nome* necessario per configurare e copiare il valore di *ResourceId*.

Per trovare il *nome* dell'account di Automazione, nel portale di Azure selezionare l'account di Automazione dal pannello **Account di Automazione** e selezionare **Tutte le impostazioni**. Nel pannello **Tutte le impostazioni** selezionare **Proprietà** in **Impostazioni account**.  Nel pannello **Proprietà** è possibile prendere nota di questi valori.<br> ![Proprietà dell'account di Automazione](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurare l'integrazione con i log di Monitoraggio di AzureSet up integration with Azure Monitor logs

1. Nel computer locale avviare **Windows PowerShell** dalla schermata **Start**.
2. Eseguire questo comando di PowerShell e sostituire il valore di `[your resource id]` e `[resource id of the log analytics workspace]` con i valori del passaggio precedente.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Dopo aver eseguito questo script, potrebbe essere necessario un'ora prima di iniziare a visualizzare i record nei log di Monitoraggio di Azure dei nuovi JobLogs o JobStreams in fase di scrittura.

Per visualizzare i log, eseguire la query seguente nella ricerca del log di analisi dei log:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificare la configurazione

Per verificare che l'account di Automazione invii i log all'area di lavoro Log Analytics, accertarsi che la diagnostica sia configurata correttamente nell'account di Automazione usando il comando di PowerShell seguente:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Nell'output verificare quanto segue:

* In *Registri*, il valore di *Enabled* è *True*.
* Il valore di *WorkspaceId* è impostato sul ResourceId dell'area di lavoro di Log Analytics.

## <a name="azure-monitor-log-records"></a>Record di log di Monitoraggio di Azure

La diagnostica dall'automazione di Azure crea due tipi di record nei log di Monitoraggio di Azure e viene contrassegnata come **AzureDiagnostics.** Le query seguenti usano il linguaggio di query aggiornato per i log di Monitoraggio di Azure.The following queries use the upgraded query language to Azure Monitor logs. Per informazioni sulle query comuni tra il linguaggio di query legacy e il nuovo linguaggio di query di Azure Kusto, visitare [legacy to new Azure Kusto Query Language cheat sheet](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Log del processo

| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora di esecuzione del processo del runbook. |
| RunbookName_s |Il nome del runbook. |
| Caller_s |Chi ha avviato l'operazione. I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati. |
| Tenant_g | GUID che identifica il tenant del chiamante. |
| JobId_g |Il GUID che rappresenta l'ID del processo del runbook. |
| ResultType |Lo stato del processo di runbook. I valori possibili sono:<br>- Nuovo<br>- Creato<br>- Avviato<br>- Interrotto<br>- Sospeso<br>- Non riuscito<br>- Completato |
| Category | La classificazione del tipo di dati. Per Automazione, il valore è JobLogs. |
| OperationName | Specifica il tipo di operazione eseguita in Azure. Per Automazione, il valore è Job. |
| Risorsa | Nome dell'account di Automazione |
| SourceSystem | In che modo i log di Monitoraggio di Azure hanno raccolto i dati. È sempre *Azure* per la diagnostica di Azure. |
| ResultDescription |Descrive lo stato del risultato del processo di runbook. I valori possibili sono:<br>- Processo avviato<br>- Processo non riuscito<br>- Processo completato |
| CorrelationId |Il GUID che rappresenta l'ID di correlazione del processo di runbook. |
| ResourceId |Specifica l'ID risorsa dell'account di Automazione di Azure del runbook. |
| SubscriptionId | ID della sottoscrizione di Azure (GUID) per l'account di Automazione. |
| ResourceGroup | Nome del gruppo di risorse dell'account di Automazione. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Flussi del processo
| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora di esecuzione del processo del runbook. |
| RunbookName_s |Il nome del runbook. |
| Caller_s |Chi ha avviato l'operazione. I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati. |
| StreamType_s |Il tipo di flusso del processo. I valori possibili sono:<br>- Avanzamento<br>- Output<br>- Avviso<br>- Errore<br>- Debug<br>- Dettagliato |
| Tenant_g | GUID che identifica il tenant del chiamante. |
| JobId_g |Il GUID che rappresenta l'ID del processo del runbook. |
| ResultType |Lo stato del processo di runbook. I valori possibili sono:<br>- In corso |
| Category | La classificazione del tipo di dati. Per Automazione, il valore è JobStreams. |
| OperationName | Specifica il tipo di operazione eseguita in Azure. Per Automazione, il valore è Job. |
| Risorsa | Nome dell'account di Automazione |
| SourceSystem | In che modo i log di Monitoraggio di Azure hanno raccolto i dati. È sempre *Azure* per la diagnostica di Azure. |
| ResultDescription |Include il flusso di output dal runbook. |
| CorrelationId |Il GUID che rappresenta l'ID di correlazione del processo di runbook. |
| ResourceId |Specifica l'ID risorsa dell'account di Automazione di Azure del runbook. |
| SubscriptionId | ID della sottoscrizione di Azure (GUID) per l'account di Automazione. |
| ResourceGroup | Nome del gruppo di risorse dell'account di Automazione. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visualizzazione dei log di automazione nei log di Monitoraggio di AzureViewing Automation Logs in Azure Monitor logs

Dopo aver iniziato a inviare i log dei processi di automazione ai log di Monitoraggio di Azure, vedere cosa è possibile fare con questi log all'interno dei log di Monitoraggio di Azure.Now that you started sending your Automation job logs to Azure Monitor logs, let's see what you can do with these logs inside Azure Monitor logs.

Per visualizzare i log eseguire questa query: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Inviare un messaggio di posta elettronica quando un processo del runbook non riesce o viene sospeso
Uno dei clienti più importanti chiede di poter inviare un messaggio di posta elettronica o un SMS quando si verificano problemi con un processo del runbook.

Per creare una regola di avviso, è necessario creare prima di tutto una ricerca nei log per trovare i record del processo del runbook che dovranno richiamare l'avviso. Fare clic su pulsante **Avviso** per creare e configurare la regola di avviso.

1. Nella pagina Panoramica dell'area di lavoro di Log Analytics fare clic su **Visualizza log**.
2. Creare una query di ricerca log per l'avviso digitando quanto segue nel campo della query: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` È anche possibile raggruppare in base al valore RunbookName usando: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se sono stati configurati log da più account di Automazione o sottoscrizioni nell'area di lavoro, è possibile raggruppare gli avvisi per sottoscrizione o account di Automazione. Si può trovare il nome dell'account di automazione nel campo Risorsa nella ricerca di JobLogs.
3. Per aprire la schermata **Crea regola** fare clic su **+ Nuova regola di avviso** nella parte superiore della pagina. Per altre informazioni sulle opzioni per la configurazione dell'avviso, vedere [Avvisi di log in Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Trovare tutti i processi completati con errori
Oltre agli avvisi per gli errori, è possibile determinare quando un processo del runbook presenta un errore non irreversibile. In questi casi PowerShell produce un flusso di errore, ma gli errori non irreversibili non comportano la sospensione o l'esito negativo del processo.

1. Nell'area di lavoro di Log Analytics fare clic su **Registri**.
2. Nel campo delle query digitare `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` e quindi fare clic sul pulsante **Cerca**.

### <a name="view-job-streams-for-a-job"></a>Visualizzare flussi del processo per un processo
Quando si esegue il debug di un processo, è consigliabile esaminarne anche i flussi. La query seguente illustra tutti i flussi per un singolo processo con GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visualizzare lo stato cronologico del processo
Infine, è consigliabile visualizzare la cronologia dei processi nel tempo. È possibile usare questa query per cercare lo stato dei processi nel tempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Grafico dello stato cronologico del processo di Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Rimuovere le impostazioni di diagnostica

Per rimuovere l'impostazione di diagnostica dall'account di Automazione, eseguire i comandi seguenti:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Riepilogo

Inviando lo stato dei processi di automazione e i dati di flusso ai log di Monitoraggio di Azure, è possibile ottenere informazioni più dettagliate sullo stato dei processi di Automazione tramite:By sending your Automation job status and stream data to Azure Monitor logs, you can get better insight into the status of your Automation jobs by:
+ Configurando avvisi che segnalino la presenza di un problema.
+ Usando viste personalizzate e query di ricerca per visualizzare i risultati del runbook, lo stato dei processi del runbook e altri indicatori chiave o metriche correlati.

I log di Monitoraggio di Azure offrono una maggiore visibilità operativa ai processi di automazione e consentono di risolvere più rapidamente gli incidenti.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla risoluzione dei problemi relativi a Log Analytics, vedere [Risoluzione dei problemi relativi alla risoluzione](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)dei dati per cui Log Analytics non raccoglie più dati.
* Per altre informazioni su come creare query di ricerca diverse ed esaminare i log dei processi di automazione con i log di Monitoraggio di Azure, vedere Ricerche nei log nei log di Monitoraggio di Azure.To learn more about how to construct different search queries and review the Automation job logs with Azure Monitor logs, see [Log searches in Azure Monitor logs.](../log-analytics/log-analytics-log-searches.md)
* Per informazioni su come creare e recuperare messaggi di output e di errore dai runbook, vedere Output e messaggi del [Runbook.](automation-runbook-output-and-messages.md)
* Per altre informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere [Verifica di un processo di runbook](automation-runbook-execution.md).
* Per altre informazioni sui log di Monitoraggio di Azure e sulle origini della raccolta dati, vedere Panoramica della raccolta di dati di archiviazione di Azure in Panoramica dei log di Monitoraggio di Azure.To learn more about Azure Monitor logs and data collection sources, see [Collecting Azure storage data in Azure Monitor logs overview.](../azure-monitor/platform/collect-azure-metrics-logs.md)

