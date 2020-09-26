---
title: Eliminare un progetto di Azure Migrate
description: In questo articolo viene illustrato come è possibile eliminare un progetto di Azure Migrate usando il portale di Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: c58081e7cf88ac4820aa7d4f367b7344f3340a77
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307875"
---
# <a name="delete-an-azure-migrate-project"></a>Eliminare un progetto di Azure Migrate

Questo articolo descrive come eliminare un progetto [Azure migrate](./migrate-services-overview.md) .


## <a name="before-you-start"></a>Prima di iniziare

Prima di eliminare un progetto:

- Quando si elimina un progetto, il progetto e i metadati del computer individuati vengono eliminati.
- Se è stata collegata un'area di lavoro Log Analytics allo strumento Server assessment per l'analisi delle dipendenze, decidere se si desidera eliminare l'area di lavoro. 
    - L'area di lavoro non viene eliminata automaticamente. Eliminarla manualmente.
    - Verificare l'utilizzo di un'area di lavoro prima di eliminarla. È possibile utilizzare la stessa area di lavoro Log Analytics per più scenari.
    - Prima di eliminare il progetto, è possibile trovare un collegamento all'area di lavoro in **Azure migrate-Servers**  >  **Azure migrate-server Assessment**, nell' **area di lavoro di OMS**.
    - Per eliminare un'area di lavoro dopo l'eliminazione di un progetto, trovare l'area di lavoro nel gruppo di risorse pertinente e seguire [queste istruzioni](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Eliminare un progetto


1. Nella portale di Azure aprire il gruppo di risorse in cui è stato creato il progetto.
2. Nella pagina gruppo di risorse selezionare **Mostra tipi nascosti**.
3. Selezionare il progetto e le risorse associate che si desidera eliminare.
    - Il tipo di risorsa per Azure Migrate progetti è **Microsoft. migrate/migrateprojects**.
    - Nella sezione successiva esaminare le risorse create per l'individuazione, la valutazione e la migrazione in un progetto Azure Migrate.
    - Se il gruppo di risorse contiene solo il progetto di Azure Migrate, è possibile eliminare l'intero gruppo di risorse.
    - Se si desidera eliminare un progetto dalla versione precedente di Azure Migrate, i passaggi sono gli stessi. Il tipo di risorsa per questi progetti è il **progetto di migrazione**.


## <a name="created-resources"></a>Risorse create

In queste tabelle vengono riepilogate le risorse create per l'individuazione, la valutazione e la migrazione in un progetto Azure Migrate.

> [!NOTE]
> Eliminare l'insieme di credenziali delle chiavi con cautela perché potrebbe contenere chiavi di sicurezza.

### <a name="vmwarephysical-server"></a>VMware/server fisico

**Risorsa** | **Tipo**
--- | ---
"Appliancename" KV | Insieme di credenziali delle chiavi
Sito "appliancename" | Microsoft. OffAzure/VMwareSites
ProjectName | Microsoft. migrate/migrateprojects
Progetto "NomeProgetto" | Microsoft. migrate/assessmentProjects
Rsvault "NomeProgetto" | Insieme di credenziali dei servizi di ripristino
"NomeProgetto"-MigrateVault-* | Insieme di credenziali dei servizi di ripristino
migrateappligwsa* | Account di archiviazione
migrateapplilsa* | Account di archiviazione
migrateapplicsa* | Account di archiviazione
migrateapplikv* | Insieme di credenziali delle chiavi
migrateapplisbns16041 | Spazio dei nomi del bus di servizio

### <a name="hyper-v-vm"></a>Macchina virtuale Hyper-V 

**Risorsa** | **Tipo**
--- | ---
ProjectName | Microsoft. migrate/migrateprojects
Progetto "NomeProgetto" | Microsoft. migrate/assessmentProjects
HyperV * KV | Insieme di credenziali delle chiavi
Sito di HyperV * | Microsoft. OffAzure/HyperVSites
"NomeProgetto"-MigrateVault-* | Insieme di credenziali dei servizi di ripristino


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aggiungere altri strumenti di [valutazione](how-to-assess.md) e [migrazione](how-to-migrate.md) . 
