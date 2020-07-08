---
title: Come monitorare e ridurre la limitazione delle richieste-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come monitorare, diagnosticare e attenuare i problemi di prestazioni che provocano la latenza e la limitazione delle richieste in Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 2812b535c7aef7987db7106bfa6b07e15a1b61c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81263387"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorare e ridurre la limitazione per evitare la latenza in Azure Time Series Insights

Quando la quantità di dati in ingresso è superiore alla configurazione dell'ambiente, in Azure Time Series Insights si può verificare una latenza o una limitazione.

È possibile evitare la latenza o la limitazione configurando l'ambiente in modo appropriato per la quantità di dati che si vuole analizzare.

È molto probabile riscontrare latenza o limitazione nei casi seguenti:

- Aggiunta di un'origine eventi contenente dati meno recenti che potrebbero superare la velocità in ingresso assegnata, imponendo a Time Series Insights di recuperare.
- Aggiunta di più origini eventi a un ambiente con conseguente picco a causa degli eventi aggiuntivi, che potrebbero superare la capacità dell'ambiente.
- Push di grandi quantità di dati cronologici a un'origine eventi, che potrebbe determinare un ritardo imponendo a Time Series Insights di recuperare.
- Aggiunta di dati di riferimento alla telemetria, che determina un aumento delle dimensioni degli eventi. La dimensione massima consentita per i pacchetti è 32 KB. i pacchetti di dati di dimensioni superiori a 32 KB vengono troncati.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Informazioni sul comportamento di ingresso dei dati Time Series Insights e su come pianificarlo.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorare la latenza e la limitazione con avvisi

Gli avvisi consentono di diagnosticare e attenuare i problemi di latenza che si verificano nell'ambiente in uso.

1. Nel portale di Azure selezionare l'ambiente Time Series Insights desiderato. Quindi selezionare **avvisi**.

   [![Aggiungere un avviso all'ambiente di Time Series Insights](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Fare clic su **+ Nuova regola di avviso**. Verrà quindi visualizzato il pannello **Crea regola** . Selezionare **Aggiungi** in **condizione**.

   [![Aggiungi riquadro avvisi](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Configurare quindi le condizioni esatte per la logica del segnale.

   [![Configurare la logica dei segnali](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Da qui è possibile configurare gli avvisi usando alcune delle condizioni seguenti:

   |Metrica  |Descrizione  |
   |---------|---------|
   |**Ingress Received Bytes** (Byte ricevuti in ingresso)     | Numero di byte non elaborati letti dalle origini eventi. Include in genere il nome e il valore delle proprietà.  |  
   |**Ingress Received Invalid Messages** (Messaggi non validi ricevuti in ingresso)     | Numero dei messaggi non validi letti da tutte le origini eventi di Hub eventi di Azure o Hub IoT di Azure.      |
   |**Ingress Received Messages** (Messaggi ricevuti in ingresso)   | Numero dei messaggi letti da tutte le origini eventi di hub eventi o hub IoT.        |
   |**Ingress Stored Bytes** (Byte archiviati in ingresso)     | Dimensioni totali degli eventi archiviati e disponibili per le query. Le dimensioni sono calcolate solo sul valore delle proprietà.        |
   |**Eventi archiviati in ingresso**    |   Numero di eventi bidimensionali archiviati e disponibili per le query.      |
   |**Ingress Received Message Time Lag (Tempo di ritardo messaggi ricevuti in ingresso) **    |  Differenza in secondi tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.      |
   |**Ingress Received Message Count Lag (Ritardo numero di messaggi ricevuti in ingresso) **    |  Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza del messaggio elaborato in ingresso.      |

   Selezionare **Operazione completata**.

1. Dopo aver configurato la logica del segnale desiderata, esaminare visivamente la regola di avviso scelta.

   [![Visualizzazione della latenza e creazione di grafici](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Limitazione e gestione in ingresso

* Se si sta eseguendo la limitazione, viene registrato un valore per l'intervallo di *tempo dei messaggi ricevuti in ingresso* che informa dell'utente circa il numero di secondi di ritardo rispetto all'ambiente Time Series Insights dal momento effettivo in cui il messaggio raggiunge l'origine evento, escluso il tempo di indicizzazione di appx. 30-60 secondi.  

  Anche per *Ingress Received Message Count Lag* (Differenza numero messaggi ricevuti in ingresso) deve essere disponibile un valore, che consente di determinare di quanti messaggi si è in ritardo.  Il modo più semplice per mettersi in pari consiste nell'aumentare la capacità dell'ambiente fino a dimensioni che consentono di recuperare la differenza.  

  Se, ad esempio, l'ambiente S1 sta dimostrando un ritardo di 5 milioni messaggi, è possibile aumentare le dimensioni dell'ambiente a sei unità per circa un giorno per poter essere rilevati.  È possibile aumentare ulteriormente per recuperare più velocemente. Il periodo di recupero è una situazione comune durante il provisioning iniziale di un ambiente, in particolare quando lo si connette a un'origine eventi che già include eventi oppure quando si esegue un caricamento in blocco di molti dati cronologici.

* Un'altra tecnica consiste nell'impostare un avviso **Ingress Stored Events** (Eventi archiviati in ingresso) per un valore maggiore o uguale a una soglia leggermente inferiore alla capacità totale dell'ambiente per un periodo di 2 ore.  Questo avviso consentirà di comprendere se si raggiunge costantemente la capacità e la probabilità di latenza è quindi elevata. 

  Ad esempio, se è stato effettuato il provisioning di tre unità S1, con capacità in ingresso di 2100 eventi al minuto, è possibile impostare un avviso **Ingress Stored Events** (Eventi archiviati in ingresso) per un valore maggiore o uguale a 1900 eventi per 2 ore. Se si supera costantemente questa soglia attivando così l'avviso, è probabile che il provisioning sia insufficiente.  

* Se si ritiene che sia stata applicata la limitazione, è possibile confrontare i **messaggi ricevuti in ingresso** con i messaggi uscita dell'origine evento.  Se l'ingresso nell'hub eventi è superiore a **Ingress Received Messages** (Messaggi ricevuti in ingresso), è probabile che a Time Series Insights venga applicata la limitazione.

## <a name="improving-performance"></a>Miglioramento delle prestazioni

Per ridurre la limitazione o i casi di latenza, la correzione migliore consiste nell'aumentare la capacità dell'ambiente.

È possibile evitare la latenza o la limitazione configurando l'ambiente in modo appropriato per la quantità di dati che si vuole analizzare. Per altre informazioni su come aggiungere capacità all'ambiente, vedere [ridimensionare l'ambiente](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni su come [diagnosticare e risolvere i problemi nell'ambiente Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).

- Informazioni [su come ridimensionare l'ambiente di Time Series Insights](time-series-insights-how-to-scale-your-environment.md).