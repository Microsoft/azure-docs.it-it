---
title: Output del database SQL di Azure da analisi di flusso di Azure
description: Questo articolo descrive il database SQL di Azure come output per analisi di flusso di Azure.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 631fdba451f69e44a675d396a42e1cddaea50a3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013958"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Output del database SQL di Azure da analisi di flusso di Azure

È possibile usare [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) come output per i dati di natura relazionale o per applicazioni che dipendono dal contenuto ospitato in un database relazionale. I processi di analisi di flusso di Azure scrivono in una tabella esistente del database SQL. Lo schema della tabella deve corrispondere esattamente ai campi e ai relativi tipi nell'output del processo. È anche possibile specificare [Azure sinapsi Analytics](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) come output tramite l'opzione di output del database SQL. Per informazioni sui modi che consentono di migliorare la velocità effettiva di scrittura, vedere l'articolo [Analisi di flusso con database SQL di Azure come output](stream-analytics-sql-output-perf.md).

È anche possibile usare [istanza gestita SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) come output. È necessario [configurare l'endpoint pubblico in SQL istanza gestita](../azure-sql/managed-instance/public-endpoint-configure.md) e quindi configurare manualmente le impostazioni seguenti in analisi di flusso di Azure. La macchina virtuale di Azure che esegue SQL Server con un database collegato è supportata anche dalla configurazione manuale delle impostazioni riportate di seguito.

## <a name="output-configuration"></a>Configurazione di output

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di database SQL.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. |
| Database | Nome del database a cui si sta inviando l'output. |
| Nome server | Nome del server SQL logico o nome dell'istanza gestita. Per Istanza gestita SQL, è necessario specificare la porta 3342. Ad esempio, *sampleserver.public.database.windows.net,3342* |
| Username | Nome utente con accesso in scrittura al database. Analisi di flusso supporta solo l'autenticazione SQL. |
| Password | Password per la connessione al database. |
| Tabella | Nome della tabella in cui viene scritto l'output. Il nome della tabella fa distinzione tra maiuscole e minuscole. Lo schema di questa tabella deve corrispondere esattamente al numero di campi e ai relativi tipi generati dall'output del processo. |
|Eredita schema di partizione| Opzione che consente di ereditare lo schema di partizionamento del passaggio di query precedente, per abilitare la topologia perfettamente parallela con più writer nella tabella. Per altre informazioni, consultare [Output di Analisi di flusso di Azure in Database SQL di Azure](stream-analytics-sql-output-perf.md).|
|Numero massimo di batch| Limite massimo consigliato rispetto al numero di record inviati con ogni transazione di inserimento bulk.|

Sono disponibili due schede che consentono l'output da analisi di flusso di Azure ad analisi sinapsi di Azure: database SQL e sinapsi di Azure. Si consiglia di scegliere la scheda di analisi delle sinapsi di Azure anziché l'adattatore del database SQL se si verifica una delle condizioni seguenti:

* **Velocità effettiva**: se la velocità effettiva prevista ora o in futuro è superiore a 10 MB/sec, usare l'opzione di output di Azure Synapse per ottenere prestazioni migliori.

* **Partizioni di input**: se si dispone di otto o più partizioni di input, usare l'opzione di output di Azure Synapse per aumentare il numero di istanze.

## <a name="partitioning"></a>Partizionamento

Il partizionamento deve essere abilitato ed è basato sulla clausola PARTITION BY nella query. Quando l'opzione eredita partizionamento è abilitata, segue il partizionamento dell'input per le [query completamente eseguibili](stream-analytics-scale-jobs.md). Per altre informazioni su come ottenere migliori prestazioni per la velocità effettiva di scrittura quando si caricano dati nel database SQL di Azure, vedere [Output di Analisi di flusso di Azure nel database SQL di Azure](stream-analytics-sql-output-perf.md).

## <a name="output-batch-size"></a>Dimensione del batch di output

È possibile configurare la dimensione massima del messaggio usando il **numero massimo di batch**. Il valore massimo predefinito è 10.000 e il valore minimo predefinito è 100 righe per singolo inserimento bulk. Per altre informazioni, vedere [limiti di SQL Azure](../azure-sql/database/resource-limits-logical-server.md). Per ogni batch viene inizialmente eseguito l'inserimento bulk con il numero massimo di batch. Il batch può essere diviso a metà (fino al numero minimo di batch) in base a errori SQL non irreversibili.

## <a name="next-steps"></a>Passaggi successivi

* [Usare le identità gestite per accedere al database SQL di Azure o a Azure sinapsi Analytics da un processo di analisi di flusso di Azure (anteprima)](sql-database-output-managed-identity.md)
* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)