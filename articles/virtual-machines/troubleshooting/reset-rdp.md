---
title: Reimpostare Servizi Desktop remoto o la relativa password di amministratore in una macchina virtuale Windows | Microsoft Docs
description: Informazioni su come reimpostare la password di un account o Servizi Desktop remoto in una macchina virtuale Windows tramite il portale di Azure o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 720d25079e1350315c9f403a8215f650db49ceb7
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743084"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Reimpostare Servizi Desktop remoto o la relativa password di amministratore in una macchina virtuale Windows
Se non è possibile connettersi a una macchina virtuale Windows, è possibile reimpostare la password di amministratore locale o la configurazione di Servizi Desktop remoto (opzione non supportata nei controller di dominio Windows). Per reimpostare la password, usare il portale di Azure o l'estensione di accesso alla macchina virtuale in Azure PowerShell. Dopo aver effettuato l'accesso alla macchina virtuale, reimpostare la password per l'amministratore locale.  
Se si usa PowerShell, verificare che il [modulo di PowerShell più recente sia installato e configurato](/powershell/azure/) e di avere eseguito l'accesso alla sottoscrizione di Azure. È anche possibile [eseguire questi passaggi per le macchine virtuali create con il modello di distribuzione classica](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp).

È possibile reimpostare Servizi Desktop remoto e le credenziali nei modi seguenti:

- [Eseguire la reimpostazione usando il portale di Azure](#reset-by-using-the-azure-portal)

- [Eseguire la reimpostazione usando l'estensione VMAccess e PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Eseguire la reimpostazione usando il portale di Azure

Innanzitutto, accedere al [portale di Azure](https://portal.azure.com) e quindi selezionare **Macchine virtuali** nel menu a sinistra. 

### <a name="reset-the-local-administrator-account-password"></a>**Reimpostare una password dell'account amministratore locale**

1. Selezionare la macchina virtuale Windows e quindi selezionare **Reimposta password** in **Supporto e risoluzione dei problemi**. Verrà visualizzata la finestra **Reimposta password**.

2. Selezionare **Reimposta password**, immettere un nome utente e una password e quindi selezionare **Aggiorna**. 

3. Provare a connettersi di nuovo alla macchina virtuale.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Reimpostare la configurazione di Servizi Desktop remoto**

Questo processo Abilita il servizio Desktop remoto nella macchina virtuale e crea una regola del firewall per la porta RDP predefinita 3389.

1. Selezionare la macchina virtuale Windows e quindi selezionare **Reimposta password** in **Supporto e risoluzione dei problemi**. Verrà visualizzata la finestra **Reimposta password**. 

2. Selezionare **Reimposta solo configurazione** e quindi **Aggiorna**. 

3. Provare a connettersi di nuovo alla macchina virtuale.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Eseguire la reimpostazione usando l'estensione VMAccess e PowerShell

Innanzitutto, verificare che il [modulo di PowerShell più recente sia installato e configurato](/powershell/azure/) e di aver eseguito l'accesso alla sottoscrizione di Azure mediante il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

### <a name="reset-the-local-administrator-account-password"></a>**Reimpostare una password dell'account amministratore locale**

- Ripristinare la password o il nome utente di amministratore con il cmdlet di PowerShell [Set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension). L'impostazione di `typeHandlerVersion` deve essere 2.0 o versione successiva, perché la versione 1 è deprecata. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Se si immette un nome diverso rispetto all'account di amministratore locale corrente nella macchina virtuale, l'estensione VMAccess aggiungerà un account amministratore locale con tale nome e assegnerà la password specificata a tale account. Se l'account amministratore locale è presente nella macchina virtuale, l'estensione VMAccess reimposterà la password. Se l'account è disabilitato, l'estensione VMAccess lo abiliterà.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Reimpostare la configurazione di Servizi Desktop remoto**

1. Reimpostare l'accesso remoto alla macchina virtuale con il cmdlet di PowerShell [Set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension). Nell'esempio seguente viene ripristinata l'estensione di accesso denominata `myVMAccess` nella macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > In qualsiasi momento una VM può avere un solo agente di accesso. Per impostare le proprietà dell'agente di accesso alla macchina virtuale, usare l'opzione `-ForceRerun`. Quando si usa `-ForceRerun`, assicurarsi di usare lo stesso nome per l'agente di accesso alla macchina virtuale usato nei comandi precedenti.

1. Se non è ancora possibile connettersi in remoto alla macchina virtuale, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](troubleshoot-rdp-connection.md). Se la connessione al controller di dominio Windows viene interrotta, sarà necessario ripristinarla da un backup del controller di dominio.

## <a name="next-steps"></a>Passaggi successivi


- Se l'installazione dell'estensione di accesso alle VM di Azure non riesce, è possibile [risolvere i problemi di estensione della macchina virtuale](../extensions/troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

- Se non si è in grado di reimpostare la password usando l'estensione di accesso alla macchina virtuale, è possibile [reimpostare la password di Windows locale offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Questo metodo è un processo più avanzato e richiede di connettere il disco rigido virtuale della macchina virtuale problematica a un'altra macchina virtuale. Seguire prima i passaggi illustrati in questo articolo e provare il metodo di reimpostazione della password offline solo se questi passaggi non funzionano.

- Informazioni [sulle estensioni e sulle funzionalità delle macchine virtuali di Azure](../extensions/features-windows.md).

- [Connettersi a una macchina virtuale di Azure con RDP o SSH](/previous-versions/azure/dn535788(v=azure.100)).


- [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
