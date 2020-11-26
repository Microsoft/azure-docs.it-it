---
title: Introduzione alle query di log in Monitoraggio di Azure | Microsoft Docs
description: Questo articolo include un'esercitazione per iniziare a scrivere query di log in Monitoraggio di Azure.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: 8b3b6a41da6e3100739da920472d582711d1e9b1
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024382"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Introduzione alle query di log in Monitoraggio di Azure

> [!NOTE]
> È possibile eseguire questo esercizio nel proprio ambiente se si raccolgono dati da almeno una macchina virtuale. In caso contrario, usare l'[ambiente Demo](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), che include numerosi dati di esempio.  Se si ha già familiarità con l'esecuzione di query in KQL e si deve solo creare rapidamente query utili basate sui tipi di risorse, vedere il [riquadro query di esempio salvate](saved-queries.md).

In questa esercitazione si apprenderà come scrivere query di log in Monitoraggio di Azure. Si apprenderà come:

- Comprendere la struttura delle query
- Ordinare i risultati di query
- Filtrare i risultati di query
- Specificare un intervallo di tempo
- Selezionare i campi da includere nei risultati
- Definire e usare campi personalizzati
- Aggregare e raggruppare i risultati

Per un'esercitazione sull'uso di Log Analytics nel portale di Azure, vedere [Introduzione a Log Analytics in Monitoraggio di Azure](get-started-portal.md).<br>
Per altri dettagli sulle query di log in Monitoraggio di Azure, vedere [Panoramica delle query di log in Monitoraggio di Azure](log-query-overview.md).

Di seguito è disponibile una versione video di questa esercitazione:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Scrittura di una nuova query

Le query possono iniziare con un nome di tabella o con il comando *search*. È consigliabile iniziare con un nome di tabella, perché definisce un chiaro ambito per la query e consente di migliorare sia le prestazioni di query che la pertinenza dei risultati.

> [!NOTE]
> Il linguaggio di query Kusto usato da Monitoraggio di Azure fa distinzione tra maiuscole e minuscole. Le parole chiave del linguaggio sono in genere scritte in lettere minuscole. Quando si usano nomi di tabelle o di colonne in una query, assicurarsi di usare la giusta combinazione di maiuscole e minuscole, come indicato nel riquadro dello schema.

### <a name="table-based-queries"></a>Query basate su tabella

Monitoraggio di Azure organizza i dati in tabelle, ognuna composta da più colonne. Tutte le tabelle e le colonne vengono visualizzate nel riquadro Schema del portale di Log Analytics. Identificare una tabella di interesse ed esaminarne i dati:

```Kusto
SecurityEvent
| take 10
```

La query precedente restituisce 10 risultati dalla tabella *SecurityEvent*, senza alcun ordine specifico. Si tratta di un modo molto comune per esaminare a grandi linee una tabella e comprenderne struttura e contenuto. Ecco come è fatta la query:

* La query inizia con il nome di tabella *SecurityEvent*, che definisce l'ambito della query.
* Il carattere di barra verticale, o pipe, (|) separa i comandi, in modo che l'output del primo comando sia l'input del comando successivo. È possibile inoltrare tramite pipe un numero qualsiasi di elementi.
* La barra verticale è seguita dal comando **take**, che restituisce un numero specifico di record arbitrari dalla tabella.

Sarebbe stato possibile eseguire la query anche senza aggiungere `| take 10`. La query sarebbe comunque valida, ma potrebbe restituire fino a 10.000 risultati.

### <a name="search-queries"></a>Query di ricerca

Le query di ricerca sono meno strutturate e in genere più appropriate per trovare record che includono un valore specifico in una delle relative colonne:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Questa query cerca nella tabella *SecurityEvent* i record che contengono il termine "Cryptographic". Di questi record, ne vengono restituiti e visualizzati 10. Se si omette la parte `in (SecurityEvent)` e si esegue solamente il comando `search "Cryptographic"`, la ricerca viene eseguita in *tutte* le tabelle e quindi richiede più tempo ed è meno efficiente.

> [!WARNING]
> Le query di ricerca in genere sono più lente delle query basate su tabelle perché devono elaborare più dati. 

## <a name="sort-and-top"></a>Sort e top
Sebbene **take** sia utile per ottenere pochi record, i risultati vengono selezionati e visualizzati senza alcun ordine particolare. Per ottenere una visualizzazione ordinata, è possibile usare il comando **sort**, per ordinare i risultati in base alla colonna preferita:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

In questo modo, tuttavia, possono venire restituiti troppi risultati e la query potrebbe richiedere tempo. La query precedente ordina l'*intera* tabella SecurityEvent in base alla colonna TimeGenerated. Il portale di analisi limita quindi la visualizzazione per mostrare solo 10.000 record. Questo approccio non è ovviamente ottimale.

Il modo migliore per ottenere solo i 10 record più recenti consiste nell'usare **top**, che consente di ordinare l'intera tabella sul lato server e quindi restituisce i primi record:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

L'ordine decrescente rappresenta il tipo di ordinamento predefinito, quindi in genere l'argomento **desc** viene omesso. L'output avrà un aspetto simile al seguente:

![Primi 10 elementi](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Where: filtro in base a una condizione
I filtri, come indica il loro nome, filtrano i dati in base a una condizione specifica. Questo è il modo più comune per limitare i risultati di query alle informazioni pertinenti.

Per aggiungere un filtro a una query, usare l'operatore **where** seguito da una o più condizioni. La query seguente, ad esempio, restituisce solo i record *SecurityEvent* in cui _Level_ corrisponde a _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Quando si scrivono le condizioni di filtro, è possibile usare le espressioni seguenti:

| Expression | Descrizione | Esempio |
|:---|:---|:---|
| == | Controllo dell'uguaglianza<br>(fa distinzione tra maiuscole e minuscole) | `Level == 8` |
| =~ | Controllo dell'uguaglianza<br>(non fa distinzione tra maiuscole e minuscole) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Controllo della disuguaglianza<br>(entrambe le espressioni sono identiche) | `Level != 4` |
| *and*, *or* | Operatori richiesti tra le condizioni| `Level == 16 or CommandLine != ""` |

Per filtrare in base a più condizioni, è possibile usare **and**:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

oppure inoltrare tramite pipe più elementi **where**, uno dopo l'altro:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> I valori possono essere di tipi diversi, quindi potrebbe essere necessario eseguire il cast per effettuare il confronto in base al tipo corretto. Ad esempio, la colonna *Level* di SecurityEvent è di tipo String, quindi è necessario eseguire il cast a un tipo numerico, come *int* o *long*, per poter usare gli operatori numerici: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Specificare un intervallo di tempo

### <a name="time-picker"></a>Controllo di selezione di data e ora

Il controllo di selezione di data e ora si trova accanto al pulsante Esegui e indica che viene eseguita una query solo sui record delle ultime 24 ore. Si tratta dell'intervallo di tempo predefinito applicato a tutte le query. Per ottenere solo i record dall'ultima ora, selezionare _Ultima ora_ ed eseguire di nuovo la query.

![Selezione ora](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtro temporale nella query

È anche possibile definire un intervallo di tempo personalizzato aggiungendo un filtro temporale alla query. È consigliabile posizionare il filtro temporale immediatamente dopo il nome della tabella: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

Nel filtro temporale precedente, `ago(30m)` indica "30 minuti fa", quindi la query restituisce solo i record degli ultimi 30 minuti. Altre unità di tempo includono giorni (2d), minuti (25m) e secondi (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Project ed extend: selezionare e calcolare le colonne

Usare **project** per selezionare colonne specifiche da includere nei risultati:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

L'esempio precedente genera l'output seguente:

![Risultati della query project](media/get-started-queries/project.png)

È anche possibile usare **project** per rinominare le colonne e definirne di nuove. L'esempio seguente usa il comando project per questi scopi:

* Selezionare solo le colonne originali *Computer* e *TimeGenerated*.
* Visualizzare la colonna *Activity* come *EventDetails*.
* Creare una nuova colonna denominata *EventCode*. La funzione **substring()** viene usata per ottenere solo i primi quattro caratteri del campo Activity.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

Il comando **extend** mantiene tutte le colonne originali nel set di risultati e definisce colonne aggiuntive. La query seguente usa **extend** per aggiungere la colonna *EventCode*. Si noti che questa colonna potrebbe non essere visualizzata alla fine dei risultati della tabella. In tal caso sarà necessario espandere i dettagli di un record per visualizzarla.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Summarize: aggregare gruppi di righe
Usare **summarize** per identificare i gruppi di record, in base a una o più colonne, e applicarvi aggregazioni. L'uso più comune di **summarize** è con *count*, che restituisce il numero di risultati in ogni gruppo.

La query seguente esamina tutti i record *Perf* dell'ultima ora, li raggruppa in base a *ObjectName* e conta i record in ogni gruppo: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

In alcuni casi è opportuno definire i gruppi in base a più dimensioni. Ogni combinazione univoca di questi valori definisce un gruppo separato:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Un altro uso comune consiste nell'eseguire calcoli matematici o statistici su ogni gruppo. Ad esempio, di seguito viene calcolata la media di *CounterValue* per ogni computer:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Sfortunatamente, i risultati di questa query non sono significativi perché combinano i valori di diversi contatori delle prestazioni. Per renderli più significativi, è necessario calcolare la media separatamente per ogni combinazione di *CounterName* e *Computer*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Riepilogare in base a una colonna temporale
Il raggruppamento dei risultati può anche essere basato su una colonna temporale o un altro valore continuo. Un semplice riepilogo di tipo `by TimeGenerated`, tuttavia, creerebbe gruppi per ogni singolo millisecondo nell'intervallo di tempo, poiché si tratta di valori univoci. 

Per creare gruppi basati su valori continui, è consigliabile suddividere l'intervallo in unità gestibili tramite **bin**. La query seguente analizza i record *Perf* che misurano la memoria libera (*Available MBytes*) in un computer specifico. Calcola il valore medio di ogni periodo di 1 ora negli ultimi 7 giorni:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Per rendere più chiaro l'output, visualizzarlo come un grafico temporale, che mostra la memoria disponibile nel tempo:

![Query relativa alla memoria disponibile nel tempo](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'uso di dati stringa in una query di log, vedere [Usare le stringhe nelle query di log di Monitoraggio di Azure](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations).
- Per altre informazioni sull'aggregazione di dati in una query di log, vedere [Aggregazioni avanzate nelle query dei log di Monitoraggio di Azure](/azure/data-explorer/write-queries#advanced-aggregations).
- Per informazioni su come aggiungere dati di più tabelle, vedere [Join nelle query di log di Monitoraggio di Azure](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
- Per la documentazione sull'intero linguaggio di query Kusto, vedere [Informazioni di riferimento sul linguaggio KQL](/azure/kusto/query/).
