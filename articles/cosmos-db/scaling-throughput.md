---
title: Ridimensionare la velocità effettiva per i contenitori di Azure Cosmos DB
description: Questo articolo descrive in che modo Azure Cosmos DB scala la velocità effettiva tra aree diverse in cui viene effettuato il provisioning dell'account Azure Cosmos.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 94b5b3d2ab1f576f87ead23b389252ec96a20f11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397352"
---
# <a name="how-does-azure-cosmos-db-globally-scale-the-provisioned-throughput"></a>In che modo Azure Cosmos DB scalare globalmente la velocità effettiva con provisioning?

In Azure Cosmos DB la velocità effettiva con provisioning viene rappresentata come unità richiesta al secondo (UR/s o il formato plurale UR). Le UR misurano il costo delle operazioni sia lettura che di scrittura sul contenitore Cosmos, come illustrato nell'immagine seguente:

:::image type="content" source="./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png" alt-text="Unità richiesta" border="false":::

È possibile effettuare il provisioning delle unità richiesta in un contenitore Cosmos o in un database Cosmos. Le UR di cui è stato effettuato il provisioning in un contenitore sono disponibili esclusivamente per le operazioni eseguite su tale contenitore. Le unità richiesta con provisioning in un database sono condivise tra tutti i contenitori all'interno del database (ad eccezione di eventuali contenitori esclusivamente assegnati a unità di richiesta).

Per scalare in modo elastico la velocità effettiva con provisioning, è possibile aumentare o diminuire le UR/sec con provisioning in qualsiasi momento. Per altre informazioni, vedere [come eseguire il provisioning della velocità effettiva](set-throughput.md) e scalare in modo elastico i contenitori e i database di Cosmos. Per la velocità effettiva con scalabilità globale, è possibile aggiungere o rimuovere aree dall'account Cosmos in qualsiasi momento. Per altre informazioni, vedere [Aggiungere o rimuovere aree dall'account di database](how-to-manage-database-account.md#addremove-regions-from-your-database-account). L'associazione di più aree a un account Cosmos è importante in molti scenari, per ottenere bassa latenza e [disponibilità elevata](high-availability.md) in tutto il mondo.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Come viene distribuita la velocità effettiva di cui è stato effettuato il provisioning tra le diverse aree

Se si esegue il provisioning delle UR ' *r '* in un contenitore Cosmos (o database), Cosmos DB garantisce che le UR *' r '* siano disponibili in *ogni* area associata all'account Cosmos. Ogni volta che si aggiunge una nuova area al proprio account, Cosmos DB esegue automaticamente il provisioning delle UR *' R '* nell'area appena aggiunta. Le operazioni eseguite sul contenitore Cosmos hanno la garanzia di ottenere le UR *' R '* in ogni area. Non è possibile assegnare in modo selettivo le UR a un'area specifica. Il provisioning delle UR con provisioning in un contenitore Cosmos (o database) viene effettuato in tutte le aree associate all'account Cosmos.

Supponendo che un contenitore Cosmos sia configurato con le UR *' R '* e che ci siano aree *' n'* associate all'account Cosmos, quindi:

- Se l'account Cosmos è configurato con una singola area di scrittura, le UR totali disponibili a livello globale nel contenitore = *R* x *N*.

- Se l'account Cosmos è configurato con più aree di scrittura, le UR totali sono disponibili a livello globale nel contenitore = *R* x (*N*+ 1). Viene effettuato automaticamente il provisioning delle UR *R* aggiuntive per elaborare i conflitti di aggiornamento e il traffico anti-entropia tra le aree.

La scelta del [modello di coerenza](consistency-levels.md) influisca anche sulla velocità effettiva. È possibile ottenere circa 2x velocità effettiva di lettura per i livelli di coerenza più flessibili (ad esempio, *sessione*, *prefisso coerente* e coerenza *finale* ) rispetto ai livelli di coerenza più avanzati (ad *esempio,* decadimento ristretto *o coerenza* assoluta).

## <a name="next-steps"></a>Passaggi successivi

Successivamente, è possibile apprendere come configurare la velocità effettiva in un contenitore o in un database:

* [Ottenere e impostare la velocità effettiva per i contenitori e i database](set-throughput.md) 

