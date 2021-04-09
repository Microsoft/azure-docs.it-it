---
title: Consigli Azure Advisor del pool SQL dedicato
description: Informazioni sulle raccomandazioni di Synapse SQL e su come vengono generate
services: synapse-analytics
author: gaursa
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 698c14d23e8e64c777260410c625129cd87d49b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585707"
---
# <a name="azure-advisor-recommendations-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Advisor raccomandazioni per il pool SQL dedicato in Azure sinapsi Analytics

Questo articolo descrive le raccomandazioni del pool SQL dedicato disponibili in Azure Advisor.  

Il pool SQL dedicato fornisce consigli per garantire che il carico di lavoro data warehouse sia costantemente ottimizzato per le prestazioni. Grazie alla stretta integrazione con [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), le procedure consigliate vengono presentate direttamente nel [portale di Azure](https://aka.ms/Azureadvisor). Il pool SQL dedicato raccoglie i dati di telemetria e i consigli relativi alle superfici per il carico di lavoro attivo a cadenza giornaliera. Di seguito vengono descritti gli scenari per i quali è supportata la presentazione di raccomandazioni e viene illustrato come applicare le azioni consigliate.

È possibile [verificare la disponibilità di raccomandazioni](https://aka.ms/Azureadvisor) oggi stesso. 

## <a name="data-skew"></a>Asimmetria dei dati

Durante l'esecuzione del carico di lavoro, l'asimmetria dei dati può causare uno spostamento dati aggiuntivo o colli di bottiglia nelle risorse. La documentazione seguente descrive come identificare le asimmetrie dei dati e come evitarle selezionando una chiave di distribuzione ottimale.

- [Identificare e rimuovere un'asimmetria](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Statistiche mancanti o non aggiornate

Una qualità non ottimale delle statistiche può compromettere significativamente le prestazioni delle query perché Query Optimizer di SQL potrebbe generare piani di query non ottimali. La documentazione seguente descrive le procedure consigliate per la creazione e l'aggiornamento delle statistiche:

- [Creazione e aggiornamento delle statistiche delle tabelle](sql-data-warehouse-tables-statistics.md)

Per visualizzare l'elenco delle tabelle interessate da questi consigli, eseguire il comando [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables) seguente. Advisor esegue in modo continuo lo stesso script T-SQL per generare tali consigli.

## <a name="replicate-tables"></a>Tabelle replicate

Per consigli sulla tabella replicata, Advisor rileva candidati di tabella basati sulle caratteristiche fisiche seguenti:

- Dimensione tabella replicata
- Numero di colonne
- Tipo di distribuzione della tabella
- Numero di partizioni

Advisor sfrutta in modo continuativo l'euristica basata sul carico di lavoro, ad esempio frequenza di accesso alla tabella, righe restituite in media e soglie sulle dimensioni e l'attività dei data warehouse, per verificare che vengano generati consigli di alta qualità.

La sezione seguente descrive l'euristica basata sul carico di lavoro che è possibile trovare nel portale di Azure per ogni raccomandazione relativa alla tabella replicata:

- Media analisi: percentuale media di righe restituite dalla tabella per ogni accesso alla tabella negli ultimi sette giorni.
- Lettura frequente, nessun aggiornamento: indica che la tabella non è stata aggiornata negli ultimi sette giorni durante la visualizzazione dell'attività di accesso.
- Percentuale di lettura/aggiornamento: la percentuale della frequenza di accesso alla tabella rispetto all'aggiornamento negli ultimi sette giorni.
- Attività: misura l'utilizzo in base all'attività di accesso. L'attività di accesso alla tabella viene confrontata con l'attività media di accesso alla tabella nel data warehouse negli ultimi sette giorni.

Attualmente Advisor visualizza al massimo quattro candidati di tabella replicata alla volta con indici columnstore cluster con priorità per l'attività più elevata.

> [!IMPORTANT]
> I consigli relativi alla tabella replicata non costituiscono una prova completa e non prendono in considerazione operazioni di spostamento dei dati. Microsoft sta lavorando per l'aggiunta di questa opzione come elemento euristico, ma nel frattempo è sempre opportuno convalidare il carico di lavoro dopo aver applicato il consiglio. Per altre informazioni sulle tabelle replicate, consultare la [documentazione](design-guidance-for-replicated-tables.md#what-is-a-replicated-table) seguente.


## <a name="adaptive-gen2-cache-utilization"></a>Utilizzo della cache adattiva (Gen2)
Quando il working set è di grandi dimensioni, la percentuale di riscontri nella cache potrebbe essere bassa e l'utilizzo della cache potrebbe risultare elevato. In questo tipo di scenario è opportuno aumentare le prestazioni in modo da incrementare la capacità della cache e rieseguire il carico di lavoro. Per altre informazioni, vedere la [documentazione](./sql-data-warehouse-how-to-monitor-cache.md) seguente. 

## <a name="tempdb-contention"></a>Conflitto di tempdb

Quando si verifica un conflitto di tempdb significativo, le prestazioni delle query possono peggiorare.  Un conflitto di tempdb può verificarsi per via delle tabelle temporanee definite dall'utente o nel caso in cui venga spostato un grande quantitativo di dati. In questo tipo di scenario è possibile eseguire un ridimensionamento per aumentare l'allocazione di tempdb e [configurare le classi di risorse e la gestione del carico di lavoro](./sql-data-warehouse-workload-management.md) in modo da rendere disponibile più memoria per le query. 

## <a name="data-loading-misconfiguration"></a>Configurazione errata del caricamento dei dati

È necessario caricare sempre i dati da un account di archiviazione nella stessa area del pool SQL dedicato per ridurre al minimo la latenza. Usare l' [istruzione Copy per l'inserimento di dati con velocità effettiva elevata](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) e suddividere i file di gestione temporanea nell'account di archiviazione per ottimizzare la velocità effettiva. Se non è possibile usare l'istruzione COPY, è possibile usare l'API SqlBulkCopy o BCP con una dimensione di batch elevata per una migliore velocità effettiva. Per informazioni aggiuntive sul caricamento dei dati, vedere la [documentazione](./guidance-for-loading-data.md)seguente.