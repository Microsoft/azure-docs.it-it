---
title: Glossario sugli strumenti di database elastici
description: Spiegazione dei termini utilizzati per gli strumenti dei database elastici
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 166e365a4ead8ad6d0f7e543c081161ebff5f027
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330462"
---
# <a name="elastic-database-tools-glossary"></a>Glossario sugli strumenti di database elastici
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I termini seguenti sono definiti per gli [strumenti di database elastici](elastic-scale-introduction.md). Gli strumenti vengono usati per gestire le [mappe partizioni](elastic-scale-shard-map-management.md) e includono la [libreria client](elastic-database-client-library.md), lo [strumento di suddivisione-unione](elastic-scale-overview-split-and-merge.md), i [pool elastici](elastic-pool-overview.md) e le [query](elastic-query-overview.md). 

Questi termini vengono usati in [Aggiunta di una partizione utilizzando gli strumenti di database elastici](elastic-scale-add-a-shard.md) e [Uso della classe RecoveryManager per correggere i problemi delle mappe partizioni](elastic-database-recovery-manager.md).

![Termini della scalabilità elastica][1]

**Database**: un database nel database SQL di Azure. 

**Routing dipendente dei dati**: la funzionalità che consente a un'applicazione di connettersi a una partizione in base a una specifica chiave di partizionamento orizzontale. Vedere [Routing dipendente dei dati](elastic-scale-data-dependent-routing.md). Confrontare con **[Multi-Shard Query](elastic-scale-multishard-querying.md)**.

**Mappa globale partizioni**: il mapping tra le chiavi di partizionamento orizzontale e le rispettive partizioni all'interno di un **set di partizioni**. La mappa partizioni globale viene archiviata nel **gestore mappe partizioni**. Confrontare con la **mappa partizioni locale**.

**Mappa partizioni di tipo elenco**: una mappa partizioni in cui le chiavi di partizionamento orizzontale vengono mappate singolarmente. Confrontare con la **mappa partizioni di tipo intervallo**.   

**Mappa partizioni locale**: archiviata in una partizione, la mappa partizioni locale contiene i mapping per gli shardlet che risiedono nella partizione.

**Esecuzione di query su più partizioni**: la possibilità di eseguire una query su più partizioni; i set di risultati vengono restituiti usando la semantica di UNION ALL (nota anche come "query di tipo fan-out"). Confrontare con **Routing dipendente dai dati**.

**Multi-tenant** e **Tenant singolo**: l'immagine mostra un database a tenant singolo e un database multi-tenant:

![Screenshot che mostra un database a tenant singolo e un database multi-tenant.](./media/elastic-scale-glossary/multi-single-simple.png)

Ecco una rappresentazione di database a tenant singolo e multi-tenant **partizionati** . 

![Database a tenant singolo e multi-tenant](./media/elastic-scale-glossary/shards-single-multi.png)

**Mappa partizioni di tipo intervallo**: una mappa partizioni in cui la strategia di distribuzione delle partizioni è basata su più intervalli di valori contigui. 

**Tabelle di riferimento**: tabelle che non vengono partizionate, ma vengono replicate tra le partizioni. I codici di avviamento postale, ad esempio, possono essere archiviati in una tabella di riferimento. 

**Partizione**: database nel database SQL di Azure che archivia i dati da un set di dati partizionato. 

**Elasticità di partizionamento**: la possibilità di eseguire il **ridimensionamento orizzontale** e il **ridimensionamento verticale**.

**Tabelle partizionate**: tabelle che vengono partizionate, ovvero i cui dati vengono distribuiti tra le partizioni in base ai valori della chiave di partizionamento orizzontale. 

**Chiave di partizionamento orizzontale**: un valore di colonna che determina la modalità di distribuzione dei dati tra le partizioni. Il tipo valore può essere uno dei seguenti: **int**, **bigint**, **varbinary** o **uniqueidentifier**. 

**Set di partizioni**: la raccolta di partizioni attribuite alla stessa mappa partizioni nel gestore delle mappe partizioni.  

**Shardlet**: il complesso dei dati associati a un singolo valore di una chiave di partizionamento orizzontale in una partizione. Uno shardlet è la più piccola unità di spostamento dei dati possibile quando si ridistribuiscono tabelle partizionate. 

**Mappa partizioni**: il set di mapping tra le chiavi di partizionamento orizzontale e le rispettive partizioni.

**Gestore mappe partizioni**: un archivio di dati e oggetti di gestione che contiene le mappe partizioni, i percorsi delle partizioni e i mapping per uno o più set di partizioni.

![Il diagramma mostra un gestore delle mappe partizioni associato a shardmaps_global, shards_global e shard_mappings_global.][2]

## <a name="verbs"></a>Verbi
**Scalare orizzontalmente**: aumentare o ridurre le dimensioni di una raccolta di partizioni aggiungendo o rimuovendo partizioni a una mappa partizioni.

![Scalabilità orizzontale e verticale][3]

**Unire**: spostare shardlet da due partizioni a una partizione e aggiornare la mappa partizioni di conseguenza.

**Spostare shardlet**: spostare un singolo shardlet in una partizione diversa. 

**Partizione**: eseguire il partizionamento orizzontale di dati strutturati in modo identico in più database in base a una chiave di partizionamento orizzontale.

**Dividere**: spostare più shardlet da una partizione a un'altra (in genere nuova). Come punto di divisione viene usata una chiave di partizionamento orizzontale fornita dall'utente.

**Ridimensionamento verticale**: aumento o riduzione delle dimensioni di calcolo di una singola partizione. Ad esempio, modifica di una partizione da Standard a Premium (che comporta più risorse di elaborazione). 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-glossary/glossary.png
[2]: ./media/elastic-scale-glossary/mappings.png
[3]: ./media/elastic-scale-glossary/h_versus_vert.png

