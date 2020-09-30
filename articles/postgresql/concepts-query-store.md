---
title: Query Store-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive la funzionalità Query Store di database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: 2cda79e1b08e67e10d42acb5093230ce8450d67d
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530919"
---
# <a name="monitor-performance-with-the-query-store"></a>Monitorare le prestazioni con Query Store

**Si applica a:** Database di Azure per PostgreSQL: versioni a server singolo 9,6 e versioni successive

La funzionalità Query Store di Database di Azure per PostgreSQL offre la possibilità di tenere traccia delle prestazioni delle query nel tempo. Query Store semplifica la risoluzione dei problemi di prestazioni consentendo di trovare rapidamente le query con il tempo di esecuzione più lungo e il più elevato utilizzo di risorse. Query Store acquisisce automaticamente una cronologia delle query e le statistiche di runtime e le conserva a scopo di verifica. I dati vengono separati per intervalli di tempo per consentire l'individuazione dei modelli di utilizzo dei database. I dati relativi a tutti gli utenti, tutti i database e tutte le query vengono archiviati in un database denominato **azure_sys** nell'istanza di Database di Azure per PostgreSQL.

> [!IMPORTANT]
> Non modificare il database **azure_sys** o i relativi schemi. Tale modifica impedirebbe il corretto funzionamento di Query Store e delle funzionalità per le prestazioni correlate.

## <a name="enabling-query-store"></a>Abilitazione di Query Store
Query Store è una funzionalità con consenso esplicito e non è quindi attivo per impostazione predefinita in un server. Viene abilitato o disabilitato a livello globale per tutti i database in un determinato server e non può essere attivato o disattivato in base al database.

### <a name="enable-query-store-using-the-azure-portal"></a>Abilitare Query Store con il portale di Azure
1. Accedere al portale di Azure e selezionare il server di Database di Azure per PostgreSQL.
2. Selezionare **Parametri del server** nella sezione **Impostazioni** del menu.
3. Cercare il `pg_qs.query_capture_mode` parametro.
4. Impostare il valore su `TOP` e **Salva**.

Per abilitare le statistiche di attesa in Query Store: 
1. Cercare il `pgms_wait_sampling.query_capture_mode` parametro.
1. Impostare il valore su `ALL` e **Salva**.


In alternativa, è possibile impostare questi parametri usando l'interfaccia della riga di comando di Azure.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Per il salvataggio permanente del primo batch di dati nel database azure_sys possono essere necessari fino a 20 minuti.

## <a name="information-in-query-store"></a>Informazioni in Query Store
Query Store include due archivi:
- Un archivio delle statistiche di runtime per il salvataggio permanente delle informazioni delle statistiche di esecuzione delle query.
- Un archivio delle statistiche di attesa per il salvataggio permanente delle informazioni delle statistiche di attesa.

Gli scenari comuni per l'uso di Query Store includono:
- Determinazione del numero di volte in cui una query è stata eseguita in un determinato intervallo di tempo
- Confronto del tempo di esecuzione di una query nei diversi intervalli di tempo per identificare differenze significative
- Identificazione delle query con il tempo di esecuzione più lungo nelle ultime X ore
- Identificazione delle prime N query in attesa delle risorse
- Comprensione della natura dell'attesa per una determinata query

Per ridurre al minimo l'utilizzo di spazio, le statistiche di esecuzione di runtime nell'archivio delle statistiche di runtime vengono aggregate per un intervallo di tempo fisso configurabile. Le informazioni negli archivi sono visibili eseguendo query sulle viste di Query Store.

## <a name="access-query-store-information"></a>Informazioni sull'accesso Query Store

I dati Query Store vengono archiviati nel database di azure_sys nel server postgres. 

La query seguente restituisce informazioni sulle query in Query Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

In alternativa, usare questa query per le statistiche di attesa:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Ricerca di query in relazione all'attesa
I tipi di eventi di attesa combinano diversi eventi di attesa in bucket in base alla somiglianza. Query Store indica il tipo di evento di attesa, il nome dello specifico evento di attesa e la query in questione. La possibilità di correlare queste informazioni sulle attese alle statistiche di runtime delle query consente di comprendere in modo più approfondito ciò che contribuisce alle caratteristiche di prestazioni delle query.

Di seguito sono riportati alcuni esempi di come è possibile ottenere informazioni dettagliate sul carico di lavoro usando le statistiche di attesa in Query Store:

| **Osservazione** | **Azione** |
|---|---|
|Attese di blocco elevate | Controllare il testo delle query interessate e identificare le entità di destinazione. Cercare in Query Store altre query che modificano la stessa entità e che vengono eseguite spesso e/o hanno durata elevata. Dopo aver identificato tali query, valutare la possibilità di modificare la logica dell'applicazione per migliorare la concorrenza o usare un livello di isolamento meno restrittivo.|
| Attese di I/O del buffer elevate | Trovare le query con un numero elevato di letture fisiche in Query Store. Se corrispondono alle query con attese di I/O elevate, valutare la possibilità di introdurre un indice sull'entità sottostante in modo da eseguire ricerche anziché analisi. Questo ridurrebbe al minimo il sovraccarico di I/O delle query. Controllare le **raccomandazioni per le prestazioni** relative al server nel portale per verificare se sono presenti raccomandazioni sugli indici per il server che ottimizzerebbero le query.|
| Attese di memoria elevate | Trovare le query con il maggiore utilizzo di memoria in Query Store. Queste query probabilmente ritardano l'avanzamento delle query interessate. Controllare le **raccomandazioni per le prestazioni** relative al server nel portale per verificare se sono presenti raccomandazioni sugli indici che ottimizzerebbero queste query.|

## <a name="configuration-options"></a>Opzioni di configurazione
Quando è abilitato, Query Store salva i dati in intervalli di aggregazione di 15 minuti, con un massimo di 500 query distinte per intervallo. 

Per la configurazione dei parametri di Query Store sono disponibili le opzioni seguenti.

| **Parametro** | **Descrizione** | **Default** | **Range**|
|---|---|---|---|
| pg_qs.query_capture_mode | Imposta le istruzioni di cui verrà tenuta traccia. | Nessuno | none, top, all |
| pg_qs.max_query_text_length | Imposta la lunghezza massima di query che è possibile salvare. Le query più lunghe verranno troncate. | 6000 | 100-10000 |
| pg_qs.retention_period_in_days | Imposta il periodo di conservazione. | 7 | 1-30 |
| pg_qs.track_utility | Imposta se deve essere tenuta traccia dei comandi dell'utilità. | on | on, off |

Le opzioni seguenti si applicano specificamente alle statistiche di attesa.

| **Parametro** | **Descrizione** | **Default** | **Range**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Imposta le istruzioni di cui verrà tenuta traccia per le statistiche di attesa. | Nessuno | none, all|
| Pgms_wait_sampling.history_period | Imposta la frequenza di campionamento degli eventi di attesa, in millisecondi. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** prevale su **pgms_wait_sampling.query_capture_mode**. Se il valore di pg_qs.query_capture_mode è NONE, l'impostazione di pgms_wait_sampling.query_capture_mode non ha alcun effetto.


Per ottenere o impostare un diverso valore per un parametro, usare il [portale di Azure](howto-configure-server-parameters-using-portal.md) o l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).

## <a name="views-and-functions"></a>Viste e funzioni
Visualizzare e gestire Query Store usando le viste e le funzioni seguenti. Queste viste possono essere usate da qualsiasi membro del ruolo pubblico di PostgreSQL per visualizzare i dati in Query Store e sono disponibili solo nel database **azure_sys**.

Le query vengono normalizzate esaminandone la struttura dopo la rimozione di valori letterali e costanti. Due query identiche tranne per i valori letterali avranno lo stesso hash.

### <a name="query_storeqs_view"></a>query_store.qs_view
Questa vista restituisce tutti i dati in Query Store. Contiene una riga per ogni specifico ID database, ID utente e ID query. 

|**Nome**   |**Tipo** | **Riferimenti**  | **Descrizione**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID nella tabella runtime_stats_entries|
|user_id    |oid    |pg_authid.oid  |OID dell'utente che ha eseguito l'istruzione|
|db_id  |oid    |pg_database.oid    |OID del database in cui l'istruzione è stata eseguita|
|query_id   |bigint  || Codice hash interno, calcolato dall'albero di analisi dell'istruzione|
|query_sql_text |Varchar(10000)  || Testo di un'istruzione rappresentativa. Query diverse con la stessa struttura vengono raggruppate e questo è il testo per la prima query del gruppo.|
|plan_id    |bigint |   |ID del piano corrispondente alla query, non ancora disponibile|
|start_time |timestamp  ||  Le query vengono aggregate per intervalli di tempo. La durata di un intervallo è di 15 minuti per impostazione predefinita. Questo timestamp è l'ora di inizio corrispondente all'intervallo di tempo della voce.|
|end_time   |timestamp  ||  Ora di fine corrispondente all'intervallo di tempo della voce|
|calls  |bigint  || Numero di volte in cui la query è stata eseguita|
|total_time |double precision   ||  Tempo totale di esecuzione della query, in millisecondi|
|min_time   |double precision   ||  Tempo minimo di esecuzione della query, in millisecondi|
|max_time   |double precision   ||  Tempo massimo di esecuzione della query, in millisecondi|
|mean_time  |double precision   ||  Tempo medio di esecuzione della query, in millisecondi|
|stddev_time|   double precision    ||  Deviazione standard del tempo di esecuzione della query, in millisecondi |
|rows   |bigint ||  Numero totale di righe recuperate o interessate dall'istruzione|
|shared_blks_hit|   bigint  ||  Numero totale di riscontri nella cache dei blocchi condivisi ottenuto dall'istruzione|
|shared_blks_read|  bigint  ||  Numero totale dei blocchi condivisi letti dall'istruzione|
|shared_blks_dirtied|   bigint   || Numero totale dei blocchi condivisi modificati ma non salvati dall'istruzione |
|shared_blks_written|   bigint  ||  Numero totale dei blocchi condivisi scritti dall'istruzione|
|local_blks_hit|    bigint ||   Numero totale di riscontri nella cache dei blocchi locali ottenuto dall'istruzione|
|local_blks_read|   bigint   || Numero totale dei blocchi locali letti dall'istruzione|
|local_blks_dirtied|    bigint  ||  Numero totale dei blocchi locali modificati ma non salvati dall'istruzione|
|local_blks_written|    bigint  ||  Numero totale dei blocchi locali scritti dall'istruzione|
|temp_blks_read |bigint  || Numero totale dei blocchi temporanei letti dall'istruzione|
|temp_blks_written| bigint   || Numero totale dei blocchi temporanei scritti dall'istruzione|
|blk_read_time  |double precision    || Tempo totale impiegato dall'istruzione per la lettura dei blocchi, in millisecondi, se il parametro track_io_timing è abilitato. In caso contrario, è zero.|
|blk_write_time |double precision    || Tempo totale impiegato dall'istruzione per la scrittura dei blocchi, in millisecondi, se il parametro track_io_timing è abilitato. In caso contrario, è zero.|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Questa vista restituisce i dati del testo delle query in Query Store. Contiene una riga per ogni specifico query_text.

|**Nome**|  **Tipo**|   **Descrizione**|
|---|---|---|
|query_text_id  |bigint     |ID della tabella query_texts|
|query_sql_text |Varchar(10000)     |Testo di un'istruzione rappresentativa. Query diverse con la stessa struttura vengono raggruppate e questo è il testo per la prima query del gruppo.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
Questa vista restituisce i dati degli eventi di attesa in Query Store. Contiene una riga per ogni specifico ID database, ID utente, ID query ed evento.

|**Nome**|  **Tipo**|   **Riferimenti**| **Descrizione**|
|---|---|---|---|
|user_id    |oid    |pg_authid.oid  |OID dell'utente che ha eseguito l'istruzione|
|db_id  |oid    |pg_database.oid    |OID del database in cui l'istruzione è stata eseguita|
|query_id   |bigint     ||Codice hash interno, calcolato dall'albero di analisi dell'istruzione|
|event_type |testo       ||Tipo di evento atteso dal back-end|
|event  |testo       ||Nome dell'evento di attesa, se il back-end è attualmente in attesa|
|calls  |Integer        ||Numero dello stesso evento acquisito|


### <a name="functions"></a>Funzioni
Query_store.qs_reset() restituisce void

`qs_reset` rimuove tutte le statistiche finora raccolte da Query Store. Questa funzione può essere eseguita solo dal ruolo di amministratore del server.

Query_store.staging_data_reset() restituisce void

`staging_data_reset` rimuove tutte le statistiche raccolte in memoria da Query Store, ossia i dati in memoria che non sono ancora stati scaricati nel database. Questa funzione può essere eseguita solo dal ruolo di amministratore del server.


## <a name="azure-monitor"></a>Monitoraggio di Azure
Database di Azure per PostgreSQL è integrato con [le impostazioni di diagnostica di monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md). Le impostazioni di diagnostica consentono di inviare i log Postgres in formato JSON ai [log di monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md) per l'analisi e gli avvisi, gli hub eventi per lo streaming e l'archiviazione di Azure per l'archiviazione.

>[!IMPORTANT]
> Questa funzionalità di diagnostica per è disponibile solo nei piani tariffari per utilizzo generico e con ottimizzazione per la memoria.

### <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica
È possibile abilitare le impostazioni di diagnostica per il server Postgres usando il portale di Azure, l'interfaccia della riga di comando, l'API REST e PowerShell. Le categorie di log da configurare sono **QueryStoreRuntimeStatistics** e **QueryStoreWaitStatistics**. 

Per abilitare i log delle risorse usando il portale di Azure:

1. Nel portale passare a impostazioni di diagnostica nel menu di navigazione del server postgres.
2. Selezionare Aggiungi impostazioni di diagnostica.
3. Assegnare un nome a questa impostazione.
4. Selezionare l'endpoint preferito (account di archiviazione, Hub eventi, log Analytics).
5. Selezionare i tipi di log **QueryStoreRuntimeStatistics** e **QueryStoreWaitStatistics**.
6. Salvare l’impostazione.

Per abilitare questa impostazione usando PowerShell, l'interfaccia della riga di comando o l'API REST, vedere l'articolo relativo alle [impostazioni di diagnostica](../azure-monitor/platform/diagnostic-settings.md).

### <a name="json-log-format"></a>Formato di log JSON
Le tabelle seguenti descrivono i campi per i due tipi di log. A seconda dell'endpoint di output scelto è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

#### <a name="querystoreruntimestatistics"></a>QueryStoreRuntimeStatistics
|**Campo** | **Descrizione** |
|---|---|
| TimeGenerated [UTC] | Timestamp in cui il log è stato registrato in formato UTC. |
| ResourceId | URI della risorsa di Azure del server Postgres |
| Category | `QueryStoreRuntimeStatistics` |
| OperationName | `QueryStoreRuntimeStatisticsEvent` |
| LogicalServerName_s | Nome server Postgres | 
| runtime_stats_entry_id_s | ID nella tabella runtime_stats_entries |
| user_id_s | OID dell'utente che ha eseguito l'istruzione |
| db_id_s | OID del database in cui l'istruzione è stata eseguita |
| query_id_s | Codice hash interno, calcolato dall'albero di analisi dell'istruzione |
| end_time_s | Ora di fine corrispondente al bucket temporale per questa voce |
| calls_s | Numero di volte in cui la query è stata eseguita |
| total_time_s | Tempo totale di esecuzione della query, in millisecondi |
| min_time_s | Tempo minimo di esecuzione della query, in millisecondi |
| max_time_s | Tempo massimo di esecuzione della query, in millisecondi |
| mean_time_s | Tempo medio di esecuzione della query, in millisecondi |
| ResourceGroup | Gruppo di risorse | 
| SubscriptionId | ID sottoscrizione |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Risorsa | Nome server Postgres |
| ResourceType | `Servers` | 


#### <a name="querystorewaitstatistics"></a>QueryStoreWaitStatistics
|**Campo** | **Descrizione** |
|---|---|
| TimeGenerated [UTC] | Timestamp in cui il log è stato registrato in formato UTC. |
| ResourceId | URI della risorsa di Azure del server Postgres |
| Category | `QueryStoreWaitStatistics` |
| OperationName | `QueryStoreWaitEvent` |
| user_id_s | OID dell'utente che ha eseguito l'istruzione |
| db_id_s | OID del database in cui l'istruzione è stata eseguita |
| query_id_s | Codice hash interno della query |
| calls_s | Numero dello stesso evento acquisito |
| event_type_s | Tipo di evento atteso dal back-end |
| event_s | Nome dell'evento Wait se il back-end è attualmente in attesa |
| start_time_t | Ora di inizio dell'evento |
| end_time_s | Ora di fine evento | 
| LogicalServerName_s | Nome server Postgres | 
| ResourceGroup | Gruppo di risorse | 
| SubscriptionId | ID sottoscrizione |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Risorsa | Nome server Postgres |
| ResourceType | `Servers` | 

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti
- Se un server PostgreSQL ha il parametro default_transaction_read_only on, Query Store non può acquisire i dati.
- La funzionalità Query Store può essere interrotta se si verificano query Unicode lunghe (> = 6000 byte).
- Le [repliche di lettura](concepts-read-replicas.md) replicano query Store dati dal server primario. Ciò significa che la Query Store di una replica di lettura non fornisce statistiche sulle query eseguite nella replica di lettura.


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sugli [scenari in cui Query Store può essere particolarmente utile](concepts-query-store-scenarios.md).
- Altre informazioni sulle [procedure consigliate per l'uso di Query Store](concepts-query-store-best-practices.md).
