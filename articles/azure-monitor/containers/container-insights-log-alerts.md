---
title: Registrare gli avvisi da monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive come creare avvisi di log personalizzati per l'utilizzo della memoria e della CPU da monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: 4239567c60afda6ca165e097562cb888c731f15a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614307"
---
# <a name="how-to-create-log-alerts-from-azure-monitor-for-containers"></a>Come creare avvisi dei log da Monitoraggio di Azure per i contenitori

Monitoraggio di Azure per i contenitori monitora le prestazioni dei carichi di lavoro del contenitore distribuiti nei cluster Kubernetes gestiti o autogestiti. Per generare un avviso sulla questione, in questo articolo viene descritto come creare avvisi basati su log per le situazioni seguenti con i cluster AKS:

- Quando l'utilizzo della CPU o della memoria nei nodi del cluster supera una soglia
- Quando l'utilizzo della CPU o della memoria in qualsiasi contenitore all'interno di un controller supera una soglia rispetto a un limite impostato sulla risorsa corrispondente
- Conteggi del nodo dello stato *Nobattistrada*
- Conteggi di *fasi Pod* *non riusciti*, *in sospeso*, *sconosciuti*, *in esecuzione* o completati
- Quando lo spazio libero su disco nei nodi del cluster supera una soglia

Per segnalare un utilizzo elevato della CPU o della memoria o di spazio su disco insufficiente nei nodi del cluster, utilizzare le query fornite per creare un avviso per la metrica o un avviso di misurazione della metrica. Mentre gli avvisi delle metriche hanno una latenza inferiore rispetto agli avvisi del log, gli avvisi del log forniscono query avanzate e una maggiore complessità. Le query di avviso del log confrontano un valore DateTime con quello attuale usando l'operatore *Now* e tornando indietro di un'ora. Il monitoraggio di Azure per i contenitori archivia tutte le date nel formato UTC (Coordinated Universal Time).

Se non si ha familiarità con gli avvisi di monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure prima di](../platform/alerts-overview.md) iniziare. Per altre informazioni sugli avvisi che usano le query di log, vedere [avvisi del log in monitoraggio di Azure](../alerts/alerts-unified-log.md). Per altre informazioni sugli avvisi delle metriche, vedere la pagina relativa agli [avvisi delle metriche in monitoraggio di Azure](../alerts/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Query di ricerca log di utilizzo risorse

Le query in questa sezione supportano ogni scenario di avviso. Sono usati nel passaggio 7 della sezione [Create alert](#create-an-alert-rule) di questo articolo.

La query seguente calcola l'utilizzo medio della CPU come media di utilizzo della CPU dei nodi membri ogni minuto.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

La query seguente calcola l'utilizzo medio della memoria come media di utilizzo della memoria dei nodi membri ogni minuto.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Nelle query seguenti vengono usati i valori segnaposto \<your-cluster-name> e \<your-controller-name> per rappresentare il cluster e il controller. Sostituirli con valori specifici dell'ambiente quando si configurano gli avvisi.

La query seguente calcola l'utilizzo medio della CPU di tutti i contenitori in un controller come media di utilizzo della CPU di ogni istanza del contenitore in un controller ogni minuto. La misurazione è una percentuale del limite configurato per un contenitore.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

La query seguente calcola l'utilizzo medio della memoria di tutti i contenitori in un controller come media di utilizzo della memoria di ogni istanza del contenitore in un controller ogni minuto. La misurazione è una percentuale del limite configurato per un contenitore.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

La query seguente restituisce tutti i nodi e i conteggi con lo stato *Ready* e *nobattistraday*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
La query seguente restituisce i conteggi delle fasi pod in base a tutte le fasi: *failed*, *Pending*, *Unknown*, *Running* o *succeeded*.  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Per eseguire un avviso su determinate fasi di Pod, ad esempio *in sospeso*, *non riuscite* o *sconosciute*, modificare l'ultima riga della query. Ad esempio, per segnalare l'uso di *FailedCount* : <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

La query seguente restituisce i dischi dei nodi del cluster che superano il 90% di spazio libero utilizzato. Per ottenere l'ID del cluster, eseguire prima la query seguente e copiare il valore dalla `ClusterId` proprietà:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Creare una regola di avviso

Questa sezione illustra la creazione di una regola di avviso di misurazione delle metriche usando i dati sulle prestazioni di monitoraggio di Azure per i contenitori. È possibile usare questo processo di base con una serie di query di log per inviare avvisi su contatori di prestazioni diversi. Usare una delle query di ricerca nei log fornite in precedenza per iniziare con. Per creare usando un modello ARM, vedere [esempi di creazione di un avviso di log con il modello di risorsa di Azure](../alerts/alerts-log-create-templates.md).

>[!NOTE]
>La procedura seguente per creare una regola di avviso per l'utilizzo delle risorse del contenitore richiede di passare a una nuova API per gli avvisi del log, come descritto in [Switch API preferenza for log alerts](../alerts/alerts-log-api-switch.md).
>

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure cercare e selezionare **Aree di lavoro di Log Analytics**.
3. Nell'elenco delle aree di lavoro Log Analytics selezionare l'area di lavoro che supporta monitoraggio di Azure per i contenitori. 
4. Nel riquadro sul lato sinistro selezionare **log** per aprire la pagina log di monitoraggio di Azure. Usare questa pagina per scrivere ed eseguire query di log di Azure.
5. Nella pagina **logs** incollare una delle [query](#resource-utilization-log-search-queries) fornite in precedenza nel campo della **query di ricerca** e quindi selezionare **Run (Esegui** ) per convalidare i risultati. Se non si esegue questo passaggio, l'opzione **+ nuovo avviso** non è disponibile per la selezione.
6. Selezionare **+ nuovo avviso** per creare un avviso del log.
7. Nella sezione **condizione** selezionare il **ogni volta che la ricerca log personalizzata è \<logic undefined>** una condizione di log personalizzata predefinita. Il tipo di segnale di **Ricerca log personalizzato** viene selezionato automaticamente perché si sta creando una regola di avviso direttamente dalla pagina log di monitoraggio di Azure.  
8. Incollare una delle [query](#resource-utilization-log-search-queries) fornite in precedenza nel campo della **query di ricerca** .
9. Configurare l'avviso come segue:

    1. Dall'elenco **a discesa basato su** selezionare **misurazione metrica**. Una misura metrica crea un avviso per ogni oggetto nella query con un valore superiore alla soglia specificata.
    1. Per **condizione** selezionare **maggiore di** e immettere **75** come **soglia** iniziale di base per gli avvisi di utilizzo della CPU e della memoria. Per l'avviso di spazio su disco insufficiente immettere **90**. In alternativa, immettere un valore diverso che soddisfi i criteri.
    1. Nella sezione **trigger Alert based on** selezionare **violazioni consecutive**. Nell'elenco a discesa selezionare **maggiore di** e immettere **2**.
    1. Per configurare un avviso per l'utilizzo della CPU o della memoria del contenitore, in **Aggregate on** selezionare **containerName**. Per configurare l'avviso per nodo cluster su disco basso, selezionare **ClusterId**.
    1. Nella sezione **valutato in base** a impostare il valore del **periodo** su **60 minuti**. La regola viene eseguita ogni 5 minuti e restituisce i record creati nell'ultima ora dall'ora corrente. Impostazione del periodo di tempo su un'ampia finestra account per la potenziale latenza dei dati. Garantisce inoltre che la query restituisca dati per evitare un falso negativo in cui l'avviso non viene mai attivato.

10. Selezionare **fine** per completare la regola di avviso.
11. Immettere un nome nel campo **Nome regola di avviso** . Specificare una **Descrizione** che fornisca dettagli sull'avviso. E selezionare un livello di gravità appropriato dalle opzioni fornite.
12. Per attivare immediatamente la regola di avviso, accettare il valore predefinito per **Abilita regola al momento della creazione**.
13. Selezionare un **gruppo di azioni** esistente o creare un nuovo gruppo. Questo passaggio garantisce che vengano eseguite le stesse azioni ogni volta che viene attivato un avviso. Configurare in base alle modalità di gestione degli eventi imprevisti da parte del team operativo IT o DevOps.
14. Selezionare **Crea regola di avviso** per completare la regola di avviso. L'esecuzione inizia immediatamente.

## <a name="next-steps"></a>Passaggi successivi

- Visualizzare gli [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query e esempi predefiniti per valutare o personalizzare gli avvisi, la visualizzazione o l'analisi dei cluster.

- Per altre informazioni su monitoraggio di Azure e su come monitorare altri aspetti del cluster Kubernetes, vedere [visualizzare le prestazioni del cluster Kubernetes](container-insights-analyze.md) e [visualizzare l'integrità del cluster Kubernetes](./container-insights-overview.md).
