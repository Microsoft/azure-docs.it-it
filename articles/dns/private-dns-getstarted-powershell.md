---
title: Avvio rapido - Creare una zona DNS privato di Azure con Azure PowerShell
description: Questo argomento di avvio rapido illustra come creare e gestire la prima zona DNS privato e il primo record usando Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: ee6dde6b34cccd415f9bf2052f65dcbe940715c1
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424377"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>Avvio rapido: Creare una zona DNS privato di Azure con Azure PowerShell

Questo articolo illustra i passaggi per creare la prima zona DNS privata e il primo record con Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare una zona DNS privata nella rete virtuale, specificare l'elenco di reti virtuali autorizzate a risolvere i record nella zona.  Tali reti vengono definite reti virtuali *collegate*. Se è abilitata la registrazione automatica, DNS di Azure aggiorna anche i record di zona ogni volta che una macchina virtuale viene creata o eliminata o quando cambia il relativo indirizzo IP.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Se si preferisce, è possibile completare questo avvio rapido usando l'[interfaccia della riga di comando di Azure](private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Innanzitutto, creare un gruppo di risorse per contenere la zona DNS: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-private-dns-zone"></a>Creare una zona DNS privato

Viene creata una zona DNS con il cmdlet `New-AzPrivateDnsZone` .

L'esempio seguente crea una rete virtuale denominata **myAzureVNet**. Viene quindi creata una zona DNS denominata **private.contoso.com** nel gruppo di risorse **MyAzureResourceGroup**. Dopo il collegamento della zona DNS alla rete virtuale **MyAzureVnet** viene abilitata la registrazione automatica.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Se si vuole creare una zona solo per la risoluzione dei nomi (senza la creazione automatica dei nomi host), è possibile omettere il parametro `-EnableRegistration`.

### <a name="list-dns-private-zones"></a>Elencare le zone DNS private

Omettendo il nome della zona da `Get-AzPrivateDnsZone`, è possibile enumerare tutte le zone in un gruppo di risorse. Questa operazione restituisce una matrice di oggetti di zona.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Omettendo il nome della zona e il nome del gruppo di risorse da `Get-AzPrivateDnsZone`, è possibile enumerare tutte le zone nella sottoscrizione di Azure.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>Creare le macchine virtuali di test

A questo punto, creare due macchine virtuali in modo da testare la zona DNS privata:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Questa operazione richiederà qualche minuto.

## <a name="create-an-additional-dns-record"></a>Creare un record DNS aggiuntivo

I set di record vengono creati usando il cmdlet `New-AzPrivateDnsRecordSet`. L'esempio seguente crea un record con il nome relativo **db** nella zona DNS **private.contoso.com** nel gruppo di risorse **MyAzureResourceGroup**. Il nome completo del set di record è **db.private.contoso.com**. Il tipo di record è "A", con indirizzo IP "10.2.0.4", e la durata (TTL) è 3600 secondi.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Visualizzare i record DNS

Per elencare i record DNS nella zona, eseguire:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>Testare la zona privata

A questo punto è possibile testare la risoluzione dei nomi per la zona privata **private.contoso.com**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configurare le macchine virtuali per consentire il traffico ICMP in entrata

È possibile usare il comando ping per testare la risoluzione dei nomi. Configurare quindi il firewall in entrambe le macchine virtuali per consentire i pacchetti ICMP in ingresso.

1. Connettersi a myVM01 e aprire una finestra di Windows PowerShell con privilegi di amministratore.
2. Eseguire il comando seguente:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Ripetere l'operazione per myVM02.

### <a name="ping-the-vms-by-name"></a>Effettuare il ping delle macchine virtuali in base al nome

1. Dal prompt dei comandi di Windows PowerShell in myVM02, effettuare il ping di myVM01 usando il nome host registrato automaticamente:

   ```
   ping myVM01.private.contoso.com
   ```

   Verrà visualizzato un output simile a questo:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Ora effettuare il ping del nome **db** creato in precedenza:

   ```
   ping db.private.contoso.com
   ```

   Verrà visualizzato un output simile a questo:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse **MyAzureResourceGroup** per eliminare le risorse create in questo articolo.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scenari di Zone private di DNS di Azure](private-dns-scenarios.md)
