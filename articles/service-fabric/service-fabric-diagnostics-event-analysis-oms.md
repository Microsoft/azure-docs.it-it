---
title: Analisi degli eventi di Azure Service Fabric con i log di monitoraggio di Azure
description: Informazioni sulla visualizzazione e l'analisi degli eventi usando i log di monitoraggio di Azure per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: f1e22213c857b400cc36c51cefb90e2379352893
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628969"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Analisi e visualizzazione di eventi con i log di monitoraggio di Azure
 I log di Monitoraggio di Azure raccolgono e analizzano i dati telemetrici delle applicazioni e dei servizi ospitati nel cloud e forniscono gli strumenti di analisi per sfruttarne al meglio la disponibilità e le prestazioni. Questo articolo illustra come eseguire query nei log di monitoraggio di Azure per ottenere informazioni dettagliate e risolvere i problemi che si verificano nel cluster. Vengono affrontate le seguenti domande comuni:

* Com'è possibile risolvere i problemi relativi agli eventi di integrità?
* Com'è possibile sapere quando un nodo diventa inattivo?
* Com'è possibile sapere se i servizi dell'applicazione sono stati avviati o arrestati?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Panoramica dell'area di lavoro Log Analytics

>[!NOTE] 
>Mentre l'archiviazione di diagnostica è abilitata per impostazione predefinita al momento della creazione del cluster, è tuttavia necessario configurare l'area di lavoro Log Analytics per la lettura dall'archiviazione di diagnostica.

Log di monitoraggio di Azure raccoglie dati da risorse gestite, tra cui una tabella di archiviazione di Azure o un agente, e li gestisce in un repository centrale. I dati possono essere quindi usati per analisi, avvisi e visualizzazioni o altre esportazioni. Log di monitoraggio di Azure supporta eventi, dati sulle prestazioni o altri dati personalizzati. Esaminare [i passaggi per configurare l'estensione di diagnostica per aggregare gli eventi e i](service-fabric-diagnostics-event-aggregation-wad.md) [passaggi per creare un'area di lavoro log Analytics per leggere gli eventi nell'archiviazione](service-fabric-diagnostics-oms-setup.md) per assicurarsi che i dati vengano propagati nei log di monitoraggio di Azure.

Quando i dati vengono ricevuti dai log di monitoraggio di Azure, Azure offre diverse *soluzioni di monitoraggio* che sono soluzioni preconfezionate o Dashboard operativi per monitorare i dati in ingresso, personalizzati in diversi scenari. Sono inclusi una soluzione di *Analisi Service Fabric* e una soluzione *contenitori*, le due soluzioni di diagnostica e monitoraggio più importanti se si usano i cluster di Service Fabric. In questo articolo viene descritto come usare la soluzione Analisi Service Fabric, che viene creata con l'area di lavoro.

## <a name="access-the-service-fabric-analytics-solution"></a>Accedere alla soluzione Analisi Service Fabric

Nel [portale di Azure](https://portal.azure.com)passare al gruppo di risorse in cui è stata creata la soluzione analisi Service Fabric.

Selezionare la risorsa **ServiceFabric \<nameOfOMSWorkspace\>**.

In `Summary` vengono visualizzati riquadri sotto forma di grafo per ogni soluzione abilitata, tra cui uno per Service Fabric. Fare clic sul grafo **Service Fabric** per passare alla soluzione Analisi Service Fabric.

![Soluzione Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

L'immagine seguente mostra la home page della soluzione Analisi Service Fabric. La home page fornisce una visualizzazione di riepilogo delle operazioni eseguite nel cluster.

![Screenshot che mostra il home page della soluzione Analisi Service Fabric.](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Se è stata abilitata la diagnostica al momento della creazione del cluster, è possibile visualizzare gli eventi per 

* [Eventi del cluster di Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Eventi del modello di programmazione Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventi relativi al modello di programmazione Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Oltre agli eventi predefiniti di Service Fabric, è possibile raccogliere eventi di sistema più dettagliati [aggiornando la configurazione dell'estensione di diagnostica](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Visualizzare gli eventi di Service Fabric che includono azioni sui nodi

Nella pagina Analisi Service Fabric fare clic sul grafo di **Eventi di Service Fabric**.

![Canale operativo della soluzione Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Fare clic su **Elenco** per visualizzare gli eventi in un elenco. Qui è possibile osservare tutti gli eventi di sistema che sono stati raccolti. Come riferimento, questi elementi provengono da **WADServiceFabricSystemEventsTable** nell'account di Archiviazione di Azure. Analogamente, gli eventi di Reliable Services e Reliable Actors visualizzati accanto provengono dalle rispettive tabelle.
    
![Canale operativo della query](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

In alternativa, è possibile fare clic sulla lente di ingrandimento a sinistra e usare il linguaggio di query Kusto per trovare gli elementi desiderati. Ad esempio, per trovare tutte le azioni eseguite sui nodi nel cluster, è possibile usare la query seguente. Gli ID evento usati di seguito sono disponibili nelle [informazioni di riferimento sugli eventi del canale operativo](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

È possibile eseguire query su molti più campi, ad esempio nodi specifici (Computer) o il servizio di sistema (TaskName).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Visualizzare gli eventi di Reliable Services e Reliable Actors di Service Fabric

Nella pagina Analisi Service Fabric fare clic sul grafo di **Reliable Services**.

![Reliable Services della soluzione Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Fare clic su **Elenco** per visualizzare gli eventi in un elenco. Qui è possibile visualizzare gli eventi relativi a Reliable Services. È possibile visualizzare eventi diversi per l'avvio e il completamento del servizio runasync, come in genere accade durante le distribuzioni e gli aggiornamenti. 

![Reliable Services della query](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Gli eventi relativi a Reliable Actors possono essere visualizzati in modo simile. Per configurare eventi più dettagliati per Reliable Actors, è necessario modificare `scheduledTransferKeywordFilter` nel file di configurazione per l'estensione di diagnostica (illustrato di seguito). I dettagli sui valori per questi elementi sono disponibili nelle [informazioni di riferimento sugli eventi di Reliable Actors](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Il linguaggio di query Kusto è potente. Un'altra query importante che è possibile eseguire consente di trovare i nodi che generano il maggior numero di eventi. La query nello screenshot seguente illustra gli eventi operativi di Service Fabric aggregati con il servizio e il nodo specificati.

![Eventi della query per nodo](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Passaggi successivi

* Per abilitare il monitoraggio dell'infrastruttura, ovvero i contatori delle prestazioni, vedere come [aggiungere l'agente di Log Analytics](service-fabric-diagnostics-oms-agent.md). L'agente raccoglie i contatori delle prestazioni e li aggiunge all'area di lavoro esistente.
* Per i cluster locali, log di monitoraggio di Azure offre un gateway (proxy di inoltro HTTP) che può essere usato per inviare dati ai log di monitoraggio di Azure. Per altre informazioni, vedere [connessione di computer senza accesso a Internet ai log di monitoraggio di Azure tramite il gateway log Analytics](../azure-monitor/agents/gateway.md).
* Configurare gli [avvisi automatizzati](../azure-monitor/alerts/alerts-overview.md) in modo da semplificare il rilevamento e la diagnostica.
* Acquisire familiarità con le funzionalità di [ricerca log ed esecuzione di query](../azure-monitor/logs/log-query-overview.md) incluse nei log di Monitoraggio di Azure.
* Ottenere una panoramica più dettagliata dei log di monitoraggio di Azure e delle relative offerte, vedere informazioni sui [log di monitoraggio di Azure](../azure-monitor/overview.md).
