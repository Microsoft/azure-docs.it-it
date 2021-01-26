---
title: Bilanciamento del carico in più configurazioni IP - Interfaccia della riga di comando di Azure
titleSuffix: Azure Load Balancer
description: In questo articolo vengono fornite informazioni sul bilanciamento del carico tra configurazioni IP primarie e secondarie usando l'interfaccia della riga di comando di Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 8b10e850fd3ae0282785164596f537652148a716
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791004"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Bilanciamento del carico in più configurazioni IP tramite PowerShell

> [!div class="op_single_selector"]
> * [Portale](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


Questo articolo descrive come usare Azure Load Balancer con più indirizzi IP su una scheda di interfaccia di rete secondaria. Per questo scenario sono disponibili due VM con Windows, ognuna con una scheda di interfaccia di rete primaria e secondaria. Ogni scheda di interfaccia di rete secondaria dispone di due configurazioni di indirizzo IP. Ogni macchina virtuale ospita entrambi i siti Web: contoso.com e fabrikam.com. Ogni sito Web è associato a una delle configurazioni IP della scheda di interfaccia di rete secondaria. Azure Load Balancer viene usato per esporre due indirizzi IP front-end, uno per ogni sito Web, per distribuire il traffico alla rispettiva configurazione IP per il sito Web. Questo scenario usa lo stesso numero di porta per entrambi i front-end, nonché per entrambi gli indirizzi IP del pool back-end.

![Immagine dello scenario LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Procedura per il bilanciamento del carico in più configurazioni IP

Per ottenere lo scenario descritto in questo articolo, seguire questa procedura:

1. Installare Azure PowerShell. Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/).
2. Creare un gruppo di risorse usando le impostazioni seguenti:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Per altre informazioni, vedere il passaggio 2 dell'articolo relativo alla [creazione di un gruppo di risorse](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Creare un set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) che dovrà contenere le macchine virtuali. Per questo scenario, usare il comando seguente:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Seguire le istruzioni contenute nei passaggi da 3 a 5 dell'articolo [Creare una VM Windows](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json) per preparare la creazione di una macchina virtuale con una scheda di interfaccia di rete singola. Eseguire il passaggio 6.1 e sostituire il comando del passaggio 6.2 con quanto segue:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Completare quindi i passaggi da 6.3 a 6.8 dell'articolo [Creare una VM Windows](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json).

5. Aggiungere una seconda configurazione IP a ognuna delle macchine virtuali. Seguire le istruzioni contenute nell'articolo [Assegnare più indirizzi IP alle macchine virtuali](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add). Usare le impostazioni di configurazione seguenti:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Ai fini di questa esercitazione, non è necessario associare le configurazioni IP secondarie agli indirizzi IP pubblici. Modificare il comando per rimuovere la parte relativa all'associazione dell'indirizzo IP pubblico.

6. Completare nuovamente i passaggi da 4 a 6 di questo articolo per VM2. In questo caso assicurarsi di sostituire il nome della macchina virtuale con VM2. Si noti che non è necessario creare una rete virtuale per la seconda macchina virtuale. È possibile scegliere se creare o meno una nuova subnet in base al caso d'uso.

7. Creare due indirizzi IP pubblici e archiviarli in variabili appropriate, come illustrato di seguito:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Creare due configurazioni IP front-end:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Creare i pool di indirizzi back-end, un probe e regole di bilanciamento del carico:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Dopo aver creato queste risorse, creare il servizio di bilanciamento del carico:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Aggiungere il secondo pool di indirizzi back-end e la configurazione IP front-end per il servizio di bilanciamento del carico appena creato:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. I comandi seguenti permettono di ottenere le schede di interfaccia di rete e aggiungono entrambe le configurazioni IP di ogni scheda di interfaccia di rete secondaria al pool di indirizzi back-end del servizio di bilanciamento del carico:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Infine, è necessario configurare i record risorsa DNS in modo che puntino all'indirizzo IP front-end corrispondente di Azure Load Balancer. È possibile ospitare i domini nel servizio DNS di Azure. Per altre informazioni sull'uso del servizio DNS di Azure con Azure Load Balancer, vedere [Uso del servizio DNS di Azure con altri servizi di Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su come combinare i servizi di bilanciamento del carico di Azure sono disponibili in [Uso dei servizi di bilanciamento del carico in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Informazioni su come usare diversi tipi di log in Azure per gestire e risolvere i problemi del servizio di bilanciamento del carico nei [log di monitoraggio di Azure per Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).