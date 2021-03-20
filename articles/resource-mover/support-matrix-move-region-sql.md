---
title: Supporto per lo spostamento delle risorse SQL di Azure tra le aree con Azure Resource Mover.
description: Esaminare il supporto per lo spostamento delle risorse SQL di Azure tra le aree con Azure Resource Mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 22a7738c2d4d3cc02c03c233e0821f07b459dd94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452091"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Supporto per lo trasferimento di risorse SQL di Azure tra aree di Azure

Questo articolo riepiloga il supporto e i prerequisiti per lo spostamento delle risorse SQL di Azure tra aree di Azure con il motore di risorse di Azure.

## <a name="requirements"></a>Requisiti

I requisiti sono riepilogati nella tabella seguente.

**Funzionalità** | **Supportato/Non supportato** | **Dettagli**
--- | --- | ---
**Hyperscale nel database SQL di Azure** | Non supportato | Non è possibile spostare i database nel livello di servizio dell'iperscalabilità SQL di Azure con il motore di risorse.
**Ridondanza della zona** | Supportato |  Opzioni di spostamento supportate:<br/><br/> -Tra aree che supportano la ridondanza della zona.<br/><br/> -Tra aree che non supportano la ridondanza della zona.<br/><br/> -Tra un'area che supporta la ridondanza della zona in un'area che non supporta la ridondanza della zona.<br/><br/> -Tra un'area che non supporta la ridondanza della zona in un'area che supporta la ridondanza della zona. 
**Sincronizzazione dati** | Database hub/Sync: non supportato<br/><br/> Membro di sincronizzazione: supportato. | Se un membro di sincronizzazione viene spostato, è necessario configurare la sincronizzazione dei dati per il nuovo database di destinazione.
**Replica geografica esistente** | Supportato | Le repliche geografiche esistenti vengono mappate nuovamente al nuovo database primario nell'area di destinazione.<br/><br/> Il seeding deve essere inizializzato dopo lo spostamento. [Scopri di più](../azure-sql/database/active-geo-replication-configure-portal.md)
**Transparent Data Encryption (Transparent Data Encryption) con Bring Your Own Key (BYOK)** | Supportato | [Altre](../key-vault/general/move-region.md) informazioni sullo trasferimento di insiemi di credenziali delle chiavi tra le aree.
**Transparent Data Encryption con chiave gestita dal servizio** | Supportata. |  [Altre](../key-vault/general/move-region.md) informazioni sullo trasferimento di insiemi di credenziali delle chiavi tra le aree.
**Regole per la maschera dati dinamica** | Supportata. | Le regole vengono copiate automaticamente nell'area di destinazione come parte dello spostamento. [Altre informazioni](../azure-sql/database/dynamic-data-masking-configure-portal.md)
**Sicurezza dei dati avanzata** | Non supportata. | Soluzione alternativa: configurare a livello di SQL Server nell'area di destinazione. [Altre informazioni](../azure-sql/database/azure-defender-for-sql.md)
**Regole del firewall** | Non supportata. | Soluzione alternativa: configurare le regole del firewall per SQL Server nell'area di destinazione. Le regole del firewall a livello di database vengono copiate dal server di origine al server di destinazione. [Altre informazioni](../azure-sql/database/firewall-create-server-level-portal-quickstart.md)
**Criteri di controllo** | Non supportata. | I criteri vengono reimpostati su default dopo lo spostamento. [Informazioni](../azure-sql/database/auditing-overview.md) su come eseguire la reimpostazione.
**Conservazione dei backup** | Supportata. | I criteri di conservazione backup per il database di origine vengono trasferiti al database di destinazione. [Informazioni](../azure-sql/database/long-term-backup-retention-configure.md) su come modificare le impostazioni dopo lo spostamento.
**Ottimizzazione automatica** | Non supportata. | Soluzione alternativa: impostare le impostazioni di ottimizzazione automatica dopo lo spostamento. [Altre informazioni](../azure-sql/database/automatic-tuning-enable.md)
**Avvisi del database** | Non supportata. | Soluzione temporanea: impostare gli avvisi dopo lo spostamento. [Altre informazioni](../azure-sql/database/alerts-insights-configure-portal.md)
**stretch database SQL Server di Azure** | Non supportato | Non è possibile spostare SQL Server stretch database con il motore di risorse.
**Azure Synapse Analytics** | Non supportato | Non è possibile spostare Azure sinapsi Analytics con il motore di risorse.
## <a name="next-steps"></a>Passaggi successivi

Provare [le risorse SQL di Azure](tutorial-move-region-sql.md) in un'altra area con il motore di risorse.