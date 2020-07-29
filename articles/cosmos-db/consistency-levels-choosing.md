---
title: Scegliere il livello di coerenza appropriato per l'app Azure Cosmos DB
description: Scelta del livello di coerenza ottimale per l'applicazione in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: aa8f2d14124e7cfc5db0025c295f9b38d96bf56c
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921847"
---
# <a name="choose-the-right-consistency-level"></a>Scegliere il livello di coerenza appropriato 

I database distribuiti che di basano sulla replica per gestire la disponibilità elevata, la bassa latenza o entrambe, applicano il compromesso fondamentale tra coerenza di lettura e disponibilità, latenza e velocità effettiva. La maggior parte dei database distribuiti disponibili in commercio chiede agli sviluppatori di scegliere tra i due modelli di coerenza *estremi: coerenza assoluta e coerenza* *finale* . Azure Cosmos DB consente agli sviluppatori di scegliere tra i cinque modelli di coerenza ben definiti: forte, *con*decadimento *ristretto*, *sessione*, *prefisso coerente* e *finale*. Ognuno di questi modelli di coerenza è ben definito, intuitivo e può essere usato per scenari reali specifici. Ognuno dei cinque modelli di coerenza fornisce un [compromesso preciso di disponibilità e prestazioni](consistency-levels-tradeoffs.md) ed è supportato da contratti di contratto completi. È possibile configurare una coerenza predefinita a livello di account e sostituirla [a livello di richiesta](how-to-manage-consistency.md#override-the-default-consistency-level). Le seguenti semplici osservazioni consentiranno all'utente di fare la scelta giusta in molti scenari comuni.

## <a name="sql-api-and-table-api"></a>API SQL e API Tabella

Se l'applicazione viene compilata usando l'API SQL o API Tabella, tenere presente quanto segue:

- Per molti scenari reali, la coerenza di sessione è ottimale ed è l'opzione consigliata. Per ulteriori informazioni, vedere la pagina relativa [alla modalità di gestione del token di sessione per l'applicazione](how-to-manage-consistency.md#utilize-session-tokens).

- Se l'applicazione richiede una coerenza di alto livello, si consiglia di usare il livello di coerenza con decadimento ristretto.

- Se per le operazioni di scrittura vengono richieste garanzie di coerenza più rigorose rispetto a quelle fornite dalla coerenza di sessione e dalla latenza in millisecondi a una cifra, si consiglia di usare il livello di coerenza con decadimento ristretto.  

- Se l'applicazione richiede una coerenza finale, si consiglia di usare il livello di coerenza con prefisso coerente.

- Se vengono richieste garanzie di coerenza meno rigorose rispetto a quelle offerte dalla coerenza di sessione, si consiglia di usare il livello di coerenza con prefisso coerente.

- Se è necessaria la disponibilità più elevata e la latenza più bassa, usare il livello di coerenza finale.

- Se occorre una durabilità dei dati ancora più elevata che non riduca le prestazioni, è possibile creare un livello di coerenza personalizzato a livello di applicazione. Per altre informazioni, vedere [Come implementare la sincronizzazione personalizzata nelle applicazioni](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>API Cassandra, MongoDB e Gremlin

- Per informazioni dettagliate sul mapping tra il "Livello di coerenza di lettura" offerto in Apache Cassandra e i livelli di coerenza di Cosmos DB, vedere [Livelli di coerenza e API di Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).

- Per informazioni dettagliate sul mapping tra il "Problema di lettura" di MongoDB e i livelli di coerenza di Azure Cosmos DB, vedere [Livelli di coerenza e API di Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Garanzie di coerenza in pratica

In pratica, è possibile che si ottengano spesso garanzie di coerenza più complesse. Per un'operazione di lettura, le garanzie di coerenza corrispondono al livello di aggiornamento e ordinamento dello stato del database richiesto. La coerenza di lettura è associata all'ordinamento e alla propagazione delle operazioni di scrittura/aggiornamento.  

* Quando il livello di coerenza è impostato su **decadimento ristretto**, Cosmos DB garantisce che i client leggano sempre il valore di una scrittura precedente, con un intervallo ristretto dalla finestra di decadimento.

* Quando il livello di coerenza è impostato su **assoluto**, la finestra di decadimento equivale a zero e i client hanno la certezza di leggere il valore di commit più recente dell'operazione di scrittura.

* Per i tre livelli di coerenza rimanenti, la finestra di decadimento dipende in gran parte dal carico di lavoro. Ad esempio, se nel database non sono in corso operazioni di scrittura, un'operazione di lettura con livello di coerenza **finale**, **sessione** o **prefisso coerente** è probabile che fornisca gli stessi risultati di un'operazione di lettura con livello di coerenza assoluto.

Se l'account Azure Cosmos è configurato con un livello di coerenza diverso da quello della coerenza assoluta, è possibile determinare la probabilità che i client ottengano letture solide e coerenti per i carichi di lavoro esaminando la metrica di *obsolescenza* (PBS) con decadimento ristretto. Questa metrica viene esposta nel portale di Azure. Per altre informazioni, vedere [Monitorare la metrica del decadimento ristretto probabilistico (Probabilistic Bounded Staleness, PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Il decadimento ristretto probabilistico mostra il livello di finalità della coerenza finale. Questa metrica fornisce informazioni dettagliate sulla frequenza con cui è possibile ottenere una coerenza più avanzata rispetto al livello di coerenza attualmente configurato nell'account Azure Cosmos. In altre parole, è possibile visualizzare la probabilità (misurata in millisecondi) di ottenere letture con coerenza assoluta per una combinazione di aree di scrittura e lettura.

## <a name="next-steps"></a>Passaggi successivi

È possibile reperire altre informazioni sui livelli di coerenza negli articoli seguenti:

* [Mapping dei livelli di coerenza nelle API Cosmos DB](consistency-levels-across-apis.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Come gestire il token di sessione per l'applicazione](how-to-manage-consistency.md#utilize-session-tokens)
* [Monitorare la metrica del decadimento ristretto probabilistico (Probabilistic Bounded Staleness, PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
