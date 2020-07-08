---
title: Creare una VM con un indirizzo IP privato statico-Azure PowerShell
description: Informazioni su come creare una macchina virtuale con un indirizzo IP privato usando PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: ced76b73a8a08e6886cf0cef04c74a82d05c75dd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708128"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Creare una macchina virtuale con un indirizzo IP privato statico tramite PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile creare una macchina virtuale (VM) con un indirizzo IP privato statico. Assegnare un indirizzo IP privato statico, anziché un indirizzo dinamico, se si vuole selezionare l'indirizzo da una subnet assegnato a una macchina virtuale. Altre informazioni sugli [indirizzi IP privati statici](virtual-network-ip-addresses-overview-arm.md#allocation-method). Per modificare un indirizzo IP privato assegnato a una macchina virtuale esistente da dinamico a statico o per usare indirizzi IP pubblici, vedere [aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

È possibile eseguire la procedura seguente dal computer locale o tramite Azure Cloud Shell. Per usare il computer locale, assicurarsi che [Azure PowerShell sia installato](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Per usare Azure Cloud Shell, selezionare **Prova** nell'angolo superiore destro di una qualsiasi finestra di comando riportata di seguito. Cloud Shell consente di accedere ad Azure.

1. Se si usa Cloud Shell, andare al passaggio 2. Aprire una sessione di comando e accedere ad Azure con `Connect-AzAccount`.
2. Creare un gruppo di risorse con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse nell'area di Azure Stati Uniti orientali:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Creare una configurazione di subnet e una rete virtuale con i comandi [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) e [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) :

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Creare un'interfaccia di rete nella rete virtuale e assegnare un indirizzo IP privato dalla subnet all'interfaccia di rete con i comandi [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) e [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) :

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Creare una configurazione di macchina virtuale con [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)e quindi creare la macchina virtuale con [New-AzVM](/powershell/module/az.Compute/New-azVM). Quando richiesto, specificare un nome utente e una password da usare come credenziali di accesso per la macchina virtuale:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Sebbene sia possibile aggiungere impostazioni di indirizzi IP privati al sistema operativo, è consigliabile non eseguire questa operazione finché non viene letta l' [aggiunta di un indirizzo IP privato a un sistema operativo](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Per accedere alla macchina virtuale da Internet, è necessario assegnare un indirizzo IP pubblico alla macchina virtuale. È anche possibile modificare l'assegnazione di un indirizzo IP privato dinamico a un'assegnazione statica. Per informazioni dettagliate, vedere [aggiungere o modificare indirizzi IP](virtual-network-network-interface-addresses.md). Inoltre, è consigliabile limitare il traffico di rete alla macchina virtuale associando un gruppo di sicurezza di rete all'interfaccia di rete, la subnet in cui è stata creata l'interfaccia di rete o entrambe. Per informazioni dettagliate, vedere [gestire i gruppi di sicurezza di rete](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, è possibile usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuoverli:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli [indirizzi IP privati](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e sull'assegnazione di un [indirizzo IP privato statico](virtual-network-network-interface-addresses.md#add-ip-addresses) a una macchina virtuale di Azure.
- Altre informazioni sulla creazione di macchine virtuali [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .
