---
title: Origini dati di Log Analytics Agent in monitoraggio di Azure
description: Le origini dati definiscono i dati di log che Monitoraggio di Azure raccoglie da agenti e altre origini connesse.  Questo articolo descrive come Monitoraggio di Azure usa le origini dati, illustra i dettagli su come configurarle e fornisce un riepilogo delle diverse origini dati disponibili.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 51cdee9c899feeb003a7d6301d2da0749fad65e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041936"
---
# <a name="log-analytics-agent-data-sources-in-azure-monitor"></a>Origini dati di Log Analytics Agent in monitoraggio di Azure
I dati raccolti da monitoraggio di Azure dalle macchine virtuali con l'agente di [log Analytics](./log-analytics-agent.md) vengono definiti dalle origini dati configurate nell' [area di lavoro log Analytics](../logs/data-platform-logs.md).   Ogni origine dati crea record di un tipo specifico in cui ogni tipo ha un proprio set di proprietà.

> [!IMPORTANT]
> Questo articolo illustra le origini dati per l' [agente di log Analytics](./log-analytics-agent.md) , che è uno degli agenti usati da monitoraggio di Azure. Altri agenti raccolgono dati diversi e sono configurati in modo diverso. Per un elenco degli agenti disponibili e dei dati che è possibile raccogliere, vedere [Panoramica degli agenti di monitoraggio di Azure](agents-overview.md) .

![Raccolta dati di log](media/agent-data-sources/overview.png)

> [!IMPORTANT]
> Le origini dati descritte in questo articolo si applicano solo alle macchine virtuali che eseguono l'agente di Log Analytics. 

## <a name="summary-of-data-sources"></a>Riepilogo delle origini dati
Nella tabella seguente sono elencate le origini dati di Agent attualmente disponibili con l'agente di Log Analytics.  Ogni origine dati ha un collegamento a un articolo distinto che fornisce informazioni dettagliate.   Sono inoltre disponibili informazioni sul metodo e la frequenza della raccolta. 


| Origine dati | Piattaforma | Agente di log Analytics | Agente Operations Manager | Archiviazione di Azure | È necessario Operations Manager? | Dati dell'agente Operations Manager inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Log personalizzati](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | all'arrivo |
| [Log personalizzati](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | all'arrivo |
| [Log IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |dipende dall'impostazione dell'opzione Aggiornamento file di registro |
| [Contatori delle prestazioni](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |come pianificato, almeno 10 secondi |
| [Contatori delle prestazioni](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |come pianificato, almeno 10 secondi |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |dall'Archiviazione di Azure: 10 minuti; dall'agente: all'arrivo |
| [Log eventi di Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | all'arrivo |


## <a name="configuring-data-sources"></a>Configurazione delle origini dati
Per configurare le origini dati per gli agenti Log Analytics, accedere al menu **log Analytics aree di lavoro** del portale di Azure e selezionare un'area di lavoro. Fare clic su **configurazione agenti**. Selezionare la scheda per l'origine dati che si desidera configurare. È possibile seguire i collegamenti nella tabella precedente per la documentazione per ogni origine dati e per informazioni dettagliate sulla relativa configurazione.

Qualsiasi configurazione viene recapitata a tutti gli agenti connessi all'area di lavoro.  Non è possibile escludere gli agenti connessi da questa configurazione.

[![Configurare gli eventi di Windows](media/agent-data-sources/configure-events.png)](media/agent-data-sources/configure-events.png#lightbox)



## <a name="data-collection"></a>Raccolta dati
Le configurazioni dell'origine dati vengono distribuite agli agenti connessi direttamente a Monitoraggio di Azure entro pochi minuti.  I dati specificati vengono raccolti dall'agente e distribuiti direttamente a Monitoraggio di Azure a intervalli specifici per ogni origine dati.  Per informazioni sugli intervalli specifici di ogni origine dati, vedere la documentazione.

Per gli agenti di System Center Operations Manager in un gruppo di gestione connesso, le configurazioni dell'origine dati vengono convertite in Management Pack e recapitate al gruppo di gestione ogni 5 minuti per impostazione predefinita.  L'agente scarica il Management Pack e raccoglie i dati specificati. A seconda dell'origine dati, i dati verranno inviati a un server di gestione che li inoltrerà a Monitoraggio di Azure oppure verranno inviati dall'agente a Monitoraggio di Azure senza passare per il server di gestione. Per informazioni dettagliate, vedere [Informazioni dettagliate sulla raccolta dati per le soluzioni di gestione in Azure](../monitor-reference.md).  È possibile leggere informazioni sulla connessione di Operations Manager e Monitoraggio di Azure e sulla modifica della frequenza a cui tale configurazione viene distribuita nell'articolo relativo alla [configurazione dell'integrazione con System Center Operations Manager](./om-agents.md).

Se l'agente non riesce a connettersi a Monitoraggio di Azure o a Operations Manager, continuerà a raccogliere dati che distribuirà quando stabilirà una connessione.  I dati possono andare persi se la quantità di dati raggiunge la dimensione massima della cache per il client o se l'agente non riesce a stabilire una connessione entro 24 ore.

## <a name="log-records"></a>Record di log
Tutti i dati raccolti da Monitoraggio di Azure vengono archiviati nell'area di lavoro come record.  I record raccolti da diverse origini dati avranno il proprio set di proprietà e verranno identificati dalla proprietà **Type** .  Per informazioni dettagliate su ogni tipo di record, vedere la documentazione per ogni origine dati e soluzione.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [soluzioni di monitoraggio](../insights/solutions.md) che aggiungono funzionalità a Monitoraggio di Azure e raccolgono anche dati nell'area di lavoro.
* Altre informazioni sulle [query di log](../logs/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni di monitoraggio.  
* Configurare gli [avvisi](../alerts/alerts-overview.md) per inviare notifiche immediate sui dati critici raccolti da origini dati e soluzioni di monitoraggio.