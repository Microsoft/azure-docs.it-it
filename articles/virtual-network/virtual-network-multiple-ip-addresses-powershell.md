---
title: Più indirizzi IP per le macchine virtuali di Azure - PowerShell | Documentazione Microsoft
description: Informazioni su come assegnare più indirizzi IP a una macchina virtuale tramite PowerShell. | Gestione risorse
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: allensu
ms.openlocfilehash: 819b6af0d336e454aeeb67a9be62109cb6b08bb8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708230"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Assegnare più indirizzi IP alle macchine virtuali usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Questo articolo spiega come creare una macchina virtuale (VM) tramite il modello di distribuzione Azure Resource Manager usando PowerShell. Non è possibile a assegnare più indirizzi IP alle risorse create tramite il modello di distribuzione classica. Per altre informazioni sui modelli di distribuzione di Azure, leggere l'articolo [Understand Azure deployment models](../resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Creare una macchina virtuale con più indirizzi IP

La procedura seguente illustra come creare una macchina virtuale di esempio con più indirizzi IP, come descritto nello scenario. Modificare i valori delle variabili come necessario per l'implementazione.

1. Aprire un prompt dei comandi di PowerShell e completare i passaggi rimanenti in questa sezione in una singola sessione di PowerShell. Se PowerShell non è già installato e configurato, completare la procedura disponibile nell'articolo [Come installare e configurare Azure PowerShell](/powershell/azure/overview) .
2. Accedere al proprio account con il comando `Connect-AzAccount`.
3. Sostituire *myResourceGroup* e *westus* con un nome e una località di propria scelta. Creare un gruppo di risorse. Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Creare una rete virtuale (VNet) e una subnet nella stessa località del gruppo di risorse:

   ```powershell

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

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Creare un gruppo di sicurezza di rete e una regola. Il gruppo di sicurezza di rete protegge la macchina virtuale usando regole in entrata e in uscita. In questo caso viene creata una regola in entrata per la porta 3389 che consente connessioni desktop remoto in ingresso.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Definire la configurazione IP primaria della scheda di interfaccia di rete. Modificare 10.0.0.4 in un indirizzo valido nella subnet creata, se il valore definito in precedenza non è stato usato. Prima di assegnare un indirizzo IP statico, è consigliabile verificare che non sia già in uso. Immettere il comando `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Se l'indirizzo è disponibile, l'output restituisce *True*. Se non è disponibile, l'output restituisce *false* e un elenco di indirizzi disponibili. 

    Nei comandi seguenti **sostituire \<replace-with-your-unique-name> con il nome DNS univoco da usare.** Il nome deve essere univoco tra tutti gli indirizzi IP pubblici all'interno di un'area di Azure. Questo parametro è facoltativo. Può essere rimosso se si intende connettersi alla macchina virtuale tramite l'indirizzo IP pubblico.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Quando si assegnano più configurazioni IP a un'interfaccia di rete, è necessario assegnare una configurazione a *Primary*.

    > [!NOTE]
    > Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per altre informazioni sui prezzi degli indirizzi IP, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses) . È previsto un limite per il numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione. Per altre informazioni sui limiti, vedere l'articolo [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

7. Definire la configurazione IP secondaria della schede di interfaccia di rete. È possibile aggiungere o rimuovere le configurazioni in base alle esigenze. Ogni configurazione IP deve avere un indirizzo IP privato assegnato. Ogni configurazione può avere facoltativamente un indirizzo IP pubblico assegnato.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Creare la scheda di interfaccia di rete e associarvi le tre configurazioni IP:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Anche se in questo articolo tutte le configurazioni vengono assegnate a una sola scheda di interfaccia di rete, è possibile assegnare più configurazioni IP a ogni scheda di interfaccia di rete collegata alla macchina virtuale. Per informazioni su come creare una VM con più interfacce di rete, leggere l'articolo [Creare una macchina virtuale con più schede di interfaccia di rete usando PowerShell](../virtual-machines/windows/multiple-nics.md).

9. Creare la macchina virtuale immettendo i comandi seguenti:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Aggiungere gli indirizzi IP privati al sistema operativo della macchina virtuale seguendo la procedura per il proprio sistema operativo riportata nella sezione [Aggiungere indirizzi IP a una macchina virtuale](#os-config) di questo articolo. Non aggiungere gli indirizzi IP pubblici al sistema operativo.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Aggiungere indirizzi IP a una macchina virtuale

È possibile aggiungere indirizzi IP privati e pubblici all'interfaccia di rete di Azure completando la procedura seguente. Gli esempi delle sezioni seguenti presuppongono che si disponga già di una VM con le tre configurazioni IP descritte nello [scenario](#scenario) di questo articolo, ma questa condizione non è indispensabile.

1. Aprire un prompt dei comandi di PowerShell e completare i passaggi rimanenti in questa sezione in una singola sessione di PowerShell. Se PowerShell non è già installato e configurato, completare la procedura disponibile nell'articolo [Come installare e configurare Azure PowerShell](/powershell/azure/overview) .
2. Cambiare i "valori" delle variabili $Variables seguenti specificando il nome dell'interfaccia di rete a cui si vogliono aggiungere l'indirizzo IP, il gruppo di risorse e la località in cui esiste la scheda di interfaccia di rete:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Se non si conosce il nome dell'interfaccia di rete da modificare, immettere i comandi seguenti e quindi cambiare i valori delle variabili precedenti:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Creare una variabile e impostarla sull'interfaccia di rete esistente digitando il comando seguente:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. Nei comandi seguenti modificare *myVNet* e *mySubnet* con i nomi della rete virtuale e della subnet a cui la scheda di interfaccia di rete è connessa. Immettere i comandi per recuperare gli oggetti della rete virtuale e della subnet a cui la scheda di interfaccia di rete è connessa:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Se non si conosce il nome di rete virtuale o una subnet che la scheda di interfaccia di rete è connesso, immettere il comando seguente:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Cercare un testo simile al seguente nell'output restituito:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    In questo output *MyVnet* è la rete virtuale e *MySubnet* è la subnet a cui la scheda di interfaccia di rete è connessa.

5. Completare i passaggi in una delle sezioni seguenti, a seconda delle esigenze:

   **Aggiungere un indirizzo IP privato**

   Per aggiungere un indirizzo IP privato a una scheda di interfaccia di rete, è necessario creare una configurazione IP. Il comando seguente crea una configurazione con un indirizzo IP statico 10.0.0.7. Quando si specifica un indirizzo IP statico, deve essere un indirizzo non usato per la subnet. Si consiglia di verificare l'indirizzo per assicurarsi che sia disponibile tramite il comando `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet`. Se l'indirizzo IP è disponibile, l'output restituisce *True*. Se non è disponibile, l'output restituisce *False* e un elenco di indirizzi disponibili.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Creare tutte le configurazioni usando nomi di configurazione univoci e indirizzi IP privati (per le configurazioni con indirizzi IP statici).

   Aggiungere l'indirizzo IP privato al sistema operativo della VM completando i passaggi relativi al sistema operativo indicati nella sezione [Aggiungere indirizzi IP al sistema operativo di una VM](#os-config) di questo articolo.

   **Aggiungere un indirizzo IP pubblico**

   Per aggiungere un indirizzo IP pubblico è necessario associare una risorsa indirizzo IP pubblico a una configurazione IP nuova o esistente. Completare i passaggi in una delle sezioni che seguono, a seconda del caso.

   > [!NOTE]
   > Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per altre informazioni sui prezzi degli indirizzi IP, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses) . È previsto un limite per il numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione. Per altre informazioni sui limiti, vedere l'articolo [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
   >

   **Associare la risorsa indirizzo IP pubblico a una nuova configurazione IP**

   Ogni volta che si aggiunge un indirizzo IP pubblico a una nuova configurazione IP, è necessario aggiungere anche un indirizzo IP privato, perché tutte le configurazioni IP devono avere un indirizzo IP privato. È possibile aggiungere una risorsa indirizzo IP pubblico esistente o crearne una nuova. Per crearne una nuova, usare il comando seguente:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Per creare una nuova configurazione IP con un indirizzo IP privato statico e la risorsa indirizzo IP pubblico *myPublicIp3* associata, immettere il comando seguente:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Associare la risorsa indirizzo IP pubblico a una configurazione IP esistente**

   Una risorsa indirizzo IP pubblico può essere associata a una configurazione IP cui non ne sia associata alcuna. È possibile stabilire se una configurazione IP dispone di un indirizzo IP pubblico associato immettendo il comando seguente:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   L'output sarà simile al seguente:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Poiché la colonna **PublicIpAddress** per *IpConfig-3* è vuota, nessuna risorsa di indirizzo IP pubblico è attualmente associata. È possibile aggiungere una risorsa indirizzo IP pubblico esistente a IpConfig-3 o immettere il comando seguente per crearne una:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Immettere il comando seguente per associare la risorsa indirizzo IP pubblico alla configurazione IP esistente denominata *ipconfig-3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Configurare l'interfaccia di rete con la configurazione IP immettendo il comando seguente:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Visualizzare le risorse indirizzo IP privato e indirizzo IP pubblico assegnate alla scheda di interfaccia di rete immettendo il comando seguente:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Aggiungere l'indirizzo IP privato al sistema operativo della VM completando i passaggi relativi al sistema operativo indicati nella sezione [Aggiungere indirizzi IP al sistema operativo di una VM](#os-config) di questo articolo. Non aggiungere l'indirizzo IP pubblico al sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]