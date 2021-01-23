---
title: 'Esercitazione: Disponibilità elevata per le VM Windows in Azure'
description: In questa esercitazione si apprenderà come usare Azure PowerShell per distribuire macchine virtuali a disponibilità elevata nei set di disponibilità
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e1c9cf0a60446fba6fae5c850231b0805e7ea135
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736653"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Esercitazione: Creare e distribuire macchine virtuali a disponibilità elevata con Azure PowerShell

Questa esercitazione illustra come aumentare la disponibilità e l'affidabilità delle macchine virtuali (VM) usando set di disponibilità. I set di disponibilità garantiscono che le VM vengano distribuite in Azure tra più nodi hardware isolati all'interno di un cluster. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un set di disponibilità
> * Creare una macchina virtuale in un set di disponibilità
> * Controllare le dimensioni delle macchine virtuali disponibili
> * Selezionare Azure Advisor


## <a name="availability-set-overview"></a>Informazioni generali sui set di disponibilità

Un set di disponibilità è una funzionalità di raggruppamento logico che consente di isolare le risorse VM tra loro quando vengono distribuite. Azure garantisce che le VM inserite all'interno di un set di disponibilità vengano eseguite tra più server fisici, rack di calcolo, unità di archiviazione e commutatori di rete. Eventuali errori hardware o software interessano solo un subset delle VM e nel complesso la soluzione rimane operativa. I set di disponibilità sono essenziali per creare soluzioni cloud affidabili.

Si consideri una soluzione tipica basata su macchine virtuali, in cui si hanno quattro server Web front-end e due macchine virtuali back-end. Con Azure può essere opportuno definire due set di disponibilità prima di distribuire le VM: uno per il livello Web e un altro per il livello back-end. Quando si crea una nuova VM, si specifica il set di disponibilità come parametro. Azure garantisce che le VM siano isolate in più risorse hardware fisiche. Se l'hardware fisico su cui è in esecuzione uno dei server ha un problema, le altre istanze dei server rimangono in esecuzione perché si trovano in risorse hardware diverse.

È consigliabile usare i set di disponibilità quando si vogliono distribuire soluzioni affidabili basate su macchine virtuali in Azure.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="create-an-availability-set"></a>Creare un set di disponibilità

L'hardware in un percorso è suddiviso in più domini di aggiornamento e domini di errore. I **domini di aggiornamento** sono gruppi di macchine virtuali con relativo hardware fisico sottostante che è possibile riavviare nello stesso momento. Le macchine virtuali nello stesso **dominio di errore** condividono risorse di archiviazione comuni, nonché un alimentatore e un commutatore di rete comune.  

È possibile creare un set di disponibilità con [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). In questo esempio, il numero sia dei domini di aggiornamento che dei domini di errore è *2* e il set di disponibilità viene denominato *myAvailabilitySet*.

Creare un gruppo di risorse.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Creare un set di disponibilità gestito usando [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) con il parametro `-sku aligned`.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Creare macchine virtuali in un set di disponibilità
Per garantirne la corretta distribuzione nell'hardware, le VM devono essere create all'interno del set di disponibilità. Non è possibile aggiungere una VM esistente a un set di disponibilità dopo che è stata creata. 


Quando si crea una VM con [New-AzVM](/powershell/module/az.compute/new-azvm), si usa il parametro `-AvailabilitySetName` per specificare il nome del set di disponibilità.

Impostare prima di tutto nome utente e password dell'amministratore della macchina virtuale con il comando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Creare ora due VM con [New-AzVM](/powershell/module/az.compute/new-azvm) nel set di disponibilità.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

La creazione e la configurazione di entrambe le macchine virtuali richiedono alcuni minuti. Al termine, sono disponibili due macchine virtuali distribuite nell'hardware sottostante. 

Se si esamina il set di disponibilità nel portale passando a **Gruppi di risorse** > **myResourceGroupAvailability** > **myAvailabilitySet**, è possibile osservare la distribuzione delle VM tra i due domini di errore e di aggiornamento.

![Set di disponibilità nel portale](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Controllare le dimensioni delle macchine virtuali disponibili 

Quando si crea una VM all'interno di un set di disponibilità, è necessario conoscere le dimensioni di VM disponibili nell'hardware. Usare il comando [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) per ottenere tutte le dimensioni disponibili per le macchine virtuali che si possono distribuire nel set di disponibilità.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Selezionare Azure Advisor 

È anche possibile usare Azure Advisor per ottenere altre informazioni su come migliorare la disponibilità delle VM. Azure Advisor analizza i dati di telemetria relativi all'utilizzo e alla configurazione e quindi consiglia soluzioni che consentono di migliorare l'efficacia in termini di costi, le prestazioni, la disponibilità e la sicurezza delle risorse di Azure.

Accedere al [portale di Azure](https://portal.azure.com), selezionare **Tutti i servizi** e digitare **Advisor**. Nel dashboard di Advisor vengono visualizzate raccomandazioni personalizzate per la sottoscrizione selezionata. Per altre informazioni vedere [Presentazione di Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un set di disponibilità
> * Creare una macchina virtuale in un set di disponibilità
> * Controllare le dimensioni delle macchine virtuali disponibili
> * Selezionare Azure Advisor

Passare all'esercitazione successiva per informazioni sui set di scalabilità di macchine virtuali.

> [!div class="nextstepaction"]
> [Creare un set di scalabilità di macchine virtuali](tutorial-create-vmss.md)
