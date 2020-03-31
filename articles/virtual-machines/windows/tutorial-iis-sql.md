---
title: 'Esercitazione: Creare macchine virtuali che eseguono uno stack SQL, IIS, .NET in Azure'
description: In questa esercitazione viene descritto come installare lo stack SQL di Azure, IIS e .NET in una macchina virtuale Windows in Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 762d67a291ec33e13ca9e7311b0cf11da140dd6c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80154271"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Esercitazione: Installare lo stack SQL, IIS, .NET in una macchina virtuale Windows con Azure PowerShell

In questa esercitazione verrà installato uno stack SQL, IIS, .NET usando Azure PowerShell. Questo stack è costituito da due VM che eseguono Windows Server 2016, una con IIS e .NET e l'altra con SQL Server.

> [!div class="checklist"]
> * Creare una macchina virtuale 
> * Installare IIS e .NET Core SDK nella VM
> * Creare una VM che esegue SQL Server
> * Installare l'estensione SQL Server

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="create-an-iis-vm"></a>Creare una macchina virtuale IIS 

In questo esempio si usa il cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) in Cloud Shell di PowerShell per creare rapidamente una VM Windows Server 2016 e quindi installare IIS e .NET Framework. Poiché le VM IIS e SQL condividono un gruppo di risorse e una rete virtuale, vengono create le variabili per tali nomi.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Installare IIS e .NET Framework usando l'estensione per script personalizzati con il cmdlet [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).

```azurepowershell-interactive
Set-AzVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Creare un'altra subnet

Creare una seconda subnet per la macchina virtuale SQL. Recuperare la rete virtuale con [Get-AzVirtualNetwork]{/powershell/module/az.network/get-azvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Creare una configurazione per la subnet con [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Aggiornare la rete virtuale con le informazioni relative alla nuova subnet usando [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork).
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>VM di Azure SQL

Per creare la macchina virtuale SQL, usare un'immagine preconfigurata di Azure Marketplace di un server SQL. Prima viene creata la VM, quindi l'estensione SQL Server viene installata nella VM. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Usare [Set-AzVMSqlServerExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsqlserverextension) per aggiungere l'[estensione SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) alla VM SQL.

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è installato uno stack SQL&#92;IIS&#92;.NET usando Azure PowerShell. Si è appreso come:

> [!div class="checklist"]
> * Creare una macchina virtuale 
> * Installare IIS e .NET Core SDK nella VM
> * Creare una VM che esegue SQL Server
> * Installare l'estensione SQL Server

Passare all'esercitazione successiva per apprendere come proteggere il server Web IIS con i certificati TLS/SSL.

> [!div class="nextstepaction"]
> [Proteggere il server Web IIS con i certificati TLS/SSL](tutorial-secure-web-server.md)

