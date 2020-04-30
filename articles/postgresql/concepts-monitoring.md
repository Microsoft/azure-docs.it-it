---
title: Monitorare e ottimizzare - Database di Azure per PostgreSQL - Server singolo
description: Questo articolo descrive le funzionalità di monitoraggio e ottimizzazione di database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 99c8d4e7e5263b8a7caf30833a899976d3cc829b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133695"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Monitorare e ottimizzare Database di Azure per PostgreSQL - Server singolo
Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione in relazione al carico di lavoro. Database di Azure per PostgreSQL offre varie opzioni di monitoraggio che consentono di ottenere informazioni dettagliate sul comportamento del server.

## <a name="metrics"></a>Metriche
Database di Azure per PostgreSQL offre varie metriche che consentono di ottenere informazioni approfondite sul comportamento delle risorse che supportano il server PostgreSQL. Ogni metrica viene emessa con una frequenza di un minuto e ha una cronologia che può arrivare fino a 30 giorni. È possibile configurare avvisi in base alle metriche. Per indicazioni dettagliate, vedere l'articolo su come [configurare gli avvisi](howto-alert-on-metric.md). Le altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e l'archiviazione della cronologia. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Elenco delle metriche
Per Database di Azure per PostgreSQL sono disponibili le metriche seguenti:

|Metrica|Nome visualizzato per la metrica|Unità|Descrizione|
|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Percentuale di CPU in uso.|
|memory_percent|Percentuale memoria|Percentuale|Percentuale di memoria in uso.|
|io_consumption_percent|Percentuale IO|Percentuale|Percentuale di I/O in uso.|
|storage_percent|Percentuale archiviazione|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Uso archiviazione|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|
|storage_limit|Limite archiviazione|Byte|Spazio di archiviazione massimo per il server.|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Percentuale di spazio di archiviazione dei log del server usata rispetto allo spazio di archiviazione massimo dei log del server per il server.|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Quantità di spazio di archiviazione dei log del server in uso.|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Spazio di archiviazione massimo dei log del server per il server.|
|active_connections|Connessioni attive|Conteggio|Numero di connessioni al server attive.|
|connections_failed|Connessioni non riuscite|Conteggio|Numero di connessioni al server non riuscite.|
|network_bytes_egress|Rete in uscita|Byte|Rete in uscita tra connessioni attive.|
|network_bytes_ingress|Rete in ingresso|Byte|Rete in ingresso tra connessioni attive.|
|backup_storage_used|Risorse di backup in uso|Byte|Quantità di risorse dell'archivio di backup usate.|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (Ritardo massimo tra repliche)|Byte|Ritardo in byte tra il master e la replica più in ritardo. Questa metrica è disponibile solo nel server master.|
|pg_replica_log_delay_in_seconds|Replica Lag (Ritardo replica)|Secondi|Ora dall'ultima transazione riprodotta. Questa metrica è disponibile solo per i server di replica.|

## <a name="server-logs"></a>Log del server
È possibile abilitare l'accesso al server. Questi log delle risorse possono essere inviati a [log di monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md), Hub eventi e un account di archiviazione. Per altre informazioni sull'accesso, visitare la pagina dei [log del server](concepts-server-logs.md).

## <a name="query-store"></a>Archivio query
[Query Store](concepts-query-store.md) tiene traccia delle prestazioni delle query nel tempo, incluse le statistiche di runtime di query e gli eventi di attesa. La funzionalità salva in modo permanente le informazioni sulle prestazioni dei runtime di query in un database di sistema denominato **azure_sys** nello schema query_store. È possibile controllare la raccolta e l'archiviazione dei dati tramite vari controlli di configurazione.

## <a name="query-performance-insight"></a>Informazioni dettagliate prestazioni query
[Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) funziona in combinazione con Query Store per fornire visualizzazioni accessibili dal portale di Azure. Questi grafici consentono di identificare le principali query che influiscono sulle prestazioni. Informazioni dettagliate prestazioni query è accessibile dalla sezione **supporto e risoluzione dei problemi** della pagina del portale del database di Azure per il server PostgreSQL.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) identifica le opportunità per migliorare le prestazioni dei carichi di lavoro. Suggerimenti sulle prestazioni fornisce consigli per la creazione di nuovi indici che possono migliorare le prestazioni dei carichi di lavoro. Per generare le raccomandazioni sugli indici, la funzionalità prende in considerazione diverse caratteristiche del database, tra cui lo schema e il carico di lavoro segnalati da Query Store. Dopo avere implementato le raccomandazioni per le prestazioni, i clienti devono testare le prestazioni per valutare l'impatto di tali modifiche. 

## <a name="planned-maintenance-notification"></a>Notifica di manutenzione pianificata

Le **notifiche di manutenzione pianificata** consentono di ricevere avvisi per la successiva manutenzione pianificata nel database di Azure per PostgreSQL-server singolo. Queste notifiche sono integrate con la manutenzione pianificata [dell'integrità dei servizi](../service-health/overview.md) e consentono di visualizzare tutte le operazioni di manutenzione pianificate per le sottoscrizioni in un'unica posizione. Consente inoltre di ridimensionare la notifica ai destinatari giusti per gruppi di risorse diversi, in quanto è possibile che si disponga di contatti diversi responsabili di risorse diverse. Si riceverà la notifica relativa alla manutenzione imminente di 72 ore prima dell'evento.

> [!Note]
> Ogni tentativo di fornire **notifiche di manutenzione pianificate** 72 ore di preavviso per tutti gli eventi verrà effettuato. Tuttavia, in caso di patch di sicurezza o critiche, le notifiche potrebbero essere inviate più vicino all'evento o essere omesse.

### <a name="to-receive-planned-maintenance-notification"></a>Per ricevere la notifica di manutenzione pianificata

1. Nel [portale](https://portal.azure.com) selezionare **Integrità del servizio**.
2. Nella sezione **Avvisi** selezionare **Avvisi integrità**.
3. Selezionare **+ Aggiungi avviso di integrità del servizio** e compilare i campi.
4. Compilare i campi obbligatori. 
5. Scegliere il **tipo di evento**, selezionare **manutenzione pianificata** o **selezionare tutto**
6. Nei **gruppi di azioni** definire come si vuole ricevere l'avviso (ricevere un messaggio di posta elettronica, attivare un'app per la logica e così via).  
7. Assicurarsi che Abilita regola al momento della creazione sia impostata su Sì.
8. Selezionare **Crea regola di avviso** per completare l'avviso

Per i passaggi dettagliati su come creare gli **avvisi di integrità del servizio**, vedere [creare avvisi del log attività nelle notifiche del servizio](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> Le notifiche di manutenzione pianificata sono attualmente in anteprima

## <a name="next-steps"></a>Passaggi successivi
- Vedere [come configurare gli avvisi](howto-alert-on-metric.md) per istruzioni sulla creazione di un avviso per una metrica.
- Per altre informazioni sull'accesso alle metriche e la relativa esportazione con il portale di Azure, l'API REST o l'interfaccia della riga di comando, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leggere il blog Microsoft sulle [procedure consigliate per il monitoraggio del server](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
