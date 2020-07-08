---
title: Unità di streaming in analisi di flusso di Azure
description: Questo articolo descrive l'impostazione di unità di streaming e altri fattori che influiscono sulle prestazioni in Analisi di flusso di Azure.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 397e455c8b6a1097e2a32473036e1acd2bbdf2eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84704183"
---
# <a name="understand-and-adjust-streaming-units"></a>Informazioni sulle unità di flusso e su come modificarle

Unità di streaming (SUs) rappresenta le risorse di calcolo allocate per l'esecuzione di un processo di analisi di flusso. Più alto è il numero di unità di streaming, maggiori sono le risorse di memoria e CPU allocate per il processo. Questa capacità consente di concentrarsi sulla logica di query, senza doversi preoccupare di gestire l'hardware, per eseguire il processo di Analisi di flusso nei tempi previsti.

Per ottenere l'elaborazione di flussi a bassa latenza, i processi di Analisi di flusso di Azure eseguono tutta l'elaborazione in memoria. Quando la memoria viene esaurita, il processo di streaming non riesce. Di conseguenza, per un processo di produzione è importante monitorare l'utilizzo delle risorse di un processo di streaming e assicurarsi che siano state allocate risorse sufficienti per mantenere il processo in esecuzione 24 ore su 24, 7 giorni su 7.

La metrica di utilizzo in percentuale delle unità di streaming, da 0% a 100%, descrive l'utilizzo di memoria del carico di lavoro. Per un processo di streaming con footprint minimo, questa metrica è in genere compresa tra 10% e 20%. Se il valore in percentuale di utilizzo delle unità di streaming è basso e gli eventi di input vengono gestiti con backlog, è probabile che il carico di lavoro richieda più risorse di calcolo ed è quindi necessario aumentare il numero di unità di streaming. È consigliabile mantenere la metrica delle unità di streaming al di sotto dell'80% in modo da tenere conto dei picchi occasionali. Microsoft consiglia di impostare un avviso quando la metrica di utilizzo delle unità di streaming raggiunge l'80% per evitare l'esaurimento delle risorse. Per altre informazioni, vedere [Esercitazione: Impostare gli avvisi per i processi di Analisi di flusso di Azure](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Configurare le unità di streaming di Analisi di flusso
1. Accedi per [portale di Azure](https://portal.azure.com/)

2. Nell'elenco delle risorse trovare il processo di Analisi di flusso da ridimensionare e aprirlo. 

3. Nell'intestazione **Configura** della pagina del processo selezionare **Ridimensiona**. 

    ![Configurazione del processo di Analisi di flusso nel portale di Azure][img.stream.analytics.preview.portal.settings.scale]
    
4. Usare il dispositivo di scorrimento per impostare le unità di streaming per il processo. Si noti che è possibile definire solo impostazioni specifiche delle unità di streaming. 
5. È possibile modificare il numero di unità di streaming assegnate al processo anche quando è in esecuzione. Questa operazione non è possibile se il processo usa un [output non partizionato](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#query-using-non-partitioned-output) o presenta [una query in più passaggi con diversi valori di partizionamento](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#multi-step-query-with-different-partition-by-values). Quando il processo è in esecuzione, è possibile scegliere da un set di valori SU. 

## <a name="monitor-job-performance"></a>Monitorare le prestazioni del processo
Nel portale di Azure è possibile rilevare la velocità effettiva di un processo:

![Processi di monitoraggio di Analisi dei flussi di Azure][img.stream.analytics.monitor.job]

Calcolare la velocità effettiva prevista del carico di lavoro. Se la velocità effettiva è inferiore al previsto, ottimizzare la partizione di input e la query, quindi aggiungere unità di streaming al processo.

## <a name="how-many-sus-are-required-for-a-job"></a>Quante unità di streaming sono necessarie per un processo?

Il numero di unità di streaming necessarie per un particolare processo dipende dalla configurazione della partizione per gli input e dalla query definita nel processo. La pagina **Ridimensiona** consente di impostare il numero corretto di unità di streaming. È consigliabile allocare più unità di streaming di quelle necessarie. Il motore di elaborazione di Analisi di flusso è ottimizzato per la latenza e la velocità effettiva al costo di allocazione di memoria aggiuntiva.

In generale la procedura consigliata consiste nell'iniziare con 6 unità di streaming per le query che non usano **PARTITION BY**. Determinare quindi il punto critico usando un metodo di valutazione e correzione degli errori con cui modificare il numero di unità di streaming dopo aver passato quantità rappresentative di dati e aver esaminato la metrica di utilizzo in percentuale delle unità di streaming. Il numero massimo di unità di streaming che possono essere usate da un processo di Analisi dei flussi dipende dal numero di passaggi nella query definita per il processo e dal numero di partizioni in ogni passaggio. Altre informazioni sui limiti sono disponibili [qui](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Per altre informazioni sulla scelta del numero corretto di unità di streaming, vedere questa pagina: [Ridimensionare i processi di Analisi di flusso di Azure per aumentare la velocità effettiva dell'elaborazione dei flussi di dati](stream-analytics-scale-jobs.md)

> [!Note]
> Il numero di unità di streaming necessarie per un particolare processo dipende dalla configurazione delle partizioni per gli input e dalla query definita per il processo. È prevista una quota massima di unità di streaming che è possibile selezionare per un processo. Per impostazione predefinita, ogni sottoscrizione di Azure ha una quota fino a 500 SUs per tutti i processi di analisi in un'area specifica. Per superare la quota massima di unità di streaming per le sottoscrizioni, contattare il [supporto tecnico Microsoft](https://support.microsoft.com). I valori validi di unità di streaming per processo sono 1, 3, 6 e poi a salire, con incrementi di 6.

## <a name="factors-that-increase-su-utilization"></a>Fattori che determinano un maggiore utilizzo in percentuale delle unità di streaming 

Gli elementi di query temporali costituiscono il set principale degli operatori con stato forniti da Analisi di flusso. Analisi di flusso gestisce lo stato di queste operazioni internamente per conto dell'utente, controllando l'utilizzo della memoria, i checkpoint per la resilienza e il ripristino dello stato durante gli aggiornamenti del servizio. Anche se Analisi di flusso gestisce completamente gli stati, è opportuno che gli utenti prendano in considerazione alcune indicazioni relative alle procedure consigliate.

Si noti che un processo con logica di query complessa potrebbe presentare un'elevata percentuale di utilizzo delle unità di streaming anche quando non riceve eventi di input in modo continuo. Questa situazione può verificarsi dopo un picco improvviso negli eventi di input e output. Se la query è complessa, il processo potrebbe continuare a mantenere lo stato in memoria.

La percentuale di utilizzo dell'unità di archiviazione può improvvisamente scendere a 0 per un breve periodo di tempo prima di tornare ai livelli previsti. Ciò si verifica a causa di errori temporanei o dell'avvio di aggiornamenti di sistema. L'aumento del numero di unità di streaming per un processo potrebbe non ridurre l'utilizzo di SU% se la query non è [completamente parallela](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization).

Quando si confronta l'utilizzo in un periodo di tempo, usare le [metriche della frequenza degli eventi](stream-analytics-monitoring.md). Le metriche InputEvents e eventi mostrano il numero di eventi letti ed elaborati. Sono disponibili metriche che indicano anche il numero di eventi di errore, ad esempio gli errori di deserializzazione. Quando aumenta il numero di eventi per unità di tempo, nella maggior parte dei casi viene aumentata la percentuale SU%.

## <a name="stateful-query-logicin-temporal-elements"></a>Logica di query con stato negli elementi temporali
Una delle esclusive funzionalità dei processi di Analisi di flusso di Azure è l'esecuzione dell'elaborazione con stato, ad esempio per funzioni di aggregazione finestra, join temporali e funzioni di analisi temporali. Ognuno di questi operatori mantiene le informazioni sullo stato.La dimensione massima della finestra temporale per questi elementi di query è sette giorni. 

Il concetto di finestra temporale è presente in diversi elementi di query di Analisi di flusso:
1. Funzioni di aggregazione finestra: GROUP BY di finestre temporali scorrevoli, di salto e a cascata

2. Join temporali: JOIN con funzione DATEDIFF

3. Funzioni di analisi temporale: ISFIRST, LAST e LAG con LIMIT DURATION

I fattori seguenti influiscono sulla memoria usata (parte della metrica di unità di streaming) dai processi di Analisi di flusso:

## <a name="windowed-aggregates"></a>Funzioni di aggregazione finestra
La memoria utilizzata (dimensione dello stato) per una funzione di aggregazione finestra non è sempre direttamente proporzionale alla dimensione della finestra. È invece proporzionale alla cardinalità dei dati o al numero di gruppi in ogni finestra temporale.


Ad esempio, nella query seguente il numero associato a `clusterid` è la cardinalità della query. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Per attenuare i problemi causati dalla cardinalità elevata nella query precedente, è possibile inviare eventi a hub eventi partizionati in base a `clusterid` e scalare verticalmente la query consentendo al sistema di elaborare ogni partizione di input separatamente usando **Partition by** , come illustrato nell'esempio seguente:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

In seguito al partizionamento, la query viene distribuita su più nodi. Di conseguenza, il numero di valori `clusterid` in arrivo in ogni nodo diminuisce, riducendo a sua volta la cardinalità dell'operatore GROUP BY. 

Le partizioni di Hub eventi devono essere create in base alla chiave di raggruppamento per evitare la necessità di un passaggio di riduzione. Per altre informazioni, vedere [Panoramica di Hub eventi](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Join temporali
La memoria utilizzata (dimensione dello stato) di un join temporale è proporzionale al numero di eventi nell'area di ridimensionamento temporale del join, che corrisponde alla frequenza di input degli eventi moltiplicata per la dimensione della stanza di wiggle. In altre parole, la memoria utilizzata dai join è proporzionale all'intervallo di tempo DateDiff moltiplicato per la frequenza media degli eventi.

Il numero di eventi senza corrispondenza nel join influisce sull'utilizzo della memoria per la query. La query seguente cerca le impressioni di annunci che generano clic:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

In questo esempio è possibile che vengano mostrati molti annunci e che pochi utenti facciano clic sugli annunci. È anche necessario mantenere tutti gli eventi nell'intervallo di tempo. La memoria consumata è proporzionale alle dimensioni della finestra e alla frequenza degli eventi. 

Per correggere questo problema, inviare gli eventi all'hub eventi partizionati in base alle chiavi di join (in questo caso ID) e scalare la query consentendo al sistema di elaborare ogni partizione di input separatamente usando **Partition by** , come illustrato:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

In seguito al partizionamento, la query viene distribuita su più nodi. Di conseguenza, il numero di eventi in arrivo in ogni nodo diminuisce, riducendo a sua volta le dimensioni dello stato mantenuto nell'intervallo di join. 

## <a name="temporal-analytic-functions"></a>Funzioni di analisi temporale
La memoria utilizzata (dimensione dello stato) per una funzione di analisi temporale è proporzionale alla frequenza degli eventi moltiplicata per la durata.La memoria utilizzata dalle funzioni di analisi non è proporzionale alla dimensione della finestra, ma piuttosto al numero di partizioni in ogni finestra temporale.

La correzione è simile a quella per il join temporale. È possibile scalare orizzontalmente la query usando **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Buffer non in ordine 
L'utente può configurare le dimensioni del buffer non in ordine nel riquadro di configurazione Ordinamento eventi. Il buffer viene usato per contenere gli input per la durata dell'intervallo e per riordinarli. Le dimensioni del buffer sono proporzionali alla frequenza di input degli eventi moltiplicata per la dimensione dell'intervallo per l'ordine non corretto. La dimensione predefinita dell'intervallo è 0. 

Per correggere l'overflow del buffer non in ordine, scalare orizzontalmente la query usando **PARTITION BY**. In seguito al partizionamento, la query viene distribuita su più nodi. Di conseguenza, il numero di eventi in arrivo in ogni nodo diminuisce, riducendo a sua volta il numero di eventi in ogni buffer di riordinamento. 

## <a name="input-partition-count"></a>Conteggio delle partizioni di input 
Ogni partizione di input di un processo ha un buffer. Maggiore è il numero di partizioni di input, maggiore è il numero di risorse utilizzate dal processo. Per ogni unità di streaming, Analisi di flusso di Azure può elaborare circa 1 MB di input al secondo. È pertanto possibile ottimizzare definendo una corrispondenza tra il numero di unità di streaming di Analisi di flusso e il numero di partizioni nell'istanza di Hub eventi. 

Un processo configurato con una sola unità di streaming è in genere sufficiente per un'istanza di Hub eventi con due partizioni, ovvero il numero minimo di partizioni per Hub di eventi. Se l'istanza di Hub eventi ha più partizioni, il processo di Analisi di flusso utilizza più risorse, ma non usa necessariamente la velocità effettiva aggiuntiva fornita da Hub eventi. 

Per un processo con 6 unità di streaming, possono essere necessarie 4 o 8 partizioni in Hub eventi. È tuttavia opportuno evitare di configurare troppe partizioni non necessarie poiché ciò determina un utilizzo eccessivo delle risorse, come nel caso di un'istanza di Hub eventi con 16 o più partizioni in un processo di Analisi di flusso con una sola unità di streaming. 

## <a name="reference-data"></a>Dati di riferimento 
I dati di riferimento in Analisi di flusso di Azure vengono caricati in memoria per consentire la ricerca rapida. Con l'implementazione corrente ogni operazione di join con dati di riferimento mantiene una copia dei dati di riferimento in memoria, anche se il join viene eseguito con gli stessi dati di riferimento più volte. Per le query con **PARTITION BY**, ogni partizione include una copia dei dati di riferimento, in modo che le partizioni siano completamente separate. Con l'effetto moltiplicatore l'utilizzo della memoria può aumentare rapidamente se si esegue il join con i dati di riferimento più volte con più partizioni.  

### <a name="use-of-udf-functions"></a>Uso di funzioni definite dall'utente
Quando si aggiunge una funzione definita dall'utente, Analisi di flusso di Azure carica il runtime JavaScript in memoria. Questo comportamento influisce sulla percentuale di unità di streaming.

## <a name="next-steps"></a>Passaggi successivi
* [Create parallelizable queries in Azure Stream Analytics](stream-analytics-parallelization.md) (Creare query eseguibili in parallelo in Analisi di flusso di Azure)
* [Ridimensionare processi di analisi di flusso di Azure per aumentare la velocità effettiva](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
