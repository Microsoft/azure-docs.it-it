---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/15/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6a90b23c10e08e8b14a18f9619cff5aaeb003cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045684"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress---no-gateway-connection"></a><a name="gwipnoconnection"></a> Per modificare il gateway di rete locale 'GatewayIpAddress' senza connessione gateway

Se l'indirizzo IP pubblico del dispositivo VPN a cui ci si vuole connettere è stato modificato, è necessario modificare il gateway di rete locale per riflettere tale modifica. Usare l'esempio per modificare un gateway di rete locale privo di connessione gateway.

Quando si modifica questo valore, è anche possibile modificare contemporaneamente i prefissi degli indirizzi. Assicurarsi di usare il nome del gateway di rete locale esistente per sovrascrivere le impostazioni correnti. Se si usa un nome diverso, creare un nuovo gateway di rete locale, invece di sovrascrivere il valore esistente.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="to-modify-the-local-network-gateway-gatewayipaddress---existing-gateway-connection"></a><a name="gwipwithconnection"></a> Per modificare il gateway di rete locale 'GatewayIpAddress' con una connessione gateway esistente

Se l'indirizzo IP pubblico del dispositivo VPN a cui ci si vuole connettere è stato modificato, è necessario modificare il gateway di rete locale per riflettere tale modifica. Se esiste già una connessione gateway è prima necessario rimuoverla. Dopo aver rimosso la connessione sarà possibile modificare l'indirizzo IP del gateway e ricreare una nuova connessione. È anche possibile modificare contemporaneamente i prefissi di indirizzo. Questo comporterà periodi di inattività per la connessione VPN. Quando si modifica l'indirizzo IP del gateway, non è necessario eliminare il gateway VPN. Occorre rimuovere solo la connessione.
 

1. Rimuovere la connessione. Il nome della connessione può essere trovato usando il cmdlet 'Get-AzVirtualNetworkGatewayConnection'.

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1
   ```
2. Modificare il valore 'GatewayIpAddress'. È anche possibile modificare contemporaneamente i prefissi di indirizzo. Assicurarsi di usare il nome del gateway di rete locale esistente per sovrascrivere le impostazioni correnti. In caso contrario si creerà un nuovo gateway di rete locale, invece di sovrascrivere quello esistente.

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name Site1 `
   -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
   -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
   ```
3. Creare la connessione. In questo esempio viene configurato un tipo di connessione IPsec. Quando si ricrea la connessione, usare il tipo di connessione specificato per la configurazione in uso. Per altri tipi di connessione, vedere la pagina dei [cmdlet di PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Per ottenere il nome VirtualNetworkGateway è possibile eseguire il cmdlet 'Get-AzVirtualNetworkGateway'.
   
    Impostare le variabili.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1

   $vnetgw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   ```
   
    Creare la connessione.

   ```azurepowershell-interactive 
   New-AzVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
   -Location "East US" `
   -VirtualNetworkGateway1 $vnetgw `
   -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```