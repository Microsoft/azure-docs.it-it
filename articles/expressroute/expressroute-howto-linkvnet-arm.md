---
title: 'Esercitazione: Collegare una rete virtuale a un circuito ExpressRoute - Azure PowerShell'
description: Questa esercitazione offre una panoramica delle procedure di collegamento di reti virtuali a circuiti ExpressRoute usando il modello di distribuzione di Resource Manager e Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 69067ca34b231f1b14f8cc854288c3ed4c4ac82a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91855991"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit"></a>Esercitazione: Connettere una rete virtuale a un circuito ExpressRoute
> [!div class="op_single_selector"]
> * [Portale di Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-linkvnet-cli.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (classic)](expressroute-howto-linkvnet-classic.md) (PowerShell (classico))
>

Questo articolo spiega come collegare le reti virtuali ai circuiti di Azure ExpressRoute usando il modello di distribuzione di Resource Manager e PowerShell. Le reti virtuali possono essere nella stessa sottoscrizione o appartenere a un'altra sottoscrizione. Questo articolo illustra inoltre come aggiornare un collegamento alla rete virtuale.

* È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute standard. Tutte le reti virtuali devono essere nella stessa area geopolitica quando si usa un circuito ExpressRoute standard. 

* Una singola rete virtuale può essere collegata a un massimo di quattro circuiti ExpressRoute. Seguire i passaggi di questo articolo per creare un nuovo oggetto di connessione per ogni circuito ExpressRoute a cui ci si connette. I circuiti ExpressRoute possono essere nella stessa sottoscrizione, diverse sottoscrizioni o una combinazione di entrambe le situazioni.

* Abilitando il componente aggiuntivo ExpressRoute Premium, è possibile collegare reti virtuali esterne all'area geopolitica del circuito ExpressRoute. Il componente aggiuntivo Premium consente anche di connettere più di 10 reti virtuali al circuito ExpressRoute, in base alla larghezza di banda scelta. Per altre informazioni sul componente aggiuntivo Premium, vedere le [domande frequenti](expressroute-faqs.md) .

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> - Collegare una rete virtuale della stessa sottoscrizione a un circuito
> - Collegare una rete virtuale di un'altra sottoscrizione a un circuito
> - Modificare una connessione di rete virtuale
> - Configurare ExpressRoute FastPath

## <a name="prerequisites"></a>Prerequisiti

* Verificare i [prerequisiti](expressroute-prerequisites.md), i [requisiti di routing](expressroute-routing.md) e i [flussi di lavoro](expressroute-workflows.md).

* È necessario avere un circuito ExpressRoute attivo. 
  * Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e fare in modo che venga abilitato dal provider di connettività. 
  * Assicurarsi di disporre del peering privato di Azure configurato per il circuito. Vedere l'articolo relativo alla [configurazione del routing](expressroute-howto-routing-arm.md) per istruzioni relative al routing. 
  * Assicurarsi che il peering privato di Azure sia configurato e stabilisca il peering BGP tra la rete e Microsoft per la connettività end-to-end.
  * Assicurarsi di disporre di una rete virtuale e di un gateway di rete virtuale creati e con provisioning completo. Seguire le istruzioni per [creare un gateway di rete virtuale per ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Un gateway di rete virtuale per ExpressRoute usa 'ExpressRoute' come GatewayType, non VPN.

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Collegare una rete virtuale della stessa sottoscrizione a un circuito
È possibile collegare un gateway di rete virtuale a un circuito ExpressRoute usando il cmdlet seguente. Prima di eseguire il cmdlet, assicurarsi che il gateway di rete virtuale sia stato creato e sia pronto per il collegamento.

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Collegare una rete virtuale di un'altra sottoscrizione a un circuito
È possibile condividere un circuito ExpressRoute tra più sottoscrizioni. La figura seguente mostra un semplice schema del funzionamento della condivisione di circuiti ExpressRoute tra più sottoscrizioni.

Ciascuno dei cloud più piccoli nel cloud di grandi dimensioni viene usato per rappresentare le sottoscrizioni appartenenti a reparti diversi di un'organizzazione. Ciascun reparto dell'organizzazione usa la propria sottoscrizione per distribuire i servizi, ma può condividere un solo circuito ExpressRoute per la connessione alla rete locale. Un singolo reparto (in questo esempio, IT) può possedere il circuito ExpressRoute. Altre sottoscrizioni all'interno dell'organizzazione possono usare il circuito ExpressRoute.

> [!NOTE]
> I costi relativi a connettività e larghezza di banda per il circuito ExpressRoute saranno addebitati al proprietario della sottoscrizione. Tutte le reti virtuali condividono la stessa larghezza di banda.
> 

:::image type="content" source="./media/expressroute-howto-linkvnet-classic/cross-subscription.png" alt-text="Connettività tra sottoscrizioni":::

### <a name="administration---circuit-owners-and-circuit-users"></a>Amministrazione - Proprietari e utenti del circuito

Il proprietario del circuito è l'utente esperto autorizzato della risorsa circuito ExpressRoute. Il proprietario del circuito può creare le autorizzazioni che possono essere riscattate dagli "utenti del circuito". Gli utenti del circuito sono i proprietari dei gateway di rete virtuale che non sono nella stessa sottoscrizione del circuito ExpressRoute. Gli utenti del circuito possono riscattare le autorizzazioni (un'autorizzazione per ogni rete virtuale).

Il proprietario del circuito ha la facoltà di modificare e revocare le autorizzazioni in qualsiasi momento. La revoca dell'autorizzazione comporterà l'eliminazione di tutti i collegamenti dalla sottoscrizione di cui è stato revocato l'accesso.

### <a name="circuit-owner-operations"></a>Operazioni del proprietario del circuito

**Per creare un'autorizzazione**

Il proprietario del circuito crea un'autorizzazione, che crea a sua volta una chiave di autorizzazione che può essere usata da un utente del circuito per connettere i propri gateway di rete virtuale al circuito ExpressRoute. Un'autorizzazione è valida per una sola connessione.

Il frammento di cmdlet seguente mostra come creare un'autorizzazione:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```

La risposta ai comandi precedenti conterrà la chiave di autorizzazione e lo stato:

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```

**Per verificare le autorizzazioni**

Il proprietario del circuito può esaminare tutte le autorizzazioni rilasciate in un particolare circuito eseguendo il cmdlet seguente:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Per aggiungere le autorizzazioni**

Il proprietario del circuito può aggiungere le autorizzazioni usando il cmdlet seguente:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Per eliminare le autorizzazioni**

Il proprietario del circuito può revocare o eliminare le autorizzazioni dell'utente eseguendo il cmdlet seguente:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operazioni dell'utente del circuito

L’utente del circuito deve richiedere l’ID peer e una chiave di autorizzazione al proprietario del circuito. La chiave di autorizzazione è un GUID.

L'ID peer può essere controllato con il comando seguente:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Per riscattare un'autorizzazione di connessione**

L'utente del circuito può eseguire il cmdlet seguente per riscattare un'autorizzazione di collegamento:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Per rilasciare un'autorizzazione di connessione**

È possibile rilasciare un'autorizzazione eliminando il collegamento del circuito ExpressRoute alla rete virtuale.

## <a name="modify-a-virtual-network-connection"></a>Modificare una connessione di rete virtuale
È possibile aggiornare alcune proprietà di una connessione di rete virtuale. 

**Per aggiornare il peso della connessione**

La rete virtuale può essere connessa a più circuiti ExpressRoute. È possibile ricevere lo stesso prefisso da più di un circuito ExpressRoute. Per scegliere la connessione per l'invio di traffico destinato per questo prefisso, è possibile modificare il valore *RoutingWeight* di una connessione. Il traffico verrà inviato sulla connessione con il valore massimo di *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

L'intervallo di *RoutingWeight* va da 0 a 32.000. Il valore predefinito è 0.

## <a name="configure-expressroute-fastpath"></a>Configurare ExpressRoute FastPath 
È possibile abilitare [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) se il gateway di rete virtuale è Prestazioni extra o ErGw3AZ. FastPath migliora le prestazioni del percorso dati come i pacchetti al secondo e le connessioni al secondo tra la rete locale e la rete virtuale. 

**Configurare FastPath in una nuova connessione**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Aggiornamento di una connessione esistente per abilitare FastPath**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="clean-up-resources"></a>Pulire le risorse

Se la connessione ExpressRoute non serve più, usare il comando `Remove-AzVirtualNetworkGatewayConnection` dalla sottoscrizione in cui si trova il the gateway per rimuovere il collegamento tra il gateway e il circuito.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection "MyConnection" -ResourceGroupName "MyRG"
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su ExpressRoute, vedere le Domande frequenti su ExpressRoute.

> [!div class="nextstepaction"]
> [Domande frequenti su ExpressRoute](expressroute-faqs.md)
