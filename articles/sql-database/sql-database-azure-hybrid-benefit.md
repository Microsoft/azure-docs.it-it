---
title: Vantaggio Azure Hybrid
description: Usare licenze di SQL Server esistenti per gli sconti del database SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945611"
---
# <a name="azure-hybrid-benefit"></a>Vantaggio Azure Hybrid

Nel livello di calcolo di cui è stato effettuato il provisioning del modello di acquisto basato su vCore, è possibile scambiare le licenze esistenti con tariffe scontate nel database SQL usando [vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Questo vantaggio di Azure ti permette di risparmiare fino al 30% o addirittura superiore nel database SQL di Azure usando le licenze SQL Server locali con Software Assurance. Usare il Vantaggio Azure Hybrid Calculator usando il collegamento indicato in precedenza per i valori corretti. 

> [!NOTE]
> Il passaggio a Vantaggio Azure Hybrid non richiede alcun tempo di inattività.

![prezzi](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Scegliere un modello di licenza

Con Vantaggio Azure Hybrid, è possibile scegliere di pagare solo per l'infrastruttura di Azure sottostante usando la licenza di SQL Server esistente per il motore di database SQL stesso (prezzi di calcolo di base) oppure è possibile pagare sia per l'infrastruttura sottostante sia per la licenza di SQL Server (prezzi inclusi in licenza).

È possibile scegliere o modificare il modello di licenza usando il portale di Azure o usando una delle API seguenti:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per impostare o aggiornare il tipo di licenza usando PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per impostare o aggiornare il tipo di licenza usando l'interfaccia della riga di comando di Azure:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Per impostare o aggiornare il tipo di licenza usando l'API REST:

- [Database-creazione o aggiornamento](/rest/api/sql/databases/createorupdate)
- [Databases - Update](/rest/api/sql/databases/update)
- [Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Passaggi successivi

- Per la scelta tra le opzioni di distribuzione del database SQL, vedere [scegliere l'opzione di distribuzione corretta in SQL di Azure](sql-database-paas-vs-sql-server-iaas.md).
- Per un confronto delle funzionalità del database SQL, vedere [funzionalità del database SQL di Azure](sql-database-features.md).
