---
title: Soluzione Gestione avvisi in Azure Log Analytics | Microsoft Azure
description: La soluzione Alert Management in Log Analytics consente di analizzare tutti gli avvisi nell'ambiente.  Oltre a consolidare gli avvisi generati in Log Analytics, importa gli avvisi dai gruppi di gestione di System Center Operations Manager connessi in Log Analytics.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 6f3761b1c933d113bf65c70370c29bcfa7950eea
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728655"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Soluzione Gestione avvisi in Log Analytics

![Icona di Alert Management](media/alert-management-solution/icon.png)

La soluzione Alert Management in Log Analytics consente di analizzare tutti gli avvisi nel repository di Log Analytics.  Questi avvisi possono provenire da diverse origini, incluse le fonti [create da Log Analytics](../alerts/alerts-overview.md) o [importate da Nagios o Zabbix](../vm/quick-collect-linux-computer.md). La soluzione importa anche gli avvisi da qualsiasi [gruppo di gestione di System Center Operations Manager collegato](../agents/om-agents.md).

## <a name="prerequisites"></a>Prerequisiti
La soluzione funziona con i record presenti nel repository di Log Analytics con un tipo di **avviso**, pertanto è necessario eseguire qualsiasi configurazione necessaria per raccogliere questi record.

- Per gli avvisi di Log Analytics, [creare regole di avviso](../alerts/alerts-overview.md) per creare i record degli avvisi direttamente nel repository.
- Per gli avvisi di Nagios e Zabbix, [configurare tali server](../vm/quick-collect-linux-computer.md) per inviare avvisi a Log Analytics.
- Per gli avvisi SCOM, [connettere il gruppo di gestione Operations Manager all'area di lavoro Log Analytics](../agents/om-agents.md).  Tutti gli avvisi creati in System Center Operations Manager vengono importati in Log Analytics.  

## <a name="configuration"></a>Configurazione
Aggiungere la soluzione Gestione avvisi all'area di lavoro Log Analytics usando la procedura descritta in [Aggiungere soluzioni](../insights/solutions.md). Non è richiesta alcuna ulteriore configurazione.

## <a name="management-packs"></a>Management Pack
Se il gruppo di gestione di System Center Operations Manager è connesso all'area di lavoro Log Analytics, in Center System Operations Manager verranno installati i Management Pack seguenti quando si aggiunge questa soluzione.  Per questi Management Pack non è richiesta alcuna configurazione o manutenzione.

* Alert Management di Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Per maggiori informazioni sulla modalità di aggiornamento dei Management Pack, vedere [Connettere Operations Manager a Log Analytics](../agents/om-agents.md).

## <a name="data-collection"></a>Raccolta dati
### <a name="agents"></a>Agenti
La tabella seguente descrive le origini connesse che sono supportate da questa soluzione.

| Origine connessa | Supporto | Descrizione |
|:--- |:--- |:--- |
| [Agenti di Windows](../agents/agent-windows.md) | No |Gli agenti di Windows diretti non generano avvisi.  Gli avvisi di Log Analytics possono essere creati da eventi e dati sulle prestazioni raccolti dagli agenti di Windows. |
| [Agenti Linux](../vm/quick-collect-linux-computer.md) | No |Gli agenti di Linux diretti non generano avvisi.  Gli avvisi di Log Analytics possono essere creati da eventi e dati sulle prestazioni raccolti dagli agenti di Linux.  Gli avvisi di Nagios e Zabbix vengono raccolti da quei server che richiedono l'agente Linux. |
| [Gruppo di gestione di System Center Operations Manager](../agents/om-agents.md) |Sì |Gli avvisi che sono generati con gli agenti di Operation Manager vengono distribuiti al gruppo di gestione e quindi inoltrati a Log Analytics.<br><br>Non è necessaria una connessione diretta dall'agente Operations Manager a Log Analytics. I dati degli avvisi vengono inoltrati dal gruppo di gestione al repository di Log Analytics. |


### <a name="collection-frequency"></a>Frequenza della raccolta
- I record degli avvisi sono disponibili per la soluzione appena vengono archiviati nel repository.
- I dati degli avvisi vengono inviati dal gruppo di gestione di Operation Manager a Log Analytics ogni tre minuti.  

## <a name="using-the-solution"></a>Uso della soluzione
Quando si aggiunge la soluzione Gestione avvisi all'area di lavoro Log Analytics, il riquadro **Gestione avvisi** viene aggiunto al dashboard.  Il riquadro visualizza un conteggio e la rappresentazione grafica del numero di avvisi attivi generati nelle ultime 24 ore.  Non è possibile modificare questo intervallo di tempo.

![Riquadro di Alert Management](media/alert-management-solution/tile.png)

Fare clic su sul riquadro **Gestione avvisi** per aprire il **relativo** dashboard.  Il dashboard include le colonne nella tabella seguente.  Ogni colonna elenca i primi 10 avvisi per numero corrispondente ai criteri della colonna per l'ambito e l'intervallo di tempo specificati.  È possibile eseguire una ricerca di log che fornisce l'intero elenco facendo clic su **Visualizza tutto** nella parte inferiore della colonna o facendo clic sull'intestazione di colonna.

| Colonna | Descrizione |
|:--- |:--- |
| Critical Alerts |Tutti gli avvisi con un livello di gravità Critico raggruppati per nome dell'avviso.  Fare clic sul nome di un avviso per eseguire una ricerca che restituisce tutti i record per tale avviso. |
| Warning Alerts |Tutti gli avvisi con una gravità Avviso per nome dell'avviso.  Fare clic sul nome di un avviso per eseguire una ricerca che restituisce tutti i record per tale avviso. |
| Avvisi attivi System Center Operations Manager |Tutti gli avvisi raccolti da Operations Manager con uno stato diverso da *Chiuso* raggruppati per origine che ha generato l'avviso. |
| All Active Alerts |Tutti gli avvisi con qualsiasi gravità raggruppati per nome dell'avviso. Include solo gli avvisi Operations Manager con qualsiasi stato diverso da *Chiuso*. |

Se si scorre verso destra, il dashboard elenca diverse query comuni che è possibile selezionare per eseguire una [ricerca log](../logs/log-query-overview.md) per dati di avviso:

![Dashboard di Alert Management](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Record di Log Analytics
La soluzione Alert Management consente di analizzare qualsiasi record con un tipo di **Avviso**.  Gli avvisi creati da Log Analytics o raccolti da Nagios o Zabbix non vengono raccolti direttamente dalla soluzione.

La soluzione non importa avvisi da Operations Manager e crea un record corrispondente per ciascuno di essi con un tipo di **Avviso** e un SourceSystem di **OpsManager**.  Questi record includono le proprietà elencate nella tabella seguente:  

| Proprietà | Descrizione |
|:--- |:--- |
| `Type` |*Avviso* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Dettagli dell'elemento di dati che ha causato la generazione dell'avviso nel formato XML. |
| `AlertDescription` |Descrizione dettagliata dell'avviso. |
| `AlertId` |GUID dell'avviso. |
| `AlertName` |Nome dell'avviso. |
| `AlertPriority` |Livello di priorità dell'avviso. |
| `AlertSeverity` |Livello di gravità dell'avviso. |
| `AlertState` |Ultimo stato di risoluzione dell'avviso. |
| `LastModifiedBy` |Nome dell'utente che ha effettuato l'ultima modifica dell'avviso. |
| `ManagementGroupName` |Nome del gruppo di gestione in cui è stato generato l'avviso. |
| `RepeatCount` |Numero di volte in cui è stato generato lo stesso avviso per lo stesso oggetto monitorato dalla risoluzione. |
| `ResolvedBy` |Nome dell'utente che ha risolto l'avviso. Vuoto se l'avviso non è ancora stato risolto. |
| `SourceDisplayName` |Nome visualizzato dell'oggetto di monitoraggio che ha generato l'avviso. |
| `SourceFullName` |Nome completo dell'oggetto di monitoraggio che ha generato l'avviso. |
| `TicketId` |ID ticket per l'avviso se l'ambiente System Center Operations Manager è integrato con un processo per l'assegnazione di ticket per gli avvisi.  Vuoto se non è assegnato alcun ID ticket. |
| `TimeGenerated` |Data e ora in cui è stato creato l'avviso. |
| `TimeLastModified` |Data e ora in cui è stato modificato l'avviso. |
| `TimeRaised` |Data e ora in cui è stato generato l'avviso. |
| `TimeResolved` |Data e ora in cui è stato risolto l'avviso. Vuoto se l'avviso non è ancora stato risolto. |

## <a name="sample-log-searches"></a>Ricerche di log di esempio
La tabella seguente fornisce ricerche di log di esempio per i record degli avvisi raccolti da questa soluzione. 

| Query | Descrizione |
|:---|:---|
| Avviso &#124; dove SourceSystem == "OpsManager" e AlertSeverity == "error" e TimeRaised > ago(24h) |Avvisi critici generati durante le ultime 24 ore |
| Avviso &#124; dove AlertSeverity == "warning" e TimeRaised > ago(24h) |Avvertenze generate durante le ultime 24 ore |
| Avviso &#124; dove SourceSystem == "OpsManager" e AlertState != "Closed" e TimeRaised > ago(24h) &#124; summarize Count = count() by SourceDisplayName |Origini con avvisi critici generati durante le ultime 24 ore |
| Avviso &#124; dove SourceSystem == "OpsManager" e AlertSeverity == "error" e TimeRaised > ago(24h) e AlertState != "Closed" |Avvisi critici generati durante le ultime 24 ore che sono ancora attivi |
| Avviso &#124; dove SourceSystem == "OpsManager" e TimeRaised > ago(24h) e AlertState == "Closed" |Avvisi generati durante le ultime 24 ore che sono ora chiusi |
| Avviso &#124; dove SourceSystem == "OpsManager" e TimeRaised > ago(1d) &#124; summarize Count = count() by AlertSeverity |Avvisi generati durante l'ultimo giorno raggruppati in base alla relativa gravità |
| Avviso &#124; dove SourceSystem == "OpsManager" e TimeRaised > ago(1d) &#124; ordina per RepeatCount desc |Avvisi generati durante l'ultimo giorno raggruppati in base al relativo valore di conteggio |



## <a name="next-steps"></a>Passaggi successivi
* Leggere l'articolo [Avvisi in Log Analytics](../alerts/alerts-overview.md) per informazioni dettagliate sulla generazione di avvisi di Log Analytics.