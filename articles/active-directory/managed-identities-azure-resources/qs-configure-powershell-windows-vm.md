---
title: Configurare identità gestite in una VM di Azure con PowerShell - Azure AD
description: Istruzioni dettagliate per la configurazione di identità gestite per le risorse di Azure in una VM di Azure con PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/19/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b4209ce159c9d0bbee01dd422b98832f6bb5713
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969000"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice.

Questo articolo illustra come eseguire queste identità gestite per le operazioni delle risorse di Azure su una macchina virtuale di Azure usando PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#managed-identity-types)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire gli script di esempio, sono disponibili due opzioni:
    - Usare [Azure Cloud Shell](../../cloud-shell/overview.md), che è possibile aprire con il pulsante **Prova** nell'angolo in alto a destra dei blocchi di codice.
    - Eseguire gli script in locale installando l'ultima versione di [Azure PowerShell](/powershell/azure/install-az-ps), quindi accedere ad Azure usando `Connect-AzAccount`. 

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

Questa sezione descriverà come abilitare e disabilitare un'identità gestita assegnata dal sistema tramite Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Abilitare l'identità gestita assegnata dal sistema durante la creazione di una macchina virtuale di Azure

Per creare una macchina virtuale di Azure con l'identità gestita assegnata dal sistema abilitata, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Vedere una delle guide di avvio rapido sulle VM di Azure riportate di seguito, completando solo le sezioni necessarie ("Accedere ad Azure", "Creare un gruppo di risorse", "Crea un gruppo di rete", "Creare la VM").

    Quando si raggiunge la sezione "Creare la VM", apportare una leggera modifica alla sintassi del cmdlet [New-AzVMConfig](/powershell/module/az.compute/new-azvm). Assicurarsi di aggiungere un parametro `-IdentityType SystemAssigned` per effettuare il provisioning della macchina virtuale con l'identità assegnata dal sistema abilitata, ad esempio:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName myVM -IdentityType SystemAssigned ...
    ```

   - [Creare una macchina virtuale Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Creare una macchina virtuale Linux mediante PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Abilitare l'identità gestita assegnata dal sistema in una macchina virtuale di Azure esistente

Per abilitare l'identità gestita assegnata dal sistema in una macchina virtuale di cui originariamente è stato effettuato il provisioning senza tale identità, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Recuperare le proprietà della macchina virtuale usando il cmdlet `Get-AzVM`. Per abilitare un'identità gestita assegnata dal sistema, usare l'opzione `-IdentityType` nel cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm):

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType SystemAssigned
   ```

### <a name="add-vm-system-assigned-identity-to-a-group"></a>Aggiungere l'identità assegnata dal sistema della macchina virtuale a un gruppo

Dopo aver abilitato l'identità assegnata dal sistema in una macchina virtuale, è possibile aggiungerla a un gruppo.  La procedura seguente consente di aggiungere un'identità assegnata dal sistema della macchina virtuale a un gruppo.

1. Recuperare e prendere nota dell'`ObjectID` (come specificato nel campo `Id` dei valori restituiti) dell'entità servizio della macchina virtuale:

   ```azurepowershell-interactive
   Get-AzADServicePrincipal -displayname "myVM"
   ```

1. Recuperare e prendere nota dell'`ObjectID` (come specificato nel campo `Id` dei valori restituiti) del gruppo:

   ```azurepowershell-interactive
   Get-AzADGroup -searchstring "myGroup"
   ```

1. Aggiungere l'entità servizio della macchina virtuale al gruppo:

   ```azurepowershell-interactive
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Disabilitare un'identità gestita assegnata dal sistema in una macchina virtuale di Azure

Per disabilitare l'identità gestita assegnata dal sistema in una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

Se si ha una macchina virtuale che non ha più bisogno dell'identità gestita assegnata dal sistema ma ha ancora bisogno di identità gestite assegnate dagli utenti, usare il cmdlet seguente:

1. Recuperare le proprietà della macchina virtuale usando il cmdlet `Get-AzVM` e impostare il parametro `-IdentityType` su `UserAssigned`:

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Se si ha una macchina virtuale che non ha più bisogno dell'identità gestita assegnata dal sistema e non dispone di identità gestite assegnate dall'utente, usare i comandi seguenti:

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale tramite Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Assegnare a una macchina virtuale un'identità gestita assegnata dall'utente durante la creazione

Per assegnare un'identità assegnata dall'utente a una macchina virtuale, all'account devono essere assegnati i ruoli [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e [Operatore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Vedere una delle guide di avvio rapido sulle VM di Azure riportate di seguito, completando solo le sezioni necessarie ("Accedere ad Azure", "Creare un gruppo di risorse", "Crea un gruppo di rete", "Creare la VM").

    Quando si raggiunge la sezione "Creare la VM", apportare una leggera modifica alla sintassi del cmdlet [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm). Aggiungere i parametri `-IdentityType UserAssigned` e `-IdentityID` per eseguire il provisioning della macchina virtuale con un'identità assegnata dall'utente.  Sostituire `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati.  Ad esempio:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [Creare una macchina virtuale Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Creare una macchina virtuale Linux mediante PowerShell](../../virtual-machines/linux/quick-create-powershell.md)


### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Assegnare un'identità gestita assegnata dall'utente a una macchina virtuale di Azure esistente

Per assegnare un'identità assegnata dall'utente a una macchina virtuale, all'account devono essere assegnati i ruoli [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e [Operatore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Creare un'identità gestita assegnata dall'utente tramite il cmdlet [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity).  Si noti `Id` nell'output perché sarà necessaria nel passaggio successivo.

   > [!IMPORTANT]
   > La creazione di identità gestite assegnate dall'utente supporta solo caratteri alfanumerici, il carattere di sottolineatura e il trattino (da 0 a 9, da "a" a "z", da "A" a "Z", \_ e -). Affinché l'assegnazione a VM/set di scalabilità di macchine virtuali funzioni correttamente, inoltre, il nome può contenere solo da 3 a 128 caratteri. Per altre informazioni, vedere [Domande frequenti e problemi noti](known-issues.md)

   ```azurepowershell-interactive
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
1. Recuperare le proprietà della macchina virtuale usando il cmdlet `Get-AzVM`. Quindi per assegnare un'identità gestita assegnata dall'utente alla macchina virtuale di Azure, usare le opzioni `-IdentityType` e `-IdentityID` nel cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm).  Il valore per il parametro`-IdentityId` è il valore `Id` annotato nel passaggio precedente.  Sostituire `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati.

   > [!WARNING]
   > Per conservare le identità gestite precedentemente assegnate dall'utente alla macchina virtuale, eseguire una query sulla proprietà `Identity` dell'oggetto macchina virtuale (ad esempio, `$vm.Identity`).  Se vengono restituite delle identità gestite assegnate dall'utente, includerle nel comando seguente insieme alla nuova identità gestita assegnata dall'utente che si vuole assegnare alla macchina virtuale.

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale di Azure

Per rimuovere un'identità assegnata dall'utente da una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

Se la VM ha più identità gestite assegnate dall'utente è possibile rimuoverle tutte tranne l'ultima mediante i comandi seguenti. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. `<USER ASSIGNED IDENTITY NAME>` è la proprietà del nome dell'identità gestita assegnata dall'utente, che deve rimanere nella VM. È possibile trovare queste informazioni eseguendo una query sulla proprietà `Identity` dell'oggetto macchina virtuale.  Ad esempio, `$vm.Identity`:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Se la macchina virtuale non ha un'identità gestita assegnata dal sistema e si vogliono rimuovere tutte le identità gestite assegnate dall'utente al suo interno, usare il comando seguente:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Se la macchina virtuale ha identità gestite sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità gestite assegnate dall'utente iniziando a usare solo identità gestite assegnate dal sistema.

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md)
- Per la guida introduttiva completa sulla creazione di VM di Azure, vedere:

  - [Creare una macchina virtuale Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
  - [Creare una macchina virtuale Linux con PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
