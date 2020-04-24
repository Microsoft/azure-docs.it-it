---
title: Avvisi del log in Monitoraggio di Azure
description: Attivare messaggi di posta elettronica e notifiche, chiamare URL di siti Web (webhook) o usare l'automazione quando vengono soddisfatte le condizioni di query di analisi specificate per Avvisi di Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: a6abf4665c27771497037da35f85bb540e6e904e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665222"
---
# <a name="log-alerts-in-azure-monitor"></a>Avvisi del log in Monitoraggio di Azure

Questo articolo contiene informazioni dettagliate sugli avvisi di log, uno dei tipi di avvisi supportati nel nuovo servizio [Avvisi di Azure](../../azure-monitor/platform/alerts-overview.md) che consente agli utenti di usare la piattaforma di analisi di Azure come base per la generazione di avvisi.

Un avviso di log è costituito da regole di ricerca log create per i [log di monitoraggio di Azure](../../azure-monitor/learn/tutorial-viewdata.md) o [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Per altre informazioni sull'utilizzo, vedere [Creating log alerts in Azure](../../azure-monitor/platform/alerts-log.md) (Creazione di avvisi dei log in Azure).

> [!NOTE]
> I dati di log più comuni dei [log di monitoraggio di Azure](../../azure-monitor/learn/tutorial-viewdata.md) sono ora disponibili anche nella piattaforma di metriche in Monitoraggio di Azure. Per i dettagli, vedere [Metric Alert for Logs](../../azure-monitor/platform/alerts-metric-logs.md) (Avvisi di metrica per i log).


## <a name="log-search-alert-rule---definition-and-types"></a>Regole di avviso di ricerca log - Definizioni e tipi

Le regole di ricerca log vengono create da Avvisi di Azure in modo da eseguire automaticamente le query di log specificate a intervalli regolari.  Se i risultati della query del log corrispondono a criteri specifici viene creato un record di avviso. La regola può quindi eseguire automaticamente una o più azioni tramite [gruppi di azioni](../../azure-monitor/platform/action-groups.md). Potrebbe essere necessario il ruolo di [Collaboratore di Monitoraggio di Azure](../../azure-monitor/platform/roles-permissions-security.md) per creare, modificare e aggiornare gli avvisi dei log, oltre ai diritti di accesso ed esecuzione di query per le destinazioni dell'analisi in una regola di avviso o una query di avviso. Se la creazione dell'utente non ha accesso a tutte le destinazioni di analisi nella regola di avviso o nella query di avviso, la creazione della regola potrebbe non riuscire o la regola di avviso del log verrà eseguita con risultati parziali.

Le regole di ricerca log sono definite dai dettagli seguenti:

- **Query di log**.  La query eseguita ogni volta che viene attivata la regola di avviso.  I record restituiti da questa query vengono usati per determinare se deve essere attivato un avviso. La query di analisi può essere applicata a un'area di lavoro di Log Analytics o un'app di Application Insights specifica e addirittura a [più risorse di Log Analytics e Application Insights](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights), purché l'utente abbia i diritti di accesso e di query su tutte le risorse. 
    > [!IMPORTANT]
    > supporto [delle query tra risorse](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) negli avvisi del log per Application Insights e avvisi del log per [log Analytics configurate solo con l'API scheduledQueryRules](../../azure-monitor/platform/alerts-log-api-switch.md) .

    Alcuni comandi e combinazioni di analisi non sono compatibili con l'uso negli avvisi dei log. Per informazioni più dettagliate, vedere [Query per avvisi dei log in Monitoraggio di Azure](../../azure-monitor/platform/alerts-log-query.md).

- **Periodo di tempo**.  Specifica l'intervallo di tempo per la query. La query restituisce solo i record creati in questo intervallo dell'ora corrente. Il periodo di tempo limita i dati recuperati per la query di log in modo da impedirne l'uso improprio e ignora qualsiasi comando di tempo (come ago) usato nella query di log. <br>*Se, ad esempio, il periodo di tempo è impostato su 60 minuti e la query viene eseguita alle 1:15 PM, per eseguire la query di log vengono restituiti solo i record creati tra 12:15 e 1:15 PM. Ora, se la query di log usa il comando time come fa (7D), la query di log verrebbe eseguita solo per i dati compresi tra 12:15 e 1:15 PM, come se i dati fossero disponibili solo negli ultimi 60 minuti. E non per sette giorni di dati come specificato nella query di log.*

- **Frequenza**.  Specifica la frequenza con cui deve essere eseguita la query. Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore. Deve essere uguale o minore del periodo di tempo.  Se il valore è maggiore del periodo di tempo, il record rischia di essere omesso.<br>*Si consideri, ad esempio, un periodo di tempo di 30 minuti e una frequenza di 60 minuti.  Se la query viene eseguita a 1:00, restituisce record compresi tra 12:30 e 1:00 PM.  Alla successiva esecuzione della query è 2:00 quando restituisce record compresi tra 1:30 e 2:00.  Tutti i record creati tra 1:00 e 1:30 non verranno mai valutati.*

- **Soglia**.  Per determinare se è necessario creare un avviso, vengono valutati i risultati della ricerca log.  La soglia è diversa per tipi diversi di regole di avviso di ricerca log.

Le regole di ricerca log, sia per i [log di monitoraggio di Azure](../../azure-monitor/learn/tutorial-viewdata.md) sia per [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), possono essere di due tipi diversi. Ognuno di questi tipi viene descritto in dettaglio nelle sezioni seguenti.

- **[Numero di risultati](#number-of-results-alert-rules)**. Singolo avviso creato quando i record di numeri restituiti dalla ricerca log superano un numero specificato.
- **[Unità di misura della metrica](#metric-measurement-alert-rules)**.  Avviso creato per ogni oggetto nei risultati della ricerca log quando i valori superano la soglia specificata.

Di seguito sono riportate le differenze tra i tipi di regola di avviso.

- Le regole di avviso *Numero di risultati* creano sempre un singolo avviso, mentre una regola di avviso *Unità di misura della metrica* crea un avviso per ogni oggetto che supera la soglia.
- Le regole di avviso *Numero di risultati* creano un avviso quando la soglia viene superata una sola volta. Le regole di avviso *Unità di misura della metrica* possono creare un avviso quando viene superata la soglia più di una volta in un intervallo di tempo specifico.

### <a name="number-of-results-alert-rules"></a>Regole di avviso Numero di risultati

Le regole di avviso **Numero di risultati** creano un singolo avviso quando il numero di record restituiti dalla query di ricerca supera la soglia specificata. Questo tipo di regola di avviso è adatto per l'uso con eventi come quelli dei log eventi di Windows, Syslog, WebApp Response e log personalizzati.  Si potrebbe voler creare un avviso quando viene creato un determinato evento di errore o quando vengono creati più eventi di errore entro un periodo di tempo specifico.

**Soglia**: la soglia per una regola di avviso Numero di risultati è maggiore o minore di un valore specifico.  Se il numero di record restituiti dalla ricerca log corrisponde a questi criteri, viene creato un avviso.

Per generare un avviso per un evento singolo, impostare il numero di risultati su un valore maggiore di 0 e controllare l'occorrenza di un singolo evento creato dopo l'ultima esecuzione della query. Alcune applicazioni possono registrare un errore occasionale che non deve necessariamente generare un avviso.  Ad esempio, l'applicazione può ripetere il processo che ha creato l'evento di errore e riuscire quindi al tentativo al successivo.  In questo caso, non è consigliabile creare l'avviso, a meno che non vengano creati più eventi entro un periodo di tempo specifico.  

In alcuni casi, è possibile creare un avviso in assenza di un evento.  Ad esempio, un processo può registrare eventi regolari per indicare che funziona correttamente.  Se non registra uno di questi eventi entro un periodo di tempo specifico, dovrà essere creato un avviso.  In questo caso, impostare la soglia su un valore **minore di 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Esempio di avviso di log di tipo Numero di record

Si consideri uno scenario in cui si vuole sapere quando l'app basata sul Web fornisce una risposta agli utenti con codice 500, Errore interno del server. Si deve creare una regola di avviso con i dettagli seguenti:  

- **Query:** requests | where resultCode == "500"<br>
- **Periodo di tempo:** 30 minuti<br>
- **Frequenza avviso:** cinque minuti<br>
- **Valore di soglia:** maggiore di 0<br>

L'avviso esegue la query ogni 5 minuti, con 30 minuti di dati, per cercare tutti i record in cui il codice di risultato è 500. Se viene trovato anche un solo record di questo tipo, viene generato l'avviso e viene attivata l'azione configurata.

### <a name="metric-measurement-alert-rules"></a>Regole di avviso Unità di misura della metrica

Le regole di avviso di **misurazione delle metriche** creano un avviso per ogni oggetto in una query con un valore che supera una soglia specificata e una condizione di trigger specificata. Diversamente dalle regole **di avviso numero di risultati** , le regole di avviso di **misurazione delle metriche** funzionano quando il risultato dell'analisi fornisce una serie temporale. Presentano le differenze seguenti dalle regole di avviso **Numero di risultati**.

- **Funzione di aggregazione**: determina il calcolo che viene eseguito e possibilmente un campo numerico da aggregare.  Ad esempio, **count()** restituisce il numero di record nella query, **avg(CounterValue)** restituisce la media del campo CounterValue nell'intervallo. La funzione di aggregazione nella query deve essere denominata AggregatedValue e deve fornire un valore numerico. 

- **Campo del gruppo**: viene creato un record con un valore aggregato per ogni istanza di questo campo e può essere generato un avviso per ognuna.  Ad esempio, se si desidera generare un avviso per ogni computer, si userà **dal Computer**. Se nella query di avviso sono specificati più campi gruppo, l'utente può specificare il campo da usare per ordinare i risultati tramite il parametro **Aggregate On** (metricColumn).

    > [!NOTE]
    > L'opzione *Aggregate On* (metricColumn) è disponibile solo per gli avvisi relativi ai log di tipo Unità di misura della metrica per Application Insights e per gli avvisi relativi ai log per [Log Analytics configurati usando l'API scheduledQueryRules](../../azure-monitor/platform/alerts-log-api-switch.md).

- **Intervallo**: definisce l'intervallo di tempo in cui i dati vengono aggregati.  Ad esempio, specificando **cinque minuti**, viene creato un record per ogni istanza del campo Gruppo, aggregato a intervalli di 5 minuti nel periodo di tempo specificato per l'avviso.

    > [!NOTE]
    > Per specificare l'intervallo, è necessario usare la funzione Bin nella query. Poiché la funzione bin() può generare risultati con intervalli di tempo diversi, il servizio convertirà automaticamente il comando bin in comando bin_at con l'ora appropriata al runtime per garantire risultati con punto fisso. Il tipo di avviso di log Unità di misura della metrica è progettato per funzionare con le query che contengono fino a tre istanze del comando bin().
    
- **Soglia**: la soglia per le regole di avviso Unità di misurazione della metrica è definita da un valore di aggregazione e da un numero di violazioni della sicurezza.  Se qualsiasi punto dati in una ricerca di log supera questo valore, ciò viene considerato una violazione.  Se il numero di violazioni per un oggetto nei risultati supera il valore specificato, viene creato un avviso per l'oggetto.

L'errata configurazione dell'opzione *Aggregate On* o *metricColumn* può causare errori di attivazione delle regole di avviso. Per altre informazioni, vedere [Risoluzione dei problemi quando la regola di avviso Unità di misura della metrica non è corretta](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Esempio di avviso di log di tipo Unità di misurazione

Si consideri uno scenario in cui si desidera creare un avviso se l'uso del processo di un computer supera il 90% tre volte in 30 minuti.  Si deve creare una regola di avviso con i dettagli seguenti:  

- **Query:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **Periodo di tempo:** 30 minuti<br>
- **Frequenza avviso:** cinque minuti<br>
- **Logica di avviso-soglia & della condizione:** Maggiore di 90<br>
- **Campo gruppo (aggregate-on):** Computer
- **Attiva l'avviso in base a:** totale violazioni maggiore di 2<br>

La query crea un valore medio per ogni computer a intervalli di 5 minuti.  Questa query verrebbe eseguita ogni 5 minuti per i dati raccolti nei 30 minuti precedenti. Dato che il campo Gruppo (funzione Aggregate On) scelto fa parte della colonna "Computer" - AggregatedValue viene suddiviso in valori diversi di "Computer" e si determina l'utilizzo medio del processore per ogni computer a intervalli di 5 minuti.  Ad esempio, il risultato della query di esempio per tre computer, sarebbe il seguente.


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Se il risultato della query dovesse essere tracciato, verrebbe visualizzato come segue.

![Risultati della query di esempio](media/alerts-unified-log/metrics-measurement-sample-graph.png)

In questo esempio vediamo l'utilizzo medio del processore calcolato per un arco di tempo di 5 minuti, suddiviso a intervalli di 5 minuti per ognuno dei tre computer. La soglia di 90 viene violata una sola volta da srv01 nell'intervallo 1:25. In confronto, srv02 supera la soglia di 90 negli intervalli 1:10, 1:15 e 1:25, mentre srv03 supera la soglia di 90 negli intervalli 1:10, 1:15, 1:20 e 1:30.
Dato che l'avviso è configurato per attivarsi nel caso in cui il totale delle violazioni sia superiore a due, vediamo che solo srv02 e srv03 soddisfano questi criteri. Di conseguenza, verranno creati avvisi separati per SRV02 e Srv03, poiché hanno violato due volte la soglia del 90% in più cesti temporali.  Se l' *avviso trigger basato su:* parametro è stato invece configurato per l'opzione *violazioni continue* , viene generato un avviso **solo** per Srv03 poiché ha violato la soglia per tre ceste orari consecutivi da 1:10 a 1:20. **Non** verrebbe attivato per srv02, dal momento che ha violato la soglia di due intervalli tempo consecutivi compresi tra 1:10 e 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Regola di avviso di ricerca log - attivazione e stato

Le regole di avviso di ricerca log funzionano solo sulla logica compilata nella query. Il sistema di avvisi non dispone di alcun contesto dello stato del sistema, della finalità o della causa radice implicita nella query. Di conseguenza, gli avvisi del log sono denominati senza stato. Le condizioni vengono valutate come "TRUE" o "FALSE" ogni volta che vengono eseguite.  Verrà generato un avviso ogni volta che la valutazione della condizione di avviso è "TRUE", indipendentemente dal fatto che venga attivata in precedenza.    

Questo comportamento viene ora visualizzato con un esempio pratico. Si supponga di disporre di una regola di avviso del log denominata *Contoso-Log-Alert*, che è configurata come illustrato nell' [esempio relativo all'avviso di log dei tipi di risultati](#example-of-number-of-records-type-log-alert). La condizione è una query di avviso personalizzata progettata per cercare il codice risultato 500 nei log. Se nei log si trovano più codici di risultato 500, la condizione dell'avviso è true. 

A ogni intervallo riportato di seguito, il sistema di avvisi di Azure valuta la condizione per *Contoso-Log-Alert*.


| Tempo    | Numero di record restituiti dalla query di ricerca nei log | Condizione del log valutazione | Risultato 
| ------- | ----------| ----------| ------- 
| 1:05 PM | 0 record | 0 non è > 0, quindi FALSE |  L'avviso non viene attivato. Non è stata chiamata alcuna azione.
| 1:10 PM | 2 record | 2 > 0, quindi TRUE  | Avvisi attivati e gruppi di azioni chiamati. Stato dell'avviso attivo.
| 1:15 PM | 5 record | 5 > 0 TRUE  | Avvisi attivati e gruppi di azioni chiamati. Stato dell'avviso attivo.
| 1:20 PM | 0 record | 0 non è > 0, quindi FALSE |  L'avviso non viene attivato. Non è stata chiamata alcuna azione. Lo stato dell'avviso è rimasto attivo.

Utilizzando il caso precedente come esempio:

Alle 1:15 gli avvisi di Azure non possono determinare se i problemi sottostanti rilevati a 1:10 vengono mantenuti e se i record sono errori di rete nuovi o ripetizioni di errori precedenti alle ore 1:22:00. È possibile che la query fornita dall'utente non tenga conto dei record precedenti e che il sistema non lo conosca. Il sistema di avvisi di Azure è stato creato in modo da essere in errore sul lato dell'attenzione e genera nuovamente l'avviso e le azioni associate alle 1:15 PM. 

Alle 1:20, quando vengono visualizzati zero record con il codice risultato 500, gli avvisi di Azure non possono essere certi che la causa del codice di risultato 500 visualizzato alle 1:10 e 1:15 è ora risolta. Non sa se i problemi di errore 500 si verificheranno di nuovo per gli stessi motivi. Di conseguenza, *Contoso-Log-Alert* non cambia in **risolto** nel dashboard avvisi di Azure e/o le notifiche non vengono inviate indicando che l'avviso è stato risolto. Solo l'utente, che conosce la condizione esatta o il motivo della logica incorporata nella query di analisi, può [contrassegnare l'avviso come chiuso](alerts-managing-alert-states.md) se necessario.

## <a name="pricing-and-billing-of-log-alerts"></a>Prezzi e fatturazione degli avvisi dei log

I prezzi applicabili agli avvisi dei log sono disponibili nella pagina [Prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). Nelle fatture di Azure gli avvisi dei log sono rappresentati come tipo `microsoft.insights/scheduledqueryrules` con:

- Avvisi dei log in Application Insights visualizzati con il nome esatto insieme al gruppo di risorse e alle proprietà dell'avviso
- Avvisi dei log in Log Analytics visualizzati con il nome esatto insieme al gruppo di risorse e alle proprietà dell'avviso, se creati con l'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

L'[API legacy Log Analytics](../../azure-monitor/platform/api-alerts.md) ha azioni di avviso e pianificazioni che fanno parte della ricerca salvata di Log Analytics e non [risorse di Azure](../../azure-resource-manager/management/overview.md) vere e proprie. Pertanto per abilitare la fatturazione per tali avvisi di log legacy creati per Log Analytics usando il portale di Azure **senza** [passare alla nuova API](../../azure-monitor/platform/alerts-log-api-switch.md) o usando l'[API legacy Log Analytics](../../azure-monitor/platform/api-alerts.md), vengono create pseudo-regole di avviso nascoste in `microsoft.insights/scheduledqueryrules` per la fatturazione in Azure. Le pseudo-regole di avviso nascoste create per la fatturazione in `microsoft.insights/scheduledqueryrules` vengono visualizzate come `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` insieme al gruppo di risorse e alle proprietà di avviso.

> [!NOTE]
> Se sono presenti caratteri non validi come `<, >, %, &, \, ?, /`, vengono sostituiti con `_` nella pseudo-regola di avviso nascosta e pertanto anche nella fattura di Azure.

Per rimuovere le risorse scheduleQueryRules nascoste create per le regole di avviso mediante l'[API legacy Log Analytics](api-alerts.md), l'utente può eseguire una delle operazioni seguenti:

- Può [cambiare la preferenza dell'API per le regole di avviso nell'area di lavoro di Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) e, senza perdere le regole di avviso o il monitoraggio, passare all'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) conforme ad Azure Resource Manager. In questo modo non è più necessario creare pseudo-regole di avviso nascoste per la fatturazione.
- Se l'utente non vuole cambiare la preferenza dell'API, dovrà **eliminare** la pianificazione e l'azione di avviso originali usando l'[API legacy Log Analytics](api-alerts.md) o eliminare nel [portale di Azure la regola di avviso dei log originale](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal).

Inoltre, per le risorse scheduleQueryRules nascoste create per la fatturazione di regole di avviso tramite l' [API log Analytics legacy](api-alerts.md), qualsiasi operazione di modifica, ad esempio PUT, avrà esito negativo. Poiché le `microsoft.insights/scheduledqueryrules` pseudo regole del tipo hanno lo scopo di addebitare le regole di avviso create usando l' [API log Analytics legacy](api-alerts.md). Qualsiasi modifica della regola di avviso deve essere eseguita usando l' [api log Analytics legacy](api-alerts.md) (o) l'utente può [cambiare la preferenza API per le regole di avviso per l'uso dell'](../../azure-monitor/platform/alerts-log-api-switch.md) [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) .

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [creazione di avvisi dei log in Azure](../../azure-monitor/platform/alerts-log.md).
* Acquisire familiarità con i [webhook negli avvisi dei log in Azure](alerts-log-webhook.md).
* Informazioni su [Avvisi di Azure](../../azure-monitor/platform/alerts-overview.md).
* Altre informazioni su [Application Insights](../../azure-monitor/app/analytics.md).
* Altre informazioni su [log Analytics](../../azure-monitor/log-query/log-query-overview.md).
