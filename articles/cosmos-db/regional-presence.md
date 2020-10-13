---
title: Presenza a livello di area con Azure Cosmos DB
description: Questo articolo include informazioni sulla presenza a livello di area di Azure Cosmos DB e sui diversi ambienti cloud.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: e2242e8febad5d55813721266f1286250af47111
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082886"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Presenza a livello di area con Azure Cosmos DB

Azure Cosmos DB è un servizio di base in Azure e, per impostazione predefinita, è sempre disponibile in [tutte le aree in cui è disponibile Azure](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all).

:::image type="content" source="./media/regional-presence/regional-presence.png" alt-text="Aree in cui è disponibile Azure Cosmos DB" lightbox="./media/regional-presence/regional-presence.png" border="false":::

Cosmos DB è disponibile in tutti i cinque ambienti cloud distinti di Azure disponibili per i clienti:

* Cloud **Azure pubblico**, disponibile a livello globale.

* **Azure Cina 21ViaNet** è disponibile tramite una partnership univoca tra Microsoft e 21ViaNet, uno dei principali provider Internet in Cina.

* **Azure Germania** fornisce servizi con un modello di trustee dei dati, che garantisce che i dati dei clienti rimangano in Germania sotto il controllo di T-Systems International GmbH, una filiale di Deutsche Telekom, che funge da trustee dei dati tedesco.

* **Azure per enti pubblici** è disponibile in quattro aree negli Stati Uniti per enti pubblici degli Stati Uniti e i rispettivi partner. 

* **Azure per enti pubblici per il dipartimento della difesa (DOD)** è disponibile in due aree del Stati Uniti al dipartimento di difesa degli Stati Uniti.

## <a name="regional-presence-with-global-distribution"></a>Presenza a livello di area con distribuzione globale

Tutte le API esposte da Azure Cosmos DB (incluse SQL, MongoDB, Cassandra, Gremlin e Table) sono disponibili per impostazione predefinita in tutte le aree di Azure. È possibile, ad esempio, che le API MongoDB e Cassandra siano esposte da Azure Cosmos DB non solo in tutte le aree globali di Azure, ma anche in cloud sovrani come le aree Cina, Germania, Government e Department of Defense (DoD).

Azure Cosmos DB è un servizio di database [distribuito a livello globale](distribute-data-globally.md) . È possibile associare un numero qualsiasi di aree di Azure al proprio account Azure Cosmos e i dati vengono replicati automaticamente e in modo trasparente. È possibile aggiungere o rimuovere un'area all'account Azure Cosmos in qualsiasi momento. Con la funzionalità di distribuzione globale chiavi in mano e il protocollo di replica multimaster, Azure Cosmos DB offre latenza inferiore a 10 ms per la lettura e la scrittura al 99° percentile, una disponibilità del 99,999% per lettura e scrittura e la capacità di scalabilità elastica della velocità effettiva di cui è stato effettuato il provisioning per le letture e le scritture in tutte le aree associate all'account Azure Cosmos. Azure Cosmos DB offre anche cinque modelli di coerenza ben definiti ed è possibile scegliere di applicare un modello di coerenza specifico ai dati. Infine, Azure Cosmos DB è l'unico servizio di database nel settore che fornisce una [contratto di servizio completa (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) che comprende la velocità effettiva con provisioning, la latenza al 99 ° percentile, la disponibilità elevata e la coerenza. Le funzionalità sopra riportate sono disponibili in tutti i cloud di Azure.

Il supporto per la replica globale è limitato all'interno di un ambiente cloud di Azure. Ad esempio, Azure Cosmos DB account di *Azure Public* possono eseguire la replica in qualsiasi area di Azure all'interno di *Azure Public* , ma non possono essere replicati nelle aree di Azure in *Azure Cina 21ViaNet*.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile ottenere informazioni sui concetti di base di Azure Cosmos DB con gli articoli seguenti:

* [Distribuire i dati a livello globale](distribute-data-globally.md)
* [Come gestire un account Azure Cosmos DB](manage-account.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md)
* [Contratto di servizio Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
