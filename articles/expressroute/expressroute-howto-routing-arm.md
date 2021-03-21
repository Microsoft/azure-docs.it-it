---
title: 'Esercitazione: Configurare il peering per un circuito ExpressRoute - Azure PowerShell'
description: Questa esercitazione illustra come creare e gestire la configurazione del routing per un circuito ExpressRoute nel modello di distribuzione di Resource Manager tramite PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 7cfd378ae621192cd98b482b66c85c3dcd3ca454
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721940"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Esercitazione: Creare e modificare il peering per un circuito ExpressRoute usando PowerShell

Questa esercitazione illustra come creare e gestire la configurazione del routing per un circuito ExpressRoute nel modello di distribuzione di Resource Manager tramite PowerShell. La procedura seguente mostra anche come controllare lo stato e aggiornare, eliminare o effettuare il deprovisioning dei peering per un circuito ExpressRoute. Se si vuole usare un metodo diverso per eseguire operazioni nel circuito, selezionare l'articolo appropriato nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Portale di Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-routing-cli.md)
> * [Peering pubblico](about-public-peering.md)
> * [Video - Peering privato](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Peering Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (classic)](expressroute-howto-routing-classic.md) (PowerShell (classico))
> 

Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (in genere un IPVPN, come MPLS), il provider di connettività configurerà e gestirà il routing per conto dell'utente.

> [!IMPORTANT]
> Attualmente non vengono annunciati peering configurati da provider di servizi tramite il portale di gestione del servizio. L'abilitazione di questa funzionalità sarà presto disponibile. Rivolgersi al provider di servizi prima di configurare peering BGP.
> 

È possibile configurare il peering privato e il peering Microsoft per un circuito ExpressRoute (il peering pubblico di Azure è deprecato per i nuovi circuiti). I peering possono essere configurati nell'ordine che si preferisce. assicurandosi, tuttavia, di completare la configurazione di un peering per volta. Per altre informazioni sul routing dei domini e il peering , vedere [Domini di routing ExpressRoute](expressroute-circuit-peerings.md). Per informazioni sul peering pubblico, vedere [Creare e gestire il peering pubblico di ExpressRoute](about-public-peering.md).

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> - Configurare, aggiornare ed eliminare il peering Microsoft per un circuito
> - Configurare, aggiornare ed eliminare il peering privato di Azure per un circuito

## <a name="prerequisites"></a>Prerequisiti

* Prima di iniziare la configurazione, rivedere le pagine seguenti:
    * [Prerequisiti](expressroute-prerequisites.md) 
    * [Requisiti di routing](expressroute-routing.md)
    * [Flussi di lavoro](expressroute-workflows.md)
* È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e chiedere al provider di connettività di abilitarlo prima di continuare. Per poter eseguire i cmdlet descritti in questo articolo, deve essere stato effettuato il provisioning del circuito ExpressRoute e il circuito deve essere nello stato abilitato.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Peering Microsoft

Questa sezione consente di creare, ottenere, aggiornare ed eliminare la configurazione del peering Microsoft per un circuito ExpressRoute.

> [!IMPORTANT]
> Per i circuiti ExpressRoute configurati prima del 1 agosto 2017 tutti i prefissi di servizio saranno annunciati tramite il peering Microsoft, anche se non sono definiti i filtri di route. Il peering Microsoft dei circuiti ExpressRoute che vengono configurati dopo il 1° agosto 2017 non avrà alcun prefisso annunciato fino a quando non viene associato un filtro di route per il circuito. Per altre informazioni, vedere [Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md) (Configurare un filtro di route per il peering Microsoft).
> 

### <a name="to-create-microsoft-peering"></a>Per creare il peering Microsoft

1. Accedere e selezionare la sottoscrizione.

   Se PowerShell è installato in locale, eseguire l'accesso. Se si usa Azure Cloud Shell, è possibile ignorare questo passaggio.

   ```azurepowershell
   Connect-AzAccount
   ```

   Selezionare la sottoscrizione desiderata per creare il circuito ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Creare un circuito ExpressRoute.

   Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di abilitare il peering Microsoft per l'utente. Non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, una volta creato il circuito proseguire la configurazione seguendo questa procedura. 

3. Controllare che il circuito ExpressRoute sia nello stato di provisioning eseguito e abilitato. Usare l'esempio seguente:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   La risposta restituita è simile all'esempio seguente:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Configurare il peering Microsoft per il circuito. Prima di continuare, verificare di avere le informazioni seguenti.

   * Una subnet /30 o /126 per il collegamento primario. Il blocco di indirizzi deve essere un prefisso IPv4 o IPv6 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Una subnet /30 o /126 per il collegamento secondario. Il blocco di indirizzi deve essere un prefisso IPv4 o IPv6 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * Prefissi annunciati: fornire un elenco di tutti i prefissi che si intende annunciare nella sessione BGP. Sono accettati solo prefissi di indirizzi IP pubblici. Se si intende inviare un set di prefissi, è possibile creare un elenco delimitato da virgole. Questi prefissi devono essere intestati all'utente in un registro RIR o IRR. Le sessioni BGP IPv4 richiedono prefissi IPv4 annunciati e le sessioni BGP IPv6 richiedono prefissi IPv6 annunciati. 
   * Routing Registry Name: è possibile specificare il registro RIR/IRR in cui sono registrati il numero AS e i prefissi.
   * Facoltativo:
     * ASN cliente: se si annunciano prefissi non registrati con l'ASN del peering, è possibile specificare il numero ASN con cui sono registrati.
     * Un hash MD5, se si sceglie di usarne uno.

> [!IMPORTANT]
> Microsoft verifica se all'utente sono assegnati "Prefissi pubblici annunciati" e "ASN peer" (o "ASN cliente") nel registro di sistema di routing Internet. Se si ricevono prefissi pubblici da un'altra entità e l'assegnazione non viene registrata con il registro di sistema di routing, la convalida automatica non verrà completata e sarà richiesta la convalida manuale. Se la convalida automatica non riesce, si vedrà 'AdvertisedPublicPrefixesState' impostato su 'Convalida necessaria' nell'output di "Get-AzExpressRouteCircuitPeeringConfig" (vedere "Ottenere i dettagli del peering Microsoft" nella sezione seguente). 
> 
> Se viene visualizzato il messaggio 'Convalida necessaria', raccogliere i documenti che mostrano che i prefissi pubblici sono assegnati all'organizzazione dall'entità elencata come proprietaria dei prefissi nel registro di routing, quindi inviarli per la convalida manuale aprendo un ticket di supporto. 
> 

   Per configurare il peering Microsoft per il circuito, applicare l'esempio seguente:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Ottenere i dettagli del peering Microsoft

Per ottenere i dettagli di configurazione, usare l'esempio seguente:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Per aggiornare la configurazione del peering Microsoft

Per aggiornare qualsiasi parte della configurazione, applicare l'esempio seguente:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Peering privato di Azure

Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering privato di Azure per un circuito ExpressRoute.

> [!IMPORTANT]
> Il supporto IPv6 per il peering privato è attualmente disponibile in **anteprima pubblica**. 
> 
> 

### <a name="to-create-azure-private-peering"></a>Per creare un peering privato di Azure

1. Importare il modulo PowerShell per ExpressRoute.

   Installare il programma di installazione di PowerShell più recente da [PowerShell Gallery](https://www.powershellgallery.com/). Importare quindi i moduli di Azure Resource Manager nella sessione di PowerShell per iniziare a usare i cmdlet per ExpressRoute. Sarà necessario eseguire PowerShell come amministratore.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importare tutti i moduli Az.\* nell'intervallo noto delle versioni semantiche.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   È possibile anche importare un solo modulo nell'intervallo noto delle versioni semantiche.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Accedere al proprio account.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Selezionare la sottoscrizione desiderata per creare il circuito ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Creare un circuito ExpressRoute.

   Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure per l'utente. Non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, una volta creato il circuito proseguire la configurazione seguendo questa procedura.

3. Controllare che il circuito ExpressRoute sia nello stato di provisioning eseguito e abilitato. Usare l'esempio seguente:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   La risposta restituita è simile all'esempio seguente:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Configurare il peering privato di Azure per il circuito. Prima di continuare con i passaggi successivi, verificare di avere gli elementi seguenti:

   * Coppia di subnet che non fanno parte di alcuno spazio indirizzi riservato per le reti virtuali. Una subnet verrà usata per il collegamento primario e l'altra per il collegamento secondario. Da ognuna di queste subnet si assegnerà al router il primo indirizzo IP utilizzabile, poiché il secondo indirizzo IP utilizzabile per il router viene usato da Microsoft. Sono disponibili tre opzioni per questa coppia di subnet:
       * IPv4: due subnet/30.
       * IPv6: due subnet/126.
       * Entrambi: due subnet/30 e due subnet/126.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte. È possibile usare il numero AS privato per questo peering. Assicurarsi di non usare 65515.
   * Facoltativo:
     * Un hash MD5, se si sceglie di usarne uno.

   Per configurare il peering privato di Azure per il circuito, usare l'esempio seguente:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se si sceglie di usare un hash MD5, usare l'esempio seguente:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6 -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Assicurarsi di specificare il numero AS come ASN di peering e non come ASN cliente.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Per visualizzare i dettagli relativi al peering privato di Azure

Per ottenere i dettagli di configurazione, usare l'esempio seguente:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Per aggiornare la configurazione del peering privato di Azure

Per aggiornare qualsiasi parte della configurazione, usare l'esempio seguente. In questo esempio, l'ID VLAN del circuito viene aggiornato da 200 a 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 500

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Pulire le risorse

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Per eliminare il peering Microsoft

Per rimuovere la configurazione di peering, eseguire il cmdlet seguente:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Per eliminare un peering privato di Azure

Per rimuovere la configurazione di peering, eseguire l'esempio seguente:

> [!WARNING]
> Prima di eseguire questo esempio, verificare che tutte le reti virtuali e le connessioni di Copertura globale di ExpressRoute siano state rimosse. 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Passaggi successivi

Dopo la configurazione del peering privato di Azure, è possibile creare un gateway ExpressRoute per collegare una rete virtuale al circuito. 

> [!div class="nextstepaction"]
> [Configurare un gateway di rete virtuale per ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
