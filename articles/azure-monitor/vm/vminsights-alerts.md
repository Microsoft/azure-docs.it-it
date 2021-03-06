---
title: Avvisi da VM Insights
description: Viene descritto come creare regole di avviso da dati sulle prestazioni raccolti da VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 06c58b7081ed68724a3c907f8fe76dcf5f7b8057
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046806"
---
# <a name="how-to-create-alerts-from-vm-insights"></a>Come creare avvisi da VM Insights
Gli [avvisi in monitoraggio di Azure](../alerts/alerts-overview.md) notificano in modo proattivo i dati e i modelli interessanti nei dati di monitoraggio. VM Insights non include regole di avviso preconfigurate, ma è possibile crearne di personalizzate in base ai dati raccolti. Questo articolo fornisce indicazioni sulla creazione di regole di avviso, incluso un set di query di esempio.

> [!IMPORTANT]
> Gli avvisi descritti in questo articolo si basano sulle query di log dei dati raccolti da VM Insights. Questa operazione è diversa rispetto agli avvisi creati da [monitoraggio di Azure per l'integrità Guest della VM](vminsights-health-overview.md) , una funzionalità attualmente disponibile in anteprima pubblica. Poiché questa funzionalità è vicina alla disponibilità generale, verranno consolidate le linee guida per gli avvisi.


## <a name="alert-rule-types"></a>Tipi di regole di avviso
Monitoraggio di Azure dispone [di diversi tipi di regole di avviso](../alerts/alerts-overview.md#what-you-can-alert-on) in base ai dati usati per creare l'avviso. Tutti i dati raccolti da VM Insights vengono archiviati nei log di monitoraggio di Azure che supportano gli [avvisi del log](../alerts/alerts-log.md). Attualmente non è possibile usare gli [avvisi delle metriche](../alerts/alerts-log.md) con i dati sulle prestazioni raccolti da VM Insights, perché i dati non vengono raccolti nelle metriche di monitoraggio di Azure. Per raccogliere dati per gli avvisi delle metriche, installare l' [estensione di diagnostica](../agents/diagnostics-extension-overview.md) per le macchine virtuali Windows o l' [agente Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) per le macchine virtuali Linux per raccogliere dati sulle prestazioni in metriche.

Sono disponibili due tipi di avvisi del log in monitoraggio di Azure:

- Il [numero di avvisi di risultati](../alerts/alerts-unified-log.md#count-of-the-results-table-rows) crea un singolo avviso quando una query restituisce almeno un numero specificato di record. Questi sono ideali per i dati non numerici, ad esempio gli eventi Windows e syslog raccolti dall' [agente log Analytics](../agents/log-analytics-agent.md) o per l'analisi delle tendenze delle prestazioni in più computer.
- Gli [avvisi di misurazione delle metriche](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) creano un avviso separato per ogni record in una query con un valore che supera una soglia definita nella regola di avviso. Queste regole di avviso sono ideali per i dati sulle prestazioni raccolti da VM Insights, in quanto consentono di creare singoli avvisi per ogni computer.


## <a name="alert-rule-walkthrough"></a>Procedura dettagliata della regola di avviso
Questa sezione illustra la creazione di una regola di avviso di misurazione delle metriche usando i dati sulle prestazioni di VM Insights. È possibile usare questo processo di base con una serie di query di log per inviare avvisi su contatori di prestazioni diversi.

Per iniziare, creare una nuova regola di avviso seguendo la procedura descritta in [creare, visualizzare e gestire gli avvisi del log mediante monitoraggio di Azure](../alerts/alerts-log.md). Per la **risorsa** selezionare l'area di lavoro log Analytics utilizzata dalle macchine virtuali di monitoraggio di Azure nella sottoscrizione. Poiché la risorsa di destinazione per le regole di avviso del log è sempre un'area di lavoro Log Analytics, la query di log deve includere qualsiasi filtro per determinate macchine virtuali o set di scalabilità di macchine virtuali. 

Per la **condizione** della regola di avviso, usare una delle query nella [sezione seguente](#sample-alert-queries) come **query di ricerca**. La query deve restituire una proprietà numerica denominata *AggregatedValue*. Dovrebbe riepilogare i dati in base al computer, in modo da poter creare un avviso separato per ogni macchina virtuale che supera la soglia.

Nella **logica di avviso** selezionare **misurazione metrica** e quindi specificare un **valore soglia**. In **attiva l'avviso in base a** specificare il numero di volte in cui deve essere superata la soglia prima che venga creato un avviso. È possibile, ad esempio, non preoccuparsi se il processore supera una soglia una sola volta e quindi torna alla normalità, ma se continua a superare la soglia rispetto a più misurazioni consecutive.

La sezione **valutata in base a** definisce la frequenza con cui viene eseguita la query e l'intervallo di tempo per la query. Nell'esempio riportato di seguito, la query viene eseguita ogni 15 minuti e valuta i valori delle prestazioni raccolti nei 15 minuti precedenti.


![Regola di avviso di misurazione delle metriche](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Query di avviso di esempio
È possibile usare le query seguenti con una regola di avviso di misurazione della metrica usando i dati sulle prestazioni raccolti da VM Insights. Ognuno riepiloga i dati in base al computer, in modo che venga creato un avviso per ogni computer con un valore che supera la soglia.

### <a name="cpu-utilization"></a>Uso della CPU

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Memoria disponibile in MB

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Memoria disponibile in percentuale

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Disco logico usato: tutti i dischi in ogni computer

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Disco logico usato-dischi singoli

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>IOPS disco logico

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Velocità dati disco logico

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Interfacce di rete byte ricevuti-tutte le interfacce

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Interfacce di rete byte ricevuti-interfacce singole

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Interfacce di rete byte inviati-tutte le interfacce

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Interfacce di rete byte inviati-interfacce singole

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Set di scalabilità di macchine virtuali
Modificare con l'ID sottoscrizione, il gruppo di risorse e il nome del set di scalabilità di macchine virtuali.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Macchina virtuale specifica
Modificare con l'ID sottoscrizione, il gruppo di risorse e il nome della macchina virtuale.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>Utilizzo della CPU per tutte le risorse di calcolo in una sottoscrizione
Modificare con l'ID sottoscrizione.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>Utilizzo della CPU per tutte le risorse di calcolo in un gruppo di risorse
Modificare con l'ID sottoscrizione e il gruppo di risorse.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli [avvisi in monitoraggio di Azure](../alerts/alerts-overview.md).
- Altre informazioni sulle [query di log con i dati di VM Insights](vminsights-log-search.md).
