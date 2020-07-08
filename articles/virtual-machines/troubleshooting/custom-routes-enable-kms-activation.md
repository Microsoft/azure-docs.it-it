---
title: Usare le route personalizzate di Azure per abilitare l'attivazione del server di gestione delle chiavi con tunneling forzato | Microsoft Docs
description: Mostra come usare le route personalizzate di Azure per abilitare l'attivazione del server di gestione delle chiavi con tunneling forzato in Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 90034a56fcf5211059d37270e12391249f7a16b5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77920162"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>L'attivazione di Windows ha esito negativo in uno scenario di tunneling forzato

Questo articolo descrive come risolvere il problema di attivazione del server di gestione delle chiavi che potrebbe verificarsi quando viene abilitato il tunneling forzato in una connessione VPN da sito a sito o in scenari ExpressRoute.

## <a name="symptom"></a>Sintomo

Viene abilitato il [tunneling forzato](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) nelle subnet della rete virtuale di Azure per indirizzare alla rete locale tutto il traffico associato a Internet. In questo scenario, le macchine virtuali (VM) di Azure che eseguono Windows non riescono ad attivare Windows.

## <a name="cause"></a>Causa

Le macchine virtuali Windows di Azure devono connettersi al server di gestione delle chiavi di Azure per l'attivazione di Windows. Per l'attivazione è necessario che la richiesta provenga da un indirizzo IP pubblico di Azure. Nello scenario di tunneling forzato l'attivazione avrà esito negativo poiché la richiesta proviene dalla rete locale anziché da un indirizzo IP pubblico di Azure.

## <a name="solution"></a>Soluzione

Per risolvere questo problema, usare il traffico di attivazione da route a route personalizzato di Azure verso il server di gestione delle chiavi di Azure.

L'indirizzo IP 23.102.135.246 è l'indirizzo del server di gestione delle chiavi per il cloud Azure Global. Il suo nome DNS è kms.core.windows.net. Se si usano altre piattaforme di Azure, ad esempio Azure Germania, è necessario usare l'indirizzo IP del server di gestione delle chiavi corrispondente. Per altre informazioni, vedere la tabella seguente:

|Piattaforma| DNS del server di gestione delle chiavi|Indirizzo IP del server di gestione delle chiavi|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Germania|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|21Vianet per Azure Cina|kms.core.chinacloudapi.cn|42.159.7.249|


Per aggiungere la route personalizzata, seguire questa procedura:

### <a name="for-resource-manager-vms"></a>Per le macchine virtuali di Resource Manager

 

> [!NOTE] 
> L'attivazione usa indirizzi IP pubblici e sarà interessata da uno SKU standard Load Balancer la configurazione. Per informazioni sui requisiti, vedere attentamente le [connessioni in uscita in Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) .

1. Aprire Azure PowerShell e [accedere alla propria sottoscrizione di Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Eseguire i comandi seguenti:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. Passare alla macchina virtuale che presenta problemi di attivazione. Usare [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) per verificare se sia possibile raggiungere il server KMS:

        psping kms.core.windows.net:1688

4. Provare ad attivare Windows e verificare se il problema è stato risolto.

### <a name="for-classic-vms"></a>Per VM classiche

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

1. Aprire Azure PowerShell e [accedere alla propria sottoscrizione di Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Eseguire i comandi seguenti:

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Passare alla macchina virtuale che presenta problemi di attivazione. Usare [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) per verificare se sia possibile raggiungere il server KMS:

        psping kms.core.windows.net:1688

4. Provare ad attivare Windows e verificare se il problema è stato risolto.

## <a name="next-steps"></a>Passaggi successivi

- [Chiavi di configurazione di client del Servizio di gestione delle chiavi](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Review and Select Activation Methods](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
) (Esaminare e selezionare i metodi di attivazione)
