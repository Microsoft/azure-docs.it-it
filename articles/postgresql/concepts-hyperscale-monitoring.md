---
title: Monitorare e ottimizzare l'iperscalabilità (CITUS)-database di Azure per PostgreSQL
description: Questo articolo descrive le funzionalità di monitoraggio e ottimizzazione di database di Azure per PostgreSQL-iperscalabilità (CITUS)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 544f871f62481243cda2409db24b0d067df28c32
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580577"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Monitorare e ottimizzare database di Azure per PostgreSQL-iperscalabilità (CITUS)

Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione in relazione al carico di lavoro. Iperscale (CITUS) fornisce varie opzioni di monitoraggio per fornire informazioni sul comportamento dei nodi in un gruppo di server.

## <a name="metrics"></a>Metriche

Iperscale (CITUS) fornisce le metriche per ogni nodo in un gruppo di server. Le metriche forniscono informazioni approfondite sul comportamento delle risorse di supporto. Ogni metrica viene emessa con una frequenza di un minuto e ha una cronologia che può arrivare fino a 30 giorni.

Oltre a visualizzare i grafici delle metriche, è possibile configurare gli avvisi. Per indicazioni dettagliate, vedere l'articolo su come [configurare gli avvisi](howto-hyperscale-alert-on-metric.md).  Altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e la cronologia di archiviazione. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../azure-monitor/data-platform.md).

### <a name="list-of-metrics"></a>Elenco delle metriche

Queste metriche sono disponibili per i nodi iperscalari (CITUS):

|Metrica|Nome visualizzato per la metrica|Unità|Descrizione|
|---|---|---|---|
|active_connections|Connessioni attive|Conteggio|Numero di connessioni al server attive.|
|cpu_percent|Percentuale CPU|Percentuale|Percentuale di CPU in uso.|
|iops|IOPS|Conteggio|Vedere la [definizione IOPS](../virtual-machines/premium-storage-performance.md#iops) e la [velocità effettiva di iperscala (CITUS)](concepts-hyperscale-configuration-options.md)|
|memory_percent|Percentuale memoria|Percentuale|Percentuale di memoria in uso.|
|network_bytes_ingress|Rete in ingresso|Byte|Rete in ingresso tra connessioni attive.|
|network_bytes_egress|Rete in uscita|Byte|Rete in uscita tra connessioni attive.|
|storage_percent|Percentuale archiviazione|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Uso archiviazione|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|

Azure non fornisce metriche aggregate per il cluster nel suo complesso, ma le metriche per più nodi possono essere inserite nello stesso grafico.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [come configurare gli avvisi](howto-hyperscale-alert-on-metric.md) per istruzioni sulla creazione di un avviso per una metrica.