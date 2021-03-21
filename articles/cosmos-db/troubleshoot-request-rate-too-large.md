---
title: Risolvere i problemi relativi alle eccezioni Azure Cosmos DB frequenza di richieste troppo grandi
description: Informazioni su come diagnosticare e correggere le eccezioni per la frequenza delle richieste troppo grandi.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c3a692a8df93931bd8fd7982e69fe2f9118444c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94411338"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Diagnosticare e risolvere i problemi relativi alle eccezioni Azure Cosmos DB frequenza di richieste troppo grandi
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Un messaggio di "frequenza di richiesta troppo grande" o codice di errore 429 indica che le richieste sono soggette A limitazioni.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
La sezione seguente contiene le cause e le soluzioni note per un numero eccessivo di richieste.

### <a name="check-the-metrics"></a>Controllare le metriche
Controllare [Azure Cosmos DB monitoraggio](monitor-cosmos-db.md) per visualizzare il numero di 429 eccezioni.

#### <a name="cause"></a>Causa:
La velocità effettiva utilizzata (unità richiesta al secondo) ha superato la [velocità effettiva con provisioning](set-throughput.md). L'SDK ripete automaticamente le richieste in base ai criteri di ripetizione specificati. Se questo errore si verifica spesso, è consigliabile aumentare la velocità effettiva della raccolta. Controllare le metriche del portale per verificare se sono presenti errori 429. Esaminare la chiave di partizione per assicurarsi che venga generata una [distribuzione uniforme del volume di archiviazione e di richiesta](partitioning-overview.md).

#### <a name="solution"></a>Soluzione:
1. Usare il [portale o l'SDK](set-throughput.md) per aumentare la velocità effettiva con provisioning.
1. Consente di passare al database o al contenitore per la [scalabilità](provision-throughput-autoscale.md)automatica.

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi quando si usa Azure Cosmos DB .NET SDK.
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).
* [Diagnosticare e risolvere](troubleshoot-java-sdk-v4-sql.md) i problemi quando si usa il Azure Cosmos DB Java v4 SDK.
* Informazioni sulle linee guida sulle prestazioni per [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).