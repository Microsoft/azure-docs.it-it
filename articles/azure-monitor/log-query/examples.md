---
title: Esempi di query in Log Analytics di Monitoraggio di Azure | Microsoft Docs
description: Esempi di query in Log Analytics che usano il linguaggio Kusto.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/03/2018
ms.author: bwren
ms.openlocfilehash: d5cad3869e74f33a2d1a56352c658bb9c8f23db6
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52885809"
---
# <a name="log-analytics-query-examples"></a>Esempi di query in Log Analytics
L'articolo include vari esempi di [query](../../azure-monitor/log-query/log-query-overview.md) che usano il [linguaggio Kusto](https://docs.microsoft.com/azure/kusto/query/) per recuperare tipi diversi di dati da Log Analytics. Poiché vengono usati metodi diversi per consolidare e analizzare i dati, è possibile usare questi esempi per identificare strategie diverse che si possono applicare in base alle necessità.  

Per informazioni dettagliate sulle diverse parole chiave usate in questi esempi, vedere il [materiale di riferimento per il linguaggio Kusto](https://docs.microsoft.com/azure/kusto/query/). Se non si conosce Log Analytics, seguire una [lezione sulla creazione di query](get-started-queries.md).

## <a name="events"></a>Eventi

### <a name="search-application-level-events-described-as-cryptographic"></a>Cercare gli eventi a livello di applicazione descritti come "crittografici"
Questo esempio cerca nella tabella **Events** (Eventi) i record in cui **EventLog** è _Application_ (Applicazione) e **RenderedDescription** contiene _cryptographic_ (crittografico). La ricerca include i record delle ultime 24 ore.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription == "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Cercare eventi correlati all'unmarshalling
Cercare nelle tabelle **Event** (Evento) e **SecurityEvents** i record che indicano _unmashaling_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Heartbeat

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Creare un grafico che visualizza da una settimana all'altra il numero di computer che inviano dati

L'esempio seguente crea un grafico del numero di computer distinti che hanno inviato heartbeat, ogni settimana.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Individuare i computer non aggiornati

L'esempio seguente trova i computer attivi nelle ultime 24 ore, ma che non hanno inviato heartbeat nell'ultima ora.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Ottenere il record di heartbeat più recente per ogni indirizzo IP di computer

Questo esempio restituisce il record di heartbeat più recente per ogni indirizzo IP di computer.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Trovare la corrispondenza tra i record di stato protetto e i record di heartbeat

Questo esempio trova i record di stato di protezione e i record di heartbeat correlati, corrispondenti per computer e ora.
Il campo dell'ora viene arrotondato al minuto più vicino. A tal fine abbiamo usato il calcolo del bin di runtime: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Frequenza di disponibilità del server

Calcolare la frequenza di disponibilità del server in base ai record di heartbeat. La disponibilità viene definita come "almeno 1 heartbeat ogni ora".
Se quindi un server è stato disponibile 98 ore su 100, la frequenza di disponibilità è del 98%.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Più tipi di dati

### <a name="chart-the-record-count-per-table"></a>Creare un grafico del conteggio dei record per ogni tabella
L'esempio seguente raccoglie tutti i record di tutte le tabelle delle ultime cinque ore e conta quanti record erano presenti in ogni tabella. I risultati vengono mostrati in un diagramma temporale.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Contare tutti i log raccolti nell'ultima ora in base al tipo
L'esempio seguente cerca ogni elemento segnalato nell'ultima ora e conta i record di ogni tabella in base a **Type** (Tipo). I risultati vengono visualizzati in un grafico a barre.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Contare i record di diagnostica di Azure in base alla categoria
Questo esempio conta tutti i record di diagnostica di Azure per ogni categoria univoca.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Ottenere un record casuale per ogni categoria univoca
Questo esempio ottiene un singolo record casuale di diagnostica di Azure per ogni categoria univoca.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Ottenere il record più recente per categoria
Questo esempio ottiene il record di diagnostica di Azure più recente in ogni categoria univoca.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Monitoraggio della rete

### <a name="computers-with-unhealthy-latency"></a>Computer con latenza dannosa
Questo esempio crea un elenco di computer distinti con latenza dannosa.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Prestazioni

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Aggiungere i record delle prestazioni di un computer per correlare memoria e CPU
Questo esempio correla i record delle **prestazioni** di un computer specifico e crea due diagrammi temporali, uno relativo all'utilizzo medio della CPU e l'altro per l'utilizzo massimo della memoria.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Grafico dell'utilizzo della CPU nelle prestazioni per computer
Questo esempio calcola e crea un grafico dell'utilizzo della CPU in computer che iniziano con _Contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Stato protezione

### <a name="computers-with-non-reporting-protection-status-duration"></a>Computer con durata dello stato di protezione Segnalazioni non inviate
Questo esempio elenca i computer che hanno avuto uno stato di protezione _Not Reporting_ e la durata di questo stato.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Trovare la corrispondenza tra i record di stato protetto e i record di heartbeat
Questo esempio trova i record di stato di protezione e i record di heartbeat correlati, corrispondenti per computer e ora.
Il campo dell'ora viene arrotondato al minuto più vicino usando **bin**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Record di sicurezza

### <a name="count-security-events-by-activity-id"></a>Contare gli eventi di sicurezza per ID attività


Questo esempio si affida alla struttura fissa della colonna **Activity** (Attività): \<ID\>-\<Name\> (Nome).
Analizza il valore di **Activity** (Attività) in due nuove colonne e conta le occorrenze di ogni **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Contare gli eventi di sicurezza correlati ad autorizzazioni
Questo esempio mostra il numero di record **securityEvent**, in cui la colonna **Activity** (Attività)contiene l'intero termine _Permissions_ (Autorizzazioni). La query si applica ai record creati negli ultimi 30 minuti.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Trovare gli account che non sono riusciti a eseguire l'accesso da computer con un rilevamento di sicurezza
Questo esempio trova e conta gli account che non sono riusciti a eseguire l'accesso da computer in cui si identifica un rilevamento di sicurezza.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Sono disponibili i dati di sicurezza?
L'esplorazione dei dati spesso inizia con il controllo della loro disponibilità. Questo esempio mostra il numero di record **SecurityEvent** negli ultimi 30 minuti.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Analizzare l'ID e il nome dell'attività
I due esempi seguenti si affidano alla struttura fissa della colonna **Activity** (Attività): \<ID\>-\<Name\> (Nome). Il primo esempio usa l'operatore di **analisi** per assegnare valori a due nuove colonne: **activityID** e **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

Questo esempio usa l'operatore di **suddivisione** per creare una matrice di valori separati
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Processi di credenziali esplicite
L'esempio seguente mostra un grafico a torta dei processi nell'ultima settimana che hanno usato credenziali esplicite

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Principali processi di esecuzione

L'esempio seguente mostra una sequenza temporale di attività per i cinque processi più comuni, negli ultimi tre giorni.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Trovare ripetuti tentativi di accesso non riusciti con lo stesso account da indirizzi IP diversi

L'esempio seguente trova i tentativi di accesso non riusciti avvenuti con lo stesso account da più di cinque indirizzi IP diversi nelle ultime sei ore.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Trovare gli account utente che non sono riusciti a eseguire l'accesso 
L'esempio seguente identifica gli account utente che non sono riusciti a eseguire l'accesso più di cinque volte nell'ultimo giorno e l'ora in cui è avvenuto l'ultimo tentativo di accesso.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Tramite le istruzioni **join** e **let** possiamo controllare se gli stessi account sospetti sono riusciti a eseguire l'accesso in un secondo momento.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Uso

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>Calcolare le dimensioni medie dei report sull'utilizzo delle prestazioni per computer

Questo esempio calcola le dimensioni medie dei report sull'utilizzo delle prestazioni per computer, nelle ultime 3 ore.
I risultati vengono visualizzati in un grafico a barre.
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>Percentili di latenza del diagramma temporale 50 e 95

Questo esempio calcola e crea un grafico del 50esimo e del 95esimo percentile di **avgLatency** segnalata per ogni ora nelle ultime 24 ore.

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>Utilizzo di computer specifici in data corrente
Questo esempio recupera i dati di **utilizzo** relativi all'ultimo giorno per nomi di computer che contengono la stringa _ContosoFile_. I risultati vengono ordinati in base a **TimeGenerated**.

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>Aggiornamenti

### <a name="computers-still-missing-updates"></a>Computer con aggiornamenti ancora mancanti
Questo esempio mostra un elenco di computer in cui alcuni giorni prima mancavano uno o più aggiornamenti critici e in cui mancano tuttora.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(3d)..ago(2d))
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| summarize makeset(Computer);

Update
| where TimeGenerated > ago(1d)
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Passaggi successivi

- Vedere il [materiale di riferimento per il linguaggio Kusto](/azure/kusto/query) per informazioni dettagliate sul linguaggio.
- Seguire la [lezione sulla scrittura di query in Log Analytics](get-started-queries.md).
