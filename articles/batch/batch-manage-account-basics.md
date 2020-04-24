---
title: Gestire l'account
description: Informazioni su ciò che comprende un account Azure Batch
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce0c612e3434249c0c7d078949b10e0b9f6b1f10
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116010"
---
# <a name="manage-your-batch-account"></a>Gestire l'account batch

Un account Batch è un'entità identificata in modo univoco all'interno del servizio Batch. Tutte le operazioni di elaborazione sono associata a un account Batch.

È possibile creare un account Batch di Azure usando il [portale di Azure](batch-account-create-portal.md) o a livello di codice, ad esempio con la [libreria di gestione .NET per Batch](batch-management-dotnet.md). Quando si crea l'account, è possibile associare un account di archiviazione di Azure per l'archiviazione di applicazioni e dati di input e output correlati al processo.

È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

## <a name="components-of-the-batch-account"></a>Componenti dell'account batch

L'account batch consente di eseguire in modo efficiente processi batch paralleli e HPC (High Performance Computing) su larga scala in Azure. All'interno dell'account gestito:

- Applicazioni in esecuzione

- Allocazione di pool e nodi all'interno di pool

- Numero e tipi di attività 

- Input e output dei dati. Non è necessario installare software aggiuntivo per gestire le attività.

- Quando si crea l'account batch, viene richiesto di assegnarvi un nome. Questo nome è l'ID e, una volta assegnato, non può essere modificato.

- Per modificare il nome di un account, è necessario eliminarlo e creare un nuovo account batch.

- L'account viene creato nella sottoscrizione che si vuole usare.

- Usare l'account per identificare e recuperare le chiavi degli account primarie e secondarie da qualsiasi account batch all'interno della sottoscrizione.

- L'account mantiene le informazioni sull'allocazione del pool e le quote principali.  

- L'account contiene informazioni sulla posizione.

- L'account identifica l'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- Creare un account batch usando il [portale di Azure](batch-account-create-portal.md).
- Creare un account batch a livello di codice, ad esempio con la [libreria batch Management .NET](batch-management-dotnet.md).
- [Configurare o disabilitare l'accesso remoto ai nodi di calcolo in un pool di Azure batch](pool-endpoint-configuration.md).
- [Eseguire attività di preparazione e rilascio del processo in nodi di calcolo di Batch](batch-job-prep-release.md)

