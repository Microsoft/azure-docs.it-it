---
title: Transazioni del database e controllo della concorrenza ottimistica in Azure Cosmos DB
description: Questo articolo illustra le transazioni del database e il controllo della concorrenza ottimistica in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: d453bb4071c4a6972e01b8f7e90375181caf6d01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74806525"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transazioni e controllo della concorrenza ottimistica

Le transazioni di database offrono un modello di programmazione sicuro e prevedibile per gestire le modifiche simultanee ai dati. I database relazionali tradizionali, come SQL Server, consentono di scrivere la logica di business utilizzando stored procedure e/o trigger, inviarli al server per l'esecuzione direttamente nel motore di database. Con i database relazionali tradizionali, è necessario gestire due linguaggi di programmazione diversi, ovvero il linguaggio di programmazione delle applicazioni (non transazionale), ad esempio JavaScript, Python, C#, Java e così via, e il linguaggio di programmazione transazionale (ad esempio, T-SQL) eseguito in modo nativo dal database.

Il motore di database in Azure Cosmos DB supporta transazioni conformi ACID con l'isolamento dello snapshot. Tutte le operazioni di database all'interno dell'ambito della [partizione logica](partition-data.md) di un contenitore vengono eseguite in modo transazionale all'interno del motore di database ospitato dalla replica della partizione. Tali operazioni includono sia operazioni di scrittura (aggiornamento di uno o più elementi all'interno della partizione logica) e lettura. Nella tabella seguente vengono illustrati diversi tipi di transazioni e operazioni:

| **Operazione**  | **Tipo di operazione** | **Transazione singola o con più elementi** |
|---------|---------|---------|
| Inserimento (senza un trigger di pre/post) | Scrittura | Transazione di un singolo elemento |
| Inserimento (con un trigger di pre/post) | Lettura e scrittura | Transazione di più elementi |
| Sostituzione (senza un trigger di pre/post) | Scrittura | Transazione di un singolo elemento |
| Sostituzione (con un trigger di pre/post) | Lettura e scrittura | Transazione di più elementi |
| Upsert (senza un trigger di pre/post) | Scrittura | Transazione di un singolo elemento |
| Upsert (con un trigger di pre/post) | Lettura e scrittura | Transazione di più elementi |
| Eliminazione (senza un trigger di pre/post) | Scrittura | Transazione di un singolo elemento |
| Eliminazione (con un trigger di pre/post) | Lettura e scrittura | Transazione di più elementi |
| Esegui stored procedure | Lettura e scrittura | Transazione di più elementi |
| Il sistema ha avviato l'esecuzione di una procedure di unione | Scrittura | Transazione di più elementi |
| Il sistema ha avviato l'esecuzione dell'eliminazione di elementi in base alla scadenza (durata TTL) di un elemento | Scrittura | Transazione di più elementi |
| Lettura | Lettura | Transazione di un singolo elemento |
| Feed di modifiche | Lettura | Transazione di più elementi |
| Lettura impaginata | Lettura | Transazione di più elementi |
| Query impaginata | Lettura | Transazione di più elementi |
| Eseguire la funzione definita dall'utente come parte della query impaginata | Lettura | Transazione di più elementi |

## <a name="multi-item-transactions"></a>Transazioni di più elementi

Azure Cosmos DB consente di scrivere [stored procedure, trigger pre/post, funzioni definite dall'utente (UDF)](stored-procedures-triggers-udfs.md) e procedure di merge in JavaScript. Azure Cosmos DB supporta in modo nativo l'esecuzione di JavaScript nel relativo motore di database. È possibile registrare stored procedure, trigger di pre/post, funzioni definite dall'utente (UDF) e le procedure di unione in un contenitore e successivamente eseguirli a livello transazionale all'interno del motore di database Azure Cosmos. La scrittura della logica di applicazione in JavaScript permette l'espressione naturale del flusso di controllo, definizione dell'ambito delle variabili e assegnazione e integrazione delle primitive di gestione delle eccezioni con transazioni di database direttamente sotto forma di linguaggio di programmazione JavaScript.

Sulle stored procedure, trigger, UDF e procedure di unione basati su JavaScript viene eseguito il wrapping all'interno di una transazione di ambiente ACID con isolamento dello snapshot tra tutti gli elementi all'interno della partizione logica. Se JavaScript genera un'eccezione durante l'esecuzione, l'intera transazione sarà interrotta e verrà eseguito il rollback. Il modello di programmazione risultante è semplice ma efficace. Gli sviluppatori JavaScript ottengono un modello di programmazione durevole, usando al tempo stesso i costrutti dei propri linguaggi preferiti e i primitivi di librerie.

La possibilità di eseguire JavaScript direttamente nel motore di database offre prestazioni ed esecuzione transazionale di operazioni di database negli elementi di un contenitore. Poiché il motore di database di Azure Cosmos DB supporta in modo nativo JSON e JavaScript, non vi sono eventuali mancate corrispondenze di impedenza tra i sistemi di tipi di un applicazione e del database.

## <a name="optimistic-concurrency-control"></a>Controllo della concorrenza ottimistica

Il controllo della concorrenza ottimistica consente di evitare la perdita di aggiornamenti ed eliminazioni. Operazioni simultanee in conflitto vengono sottoposte al regolare blocco pessimistico del motore di database ospitato dalla partizione logica che possiede l'elemento. Quando due operazioni simultanee tentano di aggiornare la versione più recente di un elemento all'interno di una partizione logica, una di esse avrà la precedenza e l'altra avrà esito negativo. Tuttavia, se una o due operazioni che tentano di aggiornare contemporaneamente lo stesso elemento avevano precedentemente letto un valore meno recente dell'elemento, il database non riconosce se il valore precedentemente letto da una o entrambe le operazioni in conflitto era effettivamente il valore più recente dell'elemento. Fortunatamente, questa situazione può essere rilevata con il **controllo della concorrenza ottimistica** prima di consentire alle due operazioni di immettere il limite della transazione all'interno del motore di database. Il controllo di concorrenza ottimistica protegge i dati impedendo che vengano sovrascritte le modifiche apportate da altri utenti. Impedisce anche ad altri utenti di sovrascrivere accidentalmente le proprie modifiche.

Gli aggiornamenti simultanei di un oggetto sono soggetti al controllo di concorrenza ottimistica dal livello di protocollo di comunicazione di Azure Cosmos DB. Il database di Azure Cosmos garantisce che la versione lato client dell'elemento in fase di aggiornamento (o di eliminazione) sia la stessa versione dell'elemento nel contenitore di Azure Cosmos. In questo modo si garantisce che le scritture non vengano sovrascritta accidentalmente dalle operazioni di scrittura di altri utenti e viceversa. In un ambiente multiutente, il controllo della concorrenza ottimistica protegge l'utente da un'accidentale eliminazione o aggiornamento della versione errata di un elemento. Di conseguenza, gli elementi sono protetti contro i famigerati problemi di "aggiornamento perso" o "eliminazione persa".

Ogni elemento archiviato in un contenitore di Azure Cosmos ha un sistema definito proprietà `_etag`. Il valore di `_etag` viene automaticamente generato e aggiornato dal server ogni volta che viene aggiornato l'elemento. `_etag`può essere usato con l'intestazione della `if-match` richiesta fornita dal client per consentire al server di decidere se un elemento può essere aggiornato in modo condizionale. Il valore dell' `if-match` intestazione corrisponde al valore di `_etag` nel server, l'elemento viene quindi aggiornato. Se il valore dell'intestazione `if-match` della richiesta non è più aggiornato, il server rifiuta l'operazione con un messaggio di risposta "errore di precondizione http 412". Il client può quindi recuperare nuovamente l'elemento per acquisire la versione corrente dell'elemento nel server o eseguire l'override della versione dell'elemento nel server con il proprio `_etag` valore per l'elemento. Inoltre, `_etag` può essere usato con l' `if-none-match` intestazione per determinare se è necessaria una rilettura di una risorsa.

Il `_etag` valore dell'elemento viene modificato ogni volta che l'elemento viene aggiornato. Per le operazioni Replace Item `if-match` , deve essere espressa in modo esplicito come parte delle opzioni di richiesta. Per un esempio, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L578-L674). `_etag`i valori vengono controllati in modo implicito per tutti gli elementi scritti dal stored procedure. Se viene rilevato un conflitto, il stored procedure eseguirà il rollback della transazione e genererà un'eccezione. Con questo metodo, tutte o nessuna scrittura all'interno della stored procedure viene applicata in modo atomico. Si tratta di un segnale per l'applicazione per riapplicare gli aggiornamenti e ripetere la richiesta del client originale.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle transazioni del database e il controllo della concorrenza ottimistica negli articoli seguenti:

- [Usare database, contenitori ed elementi di Azure Cosmos](databases-containers-items.md)
- [Livelli di coerenza](consistency-levels.md)
- [Tipi di conflitto e criteri di risoluzione dei conflitti](conflict-resolution-policies.md)
- [Stored procedure, trigger e funzioni definite dall'utente](stored-procedures-triggers-udfs.md)
