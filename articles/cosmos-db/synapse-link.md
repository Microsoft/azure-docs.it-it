---
title: Vantaggi di Collegamento ad Azure Synapse per Azure Cosmos DB e quando usare il servizio
description: Informazioni su Collegamento ad Azure Synapse per Azure Cosmos DB. Collegamento a Synapse consente di eseguire analisi quasi in tempo reale (HTAP) usando Azure Synapse Analytics su dati operativi in Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 123c443e1afaf8eaded7021b963b68b3d8a8f554
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483759"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Che cos'è Collegamento ad Azure Synapse per Azure Cosmos DB?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Collegamento ad Azure Synapse per Azure Cosmos DB è una funzionalità HTAP (Hybrid Transactional and Analytical Processing) nativa del cloud che consente di eseguire analisi quasi in tempo reale su dati operativi in Azure Cosmos DB. Collegamento ad Azure Synapse crea una stretta integrazione tra Azure Cosmos DB e Azure Synapse Analytics.

Usando l'[archivio analitico di Azure Cosmos DB](analytical-store-introduction.md), un archivio a colonne completamente isolato, Collegamento a Synapse consente di evitare l'analisi ETL (Extract-Transform-Load, estrazione, trasformazione e caricamento) in [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) su dati operativi su larga scala. Gli analisti aziendali, gli ingegneri dei dati e gli scienziati dei dati possono ora usare Synapse Spark o Synapse SQL in modo intercambiabile per eseguire attività di business intelligence, analisi e pipeline di Machine Learning quasi in tempo reale. Questo risultato si ottiene senza influire sulle prestazioni dei carichi di lavoro transazionali in Azure Cosmos DB. 

L'immagine seguente mostra l'integrazione di Collegamento ad Azure Synapse con Azure Cosmos DB e Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Diagramma dell'architettura per l'integrazione di Azure Synapse Analytics con Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Vantaggi

Per analizzare set di dati operativi di grandi dimensioni riducendo al minimo l'impatto sulle prestazioni dei carichi di lavoro transazionali cruciali, solitamente i dati operativi di Azure Cosmos DB vengono estratti ed elaborati da pipeline ETL (Extract-Transform-Load, estrazione, trasformazione e caricamento). Le pipeline ETL richiedono molti livelli di spostamento dei dati, il che genera una complessità operativa elevata e un impatto sulle prestazioni dei carichi di lavoro transazionali. L'analisi di dati operativi dal momento in cui vengono generati comporta inoltre un aumento della latenza.

Rispetto alle soluzioni tradizionali basate su ETL, Collegamento ad Azure Synapse per Azure Cosmos DB offre diversi vantaggi, tra cui:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Riduzione della complessità senza processi ETL da gestire

Collegamento ad Azure Synapse consente di accedere direttamente all'archivio analitico di Azure Cosmos DB tramite Azure Synapse Analytics senza complessi spostamenti di dati. Tutti gli aggiornamenti apportati ai dati operativi sono visibili nell'archivio analitico quasi in tempo reale senza ETL o processi di feed di modifiche. È possibile eseguire analisi su larga scala su archivi analitici, Azure Synapse Analytics, senza alcuna trasformazione dei dati aggiuntiva.

### <a name="near-real-time-insights-into-your-operational-data"></a>Informazioni dettagliate quasi in tempo reale sui dati operativi

È ora possibile ottenere informazioni dettagliate sui dati operativi quasi in tempo reale usando Collegamento ad Azure Synapse. I sistemi basati su ETL tendono ad avere una latenza più elevata per l'analisi dei dati operativi, a causa dei molti livelli necessari per estrarli, trasformarli e caricarli. Grazie all'integrazione nativa dell'archivio analitico di Azure Cosmos DB con Azure Synapse Analytics, è possibile analizzare i dati operativi quasi in tempo reale, rendendo possibili nuovi scenari aziendali. 

### <a name="no-impact-on-operational-workloads"></a>Nessun impatto sui carichi di lavoro operativi

Con Collegamento a Synapse, è possibile eseguire query analitiche sull'archivio analitico di Azure Cosmos DB (un archivio a colonne separato) durante l'elaborazione delle operazioni transazionali, usando una velocità effettiva con provisioning per il carico di lavoro transazionale (un archivio transazionale basato su righe).  Il carico di lavoro analitico viene gestito indipendentemente dal traffico del carico di lavoro transazionale senza consumare la velocità effettiva con provisioning dei dati operativi.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Ottimizzazione per carichi di lavoro analitici su larga scala

L'archivio analitico di Azure Cosmos DB è ottimizzato per offrire scalabilità, elasticità e prestazioni per carichi di lavoro analitici senza alcuna dipendenza dai runtime di calcolo. La tecnologia di archiviazione è gestita automaticamente per ottimizzare i carichi di lavoro analitici. Grazie al supporto incorporato in Azure Synapse Analytics, l'accesso al livello di archiviazione assicura semplicità e prestazioni elevate.

### <a name="cost-effective"></a>Conveniente

Con Collegamento ad Azure Synapse, si ottiene una soluzione per l'analisi operativa completamente gestita e ottimizzata per i costi. La soluzione elimina i livelli aggiuntivi di archiviazione e calcolo necessari nelle tradizionali pipeline ETL per l'analisi dei dati operativi. 

L'archivio analitico di Azure Cosmos DB segue un modello di determinazione dei prezzi a consumo, in base all'archiviazione dei dati e al numero di operazioni di lettura/scrittura e di query di analisi eseguite. Non è necessario effettuare il provisioning di una velocità effettiva, come è attualmente richiesto per i carichi di lavoro transazionali. L'accesso ai dati con motori di calcolo estremamente elastici da Azure Synapse Analytics aumenta in modo significativo l'efficienza dei costi complessivi associati alla gestione delle risorse di archiviazione e di calcolo.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analisi per scritture in più aree disponibili a livello locale e distribuite a livello globale

È possibile eseguire efficacemente query analitiche sulla copia locale dei dati più vicina in Azure Cosmos DB. Azure Cosmos DB include la funzionalità all'avanguardia per l'esecuzione dei carichi di lavoro analitici distribuiti a livello globale oltre che dei carichi di lavoro transazionali in modalità attiva/attiva.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Abilitare scenari HTAP per i dati operativi

Collegamento a Synapse raggruppa l'archivio analitico di Azure Cosmos DB con il supporto del runtime analitico di Azure Synapse. Grazie a questa integrazione, è possibile sviluppare soluzioni HTAP (Hybrid Transactional/Analytical Processing) native del cloud che generano informazioni dettagliate basate sugli aggiornamenti in tempo reale dei dati operativi su set di dati di grandi dimensioni. È così possibile realizzare nuovi scenari aziendali per generare avvisi basati su tendenze dinamiche, creare dashboard quasi in tempo reale e definire esperienze aziendali basate sul comportamento degli utenti.

### <a name="azure-cosmos-db-analytical-store"></a>Archivio analitico di Azure Cosmos DB

L'archivio analitico di Azure Cosmos DB è una rappresentazione orientata alle colonne dei dati operativi disponibili in Azure Cosmos DB. Questo archivio analitico è indicato per eseguire query rapide e a costi contenuti su set di dati operativi di grandi dimensioni, senza copiare i dati e senza influire sulle prestazioni dei carichi di lavoro transazionali.

L'archivio analitico preleva automaticamente inserimenti, aggiornamenti ed eliminazioni ad alta frequenza nei carichi di lavoro transazionali quasi in tempo reale, come funzionalità completamente gestita (sincronizzazione automatica) di Azure Cosmos DB. Non sono necessari feed di modifiche o processi ETL. 

Se si ha un account Azure Cosmos DB distribuito a livello globale, un archivio analitico abilitato per un contenitore sarà disponibile in tutte le aree per tale account. Per altre informazioni sull'archivio analitico, vedere l'articolo [Panoramica dell'archivio analitico di Azure Cosmos DB](analytical-store-introduction.md).

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integrazione con Azure Synapse Analytics

Con Collegamento a Synapse è ora possibile connettersi direttamente ai contenitori Azure Cosmos DB da Azure Synapse Analytics e accedere all'archivio analitico senza connettori separati. Azure Synapse Analytics attualmente supporta Collegamento a Synapse con [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) pool SQL [serverless.](../synapse-analytics/sql/on-demand-workspace-overview.md)

È possibile eseguire query sui dati dell'archivio analitico di Azure Cosmos DB simultaneamente, con interoperabilità tra diversi runtime di analisi supportati da Azure Synapse Analytics. Per analizzare i dati operativi, non è necessario eseguire trasformazioni aggiuntive. È possibile eseguire query e analizzare i dati dell'archivio analitico tramite:

* Synapse Apache Spark con supporto completo per Scala, Python, SparkSQL e C#. Synapse Spark è fondamentale per gli scenari di ingegneria dei dati e data science

* Pool SQL serverless con linguaggio T-SQL e supporto per strumenti di business intelligence familiari(ad esempio, Power BI Premium e così via)

> [!NOTE]
> Da Azure Synapse Analytics è possibile accedere agli archivi analitici e transazionali nel contenitore Azure Cosmos DB. Se tuttavia si vuole eseguire un'analisi su larga scala dei dati operativi, è consigliabile usare l'archivio analitico per evitare l'impatto sulle prestazioni dei carichi di lavoro transazionali.

> [!NOTE]
> È possibile eseguire analisi a bassa latenza in un'area di Azure connettendo il contenitore Azure Cosmos DB al runtime di Synapse in tale area.

Questa integrazione consente gli scenari HTAP seguenti per utenti diversi:

* Un tecnico di business intelligence che vuole modellare e pubblicare un report Power BI per accedere ai dati operativi in tempo reale in Azure Cosmos DB direttamente tramite Synapse SQL.

* Un analista di dati che vuole ricavare informazioni dettagliate dai dati operativi di un contenitore Azure Cosmos DB eseguendo query con Synapse SQL, leggere i dati su larga scala e combinare i risultati con altre origini dati.

* Uno scienziato dei dati che vuole usare Synapse Spark per trovare una funzionalità che consenta di migliorare il modello e sottoporlo a training senza eseguire operazioni complesse di ingegneria dei dati. I risultati del modello possono anche essere scritti dopo l'inferenza in Azure Cosmos DB per l'assegnazione di punteggi in tempo reale ai dati tramite Spark Synapse.

* Un ingegnere dei dati che vuole rendere i dati accessibili ai consumer creando tabelle SQL o Spark su contenitori Azure Cosmos DB senza processi ETL manuali.

Per altre informazioni sul supporto del runtime di Azure Synapse Analytics per Azure Cosmos DB, vedere [Supporto di Azure Synapse Analytics per Cosmos DB](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Quando usare Collegamento ad Azure Synapse per Azure Cosmos DB?

L'uso di Collegamento a Synapse è consigliato nei casi seguenti:

* Per i clienti di Azure Cosmos DB che vogliono eseguire attività di analisi, BI e Machine Learning sui dati operativi. In questi casi, Collegamento a Synapse offre un'esperienza di analisi maggiormente integrata senza influire sulla velocità effettiva con provisioning dell'archivio transazionale. Ad esempio:

  * Si eseguono attività di analisi o BI sui dati operativi di Azure Cosmos DB direttamente usando connettori separati

  * Si eseguono processi ETL per estrarre dati operativi in un sistema di analisi distinto.
 
In questi casi, Collegamento a Synapse offre un'esperienza di analisi maggiormente integrata senza influire sulla velocità effettiva con provisioning dell'archivio transazionale.

L'uso di Collegamento a Synapse non è consigliato se è necessario rispettare i requisiti dei tradizionali data warehouse, ad esempio concorrenza elevata, gestione dei carichi di lavoro e persistenza degli aggregati tra più origini dati. Per altre informazioni, vedere gli [scenari comuni che possono essere basati su Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link-use-cases.md).

## <a name="limitations"></a>Limitazioni

* Collegamento ad Azure Synapse per Azure Cosmos DB è supportato per l'API SQL e l'API Azure Cosmos DB per MongoDB. Non è supportata per l'API Gremlin, API Cassandra e API Tabella.

* L'archivio analitico può essere abilitato solo per i nuovi contenitori. Per usare l'archivio analitico per i contenitori esistenti, eseguire la migrazione dei dati dai contenitori esistenti a nuovi contenitori usando gli [Azure Cosmos DB di migrazione.](cosmosdb-migrationchoices.md) È possibile abilitare Collegamento a Synapse per gli account di Azure Cosmos DB esistenti.

* Per i contenitori con archivio analitico attivato, il backup e il ripristino automatici dei dati nell'archivio analitico non sono attualmente supportati. Quando Collegamento a Synapse è abilitata in un account di database, Azure Cosmos DB continuerà a eseguire automaticamente i [backup](./online-backup-and-restore.md) dei dati nell'archivio transazionale (solo) dei contenitori a intervalli di backup pianificati, come sempre. È importante notare che quando un contenitore con l'archivio analitico attivato viene ripristinato in un nuovo account, il contenitore verrà ripristinato con solo l'archivio transazionale e nessun archivio analitico abilitato.

* L'accesso all'archivio analitico di Azure Cosmos DB con Synapse SQL con provisioning non è attualmente disponibile.

## <a name="security"></a>Sicurezza

Collegamento a Synapse consente di eseguire analisi near real-time sui dati cruciali in Azure Cosmos DB. È fondamentale assicurarsi che i dati aziendali critici siano archiviati in modo sicuro in archivi transazionali e analitici. Azure Synapse link per Azure Cosmos DB è progettato per soddisfare questi requisiti di sicurezza tramite le funzionalità seguenti:

* **Isolamento della rete tramite endpoint privati:** è possibile controllare l'accesso alla rete ai dati negli archivi transazionali e analitici in modo indipendente. L'isolamento della rete viene eseguito usando endpoint privati gestiti separati per ogni archivio, all'interno di reti virtuali gestite Azure Synapse aree di lavoro. Per altre informazioni, vedere l'articolo Configurare [endpoint privati per l'archivio analitico.](analytical-store-private-endpoints.md)

* **Crittografia dei dati** con chiavi gestite dal cliente: è possibile crittografare facilmente i dati tra archivi transazionali e analitici usando le stesse chiavi gestite dal cliente in modo automatico e trasparente. Per altre informazioni, vedere l'articolo [Configurare chiavi gestite dal](how-to-setup-cmk.md) cliente.

* Gestione sicura delle **chiavi:** l'accesso ai dati nell'archivio analitico dai pool SQL senza server Synapse Spark e Synapse richiede la gestione Azure Cosmos DB chiavi all'interno Synapse Analytics aree di lavoro. Invece di usare le chiavi Azure Cosmos DB account inline nei processi Spark o negli script SQL, Azure Synapse Link offre funzionalità più sicure.

  * Quando si usano pool SQL senza server Synapse, è possibile eseguire query sull'archivio analitico Azure Cosmos DB pre-creazione di credenziali SQL archiviando le chiavi dell'account e facendo riferimento a tali chiavi nella `OPENROWSET` funzione . Per altre informazioni, vedere [Eseguire query con un pool SQL serverless nell'articolo Azure Synapse Link.](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

  * Quando si usa Synapse Spark, è possibile archiviare le chiavi dell'account in oggetti servizio collegati che puntano a un database Azure Cosmos DB e fare riferimento a questo elemento nella configurazione spark in fase di esecuzione. Per altre informazioni, vedere [Copiare dati in un pool SQL](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md) dedicato usando Apache Spark articolo.


## <a name="pricing"></a>Prezzi

Il modello di fatturazione di Collegamento ad Azure Synapse include i costi sostenuti con l'uso dell'archivio analitico di Azure Cosmos DB e del runtime di Synapse. Per altre informazioni, vedere gli articoli [Prezzi dell'archivio analitico di Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing) e [Prezzi di Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la documentazione seguente:

* [Panoramica dell'archivio analitico di Azure Cosmos DB](analytical-store-introduction.md)

* [Introduzione a Collegamento ad Azure Synapse per Azure Cosmos DB](configure-synapse-link.md)
 
* [Funzionalità supportate dal runtime di Azure Synapse Analytics](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Domande frequenti su Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Casi d'uso di Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link-use-cases.md)
