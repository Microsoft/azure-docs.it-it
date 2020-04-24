---
title: Monitorare le risorse di Azure con monitoraggio di Azure | Microsoft Docs
description: Viene descritto come raccogliere e analizzare i dati di monitoraggio dalle risorse in Azure tramite monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249269"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitoraggio delle risorse di Azure con monitoraggio di Azure
Quando le applicazioni e i processi aziendali critici si basano sulle risorse di Azure, è opportuno monitorarle per la disponibilità, le prestazioni e il funzionamento. Questo articolo descrive i dati di monitoraggio generati dalle risorse di Azure e come è possibile usare le funzionalità di monitoraggio di Azure per analizzare e generare avvisi su questi dati.

> [!IMPORTANT]
> Questo articolo si applica a tutti i servizi di Azure che usano monitoraggio di Azure. Le risorse di calcolo, incluse le VM e il servizio app, generano gli stessi dati di monitoraggio descritti qui, ma dispongono anche di un sistema operativo guest che può generare anche log e metriche. Per informazioni dettagliate su come raccogliere e analizzare i dati, vedere la documentazione di monitoraggio per questi servizi.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
Monitoraggio di Azure è un servizio di monitoraggio completo dello stack in Azure che offre un set completo di funzionalità per il monitoraggio delle risorse di Azure, oltre alle risorse in altri cloud e in locale. La [piattaforma dati di monitoraggio di Azure](../platform/data-platform.md) raccoglie dati in [log](../platform/data-platform-logs.md) e [metriche](../platform/data-platform-metrics.md) in cui possono essere analizzati insieme usando un set completo di strumenti di monitoraggio, come descritto nelle sezioni seguenti.

- [Che cosa è possibile fare con le metriche di monitoraggio di Azure?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Che cosa è possibile fare con i log di monitoraggio di Azure?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Non appena si crea una risorsa di Azure, monitoraggio di Azure viene abilitato e inizia a raccogliere le metriche e i log attività che è possibile [visualizzare e analizzare nella portale di Azure](#monitoring-in-the-azure-portal). Con alcune configurazioni è possibile raccogliere dati di monitoraggio aggiuntivi e abilitare funzionalità aggiuntive. Per informazioni dettagliate sui requisiti di configurazione, vedere [monitoraggio dei dati](#monitoring-data) di seguito.


## <a name="costs-associated-with-monitoring"></a>Costi associati al monitoraggio
Non sono previsti costi per l'analisi dei dati di monitoraggio raccolti per impostazione predefinita. Sono inclusi gli elementi seguenti:

- Raccolta delle metriche della piattaforma e relativa analisi con Esplora metriche.
- Raccolta del log attività e relativa analisi nella portale di Azure.
- Creazione di una regola di avviso del log attività.

Non sono previsti costi di monitoraggio di Azure per la raccolta e l'esportazione di log e metriche, ma potrebbero esserci costi correlati associati alla destinazione:

- Costi associati all'inserimento e alla conservazione dei dati quando si raccolgono log e metriche nell'area di lavoro Log Analytics. [Per log Analytics, vedere prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).
- Costi associati all'archiviazione dei dati quando si raccolgono log e metriche in un account di archiviazione di Azure. Vedere [prezzi di archiviazione di Azure per l'archiviazione BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Costi associati al flusso di hub eventi quando si inviano log e metriche a hub eventi di Azure. Vedere [prezzi di hub eventi di Azure](https://azure.microsoft.com/pricing/details/event-hubs/).

È possibile che i costi di monitoraggio di Azure siano associati a quanto segue. Vedere i [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/):

- Esecuzione di una query di log.
- Creazione di una regola di avviso per una metrica o una query di log.
- Invio di una notifica da qualsiasi regola di avviso.
- Accesso alle metriche tramite l'API.

## <a name="monitoring-data"></a>Dati di monitoraggio
Le risorse in Azure generano [log](../platform/data-platform-logs.md) e [metriche](../platform/data-platform-metrics.md) illustrate nel diagramma seguente. Fare riferimento alla documentazione per ogni servizio di Azure per i dati specifici che generano ed eventuali soluzioni o informazioni aggiuntive fornite.

![Panoramica](media/monitor-azure-resource/logs-metrics.png)



- [Metriche della piattaforma](../platform/data-platform-metrics.md) : valori numerici che vengono raccolti automaticamente a intervalli regolari e descrivono un aspetto di una risorsa in un determinato momento. 
- [Log delle risorse](../platform/platform-logs-overview.md) : forniscono informazioni approfondite sulle operazioni eseguite all'interno di una risorsa di Azure (il piano dati), ad esempio il recupero di un segreto da un Key Vault o l'esecuzione di una richiesta a un database. Il contenuto e la struttura dei log delle risorse variano in base al servizio di Azure e al tipo di risorsa.
- [Log attività](../platform/platform-logs-overview.md) : fornisce informazioni approfondite sulle operazioni su ogni risorsa di Azure nella sottoscrizione dall'esterno (il piano di gestione), ad esempio la creazione di una nuova risorsa o l'avvio di una macchina virtuale. Si tratta di informazioni su cosa, chi e quando per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione.


## <a name="configuration-requirements"></a>Requisiti di configurazione

### <a name="configure-monitoring"></a>Configurare il monitoraggio
Alcuni dati di monitoraggio vengono raccolti automaticamente, ma potrebbe essere necessario eseguire alcune configurazioni a seconda delle esigenze. Per informazioni specifiche su ogni tipo di dati di monitoraggio, vedere le informazioni riportate di seguito.

- [Metriche della piattaforma](../platform/data-platform-metrics.md) : le metriche della piattaforma vengono raccolte automaticamente nelle [metriche di monitoraggio di Azure](../platform/data-platform-metrics.md) senza che sia necessaria alcuna configurazione. Creare un'impostazione di diagnostica per inviare le voci ai log di monitoraggio di Azure o inoltrarli all'esterno di Azure.
- [Log delle](../platform/platform-logs-overview.md) risorse: i log delle risorse vengono generati automaticamente dalle risorse di Azure, ma non vengono raccolti senza un'impostazione di diagnostica.  Creare un'impostazione di diagnostica per inviare le voci ai log di monitoraggio di Azure o inoltrarli all'esterno di Azure.
- [Log attività](../platform/platform-logs-overview.md) : il log attività viene raccolto automaticamente senza alcuna configurazione necessaria e può essere visualizzato nella portale di Azure. Creare un'impostazione di diagnostica per copiarla nei log di monitoraggio di Azure o per inviarli all'esterno di Azure.

### <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
Per raccogliere dati nei log di monitoraggio di Azure è necessaria un'area di lavoro Log Analytics. È possibile avviare il monitoraggio rapido del servizio creando una nuova area di lavoro, ma può essere utile usare un'area di lavoro che raccoglie dati da altri servizi. Per informazioni dettagliate sulla creazione di un'area di lavoro e sulla [progettazione della distribuzione dei log di monitoraggio di Azure](../platform/design-logs-deployment.md) , vedere [creare un'area di lavoro log Analytics nel portale di Azure](../learn/quick-create-workspace.md) . Se si usa un'area di lavoro esistente nell'organizzazione, sarà necessario disporre delle autorizzazioni appropriate, come descritto in [gestire l'accesso ai dati e alle aree di lavoro di log in monitoraggio di Azure](../platform/manage-access.md). 





## <a name="diagnostic-settings"></a>Impostazioni di diagnostica
Le impostazioni di diagnostica definiscono dove devono essere inviati i log delle risorse e le metriche per una determinata risorsa. Le destinazioni possibili sono:

- [Log Analytics area di lavoro](../platform/resource-logs-collect-workspace.md) che consente di analizzare i dati con altri dati di monitoraggio raccolti da monitoraggio di Azure con potenti query di log e anche per sfruttare altre funzionalità di monitoraggio di Azure, ad esempio avvisi e visualizzazioni del log. 
- [Hub eventi](../platform/resource-logs-stream-event-hubs.md) per trasmettere i dati a sistemi esterni, ad esempio Siem di terze parti e altre soluzioni di log Analytics. 
- [Account di archiviazione di Azure](../platform/resource-logs-collect-storage.md) , utile per il controllo, l'analisi statica o il backup.

Seguire la procedura descritta in [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](../platform/diagnostic-settings.md) per creare e gestire le impostazioni di diagnostica tramite il portale di Azure. Vedere [creare un'impostazione di diagnostica in Azure usando un modello di gestione risorse](../platform/diagnostic-settings-template.md) per definirli in un modello e abilitare il monitoraggio completo per una risorsa al momento della creazione.


## <a name="monitoring-in-the-azure-portal"></a>Monitoraggio nel portale di Azure
 È possibile accedere ai dati di monitoraggio per la maggior parte delle risorse di Azure dal menu della risorsa nel portale di Azure. In questo modo sarà possibile accedere ai dati di una singola risorsa usando gli strumenti standard di monitoraggio di Azure. Alcuni servizi di Azure forniranno opzioni diverse, quindi è necessario fare riferimento alla documentazione per tale servizio per altre informazioni. Usare il menu **monitoraggio di Azure** per analizzare i dati di tutte le risorse monitorate. 

### <a name="overview"></a>Panoramica
Molti servizi includeranno i dati di monitoraggio nella pagina **Panoramica** come riepilogo rapido del proprio funzionamento. Questo riepilogo sarà in genere basato su un sottoinsieme delle metriche della piattaforma archiviate nelle metriche di Monitoraggio di Azure. Altre opzioni di monitoraggio saranno in genere disponibili in una sezione di **monitoraggio** dei servizi. .

![Pagina di panoramica](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni 
Alcuni servizi forniranno strumenti oltre le funzionalità standard di monitoraggio di Azure. [Insights](../insights/insights-overview.md) offre un'esperienza di monitoraggio personalizzata basata sulla piattaforma dati di monitoraggio di Azure e sulle funzionalità standard. Le [soluzioni](../insights/solutions.md) forniscono la logica di monitoraggio predefinita basata sui log di monitoraggio di Azure. 

Se un servizio ha una visione di monitoraggio di Azure, è possibile accedervi dal **monitoraggio** nel menu di ogni risorsa. Accedere a tutte le informazioni e alle soluzioni dal menu **monitoraggio di Azure** .

![Informazioni dettagliate](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Metriche
Analizzare le metriche nella portale di Azure usando [Esplora metriche](../platform/metrics-getting-started.md) , disponibile dalla voce di menu **metrica** per la maggior parte dei servizi. Questo strumento consente di usare singole metriche o combinare più per identificare le correlazioni e le tendenze. 

- Per le nozioni di base sull'uso di Esplora metriche, vedere [Introduzione ad Azure Esplora metriche](../platform/metrics-getting-started.md) .
- Vedere [funzionalità avanzate di Azure Esplora metriche](../platform/metrics-charts.md) per le funzionalità avanzate di Esplora metriche, ad esempio l'uso di più metriche e l'applicazione di filtri e suddivisione.

![Metriche](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Log attività 
Consente di visualizzare le voci nel log attività del portale di Azure con il filtro iniziale impostato sulla risorsa corrente. Copiare il log attività in un'area di lavoro di Log Analytics per accedervi per usarlo nelle query e nelle cartelle di lavoro di log. 

- Vedere [visualizzare e recuperare gli eventi del log attività di Azure](../platform/activity-log-view.md) per informazioni dettagliate sulla visualizzazione del log attività e il recupero di voci con diversi metodi.
- Vedere la documentazione per il servizio di Azure per gli eventi specifici che vengono registrati.

![Log attività](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure
I log di monitoraggio di Azure consolidano i log e le metriche da più servizi e altre origini dati per l'analisi con uno strumento di query potente. Come descritto in precedenza, creare un'impostazione di diagnostica per raccogliere metriche della piattaforma, log attività e log delle risorse in un'area di lavoro Log Analytics in monitoraggio di Azure.

[Log Analytics](../log-query/get-started-portal.md) consente di lavorare con le [query di log](../log-query/log-query-overview.md), una funzionalità potente di monitoraggio di Azure che consente di eseguire analisi avanzate dei dati di log usando un linguaggio di query con funzionalità complete. Aprire Log Analytics dai **log** nel menu **monitoraggio** per una risorsa di Azure per usare le query di log usando la risorsa come [ambito della query](../log-query/scope.md#query-scope). In questo modo è possibile analizzare i dati in più tabelle solo per tale risorsa. Usare i **log** dal menu monitoraggio di Azure per accedere ai log di tutte le risorse. 

- Per un'esercitazione sull'uso del linguaggio di query usato per scrivere query di log, vedere [Introduzione alle query di log in monitoraggio di Azure](../log-query/get-started-queries.md) .
- Vedere [raccogliere i log delle risorse di Azure nell'area di lavoro log Analytics in monitoraggio di Azure](../platform/resource-logs-collect-workspace.md) per informazioni su come vengono raccolti i log delle risorse nei log di monitoraggio di Azure e informazioni dettagliate su come accedervi in una query.
- Vedere [modalità di raccolta](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode) per una spiegazione del modo in cui i dati del log delle risorse sono strutturati nei log di monitoraggio di Azure.
- Per informazioni dettagliate sulla tabella nei log di monitoraggio di Azure, vedere la documentazione per ogni servizio di Azure.

![Log](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitoraggio dalla riga di comando
È possibile accedere ai dati di monitoraggio raccolti dalla risorsa da una riga di comando o includere in uno script usando [Azure PowerShell](/powershell/azure/) o l' [interfaccia della riga di comando di Azure](/cli/azure/). 

- Vedere informazioni di [riferimento sulle metriche CLI](/cli/azure/monitor/metrics) per accedere ai dati delle metriche dall'interfaccia della riga di comando.
- Vedere l' [log Analytics di riferimento CLI](/cli/azure/ext/log-analytics/monitor/log-analytics) per accedere a monitoraggio di Azure registra i dati usando una query di log dall'interfaccia della riga di comando.
- Per accedere ai dati delle metriche da Azure PowerShell, vedere [Azure PowerShell riferimento sulle metriche](/powershell/module/azurerm.insights/get-azurermmetric) .
- Vedere [Azure PowerShell riferimento alla query di log](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) per accedere a monitoraggio di Azure registra i dati usando una query di log da Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitoraggio dall'API REST
Includere i dati di monitoraggio raccolti dalla risorsa in un'applicazione personalizzata usando un'API REST.

- Per informazioni dettagliate sull'accesso alle metriche dall'API REST di monitoraggio di Azure, vedere [procedura dettagliata sull'API REST di monitoraggio di Azure](../platform/rest-api-walkthrough.md) .
- Vedere [API REST di azure log Analytics](https://dev.loganalytics.io/) per informazioni sull'accesso ai dati di log di monitoraggio di Azure tramite una query di Log da Azure PowerShell.

## <a name="alerts"></a>Avvisi
Gli [avvisi](../platform/alerts-overview.md) inviano notifiche in modo proattivo e potenzialmente intervenire quando si trovano condizioni importanti nei dati di monitoraggio. Si crea una regola di avviso che definisce una destinazione per l'avviso, le condizioni per stabilire se creare un avviso e le azioni da intraprendere in risposta.

Per diversi tipi di regole di avviso vengono usati tipi diversi di dati di monitoraggio.

- [Avviso del log attività](../platform/alerts-activity-log.md) : creare un avviso quando viene creata una voce nel log attività che corrisponde a criteri specifici. Questo consente di ricevere una notifica ad esempio quando viene creato un particolare tipo di risorsa o se una modifica della configurazione ha esito negativo.
- [Avviso metrica](../platform/alerts-metric.md) : creare un avviso quando un valore della metrica supera una determinata soglia. Gli avvisi delle metriche sono più reattivi rispetto ad altri avvisi e possono essere risolti automaticamente quando il problema viene corretto.
- [Avviso query log](../platform/alerts-log.md) : esegue una query di log a intervalli regolari e crea un avviso se viene trovata una determinata condizione. In questo modo è possibile eseguire analisi complesse su più set di dati e.

Usare gli **avvisi** dal menu di una risorsa per visualizzare gli avvisi e gestire le regole di avviso per la risorsa. Solo gli avvisi del log attività e le metriche usano le singole risorse di Azure come destinazione. Gli avvisi di query di log usano un'area di lavoro Log Analytics come destinazione e sono basati su una query che può accedere a tutti i log archiviati nell'area di lavoro. Usare il menu monitoraggio di Azure per visualizzare e gestire gli avvisi per tutte le risorse e per gestire le regole di avviso per le query di log.

- Per informazioni dettagliate sulla creazione di regole di avviso, vedere gli articoli relativi ai diversi tipi di avviso.
- Per informazioni dettagliate sulla creazione di un gruppo di azioni che consente di gestire le risposte agli avvisi, vedere [creare e gestire gruppi di azioni nella portale di Azure](../platform/action-groups.md) .



## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sui log delle risorse per i diversi servizi di Azure [, vedere servizi, schemi e categorie supportati per i log delle risorse di Azure](../platform/diagnostic-logs-schema.md) .  
