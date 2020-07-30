---
title: Gestire più database con i pool elastici
description: Gestisci e ridimensiona più database nel database SQL di Azure, centinaia e migliaia, usando i pool elastici. Un unico prezzo per le risorse che è possibile distribuire in base alle esigenze.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
ms.date: 07/28/2020
ms.openlocfilehash: 33f87bf6f030adb48f2c4f8eb45027c1b298d812
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419717"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>I pool elastici consentono di gestire e ridimensionare più database nel database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I pool elastici del database SQL di Azure rappresentano una soluzione semplice e conveniente per la gestione e il ridimensionamento di più database con esigenze di utilizzo variabili e imprevedibili. I database in un pool elastico si trovano in un unico server e condividono un numero impostato di risorse a un prezzo fisso. I pool elastici nel database SQL di Azure consentono agli sviluppatori di SaaS di ottimizzare i costi per un gruppo di database all'interno di un budget definito, garantendo allo stesso tempo prestazioni elastiche per ogni database.

## <a name="what-are-sql-elastic-pools"></a>Definizione di pool elastici SQL

Gli sviluppatori di SaaS compilano applicazioni basate su livelli di dati su larga scala costituiti da più database. Un modello di applicazione comune è il provisioning di un database singolo per ogni cliente. Tuttavia, i diversi clienti hanno spesso modelli di utilizzo variabili e imprevedibili ed è difficile prevedere i requisiti delle risorse di ogni singolo utente del database. In genere, le opzioni erano due:

- Effettuare il provisioning di risorse eccessivo in base all'utilizzo massimo e pagare più del necessario, o
- Ricorrere al provisioning insufficiente per risparmiare sui costi, a scapito delle prestazioni e della soddisfazione del cliente durante i picchi.

I pool elastici risolvono il problema assicurando ai database l'ottenimento delle risorse di prestazioni necessarie, quando richieste. Forniscono un meccanismo semplice di allocazione delle risorse che rientra in un budget prevedibile. Per altre informazioni sui modelli di progettazione per applicazioni SaaS con pool elastici, vedere l'articolo relativo ai [modelli di progettazione per applicazioni SaaS multi-tenant con database SQL di Azure](saas-tenancy-app-design-patterns.md).
>
> [!IMPORTANT]
> Non è previsto alcun costo per database per i pool elastici. Viene fatturata ogni ora in cui un pool esiste con il livello massimo di eDTU o vCore, indipendentemente dall'utilizzo o dal fatto che il pool sia stato attivo per meno di un'ora.

I pool elastici consentono agli sviluppatori di acquistare risorse per un pool condiviso da più database, in modo da supportare periodi di utilizzo imprevisti da parte dei singoli database. È possibile configurare le risorse per il pool in base al [modello di acquisto basato su DTU](service-tiers-dtu.md) o al [modello di acquisto basato su vCore](service-tiers-vcore.md). Il requisito di risorse per un pool è determinato dall'utilizzo aggregato dei relativi database. La quantità di risorse disponibili per il pool dipende dal budget dello sviluppatore. Lo sviluppatore aggiunge semplicemente i database al pool, imposta facoltativamente le risorse minime e massime per i database (DTU minimo e massimo o Vcore minimo o massimo a seconda del modello di Resourcing scelto), quindi imposta le risorse del pool in base al relativo budget. Utilizzando i pool, lo sviluppatore può aumentare con facilità i servizi offerti da una piccola nuova impresa fino a un'azienda matura in continua crescita.

All'interno del pool i singoli database sono sufficientemente flessibili da assicurare una scalabilità automatica nell'ambito di parametri prefissati. Se il carico di lavoro è importante, un database può utilizzare più risorse per soddisfare la domanda. Se invece il carico di lavoro è più leggero, i database in assenza di carico non utilizzano risorse. La possibilità di effettuare il provisioning delle risorse per l'intero pool e non per i singoli database semplifica le attività di gestione. È inoltre disponibile un budget per il pool prevedibile. È possibile aggiungere altre risorse a un pool esistente con tempi di inattività minimi. Analogamente, se le risorse aggiuntive non sono più necessarie, è possibile rimuoverle da un pool esistente in qualsiasi momento. È possibile aggiungere o rimuovere database dal pool. Se si prevede che un database sottoutilizzerà le proprie risorse, è possibile rimuoverlo.

> [!NOTE]
> Quando si spostano database all'interno o all'esterno di un pool elastico, non si verifica alcun tempo di inattività, ad eccezione di un breve periodo (nell'ordine di secondi) alla fine dell'operazione, quando vengono rilasciate le connessioni al database.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando considerare un pool elastico del database SQL

I pool sono adatti per un numero elevato di database con modelli di utilizzo specifici. Per un determinato database, questo modello è caratterizzato da un utilizzo medio ridotto con picchi di utilizzo relativamente poco frequenti. Viceversa, più database con utilizzo persistente medio-elevato non devono essere inseriti nello stesso pool elastico.

Più database è possibile aggiungere a un pool, maggiori diventano i risparmi. A seconda del modello di utilizzo dell'applicazione, è possibile vedere Risparmi con due database S3.

Le sezioni seguenti aiutano a comprendere se l'insieme specifico di database può trarre vantaggio dall'uso di un pool. Gli esempi utilizzano pool Standard, ma gli stessi principi si applicano anche ai pool Basic e Premium.

### <a name="assessing-database-utilization-patterns"></a>Valutazione dei modelli di utilizzo di database

Nella figura seguente viene illustrato un esempio di un database che trascorre il tempo di inattività, ma anche periodicamente picchi di attività. Si tratta di un modello di utilizzo adatto per un pool:

   ![un database singolo adatto per un pool](./media/elastic-pool-overview/one-database.png)

Per il periodo di cinque minuti illustrato, DB1 raggiunge picchi fino a 90 DTU, ma l'utilizzo medio complessivo è inferiore a cinque DTU. L'esecuzione di questo carico di lavoro in un database singolo richiede dimensioni di calcolo S3, ma in questo modo la maggior parte delle risorse rimane inutilizzata durante i periodi di minore attività.

Un pool consente la condivisione tra più database di queste DTU inutilizzate e quindi riduce le DTU richieste e i costi complessivi.

Compila l'esempio precedente, si supponga che vi sono altri database con i modelli di utilizzo simili come DB1. Nelle due figure seguenti vengono presentati in parallelo l'utilizzo di quattro database e quello di 20 database all'interno di uno stesso grafico per mostrare l'assenza di sovrapposizione degli elementi che rappresentano l'utilizzo dei database nel tempo quando viene usato il modello di acquisto basato su DTU:

   ![quattro database con un modello di utilizzo adatto per un pool](./media/elastic-pool-overview/four-databases.png)

   ![venti database con un modello di utilizzo adatto per un pool](./media/elastic-pool-overview/twenty-databases.png)

Dalla riga di colore nera nella figura precedente viene illustrato l'utilizzo di DTU di aggregazione in tutti i database di 20. Viene illustrato che l'utilizzo di DTU aggregato non mai supera le 100 DTU, ciò indica che i 20 database possono condividere 100 eDTU nel corso di tale periodo di tempo. Questo comporta una riduzione di 20 volte delle DTU e di 13 volte del prezzo rispetto all'inserimento di ogni database in dimensioni di calcolo S3 per singoli database.

In questo esempio è ideale per i motivi seguenti:

- Esistono grandi differenze tra i picchi di utilizzo e l'utilizzo medio per ogni database.
- Il picco di utilizzo per ogni database si verifica in diversi momenti nel tempo.
- Le eDTU vengono condivise tra più database.

Il prezzo di un pool è una funzione delle eDTU del pool. Mentre il prezzo unitario delle eDTU di un pool è 1,5 volte maggiore del prezzo unitario delle DTU per un database singolo, le **eDTU del pool possono essere condivise da molti database ed è necessario un minor numero totale di eDTU**. Queste distinzioni nella determinazione dei prezzi e nella condivisione di eDTU costituiscono la base del potenziale risparmio sul prezzo che il pool è in grado di fornire.

Le seguenti regole relative al numero e all'uso del database consentono di garantire che un pool offra una riduzione dei costi rispetto all'uso di dimensioni di calcolo per database singoli.

### <a name="minimum-number-of-databases"></a>Numero minimo di database

Se la quantità aggregata di risorse per i singoli database è maggiore di 1,5 volte rispetto alle risorse necessarie per il pool, un pool elastico è più conveniente.

***Esempio di modello di acquisto basato su DTU*** Sono necessari almeno due database S3 o almeno 15 database S0 perché un pool di 100 eDTU risulti più conveniente rispetto all'uso di dimensioni di calcolo per database singoli.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Numero massimo di picco contemporaneamente database

Condividendo le risorse, non tutti i database in un pool possono usare contemporaneamente le risorse fino al limite disponibile per i singoli database. Meno database raggiungono il picco contemporaneamente, minore è il valore da impostare per le risorse del pool e quindi più redditizio diventa il pool stesso. In generale, non più di 2/3 (o del 67%) dei database nel pool dovrebbe raggiungere contemporaneamente il picco di utilizzo delle risorse.

***Esempio di modello di acquisto basato su DTU*** Per ridurre i costi per tre database S3 in un pool di eDTU 200, al massimo due di questi database possono aumentare contemporaneamente il loro utilizzo. In caso contrario, se più di due di questi quattro database S3 raggiungono il picco contemporaneamente, il pool dovrebbe essere ridimensionato a più di 200 eDTU. Se il pool viene ridimensionato a più di 200 eDTU, più database S3 dovranno essere aggiunti al pool per mantenere i costi inferiori rispetto a quelli delle dimensioni di calcolo per singoli database.

Si noti che questo esempio non considera l'utilizzo di altri database nel pool. Se tutti i database con alcune utilizzo in qualsiasi punto nel tempo, minore di 2/3 (o 67%) dei database possono picco contemporaneamente.

### <a name="resource-utilization-per-database"></a>Utilizzo delle risorse per ogni database

Una notevole differenza tra il picco e l'utilizzo medio di un database indica periodi prolungati di utilizzo ridotto e brevi periodi di utilizzo elevato. Questo modello di utilizzo è ideale per la condivisione delle risorse tra database. Un database deve essere considerato per un pool quando relativo picchi di utilizzo sono circa 1.5 volte maggiore relativo utilizzo medio.

***Esempio di modello di acquisto basato su DTU*** Un database S3 con picchi di 100 DTU e in media utilizza 67 DTU o meno è un buon candidato per la condivisione di edtu in un pool. In alternativa, un database S1 con picchi di 20 DTU e utilizzo medio di 13 DTU o meno è un buon candidato per un pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Come scegliere le dimensioni più adatte del pool

La dimensione ottimale per un pool dipende dalle risorse di aggregazione e dalle risorse di archiviazione necessarie per tutti i database nel pool. È quindi necessario stabilire quanto segue:

- Numero massimo di risorse utilizzate da tutti i database nel pool (DTU massimo o Vcore massimo a seconda del modello di acquisto scelto).
- Byte di archiviazione massima utilizzati da tutti i database nel pool.

Per i livelli di servizio e i limiti disponibili per ogni modello di risorsa, vedere il [modello di acquisto basato su DTU](service-tiers-dtu.md) o il [modello di acquisto basato su vCore](service-tiers-vcore.md).

I passaggi seguenti consentono di stimare se un pool è più conveniente rispetto ai database singoli:

1. Stimare le eDTU o i vCore necessari per il pool come segue:

Per il modello di acquisto basato su DTU:

MAX (<*numero totale di* database x *utilizzo medio di DTU per database*>, <*numero di database con picco contemporaneamente* x picco di *utilizzo DTU per database*)

Per il modello di acquisto basato su vCore:

MAX (<*numero totale di* database x *utilizzo medio di vCore per database*>, <*numero di database con picco contemporaneamente* x picco di *utilizzo vCore per database*)

2. Stimare lo spazio di archiviazione necessario per il pool aggiungendo il numero di byte necessari per tutti i database nel pool. Determinare quindi la dimensione del pool in eDTU che fornisce la quantità di spazio di archiviazione.
3. Per il modello di acquisto basato su DTU, considerare la stima di eDTU maggiore tra il Passaggio 1 e il Passaggio 2. Per il modello di acquisto basato su vCore, considerare la stima di vCore del Passaggio 1.
4. Vedere la [pagina sui prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/) e trovare la dimensione di pool più piccola, che sia maggiore della stima del Passaggio 3.
5. Confrontare il prezzo di pool ottenuto nel passaggio 5 con il prezzo dell'uso di dimensioni di calcolo appropriate per database singoli.

> [!IMPORTANT]
> Se il numero di database in un pool si avvicina al valore massimo supportato, assicurarsi di considerare la [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md).

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Uso di altre funzionalità del database SQL con i pool elastici

### <a name="elastic-jobs-and-elastic-pools"></a>Processi e pool elastici

L'uso di un pool permette di semplificare le attività di gestione con l'esecuzione di script in **[processi elastici](elastic-jobs-overview.md)**. Un processo elastico elimina la maggior parte delle attività ripetitive associate a un elevato numero di database.

Per altre informazioni sugli altri strumenti di database per usare più database, vedere [Aumentare il numero di istanze con il database SQL di Azure](elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opzioni di continuità aziendale per i database in un pool elastico

I database in pool supportano in genere le stesse [funzionalità di continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md) disponibili per i singoli database.

- **Ripristino temporizzato**

  Il ripristino temporizzato usa i backup automatici del database per ripristinare un database in un pool a un punto specifico nel tempo. Vedere [Ripristino temporizzato](recovery-using-backups.md#point-in-time-restore)

- **Ripristino geografico**

  Il ripristino geografico fornisce un'opzione predefinita di ripristino quando un database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](disaster-recovery-guidance.md)

- **Replica geografica attiva**

  Per le applicazioni che hanno requisiti di ripristino più elevati di quelli supportati dal ripristino geografico, configurare la [replica geografica attiva](active-geo-replication-overview.md) o un [gruppo di failover automatico](auto-failover-group-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Creare un nuovo pool elastico del database SQL tramite il portale di Azure

Esistono due modi per creare un pool elastico nel portale di Azure.

1. Passare alla [portale di Azure](https://portal.azure.com) per creare un pool elastico. Cercare e selezionare **Azure SQL**.
2. Selezionare **+Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive sui pool elastici selezionando **Mostra dettagli** nel riquadro **database** .
3. Nel riquadro **database** selezionare **pool elastico** nell'elenco a discesa **tipo di risorsa** e quindi selezionare **Crea**:

   ![Creare un pool elastico](./media/elastic-pool-overview/create-elastic-pool.png)

4. In alternativa, è possibile creare un pool elastico passando a un server esistente e facendo clic su **+ nuovo pool** per creare un pool direttamente in tale server.

> [!NOTE]
> È possibile creare più pool in un server, ma non aggiungere database da diversi server nello stesso pool.

Il livello di servizio del pool determina le funzionalità disponibili per i database elastici nel pool e la quantità massima di risorse disponibili per ogni database. Per informazioni dettagliate, vedere i limiti delle risorse per i pool elastici nel [modello basato su DTU](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Per i limiti delle risorse basate su vCore per i pool elastici, vedere [Limiti di risorse basate su vCore - pool elastici](resource-limits-vcore-elastic-pools.md).

Per configurare le risorse e i prezzi del pool, fare clic su **Configura pool**. Selezionare quindi un livello di servizio, aggiungere database al pool e configurare i limiti delle risorse per il pool e i relativi database.

Al termine della configurazione del pool, è possibile fare clic su Applica, assegnare un nome al pool e fare clic su OK per creare il pool.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorare un pool elastico e i relativi database

Dal portale di Azure è possibile monitorare l'uso di un pool elastico e dei database al suo interno. È anche possibile apportare un set di modifiche al pool elastico e inviare tutte le modifiche contemporaneamente. Le modifiche includono l'aggiunta o la rimozione di database, la modifica delle impostazioni del pool elastico o la modifica delle impostazioni del database.

Per avviare il monitoraggio del pool elastico, trovare e aprire un pool elastico nel portale. Verrà visualizzata una schermata che offre una panoramica dello stato del pool elastico. ad esempio:

- Grafici di monitoraggio che illustrano l'utilizzo delle risorse del pool elastico
- Avvisi recenti e suggerimenti, se disponibili, per il pool elastico

L'immagine seguente illustra un esempio di pool elastico:

![Visualizzazione del pool](./media/elastic-pool-overview/basic.png)

Per visualizzare altre informazioni relative al pool è possibile fare clic su una qualsiasi delle informazioni disponibili in questa panoramica. Facendo clic sul grafico **Utilizzo risorse**, viene aperta la visualizzazione di Monitoraggio di Azure in cui è possibile personalizzare le metriche e l'intervallo di tempo visualizzati nel grafico. Facendo clic su una qualsiasi delle notifiche disponibili, viene visualizzato un pannello che mostra tutti i dettagli dell'avviso o del suggerimento.

Per monitorare i database all'interno del pool, è possibile fare clic su **Utilizzo risorse database** nella sezione **Monitoraggio** del menu delle risorse a sinistra.

![Pagina Utilizzo risorse database](./media/elastic-pool-overview/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Per personalizzare la visualizzazione del grafico

È possibile modificare il grafico e la pagina Metrica per visualizzare altre metriche, ad esempio la percentuale di CPU, la percentuale di IO dei dati e la percentuale di IO del log usata.

Nel modulo **Modifica grafico** è possibile specificare un intervallo di tempo fisso, oppure fare clic su **personalizzato** per selezionare un periodo qualsiasi di 24 ore nelle ultime due settimane, e quindi selezionare le risorse da monitorare.

### <a name="to-select-databases-to-monitor"></a>Per selezionare i database da monitorare

Per impostazione predefinita, il grafico nel pannello **Utilizzo risorse database** mostra i cinque database più utilizzati in base a DTU o CPU, a seconda del livello di servizio. È possibile visualizzare i dati relativi ad altri database nel grafico selezionando e deselezionando i database dall'elenco sottostante tramite le caselle di controllo a sinistra.

È inoltre possibile selezionare altre metriche da visualizzare in modalità affiancata in questa tabella di database per ottenere una visione più dettagliata delle prestazioni dei database.

Per altre informazioni, vedere [Usare il portale di Azure per creare avvisi per il database SQL di Azure](alerts-insights-configure-portal.md).

## <a name="customer-case-studies"></a>Case study dei clienti

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart ha usato i pool elastici con il database SQL di Azure per espandere rapidamente i servizi aziendali con una frequenza di 1.000 nuovi database SQL di Azure al mese.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco usa i pool elastici con il database SQL di Azure per eseguire rapidamente il provisioning e la scalabilità dei servizi per migliaia di tenant nel cloud.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Daxko/CSI usa i pool elastici con il database SQL di Azure per accelerare il ciclo di sviluppo e migliorare le prestazioni e i servizi dei clienti.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui prezzi, vedere [prezzi dei pool elastici](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Per ridimensionare i pool elastici, vedere [Ridimensionamento dei pool elastici](elastic-pool-scale.md) e [Ridimensionare un pool elastico - codice di esempio](scripts/monitor-and-scale-pool-powershell.md)
- Per altre informazioni sui modelli di progettazione per applicazioni SaaS con pool elastici, vedere l'articolo relativo ai [modelli di progettazione per applicazioni SaaS multi-tenant con database SQL di Azure](saas-tenancy-app-design-patterns.md).
- Per un'esercitazione sul SaaS con i pool elastici, vedere [Introduzione all'applicazione SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md).
- Per informazioni sulla gestione delle risorse in pool elastici con molti database, vedere [gestione delle risorse in pool elastici densi](elastic-pool-resource-management.md).
