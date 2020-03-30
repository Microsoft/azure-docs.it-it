---
title: Automatizzare le attività di gestione in macchine virtuali SQL (distribuzione classica) | Microsoft Docs
description: Questo argomento descrive come gestire l'estensione di SQL Server Agent, che consente di automatizzare attività di amministrazione di SQL Server specifiche. Queste includono il backup automatizzato, l'applicazione automatica delle patch e l'integrazione dell'insieme di credenziali delle chiavi di Azure. In questo argomento viene usata la modalità di distribuzione classica.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b76ade40db1e85abc0fb42af2e6f4ab88cb092c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982273"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizzare le attività di gestione in macchine virtuali SQL con l'estensione SQL Server Agent (distribuzione classica)
> [!div class="op_single_selector"]
> * [Gestione risorse](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Classico](../classic/sql-server-agent-extension.md)
> 
>
 
L'Estensione Agente IaaS di SQL Server (SQLIaaSAgent) viene eseguita sulle macchine virtuali di Azure per automatizzare le attività di amministrazione. Questo argomento fornisce una panoramica dei servizi supportati dall'estensione e delle istruzioni per l'installazione, lo stato e la rimozione.

> [!IMPORTANT] 
> Azure include due diversi modelli di distribuzione per la creazione e l'utilizzo delle risorse: [Resource Manager e Classic](../../../azure-resource-manager/management/deployment-models.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.  Per visualizzare la versione di Resource Manager di questo articolo, vedere [SQL Server Agent Extension for SQL Server VMs Gestione risorse](../sql/virtual-machines-windows-sql-server-agent-extension.md)(Estensione Agente IaaS per le VM SQL Resource Manager).

## <a name="supported-services"></a>Servizi supportati
L'Estensione Agente IaaS di SQL Server supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | Descrizione |
| --- | --- |
| **Backup automatico di SQL** |Consente di automatizzare la pianificazione delle operazioni di backup per tutti i database correlati all'istanza predefinita di SQL Server nella VM. Per altre informazioni, vedere [Backup automatico per SQL Server in macchine virtuali di Azure (distribuzione classica)](../classic/sql-automated-backup.md). |
| **Applicazione automatica delle patch di SQL** |Consente di configurare una finestra di manutenzione durante la quale è possibile eseguire aggiornamenti importanti di Windows nella macchina virtuale, evitandone l'esecuzione nei periodi di picco del carico di lavoro. Per altre informazioni, vedere [Applicazione automatica delle patch per SQL Server in macchine virtuali di Azure (distribuzione classica)](../classic/sql-automated-patching.md). |
| **Integrazione dell'insieme di credenziali delle chiavi di AzureAzure Key Vault** |Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere [Configurare l'integrazione di Azure Key Vault per SQL Server in macchine virtuali di Azure (distribuzione classica)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Prerequisiti
Requisiti per l'uso dell'Estensione Agente IaaS di SQL Server nella VM:

### <a name="operating-system"></a>Sistema operativo:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Versioni di SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Scaricare e configurare i comandi di Azure PowerShell più recenti](/powershell/azure/overview).

Avviare Windows PowerShell e connetterlo alla sottoscrizione di Azure mediante il comando **Add-AzureAccount** .

    Add-AzureAccount

Se si dispone di più sottoscrizioni, usare **Select-AzureSubscription** per selezionare la sottoscrizione contenente la VM di destinazione classica.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

A questo punto, usando il comando **Get-AzureVM** è possibile ottenere un elenco delle macchine virtuali classiche e dei nomi di servizio associati.

    Get-AzureVM

## <a name="installation"></a>Installazione
Per le VM classiche è necessario usare PowerShell per installare l'estensione di SQL Server IaaS Agent e configurarne i servizi associati. Usare il cmdlet di PowerShell **Set-AzureVMSqlServerExtension** per installare l'estensione. Ad esempio, il comando seguente installa l'estensione in una VM di Windows Server (distribuzione classica) denominandola "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Se si esegue l'aggiornamento alla versione più recente dell'estensione dell'agente IaaS di SQL, è necessario riavviare la macchina virtuale dopo l'aggiornamento dell'estensione.

> [!NOTE]
> Le macchine virtuali classiche non dispongono di un'opzione per installare e configurare l'estensione di SQL Server IaaS Agent tramite il portale.

> [!NOTE]
> L'estensione SQL Server IaaS Agent è supportata solo nelle [immagini della raccolta di macchine virtuali di SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (con pagamento in base al consumo o Bring Your Own License). Non è supportata se si installa manualmente SQL Server in una macchina virtuale Windows Server con il solo sistema operativo o se si distribuisce il disco rigido virtuale di una macchina virtuale SQL Server personalizzata. In questi casi dovrebbe essere possibile installare e gestire manualmente l'estensione usando PowerShell, ma è consigliabile installare invece un'immagine della raccolta di macchine virtuali di SQL Server e quindi personalizzarla.

## <a name="status"></a>Stato
Un modo per verificare che l'estensione sia installata consiste nel visualizzare lo stato dell'agente nel portale di Azure. Selezionare una macchina virtuale elencata nel pannello della macchina virtuale e quindi fare clic su **Estensioni**. Verrà elencata l'estensione **SQLIaaSAgent** .

![Estensione Agente IaaS di SQL Server nel portale di Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

È anche possibile usare il cmdlet di Azure Powershell **Get-AzureVMSqlServerExtension.You** can also use the Get-AzureVMSqlServerExtension Azure Powershell cmdlet.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Rimozione
Nel portale di Azure è possibile disinstallare l'estensione facendo clic sui puntini di sospensione nel pannello **Estensioni** delle proprietà della macchina virtuale. Fare clic su **Disinstalla**.

![Disinstallare l'Estensione Agente IaaS di SQL Server nel portale di Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

È anche possibile usare il cmdlet di PowerShell **Remove-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Passaggi successivi
Iniziare a usare uno dei servizi supportati dall'estensione. Per altre informazioni, vedere gli argomenti citati nella sezione [Servizi supportati](#supported-services) di questo articolo.

Per altre informazioni sull'esecuzione di SQL Server in Macchine virtuali di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

