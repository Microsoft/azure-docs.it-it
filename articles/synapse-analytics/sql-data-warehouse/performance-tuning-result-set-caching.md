---
title: Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati
description: Panoramica delle funzionalità di memorizzazione nella cache dei set di risultati per il pool SQL sinapsi in Azure sinapsi Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: eadbe13269ce1259b4560af117f5b15b3b294151
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730594"
---
# <a name="performance-tuning-with-result-set-caching"></a>Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati

Quando è abilitata la memorizzazione nella cache del set di risultati, SQL Analytics memorizza automaticamente nella cache i risultati delle query nel database utente per l'utilizzo ripetitivo.  In questo modo, le esecuzioni di query successive ottengono risultati direttamente dalla cache permanente, quindi il ricalcolo non è necessario.   La memorizzazione nella cache del set di risultati migliora le prestazioni delle query e riduce l'utilizzo delle risorse di calcolo.  Inoltre, le query che utilizzano i set di risultati memorizzati nella cache non utilizzano slot di concorrenza e pertanto non vengono conteggiati rispetto ai limiti di concorrenza esistenti. Per la sicurezza, gli utenti possono accedere solo ai risultati memorizzati nella cache se hanno le stesse autorizzazioni di accesso ai dati degli utenti che creano i risultati memorizzati nella cache.  

## <a name="key-commands"></a>Comandi chiave

[Attivare/disattivare la memorizzazione nella cache del set di risultati per un database utente](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Attivare/disattivare la memorizzazione nella cache del set di risultati per un database utente](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Attivare/disattivare la memorizzazione nella cache del set di risultati per una sessione](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Controllare le dimensioni del set di risultati memorizzato nella cache](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[Pulire la cache](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Elementi non memorizzati nella cache  

Dopo l'attivazione della memorizzazione nella cache dei set di risultati per un database, i risultati vengono memorizzati nella cache per tutte le query fino al riempimento della cache, ad eccezione delle query seguenti:

- Query che usano funzioni non deterministiche come DateTime.Now()
- Query che usano funzioni definite dall'utente
- Query che utilizzano tabelle con sicurezza a livello di riga o a livello di colonna abilitata
- Query che restituiscono dati con dimensioni di riga maggiori di 64 KB
- Query che restituiscono dati di grandi dimensioni (>10 GB) 

> [!IMPORTANT]
> Le operazioni per creare la cache dei set di risultati e recuperare i dati dalla cache avvengono nel nodo di controllo di un'istanza del pool SQL sinapsi.
> Quando la memorizzazione nella cache del set di risultati è attivata, l'esecuzione di query che restituiscono set di risultati di grandi dimensioni, ad esempio >1 GB, può causare una limitazione elevata sul nodo di controllo e rallentare la risposta complessiva della query sull'istanza.  Queste query vengono in genere usate durante l'esplorazione dei dati o le operazioni ETL (estrazione, trasformazione e caricamento). Per evitare il sovraccarico del nodo di controllo e la comparsa di problemi di prestazioni, gli utenti devono DISATTIVARE la memorizzazione nella cache del set di risultati nel database prima di eseguire query di questo tipo.  

Eseguire questa query per il tempo impiegato dalle operazioni di memorizzazione nella cache dei set di risultati per una query:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Di seguito è riportato un esempio di output per una query eseguita con la memorizzazione nella cache dei set di risultati disabilitata.

![Query-passaggi-con-RSC-disabilitato](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Di seguito è riportato un esempio di output per una query eseguita con la memorizzazione nella cache dei set di risultati abilitata.

![Query-passaggi-con-RSC-abilitato](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Quando vengono usati i risultati memorizzati nella cache

Il set di risultati memorizzato nella cache viene riutilizzato per una query se vengono soddisfatti tutti i requisiti seguenti:

- L'utente che esegue la query ha accesso a tutte le tabelle a cui viene fatto riferimento nella query.
- È presente una corrispondenza esatta tra la nuova query e la query precedente che ha generato la cache dei set di risultati.
- Non sono presenti dati o modifiche dello schema nelle tabelle da cui è stato generato il set di risultati memorizzato nella cache.

Eseguire questo comando per verificare se una query è stata eseguita con un riscontro nella cache dei risultati. La colonna result_cache_hit restituisce 1 per il riscontro nella cache, 0 per mancato riscontro nella cache e i valori negativi per i motivi per cui non è stato usato il caching del set di risultati. Per informazioni dettagliate, vedere [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gestire i risultati memorizzati nella cache

Le dimensioni massime della cache dei set di risultati sono pari a 1 TB per ogni database.  I risultati memorizzati nella cache vengono automaticamente invalidati quando i dati della query sottostante cambiano.  

La rimozione della cache viene gestita da SQL Analytics automaticamente dopo questa pianificazione:

- Ogni 48 ore se il set di risultati non è stato usato o è stato invalidato.
- Quando la cache del set di risultati si avvicina alla dimensione massima.

Gli utenti possono svuotare manualmente l'intera cache dei set di risultati utilizzando una di queste opzioni:

- Disattivare la funzionalità di memorizzazione nella cache dei set di risultati per il database
- Eseguire DBCC DROPRESULTSETCACHE durante la connessione al database

Se si sospende un database, il set di risultati memorizzato nella cache non verrà vuoto.  

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).
