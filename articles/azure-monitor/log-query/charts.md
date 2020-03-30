---
title: Creazione di grafici e diagrammi da query di log di Monitoraggio di Azure | Microsoft Docs
description: Descrive le diverse modalità di visualizzazione dei dati dei log in Monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670322"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Creazione di grafici e diagrammi da query di Monitoraggio di Azure

> [!NOTE]
> Prima di seguire questa lezione, è consigliabile completare [Aggregazioni avanzate nelle query di Monitoraggio di Azure](advanced-aggregations.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Questo articolo descrive le diverse modalità di visualizzazione dei dati dei log in Monitoraggio di Azure.

## <a name="charting-the-results"></a>Disegnare i risultati
Iniziare esaminando il numero di computer disponibili nell'ultima ora per ogni sistema operativo:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Per impostazione predefinita, i risultati vengono visualizzati sotto forma di tabella:

![Tabella](media/charts/table-display.png)

Per una visualizzazione più efficace dei risultati, selezionare **Grafico** e scegliere l'opzione **Torta**:

![Grafico a torta](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Grafici di tempo
Visualizzare la media di tempo processore al 50° e al 95° percentile in bin di 1 ora. La query genera più serie ed è quindi possibile selezionare le serie da visualizzare nel grafico temporale:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Selezionare l'opzione Visualizzazione grafico **a** linee:

![Grafico a linee](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Linea di riferimento

Una linea di riferimento consente di identificare facilmente se la metrica supera una soglia specifica. Per aggiungere una riga a un grafico, estendere il set di dati con una colonna costante:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Linea di riferimento](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Uso di più dimensioni
La presenza di più espressioni nella clausola `by` di `summarize` determina la creazione di più righe nei risultati, una per ogni combinazione di valori.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Quando si visualizzano i risultati sotto forma di grafico, viene usata la prima colonna della clausola `by`. L'esempio seguente illustra un istogramma in pila che usa _EventID._ Le dimensioni devono essere di tipo `string`, quindi in questo esempio per _EventID_ viene eseguito il cast a string. 

![Grafico a barre per EventID](media/charts/charts-and-diagrams-multiDimension1.png)

È possibile passare a un'altra colonna selezionandola nell'elenco a discesa con i nomi di colonna. 

![Grafico a barre per AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere altre lezioni per l'uso del [linguaggio di query Kusto](/azure/kusto/query/) con i dati di log di Monitoraggio di Azure:

- [Operazioni con stringhe](string-operations.md)
- [Operazioni con data e ora](datetime-operations.md)
- [Funzioni di aggregazione](aggregations.md)
- [Aggregazioni avanzate](advanced-aggregations.md)
- [Strutture dei dati e JSON](json-data-structures.md)
- [Scrittura di query avanzate](advanced-query-writing.md)
- [Join](joins.md)
