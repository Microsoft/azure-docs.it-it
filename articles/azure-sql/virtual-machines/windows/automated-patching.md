---
title: Applicazione automatica delle patch per VM SQL Server (Resource Manager) | Documentazione Microsoft
description: Questo articolo illustra la funzionalità di applicazione automatica delle patch per le macchine virtuali di SQL Server in esecuzione in Azure che usano Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3a255b87724bb0c2f86743a5efc3613aba765c78
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219637"
---
# <a name="automated-patching-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Gestione risorse](automated-patching.md)
> * [Classico](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

L'applicazione automatica delle patch stabilisce una finestra di manutenzione per una macchina virtuale di Azure che esegue SQL Server. Gli aggiornamenti automatici possono essere installati solo durante questo periodo di manutenzione. Per SQL Server, questa restrizione verifica che gli aggiornamenti di sistema e i riavvii associati vengano eseguiti nel momento migliore per il database. 

> [!IMPORTANT]
> Vengono installati solo gli aggiornamenti di Windows e SQL Server contrassegnati come **Importante** o **Critico**. Gli altri aggiornamenti di SQL Server, ad esempio i Service Pack e gli aggiornamenti cumulativi che non sono contrassegnati come **Importante** o **Critico**, devono essere installati manualmente. 

L'applicazione automatica delle patch dipende dall'[estensione SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md).

## <a name="prerequisites"></a>Prerequisiti
Per usare l'applicazione automatica delle patch, tenere in considerazione i seguenti prerequisiti:

**Sistema operativo**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versione di SQL Server**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Installare i comandi di Azure PowerShell più recenti](/powershell/azure/overview) se si prevede di configurare l'applicazione automatica delle patch con PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> L'applicazione automatica delle patch si basa sull'estensione dell'agente IaaS di SQL Server. Per impostazione predefinita, le attuali immagini della raccolta di macchine virtuali di SQL aggiungono questa estensione. Per altre informazioni, vedere [Estensione Agente IaaS di SQL Server](sql-server-iaas-agent-extension-automate-management.md).
> 
> 

## <a name="settings"></a>Impostazioni
Nella seguente tabella sono descritte le opzioni che possono essere configurate per l'applicazione automatica delle patch. I passaggi di configurazione effettivi variano a seconda che venga usato il portale di Azure o i comandi di Windows PowerShell di Azure.

| Impostazione | Valori possibili | Descrizione |
| --- | --- | --- |
| **Applicazione automatica delle patch** |Enable/Disable (disabilitato) |Abilita o disabilita l'applicazione automatica delle patch per una macchina virtuale di Azure. |
| **Pianificazione della manutenzione** |Ogni giorno, lunedì, martedì, mercoledì, giovedì, venerdì, sabato, domenica |Pianificazione per il download e l'installazione degli aggiornamenti di Windows, SQL Server e Microsoft per la macchina virtuale. |
| **Ora di inizio manutenzione** |0-24 |Ora di inizio locale per aggiornare la macchina virtuale. |
| **Durata dell'intervallo di manutenzione** |30-180 |Numero di minuti consentito per completare il download e l'installazione degli aggiornamenti. |
| **Categoria delle patch** |Importante | Categoria degli aggiornamenti di Windows da scaricare e installare.|

## <a name="configure-in-the-azure-portal"></a>Configurare nel portale di Azure
È possibile usare il portale di Azure per configurare l'applicazione automatica delle patch durante il provisioning o per le VM esistenti.

### <a name="new-vms"></a>Nuove VM
Usare il portale di Azure per configurare l'applicazione automatica delle patch quando si crea una nuova macchina virtuale di SQL Server nel modello di distribuzione di Resource Manager.

Nella scheda **Impostazioni di SQL Server** selezionare **Cambia la configurazione** in **Applicazione automatica delle patch**. Nella seguente schermata del Portale di Azure viene mostrato il pannello **Applicazione automatica delle patch di SQL** .

![Applicazione automatica delle patch di SQL nel portale di Azure](./media/automated-patching/azure-sql-arm-patching.png)

Per altre informazioni, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](create-sql-vm-portal.md).

### <a name="existing-vms"></a>VM esistenti

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Per le macchine virtuali di SQL Server esistenti, aprire la [risorsa Macchine virtuali SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) e selezionare **Applicazione di patch** in **Impostazioni**. 

![Applicazione automatizzata di patch SQL per le VM esistenti](./media/automated-patching/azure-sql-rm-patching-existing-vms.png)


Al termine, fare clic sul pulsante **OK** nella parte inferiore del pannello **Configurazione di SQL Server** per salvare le modifiche.

Se si intende abilitare l'applicazione automatica delle patch per la prima volta, Azure configura l'agente IaaS di SQL Server in background. Durante questo periodo, nel portale di Azure potrebbe non essere visualizzata l'informazione relativa alla configurazione dell'applicazione automatica delle patch. Attendere alcuni minuti per l'installazione e la configurazione dell'agente. A questo punto, nel portale di Azure vengono visualizzate le nuove impostazioni.

## <a name="configure-with-powershell"></a>Configurare con PowerShell
Dopo il provisioning della VM di SQL, usare PowerShell per configurare l'applicazione automatica delle patch.

Nell'esempio seguente, PowerShell viene utilizzato per configurare l'applicazione automatizzata di patch in una macchina virtuale di SQL Server esistente. Il comando **New-AzVMSqlServerAutoPatchingConfig** configura una nuova finestra di manutenzione per gli aggiornamenti automatici.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Se l'estensione non è stata ancora installata, l'installazione riavvierà SQL Server.

In base a questo esempio, nella tabella seguente vengono descritti gli effetti pratici sulla macchina virtuale di Azure di destinazione:

| Parametro | Effetto |
| --- | --- |
| **DayOfWeek** |Patch installate ogni giovedì. |
| **MaintenanceWindowStartingHour** |Inizio degli aggiornamenti alle ore 11:00. |
| **MaintenanceWindowsDuration** |Le patch devono essere installate entro 120 minuti. In base all'ora di inizio, devono essere completate entro le ore 13:00. |
| **PatchCategory** |L'unica impostazione possibile per questo parametro è **Important**. In questo modo vengono installati gli aggiornamenti di Windows contrassegnati con la dicitura Importante, ma non gli aggiornamenti di SQL Server che non sono inclusi in questa categoria. |

Potrebbero essere necessari diversi minuti per installare e configurare l'agente IaaS di SQL Server.

Per disabilitare l’applicazione automatica delle patch, eseguire lo stesso script senza il parametro **-Enable** per **New-AzVMSqlServerAutoPatchingConfig**. L'assenza del parametro **-Enable** segnala il comando per disabilitare la funzionalità.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre attività di automazione disponibili, vedere [Estensione Agente IaaS di SQL Server](sql-server-iaas-agent-extension-automate-management.md).

Per altre informazioni sull'esecuzione di SQL Server nelle VM di Azure, vedere [Panoramica di SQL Server nelle macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

