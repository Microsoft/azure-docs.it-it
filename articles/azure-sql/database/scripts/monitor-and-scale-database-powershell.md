---
title: Usare PowerShell per monitorare e dimensionare un database singolo nel database SQL di Azure
description: Usare uno script di esempio di Azure PowerShell per monitorare e dimensionare un database singolo nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 04c19ca8fbdaed85225b5af128c72d393e5350e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573265"
---
# <a name="use-powershell-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Usare PowerShell per monitorare e dimensionare un database singolo nel database SQL di Azure

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Questo esempio di script di PowerShell consente di monitorare le metriche delle prestazioni di un database singolo, aumentare le dimensioni di calcolo del database stesso e creare una regola di avviso per una delle metriche delle prestazioni.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario Az PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-script"></a>Script di esempio

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=15-16 "Monitor and scale single database")]

> [!NOTE]
> Per un elenco completo delle metriche, vedere [Metriche supportate](../../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases).
> [!TIP]
> Usare [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) per ottenere lo stato delle operazioni di database e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity) per annullare un'operazione di aggiornamento di database.

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server che ospita un database singolo o un pool elastico. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Mostra le informazioni sull'utilizzo delle dimensioni per il database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Aggiorna le proprietà del database o sposta un database all'interno, all'esterno o tra pool elastici. |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Imposta una regola di avviso per monitorare automaticamente le metriche in futuro. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Per altri esempi di script di PowerShell, vedere [Script di Azure PowerShell](../powershell-script-content-guide.md).
