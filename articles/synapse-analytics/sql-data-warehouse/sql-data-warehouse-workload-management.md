---
title: Gestione dei carichi di lavoro
description: Linee guida per l'implementazione della gestione del carico di lavoro in Azure sinapsi Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: dd867d4aa9a9ef5ed73e78a46826a8cd5239039b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80744226"
---
# <a name="what-is-workload-management"></a>Definizione di gestione del carico di lavoro

L'esecuzione di carichi di lavoro misti può causare problemi di risorse nei sistemi occupati.  Gli architetti di soluzioni cercano modi per separare le attività di data warehousing classiche, ad esempio il caricamento, la trasformazione e l'esecuzione di query sui dati, per garantire che esistano risorse sufficienti per raggiungere i contratti di servizi.  

L'isolamento dei server fisici può comportare sacche di infrastruttura sottoutilizzate, sovrarubriche o in uno stato in cui le cache vengono costantemente avviate con l'avvio e l'arresto dell'hardware.  Uno schema di gestione del carico di lavoro riuscito gestisce efficacemente le risorse, garantisce un utilizzo delle risorse altamente efficiente e ottimizza il ritorno sugli investimenti (ROI).

Un carico di lavoro data warehouse si riferisce a tutte le operazioni che trascorreno in relazione a un data warehouse. La profondità e l'ampiezza di questi componenti dipendono dal livello di maturità del data warehouse.  Il carico di lavoro data warehouse comprende:

- Intero processo di caricamento dei dati nel warehouse
- Esecuzione di data warehouse analisi e creazione di report
- Gestione dei dati nel data warehouse
- Esportazione dei dati dal data warehouse

La capacità a livello di prestazioni di un data warehouse è determinata dalle [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Per visualizzare le risorse allocate per tutti i profili prestazioni, vedere [limiti di memoria e concorrenza](memory-concurrency-limits.md).
- Per regolare la capacità, è possibile [aumentare o ridurre](quickstart-scale-compute-portal.md)le prestazioni.

## <a name="workload-management-concepts"></a>Concetti relativi alla gestione del carico di lavoro

In passato, per l'analisi SQL in sinapsi di Azure si gestivano le prestazioni delle query tramite [le classi di risorse](resource-classes-for-workload-management.md).  Classi di risorse consentite per l'assegnazione di memoria a una query basata sull'appartenenza ai ruoli.  La sfida principale con le classi di risorse è che, una volta configurata, non era possibile controllare il carico di lavoro.  

Ad esempio, la concessione di un'appartenenza a un ruolo utente ad hoc a smallrc consentiva all'utente di utilizzare il 100% della memoria nel sistema.  Con le classi di risorse, non è possibile riservare e verificare che le risorse siano disponibili per i carichi di lavoro critici.

La gestione del carico di lavoro del pool SQL sinapsi in una sinapsi di Azure è costituita da tre concetti generali: [classificazione del carico](sql-data-warehouse-workload-classification.md)di lavoro, [importanza del carico](sql-data-warehouse-workload-importance.md) di lavoro e [isolamento](sql-data-warehouse-workload-isolation.md)  Queste funzionalità offrono un maggiore controllo sulle modalità di utilizzo delle risorse di sistema da un carico di lavoro.

La classificazione del carico di lavoro è il concetto di assegnazione di una richiesta a un gruppo di carico di lavoro e impostazione dei livelli di importanza.  Storicamente, questa assegnazione è stata eseguita tramite l'appartenenza ai ruoli usando [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Questa operazione può essere eseguita tramite il [classificazione di creazione del carico di lavoro](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  La funzionalità di classificazione offre un set più completo di opzioni quali etichetta, sessione e tempo per classificare le richieste.

L'importanza del carico di lavoro influisce sull'ordine in cui una richiesta ottiene l'accesso alle risorse.  In un sistema occupato, una richiesta con priorità più elevata dispone del primo accesso alle risorse.  L'importanza può inoltre garantire l'accesso ordinato ai blocchi.

L'isolamento del carico di lavoro riserva risorse per un gruppo di carico di lavoro.  Le risorse riservate in un gruppo di carico di lavoro vengono mantenute esclusivamente per il gruppo del carico di lavoro per garantire l'esecuzione  I gruppi del carico di lavoro consentono inoltre di definire la quantità di risorse assegnate per ogni richiesta, in modo analogo alle classi di risorse.  I gruppi del carico di lavoro offrono la possibilità di riservare o limitare la quantità di risorse utilizzabili da un set di richieste.  Infine, i gruppi del carico di lavoro sono un meccanismo per applicare le regole, ad esempio il timeout della query, alle richieste.  

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla classificazione del carico di lavoro, vedere [classificazione dei carichi di lavoro](sql-data-warehouse-workload-classification.md).  
- Per ulteriori informazioni sull'isolamento dei carichi di lavoro, vedere [isolamento del carico di lavoro](sql-data-warehouse-workload-isolation.md).  
- Per ulteriori informazioni sull'importanza del carico di lavoro, vedere [priorità del carico di lavoro](sql-data-warehouse-workload-importance.md).  
- Per ulteriori informazioni sul monitoraggio della gestione del carico di lavoro, vedere [monitoraggio del carico di lavoro portale di gestione](sql-data-warehouse-workload-management-portal-monitor.md).  
