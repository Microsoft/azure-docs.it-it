---
title: Domande frequenti su API diverse in Azure Cosmos DB
description: Risposte alle domande frequenti su Azure Cosmos DB, un servizio di database multimodello distribuito a livello globale. Informazioni su capacità, livelli di prestazioni e scalabilità.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 25b669493e1ee32df4a8c6fc33bdb1b21b08c70f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683337"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Domande frequenti su diverse API in Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quali sono i casi d'uso tipici per Azure Cosmos DB?

Azure Cosmos DB è la scelta ideale per le nuove applicazioni Web, per dispositivi mobili, di gioco e IoT in cui scalabilità automatica, prestazioni prevedibili, tempi di risposta rapidi nell'ordine di millisecondi e la possibilità di eseguire query su dati privi di schema sono importanti. Azure Cosmos DB si presta allo sviluppo rapido e al supporto dell'iterazione continua dei modelli di dati delle applicazioni. Le applicazioni che gestiscono contenuto e dati generati dall'utente rappresentano [casi d'uso comuni di Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Come vengono offerte prestazioni prevedibili in Azure Cosmos DB?

Un'[unità richiesta](request-units.md) (UR) è l'unità di misura della velocità effettiva in Azure Cosmos DB. Una velocità effettiva 1RU corrisponde alla velocità effettiva del GET di un documento da 1 KB. Ogni operazione in Azure Cosmos DB, incluse letture, scritture, query SQL ed esecuzioni di stored procedure, ha un valore di UR deterministico basato sulla velocità effettiva necessaria per completare l'operazione. Invece di considerare CPU, I/O e memoria e il modo in cui ognuno di essi influisce sulla velocità effettiva dell'applicazione, è possibile ragionare in termini di singola misura di UR.

È possibile configurare ogni contenitore di Azure Cosmos con una velocità effettiva con provisioning in termini di UR di velocità effettiva al secondo. Per applicazioni di qualsiasi dimensione, è possibile effettuare un benchmark delle singole richieste per misurarne i valori di UR ed effettuare il provisioning di un contenitore per gestire il totale delle unità richiesta tra tutte le richieste. È anche possibile aumentare o ridurre la velocità effettiva del contenitore con il variare delle esigenze dell'applicazione. Per ulteriori informazioni sulle unità richiesta e su come determinare le esigenze del contenitore, provare il [calcolatore della velocità effettiva](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>In che modo Azure Cosmos DB supporta diversi modelli di dati, ad esempio chiave/valore, colonne, documenti e grafi?

I modelli di dati chiave/valore (tabella), colonne, documenti e grafi sono tutti supportati a livello nativo grazie alla progettazione ARS (atom, record e sequenze) su cui si basa Azure Cosmos DB. È facilmente possibile eseguire il mapping e la proiezione di atom, record e sequenze in diversi modelli di dati. Le API per un subset di modelli sono già disponibili (SQL, MongoDB, Tabella e Gremlin) e altre, specifiche di modelli di dati aggiuntivi, saranno disponibili in futuro.

Azure Cosmos DB ha un motore di indicizzazione indipendente dallo schema che può indicizzare automaticamente tutti i dati inseriti senza richiedere schemi o indici secondari agli sviluppatori. Il motore si basa su un set di layout di indice logici (invertiti, colonne, albero) che separano il layout di archiviazione dai sottosistemi di elaborazione di indici e query. Cosmos DB supporta anche un set di API e protocolli di collegamento in modalità estendibile e consente di convertirli in modo efficiente nel modello di dati principale (1) e nei layout di indice logici (2), supportando in modo esclusivo più modelli di dati a livello nativo.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>È possibile usare più API per accedere ai dati?

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. Laddove la modalità multimodello Azure Cosmos DB supporta più API e più modelli di dati, diverse API utilizzano formati dati diversi per l'archiviazione e il protocollo wire. Ad esempio, SQL usa JSON, MongoDB USA BSON, la tabella USA EDM, Cassandra USA CQL, gremlin USA il formato JSON. Di conseguenza, è consigliabile usare la stessa API per tutti gli accessi ai dati in un determinato account.

Ogni API opera in modo indipendente, ad eccezione di Gremlin e API SQL, che sono interoperativi.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB è conforme alla normativa HIPAA?

Sì, Azure Cosmos DB è conforme alla normativa HIPAA. La normativa HIPAA stabilisce i requisiti per l'uso, la divulgazione e la protezione delle informazioni sanitarie personali sensibili. Per ulteriori informazioni, vedere [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quali sono i limiti di archiviazione di Azure Cosmos DB?

In Azure Cosmos DB non esiste alcun limite alla quantità totale di dati che può essere archiviata in un contenitore.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quali sono i limiti di velocità effettiva di Azure Cosmos DB?

In Azure Cosmos DB non esiste alcun limite alla velocità effettiva totale che può essere supportata da un contenitore. Lo scopo primario è distribuire il carico di lavoro in modo abbastanza uniforme tra un numero sufficientemente elevato di chiavi di partizione.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Le modalità di connettività Diretta e Gateway sono crittografate?

Sì, entrambe le modalità sono completamente crittografate.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Quanto costa Azure Cosmos DB?

Per informazioni dettagliate, vedere la pagina [Prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Gli addebiti per l'utilizzo di Azure Cosmos DB sono determinati dal numero di contenitori sottoposti a provisioning, dal numero di ore in cui i contenitori sono stati online e dalla velocità effettiva di provisioning per ogni contenitore.

### <a name="is-a-free-account-available"></a>È disponibile un account gratuito?

Sì, è possibile ottenere un account gratuito per un tempo limitato, senza alcun impegno. Per iscriversi, visitare la pagina [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Per altre informazioni, vedere [Domande frequenti su Azure Cosmos DB](#try-cosmos-db).

I nuovi utenti possono iscriversi per ottenere un [account gratuito di Azure](https://azure.microsoft.com/free/), che è valido 30 giorni e include un credito per provare tutti i servizi di Azure. Se si ha una sottoscrizione di Visual Studio, si ha anche diritto a [crediti Azure gratuiti](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) da usare per qualsiasi servizio di Azure.

È anche possibile usare l'[emulatore di Azure Cosmos DB](local-emulator.md) per sviluppare e testare gratuitamente l'applicazione in locale, senza creare una sottoscrizione di Azure. Quando si è soddisfatti del funzionamento dell'applicazione nell'emulatore di Azure Cosmos DB, è possibile iniziare a usare l'account Azure Cosmos DB nel cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Come è possibile ottenere informazioni e supporto aggiuntivi per Azure Cosmos DB?

In caso di domande tecniche, inviare un messaggio a uno di questi due forum di domande e risposte:

* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow è ideale per domande relative alla programmazione. Assicurarsi che la domanda sia [pertinente](https://stackoverflow.com/help/on-topic) e [specificare il numero massimo possibile di dettagli, in modo che la domanda sia chiara e sia possibile fornire una risposta](https://stackoverflow.com/help/how-to-ask).

Per richiedere nuove funzionalità, creare una nuova richiesta sul sito di [User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Per risolvere un problema relativo all'account, inviare una [richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nel portale di Azure.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Provare gratuitamente Azure Cosmos DB

È ora possibile provare Azure Cosmos DB per un tempo limitato senza alcuna sottoscrizione, gratuitamente e senza impegno. Per iscriversi a una sottoscrizione di prova Azure Cosmos DB, vedere [provare Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) e usare qualsiasi account Microsoft personale (MSA). Questa sottoscrizione differisce dalla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/) e può essere usata insieme alla versione di valutazione gratuita di Azure o a una sottoscrizione a pagamento di Azure.

Le sottoscrizioni di prova di Azure Cosmos DB vengono visualizzate nel portale di Azure accanto alle altre sottoscrizioni associate al proprio ID utente.

Le condizioni seguenti si applicano alle sottoscrizioni di prova di Azure Cosmos DB:

* L'accesso all'account può essere concesso agli account Microsoft personali (MSA). Evitare di usare account o account Active Directory (AAD) appartenenti ai tenant di AAD aziendali, è possibile che siano presenti limitazioni che potrebbero bloccare la concessione dell'accesso.
* Un [contenitore con provisioning della velocità effettiva](./set-throughput.md#set-throughput-on-a-container) per ogni sottoscrizione per account SQL, API Gremlin e tabella.
* Fino a tre [raccolte con provisioning della velocità effettiva](./set-throughput.md#set-throughput-on-a-container) per ogni sottoscrizione per gli account MongoDB.
* Un [database con provisioning della velocità effettiva](./set-throughput.md#set-throughput-on-a-database) per ogni sottoscrizione. I database con provisioning della velocità effettiva possono contenere un numero qualsiasi di contenitori all'interno di.
* 10 GB di capacità di archiviazione.
* La replica globale è disponibile nelle seguenti [aree di Azure](https://azure.microsoft.com/regions/): Stati Uniti centrali, Europa settentrionale e Asia sud-orientale
* Velocità effettiva massima di 5 UR/sec quando ne viene effettuato il provisioning a livello di contenitore.
* Velocità effettiva massima di 20 UR/sec quando ne viene effettuato il provisioning a livello di database.
* Le sottoscrizioni scadono dopo 30 giorni e possono essere estese a un massimo di 31 giorni in totale.
* Non è possibile creare ticket di supporto di Azure per gli account di prova di Azure Cosmos DB. È tuttavia previsto il supporto per i sottoscrittori con piani di supporto esistenti.

## <a name="set-up-azure-cosmos-db"></a>Configurazione di Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Come ci si iscrive ad Azure Cosmos DB?

Azure Cosmos DB è disponibile nel portale di Azure. Per prima cosa, iscriversi per ottenere una sottoscrizione di Azure. Dopo aver effettuato l'iscrizione, è possibile aggiungere un account Azure Cosmos DB alla sottoscrizione di Azure.

### <a name="what-is-a-master-key"></a>Che cos'è una chiave master?

Una chiave master è un token di sicurezza per l'accesso a tutte le risorse di un account. Gli utenti con la chiave hanno accesso in lettura e scrittura a tutte le risorse nell'account del database. Distribuire le chiavi master con cautela. La chiave master primaria e la chiave master secondaria sono disponibili nel pannello **Chiavi** del [portale di Azure][azure-portal]. Per altre informazioni sulle chiavi, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Su quali aree è possibile impostare PreferredLocations?

Il valore di PreferredLocations può essere impostato su qualsiasi area di Azure in cui è disponibile Cosmos DB. Per un elenco delle aree disponibili, vedere [aree di Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Di quali aspetti è consigliabile tenere conto quando si distribuiscono dati nel mondo tramite i data center di Azure?

Azure Cosmos DB è presente in tutte le aree di Azure, come specificato nella pagina [Aree di Azure](https://azure.microsoft.com/regions/). Dato che si tratta del servizio di base, in ogni nuovo data center è disponibile Azure Cosmos DB.

Quando si imposta un'area, tenere presente che Azure Cosmos DB rispetta i cloud sovrani e per enti pubblici. Di conseguenza, se si crea un account in un'[area sovrana](https://azure.microsoft.com/global-infrastructure/) non è possibile eseguire la replica all'[esterno di tale area](https://azure.microsoft.com/global-infrastructure/). Analogamente, non è possibile abilitare la replica in altre località sovrane da un account esterno.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>È possibile passare dal provisioning della velocità effettiva a livello di contenitore al provisioning della velocità effettiva a livello di database? O viceversa

Il provisioning della velocità effettiva a livello di contenitore e di database costituisce due offerte separate e il passaggio tra le due richiede la migrazione dei dati dall'origine alla destinazione. Ciò significa che è necessario creare un nuovo database o un nuovo contenitore e quindi eseguire la migrazione dei dati tramite la [libreria dell'executor bulk](bulk-executor-overview.md) o [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure CosmosDB supporta l'analisi delle serie temporali?

Sì, Azure CosmosDB supporta l'analisi delle serie temporali. Ecco un esempio per il [modello di serie temporali](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Questo esempio mostra come usare il feed di modifiche per creare visualizzazioni aggregate sui dati delle serie temporali. È possibile estendere questo approccio usando Spark Streaming o un altro strumento di elaborazione di dati di flusso.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Quali sono le quote del servizio Azure Cosmos DB e i limiti di velocità effettiva

Per ulteriori informazioni, vedere le [quote del servizio](concepts-limits.md) Azure Cosmos DB e i [limiti per tutti i contenitori e](set-throughput.md#comparison-of-models) gli articoli di database.

## <a name="sql-api"></a>API SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Come si inizia a eseguire lo sviluppo con l'API SQL?

Per prima cosa, è necessario iscriversi per ottenere una sottoscrizione di Azure. Dopo aver ottenuto una sottoscrizione di Azure, è possibile aggiungere un contenitore dell'API SQL alla sottoscrizione di Azure. Per istruzioni sull'aggiunta di un account Azure Cosmos DB, vedere [creare un account del database di Azure Cosmos](create-sql-api-dotnet.md#create-account).

[SDK](sql-api-sdk-dotnet.md) per .NET, Python, Node.js, JavaScript e Java. Gli sviluppatori possono anche usare le [API HTTP RESTful](/rest/api/cosmos-db/) per interagire con le risorse di Azure Cosmos DB da un'ampia gamma di piattaforme e linguaggi.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>È possibile accedere a esempi già pronti per iniziare rapidamente?

In GitHub sono disponibili esempi per gli SDK [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md) e [Python](sql-api-python-samples.md) per l'API SQL.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Il database dell'API SQL supporta dati privi di schema?

Sì. L'API SQL consente alle applicazioni di archiviare documenti JSON arbitrari senza hint o definizioni dello schema. I dati sono immediatamente disponibili per l'esecuzione di query tramite l'interfaccia di query SQL di Azure Cosmos DB.

### <a name="does-the-sql-api-support-acid-transactions"></a>L'API SQL supporta le transazioni ACID?

Sì. L'API SQL supporta le transazioni tra documenti espresse come trigger e stored procedure JavaScript. L'ambito delle transazioni è limitato a una singola partizione in ogni contenitore e le transazioni vengono eseguite con semantica ACID in modalità "tutto o niente", isolate dall'altro codice e le altre richieste utente eseguite contemporaneamente. Se vengono generate eccezioni tramite l'esecuzione sul lato server del codice dell'applicazione JavaScript, viene eseguito il rollback dell'intera transazione. 

### <a name="what-is-a-container"></a>Che cos'è un contenitore?

Un contenitore è un gruppo di documenti con la logica dell'applicazione JavaScript associata. Un contenitore è un'entità fatturabile, in cui il [costo](performance-levels.md) è determinato dalla velocità effettiva e dallo spazio di archiviazione utilizzato. I contenitori possono estendersi su una o più partizioni o server e possono essere ridimensionati per gestire volumi praticamente illimitati di archiviazione o velocità effettiva.

* Per l'API SQL, un contenitore viene mappato a un contenitore.
* Per gli account dell'API Cosmos DB per MongoDB, viene eseguito il mapping di un contenitore a una raccolta.
* Per gli account dell'API Table e Cassandra si ha il mapping fra ogni contenitore e una tabella.
* Per gli account dell'API Gremlin si ha il mapping fra ogni contenitore e un grafico.

I contenitori sono anche le entità di fatturazione per Azure Cosmos DB. Ogni contenitore viene fatturato su base oraria a seconda della velocità effettiva di provisioning e dello spazio di archiviazione utilizzato. Per altre informazioni, vedere [Prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Come si crea un database?

È possibile creare database usando il [portale di Azure](https://portal.azure.com), come descritto in [aggiungere un contenitore](create-sql-api-java.md#add-a-container), uno degli SDK di [Azure Cosmos DB](sql-api-sdk-dotnet.md)o le [API REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Come è possibile configurare gli utenti e le autorizzazioni?

È possibile creare utenti e autorizzazioni usando uno degli [SDK per l'API di Cosmos DB](sql-api-sdk-dotnet.md) o le [API REST](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>L'API SQL supporta SQL?

Il linguaggio di query SQL supportato dagli account dell'API SQL è un sottoinsieme ottimizzato delle funzionalità di query supportate da SQL Server. Il linguaggio di query SQL di Azure Cosmos DB offre operatori gerarchici e relazionali avanzati ed estendibilità tramite funzioni definite dall'utente basate su JavaScript. La grammatica JSON consente la modellazione di documenti JSON come alberi con nodi con etichetta, che vengono usati sia dalle tecniche di indicizzazione automatica di Azure Cosmos DB che dal dialetto di query SQL di Azure Cosmos DB. Per informazioni sull'uso della grammatica SQL, vedere l'articolo [Query SQL][query].

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>L'API SQL supporta le funzioni di aggregazione SQL?

L'API SQL supporta l'aggregazione a bassa latenza su qualsiasi scala con le funzioni di aggregazione `COUNT`, `MIN`, `MAX`, `AVG` e `SUM` tramite la grammatica SQL. Per altre informazioni, vedere [Funzioni di aggregazione](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Come viene offerta la concorrenza nell'API SQL?

L'API SQL supporta il controllo della concorrenza ottimistica tramite tag di entità HTTP o ETag. Ogni risorsa dell'API SQL ha un ETag, che viene impostato nel server ogni volta che un documento viene aggiornato. In tutti i messaggi di risposta sono inclusi l'intestazione ETag e il valore corrente. Gli ETag possono essere usati con l'intestazione If-Match per consentire al server di stabilire se una risorsa deve essere aggiornata. Il valore di If-Match è il valore ETag con cui eseguire il confronto. Se il valore ETag corrisponde al valore ETag del server, la risorsa verrà aggiornata. Se l'ETag non è aggiornato, il server rifiuta l'operazione con un codice di risposta di tipo "HTTP 412 - Precondizione non riuscita". Il client recupera quindi nuovamente la risorsa per acquisire il relativo valore ETag aggiornato. Inoltre, gli ETag possono essere usati con l'intestazione If-None-Match per stabilire se è necessario ripetere il recupero di una risorsa.

Per applicare la concorrenza ottimistica in .NET, usare la classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Per un esempio .NET, vedere [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) nell'esempio DocumentManagement su GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Come si eseguono le transazioni nell'API SQL?

L'API SQL supporta le transazioni Language Integrated tramite trigger e stored procedure JavaScript. Tutte le operazioni su database negli script vengono eseguite con isolamento dello snapshot. Se si tratta di un contenitore a partizione singola, l'ambito dell'esecuzione è quello del contenitore. Se il contenitore è partizionato, l'ambito dell'esecuzione è quello dei documenti con lo stesso valore della chiave di partizione all'interno del contenitore. Viene acquisito uno snapshot delle versioni (ETag) del documento all'inizio della transazione e il commit della transazione viene eseguito solo se lo script ha esito positivo. Se JavaScript genera un errore, viene eseguito il rollback della transazione. Per altre informazioni, vedere [Programmazione JavaScript lato server per Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Come è possibile eseguire inserimenti in blocco in Cosmos DB?

È possibile eseguire l'inserimento in blocco di documenti in Azure Cosmos DB in uno dei modi seguenti:

* Lo strumento BulkExecutor, descritto in [Uso della libreria .NET BulkExecutor ](bulk-executor-dot-net.md) e [Uso della libreria Java BulkExecutor](bulk-executor-java.md)
* Strumento di migrazione dei dati, descritto in [Strumento di migrazione dei database per Azure Cosmos DB](import-data.md).
* Stored procedure, descritte in [Programmazione JavaScript lato server per Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>L'API SQL supporta la memorizzazione nella cache dei collegamenti alle risorse?

Sì. Dato che Azure Cosmos DB è un servizio RESTful, i collegamenti alle risorse sono immutabili e possono essere memorizzati nella cache. I client dell'API SQL possono specificare un'intestazione "If-None-Match" per le letture su qualsiasi contenitore o documento di tipo risorsa, quindi aggiornare le copie locali in seguito alla modifica della versione del server.

### <a name="is-a-local-instance-of-sql-api-available"></a>È disponibile un'istanza locale dell'API SQL?

Sì. L'[emulatore di Azure Cosmos DB](local-emulator.md) offre un'emulazione estremamente fedele del servizio Cosmos DB. Supporta le stesse funzionalità di Azure Cosmos DB, incluso il supporto per la creazione e l'esecuzione di query su documenti JSON, il provisioning e il ridimensionamento delle raccolte e l'esecuzione di stored procedure e trigger. È possibile sviluppare e testare le applicazioni usando l'emulatore di Azure Cosmos DB e distribuirle in Azure su scala globale apportando una singola modifica di configurazione all'endpoint di connessione per Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Perché i valori a virgola mobile lunghi in un documento appaiono arrotondati se visualizzati da Esplora dati nel portale?

Si tratta di una limitazione di JavaScript. JavaScript usa numeri di formato a virgola mobile e precisione doppia come specificato in IEEE 754 e può conservare in modo sicuro i numeri tra-(2<sup>53</sup> -1) e 2<sup>53</sup>-1 (ad esempio, 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Quali sono le autorizzazioni consentite nella gerarchia degli oggetti?

La creazione di autorizzazioni tramite ResourceToken è consentita a livello di contenitore e dei relativi discendenti, ad esempio documenti e allegati. Di conseguenza, la creazione di un'autorizzazione a livello di database o di account non è attualmente consentita.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API Azure Cosmos DB per MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Che cos'è l'API di Azure Cosmos DB per MongoDB?

L'API Azure Cosmos DB per MongoDB è un livello di compatibilità del protocollo wire che consente alle applicazioni di comunicare in modo semplice e trasparente con il motore di database nativo di Azure Cosmos usando gli SDK e i driver supportati dalla community esistenti per MongoDB. Gli sviluppatori possono ora usare toolchain e competenze di MongoDB esistenti per creare applicazioni che sfruttano i vantaggi offerti da Azure Cosmos DB. Gli sviluppatori traggono vantaggio dalle funzionalità esclusive di Azure Cosmos DB, tra cui la distribuzione globale con replica multimaster, l'indicizzazione automatica, la manutenzione dei backup, i contratti di servizio con supporto finanziario e così via.

### <a name="how-do-i-connect-to-my-database"></a>Come ci si connette al database?

Il modo più veloce per connettersi a un database Cosmos con l'API di Azure Cosmos DB per MongoDB è quello di passare al [portale di Azure](https://portal.azure.com). Accedere al proprio account e quindi fare clic su **Avvio rapido** nel menu di spostamento a sinistra. La Guida introduttiva è il modo migliore per ottenere i frammenti di codice per la connessione al database.

Azure Cosmos DB applica standard e requisiti di sicurezza rigidi. Gli account Azure Cosmos DB richiedono l'autenticazione e la comunicazione sicura tramite TLS. Assicurarsi quindi di usare TLSv 1.2.

Per altre informazioni, vedere [Connettersi al database Cosmos con l'API di Azure Cosmos DB per MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Vi sono altri codici di errore che è necessario gestire quando si usa l'API di Azure Cosmos DB per MongoDB?

Oltre ai codici di errore comuni di MongoDB, l'API di Azure Cosmos DB per MongoDB può restituire codici di errore specifici:

| Errore               | Codice  | Descrizione  | Soluzione  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Il numero totale di unità richiesta utilizzate è superiore al tasso di unità richiesta di cui è stato effettuato il provisioning per il contenitore ed è stato limitato. | Valutare la possibilità di ridimensionare la velocità effettiva assegnata a un contenitore o a un set di contenitori dal portale di Azure oppure riprovare. |
| ExceededMemoryLimit | 16501 | In quanto servizio multi-tenant, l'operazione ha superato il limite di allocazione di memoria del client. | Ridurre l'ambito dell'operazione tramite criteri di query più restrittivi oppure contattare il supporto tecnico dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br><em> &nbsp; &nbsp;Esempio: &nbsp;DB. GetCollection (' Users '). Aggregate ([ &nbsp;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {Name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {Age:-1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Il driver Simba per MongoDB è supportato per l'uso con l'API di Azure Cosmos DB per MongoDB?

Sì, è possibile usare il driver ODBC Mongo di Simba con l'API di Azure Cosmos DB per MongoDB

## <a name="table-api"></a><a id="table"></a>API di tabella

### <a name="how-can-i-use-the-table-api-offering"></a>Come si può usare l'offerta API Tabelle?

L'API Table di Azure Cosmos DB è disponibile nel [portale di Azure][azure-portal]. Per prima cosa, è necessario iscriversi per ottenere una sottoscrizione di Azure. Dopo aver effettuato l'iscrizione, è possibile aggiungere un account API Table di Azure Cosmos DB alla sottoscrizione di Azure e quindi aggiungere tabelle all'account.

Per informazioni sui linguaggi supportati e sulle guide introduttive associate, vedere [Introduzione all'API Tabelle di Azure Cosmos DB](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>È necessario un nuovo SDK per usare l'API Tabelle?

No, gli SDK di archiviazione esistenti dovrebbero funzionare ancora. È tuttavia consigliabile ottenere sempre gli SDK più recenti per assicurare il supporto migliore e in molti casi prestazioni superiori. Per un elenco dei linguaggi supportati, vedere [Introduzione all'API Tabelle di Azure Cosmos DB](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Quali sono le differenze tra il comportamento dell'API Tabelle e quello dell'archiviazione tabelle di Azure?

Esistono alcune differenze a livello di comportamento che gli utenti di archiviazione tabelle di Azure che vogliono creare tabelle con l'API Tabelle di Azure Cosmos DB devono tenere in considerazione:

* L'API Tabelle di Azure Cosmos DB usa un modello di capacità riservata per assicurare prestazioni garantite, ma ciò comporta il pagamento della capacità non appena viene creata la tabella, anche se la capacità non viene usata. Archiviazione tabelle di Azure prevede solo il pagamento della capacità effettivamente usata. Questo è il motivo per cui l'API Tabella può offrire un contratto di servizio con 10 ms di lettura e 15 ms di scrittura al 99° percentile, mentre Archiviazione tabelle di Azure offre un contratto di servizio con 10 secondi. Con l'API Tabelle, tuttavia, sono previsti costi anche per le tabelle vuote senza richieste, in modo da assicurare che sia disponibile la capacità per la gestione di eventuali richieste in base al contratto di servizio offerto da Azure Cosmos DB.
* I risultati della query restituiti dal API Tabella non sono ordinati in base all'ordine delle chiavi di partizione/chiavi di riga così come sono nell'archivio tabelle di Azure.
* Le chiavi di riga possono avere una dimensione massima di 255 byte.
* I batch possono avere una dimensione massima di 2 MB.
* CORS non è attualmente supportato.
* I nomi di tabella nell'archiviazione tabelle di Azure non fanno distinzione tra maiuscole e minuscole, ma sono in Azure Cosmos DB API Tabella
* Alcuni dei formati interni di Azure Cosmos DB per le informazioni di codifica, ad esempio i campi binari, non offrono attualmente l'efficienza auspicabile. Di conseguenza questo può causare limitazioni impreviste per le dimensioni dei dati. Ad esempio, attualmente non è possibile usare l'intero MB di un'entità tabella per archiviare i dati binari, perché la codifica incrementa le dimensioni dei dati.
* Il nome della proprietà dell'entità' ID ' non è attualmente supportato
* TableQuery TakeCount non è limitato a 1000

Per quanto riguarda l'API REST, sono disponibili alcune opzioni relative a endpoint/query non supportate dall'API Tabella di Azure Cosmos DB:

| Metodi REST | Opzione relativa a endpoint/query REST | URL della documentazione | Spiegazione |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /?restype=service@comp=properties| [Set Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) (Operazione Set Table Service Properties) [Get Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) (Operazione Get Table Service Properties) | Questo endpoint viene usato per configurare le regole CORS, la configurazione di analisi di archiviazione e le impostazioni di registrazione. CORS non è attualmente supportato e l'analisi e la registrazione vengono gestite in modo diverso in Azure Cosmos DB rispetto ad archiviazione tabelle di Azure. |
| OPTIONS | /\<Table-Resource-Name> | [Pre-flight CORS table request](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) (Operazione Pre-flight CORS table request) | Questa è una parte di CORS che non è attualmente supportata da Azure Cosmos DB. |
| GET | /?restype=service@comp=stats | [Get Table Service Stats](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) (Operazione Get Table Service Stats) | Fornisce informazioni sulla velocità con cui i dati vengono replicati tra il server primario e i server secondari. Non è necessario in Cosmos DB perché la replica fa parte delle operazioni di scrittura. |
| GET, PUT | /mytable?comp=acl | [Get Table ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) (Operazione Get Table ACL) e [Set Table ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) (Operazione Set Table ACL) | Ottiene e configura i criteri di accesso archiviati usati per gestire le firme di accesso condiviso. Anche se la firma di accesso condiviso è supportata, viene configurata e gestita in modo diverso. |

L'API Tabelle di Azure Cosmos DB supporta inoltre il formato JSON, non ATOM.

Nonostante Azure Cosmos DB supporti le firme di accesso condiviso, non supporta alcuni criteri, in particolare i criteri correlati alle operazioni di gestione, ad esempio il diritto di creare nuove tabelle.

Per .NET SDK in particolare, alcune classi e alcuni metodi non sono attualmente supportati da Azure Cosmos DB.

| Class | Metodo non supportato |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext | * (questa classe è deprecata) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Come si forniscono commenti e suggerimenti sull'SDK o sui bug?

È possibile condividere commenti e suggerimenti in uno dei modi seguenti:

* [User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow è ideale per domande relative alla programmazione. Assicurarsi che la domanda sia [pertinente](https://stackoverflow.com/help/on-topic) e [specificare il numero massimo possibile di dettagli, in modo che la domanda sia chiara e sia possibile fornire una risposta](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Quale stringa di connessione è necessario usare per connettersi all'API Tabelle?

La stringa di connessione è:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

È possibile ottenere la stringa di connessione nella pagina Stringa di connessione del portale di Azure.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Come si sostituiscono le impostazioni di configurazione per le opzioni relative alle richieste in .NET SDK per l'API Tabelle?

Alcune impostazioni vengono gestite nel metodo CreateCloudTableClient e altre tramite il file app.config nella sezione appSettings dell'applicazione client. Per informazioni sulle impostazioni di configurazione, vedere [Funzionalità di Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Sono stati introdotti cambiamenti per i clienti che usano gli SDK di archiviazione tabelle di Azure?

Nessuno. Non è stato introdotto alcun cambiamento per i clienti nuovi o esistenti che usano gli SDK di archiviazione tabelle di Azure esistenti.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Come si visualizzano i dati di tabella archiviati in Azure Cosmos DB da usare con l'API Tabella?

Per esplorare i dati si può usare il portale di Azure. È anche possibile usare il codice dell'API Tabelle o gli strumenti illustrati nella risposta successiva.

### <a name="which-tools-work-with-the-table-api"></a>Quali strumenti è possibile usare con l'API Tabelle?

È possibile usare [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

La nuova API Tabelle è supportata dagli strumenti sufficientemente flessibili da accettare una stringa di connessione nel formato specificato in precedenza. Per un elenco degli strumenti di tabella, vedere la pagina [Strumenti client di Archiviazione di Azure](../storage/common/storage-explorers.md).

### <a name="is-the-concurrency-on-operations-controlled"></a>La concorrenza nelle operazioni è controllata?

Sì. La concorrenza ottimistica è supportata tramite l'uso del meccanismo degli ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>È supportato il modello di query OData per le entità?

Sì. L'API Tabelle supporta query OData e query LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>È supportata la connessione affiancata tra archiviazione tabelle di Azure e l'API Tabelle di Azure Cosmos DB?

Sì. È possibile connettersi creando due istanze separate di CloudTableClient, ognuna delle quali punta a un URI specifico tramite la stringa di connessione.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Come si esegue la migrazione di un'applicazione di archiviazione tabelle di Azure esistente a questa offerta?

Sono supportati [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) e l'[Utilità di migrazione dati di Azure Cosmos DB](import-data.md).

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Come si espandono le dimensioni di archiviazione per questo servizio, se ad esempio *n* GB di dati iniziali crescono nel tempo fino a 1 TB?

Azure Cosmos DB è progettato per offrire spazio di archiviazione illimitato tramite scalabilità orizzontale. Il servizio può monitorare e aumentare efficacemente lo spazio di archiviazione.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Come si monitora l'offerta dell'API Tabelle?

È possibile usare il riquadro **Metriche** dell'API Tabelle per monitorare le richieste e l'utilizzo dello spazio di archiviazione.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Come si calcola la velocità effettiva necessaria?

È possibile usare lo strumento di stima della capacità per calcolare il valore di TableThroughput necessario per le operazioni. Per altre informazioni, vedere [Estimate Request Units and Data Storage](https://www.documentdb.com/capacityplanner) (Stimare le unità richiesta e l'archiviazione dei dati). In generale, è possibile rappresentare un'entità in formato JSON e specificare i numeri relativi alle operazioni.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>È possibile usare l'SDK dell'API Tabelle in locale con l'emulatore?

Attualmente non è possibile.

### <a name="can-my-existing-application-work-with-the-table-api"></a>È possibile usare un'applicazione esistente con l'API Tabelle?

Sì, è supportata la stessa API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>È necessario eseguire la migrazione delle applicazioni di Archiviazione tabelle di Azure esistenti all'SDK se non si vogliono usare le funzionalità dell'API Tabella?

No. È possibile creare e usare asset di archiviazione tabelle di Azure esistenti senza interruzioni di alcun tipo. Se non si usa l'API Tabella, tuttavia, non è possibile usufruire dell'indicizzazione automatica, dell'opzione di coerenza aggiuntiva o della distribuzione globale.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Come si aggiunge la replica dei dati nell'API Tabella in più aree di Azure?

Per aggiungere aree adatte all'applicazione, è possibile usare le [impostazioni di replica globali](tutorial-global-distribution-sql-api.md#portal) del portale Azure Cosmos DB. Per sviluppare un'applicazione distribuita a livello globale, è consigliabile anche aggiungere l'applicazione con il valore di PreferredLocation impostato sull'area locale, per garantire una bassa latenza di lettura.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Come si modifica l'area di scrittura primaria per l'account nell'API Tabelle?

È possibile usare il riquadro del portale relativo alla replica globale di Azure Cosmos DB per aggiungere un'area e quindi effettuare il failover nell'area necessaria. Per istruzioni, vedere l'articolo relativo allo [sviluppo con account Azure Cosmos DB tra più aree](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Come si configurano le aree di lettura preferite per una bassa latenza quando si distribuiscono i dati?

Per facilitare la lettura dalla località locale, usare la chiave PreferredLocation nel file app.config. Per le applicazioni esistenti, l'API Tabelle genera un errore in caso di impostazione di LocationMode. Rimuovere tale codice, perché l'API Tabelle acquisisce queste informazioni dal file app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Come funzionano i livelli di coerenza nell'API Tabelle?

Azure Cosmos DB offre compromessi ben ponderati tra coerenza, disponibilità e latenza. Azure Cosmos DB offre cinque livelli di coerenza per gli sviluppatori che usano l'API Tabelle, consentendo così di scegliere il modello di coerenza appropriato a livello di tabella ed effettuare singole richieste durante l'esecuzione di query sui dati. Quando un client si connette, può specificare un livello di coerenza. È possibile modificare il livello tramite l'argomento consistencyLevel di CreateCloudTableClient.

L'API Tabelle offre letture a bassa latenza con la "lettura delle proprie scritture", con la coerenza con obsolescenza associata come impostazione predefinita. Per altre informazioni, vedere l'articolo relativo ai [livelli di coerenza](consistency-levels.md).

Per impostazione predefinita, l'archivio tabelle di Azure offre coerenza assoluta all'interno di un'area e coerenza finale nelle località secondarie.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>L'API Tabelle di Azure Cosmos DB offre più livelli di coerenza rispetto all'archiviazione tabelle di Azure?

Sì. Per informazioni su come trarre vantaggio dalla natura distribuita di Azure Cosmos DB, vedere l'articolo relativo ai [livelli di coerenza](consistency-levels.md). Grazie alle garanzie associate ai livelli di coerenza, possono essere usati in tutta sicurezza.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Quando è abilitata la distribuzione globale, quanto tempo è necessario per replicare i dati?

Azure Cosmos DB esegue il commit permanente dei dati nell'area locale e ne effettua immediatamente il push in altre aree, nell'arco di millisecondi. Questa replica dipende solo dal tempo di round trip del data center. Per altre informazioni sulla funzionalità di distribuzione globale di Azure Cosmos DB, vedere l'articolo relativo ad [Azure Cosmos DB come servizio di database distribuito a livello globale in Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>È possibile modificare il livello di coerenza delle richieste di lettura?

Con Azure Cosmos DB, è possibile impostare il livello di coerenza a livello di contenitore (nella tabella). Usando .NET SDK, si può modificare il livello specificando il valore della chiave TableConsistencyLevel nel file app.config. I valori possibili sono: Strong, Bounded Staleness, Session, Consistent Prefix ed Eventual. Per altre informazioni, vedere [Livelli di coerenza dei dati ottimizzabili in Azure Cosmos DB](consistency-levels.md). Il concetto chiave è che non è possibile impostare un livello di coerenza delle richieste superiore rispetto all'impostazione della tabella. Ad esempio, non è possibile impostare il livello di coerenza Eventual per la tabella e il livello di coerenza Strong per le richieste.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Come viene gestito il failover dall'API Tabelle se un'area diventa inattiva?

L'API Tabelle sfrutta i vantaggi della piattaforma distribuita a livello globale di Azure Cosmos DB. Affinché l'applicazione possa tollerare i tempi di inattività del data center, abilitare almeno un'altra area per l'account nel portale di Azure Cosmos DB, come illustrato nell'articolo relativo allo [sviluppo con account Azure Cosmos DB tra più aree](high-availability.md). Si può impostare la priorità dell'area usando il portale. Vedere l'articolo relativo allo [sviluppo con account Azure Cosmos DB tra più aree](high-availability.md).

È possibile aggiungere il numero di aree desiderato per l'account e controllare in quale potrà essere effettuato il failover specificando una priorità di failover. Per usare il database, è necessario includere anche un'applicazione. Nel corso di questa operazione, i clienti non riscontrano tempi di inattività. A differenza degli altri SDK, l'[SDK client .NET più recente](table-sdk-dotnet.md) supporta l'homing automatico. ovvero può rilevare l'area inattiva ed effettuare automaticamente il failover nella nuova area.

### <a name="is-the-table-api-enabled-for-backups"></a>L'API Tabelle è abilitata per il backup?

Sì, l'API Tabelle sfrutta i vantaggi della piattaforma di Azure Cosmos DB per i backup. I backup vengono eseguiti automaticamente. Per altre informazioni, vedere [Backup online automatico e ripristino con Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>L'API Tabelle indicizza tutti gli attributi di un'entità per impostazione predefinita?

Sì. Per impostazione predefinita vengono indicizzati tutti gli attributi di un'entità. Per ulteriori informazioni, vedere [Azure Cosmos DB: criteri di indicizzazione](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Questo significa che non è necessario creare più di un indice per soddisfare le query?

Sì. L'API Tabelle di Azure Cosmos DB offre l'indicizzazione automatica di tutti gli attributi senza definizione di schema. Questa automazione consente agli sviluppatori di concentrarsi sull'applicazione anziché sulla creazione e sulla gestione degli indici. Per ulteriori informazioni, vedere [Azure Cosmos DB: criteri di indicizzazione](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>È possibile modificare i criteri di indicizzazione?

Sì. È possibile modificare i criteri di indicizzazione specificando la definizione di indice. È necessario eseguire correttamente la codifica e l'escape delle impostazioni.

Per gli SDK non .NET è possibile configurare i criteri di indicizzazione solo nel portale in **Esplora dati**. Passare alla tabella specifica da modificare, quindi selezionare **Scala e impostazioni**->Criteri di indicizzazione, apportare le modifiche desiderate e quindi scegliere **Salva**.

Da .NET SDK è possibile inviare i criteri nel file app.config:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Come piattaforma, Azure Cosmos DB offre numerose funzionalità, come l'ordinamento, le aggregazioni, la gerarchia e così via. Queste funzionalità verranno aggiunte all'API Table?

L'API Tabelle offre le stesse funzionalità di query dell'archiviazione tabelle di Azure. Azure Cosmos DB supporta anche l'ordinamento, le aggregazioni, le query geospaziali, la gerarchia e un'ampia gamma di funzioni predefinite. Per altre informazioni, vedere [Query SQL](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Quando è consigliabile modificare TableThroughput per l'API Tabelle?

È consigliabile modificare TableThroughput quando si verifica una delle condizioni seguenti:

* Si esegue un'operazione di estrazione, trasformazione e caricamento (ETL) di dati o si vuole caricare una grande quantità di dati in breve tempo.
* È necessaria maggiore velocità effettiva del contenitore o di un set di contenitori nel back-end. Ad esempio, si rileva che la velocità effettiva usata è superiore alla velocità effettiva di cui è stato effettuato il provisioning e viene applicata la limitazione. Per altre informazioni, vedere [impostare la velocità effettiva per i contenitori di Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>È possibile aumentare o ridurre la velocità effettiva di una tabella dell'API Tabelle?

Sì, è possibile usare il riquadro relativo alla scalabilità del portale di Azure Cosmos DB per ridimensionare la velocità effettiva. Per altre informazioni, vedere l'articolo su come [impostare la velocità effettiva](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Per le tabelle appena sottoposte a provisioning viene impostato un valore predefinito di TableThroughput?

Sì. Se non si sostituisce il valore di TableThroughput in app.config e non si usa un contenitore già creato in Azure Cosmos DB, il servizio crea una tabella con velocità effettiva di 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>I prezzi per i clienti esistenti del servizio archiviazione tabelle di Azure sono cambiati?

Nessuno. Il prezzo per i clienti esistenti di Archiviazione tabelle di Azure non è cambiato.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Come viene calcolato il prezzo per l'API Tabelle?

Il prezzo dipende dal valore di TableThroughput allocato.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Come si gestisce l'eventuale limitazione della frequenza nelle tabelle dell'offerta dell'API Tabelle?

Se la frequenza delle richieste supera la capacità della velocità effettiva di cui è stato effettuato il provisioning per il contenitore sottostante o un set di contenitori, verrà restituito un errore e l'SDK ripeterà la chiamata applicando i criteri di ripetizione dei tentativi.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Perché è necessario scegliere una velocità effettiva oltre alla chiave di partizione e alla chiave di riga per sfruttare i vantaggi dell'offerta API Tabelle di Azure Cosmos DB?

Azure Cosmos DB imposta una velocità effettiva predefinita per il contenitore, se non se ne specifica una nel file app.config o tramite il portale.

Azure Cosmos DB offre garanzie di prestazioni e latenza, con limiti superiori nelle operazioni. Questa garanzia è possibile quando il motore può applicare la governance nelle operazioni del tenant. L'impostazione di TableThroughput assicura la velocità effettiva e la latenza garantite, perché la piattaforma riserva tale capacità e garantisce l'esito positivo delle operazioni.

È possibile usare la specifica della velocità effettiva modificandola in modo elastico per trarre vantaggio dalla stagionalità dell'applicazione, soddisfare le esigenze in termini di velocità effettiva e ridurre i costi.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Archiviazione tabelle di Azure è una soluzione economica se si eseguono raramente query, perché si paga solo per l'archiviazione dei dati. Con l'offerta relativa all'API Tabella di Azure Cosmos DB sembra che vengano effettuati addebiti anche se non si esegue alcuna transazione o non si archiviano dati. Per quale motivo?

Azure Cosmos DB è progettato come un sistema basato su contratti di servizio distribuito a livello globale con garanzie di disponibilità, latenza e velocità effettiva. La velocità effettiva riservata in Azure Cosmos DB è garantita, a differenza della velocità effettiva di altri sistemi. Azure Cosmos DB offre funzionalità aggiuntive che sono state richieste dai clienti, come gli indici secondari e la distribuzione globale.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Quando si inseriscono dati nell'archiviazione tabelle di Azure, non viene mai visualizzata una notifica di raggiungimento della quota che indica che la partizione è piena. Con l'API Tabelle viene visualizzato questo messaggio. Questa offerta impone limitazioni e la modifica dell'applicazione esistente?

Azure Cosmos DB è un sistema basato su contratti di servizio che offre scalabilità illimitata, con garanzie di latenza, velocità effettiva, disponibilità e coerenza. Per assicurarsi le prestazioni Premium garantite, verificare che l'indice e le dimensioni dei dati siano gestibili e scalabili. Il limite di 10 GB per il numero di entità o elementi per chiave di partizione ha lo scopo di assicurare prestazioni di ricerca e di query elevate. Per garantire la scalabilità dell'applicazione anche per Archiviazione di Azure, è consigliabile *non* creare una partizione critica archiviando tutte le informazioni in una partizione ed eseguendo query su di essa.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>La chiave di partizione e la chiave di riga sono ancora necessarie con l'API Tabelle?

Sì. Dato che la superficie di attacco dell'API Tabelle è simile a quella di Azure Table Storage SDK, la chiave di partizione offre un modo efficiente per distribuire i dati. La chiave di riga è univoca all'interno della partizione. La chiave di riga deve essere presente e non può essere Null come nell'SDK standard. La chiave di riga è lunga 255 byte, mentre la chiave di partizione ha una lunghezza di 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Quali sono i messaggi di errore per l'API Tabelle?

L'archiviazione tabelle di Azure e l'API Tabelle di Azure Cosmos DB usano gli stessi SDK, quindi la maggior parte degli errori sarà uguale.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Perché quando si prova a creare numerose tabelle una dopo l'altra nell'API Tabelle viene applicata la limitazione?

Azure Cosmos DB è un sistema basato su contratti di servizio che offre garanzie di latenza, velocità effettiva, disponibilità e coerenza. Dato che è un sistema con provisioning, riserva risorse per garantire tali requisiti. La rapida frequenza di creazione delle tabelle viene rilevata e limitata. È consigliabile esaminare la frequenza di creazione di tabelle e ridurla a meno di 5 al minuto. Tenere presente che l'API Tabelle è un sistema con provisioning. Il relativo pagamento ha inizio nel momento in cui ne viene effettuato il provisioning.

## <a name="gremlin-api"></a>API Gremlin

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Per lo sviluppo C#/.NET è necessario usare il pacchetto Microsoft.Azure.Graphs o Gremlin.NET?

L'API Gremlin di Azure Cosmos DB sfrutta i vantaggi dei driver open source come connettori principali per il servizio. L'opzione consigliata consiste quindi nell'usare [driver supportati da Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>In che modo vengono addebitate le unità richiesta al secondo quando si eseguono query in un database a grafi?

Tutti gli oggetti, i vertici e gli archi dei grafi vengono rappresentati come documenti JSON nel back-end. Poiché una query Gremlin può modificare uno o più oggetti del grafo alla volta, il costo associato a tale operazione è direttamente correlato agli oggetti e agli archi elaborati dalla query. Si tratta dello stesso processo usato da Azure Cosmos DB per tutte le altre API. Per altre informazioni, vedere [unità richiesta in Azure Cosmos DB](request-units.md).

L'addebito per unità richiesta viene basato sul set di dati in uso dell'attraversamento e non sul set di risultati. Ad esempio, se una query ha come obiettivo il recupero di un singolo vertice come risultato ma deve attraversare altri oggetti per ottenere tale risultato, il costo sarà basato su tutti gli oggetti del grafo necessari per il calcolo del vertice specifico.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Qual è la scalabilità massima consentita per un database Graph in Azure Cosmos DB API Gremlin?

Azure Cosmos DB usa il [partizionamento orizzontale](partition-data.md) per gestire automaticamente gli incrementi nei requisiti relativi a risorse di archiviazione e velocità effettiva. La velocità effettiva massima e la capacità di archiviazione di un carico di lavoro sono determinate dal numero di partizioni associate a un determinato contenitore. Tuttavia, un contenitore dell'API Gremlin dispone di un set specifico di linee guida per garantire un'esperienza di prestazioni adeguata su larga scala. Per altre informazioni sul partizionamento e sulle procedure consigliate, vedere l'articolo relativo al [partizionamento in Azure Cosmos DB](partition-data.md).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Come ci si può proteggere da attacchi di tipo injection con driver Gremlin?

La maggior parte dei driver di Apache Tinkerpop Gremlin nativi consente l'opzione per fornire un dizionario di parametri per l'esecuzione di query. Ecco un esempio di come ottenere questo risultato in [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) e in [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Perché viene ricevuto l'errore "errore di compilazione della query Gremlin: Impossibile trovare un metodo"?

L'API Gremlin di Azure Cosmos DB implementa un sottoinsieme di funzionalità definite nella superficie di attacco di Gremlin. Per le procedure supportate e altre informazioni, vedere l'articolo [Supporto Gremlin](gremlin-support.md).

La soluzione alternativa migliore consiste nel riscrivere la procedura Gremlin necessaria con le funzionalità supportate, poiché tutti i passaggi Gremlin essenziali sono supportati da Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Perché viene visualizzato il messaggio "WebSocketException: il server ha restituito il codice di stato" 200 "quando è previsto il codice di stato" 101 "?

Questo errore viene probabilmente generato quando viene usato un endpoint non corretto. L'endpoint che genera questo errore ha il criterio seguente:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Endpoint dei documenti per il database a grafi.  L'endpoint corretto da usare è l'endpoint Gremlin, che ha il formato seguente:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Perché viene ricevuto l'errore "RequestRateIsTooLarge"?

Questo errore indica che le unità richiesta allocate al secondo non sono sufficienti per la query. L'errore viene in genere visualizzato quando si esegue una query che ottiene tutti i vertici:

```
// Query example:
g.V()
```

La query proverà a recuperare tutti i vertici dal grafo. Il costo di questa query in termini di unità richiesta sarà uguale almeno al numero di vertici. È necessario modificare l'impostazione UR/s in modo specifico per questa query.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Perché le connessioni del driver Gremlin vengono interrotte?

Una connessione Gremlin viene effettuata tramite una connessione WebSocket. Benché le connessioni WebSocket non abbiano una durata specifica, l'API Gremlin di Azure Cosmos DB interromperà le connessioni inattive dopo 30 minuti di inattività.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Perché non è possibile usare le chiamate API Fluent nei driver Gremlin nativi?

Le chiamate API Fluent non sono ancora supportate dall'API Gremlin di Azure Cosmos DB. Le chiamate API Fluent richiedono una funzionalità di formattazione interna denominata supporto per bytecode che non è attualmente supportata dall'API Gremlin di Azure Cosmos DB. A causa dello stesso motivo, non è attualmente supportato il driver Gremlin-JavaScript più recente.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>In che modo è possibile valutare l'efficienza delle query di Gremlin?

Il passaggio di anteprima **executionProfile()** può essere usato per fornire un'analisi del piano di esecuzione della query. Questo passaggio deve essere aggiunto alla fine di qualsiasi query di Gremlin, come illustrato dall'esempio seguente:

**Esempio di query**

```
g.V('mary').out('knows').executionProfile()
```

**Output di esempio**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

L'output del profilo precedente Mostra la quantità di tempo impiegato per ottenere gli oggetti vertex, gli oggetti Edge e le dimensioni del set di dati di lavoro. Queste informazioni sono correlate alle misure dei costi standard per le query di Azure Cosmos DB.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
