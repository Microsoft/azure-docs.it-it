---
title: Informazioni di riferimento sui cmdlet di PowerShell
description: Informazioni sui cmdlet di PowerShell per Utilità di pianificazione di Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: ccc9f709348d961e49bced00946658a6997837c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368112"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Informazioni di riferimento sui cmdlet PowerShell per Utilità di pianificazione di Azure

> [!IMPORTANT]
> [App per la logica di Azure](../logic-apps/logic-apps-overview.md) sostituirà Utilità di pianificazione di Azure di cui è [in corso il ritiro](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Per continuare a usare i processi configurati nell'utilità di pianificazione, [eseguire la migrazione alle app per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) il prima possibile. 
>
> Utilità di pianificazione non è più disponibile nel portale di Azure, ma l'[API REST](/rest/api/scheduler) e i [cmdlet di PowerShell per Utilità di pianificazione di Azure](scheduler-powershell-reference.md) rimangono attualmente disponibili, quindi è possibile gestire processi e raccolte di processi.

Per generare script per la creazione e la gestione dei processi dell'Utilità di pianificazione e delle raccolte dei processi, è possibile usare i cmdlet di PowerShell. Questo articolo elenca i principali cmdlet di PowerShell  per Utilità di pianificazione Azure con collegamenti ai relativi articoli di riferimento. Per installare Azure PowerShell per l'abbonamento di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/). Per altre informazioni sui [cmdlet di Azure Resource Manager](/powershell/azure/) vedere [Uso di Azure PowerShell con Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Cmdlet | Descrizione |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Disabilita una raccolta di processi. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Abilita una raccolta di processi. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Ottiene processi dell'Utilità di pianificazione. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Ottiene raccolte di processi. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Ottiene la cronologia processi. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Crea un processo HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Crea una raccolta di processi. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Crea un processo di coda del bus di servizio. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Crea un processo di argomento del bus di servizio. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Crea un processo di coda di archiviazione. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Elimina un processo dell'Utilità di pianificazione. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Rimuove una raccolta di processi. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modifica un processo HTTP dell'Utilità di pianificazione. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modifica una raccolta di processi. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modifica un processo di coda del bus di servizio. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modifica un processo di argomento del bus di servizio. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modifica un processo di coda di archiviazione. |
||| 

Per altre informazioni, è possibile eseguire uno di questi cmdlet: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Passaggi successivi

* [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)
* [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)
* [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](/rest/api/scheduler)