---
title: Uso del supporto del feed delle modifiche in Azure Cosmos DB
description: Usare il supporto dei feed di modifiche del database di Azure Cosmos per tenere traccia delle modifiche nei documenti, l'elaborazione basata su eventi come i trigger e mantenere aggiornate le cache e i sistemi analiticiUse Azure Cosmos DB change feed support to track changes in documents, event-based processing like triggers, and keep caches and analytic systems up-to-date
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 898dfe7a619981b93af98effa942fdecbeb42dde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368129"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Feed di modifiche in Azure Cosmos DB - panoramica

Il supporto del feed di modifiche in Azure Cosmos DB ascolta eventuali modifiche in un contenitore di Azure Cosmos. Restituisce quindi l'elenco di documenti cambiati nell'ordine in cui sono stati modificati. Le modifiche sono persistenti, possono essere elaborate in modo asincrono e incrementale e l'output può essere distribuito a uno o più consumer per l'elaborazione parallela. 

Azure Cosmos DB è particolarmente adatto per le applicazioni IoT, di videogiochi, del settore della vendita al dettaglio e di registrazioni di operazioni. Uno schema progettuale comune in queste applicazioni prevede l'uso delle modifiche ai dati per attivare azioni aggiuntive. Esempi di azioni aggiuntive:

* Attivazione di una notifica o di una chiamata a un'API quando un elemento viene inserito o modificato.
* Elaborazione di flussi in tempo reale per l'elaborazione IoT o di analisi in tempo reale sui dati operativi.
* Spostamento dei dati aggiuntivi tramite sincronizzazione con una cache, un motore di ricerca o un data warehouse oppure tramite archiviazione dei dati nella risorsa di archiviazione offline sicura.

Il feed di modifiche in Azure Cosmos DB consente di creare soluzioni efficienti e scalabili per ognuno di questi modelli, come illustrato nell'immagine seguente:

![Uso del feed delle modifiche di Azure Cosmos DB per agevolare le analisi in tempo reale e gli scenari di calcolo guidati dagli eventi](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>API e SDK client supportati

La funzionalità è attualmente supportata dalle API e dagli SDK client Cosmos DB seguenti.

| **Driver client** | **Interfaccia della riga di comando di AzureAzure** | **SQL API** | **API di Azure Cosmos DB per Cassandra** | **API di Azure Cosmos DB per MongoDB** | **Gremlin API**|**API tabella** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | ND | Sì | Sì | Sì | Sì | No |
|Java|ND|Sì|Sì|Sì|Sì|No|
|Python|ND|Sì|Sì|Sì|Sì|No|
|Node/JS|ND|Sì|Sì|Sì|Sì|No|

## <a name="change-feed-and-different-operations"></a>Feed di modifiche e operazioni diverse

Oggi, si vedranno tutte le operazioni nel feed di modifiche. La funzionalità con cui è possibile controllare il feed di modifiche per operazioni specifiche, ad esempio solo gli aggiornamenti e non gli inserimenti non è ancora disponibile. È possibile aggiungere un "marcatore morbido" sull'elemento per gli aggiornamenti e filtrare in base a tale durante l'elaborazione degli elementi nel feed di modifiche. Attualmente il feed di modifiche non registra le eliminazioni. In modo simile all'esempio precedente, è possibile aggiungere un soft marker per gli elementi da eliminare, ad esempio, è possibile aggiungere all'elemento un attributo denominato "eliminato" e impostarlo su "true" e impostare un TTL nell'elemento, in modo che possa essere eliminato automaticamente. È possibile leggere il feed di modifiche per gli elementi storici (la modifica più recente corrispondente all'elemento, non include le modifiche intermedie), ad esempio gli elementi aggiunti cinque anni fa. Se il documento non è stato eliminato, è possibile leggere il feed di modifiche fino all'origine del contenitore.

### <a name="sort-order-of-items-in-change-feed"></a>Ordinamento degli elementi nel feed di modifiche

Gli elementi nel feed di modifiche sono ordinati in base all'ora di modifica. Questo ordinamento è garantito per ogni chiave di partizione logica.

### <a name="consistency-level"></a>Livello di coerenza

Durante l'utilizzo del feed di modifiche in un livello di coerenza Eventuale, potrebbero verificarsi eventi duplicati tra le successive operazioni di lettura del feed di modifiche (l'ultimo evento di un'operazione di lettura viene visualizzato come la prima della successiva).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed di modifiche in più aree negli account Azure Cosmos

In un account Azure Cosmos con più aree, se un'area di scrittura effettua il failover, il feed di modifiche verrà utilizzato nell'operazione di failover manuale e sarà contiguo.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed di modifiche e durata (TTL)

Se una proprietà TTL (Time to Live) è impostata su un elemento su -1, il feed di modifiche verrà mantenuto per sempre. Se i dati non sono eliminati, rimarranno nel feed di modifiche.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Feed di modifiche e _etag, _lsn or _ts

Il formato _etag è interno e non è consigliabile dipendere da esso, perché può cambiare in qualsiasi momento. _ts è un timestamp di creazione o di modifica. Si può usare _ts per confronti cronologici. _lsn è un ID batch che viene aggiunto solo per il feed di modifiche; rappresenta l'ID della transazione. Molti elementi potrebbe avere lo stesso _lsn. ETag su FeedResponse è diverso dall'_etag che viene visualizzato nell'elemento. _etag è un identificatore interno utilizzato per il controllo della concorrenza e indica la versione dell'elemento mentre ETag viene utilizzato per la sequenziazione del feed.

## <a name="change-feed-use-cases-and-scenarios"></a>Casi d'uso e scenari del feed di modifiche

Il feed di modifiche consente l'elaborazione efficiente di grandi serie di dati con un elevato volume di scritture. Il feed di modifiche offre anche un'alternativa all'esecuzione di query su un intero set di dati per identificare le modifiche apportate.

### <a name="use-cases"></a>Casi d'uso

Ad esempio, con un feed di modifiche è possibile eseguire in modo efficiente le operazioni seguenti:

* Aggiornare una cache, aggiornare un indice di ricerca o un data warehouse con i dati archiviati in Azure Cosmos DB.

* Implementare la suddivisione in livelli e l'archiviazione a livello di applicazione, ad esempio archiviare i dati più usati ("hot data") in Azure Cosmos DB e trasferire quelli meno usati ("cold data") in altri sistemi di archiviazione, ad esempio [Archiviazione BLOB di Azure](../storage/common/storage-introduction.md).

* Eseguire migrazioni senza alcun tempo di inattività a un altro account Azure Cosmos o un altro contenitore di Azure Cosmos con una chiave di partizione logica diversa.

* Implementare [architettura lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) usando Azure Cosmos DB, dove Azure Cosmos DB supporta sia i livelli di servizio in tempo reale, batch e query, consentendo così l'architettura lambda con basso costo totale di proprietà.

* Ricevere e archiviare i dati di eventi da dispositivi, sensori, infrastrutture e applicazioni, per poi elaborarli in tempo reale, ad esempio usando [Spark](../hdinsight/spark/apache-spark-overview.md).  L'immagine seguente mostra come è possibile implementare l'architettura lambda usando Azure Cosmos DB tramite il feed di modifiche:

![Pipeline lambda basate su Azure Cosmos DB per l'inserimento e le query](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenari

Di seguito sono indicati alcuni degli scenari che è possibile implementare facilmente con il feed di modifiche:

* Nelle app Web e per dispositivi mobili [senza server](https://azure.microsoft.com/solutions/serverless/) è possibile tenere traccia di eventi come tutte le modifiche al profilo, alle preferenze o alle località dei clienti, e attivare determinate azioni come l'invio di notifiche push ai dispositivi tramite [Funzioni di Azure](change-feed-functions.md).

* Se si usa Azure Cosmos DB per creare un gioco, è ad esempio possibile usare il feed delle modifiche per implementare classifiche in tempo reale in base ai punteggi delle partite completate.


## <a name="working-with-change-feed"></a>Utilizzo del feed di modifiche

È possibile utilizzare il feed di modifiche utilizzando le opzioni seguenti:

* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
* [Utilizzo del feed di modifiche con il processore del feed di modifiche](change-feed-processor.md) 

Il feed di modifiche è disponibile per ogni chiave di partizione logica nel contenitore, ed è quindi possibile distribuirlo a uno o più consumer per l'elaborazione parallela, come illustrato nell'immagine seguente.

![Elaborazione distribuita del feed delle modifiche di Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funzionalità del feed di modifiche

* Il feed di modifiche è abilitato per impostazione predefinita per tutti gli account Azure Cosmos.

* È possibile usare la [velocità effettiva con provisioning](request-units.md) per leggere dal feed di modifiche, proprio come ogni altra operazione di Azure Cosmos DB, in ogni area associata al database di Azure Cosmos.

* Il feed di modifiche include inserimenti e operazioni di aggiornamenti eseguite sugli elementi all'interno del contenitore. È possibile acquisire le eliminazioni impostando un flag "eliminazione temporanea" all'interno degli elementi (ad esempio, documenti) al posto delle eliminazioni. In alternativa, è possibile impostare un periodo di scadenza limitato per gli elementi con la [funzionalità TTL](time-to-live.md). Per esempio, 24 ore e utilizzare il valore di quella proprietà per acquisire le eliminazioni. Con questa soluzione è necessario elaborare le modifiche in un intervallo di tempo minore rispetto al periodo di scadenza TTL. 

* Ogni modifica apportata a un elemento viene visualizzata una sola volta nel feed di modifiche e i client devono gestire la logica di checkpoint. Se si desidera evitare la complessità della gestione dei checkpoint, il processore del feed di modifiche fornisce il checkpoint automatico e la semantica "almeno una volta". Vedere Utilizzo del feed di modifiche con il [processore del feed](change-feed-processor.md)di modifiche .

* Solo la modifica più recente per un determinato elemento viene inclusa nel registro modifiche. Le modifiche intermedie potrebbero non essere disponibili.

* Il feed di modifiche è ordinato in base all'ordine di modifica in ciascun valore della chiave di partizione. Non esiste alcun ordine garantito tra i valori della chiave di partizione.

* Le modifiche possono essere sincronizzate da qualsiasi punto nel tempo, in altre parole non è previsto un periodo di conservazione dei dati fisso per cui sono disponibili le modifiche.

* Le modifiche sono disponibili in parallelo per tutte le chiavi di partizione logica di un contenitore di Azure Cosmos. Questa funzionalità consente di apportare modifiche da contenitori di grandi dimensioni per poi elaborarle in parallelo da più consumer.

* Le applicazioni possono richiedere più feed di modifiche nello stesso contenitore contemporaneamente. Changefeedoptions.StartTime è utilizzabile per fornire un punto di partenza iniziale. Ad esempio, per trovare il token di continuazione corrispondente a un tempo specificato. Se si specifica ContinuationToken, questo avrà priorità rispetto ai valori di StartTime e StartFromBeginning. La precisione di ChangeFeedOptions.StartTime è circa di 5 secondi. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed di modifiche nelle API per Cassandra e MongoDB

Change feed functionality is surfaced as change stream in MongoDB API and Query with predicate in Cassandra API. Per altre informazioni sui dettagli di implementazione per l'API MongoDB, vedere [Modifica dei flussi nell'API di Azure Cosmos DB per MongoDB](mongodb-change-streams.md).

Apache Cassandra nativo fornisce Change Data Capture (CDC), un meccanismo per contrassegnare tabelle specifiche per l'archiviazione, nonché rifiutare le scritture in tali tabelle una volta raggiunta una dimensione configurabile su disco per il registro CDC. The change feed feature in Azure Cosmos DB API for Cassandra enhances the ability to query the changes with predicate via CQL. Per altre informazioni sui dettagli di implementazione, vedere [Change feed in Azure Cosmos DB API for Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere altre informazioni sul feed di modifiche negli articoli seguenti:

* [Opzioni per la lettura di feed di modifiche](read-change-feed.md)
* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
* [Uso del processore dei feed di modifiche](change-feed-processor.md)
