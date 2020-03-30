---
title: Informazioni dettagliate prestazioni query
description: Il monitoraggio delle prestazioni delle query identifica la maggior parte delle query che consumano cpu e a esecuzione prolungata per i database singoli e in pool nel database SQL di Azure.Query performance monitoring identifies the most CPU-consuming and long-running queries for single and pooled databases in Azure SQL database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: f5998fde6659715de4fcb533cb0f41a8939b1c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214052"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Informazioni dettagliate prestazioni query per il database SQL di Azure

Informazioni dettagliate sulle prestazioni delle query fornisce un'analisi intelligente delle query per database singoli e in pool. Consente di identificare le query di consumo di risorse principali e a esecuzione prolungata nel carico di lavoro. In questo modo è possibile trovare le query da ottimizzare per migliorare le prestazioni complessive del carico di lavoro e utilizzare in modo efficiente la risorsa che si sta pagando. Informazioni dettagliate sulle prestazioni delle query consente di dedicare meno tempo alla risoluzione dei problemi relativi alle prestazioni del database fornendo:

* Approfondimenti sul consumo di risorse di database (DTU)
* Dettagli sulle query di database principali per CPU, durata e conteggio dell'esecuzione (potenziali candidati per l'ottimizzazione delle prestazioni per migliorare le prestazioni)
* Possibilità di eseguire il drill-down dei dettagli di una query, per visualizzare il testo della query e la cronologia dell'utilizzo delle risorse
* Annotazioni che mostrano i consigli sulle prestazioni degli [advisor di databaseAnnotations](sql-database-advisor.md) that show performance recommendations from database advisors

![Informazioni dettagliate prestazioni query](./media/sql-database-query-performance/opening-title.png)

## <a name="prerequisites"></a>Prerequisiti

Per Informazioni dettagliate sulle prestazioni delle query è necessario che l' [archivio query](https://msdn.microsoft.com/library/dn817826.aspx) sia attivo nel database. Per impostazione predefinita, è abilitato automaticamente per tutti i database SQL di Azure. Se Query Store non è in esecuzione, il portale di Azure richiederà di abilitarlo.

> [!NOTE]
> Se nel portale viene visualizzato un messaggio che indica che Query Store non è configurato correttamente nel database, vedere [Ottimizzare la configurazione di Query Store](#optimize-the-query-store-configuration).

## <a name="permissions"></a>Autorizzazioni

Sono necessarie le autorizzazioni di [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) seguenti per usare Informazioni dettagliate prestazioni query:

* Le autorizzazioni **Lettore**, **Proprietario**, **Collaboratore**, **Collaboratore database SQL** o **Collaboratore SQL Server** sono necessarie per visualizzare le query principali a livello di uso delle risorse e i grafici.
* Le autorizzazioni **Proprietario**, **Collaboratore**, **Collaboratore database SQL** o **Collaboratore SQL Server** sono necessarie per visualizzare il testo della query.

## <a name="use-query-performance-insight"></a>Usare Informazioni dettagliate prestazioni query

Query Performance Insight è facile da usare:

1. Aprire il [portale di Azure](https://portal.azure.com/) e individuare il database da esaminare.
2. Dal menu a sinistra aprire **Intelligent Performance** > **Query Performance Insight**.
  
   ![Informazioni dettagliate prestazioni query nel menu](./media/sql-database-query-performance/tile.png)

3. Nella prima scheda, esaminare l'elenco delle query principali a livello di utilizzo delle risorse.
4. Selezionare una singola query per visualizzarne i dettagli.
5. Aprire**i consigli** per le **prestazioni** > intelligenti e verificare se sono disponibili consigli sulle prestazioni. Per altre informazioni sulle raccomandazioni per le prestazioni integrate, vedere [Advisor per database SQL](sql-database-advisor.md).
6. Usare i dispositivi di scorrimento o le icone dello zoom per modificare l'intervallo osservato.

   ![Dashboard delle prestazioni](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Per consentire al database SQL di eseguire il rendering delle informazioni in Informazioni dettagliate prestazioni query, Query Store deve acquisire un paio d'ore di dati. Se il database non ha alcuna attività o Query Store non è attivo in un determinato periodo, i grafici saranno vuoti quando si visualizza tale intervallo di tempo in Informazioni dettagliate prestazioni query. È possibile abilitare Query Store in qualsiasi momento, se non è in esecuzione. Per altre informazioni, vedere [Best practices with Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store) (Procedure consigliate per Query Store).
>

Per altri suggerimenti sulle prestazioni del database, selezionare [Raccomandazioni](sql-database-advisor.md) nel pannello di spostamento di Informazioni dettagliate prestazioni query.

![Scheda Raccomandazioni](./media/sql-database-query-performance/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Esaminare le query principali a livello di uso della CPU

Per impostazione predefinita, alla prima apertura Informazioni dettagliate prestazioni query mostra le cinque query principali a livello di uso della CPU.

1. Selezionare o deselezionare le singole query per includerle o escluderle dal grafico usando le caselle di controllo.

   La linea superiore visualizza la percentuale DTU complessiva per il database. Le barre mostrano la percentuale di uso della CPU per le query selezionate durante l'intervallo selezionato. Ad esempio, se è selezionata l'opzione **Settimana precedente**, ogni barra rappresenta un singolo giorno.

   ![Query principali](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > La linea DTU visualizzata viene aggregata in base a un valore massimo di consumo per periodi di un'ora. È pensata per consentire solo un confronto generale con le statistiche di esecuzione delle query. In alcuni casi, l'uso DTU può apparire troppo elevato rispetto alle query eseguite, ma la situazione potrebbe essere diversa.
   >
   > Ad esempio, se una query raggiunge il limite del 100% di DTU solo per pochi minuti, la linea DTU in Informazioni dettagliate prestazioni query mostrerà un'intera ora di consumo al 100% (come conseguenza del valore massimo aggregato).
   >
   > Per un confronto più preciso (fino a 1 minuto), è consigliabile creare un grafico dell'uso DTU personalizzato:
   >
   > 1. Nel portale di Azure selezionare Monitoraggio database SQL di Azure .In the Azure portal, select **Azure SQL Database** > **Monitoring**.
   > 2. Selezionare **Metriche**.
   > 3. Selezionare **+Aggiungi grafico**.
   > 4. Selezionare la percentuale DTU nel grafico.
   > 5. Inoltre, selezionare **Ultime 24 ore** nel menu superiore sinistro e impostare l'opzione su un minuto.
   >
   > Usare il grafico DTU personalizzato con un maggiore livello di dettaglio per eseguire confronti con il grafico di esecuzione delle query.

   La griglia inferiore mostra informazioni aggregate per le query visibili:

   * ID query, ovvero un identificatore univoco per la query nel database.
   * Uso della CPU per query durante un intervallo osservabile, che dipende dalla funzione di aggregazione.
   * Durata per ogni query, che anche in questo caso dipende dalla funzione di aggregazione.
   * Numero totale di esecuzioni per una query specifica.

2. Se i dati non sono aggiornati, selezionare il pulsante **Aggiorna**.

3. Usare i dispositivi di scorrimento e i pulsanti dello zoom per modificare l'intervallo di osservazione ed esaminare i picchi nel consumo:

   ![Dispositivi di scorrimento e pulsanti dello zoom per modificare l'intervallo](./media/sql-database-query-performance/zoom.png)

4. Facoltativamente, è possibile selezionare la scheda **Personalizzato** per personalizzare la visualizzazione per:

   * Metrica (CPU, durata, conteggio delle esecuzioni).
   * Intervallo di tempo (ultime 24 ore, ultima settimana, ultimo mese).
   * Numero di query.
   * Funzione di aggregazione.
  
   ![Scheda Personalizzato](./media/sql-database-query-performance/custom-tab.png)
  
5. Selezionare il pulsante **Vai >** per accedere alla visualizzazione personalizzata.

   > [!IMPORTANT]
   > Informazioni dettagliate prestazioni query supporta la visualizzazione solo delle prime 5-20 query a livello di uso, a seconda della selezione. Il database può eseguire molte altre query oltre a quelle principali visualizzate e tali query non verranno incluse nel grafico.
   >
   > Potrebbe essere presente un tipo di carico di lavoro del database in cui numerose query di piccole dimensioni, oltre a quelle principali visualizzate, vengono eseguite di frequente e usano la maggior parte delle DTU. Queste query non vengono mostrate nel grafico delle prestazioni.
   >
   > Ad esempio, una query potrebbe aver usato una notevole quantità di DTU per un certo periodo di tempo, anche se il consumo totale nel periodo osservato è minore di altre query principali. In tal caso, l'uso delle risorse di questa query non sarà visibile nel grafico.
   >
   > Se è necessario esaminare le esecuzioni delle query principali oltre i limiti di Informazioni dettagliate prestazioni query, è consigliabile usare [Analisi SQL di Azure](../azure-monitor/insights/azure-sql.md) per funzionalità avanzate di monitoraggio e risoluzione dei problemi delle prestazioni dei database.
   >

## <a name="view-individual-query-details"></a>Visualizzare i dettagli delle singole query

Per visualizzare i dettagli relativi alle query:

1. Selezionare qualsiasi query nell'elenco delle query principali.

    ![Elenco delle query principali](./media/sql-database-query-performance/details.png)

   Verrà aperta una visualizzazione dettagliata. Tale visualizzazione mostra l'uso della CPU, la durata e il conteggio delle esecuzioni nel corso del tempo.

2. Selezionare le funzionalità del grafico per informazioni dettagliate.

   * Il grafico superiore mostra una linea con la percentuale DTU complessiva per il database. Le barre rappresentano la percentuale CPU usata dalla query selezionata.
   * Nel secondo grafico viene mostrata la durata totale della query selezionata.
   * Nel grafico in fondo viene mostrato il numero totale delle esecuzioni effettuate dalla query selezionata.

   ![Dettagli sulle query](./media/sql-database-query-performance/query-details.png)

3. Facoltativamente, usare i dispositivi di scorrimento, i pulsanti dello zoom oppure fare clic su **Impostazioni** per personalizzare la modalità di visualizzazione dei dati della query o per mostrare un intervallo di tempo diverso.

   > [!IMPORTANT]
   > Informazioni dettagliate prestazioni query non acquisisce le query DDL. In alcuni casi, potrebbe non acquisire tutte le query ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Esaminare le query principali in base alla durata

Due metriche in Informazioni dettagliate prestazioni query consentono di individuare i potenziali colli di bottiglia: durata e conteggio delle esecuzioni.

Le query con esecuzione prolungata hanno le maggiori probabilità di bloccare gli altri utenti e le risorse più a lungo, nonché di limitare la scalabilità. Sono anche i candidati ideali per l'ottimizzazione.

Per identificare le query con esecuzione prolungata:

1. Aprire la scheda **Personalizzato** in Informazioni dettagliate prestazioni query per il database selezionato.
2. Impostare le metriche su **Durata**.
3. Selezionare il numero di query e l'intervallo di osservazione.
4. Selezionare la funzione di aggregazione:

   * **Somma** esegue la somma del tempo di esecuzione di tutte le query durante l'intero intervallo di osservazione.
   * **Max** individua le query con il tempo di esecuzione massimo durante l'intero intervallo di osservazione.
   * **Media** rileva il tempo medio di esecuzione di tutte le query e mostra i valori medi più alti tra quelli rilevati.

   ![Durata delle query](./media/sql-database-query-performance/top-duration.png)

5. Selezionare il pulsante **Vai >** per accedere alla visualizzazione personalizzata.

   > [!IMPORTANT]
   > Regolando la visualizzazione della query, la linea DTU non viene aggiornata. La linea DTU mostra sempre il valore massimo di consumo per l'intervallo.
   >
   > Per comprendere più in dettaglio il consumo di DTU del database (fino a 1 minuto), è consigliabile creare un grafico personalizzato nel portale di Azure:
   >
   > 1. Selezionare**Monitoraggio** **database** > SQL di Azure .
   > 2. Selezionare **Metriche**.
   > 3. Selezionare **+Aggiungi grafico**.
   > 4. Selezionare la percentuale DTU nel grafico.
   > 5. Inoltre, selezionare **Ultime 24 ore** nel menu superiore sinistro e impostare l'opzione su un minuto.
   >
   > È consigliabile usare il grafico DTU personalizzato per eseguire un confronto con il grafico delle prestazioni delle query.
   >

## <a name="review-top-queries-per-execution-count"></a>Esaminare le query principali in base al conteggio delle esecuzioni

Un'applicazione utente che usa il database potrebbe rallentare, anche se un numero elevato di esecuzioni potrebbe non influire sul database stesso e l'uso delle risorse è limitato.

In alcuni casi, un conteggio di esecuzioni molto elevato potrebbe causare l'aumento dei round trip di rete. I round trip influiscono sulle prestazioni. Sono soggetti alla latenza di rete e alla latenza di server downstream.

Ad esempio, molti siti Web basati sui dati accedono in maniera massiccia al database per tutte le richieste dell'utente. Anche se il pool di connessioni è di supporto, il traffico di rete aumentato e il carico di elaborazione sul server di database possono rallentare le prestazioni. In generale, mantenere i round trip al minimo.

Per identificare le query eseguite di frequente ("chatty):To identify frequently executed ("chatty") queries:

1. Aprire la scheda **Personalizzato** in Informazioni dettagliate prestazioni query per il database selezionato.
2. Impostare le metriche su **Conteggio delle esecuzioni**.
3. Selezionare il numero di query e l'intervallo di osservazione.
4. Selezionare il pulsante **Vai >** per accedere alla visualizzazione personalizzata.

   ![Conteggio delle esecuzioni delle query](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Informazioni sulle annotazioni di ottimizzazione delle prestazioni

Durante l'esplorazione del carico di lavoro in Informazioni dettagliate prestazioni query, è possibile notare la presenza di icone con una linea verticale nella parte superiore del grafico.

Queste icone sono annotazioni. Mostrano raccomandazioni sulle prestazioni da [Advisor per database SQL](sql-database-advisor.md). Passando il puntatore su un'annotazione, è possibile ottenere informazioni riepilogative sulle raccomandazioni per le prestazioni.

   ![Annotazione delle query](./media/sql-database-query-performance/annotation.png)

Per ottenere altre informazioni o applicare le raccomandazioni di Advisor, selezionare l'icona per aprire i dettagli dell'azione consigliata. Se si tratta di una raccomandazione attiva, è possibile applicarla direttamente dal portale.

   ![Dettagli delle annotazioni delle query](./media/sql-database-query-performance/annotation-details.png)

In alcuni casi, a causa del livello di zoom, è possibile che le annotazioni vicine tra loro vengano compresse in una singola annotazione. Informazioni dettagliate prestazioni query rappresenta gli elementi come un'icona di annotazione di gruppo. Selezionando l'icona di un'annotazione di gruppo, viene aperto un nuovo pannello che elenca le annotazioni.

Correlare le query e le azioni di ottimizzazione delle prestazioni può servire ad avere una migliore comprensione del carico di lavoro.

## <a name="optimize-the-query-store-configuration"></a>Ottimizzare la configurazione dell'archivio queryOptimize the Query Store configuration

Durante l'uso di Informazioni dettagliate prestazioni query, possono essere visualizzati messaggi di errore di Query Store simili ai seguenti:

* "Archivio query non è correttamente configurato in questo database. Per altre informazioni, fare clic qui."
* "Archivio query non è correttamente configurato in questo database. Fare clic qui per modificare le impostazioni."

Questi messaggi in genere vengono visualizzati quando Query Store non è in grado di raccogliere nuovi dati.

Il primo caso si verifica quando Query Store è in stato di sola lettura e i parametri sono impostati in modo ottimale. È possibile risolvere il problema aumentando le dimensioni dell'archivio dati o svuotando Query Store. Se si svuota Query Store, tutti i dati di telemetria raccolti in precedenza andranno persi.

   ![Dettagli di Query Store](./media/sql-database-query-performance/qds-off.png)

Il secondo caso si verifica quando Query Store non è abilitato o se i parametri non sono impostati in modo ottimale. È possibile modificare i criteri di conservazione e acquisizione e anche abilitare Query Store eseguendo i comandi seguenti forniti da [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) o dal portale di Azure.

### <a name="recommended-retention-and-capture-policy"></a>Criteri di conservazione e acquisizione consigliati

Esistono due tipi di criteri di conservazione:

* **Basato sulle dimensioni**: se questo criterio è impostato su **AUTO**, pulirà automaticamente i dati quando viene raggiunta la dimensione massima vicina.
* **Basato sul tempo:** per impostazione predefinita, questo criterio è impostato su 30 giorni. Se Query Store esaurisce lo spazio, verranno eliminate le informazioni sulle query più vecchie di 30 giorni.

È possibile impostare i criteri di acquisizione su:

* **All**: Query Store acquisisce tutte le query.
* **Automatico:** Query Store ignora le query e le query poco frequenti con durata di compilazione ed esecuzione insignificante. Le soglie per il conteggio delle esecuzioni e la durata di compilazione ed esecuzione vengono stabilite internamente. Questa è l'opzione predefinita.
* **Nessuno:** Query Store interrompe l'acquisizione di nuove query, ma le statistiche di runtime per le query già acquisite vengono comunque raccolte.

È consigliabile impostare tutti i criteri su **AUTOMATICO** e i criteri di pulizia su 30 giorni eseguendo i comandi seguenti da [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) o dal portale di Azure. Sostituire `YourDB` con il nome del database.

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Per aumentare le dimensioni di Query Store, connettersi a un database tramite [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) o il portale di Azure ed eseguire la query seguente. Sostituire `YourDB` con il nome del database.

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Applicando queste impostazioni, Query Store raccoglierà i dati di telemetria per le nuove query. Se è necessario che Query Store sia subito operativo, è possibile scegliere di svuotare Query Store eseguendo la query seguente tramite SSMS o il portale di Azure. Sostituire `YourDB` con il nome del database.

> [!NOTE]
> Eseguendo la query seguente, verranno eliminati tutti i dati di telemetria monitorati raccolti in precedenza in Query Store.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Passaggi successivi

Prendi in considerazione l'uso di [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) per il monitoraggio avanzato delle prestazioni di un'ampia flotta di database singoli e in pool, pool elastici, istanze gestite e database di istanze.
