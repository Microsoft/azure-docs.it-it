---
title: Monitorare e ottimizzare - Database di Azure per PostgreSQL - Server singolo
description: Questo articolo descrive le funzionalità di monitoraggio e ottimizzazione di database di Azure per PostgreSQL-server singolo.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 8c4479d0892a8a6d5f613eff4592cca93e9fb179
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605128"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Monitorare e ottimizzare Database di Azure per PostgreSQL - Server singolo
Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione in relazione al carico di lavoro. Database di Azure per PostgreSQL offre varie opzioni di monitoraggio che consentono di ottenere informazioni dettagliate sul comportamento del server.

## <a name="metrics"></a>Metriche
Database di Azure per PostgreSQL offre varie metriche che consentono di ottenere informazioni approfondite sul comportamento delle risorse che supportano il server PostgreSQL. Ogni metrica viene emessa a una frequenza di un minuto e presenta fino a [93 giorni di cronologia](../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics). È possibile configurare avvisi in base alle metriche. Per indicazioni dettagliate, vedere l'articolo su come [configurare gli avvisi](howto-alert-on-metric.md). Le altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e l'archiviazione della cronologia. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../azure-monitor/data-platform.md).

### <a name="list-of-metrics"></a>Elenco delle metriche
Per Database di Azure per PostgreSQL sono disponibili le metriche seguenti:

|Metrica|Nome visualizzato per la metrica|Unità|Descrizione|
|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Percentuale di CPU in uso.|
|memory_percent|Percentuale memoria|Percentuale|Percentuale di memoria in uso.|
|io_consumption_percent|Percentuale IO|Percentuale|Percentuale di I/O in uso. (Non applicabile ai server di livello Basic).|
|storage_percent|Percentuale archiviazione|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Uso archiviazione|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|
|storage_limit|Limite archiviazione|Byte|Spazio di archiviazione massimo per il server.|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Percentuale di spazio di archiviazione dei log del server usata rispetto allo spazio di archiviazione massimo dei log del server per il server.|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Quantità di spazio di archiviazione dei log del server in uso.|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Spazio di archiviazione massimo dei log del server per il server.|
|active_connections|Connessioni attive|Conteggio|Numero di connessioni al server attive.|
|connections_failed|Connessioni non riuscite|Conteggio|Numero di connessioni stabilite che hanno avuto esito negativo.|
|network_bytes_egress|Rete in uscita|Byte|Rete in uscita tra connessioni attive.|
|network_bytes_ingress|Rete in ingresso|Byte|Rete in ingresso tra connessioni attive.|
|backup_storage_used|Risorse di backup in uso|Byte|Quantità di risorse dell'archivio di backup usate. Questa metrica rappresenta la somma dello spazio di archiviazione utilizzato da tutti i backup completi del database, backup differenziali e backup del log mantenuti in base al periodo di conservazione dei backup impostato per il server. La frequenza dei backup è gestita dal servizio e illustrata nell' [articolo concetti](concepts-backup.md). Per l'archiviazione con ridondanza geografica, l'utilizzo dell'archiviazione di backup è due volte quello dell'archiviazione con ridondanza locale.|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (Ritardo massimo tra repliche)|Byte|Ritardo in byte tra la replica primaria e quella più in ritardo. Questa metrica è disponibile solo sul server primario.|
|pg_replica_log_delay_in_seconds|Replica Lag (Ritardo replica)|Secondi|Ora dall'ultima transazione riprodotta. Questa metrica è disponibile solo per i server di replica.|

## <a name="server-logs"></a>Log del server
È possibile abilitare l'accesso al server. Questi log delle risorse possono essere inviati a [log di monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md), Hub eventi e un account di archiviazione. Per altre informazioni sull'accesso, visitare la pagina dei [log del server](concepts-server-logs.md).

## <a name="query-store"></a>Query Store
[Query Store](concepts-query-store.md) tiene traccia delle prestazioni delle query nel tempo, incluse le statistiche di runtime di query e gli eventi di attesa. La funzionalità salva in modo permanente le informazioni sulle prestazioni dei runtime di query in un database di sistema denominato **azure_sys** nello schema query_store. È possibile controllare la raccolta e l'archiviazione dei dati tramite vari controlli di configurazione.

## <a name="query-performance-insight"></a>Informazioni dettagliate prestazioni query
[Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) funziona in combinazione con Query Store per fornire visualizzazioni accessibili dal portale di Azure. Questi grafici consentono di identificare le principali query che influiscono sulle prestazioni. Informazioni dettagliate prestazioni query è accessibile dalla sezione **supporto e risoluzione dei problemi** della pagina del portale del database di Azure per il server PostgreSQL.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) identifica le opportunità per migliorare le prestazioni dei carichi di lavoro. Suggerimenti sulle prestazioni fornisce consigli per la creazione di nuovi indici che possono migliorare le prestazioni dei carichi di lavoro. Per generare le raccomandazioni sugli indici, la funzionalità prende in considerazione diverse caratteristiche del database, tra cui lo schema e il carico di lavoro segnalati da Query Store. Dopo avere implementato le raccomandazioni per le prestazioni, i clienti devono testare le prestazioni per valutare l'impatto di tali modifiche. 

## <a name="planned-maintenance-notification"></a>Notifica di manutenzione pianificata

Le [notifiche di manutenzione pianificata](./concepts-planned-maintenance-notification.md) consentono di ricevere avvisi per la successiva manutenzione pianificata nel database di Azure per PostgreSQL-server singolo. Queste notifiche sono integrate con la manutenzione pianificata [dell'integrità dei servizi](../service-health/overview.md) e consentono di visualizzare tutte le operazioni di manutenzione pianificate per le sottoscrizioni in un'unica posizione. Consente inoltre di ridimensionare la notifica ai destinatari giusti per gruppi di risorse diversi, in quanto è possibile che si disponga di contatti diversi responsabili di risorse diverse. Si riceverà la notifica relativa alla manutenzione imminente di 72 ore prima dell'evento.

Per altre informazioni su come configurare le notifiche, vedere il documento relativo alle [notifiche di manutenzione pianificata](./concepts-planned-maintenance-notification.md) .

## <a name="next-steps"></a>Passaggi successivi
- Vedere [come configurare gli avvisi](howto-alert-on-metric.md) per istruzioni sulla creazione di un avviso per una metrica.
- Per altre informazioni su come accedere ed esportare le metriche usando il portale di Azure, l'API REST o l'interfaccia della riga di comando, vedere [Panoramica delle metriche di Azure](../azure-monitor/data-platform.md)
- Leggere il blog Microsoft sulle [procedure consigliate per il monitoraggio del server](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
- Altre informazioni sulle [notifiche di manutenzione pianificata](./concepts-planned-maintenance-notification.md) nel database di Azure per PostgreSQL-server singolo.