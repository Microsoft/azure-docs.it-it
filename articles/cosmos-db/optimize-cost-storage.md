---
title: Ottimizzare i costi di archiviazione in Azure Cosmos DB
description: Questo articolo illustra come gestire i costi di archiviazione dei dati archiviati in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 92bd3ff925080def4b2f074d07e662dfdbdbee01
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080851"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Ottimizzare i costi di archiviazione in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB offre archiviazione e velocità effettiva illimitate. A differenza della velocità effettiva, di cui è necessario effettuare il provisioning o è necessario configurare nei contenitori o database di Azure Cosmos, l'archiviazione viene fatturata in base al consumo. La fatturazione viene effettuata solo per lo spazio di archiviazione logico utilizzato e non è necessario riservare in anticipo alcuno spazio di archiviazione. L'archiviazione viene scalata automaticamente in base ai dati aggiunti o rimossi in un contenitore di Azure Cosmos.

## <a name="storage-cost"></a>Costo di archiviazione

Lo spazio di archiviazione viene fatturato in GB. Lo spazio di archiviazione locale basato su unità SSD viene usato dai dati e dall'indicizzazione. Lo spazio di archiviazione totale usato è uguale allo spazio di archiviazione richiesto dai dati e dagli indici usati in tutte le aree in cui si usa Azure Cosmos DB. Se si replica a livello globale un account Azure Cosmos in tre aree, si pagherà il costo di archiviazione totale in ognuna di queste tre aree. Per stimare i requisiti di archiviazione, vedere lo strumento [Capacity Planner](https://www.documentdb.com/capacityplanner). Il costo dell'archiviazione in Azure Cosmos DB è di $0,25 GB/mese. Per gli ultimi aggiornamenti, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/). È possibile impostare gli avvisi per determinare lo spazio di archiviazione usato dal contenitore Azure Cosmos. Per monitorare lo spazio di archiviazione, vedere l'articolo [Monitorare Azure Cosmos DB](./monitor-cosmos-db.md).

## <a name="optimize-cost-with-item-size"></a>Ottimizzare i costi con le dimensioni dell'elemento

Per prestazioni ottimali e vantaggi economici Azure Cosmos DB prevede che le dimensioni dell'elemento siano al massimo di 2 MB. Se è necessario archiviare un elemento superiore a 2 MB di dati, provare a riprogettare lo schema dell'elemento. Nel raro caso in cui non sia possibile riprogettare lo schema, è possibile suddividere l'elemento in elementi secondari e collegarli in modo logico con un identificatore comune (ID). Tutte le funzionalità di Azure Cosmos DB funzionano in modo coerente ancorandosi a quell'identificatore logico.

## <a name="optimize-cost-with-indexing"></a>Ottimizzare i costi con l'indicizzazione

Per impostazione predefinita, i dati vengono indicizzati automaticamente, il che può aumentare lo spazio di archiviazione totale utilizzato. Tuttavia, è possibile applicare alcuni criteri di indicizzazione personalizzati per ridurre questo sovraccarico. L'indicizzazione automatica che non è stata ottimizzata tramite i criteri è pari a circa il 10-20% delle dimensioni dell'elemento. Rimuovendo o personalizzando i criteri di indicizzazione, non si pagano costi aggiuntivi per le scritture e non si richiede una capacità di elaborazione aggiuntiva. Per configurare i criteri di indicizzazione personalizzati, vedere [Indicizzazione in Azure Cosmos DB](index-policy.md). Se in precedenza si è lavorato con i database relazionali, si potrebbe pensare che "indicizzare tutto" significhi raddoppiare se non di più lo spazio di archiviazione. Tuttavia, nel caso medio in Azure Cosmos DB lo spazio di archiviazione utilizzato risulta essere molto più basso. In Azure Cosmos DB, l'overhead di archiviazione dell'indice è in genere basso (10-20%) anche con l'indicizzazione automatica, perché è progettato per occupare uno spazio di archiviazione ridotto. Grazie alla gestione dei criteri di indicizzazione, è possibile controllare il compromesso di spazio occupato per gli indici e prestazioni delle query in modo più dettagliato.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Ottimizzare i costi con la durata e il feed di modifiche

Una volta che i dati non sono più necessari, è possibile eliminarli normalmente dall'account Azure Cosmos usando la [durata](time-to-live.md) e il [feed di modifiche](change-feed.md) oppure è possibile migrare i vecchi dati in un altro archivio dati come l'archiviazione BLOB di Azure o Azure Data Warehouse. Impostando un valore di durata, o TTL, Azure Cosmos DB consente di eliminare automaticamente elementi da un contenitore dopo un determinato periodo di tempo. Per impostazione predefinita, è possibile impostare la durata a livello di contenitore ed eseguire l'override del valore per singolo elemento. Dopo aver impostato la durata a livello di contenitore o di elemento, Azure Cosmos DB rimuove automaticamente questi elementi dopo il periodo di tempo specificato, a partire dall'ora dell'ultima modifica. Usando il feed di modifiche, è possibile migrare i dati in un altro contenitore di Azure Cosmos DB o in un archivio dati esterno. La migrazione non prevede tempi di inattività e al termine della migrazione è possibile eliminare o configurare la durata per eliminare il contenitore di Azure Cosmos di origine.

## <a name="optimize-cost-with-rich-media-data-types"></a>Ottimizzare i costi con i tipi di dati multimediali avanzati 

Se si vogliono archiviare tipi di dati multimediali avanzati, ad esempio video, immagini e così via, sono disponibili una serie di opzioni in Azure Cosmos DB. Un'opzione consiste nell'archiviare questi tipi di dati multimediali avanzati come elementi di Azure Cosmos. È previsto un limite di 2 MB per ogni elemento ed è possibile ovviare a questo limite concatenando l'elemento di dati in più elementi secondari. Oppure è possibile archiviarli nell'archiviazione BLOB di Azure e usare i metadati per farvi riferimento dagli elementi di Azure Cosmos. Esistono una serie di vantaggi e svantaggi con questo approccio. Il primo approccio offre le migliori prestazioni in termini di contratti di servizio per latenza, velocità effettiva nonché funzionalità di distribuzione globale chiavi in mano per i tipi di dati multimediali avanzati oltre ai normali elementi di Azure Cosmos. Tuttavia, il supporto è disponibile a un prezzo più alto. Archiviando i dati multimediali nell'archiviazione BLOB di Azure Blob, è possibile ridurre i costi complessivi. Se la latenza è critica, è possibile usare Archiviazione Premium per i file multimediali avanzati a cui viene fatto riferimento dagli elementi di Azure Cosmos. Questo si integra in modo nativo con la rete CDN per gestire le immagini dal server perimetrale a costi inferiori per ovviare alle restrizioni geografiche. Il lato negativo di questo scenario è che è necessario gestire due servizi, Azure Cosmos DB e Archiviazione BLOB di Azure, con un possibile aumento dei costi operativi. 

## <a name="check-storage-consumed"></a>Controllare lo spazio di archiviazione utilizzato

Per controllare lo spazio di archiviazione utilizzato da un contenitore di Azure Cosmos, è possibile eseguire una richiesta HEAD o GET per il contenitore e controllare le intestazioni `x-ms-request-quota` e `x-ms-request-usage`. In alternativa, quando si usa .NET SDK, è possibile usare le proprietà [le](/previous-versions/azure/dn850325(v%3Dazure.100))e [DocumentSizeUsage](/previous-versions/azure/dn850324(v=azure.100)) per ottenere lo spazio di archiviazione utilizzato.

## <a name="using-sdk"></a>Uso dell'SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Passaggi successivi

È ora possibile passare ad altre informazioni sull'ottimizzazione dei costi in Azure Cosmos DB con gli articoli seguenti:

* Altre informazioni sull'[Ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni su [come comprendere la fatturazione di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[Ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione del costo delle query](./optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Azure Cosmos multi-area](optimize-cost-regions.md)
