---
title: Informazioni sul monitoraggio dei processi di Analisi di flusso di Azure
description: Questo articolo descrive come monitorare i processi di Analisi di flusso di Azure nel portale di Azure.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: seodec18
ms.openlocfilehash: 6d3558511721a91c3a195cb510a1a00d5d8a9a51
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487879"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Informazioni sul monitoraggio dei processi di Analisi di flusso e su come monitorare le query

## <a name="introduction-the-monitor-page"></a>Introduzione: Pagina Monitoraggio
Il portale di Azure evidenzia le metriche delle prestazioni chiave che possono essere usate per monitorare e risolvere i problemi di prestazioni delle query e dei processi. Per visualizzare queste metriche, esplorare il processo di analisi di flusso di cui si desidera vedere le metriche e visualizzare la sezione **Monitoraggio** nella pagina della panoramica.  

![Collegamento Monitoraggio dei processi di Analisi di flusso](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Verrà visualizzata la finestra mostrata di seguito:

![Dashboard Monitoraggio dei processi di Analisi di flusso](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metriche disponibili per l'analisi di flusso
| Metrica                 | Definizione                               |
| ---------------------- | ---------------------------------------- |
| Eventi di input con backlog       | Numero di eventi di input con backlog. Un valore diverso da zero per questa metrica implica che il processo non è in grado di rimanere aggiornato con il numero di eventi in arrivo. Se il valore aumenta lentamente o in modo coerente con un numero diverso da zero, è necessario aumentare il numero di istanze del processo. Per altre informazioni, visitare [Informazioni sulle unità di flusso e su come modificarle](stream-analytics-streaming-unit-consumption.md). |
| Errori di conversione dati | Numero di eventi di output che non è stato possibile convertire nello schema di output previsto. Criteri di errore possono essere modificati in "Drop" (rilascia) per rimuovere gli eventi che verificano questo scenario. |
| % Di utilizzo CPU (anteprima)       | Percentuale di CPU utilizzata dal processo. Anche se questo valore è molto elevato (90% o superiore), non è necessario aumentare il numero di unità di streaming in base a questa sola metrica. Se aumenta il numero di eventi di input con backlog o di ritardo della filigrana, è possibile usare questa metrica% di utilizzo della CPU per determinare se la CPU è il collo di bottiglia. È possibile che questa metrica includa picchi intermittenti. È consigliabile eseguire test di ridimensionamento per determinare il limite superiore del processo dopo il quale gli input ricevono un ritardo o un ritardo della filigrana a causa del collo di bottiglia della CPU. |
| Eventi di input anticipati       | Eventi il cui timestamp di applicazione è precedente all'ora di arrivo di più di 5 minuti. |
| Richieste di funzioni non riuscite | Numero di chiamate non riuscite alla funzione di Azure Machine Learning (se presente). |
| Eventi di funzioni        | Numero di chiamate inviate alla funzione di Azure Machine Learning (se presente). |
| Richieste di funzioni      | Numero di chiamate alla funzione di Azure Machine Learning (se presente). |
| Errori di deserializzazione dell'input       | Numero di eventi di input che non è stato possibile deserializzare.  |
| Byte evento di input      | Quantità di dati ricevuta dal processo di Analisi di flusso, in termini di byte. Può essere usata per convalidare l'invio degli eventi all'origine di input. |
| Eventi di input           | Numero di record deserializzati da eventi di input. Questo conteggio non include gli eventi in ingresso che generano errori di deserializzazione. Gli stessi eventi possono essere inseriti più volte da Analisi di flusso in scenari come i recuperi interni e i self join. È quindi consigliabile non aspettarsi che le metriche degli eventi di input e di output corrispondano se il processo ha una semplice query "pass-through". |
| Origini di input ricevute       | Numero di messaggi ricevuti dal processo. Per l'hub eventi, un messaggio è un singolo EventData. Per il BLOB, un messaggio è un singolo BLOB. Si noti che le origini di input vengono conteggiate prima della deserializzazione. Se si verificano errori di deserializzazione, le origini di input possono essere maggiori degli eventi di input. In caso contrario, possono essere minori o uguali agli eventi di input poiché ogni messaggio può contenere più eventi. |
| Ultimi eventi di input      | Eventi arrivati in un secondo momento rispetto alla finestra di tolleranza per arrivo in ritardo configurata. Altre informazioni sulle [Considerazioni sull'ordine degli eventi con Analisi di flusso di Azure](./stream-analytics-time-handling.md). |
| Eventi non ordinati    | Numero di eventi non ordinati ricevuti che sono stati eliminati o a cui è stato assegnato un timestamp modificato, in base ai Criteri di ordinamento eventi. Può essere influenzato dalla configurazione dell'impostazione della Finestra di tolleranza elementi non in ordine. |
| Eventi di output          | Quantità di dati inviata dal processo di Analisi di flusso alla destinazione di output, in termini di numero di eventi. |
| Errori di runtime         | Numero totale di errori correlati all'elaborazione delle query (esclusi gli errori rilevati durante l'inserimento di eventi o l'output dei risultati) |
| % utilizzo unità di streaming       | Percentuale di memoria utilizzata dal processo. Se il% di utilizzo è costantemente superiore al 80%, il ritardo della filigrana è in aumento e il numero di eventi backlog è in aumento, è consigliabile aumentare le unità di streaming. Un utilizzo elevato indica che il processo sta usando vicino al numero massimo di risorse allocate. |
| Ritardo limite       | Il ritardo limite massimo in tutte le partizioni di tutti gli output del processo. |

È possibile usare queste metriche per [monitorare le prestazioni del processo di Analisi di flusso](./stream-analytics-set-up-alerts.md#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalizzazione del monitoraggio nel portale di Azure
È possibile modificare il tipo di grafico, le metriche visualizzate e l’intervallo di tempo nelle impostazioni di Modifica grafico. Per altre informazioni, vedere [How to Customize Monitoring](../azure-monitor/data-platform.md) (Come personalizzare il monitoraggio).

  ![Grafico cronologico del monitoraggio delle query di Analisi di flusso](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Output più recente
Un altro interessante punto dati per monitorare il processo è l'ora dell'ultimo output, visualizzata nella pagina di panoramica.
L'ora visualizzata corrisponde al tempo di applicazione (ovvero l'ora del timestamp relativo ai dati dell'evento) dell'output più recente del processo.

## <a name="get-help"></a>Ottenere aiuto
Per maggiore supporto, provare la [Pagina delle domande di Domande e risposte Microsoft per Analisi di flusso di Azure](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](/rest/api/streamanalytics/)
