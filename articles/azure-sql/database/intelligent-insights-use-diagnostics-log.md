---
title: Log di diagnostica delle prestazioni Intelligent Insights
description: Intelligent Insights fornisce un log di diagnostica dei problemi di prestazioni del database SQL di Azure e di Azure SQL Istanza gestita
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/12/2020
ms.openlocfilehash: 398a96dc505309e565b13cb42f610d8571b9413e
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986328"
---
# <a name="use-the-intelligent-insights-performance-diagnostics-log-of-azure-sql-database-and-azure-sql-managed-instance-performance-issues"></a>Usare il log di diagnostica delle prestazioni Intelligent Insights dei problemi di prestazioni del database SQL di Azure e di Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Questa pagina contiene informazioni su come usare il log di diagnostica delle prestazioni generato da [Intelligent Insights](intelligent-insights-overview.md) del database SQL di Azure e di azure SQL istanza gestita problemi di prestazioni, il formato e i dati in esso contenuti per le esigenze di sviluppo personalizzate. È possibile inviare questo log di diagnostica a [log di monitoraggio di Azure](../../azure-monitor/insights/azure-sql.md), [Hub eventi di Azure](../../azure-monitor/platform/resource-logs-stream-event-hubs.md), archiviazione di [Azure](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)o una soluzione di terze parti per le funzionalità di avviso e creazione di report di DevOps personalizzate.

> [!NOTE]
> Intelligent Insights è una funzionalità di anteprima, non disponibile nelle aree geografiche seguenti: Europa occidentale, Europa settentrionale, Stati Uniti occidentali 1 e Stati Uniti orientali 1.

## <a name="log-header"></a>Intestazione del log

Il log di diagnostica usa il formato JSON standard per restituire i risultati di Intelligent Insights. L'esatta proprietà relativa alla categoria per accedere al log di Intelligent Insights è un valore fisso "SQLInsights".

L'intestazione del log è comune ed è costituita dal timestamp (TimeGenerated) che indica quando è stata creata una voce, Include anche un ID di risorsa (ResourceId) che fa riferimento al database specifico a cui è correlata la voce. Categoria (Category), livello (Level) e nome operazione (OperationName) sono proprietà fisse il cui valore non cambia. Indicano che la voce nel log è informativa e che deriva da Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ID del problema e database interessato

La proprietà relativa all'identificazione del problema (issueId_d) rappresenta un modo per tenere traccia in modo univoco dei problemi di prestazioni fino a quando non vengono risolti. Più record di eventi nel log che segnalano lo stato dello stesso problema condividono lo stesso ID del problema.

Insieme all'ID del problema, il log di diagnostica segnala i timestamp di inizio (intervalStartTime_t) e di fine (intervalEndTme_t) dell'evento specifico correlato a un problema segnalato nel log di diagnostica.

La proprietà relativa al pool elastico (elasticPoolName_s) indica a quale pool elastico appartiene il database con il problema. Se il database non fa parte di un pool elastico, questa proprietà non include alcun valore. Il database in cui è stato rilevato un problema viene specificato nella proprietà relativa al nome del database (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Problemi rilevati

La sezione successiva del log delle prestazioni di Intelligent Insights contiene i problemi di prestazioni rilevati tramite l'intelligenza artificiale integrata. I rilevamenti vengono forniti nelle proprietà all'interno del log di diagnostica JSON. Questi rilevamenti consistono nella categoria di un problema, l'impatto dei problemi, le query interessate e le metriche. Le proprietà relative ai rilevamenti possono contenere più problemi di prestazioni rilevati.

I problemi di prestazioni rilevati vengono segnalati con la struttura della proprietà relativa ai rilevamenti seguente:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

Nella tabella seguente vengono descritti i dettagli e i modelli di prestazioni rilevabili restituiti nel log di diagnostica.

### <a name="detection-category"></a>Categoria di rilevamento

La proprietà relativa alla categoria (Category) descrive la categoria dei modelli di prestazioni rilevabili. Vedere la tabella seguente per tutte le possibili categorie di modelli di prestazioni rilevabili. Per altre informazioni, vedere [Risolvere i problemi di prestazioni del database SQL di Azure con Intelligent Insights](intelligent-insights-troubleshoot-performance.md).

I dettagli restituiti nel file del log di diagnostica variano a seconda del problema di prestazioni rilevato.

| Modelli di prestazioni rilevabili | Dettagli restituiti |
| :------------------- | ------------------- |
| Raggiungimento dei limiti delle risorse | <li>Risorse interessate</li><li>Hash di query</li><li>Percentuale consumo di risorse</li> |
| Aumento del carico di lavoro | <li>Numero di query la cui esecuzione è aumentata</li><li>Hash di query di query con il maggiore contributo all'aumento del carico di lavoro</li> |
| Utilizzo elevato della memoria | <li>Clerk di memoria</li> |
| Blocco | <li>Hash di query interessati</li><li>Hash di query di blocco</li> |
| Valore di MAXDOP aumentato | <li>Hash di query</li><li>Tempi di attesa CXP</li><li>Tempi di attesa</li> |
| Contesa di latch di pagina | <li>Hash di query di query che causano contesa</li> |
| Indice mancante | <li>Hash di query</li> |
| Nuova query | <li>Hash di query di nuove query</li> |
| Statistiche di attesa non comune | <li>Tipi di attesa non comune</li><li>Hash di query</li><li>Tempi di attesa della query</li> |
| Contesa di TempDB | <li>Hash di query di query che causano contesa</li><li>Attribuzione di query al tempo di attesa complessivo per la contesa di latch di pagina del database [%]</li> |
| Carenza di DTU nel pool elastico | <li>Pool elastico</li><li>Database con uso di DTU maggiore</li><li>Percentuale di DTU del pool usata dalla risorsa che consuma maggiormente</li> |
| Regressione di piani | <li>Hash di query</li><li>ID di piano appropriato</li><li>ID di piano non appropriato</li> |
| Modifica del valore di configurazione in ambito database | <li>Modifiche di configurazione in ambito database rispetto ai valori predefiniti</li> |
| Client lento | <li>Hash di query</li><li>Tempi di attesa</li> |
| Downgrade del piano tariffario | <li>Notifica di testo</li> |

### <a name="impact"></a>Impatto

La proprietà relativa all'impatto (Impact) indica quanto di un comportamento rilevato ha contribuito a generare il problema nel database. Il valore è compreso tra 1 e 3, dove 3 rappresenta il contributo massimo, 2 un contributo moderato e 1 un contributo minimo. Il valore di impatto può essere usato come input per l'automazione degli avvisi personalizzati, a seconda delle esigenze specifiche. La proprietà relativa alle query interessate (QueryHashes) specifica un elenco di hash di query che sono stati interessati dal rilevamento specifico.

### <a name="impacted-queries"></a>Query interessate

La sezione successiva del log di Intelligent Insights include informazioni sulle query specifiche che sono state interessate dai problemi di prestazioni rilevati. Queste informazioni vengono presentate come matrice di oggetti incorporati nella proprietà impact_s. La proprietà relativa all'impatto è costituita da entità e metriche. Le entità si riferiscono a una query specifica (Type: Query). L'hash di query univoco viene presentato come valore della proprietà (Value). Ognuna delle query fornite è inoltre seguita da una metrica e un valore che indica un problema di prestazioni rilevato.

Nell'esempio di log seguente è possibile notare che la query con hash 0x9102EXZ4 ha mostrato un aumento della durata di esecuzione (metrica: DurationIncreaseSeconds). Il valore pari a 110 secondi indica che l'esecuzione di questa particolare query ha richiesto 110 secondi in più. È possibile rilevare più query in quanto questa specifica sezione del log può includere più voci di query.

```json
"impact" : [{
"entity" : {
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Metriche

L'unità di misura per ogni metrica segnalata viene indicata nella proprietà relativa alla metrica (metric) con i valori possibili: secondi, numero e percentuale. Il valore di una metrica misurata viene restituito nella proprietà relativa al valore (value).

La proprietà DurationIncreaseSeconds indica l'unità di misura in secondi. Per CriticalErrorCount l'unità di misura è un numero che rappresenta un conteggio di errori.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Analisi delle cause radice dei problemi e consigli per migliorare

L'ultima parte del log delle prestazioni di Intelligent Insights riguarda l'analisi automatica delle cause radice del problema di diminuzione delle prestazioni identificato. Le diciture sono di semplice comprensione e sono presenti tramite la proprietà relativa all'analisi delle cause radice (rootCauseAnalysis_s). I consigli di miglioramento sono inclusi nel log, ove possibile.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

È possibile usare il log delle prestazioni Intelligent Insights con i [log di monitoraggio di Azure]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) o una soluzione di terze parti per le funzionalità di avviso e creazione di report di DevOps personalizzate.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui concetti di [Intelligent Insights](intelligent-insights-overview.md).
- Informazioni su come [risolvere i problemi relativi alle prestazioni con Intelligent Insights](intelligent-insights-troubleshoot-performance.md).
- Informazioni su come [monitorare i problemi di prestazioni usando analisi SQL di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Informazioni su come [raccogliere e usare i dati dei log dalle risorse di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).
