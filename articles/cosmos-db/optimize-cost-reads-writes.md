---
title: Ottimizzazione dei costi delle operazioni di lettura e scrittura in Azure Cosmos DB
description: Questo articolo spiega come ridurre i costi di Azure Cosmos DB durante l'esecuzione di operazioni di lettura e scrittura sui dati.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 725876594a7e7c5f3b3a02802f487dc5fdfb64dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535936"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Ottimizzare il costo delle operazioni di lettura e di scrittura in Azure Cosmos DB

Questo articolo descrive come viene calcolato il costo delle operazioni di lettura e scrittura dei dati da Azure Cosmos DB. Le operazioni di lettura corrispondono alle operazioni get sugli elementi e le operazioni di scrittura corrispondono a operazioni insert, replace, delete e upsert di elementi.  

## <a name="cost-of-reads-and-writes"></a>Costo di letture e scritture

Azure Cosmos DB garantisce prestazioni prevedibili in termini di latenza e velocità effettiva tramite un modello di velocità effettiva con provisioning. La velocità effettiva di cui è stato effettuato il provisioning viene rappresentata in termini di [unità richieste](request-units.md) al secondo, o UR/sec. Un'unità richiesta (UR) è un'astrazione logica sulle risorse di calcolo (CPU, memoria, I/O e così via) necessarie per eseguire una richiesta. La velocità effettiva di cui è stato effettuato il provisioning (UR) viene riservata e dedicata al contenitore o al database perché questo possa garantire latenza e velocità effettiva prevedibili. La velocità effettiva di cui è stato effettuato il provisioning consente ad Azure Cosmos DB di offrire prestazioni coerenti e prevedibili, di garantire una bassa latenza e una disponibilità elevata su qualsiasi scala. Le unità richiesta rappresentano la valuta normalizzata che semplifica il calcolo della quantità di risorse necessaria per un'applicazione. 

Non è necessario preoccuparsi di distinguere le unità richiesta di operazioni di lettura e di operazioni di scrittura. Il modello di valuta unificato per le unità richiesta garantisce l'efficienza necessaria a usare in modo intercambiabile la stessa capacità di velocità effettiva per entrambi i tipi di operazioni. La tabella seguente illustra il costo delle operazioni di lettura e delle operazioni di scrittura in termini di UR/sec per gli elementi con dimensioni 1 KB e 100 KB.

|**Dimensione dell'articolo**  |**Costo operazione di lettura** |**Costo operazione di scrittura**|
|---------|---------|---------|
|1 KB |1 UR |5 UR |
|100 kB |10 UR |50 UR |

La lettura di un elemento con dimensioni pari a 1 K costa una UR. La scrittura di un elemento con dimensioni pari a 1 KB costa cinque UR. I costi di lettura e scrittura sono applicabili quando si usa il [livello di coerenza](consistency-levels.md) della sessione predefinito.  Per le UR è necessario tenere conto di dimensioni, numero di proprietà, coerenza dei dati, proprietà indicizzate, indicizzazione e modelli di query dell'elemento.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Ottimizzare il costo di operazioni di lettura e scrittura

Quando si eseguono operazioni di scrittura, è consigliabile effettuare il provisioning di una capacità sufficiente a supportare il numero di operazioni di scrittura al secondo necessarie. È possibile aumentare la velocità effettiva di cui è stato effettuato il provisioning tramite SDK, il portale o l'interfaccia della riga di comando prima di eseguire le operazioni di scrittura e quindi ridurre la velocità effettiva dopo il completamento di tali operazioni. La velocità effettiva per il periodo di scrittura corrisponde alla velocità effettiva minima necessaria per i dati specifici più la velocità effettiva necessaria per il carico di lavoro di inserimento, presupponendo che non siano in esecuzione altri carichi di lavoro. 

Se si eseguono altri carichi di lavoro contemporaneamente, ad esempio query, operazioni di lettura, aggiornamenti ed eliminazioni, è necessario aggiungere anche le unità richiesta aggiuntive necessarie per tali operazioni. Se le operazioni di scrittura sono soggette a limiti di velocità, è possibile personalizzare i criteri di ripetizione dei tentativi e di backoff tramite gli SDK di Azure Cosmos DB. È ad esempio possibile aumentare il carico fino a quando un numero ridotto di richieste diventa soggetto a limiti di velocità. Se si verifica un limite di velocità, l'applicazione client deve eseguire il backoff per le richieste che limitano la velocità per l'intervallo tra tentativi specificato. Prima di riprovare a eseguire operazioni di scrittura, è necessario lasciar trascorrere un intervallo di tempo minimo. Il supporto dei criteri di ripetizione è incluso in SQL .NET, Java, Node.js e Python SDK e in tutte le versioni supportate dei .NET Core SDK. 

È anche possibile inserire dati in blocco in Azure Cosmos DB o copiare dati da qualsiasi archivio dati di origine supportato ad Azure Cosmos DB tramite [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory si integra in modo nativo con l'API bulk di Azure Cosmos DB per garantire le prestazioni migliori durante la scrittura di dati in Azure Cosmos DB.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile passare ad altre informazioni sull'ottimizzazione dei costi in Azure Cosmos DB con gli articoli seguenti:

* Altre informazioni sull'[Ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni su [come comprendere la fatturazione di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[Ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle query](optimize-cost-queries.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Azure Cosmos multi-area](optimize-cost-regions.md)
