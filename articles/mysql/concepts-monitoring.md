---
title: Monitoraggio-database di Azure per MySQL
description: Questo articolo illustra le metriche di monitoraggio e avviso per Database di Azure per MySQL, che includono statistiche relative a CPU, spazio di archiviazione e connessioni.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/13/2020
ms.openlocfilehash: 207693945c2fe916e99d55545d8a33c08067ba04
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538280"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitoraggio in Database di Azure per MySQL
Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione in relazione al carico di lavoro. Database di Azure per MySQL offre varie metriche che consentono di ottenere informazioni approfondite sul comportamento del server.

## <a name="metrics"></a>Metriche
Tutte le metriche di Azure hanno una frequenza di un minuto e offrono una cronologia di 30 giorni. È possibile configurare avvisi in base alle metriche. Per indicazioni dettagliate, vedere l'articolo su come [configurare gli avvisi](howto-alert-on-metric.md). Le altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e l'archiviazione della cronologia. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Elenco delle metriche
Per Database di Azure per MySQL sono disponibili le metriche seguenti:

|Metrica|Nome visualizzato per la metrica|Unità|Descrizione|
|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Percentuale di CPU in uso.|
|memory_percent|Percentuale memoria|Percentuale|Percentuale di memoria in uso.|
|io_consumption_percent|Percentuale IO|Percentuale|Percentuale di I/O in uso. (Non applicabile per i server di livello Basic)|
|storage_percent|Percentuale archiviazione|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Uso archiviazione|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Percentuale di spazio di archiviazione dei log del server usata rispetto allo spazio di archiviazione massimo dei log del server per il server.|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Quantità di spazio di archiviazione dei log del server in uso.|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Spazio di archiviazione massimo dei log del server per il server.|
|storage_limit|Limite archiviazione|Byte|Spazio di archiviazione massimo per il server.|
|active_connections|Connessioni attive|Conteggio|Numero di connessioni al server attive.|
|connections_failed|Connessioni non riuscite|Conteggio|Numero di connessioni al server non riuscite.|
|seconds_behind_master|Intervallo di replica in secondi|Conteggio|Numero di secondi di ritardo del server di replica rispetto al server di origine. (Non applicabile per i server di livello Basic)|
|network_bytes_egress|Rete in uscita|Byte|Rete in uscita tra connessioni attive.|
|network_bytes_ingress|Rete in ingresso|Byte|Rete in ingresso tra connessioni attive.|
|backup_storage_used|Risorse di backup in uso|Byte|Quantità di risorse dell'archivio di backup usate. Questa metrica rappresenta la somma dello spazio di archiviazione utilizzato da tutti i backup completi del database, backup differenziali e backup del log mantenuti in base al periodo di conservazione dei backup impostato per il server. La frequenza dei backup è gestita dal servizio e illustrata nell' [articolo concetti](concepts-backup.md). Per l'archiviazione con ridondanza geografica, l'utilizzo dell'archiviazione di backup è due volte quello dell'archiviazione con ridondanza locale.|

## <a name="server-logs"></a>Log del server
È possibile abilitare la registrazione lenta della query e del controllo nel server. Questi log sono disponibili anche tramite i log di diagnostica di Azure in log di monitoraggio di Azure, Hub eventi e account di archiviazione. Per altre informazioni sulla registrazione, vedere gli articoli [log di controllo](concepts-audit-logs.md) e log di [query lente](concepts-server-logs.md) .

## <a name="query-store"></a>Archivio query
[Query Store](concepts-query-store.md) è una funzionalità che tiene traccia delle prestazioni delle query nel tempo, incluse le statistiche di runtime di query e gli eventi di attesa. La funzionalità rende permanente le informazioni sulle prestazioni di query runtime nello schema **MySQL** . È possibile controllare la raccolta e l'archiviazione dei dati tramite vari controlli di configurazione.

## <a name="query-performance-insight"></a>Informazioni dettagliate prestazioni query
[Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) funziona in combinazione con Query Store per fornire visualizzazioni accessibili dal portale di Azure. Questi grafici consentono di identificare le principali query che influiscono sulle prestazioni. Informazioni dettagliate prestazioni query è accessibile nella sezione **prestazioni intelligenti** della pagina del portale del database di Azure per il server MySQL.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) identifica le opportunità per migliorare le prestazioni dei carichi di lavoro. Suggerimenti sulle prestazioni fornisce consigli per la creazione di nuovi indici che possono migliorare le prestazioni dei carichi di lavoro. Per generare le raccomandazioni sugli indici, la funzionalità prende in considerazione diverse caratteristiche del database, tra cui lo schema e il carico di lavoro segnalati da Query Store. Dopo avere implementato le raccomandazioni per le prestazioni, i clienti devono testare le prestazioni per valutare l'impatto di tali modifiche.

## <a name="planned-maintenance-notification"></a>Notifica di manutenzione pianificata

Le **notifiche di manutenzione pianificata** consentono di ricevere avvisi per la manutenzione pianificata imminente nel database di Azure per MySQL. Queste notifiche sono integrate con la manutenzione pianificata [dell'integrità dei servizi](../service-health/overview.md) e consentono di visualizzare tutte le operazioni di manutenzione pianificate per le sottoscrizioni in un'unica posizione. Consente inoltre di ridimensionare la notifica ai destinatari giusti per gruppi di risorse diversi, in quanto è possibile che si disponga di contatti diversi responsabili di risorse diverse. Si riceverà la notifica relativa alla manutenzione imminente di 72 ore prima dell'evento.

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
> Le notifiche di manutenzione pianificata sono attualmente disponibili in anteprima in tutte le aree **ad eccezione** degli Stati Uniti centro-occidentali

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sulla creazione di un avviso per una metrica, vedere [Come configurare gli avvisi](howto-alert-on-metric.md).
- Per altre informazioni sull'accesso alle metriche e la relativa esportazione con il portale di Azure, l'API REST o l'interfaccia della riga di comando, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leggere il blog Microsoft sulle [procedure consigliate per il monitoraggio del server](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
