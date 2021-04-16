---
title: Domande frequenti su Collegamento ad Azure Synapse per Azure Cosmos DB
description: Risposte alle domande frequenti su Collegamento a Synapse per Azure Cosmos DB in aree come fatturazione, archivio analitico, sicurezza e durata (TTL) nell'archivio analitico.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 906651f8c48824e391879e0a579c6587231e7dfd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483827"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Domande frequenti su Collegamento ad Azure Synapse per Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Collegamento ad Azure Synapse per Azure Cosmos DB crea una stretta integrazione tra Azure Cosmos DB e Azure Synapse Analytics. Consente ai clienti di eseguire analisi quasi in tempo reale sui dati operativi con l'isolamento completo delle prestazioni dai carichi di lavoro transazionali e senza una pipeline ETL. Questo articolo contiene le risposte alle domande frequenti su Collegamento a Synapse per Azure Cosmos DB.

## <a name="general-faq"></a>Domande frequenti di carattere generale

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Il Azure Synapse è supportato per tutte Azure Cosmos DB API?

Collegamento ad Azure Synapse è supportato per l'API (Core) SQL di Azure Cosmos DB e per l'API Azure Cosmos DB per MongoDB. 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>Il Azure Synapse è supportato per gli account Azure Cosmos DB più aree?

Sì, per gli account Azure Cosmos in più aree, anche i dati archiviati nell'archivio analitico vengono distribuiti globalmente. Sia in aree di scrittura singole che in più aree di scrittura, le query analitiche eseguite da Azure Synapse Analytics possono essere gestite dall'area locale più vicina.

Quando si pianifica la configurazione di un account Azure Cosmos DB più aree con il supporto dell'archivio analitico, è consigliabile aggiungere tutte le aree necessarie al momento della creazione dell'account.

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>È possibile scegliere di abilitare Azure Synapse collegamento solo per una determinata area e non per tutte le aree in una configurazione di account in più aree?

Quando Azure Synapse è abilitato per un account in più aree, l'archivio analitico viene creato in tutte le aree. I dati sottostanti sono ottimizzati per la velocità effettiva e per la coerenza transazionale nell'archivio transazionale.

### <a name="is-analytical-store-supported-in-all-azure-cosmos-db-regions"></a>L'archivio analitico è supportato in tutte Azure Cosmos DB geografiche?

Sì.

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Il backup e il ripristino sono supportati per Azure Synapse account abilitati per il collegamento?

Per i contenitori con l'archivio analitico attivato, il backup e il ripristino automatici dei dati nell'archivio analitico non sono attualmente supportati. 

Quando Collegamento a Synapse è abilitata in un account di database, Azure Cosmos DB continuerà a eseguire automaticamente i [backup](./online-backup-and-restore.md) dei dati nell'archivio transazionale (solo) dei contenitori a intervalli di backup pianificati, come sempre. È importante notare che quando un contenitore con l'archivio analitico attivato viene ripristinato in un nuovo account, il contenitore verrà ripristinato con solo l'archivio transazionale e nessun archivio analitico abilitato. 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>È possibile disabilitare la funzionalità Azure Synapse collegamento per l'account Azure Cosmos DB personale?

Attualmente, dopo aver abilitato la funzionalità Collegamento a Synapse a livello di account, non è possibile disabilitarla. Tenere presente che, se si abilita la funzionalità Collegamento a Synapse a livello di account ma non sono disponibili contenitori abilitati per l'archivio analitico, non si verificano implicazioni in termini di fatturazione.

Se è necessario disattivare la funzionalità, sono disponibili due opzioni. La prima consiste nell'eliminare e ricreare un nuovo account di Azure Cosmos DB, eseguendo la migrazione dei dati, se necessario. La seconda opzione consiste nell'aprire un ticket di supporto per ottenere supporto sulla migrazione dei dati a un altro account.

### <a name="does-analytical-store-have-any-impact-on-cosmos-db-transactional-slas"></a>L'archivio analitico ha alcun impatto Cosmos DB contratti di servizio transazionali?

No, non c'è alcun impatto.

## <a name="azure-cosmos-db-analytical-store"></a>Archivio analitico di Azure Cosmos DB

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>È possibile abilitare l'archivio analitico nei contenitori esistenti?

Attualmente, l'archivio analitico può essere abilitato solo per i nuovi contenitori (in account nuovi ed esistenti).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>È possibile disabilitare l'archivio analitico nei contenitori Azure Cosmos DB dopo l'abilitazione durante la creazione del contenitore?

Attualmente l'archivio analitico non può essere disabilitato dopo che è stato abilitato durante la creazione di un contenitore Azure Cosmos DB.

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>L'archivio analitico è supportato per Azure Cosmos DB contenitori con velocità effettiva con provisioning a scalabilità automatica?

Sì, l'archivio analitico può essere abilitato nei contenitori con velocità effettiva con provisioning a scalabilità automatica.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Si verificano effetti sulle unità richiesta con provisioning dell'archivio transazionale di Azure Cosmos DB?

Azure Cosmos DB garantisce l'isolamento delle prestazioni tra carichi di lavoro transazionali e analitici. L'abilitazione dell'archivio analitico in un contenitore non influirà sulle UR/s con provisioning nell'archivio transazionale di Azure Cosmos DB. I costi delle transazioni (lettura e scrittura) e dell'archiviazione per l'archivio analitico verranno addebitati separatamente. Per altri dettagli, vedere i [prezzi dell'archivio analitico di Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing).

### <a name="can-i-restrict-network-access-to-azure-cosmos-db-analytical-store"></a>È possibile limitare l'accesso alla rete Azure Cosmos DB archivio analitico?

Sì è possibile configurare un [endpoint privato gestito e](analytical-store-private-endpoints.md) limitare l'accesso alla rete dell'archivio analitico Azure Synapse rete virtuale gestita. Gli endpoint privati gestiti stabiliscono un collegamento privato all'archivio analitico. 

È possibile aggiungere sia endpoint privati dell'archivio transazionale che dell'archivio analitico allo stesso account Azure Cosmos DB in un'area di lavoro Azure Synapse Analytics dati. Se si desidera eseguire solo query analitiche, è possibile abilitare l'endpoint privato analitico solo nell'area Synapse Analytics lavoro.

### <a name="can-i-use-customer-managed-keys-with-the-azure-cosmos-db-analytical-store"></a>È possibile usare chiavi gestite dal cliente con l'Azure Cosmos DB analitico?

È possibile crittografare facilmente i dati negli archivi transazionali e analitici usando le stesse chiavi gestite dal cliente in modo automatico e trasparente. Per usare le chiavi gestite dal cliente con l'archivio analitico, è necessario usare l'identità gestita assegnata dal sistema dell'account Azure Cosmos DB nei criteri di accesso Azure Key Vault dati. Questa operazione è descritta [qui.](how-to-setup-cmk.md#using-managed-identity) Sarà quindi possibile abilitare l'archivio analitico nell'account.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Le operazioni di eliminazione e aggiornamento eseguite nell'archivio transazionale si riflettono nell'archivio analitico?

Sì, le eliminazioni e gli aggiornamenti dei dati nell'archivio transazionale si riflettono nell'archivio analitico. È possibile configurare la durata (TTL) nel contenitore per includere i dati cronologici, in modo che l'archivio analitico conservi tutte le versioni degli elementi che soddisfano i criteri di TTL. Per altri dettagli, vedere la [panoramica della durata (TTL) dell'analisi](analytical-store-introduction.md#analytical-ttl).

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>È possibile connettersi all'archivio analitico da motori di analisi diversi da Azure Synapse Analytics?

È possibile accedere ed eseguire query sull'archivio analitico solo con i vari runtime forniti da Azure Synapse Analytics. È possibile eseguire analisi e query sull'archivio analitico usando:

* Synapse Spark con supporto completo per Scala, Python, SparkSQL e C#. Synapse Spark è fondamentale per gli scenari di ingegneria dei dati e data science
* Pool SQL serverless con linguaggio T-SQL e supporto per gli strumenti di BUSINESS Intelligence familiari (ad esempio, Power BI Premium e così via)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>È possibile connettersi all'archivio analitico da Synapse SQL con provisioning?

Al momento non è possibile accedere all'archivio analitico da Synapse SQL con provisioning.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>È possibile eseguire il writeback dei risultati di aggregazione delle query da Synapse all'archivio analitico?

L'archivio analitico è un archivio di sola lettura in Azure Cosmos DB contenitore. Non è quindi possibile eseguire il writeback dei risultati di aggregazione nell'archivio analitico, ma è possibile scriverli nell'archivio transazionale di Azure Cosmos DB di un altro contenitore, che in seguito può essere sfruttato come livello di servizio.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>La replica con sincronizzazione automatica dall'archivio transazionale all'archivio analitico è asincrona o sincrona e quali sono le latenze?

La latenza di sincronizzazione automatica è in genere entro 2 minuti. Nei casi di database con velocità effettiva condivisa con un numero elevato di contenitori, la latenza di sincronizzazione automatica dei singoli contenitori potrebbe essere superiore e richiedere fino a 5 minuti. Per altre informazioni, vedere come questa latenza si adatta agli scenari. Per questo, contattare il [team di Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Esistono scenari in cui gli elementi dell'archivio transazionale non vengono propagati automaticamente nell'archivio analitico?

Se specifici elementi del contenitore violano lo [schema ben definito per l'analisi](analytical-store-introduction.md#analytical-schema), non verranno inclusi nell'archivio analitico. Se sono presenti scenari bloccati dallo schema ben definito per l'analisi, inviare un messaggio di posta elettronica al [team di Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com) per assistenza.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>È possibile partizionare i dati nell'archivio analitico in modo diverso rispetto all'archivio transazionale?

I dati nell'archivio analitico vengono partizionati in base al partizionamento orizzontale delle partizioni dell'archivio transazionale. Attualmente, non è possibile scegliere una strategia di partizionamento diversa per l'archivio analitico.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>È possibile personalizzare o sostituire il modo in cui i dati transazionali vengono trasformati in formato a colonne nell'archivio analitico?

Attualmente, non è possibile trasformare i dati quando vengono propagati automaticamente dall'archivio transazionale all'archivio analitico. Se sono presenti scenari bloccati da questa limitazione, inviare un messaggio di posta elettronica al [team di Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="is-analytical-store-supported-by-terraform"></a>L'archivio analitico è supportato da Terraform?

Attualmente Terraform non supporta i contenitori di archivi analitici. Per altre informazioni, vedere [Problemi relativi a GitHub in Terraform](https://github.com/hashicorp/terraform/issues).

## <a name="analytical-time-to-live-ttl"></a>Durata (TTL) dei dati analitici

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>La durata (TTL) per i dati analitici è supportata sia a livello di contenitore che di elemento?

Attualmente, il TTL per i dati analitici può essere configurato solo a livello di contenitore e non è previsto il supporto per impostarlo a livello di elemento.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Dopo aver impostato il TTL dei dati analitici a livello di contenitore in un contenitore Azure Cosmos DB, è possibile cambiarne il valore in seguito?

Sì, il TTL dei dati analitici può essere aggiornato a qualsiasi valore valido. Per informazioni più dettagliate sul TTL dei dati analitici, vedere l'articolo [Durata (TTL) dei dati analitici](analytical-store-introduction.md#analytical-ttl).

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>È possibile aggiornare o eliminare un elemento dall'archivio analitico dopo che è scaduto nell'archivio transazionale?

Tutti gli aggiornamenti e le eliminazioni transazionali vengono copiati nell'archivio analitico, ma se l'elemento è stato eliminato dall'archivio transazionale, non può essere aggiornato nell'archivio analitico. Per altre informazioni, vedere l'articolo [Durata (TTL) dei dati analitici](analytical-store-introduction.md#analytical-ttl).

## <a name="billing"></a>Fatturazione

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Qual è il modello di fatturazione di Azure Synapse Link per Azure Cosmos DB?

Il modello di fatturazione di Collegamento ad Azure Synapse include i costi sostenuti con l'uso dell'archivio analitico di Azure Cosmos DB e del runtime di Synapse. Per altre informazioni, vedere gli articoli [Prezzi dell'archivio analitico di Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing) e [Prezzi di Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

### <a name="what-is-the-billing-impact-if-i-enable-synapse-link-in-my-azure-cosmos-db-database-account"></a>Qual è l'impatto sulla fatturazione se si abilita Collegamento a Synapse'account Azure Cosmos DB database?

Nessuna. L'addebito verrà addebitato solo quando si crea un contenitore abilitato per l'archivio analitico e si inizia a caricare i dati.


## <a name="security"></a>Sicurezza

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Come si esegue l'autenticazione con l'archivio analitico?

L'autenticazione con l'archivio analitico è identica a quella di un archivio transazionale. Per un database specificato, è possibile eseguire l'autenticazione con la chiave primaria o di sola lettura. È possibile sfruttare il servizio collegato in Azure Synapse Studio per evitare di incollare Azure Cosmos DB chiavi nei notebook Spark. L'accesso a questo servizio collegato è disponibile per chiunque abbia accesso all'area di lavoro.

Quando si usano pool SQL serverless synapse, è possibile eseguire query sull'archivio analitico di Azure Cosmos DB creando in modo preliminare le credenziali SQL che archiviano le chiavi dell'account e facendo riferimento a tali credenziali nella funzione OPENROWSET. Per altre informazioni, vedere [Eseguire query con un pool SQL serverless nell'Azure Synapse collegamento.](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

## <a name="synapse-run-times"></a>Runtime di Synapse

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Quali sono i runtime di Synapse attualmente supportati per l'accesso all'archivio analitico di Azure Cosmos DB?

|Azure Synapse runtime |Supporto corrente |
|---------|---------|
|Azure Synapse pool di Spark | Lettura, scrittura (tramite archivio transazionale), tabella, visualizzazione temporanea |
|Azure Synapse pool SQL serverless    | Lettura, visualizzazione |
|Azure Synapse provisioning di SQL   |  Non disponibile |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-serverless-sql-pool-tables-the-same-way-they-do-with-azure-data-lake"></a>Le tabelle Azure Synapse Spark vengono sincronizzate con le Azure Synapse del pool SQL serverless come con Azure Data Lake?

Questa funzionalità non è attualmente disponibile.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>È possibile eseguire Structured Streaming di Spark dall'archivio analitico?

Attualmente il supporto di Structured Streaming di Spark per Azure Cosmos DB viene implementato tramite la funzionalità del feed di modifiche dell'archivio transazionale e non è ancora previsto nell'archivio analitico.

### <a name="is-streaming-supported"></a>Lo streaming è supportato?

Non è possibile trasmettere dati dall'archivio analitico.

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>In Azure Synapse Studio, come si riconosce se si è connessi a un contenitore Azure Cosmos DB con l'archivio di analisi abilitato?

Un contenitore Azure Cosmos DB abilitato con l'archivio analitico presenta l'icona seguente:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Icona del contenitore Azure Cosmos DB abilitato con l'archivio analitico":::

Un contenitore dell'archivio transazione sarà rappresentato dall'icona seguente:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Icona del contenitore Azure Cosmos DB abilitato con l'archivio transazionale":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Come si passano Azure Cosmos DB credenziali da Azure Synapse Studio?

Attualmente, le credenziali di Azure Cosmos DB vengono passate durante la creazione del servizio collegato dall'utente che ha accesso ai database Azure Cosmos DB. L'accesso a tale archivio è disponibile per altri utenti che hanno accesso all'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [vantaggi di Azure Synapse Link](synapse-link.md#synapse-link-benefits)

* Informazioni [sull'integrazione tra Azure Synapse Link e Azure Cosmos DB](synapse-link.md#synapse-link-integration).
