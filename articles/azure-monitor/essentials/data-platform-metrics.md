---
title: Metriche in Monitoraggio di Azure | Microsoft Docs
description: Descrive le metriche in Monitoraggio di Azure, che sono dati di monitoraggio leggeri in grado di supportare scenari near real-time.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/20/2021
ms.author: bwren
ms.openlocfilehash: 3c99002a4f8613ff40a116eeceded4b3bada1c15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936156"
---
# <a name="azure-monitor-metrics-overview"></a>Panoramica delle metriche di monitoraggio di Azure
Le metriche di monitoraggio di Azure sono una funzionalità di monitoraggio di Azure che raccoglie dati numerici dalle [risorse monitorate](../monitor-reference.md) in un database di serie temporali. Le metriche sono valori numerici raccolti a intervalli regolari e descrivono alcuni aspetti di un sistema in un determinato momento. Le metriche in Monitoraggio di Azure sono elementi leggeri e in grado di supportare scenari near real-time, rendendole particolarmente utili per la generazione di avvisi e il rilevamento rapido dei problemi. È possibile analizzarli in modo interattivo con Esplora metriche, ricevere notifiche in modo proattivo con un avviso quando un valore supera una soglia o visualizzarli in una cartella di lavoro o in un dashboard.


> [!NOTE]
> Le metriche di monitoraggio di Azure sono una metà della piattaforma dati che supporta monitoraggio di Azure. L'altro è [log di monitoraggio di Azure](../logs/data-platform-logs.md) che raccoglie e organizza i dati di log e delle prestazioni e consente di analizzarli con un linguaggio di query avanzato. Le metriche sono più leggere dei dati nei log di monitoraggio di Azure e sono in grado di supportare scenari quasi in tempo reale, rendendoli particolarmente utili per gli avvisi e il rilevamento rapido dei problemi. Le metriche possono tuttavia archiviare solo dati numerici in una determinata struttura, mentre i log possono archiviare una varietà di tipi di dati diversi ognuno con la propria struttura. È anche possibile eseguire un'analisi complessa sui dati dei log usando query di log che non possono essere usati per l'analisi dei dati di metrica.


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Che cosa è possibile fare con le metriche di Monitoraggio di Azure?
La tabella seguente elenca i diversi modi in cui è possibile usare le metriche in monitoraggio di Azure.

|  | Descrizione |
|:---|:---|
| **Analisi** | Usare [Esplora metriche](metrics-charts.md) per analizzare le metriche raccolte in un grafico e confrontare le metriche di diverse risorse. |
| **Avviso** | Configurazione di una [regola di avviso per la metrica](../alerts/alerts-metric.md) che invia una notifica o esegue un'[operazione automatica](../alerts/action-groups.md) quando il valore della metrica supera una soglia. |
| **Visualizzazione** | Aggiungere un grafico di Esplora metriche in un [dashboard di Azure](../app/tutorial-app-dashboards.md).<br>Creare una[ cartella di lavoro](../visualize/workbooks-overview.md) per combinare più set di dati in un report interattivo. Esportare i risultati di una query in [Grafana](../visualize/grafana-plugin.md) per sfruttarne le capacità di creazione dashboard e combinare i risultati con altre origini dati. |
| **Automatizzare** |  Uso della [scalabilità automatica](../autoscale/autoscale-overview.md) per aumentare o ridurre le risorse in base a un valore della metrica che supera una soglia. |
| **Recupero** | Accedere ai valori della metrica da una riga di comando mediante i [cmdlet di PowerShell](/powershell/module/az.monitor)<br>Accedere ai valori della metrica da un'applicazione personalizzata mediante [API REST](./rest-api-walkthrough.md).<br>Accedere ai valori della metrica da una riga di comando mediante l'[interfaccia della riga di comando](/cli/azure/monitor/metrics). |
| **Export** | [Instradare le metriche ai log](./resource-logs.md#send-to-azure-storage) per analizzare i dati delle metriche di Monitoraggio di Azure insieme ai dati dei log di Monitoraggio di Azure e archiviare i valori delle metriche per più di 93 giorni.<br>Trasmettere le metriche a un [hub eventi](./stream-monitoring-data-event-hubs.md) per instradarle a sistemi esterni. |
| **Archiviazione** | [Archiviare](./platform-logs-overview.md) la cronologia relativa alle prestazioni o all'integrità della risorsa a scopi di conformità, verifica o creazione di report offline. |

![Panoramica delle metriche](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>Raccolta dati
Sono disponibili tre origini principali delle metriche raccolte da Monitoraggio di Azure. Una volta raccolte nel database delle metriche di Monitoraggio di Azure, queste metriche possono essere valutate insieme indipendentemente dalla rispettiva origine.

**Risorse di Azure**. Le metriche della piattaforma vengono create da risorse di Azure e offrono visibilità riguardo a integrità e prestazioni. Ogni tipo di risorsa crea un [set distinto di metriche](./metrics-supported.md) senza che sia necessaria alcuna configurazione. Le metriche della piattaforma vengono raccolte dalle risorse di Azure a una frequenza di un minuto, se non specificato diversamente nella definizione della metrica. 

**Applicazioni**. Le metriche vengono create da Application Insights per le applicazioni monitorate e consentono di rilevare i problemi di prestazioni e tenere traccia delle tendenze di utilizzo dell'applicazione. Sono inclusi valori come il _tempo di risposta del server_ e le _eccezioni del browser_.

**Agenti macchina virtuale**. Le metriche vengono raccolte dal sistema operativo guest di una macchina virtuale. Abilitare le metriche del sistema operativo guest per le macchine virtuali Windows con l'[estensione di diagnostica Windows](../agents/diagnostics-extension-overview.md) e per le macchine virtuali Linux con l'[agente InfluxData Telegraf](https://www.influxdata.com/time-series-platform/telegraf/).

**Metriche personalizzate**. È possibile definire le metriche oltre alle metriche standard disponibili automaticamente. È possibile [definire le metriche personalizzate nell'applicazione](../app/api-custom-events-metrics.md) che viene monitorata da Application Insights o creare metriche personalizzate per un servizio di Azure usando l'[API per le metriche personalizzate](./metrics-store-custom-rest-api.md).

- Vedere [che cosa viene monitorato da monitoraggio di Azure?](../monitor-reference.md) per un elenco completo delle origini dati che possono inviare dati alle metriche di monitoraggio di Azure.

## <a name="metrics-explorer"></a>Esplora metriche
È possibile usare [Esplora metriche](metrics-charts.md) per analizzare in modo interattivo i dati nel database delle metriche e tracciare un grafico dei valori di più metriche nel tempo. È possibile aggiungere i grafici a un dashboard per visualizzarli con altre visualizzazioni. È anche possibile recuperare le metriche usando l'[API REST di monitoraggio di Azure](./rest-api-walkthrough.md).

![Esplora metriche](media/data-platform-metrics/metrics-explorer.png)

- Per iniziare a usare Esplora metriche, vedere [Introduzione a Esplora metriche di monitoraggio di Azure](./metrics-getting-started.md) .

## <a name="data-structure"></a>Struttura dei dati
I dati raccolti dalle metriche di Monitoraggio di Azure vengono archiviati in un database di serie temporali ottimizzato per l'analisi dei dati con timestamp. Ogni set di valori della metrica è una serie temporale con le proprietà seguenti:

* Ora in cui è stato raccolto il valore
* Risorsa cui è associato il valore
* Spazio dei nomi che funge da categoria per la metrica
* Nome della metrica
* Valore stesso
* Alcune metriche possono avere più dimensioni, come descritto in [Metriche multidimensionali](#multi-dimensional-metrics). Le metriche personalizzate possono avere fino a 10 dimensioni.

## <a name="multi-dimensional-metrics"></a>Metriche multidimensionali
Una delle difficoltà per i dati delle metriche è che spesso contengono informazioni limitate per fornire il contesto dei valori raccolti. Monitoraggio di Azure risolve questo problema con le metriche multidimensionali. Le dimensioni di una metrica sono coppie nome/valore contenenti dati aggiuntivi per descrivere il valore della metrica. Ad esempio, la metrica _Spazio su disco disponibile_ può avere una dimensione denominata _Unità_ con i valori _C:_ e _D:_ , che permettono la visualizzazione dello spazio su disco disponibile in tutte le unità o per ogni unità singolarmente.

L'esempio seguente mostra due set di dati per un'ipotetica metrica denominata _Velocità effettiva di rete_. Il primo set di dati non ha dimensioni. Il secondo mostra i valori con due dimensioni, _Indirizzo IP_ e _Direzione_:

### <a name="network-throughput"></a>Velocità effettiva della rete

| Timestamp     | Valore della metrica |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1\.331,8 Kbps |
| 8/9/2017 8:15 | 1\.141,4 Kbps |
| 8/9/2017 8:16 | 1\.110,2 Kbps |

Questa metrica non dimensionale può solo rispondere a una domanda di base come "Qual è la velocità effettiva della rete in un determinato momento?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Velocità effettiva della rete + due dimensioni ("IP" e "Direzione")

| Timestamp     | Dimensione "IP"   | Dimensione "Direzione" | Valore della metrica|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direzione = "Invio"    | 646,5 Kbps |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direzione = "Ricezione" | 420,1 Kbps |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direzione = "Invio"    | 150,0 Kbps |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direzione = "Ricezione" | 115,2 Kbps |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direzione = "Invio"    | 515,2 Kbps |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direzione = "Ricezione" | 371,1 Kbps |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direzione = "Invio"    | 155,0 Kbps |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direzione = "Ricezione" | 100,1 Kbps |

Questa metrica può rispondere a domande del tipo "Qual è la velocità effettiva di rete per ogni indirizzo IP?" e "Quanti dati sono stati inviati rispetto a quelli ricevuti?" Le metriche multidimensionali presentano valori analitici e diagnostici aggiuntivi rispetto alle metriche dimensionali.

## <a name="retention-of-metrics"></a>Conservazione delle metriche
Per la maggior parte delle risorse in Azure, le metriche vengono archiviate per 93 giorni. Esistono alcune eccezioni:

**Metriche del sistema operativo guest**
-   **Metriche del sistema operativo guest classiche**. Si tratta di contatori delle prestazioni raccolti dall'[estensione di diagnostica Windows](../agents/diagnostics-extension-overview.md) o dall'[estensione di diagnostica Linux](../../virtual-machines/extensions/diagnostics-linux.md) e indirizzati a un account di archiviazione di Azure. La conservazione per queste metriche è garantita almeno 14 giorni, anche se non viene scritta alcuna data di scadenza effettiva nell'account di archiviazione. Per motivi di prestazioni, il portale limita la quantità di dati visualizzati in base al volume. Il numero effettivo di giorni recuperati dal portale può pertanto essere superiore a 14 giorni se il volume di dati scritti non è molto grande.  
-   **Metriche del sistema operativo guest inviate alle metriche di Monitoraggio di Azure**. Si tratta di contatori delle prestazioni raccolti dall'[estensione di diagnostica Windows](../agents/diagnostics-extension-overview.md) e inviati al [sink di dati Monitoraggio di Azure](../agents/diagnostics-extension-overview.md#data-destinations) o tramite l'[agente InfluxData Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) nei computer Linux. La conservazione di queste metriche è di 93 giorni.
-   **Metriche del sistema operativo guest raccolte dall'agente di Log Analytics**. Si tratta di contatori delle prestazioni raccolti dall'agente di Log Analytics e inviati a un'area di lavoro Log Analytics. La conservazione di queste metriche è di 31 giorni e può essere estesa fino a 2 anni.

**Metriche basate su log di Application Insights**. 
- In background, le [metriche basate su log](../app/pre-aggregated-metrics-log-metrics.md) vengono convertite in query di log. La conservazione corrisponde alla conservazione degli eventi nei log sottostanti. Per le risorse di Application Insights i log vengono archiviati per 90 giorni.


> [!NOTE]
> È possibile [inviare le metriche della piattaforma per le risorse di Monitoraggio di Azure a un'area di lavoro Log Analytics](./resource-logs.md#send-to-azure-storage) per l'identificazione delle tendenze a lungo termine.





## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [piattaforma dati Monitoraggio di Azure](../data-platform.md).
- Informazioni sui [dati di log in Monitoraggio di Azure](../logs/data-platform-logs.md).
- Informazioni sui [dati di monitoraggio disponibili](../agents/data-sources.md) per diverse risorse in Azure.