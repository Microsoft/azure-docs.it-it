---
title: 'Creare una rete virtuale: guida introduttiva - Azure PowerShell'
titlesuffix: Azure Virtual Network
description: Questa guida introduttiva illustra come creare una rete virtuale usando il portale di Azure. Una rete virtuale consente alle risorse di Azure di comunicare tra loro e con Internet.
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b27f050d3d37daab05e8c5125d6b75a6bb4dea50
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199034"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Avvio rapido: Creare una rete virtuale usando PowerShell

Una rete virtuale consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente tra loro e con Internet. 

Questa guida introduttiva illustra come creare una rete virtuale. Dopo avere creato una rete virtuale, si distribuiscono due VM nella rete virtuale. Si effettua quindi la connessione alle VM da Internet e si stabilisce una comunicazione privata tramite la rete virtuale.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installazione di Azure PowerShell in locale o Azure Cloud Shell.

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Creare un gruppo di risorse e una rete virtuale

Esiste un numero limitato di passaggi da esaminare nei dettagli per configurare il gruppo di risorse e la rete virtuale.

### <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Per poter creare una rete virtuale, è prima necessario creare un gruppo di risorse per l'hosting della rete virtuale. Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Questo esempio crea un gruppo di risorse denominato **CreateVNetQS-RG** nella località **eastus** :

```azurepowershell-interactive
$rg = @{
    Name = 'CreateVNetQS-rg'
    Location = 'EastUS'
}
New-AzResourceGroup @rg
```

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Questo esempio crea una rete virtuale predefinita denominata **myVNet** nella località **eastus** :

```azurepowershell-interactive
$vnet = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-subnet"></a>Aggiungere una subnet

Azure distribuisce risorse in una subnet all'interno di una rete virtuale, pertanto è necessario creare una subnet. Creare una configurazione di una subnet denominata **default** con [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnet = @{
    Name = 'default'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associare la subnet alla rete virtuale

È possibile scrivere la configurazione della subnet nella rete virtuale con [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Questo comando crea la subnet:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale.

### <a name="create-the-first-vm"></a>Creare la prima VM

Creare la prima macchina virtuale con [New-AzVM](/powershell/module/az.compute/new-azvm). Quando si esegue il comandi seguente, viene chiesto di immettere le credenziali. Immettere un nome utente e una password per la macchina virtuale:

```azurepowershell-interactive
$vm1 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM1'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm1 -AsJob
```

L'opzione `-AsJob` consente di creare la macchina virtuale in background. È possibile continuare con il passaggio successivo.

Quando Azure inizia a creare la macchina virtuale in background, il risultato sarà qualcosa di simile a quanto segue:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>Creare la seconda VM

Creare la seconda macchina virtuale con questo comando:

```azurepowershell-interactive
$vm2 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM2'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm2
```

Sarà necessario creare un altro utente e un'altra password. Azure richiede alcuni minuti per creare la macchina virtuale.

> [!IMPORTANT]
> Prima di continuare con i passaggi successivi, attendere che Azure abbia finito.  La fine della procedura sarà segnalata dalla restituzione di output in PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Per ottenere l'indirizzo IP pubblico della macchina virtuale, usare il cmdlet [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress).

Questo esempio restituisce l'indirizzo IP pubblico della VM **myVm1**:

```azurepowershell-interactive
$ip = @{
    Name = 'myVM1'
    ResourceGroupName = 'CreateVNetQS-rg'
}
Get-AzPublicIpAddress @ip | select IpAddress
```

Aprire un prompt dei comandi nel computer locale. Eseguire il comando `mstsc`. Sostituire `<publicIpAddress>` con l'indirizzo IP pubblico ottenuto nell'ultimo passaggio:

> [!NOTE]
> Se questi comandi sono in esecuzione da un prompt di PowerShell nel computer locale, con il modulo Az PowerShell versione 1.0 o successiva, è possibile continuare in tale interfaccia.

```cmd
mstsc /v:<publicIpAddress>
```
1. Quando richiesto, selezionare **Connetti**.

1. Immettere il nome utente e la password specificati al momento della creazione della VM.

    > [!NOTE]
    > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Si potrebbe ricevere un avviso del certificato. Se sì, selezionare **Sì** oppure **Continua**.

## <a name="communicate-between-vms"></a>Stabilire la comunicazione tra le macchine virtuali

1. Nel desktop remoto di **myVm1**, aprire PowerShell.

1. Immettere `ping myVm2`.

    Si otterrà un risultato simile a quanto segue:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Il ping ha esito negativo perché usa il protocollo ICMP (Internet Control Message Protocol). Per impostazione predefinita, il protocollo ICMP non dispone dell'autorizzazione del firewall di Windows.

1. Per consentire a **myVm2** di eseguire il ping su **myVm1** in un passaggio successivo, immettere questo comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Questo comando permette il passaggio di dati in ingresso da parte del protocollo ICMP attraverso il firewall di Windows.

1. Chiudere la connessione Desktop remoto a **myVm1**.

1. Ripetere i passaggi di [Connettersi a una macchina virtuale da internet](#connect-to-a-vm-from-the-internet). Questa volta, connettersi a **myVm2**.

1. Da un prompt dei comandi nella VM **myVm2** immettere `ping myvm1`.

    Si otterrà un risultato simile a quanto segue:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Si ricevono risposte da **myVm1**, poiché il protocollo ICMP è stato consentito in Windows Firewall nella VM **myVm1** in un passaggio precedente.

1. Chiudere la connessione Desktop remoto a **myVm2**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non si ha più bisogno della rete virtuale e delle macchine virtuali, usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse che contiene:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateVNetQS-rg' -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido: 

* Sono state create una rete virtuale predefinita e due VM. 
* È stata effettuata la connessione a una VM da Internet ed è stata stabilita una comunicazione privata tra le due VM.

La comunicazione privata tra macchine virtuali non è soggetta a restrizioni in una rete virtuale. 

Per altre informazioni sulla configurazione di diversi tipi di comunicazioni di rete delle VM, passare all'articolo successivo:
> [!div class="nextstepaction"]
> [Filtrare il traffico di rete](tutorial-filter-network-traffic.md)
