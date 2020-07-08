---
title: Potenziamento e ampliamento dei processi di Analisi di flusso di Azure
description: Questo articolo descrive come ridimensionare un processo di Analisi di flusso tramite il partizionamento dei dati di input, l'ottimizzazione delle query e l'impostazione di unità di streaming per il processo.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: d982cc94a9ab0517d6453a30371635c1e3100676
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835598"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Ridimensionare un processo di Analisi di flusso di Azure per aumentare la velocità effettiva
Questo articolo illustra come ottimizzare una query per aumentare la velocità effettiva per i processi di Analisi di flusso. È possibile usare la seguente guida per ridimensionare il processo per gestire carichi più elevati e sfruttare i vantaggi di più risorse di sistema (ad esempio maggiore larghezza di banda, più risorse della CPU, una maggiore memoria).
Come prerequisito, è necessario leggere gli articoli seguenti:
-   [Informazioni e modifica delle unità di streaming](stream-analytics-streaming-unit-consumption.md)
-   [Creare processi che possono essere parallelizzati](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Caso 1: la query è intrinsecamente completamente eseguibile in parallelo tra le partizioni di input
Se la query è intrinsecamente completamente eseguibile in parallelo tra le partizioni di input, è possibile seguire la procedura seguente:
1.  Creare la query in modo che sia perfettamente parallela usando la parola chiave **PARTITION BY**. Visualizzare altri dettagli nella sezione dei processi perfettamente paralleli [in questa pagina](stream-analytics-parallelization.md).
2.  A seconda dei tipi di output usati nella query, alcuni output potrebbero non essere eseguibili in parallelo, o richiederebbero un'altra configurazione perfettamente parallela. Ad esempio, l'output di Power BI non è parallelizzabile. Gli output vengono sempre uniti prima di essere inviati al sink di output. I BLOB, le tabelle, l'ADLS, il Bus di servizio e la funzione di Azure vengono parallelizzati automaticamente. Gli output di SQL e SQL DW hanno un'opzione per la parallelizzazione. Hub eventi deve avere il set di configurazione PartitionKey per la corrispondenza con il campo **PARTITION BY** (in genere PartitionId). Per l'Hub eventi, prestare anche particolare attenzione a far corrispondere il numero di partizioni per tutti gli input e output per evitare il cross-over tra le partizioni. 
3.  Eseguire la query con **SU 6** (ovvero la capacità massima di un singolo nodo di calcolo) per misurare la velocità effettiva massima ottenibile, e se si usa **GROUP BY**, misurare il numero di gruppi (cardinalità) che il processo riesce a gestire. Di seguito sono elencati i sintomi generali dei limiti della risorsa del sistema nel raggiungere il processo.
    - La metrica di utilizzo % SU è superiore all'80%. Indica che l'uso della memoria è elevato. I fattori che contribuiscono all'aumento della metrica sono descritti [qui](stream-analytics-streaming-unit-consumption.md). 
    -   Il timestamp di output è in ritardo rispetto all'ora. A seconda della logica della query, il timestamp di output potrebbe avere un offset della logica dall'ora. Tuttavia, dovrebbero procedere approssimativamente allo stesso ritmo. Se il timestamp di output è sempre più in ritardo, è un indicatore del fatto che il sistema è in overworking. Può trattarsi di un risultato della limitazione del sink di output di downstream o dell'uso elevato del CPU. Non si fornisce una metrica di utilizzo del CPU in questa fase, pertanto può essere difficile distinguere i due.
        - Se il problema è dovuto alla limitazione del sink, potrebbe essere necessario aumentare il numero di partizioni di output (e anche le partizioni di input per mantenere il processo completamente eseguibili in parallelo) o aumentare la quantità di risorse del sink (ad esempio il numero di unità di richiesta per CosmosDB).
    - Nel diagramma del processo è presente una metrica dell'evento per backlog di partizione per ogni input. Se la metrica dell'evento di backlog continua ad aumentare, è anche un indicatore del fatto che la risorsa del sistema è vincolata (o a causa della limitazione del sink di output o a causa del CPU elevato).
4.  Dopo aver determinato i limiti di ciò che può essere raggiunto da un processo di 6 unità di ricerca, è possibile estrapolare in modo lineare la capacità di elaborazione del processo quando si aggiungono altre unità di ricerca, presupponendo che non siano presenti asimmetrie di dati che rendono una determinata partizione "critica".

> [!NOTE]
> Scegliere il numero giusto di Unità di Streaming: poiché l'Analisi di flusso di Azure crea un nodo di elaborazione per ogni 6 unità di ricerca aggiunta, è consigliabile rendere il numero di nodi un divisore del numero di partizioni di input, in modo che le partizioni possano essere distribuite uniformemente tra i nodi.
> Ad esempio, si è misurato che il processo delle 6 unità di ricerca può raggiungere i 4 MB/s di velocità di elaborazione e il conteggio delle partizioni di input è pari a 4. È possibile scegliere di eseguire il processo con 12 unità di ricerca per ottenere una velocità di elaborazione di circa 8 MB/s, o 24 unità di ricerca per raggiungere i 16 MB/s. È possibile decidere quando aumentare il numero di unità di ricerca per il processo a qualsiasi valore, come una funzione del tasso di input.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Caso 2 - se la query non è perfettamente parallela.
Se la query non è perfettamente parallela, è possibile seguire la procedura seguente.
1.  Iniziare prima con una query senza **PARTITION BY** per evitare la complessità del partizionamento ed eseguire la query con 6 unità di ricerca per misurare il carico massimo come nel [Caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Se è possibile ottenere il carico previsto in termini di velocità effettiva, l'operazione è conclusa. In alternativa è possibile scegliere di misurare lo stesso processo in esecuzione su SU 3 e SU 1 per determinare il numero minimo di SU adeguato per il proprio scenario.
3.  Se non è possibile ottenere la velocità effettiva desiderata, provare a suddividere la query in più passaggi, se possibile e se non dispone già di più passaggi e allocare fino a SU 6 per ogni passaggio nella query. Ad esempio, se si dispone di 3 passaggi, allocare 18 unità di ricerca nell'opzione "Scalabilità".
4.  Durante l'esecuzione di un processo di questo tipo, l'Analisi di flusso di Azure colloca ogni passaggio nel proprio nodo con 6 risorse dell'unità di risorse dedicate. 
5.  Se ancora non è stata raggiunta la destinazione del carico, è possibile tentare di usare **PARTITION BY** a partire dai passaggi più vicini all'input. Per l'operatore **GROUP BY** che non può essere partizionabile naturalmente, è possibile usare il modello di aggregazione globale o locale per eseguire un **GROUP BY** partizionato seguito da un **GROUP BY**  non partizionato. Ad esempio, se si desidera contare quante automobili attraversano ciascun casello ogni 3 minuti e il volume dei dati va oltre ciò che può essere gestito da 6 unità di ricerca.

Query:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
Nella query precedente si conteggiano le automobili per casello per partizione e quindi si somma il conteggio da tutte le partizioni insieme.

Una volta partizionata, per ogni partizione del passaggio, allocare fino a 6 unità di risorsa, ogni partizione con 6 unità di risorsa è il valore massimo, pertanto ogni partizione può essere posizionata nel proprio nodo di elaborazione.

> [!Note]
> Se la query non può essere partizionata, l'aggiunta di altre unità di risorse in una query a più passaggi non sempre servirà a migliorare la velocità effettiva. Un modo per ottenere prestazioni è quello di ridurre il volume sui passaggi iniziali usando il modello di aggregazione locale o globale, come descritto in precedenza nel passaggio 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Caso 3: si esegue un numero elevato di query indipendenti in un processo.
Per determinati casi d'uso ISV, in cui è più conveniente elaborare dati da più tenant in un singolo processo, usando input e output separati per ogni tenant, si finisce per eseguire delle query (ad esempio 20) indipendenti in un singolo processo. Il presupposto è che ogni carico di tale sottoquery è relativamente piccolo. In questo caso, è possibile seguire la procedura seguente.
1.  In questo caso, non usare **PARTITION BY** nella query
2.  Se si usa l'Hub eventi, ridurre il numero di partizione di input portandolo al valore minimo di 2.
3.  Eseguire la query con 6 unità di ricerca. Con il carico previsto per ogni sottoquery, aggiungere più sottoquery possibili, fino a quando il processo non supera i limiti delle risorse di sistema. Fare riferimento al [Caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) per i sintomi riportati in questo caso.
4.  Quando si raggiunge il limite di sottoquery misurato in precedenza, iniziare ad aggiungere la sottoquery ad un nuovo processo. Il numero di processi per l'esecuzione come una funzione del numero di query indipendente dovrebbe essere piuttosto lineare, presupponendo che non siano presenti asimmetrie del carico. È quindi possibile prevedere il numero di processi di 6 unità di ricerca necessari ad eseguire come una funzione del numero di tenant che si desidera gestire.
5.  Quando si usano i join dei dati di riferimento con tali query, unire gli input insieme, prima di creare un join con gli stessi dati di riferimento. Quindi, se necessario, suddividere gli eventi. In caso contrario, ogni join dei dati di riferimento mantiene una copia dei dati di riferimento nella memoria, probabilmente ingrandendo inutilmente l'uso della memoria.

> [!Note] 
> Quanti tenant inserire in ogni processo?
> Questo modello di query spesso ha un numero elevato di sottoquery e ciò comporta una topologia molto grande e complessa. Il controller del processo potrebbe non essere in grado di gestire una topologia di così grandi dimensioni. Come regola generale, rimanere al di sotto di 40 tenant per il processo di 1 unità di ricerca al di sotto di 60 tenant per i processi di 3 unità di ricerca e 6 unità di ricerca. Quando si supera la capacità del controller, il processo non verrà avviato correttamente.



## <a name="get-help"></a>Ottenere aiuto
Per ulteriore supporto, provare la [Pagina delle domande di Domande e risposte Microsoft per Analisi di flusso di Azure](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

