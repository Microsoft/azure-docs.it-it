---
title: Applicazione automatica delle patch per macchine virtuali SQL Server (distribuzione classica) | Microsoft Docs
description: Informazioni sulla funzionalità di applicazione automatica delle patch per macchine virtuali SQL Server in esecuzione in Azure con il modello di distribuzione classica.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 555bd26ac894a3e06aa46eb0c51e20a35d21a9e7
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078072"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Applicazione automatica delle patch per SQL Server in macchine virtuali di Azure (distribuzione classica)
> [!div class="op_single_selector"]
> * [Gestione risorse](../../../azure-sql/virtual-machines/windows/automated-patching.md)
> * [Classico](../classic/sql-automated-patching.md)
> 
> 

L'applicazione automatica delle patch stabilisce un periodo di manutenzione per una macchina virtuale di Azure su cui è in esecuzione SQL Server. Gli aggiornamenti automatici possono essere installati solo durante questo periodo di manutenzione. Per SQL Server, gli aggiornamenti di sistema e i riavvii associati vengono eseguiti nel momento migliore per il database. 

> [!IMPORTANT]
> Vengono installati solo gli aggiornamenti di Windows contrassegnati con la dicitura **Importante**. Altri aggiornamenti di SQL Server, ad esempio gli aggiornamenti cumulativi, devono essere installati manualmente. 

L'applicazione automatica delle patch dipende dall' [estensione dell'agente IaaS di SQL Server](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure offre due modelli di distribuzione diversi per creare e usare le risorse: [Resource Manager e distribuzione classica](../../../azure-resource-manager/management/deployment-models.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per visualizzare la versione Resource Manager di questo articolo, vedere [Applicazione automatica delle patch per SQL Server in macchine virtuali di Azure (Resource Manager)](../../../azure-sql/virtual-machines/windows/automated-patching.md).

## <a name="prerequisites"></a>Prerequisiti
Per usare l'applicazione automatica delle patch, tenere in considerazione i seguenti prerequisiti:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versione di SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Installare i comandi di Azure PowerShell più recenti](/powershell/azure/overview).

**Estensione di SQL Server IaaS**:

* [Installare l'estensione di SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Impostazioni
Nella seguente tabella sono descritte le opzioni che possono essere configurate per l'applicazione automatica delle patch. Nella macchine virtuali classiche, per configurare queste impostazioni è necessario usare PowerShell.

| Impostazione | Valori possibili | Descrizione |
| --- | --- | --- |
| **Applicazione automatica delle patch** |Enable/Disable (disabilitato) |Abilita o disabilita l'applicazione automatica delle patch per una macchina virtuale di Azure. |
| **Pianificazione della manutenzione** |Ogni giorno, lunedì, martedì, mercoledì, giovedì, venerdì, sabato, domenica |Pianificazione per il download e l'installazione degli aggiornamenti di Windows, SQL Server e Microsoft per la macchina virtuale. |
| **Ora di inizio manutenzione** |0-24 |Ora di inizio locale per aggiornare la macchina virtuale. |
| **Durata dell'intervallo di manutenzione** |30-180 |Numero di minuti consentito per completare il download e l'installazione degli aggiornamenti. |
| **Categoria delle patch** |Importante |Categoria degli aggiornamenti da scaricare e installare. |

## <a name="configuration-with-powershell"></a>Configurazione con PowerShell
Nell'esempio seguente, PowerShell viene utilizzato per configurare l'applicazione automatizzata di patch in una macchina virtuale di SQL Server esistente. Il comando **New-AzureVMSqlServerAutoPatchingConfig** configura un nuovo periodo di manutenzione per gli aggiornamenti automatici.

```azurepowershell
$aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM
```

In base a questo esempio, nella tabella seguente vengono descritti gli effetti pratici sulla macchina virtuale di Azure di destinazione:

| Parametro | Effetto |
| --- | --- |
| **DayOfWeek** |Patch installate ogni giovedì. |
| **MaintenanceWindowStartingHour** |Inizio degli aggiornamenti alle ore 11:00. |
| **MaintenanceWindowDuration** |Le patch devono essere installate entro 120 minuti. In base all'ora di inizio, devono essere completate entro le ore 13:00. |
| **PatchCategory** |L'unica impostazione possibile per questo parametro è "Important". |

Potrebbero essere necessari diversi minuti per installare e configurare l'agente IaaS di SQL Server.

Per disabilitare l’applicazione automatizzata di patch, eseguire lo stesso script senza il parametro -Enable per New-AzureVMSqlServerAutoPatchingConfig. Come per l'installazione, la disabilitazione dell’applicazione automatizzata di patch può richiedere alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre attività di automazione disponibili, vedere [Estensione Agente IaaS di SQL Server](../classic/sql-server-agent-extension.md).

Per altre informazioni sull'esecuzione di SQL Server nelle VM di Azure, vedere [Panoramica di SQL Server nelle macchine virtuali di Azure](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

