---
title: Casi d'uso e scenari comuni per Azure Cosmos DB
description: "Informazioni sui primi cinque casi d'uso per Cosmos DB: contenuto generato dall'utente, registrazione di eventi, dati di catalogo, dati delle preferenze utente e \"Internet delle cose\" (IoT)."
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 76016da2ec542091aa57d5081e275a1f9f6671cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85114266"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Casi d'uso comuni di Azure Cosmos DB
Questo articolo fornisce una panoramica di diversi casi d'uso comuni per Azure Cosmos DB.  I consigli forniti sono un punto di partenza per sviluppare applicazioni con Cosmos DB.   

Alla fine della lettura, si avranno le risposte alle domande seguenti: 

* Quali sono i casi d'uso comuni per Azure Cosmos DB?
* Quali sono i vantaggi dell'uso di Azure Cosmos DB per le applicazioni di vendita al dettaglio?
* Quali sono i vantaggi dell'uso di Azure Cosmos DB come archivio dati per sistemi Internet delle cose (IoT)?
* Quali sono i vantaggi dell'uso di Azure Cosmos DB per le applicazioni Web e per dispositivi mobili?

## <a name="introduction"></a>Introduzione
[Azure Cosmos DB](../cosmos-db/introduction.md) è il servizio di database di Microsoft distribuito a livello globale. Il servizio è progettato per consentire ai clienti di ridimensionare in modo elastico e indipendente velocità effettiva e memoria tra più aree geografiche. Azure Cosmos DB è il primo servizio di database distribuito oggi a livello globale in grado di offrire [contratti di servizio](https://azure.microsoft.com/support/legal/sla/cosmos-db/) comprendenti velocità effettiva, latenza, disponibilità e coerenza. 

Azure Cosmos DB è un database multimodello distribuito a livello globale che viene usato in un'ampia gamma di applicazioni e casi di utilizzo. È un'ottima scelta per le applicazioni [senza server](https://azure.com/serverless) che richiedono tempi di risposta nell'ordine dei millisecondi e che devono essere ridimensionate rapidamente e a livello globale. Supporta diversi modelli di dati (chiave-valore, documenti, grafi e grafici a colonne), oltre a molte API per l'accesso ai dati, tra cui l'[API di Azure Cosmos DB per MongoDB](mongodb-introduction.md), l'[API SQL](documentdb-introduction.md), l'[API Gremlin](graph-introduction.md) e l'[API Tabella](table-introduction.md), in modo nativo ed estendibile. 

Di seguito sono indicati alcuni attributi di Azure Cosmos DB che lo rendono particolarmente adatto ad applicazioni a prestazioni elevate destinate a una distribuzione globale.

* Azure Cosmos DB partiziona in modo nativo i dati per la disponibilità e la scalabilità elevate. Azure Cosmos DB garantisce al 99,99% disponibilità, velocità effettiva, bassa latenza e coerenza per tutti gli account in una singola area e per tutti gli account in più aree con coerenza media. Garantisce poi al 99,999% la disponibilità in lettura per tutti gli account di database in più aree.
* Azure Cosmos DB ha una risorsa di archiviazione basata su SSD con tempi di risposta a bassa latenza nell'ordine di millisecondi.
* Il supporto di Azure Cosmos DB per i livelli di coerenza di tipo eventuale, prefisso coerente, sessione e con obsolescenza associata consente una flessibilità completa e un basso rapporto costo-prestazioni. Nessun servizio di database offre una flessibilità maggiore di Azure Cosmos DB in termini di coerenza dei livelli. 
* Azure Cosmos DB ha un modello di determinazione dei prezzi flessibile e semplice da usare che calcola in modo indipendente lo spazio di archiviazione e la velocità effettiva.
* Il modello di velocità effettiva riservata di Azure Cosmos DB consente un approccio in termini di numero di letture/scritture invece che di CPU/memoria/operazioni di I/O dell'hardware sottostante.
* La progettazione di Azure Cosmos DB consente la scalabilità di elevati volumi di richieste, nell'ordine di trilioni di richieste al giorno.

Questi attributi sono utili nel caso di applicazioni Web, mobili, di gioco e IoT che necessitano di tempi di risposta ridotti e che devono gestire grandi quantità di letture e scritture.

## <a name="iot-and-telematics"></a>IoT e dati telematici
I casi di utilizzo IoT condividono di solito alcuni modelli di inserimento, elaborazione e archiviazione dei dati.  Questi sistemi devono prima di tutto inserire grandi quantità di dati dai sensori dei dispositivi di diverse impostazioni locali. In secondo luogo, questi sistemi elaborano e analizzano i dati di streaming per ottenere informazioni in tempo reale. I dati vengono quindi archiviati nell'archiviazione offline sicura per l'analisi batch. Microsoft Azure offre servizi avanzati che possono essere applicati per i casi d'uso di Internet, tra cui Azure Cosmos DB, Hub eventi di Azure, analisi di flusso di Azure, hub di notifica di Azure, Azure Machine Learning, Azure HDInsight e Power BI. 

:::image type="content" source="./media/use-cases/iot.png" alt-text="Architettura di riferimento di IoT per Azure Cosmos DB" border="false":::

Grandi quantità di dati possono essere acquisite da Hub eventi di Azure che consente di inserire i dati con una velocità effettiva elevata e una bassa latenza. I dati inseriti che devono essere elaborati per ottenere informazioni in tempo reale possono essere incanalati in Analisi di flusso di Azure per l'analisi in tempo reale. I dati possono essere caricati in Azure Cosmos DB per l'esecuzione di query ad hoc. Una volta caricati in Azure Cosmos DB, i dati sono pronti per l'esecuzione delle query. Inoltre, i nuovi dati e le modifiche ai dati esistenti possono essere letti nel feed di modifica. Il feed delle modifiche è un log permanente di sola aggiunta che archivia le modifiche apportate ai contenitori Cosmos in ordine sequenziale. Tutti i dati o solo le modifiche ai dati in Azure Cosmos DB possono essere usati come dati di riferimento nell'ambito di analisi in tempo reale. Inoltre, possono essere ulteriormente affinati ed elaborati connettendo i dati di Azure Cosmos DB a HDInsight per processi Pig, Hive o MapReduce.  I dati affinati vengono quindi ricaricati in Azure Cosmos DB per la creazione di report.   

Per una soluzione IoT di esempio con Azure Cosmos DB, EventHubs e Storm, vedere l'[archivio hdinsight-storm-examples su GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Per altre informazioni sulle offerte di Azure per IoT, vedere [Creare il proprio "Internet delle cose"](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Vendite e marketing
Azure Cosmos DB viene ampiamente usato nelle piattaforme di e-commerce di Microsoft che eseguono Windows Store e XBox Live. Viene inoltre usato nel settore della vendita al dettaglio per l'archiviazione dei dati dei cataloghi e per il recupero degli eventi nelle pipeline di elaborazione degli ordini.

Gli scenari di utilizzo dei dati di un catalogo includono l'archiviazione e l'esecuzione di query di un set di attributi per entità come persone, luoghi e prodotti. Gli account utente, i cataloghi di prodotti, i registri di dispositivi IoT e i sistemi di distinta base sono alcuni esempi di dati di catalogo. Gli attributi di questi dati possono variare e cambiare nel corso del tempo per rispondere ai requisiti dell'applicazione.

Considerare l'esempio di un catalogo di prodotti per un fornitore di parti di ricambio per automobili. Ogni ricambio può avere propri attributi oltre ai comuni attributi condivisi da tutti i ricambi. Inoltre, gli attributi di un ricambio specifico possono cambiare l'anno seguente quando viene rilasciato un nuovo modello. Azure Cosmos DB supporta schemi flessibili e dati gerarchici ed è pertanto adatto per l'archiviazione dei dati di un catalogo di prodotti.

:::image type="content" source="./media/use-cases/product-catalog.png" alt-text="Architettura di riferimento di IoT per Azure Cosmos DB" border="false":::

Azure DB Cosmos viene spesso usato per il recupero degli eventi per architetture basate su eventi tramite la funzionalità di [feed delle modifiche](change-feed.md). Il feed delle modifiche consente ai microservizi downstream di leggere in modo affidabile e incrementale gli inserimenti e aggiornamenti (ad esempio, gli eventi di ordine) eseguiti in un Azure Cosmos DB. Questa funzionalità può essere sfruttata per ottenere un archivio di eventi permanente come broker messaggi per eventi di modifica dello stato e gestire il flusso di lavoro per l'elaborazione degli ordini tra molti microservizi, che possono essere implementati come [Funzioni di Azure senza server](https://azure.com/serverless).

:::image type="content" source="./media/use-cases/event-sourcing.png" alt-text="Architettura di riferimento di IoT per Azure Cosmos DB" border="false":::

I dati archiviati in Azure Cosmos DB possono inoltre essere integrati con HDInsight per analisi di Big Data tramite processi Apache Spark. Per altri dettagli sul connettore Spark per Azure Cosmos DB, vedere [Run a Spark job with Cosmos DB and HDInsight](spark-connector.md) (Eseguire un processo Spark con Azure Cosmos DB e HDInsight).

## <a name="gaming"></a>Giochi
Il livello database è un componente fondamentale delle applicazioni di gioco. I giochi moderni eseguono l'elaborazione grafica in client mobili/console, ma si basano sul cloud per distribuire contenuti personalizzati come le statistiche di gioco, l'integrazione con i social media e le classifiche dei punteggi. I giochi richiedono spesso latenze di un solo millisecondo per operazioni di lettura e scrittura, per offrire un'esperienza di gioco accattivante. Un database per giochi deve essere veloce e in grado di gestire picchi significativi a livello di frequenza di richieste durante l'avvio di nuovi giochi e gli aggiornamenti delle funzionalità.

Azure Cosmos DB viene usato da giochi come [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) di [Next Games](https://www.nextgames.com/) e [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB offre agli sviluppatori di giochi i vantaggi seguenti:

* Azure Cosmos DB consente di aumentare o ridurre le prestazioni in modo flessibile. In questo modo i giochi possono gestire l'aggiornamento del profilo e le statistiche di decine di milioni di giocatori simultanei con una singola chiamata API.
* Azure Cosmos DB supporta le letture e le scritture in millisecondi per evitare eventuali ritardi durante il gioco.
* L'indicizzazione automatica di Azure Cosmos DB consente di filtrare in base a più proprietà in tempo reale, ad esempio per individuare il giocatore in base al suo ID interno o all'ID di GameCenter, Facebook o Google o per eseguire query in base all'appartenenza del giocatore a una gilda. Questa operazione può essere eseguita senza compilare indicizzazioni complesse o infrastrutture di partizionamento orizzontale.
* Le funzionalità social, ad esempio i messaggi delle chat interne al gioco, l'appartenenza a gilde del giocatore, le sfide completate, le classifiche dei punteggi e i grafici dei social network, sono più facili da implementare con uno schema flessibile.
* Azure Cosmos DB usato come PaaS (Platform-as-a-Service) gestito richiede operazioni minime di installazione e gestione per consentire un'iterazione rapida e ridurre il time-to-market.

:::image type="content" source="./media/use-cases/gaming.png" alt-text="Architettura di riferimento di IoT per Azure Cosmos DB" border="false":::

## <a name="web-and-mobile-applications"></a>Applicazioni Web e per dispositivi mobili
Azure Cosmos DB viene comunemente usato all'interno di applicazioni Web e per dispositivi mobili ed è particolarmente adatto alla creazione di modelli di interazione social, all'integrazione con servizi di terze parti e all'ottimizzazione delle esperienze personalizzate. Gli SDK di Cosmos DB possono essere usati per la compilazione avanzata di applicazioni iOS e Android usando il diffuso [framework Xamarin](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Applicazioni social
Un caso di uso comune per Azure Cosmos DB prevede l'archiviazione e il recupero di contenuto generato dall'utente (UGC, User Generated Content) per applicazioni Web, per dispositivi mobili e soprattutto per social media. Alcuni esempi di UGC includono sessioni di chat, tweet, post di blog, classificazioni e commenti. Spesso il contenuto UGC nelle applicazioni per social media comprende testo in formato libero, proprietà, tag e relazioni non limitati da una struttura rigida. I contenuti come chat, commenti e post possono essere archiviati in Cosmos DB senza richiedere trasformazioni o livelli di mapping relazionale a oggetti complessi.  Le proprietà dei dati possono essere aggiunte o modificate facilmente in modo che rispondano ai requisiti quando gli sviluppatori scorrono il codice dell'applicazione, velocizzando così la fase di sviluppo.  

Le applicazioni che si integrano con social network di terze parti devono adeguarsi alle modifiche degli schemi di queste reti. I dati, man mano che vengono automaticamente indicizzati in Cosmos DB per impostazione predefinita, sono pronti per l'esecuzione di query in qualsiasi momento. Queste applicazioni hanno quindi la flessibilità necessaria per recuperare le proiezioni in base alle rispettive esigenze.

Molte applicazioni di social networking vengono eseguite su scala globale e possono presentare modelli di utilizzo non prevedibili. La flessibilità nella scalabilità dell'archivio dati è essenziale man mano che il livello dell'applicazione viene scalato in modo da soddisfare la domanda di utilizzo.  È possibile scalare orizzontalmente aggiungendo altre partizioni di dati con un account Cosmos DB.  Si possono anche creare altri account Cosmos DB in più aree. Per la disponibilità delle aree del servizio Cosmos DB, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/use-cases/apps-with-global-reach.png" alt-text="Architettura di riferimento di IoT per Azure Cosmos DB" border="false":::

### <a name="personalization"></a>Personalizzazione
Oggi le applicazioni moderne sono caratterizzate da esperienze e visualizzazioni complesse, in genere dinamiche e in grado di soddisfare le preferenze o gli stati d'animo degli utenti e le diverse esigenze di personalizzazione. È quindi necessario che le applicazioni possano recuperare le impostazioni personalizzate in modo efficace per eseguire rapidamente il rendering delle esperienze e degli elementi dell'interfaccia utente. 

JSON, supportato da Cosmos DB, è un formato adatto a rappresentare i dati di layout dell'interfaccia utente, essendo non solo leggero, ma anche facilmente interpretabile da JavaScript. Cosmos DB offre livelli di coerenza regolabili che consentono letture veloci con scritture a bassa latenza. Di conseguenza, l'archiviazione dei dati di layout dell'interfaccia utente, incluse le impostazioni personalizzate, come documenti JSON in Cosmos DB è un metodo efficace per avere questi dati in rete.

:::image type="content" source="./media/use-cases/personalization.png" alt-text="Architettura di riferimento di IoT per Azure Cosmos DB" border="false":::

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare Azure Cosmos DB, seguire le [guide introduttive](create-sql-api-dotnet.md), che illustrano come creare un account ed eseguire le prime operazioni con Cosmos DB.

* Per altre informazioni sui clienti che usano Azure Cosmos DB, vedere la pagina [Case Study del cliente](https://azure.microsoft.com/case-studies/?service=cosmos-db) .
