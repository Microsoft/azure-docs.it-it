---
title: Distribuire e configurare il firewall di Azure con Azure PowerShell
description: Questo articolo illustra come distribuire e configurare il firewall di Azure usando il Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/28/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: c720d7c261421ade9dfce01f0b116123dcab1e55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071704"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Distribuire e configurare il firewall di Azure con Azure PowerShell

Il controllo dell'accesso alla rete in uscita è un componente importante di un piano di sicurezza della rete generale. Ad esempio, potrebbe essere utile limitare l'accesso ai siti Web. In alternativa, potrebbe essere utile limitare gli indirizzi IP e le porte in uscita a cui è possibile accedere.

È possibile controllare l'accesso alla rete in uscita da una subnet di Azure con Firewall di Azure. Con Firewall di Azure, è possibile configurare:

* Regole di applicazione che definiscono i nomi di dominio completi (FQDN) accessibili da una subnet.
* Regole di rete che definiscono l'indirizzo di origine, il protocollo, la porta di destinazione e l'indirizzo di destinazione.

Il traffico di rete è sottoposto alle regole del firewall configurate quando si instrada il traffico di rete al firewall come gateway predefinito della subnet.

Per questo articolo, si crea un VNet singolo semplificato con tre subnet per semplificare la distribuzione. Per le distribuzioni di produzione è consigliabile un [modello hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), in cui il firewall si trova nella propria rete virtuale. I server del carico di lavoro si trovano nelle reti virtuali associate all'interno della stessa area con una o più subnet.

* **AzureFirewallSubnet**: in questa subnet si trova il firewall.
* **Workload-SN**: in questa subnet si trova il server del carico di lavoro. Il traffico di rete di questa subnet passa attraverso il firewall.
* **Jump-SN**: in questa subnet si trova il jump server. Il jump server ha un indirizzo IP pubblico a cui è possibile connettersi con Desktop remoto. Da qui è quindi possibile connettersi al server del carico di lavoro (tramite un'altra istanza di Desktop remoto).

![Infrastruttura di rete dell'esercitazione](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In questo articolo vengono illustrate le operazioni seguenti:


* Configurare un ambiente di rete di test
* Distribuire un firewall
* Creare una route predefinita
* Configurare una regola dell'applicazione per consentire l'accesso a www.google.com
* Configurare una regola di rete per consentire l'accesso a server DNS esterni
* Testare il firewall

Se si preferisce, è possibile completare questa procedura usando il [portale di Azure](tutorial-firewall-deploy-portal.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per questa procedura è necessario eseguire PowerShell in locale. È necessario aver installato il modulo di Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Dopo avere verificato la versione di PowerShell, eseguire `Connect-AzAccount` per creare una connessione ad Azure.

## <a name="set-up-the-network"></a>Configurare la rete

In primo luogo, creare un gruppo di risorse per contenere le risorse necessarie per distribuire il firewall. Creare quindi una rete virtuale, le subnet e i server di test.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Il gruppo di risorse contiene tutte le risorse per la distribuzione.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Creare una rete virtuale

Questa rete virtuale presenta tre subnet:

> [!NOTE]
> La dimensione della subnet AzureFirewallSubnet è /26. Per altre informazioni sulle dimensioni delle subnet, vedere le [domande frequenti su Firewall di Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```
A questo punto, creare la rete virtuale:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare ora le macchine virtuali per il jump server e il server del carico di lavoro e posizionarle nelle subnet appropriate.
Quando richiesto, digitare il nome utente e la password per la macchina virtuale.

Creare la macchina virtuale Srv-Jump.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Creare una macchina virtuale del carico di lavoro senza indirizzo IP pubblico.
Quando richiesto, digitare il nome utente e la password per la macchina virtuale.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>Distribuire il firewall

A questo punto, distribuire il firewall nella rete virtuale.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Annotare l'indirizzo IP privato. Sarà necessario più avanti per la creazione della route predefinita.

## <a name="create-a-default-route"></a>Creare una route predefinita

Creare una tabella con propagazione di route BGP disabilitata

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Configurare una regola di applicazione

La regola applicazione consente l'accesso in uscita a www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections.Add($AppRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

Firewall di Azure include una raccolta di regole predefinite per i nomi di dominio completi dell'infrastruttura consentiti per impostazione predefinita. Questi nomi di dominio completi sono specifici per la piattaforma e non possono essere usati per altri scopi. Per altre informazioni, vedere [Infrastructure FQDNs](infrastructure-fqdns.md) (FQDN dell'infrastruttura).

## <a name="configure-a-network-rule"></a>Configurare una regola di rete

La regola di rete consente l'accesso in uscita a due indirizzi IP sulla porta 53 (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections.Add($NetRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Modificare l'indirizzo DNS primario e secondario per l'interfaccia di rete **Srv-Work**

A scopo di test in questa procedura, configurare gli indirizzi DNS primario e secondario del server. Questo non è un requisito generale di Firewall di Azure.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>Testare il firewall

A questo punto testare il firewall per verificare che funzioni come previsto.

1. Prendere nota dell'indirizzo IP privato per la macchina virtuale **SRV-work** :

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Connettere una sessione di Desktop remoto alla macchina virtuale **Srv-Jump** e accedere. Da qui, aprire una connessione Desktop remoto all'indirizzo IP privato **SRV-lavoro** ed effettuare l'accesso.

3. In **SRV-work**aprire una finestra di PowerShell ed eseguire i comandi seguenti:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Entrambi i comandi devono restituire le risposte, mostrando che le query DNS stanno attraversando il firewall.

1. Eseguire i comandi seguenti:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Le `www.google.com` richieste avranno esito positivo e le `www.microsoft.com` richieste avranno esito negativo. Ciò dimostra che le regole del firewall funzionano come previsto.

A questo punto si è verificato che le regole del firewall funzionano:

* È possibile risolvere i nomi DNS con il server DNS esterno configurato.
* È possibile passare al nome di dominio completo consentito ma non agli altri.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile proteggere le risorse del firewall per l'esercitazione successiva o, se non sono più necessarie, eliminare il gruppo di risorse **test-FW-RG** per eliminare tutte le risorse correlate al firewall:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: monitorare i log del Firewall di Azure](./tutorial-diagnostics.md)
