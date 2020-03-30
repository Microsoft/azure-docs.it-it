---
title: Informazioni sul monitoraggio dei processi di Analisi di flusso di Azure
description: Questo articolo descrive come monitorare i processi di Analisi di flusso di Azure nel portale di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 4e9f90035816269d2d41781be34d0d8080628b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431660"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Informazioni sul monitoraggio dei processi di Analisi di flusso e su come monitorare le query

## <a name="introduction-the-monitor-page"></a>Introduzione: Pagina di monitoraggio
Il portale di Azure espone le metriche chiave delle prestazioni che possono essere usate per monitorare e risolvere i problemi relativi alle prestazioni delle query e dei processi. Per visualizzare queste metriche, esplorare il processo di analisi di flusso di cui si desidera vedere le metriche e visualizzare la sezione **Monitoraggio** nella pagina della panoramica.  

![Collegamento Monitoraggio dei processi di Analisi di flusso](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Verrà visualizzata la finestra mostrata di seguito:

![Dashboard Monitoraggio dei processi di Analisi di flusso](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metriche disponibili per l'analisi di flusso
| Metrica                 | Definizione                               |
| ---------------------- | ---------------------------------------- |
| Eventi di input con backlog       | Numero di eventi di input con backlog. Un valore diverso da zero per questa metrica implica che il processo non è in grado di rimanere aggiornato con il numero di eventi in arrivo. Se il valore aumenta lentamente o in modo coerente con un numero diverso da zero, è necessario ampliare il processo. Per altre informazioni, visitare [Informazioni sulle unità di flusso e su come modificarle](stream-analytics-streaming-unit-consumption.md). |
| Errori di conversione dati | Numero di eventi di output che non è stato possibile convertire nello schema di output previsto. Criteri di errore possono essere modificati in "Drop" (rilascia) per rimuovere gli eventi che verificano questo scenario. |
| Eventi di input anticipati       | Eventi il cui timestamp di applicazione è precedente all'ora di arrivo di più di 5 minuti. |
| Richieste di funzioni non riuscite | Numero di chiamate non riuscite alla funzione di Azure Machine Learning (se presente). |
| Eventi di funzioni        | Numero di chiamate inviate alla funzione di Azure Machine Learning (se presente). |
| Richieste di funzioni      | Numero di chiamate alla funzione di Azure Machine Learning (se presente). |
| Errori di deserializzazione dell'input       | Numero di eventi di input che non è stato possibile deserializzare.  |
| Byte evento di input      | Quantità di dati ricevuta dal processo di Analisi di flusso, in termini di byte. Può essere usata per convalidare l'invio degli eventi all'origine di input. |
| Eventi di input           | Numero di record deserializzati da eventi di input. Questo conteggio non include gli eventi in ingresso che generano errori di deserializzazione. |
| Origini di input ricevute       | Numero di messaggi ricevuti dal processo. Per l'Hub eventi, un messaggio è un singolo EventData.For Event Hub, a message is a single EventData. Per Blob, un messaggio è un singolo BLOB. Si noti che le origini di input vengono conteggiate prima della deserializzazione. Se sono presenti errori di deserializzazione, le origini di input possono essere maggiori degli eventi di input. In caso contrario, può essere minore o uguale agli eventi di input poiché ogni messaggio può contenere più eventi. |
| Ultimi eventi di input      | Eventi arrivati in un secondo momento rispetto alla finestra di tolleranza per arrivo in ritardo configurata. Altre informazioni sulle [Considerazioni sull'ordine degli eventi con Analisi di flusso di Azure](stream-analytics-out-of-order-and-late-events.md). |
| Eventi non ordinati    | Numero di eventi non ordinati ricevuti che sono stati eliminati o a cui è stato assegnato un timestamp modificato, in base ai Criteri di ordinamento eventi. Può essere influenzato dalla configurazione dell'impostazione della Finestra di tolleranza elementi non in ordine. |
| Eventi di output          | Quantità di dati inviata dal processo di Analisi di flusso alla destinazione di output, in termini di numero di eventi. |
| Errori di runtime         | Numero totale di errori correlati all'elaborazione delle query (esclusi gli errori rilevati durante l'inserimento di eventi o l'output dei risultati) |
| % utilizzo unità di streaming       | L'utilizzo delle unità di streaming assegnate a un processo dalla scheda Scalabilità del processo. Se questo indicatore raggiunge l'80% o più, esiste una forte probabilità che l'elaborazione di eventi possa essere ritardata o interrotta. |
| Ritardo limite       | Il ritardo limite massimo in tutte le partizioni di tutti gli output del processo. |

È possibile usare queste metriche per [monitorare le prestazioni del processo di Analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalizzazione del monitoraggio nel portale di Azure
È possibile modificare il tipo di grafico, le metriche visualizzate e l’intervallo di tempo nelle impostazioni di Modifica grafico. Per altre informazioni, vedere [How to Customize Monitoring](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) (Come personalizzare il monitoraggio).

  ![Grafico cronologico del monitoraggio delle query di Analisi di flusso](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Output più recente
Un altro interessante punto dati per monitorare il processo è l'ora dell'ultimo output, visualizzata nella pagina di panoramica.
L'ora visualizzata corrisponde al tempo di applicazione (ovvero l'ora del timestamp relativo ai dati dell'evento) dell'output più recente del processo.

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi di flusso di AzureGet started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Guida di riferimento al linguaggio di query di Analisi di flusso di AzureAzure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
