---
title: Routing del traffico di rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni sul modo in cui Azure instrada il traffico di rete virtuale e su come personalizzare il routing di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2021
ms.author: aldomel
ms.openlocfilehash: c8d188c7bb7034cda450049c3d4912cc1517dae5
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645273"
---
# <a name="virtual-network-traffic-routing"></a>Routing del traffico di rete virtuale

Informazioni sul modo in cui Azure instrada il traffico tra Azure, l'ambiente locale e le risorse Internet. Azure crea automaticamente una tabella di route per ogni subnet all'interno di una rete virtuale di Azure e aggiunge le route predefinite di sistema alla tabella. Per altre informazioni sulle reti virtuali e sulle subnet, vedere la [panoramica della rete virtuale](virtual-networks-overview.md). È possibile eseguire l'override di alcune delle route di sistema di Azure usando [route personalizzate](#custom-routes) e aggiungere altre route personalizzate alle tabelle di route. Azure instrada il traffico in uscita da una subnet in base alle route della tabella di route di una subnet.

## <a name="system-routes"></a>Route di sistema

Azure crea automaticamente route di sistema e assegna le route a ogni subnet in una rete virtuale. Non è possibile creare route di sistema né rimuoverle, ma è possibile eseguire l'override di alcune route di sistema usando [route personalizzate](#custom-routes). Azure crea route di sistema predefinite per ogni subnet e aggiunge altre [route predefinite facoltative](#optional-default-routes) a subnet specifiche, oppure a ogni subnet, quando si usano funzionalità specifiche di Azure.

### <a name="default"></a>Predefinito

Ogni route contiene un prefisso degli indirizzi e il tipo di hop successivo. Quando il traffico in uscita da una subnet viene inviato a un indirizzo IP compreso nel prefisso degli indirizzi di una route, la route che contiene il prefisso è quella usata da Azure. Vedere in che modo [Azure seleziona una route](#how-azure-selects-a-route) quando più route contengono lo stesso prefisso oppure prefissi che si sovrappongono. Quando si crea una rete virtuale, Azure crea automaticamente le route di sistema predefinite seguenti per ogni subnet della rete virtuale:

|Source (Sorgente) |Prefissi degli indirizzi                                        |Tipo hop successivo  |
|-------|---------                                               |---------      |
|Predefinito|Univoco per la rete virtuale                           |Rete virtuale|
|Predefinito|0.0.0.0/0                                               |Internet       |
|Predefinito|10.0.0.0/8                                              |nessuno           |
|Predefinito|192.168.0.0/16                                          |nessuno           |
|Predefinito|100.64.0.0/10                                           |nessuno           |

I tipi di hop successivi elencati nella tabella precedente rappresentano il modo in cui Azure instrada il traffico destinato al prefisso degli indirizzi elencato. Seguono le spiegazioni per i tipi di hop successivi:

* **Rete virtuale**: instrada il traffico tra gli intervalli di indirizzi all'interno dello [spazio di indirizzi](manage-virtual-network.md#add-or-remove-an-address-range) di una rete virtuale. Azure crea una route con un prefisso degli indirizzi che corrisponde a ogni intervallo di indirizzi definito nello spazio di indirizzi di una rete virtuale. Se per lo spazio di indirizzi della rete virtuale sono stati definiti più intervalli di indirizzi, Azure crea una route per ogni intervallo di indirizzi. Azure effettua il routing automatico del traffico tra le subnet usando le route create per ogni intervallo di indirizzi. Non è necessario definire gateway per consentire ad Azure di instradare il traffico tra subnet. Anche se una rete virtuale contiene subnet e ogni subnet ha un intervallo di indirizzi definito, Azure *non* crea route predefinite per gli intervalli di indirizzi della subnet, perché ogni intervallo di indirizzi della subnet si trova all'interno di un intervallo di indirizzi dello spazio di indirizzi di una rete virtuale.<br>
* **Internet**: instrada verso Internet il traffico specificato dal prefisso degli indirizzi. La route di sistema predefinita specifica il prefisso degli indirizzi 0.0.0.0/0. Se non si sostituiscono le route predefinite, Azure instrada verso Internet il traffico di tutti gli indirizzi non specificati da un intervallo di indirizzi all'interno di una rete virtuale, con un'eccezione. Se l'indirizzo di destinazione è di uno dei servizi di Azure, Azure instrada il traffico direttamente al servizio tramite la rete backbone di Azure, piuttosto che verso Internet. Il traffico tra i servizi di Azure non attraversa Internet, indipendentemente dall'area di Azure in cui si trova la rete virtuale o in cui viene distribuita un'istanza del servizio di Azure. È possibile eseguire l'override della route di sistema predefinita di Azure per il prefisso degli indirizzi 0.0.0.0/0 con una [route personalizzata](#custom-routes).<br>
* **Nessuna**: il traffico indirizzato al tipo di hop successivo **Nessuno** viene eliminato e non instradato all'esterno della subnet. Azure crea automaticamente route predefinite per i prefissi degli indirizzi seguenti:<br>

    * **10.0.0.0/8 e 192.168.0.0/16**: riservati per l'uso privato in RFC 1918.<br>
    * **100.64.0.0/10**: riservato in RFC 6598.

    Se si assegnano gli intervalli di indirizzi precedenti nello spazio degli indirizzi di una rete virtuale, Azure modifica automaticamente il tipo di hop successivo della route da **Nessuno** a **Rete virtuale**. Se si assegna un intervallo di indirizzi allo spazio degli indirizzi di una rete virtuale che include (ma non è uguale a) uno dei quattro prefissi degli indirizzi riservati, Azure rimuove la route per il prefisso e aggiunge una route per il prefisso degli indirizzi aggiunto, con **Rete virtuale** come tipo di hop successivo.

### <a name="optional-default-routes"></a>Route predefinite facoltative

Azure aggiunge route di sistema predefinite per diverse funzionalità di Azure, ma solo se si abilitano queste funzionalità. A seconda della funzionalità, Azure aggiunge route predefinite facoltative a subnet specifiche o a tutte le subnet della rete virtuale. Le route di sistema aggiuntive e i tipi di hop successivi che Azure può aggiungere quando si abilitano le funzionalità sono:

|Source (Sorgente)                 |Prefissi degli indirizzi                       |Tipo hop successivo|Subnet nella rete virtuale alla quale viene aggiunta la route|
|-----                  |----                                   |---------                    |--------|
|Predefinito                |Univoco per la rete virtuale, ad esempio: 10.1.0.0/16|Peering reti virtuali                 |Tutti|
|Gateway di rete virtuale|Prefissi annunciati dall'ambiente locale tramite BGP o configurati nel gateway di rete locale     |Gateway di rete virtuale      |Tutti|
|Predefinito                |Multipli                               |VirtualNetworkServiceEndpoint|Solo la subnet per la quale è abilitato un endpoint di servizio.|

* **Peering di rete virtuale**: quando si crea un peering di rete virtuale tra due reti virtuali, viene aggiunta una route per ogni intervallo di indirizzi nello spazio degli indirizzi di ogni rete virtuale per la quale viene creato un peering. Vedere altre informazioni sul [peering di rete virtuale](virtual-network-peering-overview.md).<br>
* **Gateway di rete virtuale**: vengono aggiunte una o più route con *Gateway di rete virtuale* elencato come tipo di hop successivo quando si aggiunge un gateway di rete virtuale a una rete virtuale. Anche l'origine è *Gateway di rete virtuale*, perché il gateway aggiunge le route alla subnet. Se il gateway di rete locale scambia route con un gateway di rete virtuale di Azure tramite Border Gateway Protocol [(BGP)](#border-gateway-protocol), viene aggiunta una route per ogni route propagata dal gateway di rete locale. È consigliabile sintetizzare le route locali negli intervalli di indirizzi più ampi possibile, in modo che venga propagato il minor numero possibile di route a un gateway di rete virtuale di Azure. Il numero di route che possono essere propagate a un gateway di rete virtuale di Azure è limitato. Per informazioni dettagliate, vedere [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).<br>
* **VirtualNetworkServiceEndpoint**: Azure aggiunge gli indirizzi IP pubblici per alcuni servizi alla tabella di route quando si abilita un endpoint di servizio per il servizio. Gli endpoint di servizio vengono abilitati per singole subnet all'interno di una rete virtuale, quindi la route viene aggiunta solo alla tabella di route di una subnet per la quale è abilitato un endpoint di servizio. Gli indirizzi IP pubblici dei servizi di Azure cambiano periodicamente. Azure gestisce automaticamente gli indirizzi nella tabella di route quando gli indirizzi cambiano. Vedere altre informazioni sugli [endpoint servizio di rete virtuale](virtual-network-service-endpoints-overview.md) e sui servizi per i quali è possibile creare endpoint di servizio.<br>

    > [!NOTE]
    > I tipi di hop successivi **Peering di rete virtuale** e **VirtualNetworkServiceEndpoint** vengono aggiunti solo alle tabelle di route delle subnet che si trovano nelle reti virtuali create tramite il modello di distribuzione Azure Resource Manager. I tipi di hop successivi non vengono aggiunti alle tabelle di route associate a subnet di reti virtuali create tramite il modello di distribuzione classica. Vedere altre informazioni sui [modelli di distribuzione](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure.

## <a name="custom-routes"></a>Route personalizzate

Le route personalizzate vengono create con route [definite dall'utente](#user-defined) oppure scambiando route tra un gateway di rete locale e un gateway di rete virtuale di Azure tramite [Border Gateway Protocol](#border-gateway-protocol) (BGP). 

### <a name="user-defined"></a>Route definite dall'utente

È possibile creare route personalizzate o definite dall'utente (statiche) in Azure per eseguire l'override delle route di sistema predefinite di Azure o per aggiungere altre route alla tabella di route di una subnet. In Azure si crea una tabella di route, quindi la si associa a zero o più subnet della rete virtuale. A ogni subnet può essere associata una o nessuna tabella di route. Vedere [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) per informazioni sul numero massimo di route che possono essere aggiunte a una tabella di route e sul numero massimo di tabelle di route definite dall'utente che possono essere create per ogni sottoscrizione di Azure. Se si crea una tabella di route e la si associa a una subnet, le route nella tabella vengono combinate con (oppure sostituiscono) le route predefinite aggiunte da Azure a una subnet.

È possibile specificare i seguenti tipi di hop successivi durante la creazione di una route definita dall'utente:

* **Appliance virtuale**: un'appliance virtuale è una macchina virtuale che generalmente esegue un'applicazione di rete, ad esempio un firewall. Per informazioni su una serie di appliance virtuali di rete preconfigurate distribuibili in una rete virtuale, vedere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Quando si crea una route con il tipo hop **appliance virtuale**, si specifica anche un indirizzo IP hop successivo. L'indirizzo IP può essere:

    * L'[indirizzo IP privato](./private-ip-addresses.md) di un'interfaccia di rete collegata a una macchina virtuale. Per un'interfaccia di rete collegata a una macchina virtuale che inoltra il traffico di rete a un indirizzo diverso dal proprio è necessario abilitare l'opzione di *inoltro IP* di Azure. Questa impostazione disabilita il controllo di origine e destinazione di Azure per l'interfaccia di rete. Vedere altre informazioni su come [abilitare l'inoltro IP per un'interfaccia di rete](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Anche se l'*abilitazione dell'inoltro IP* è un'impostazione di Azure, può essere necessario abilitare l'inoltro IP anche nel sistema operativo della macchina virtuale affinché l'appliance inoltri il traffico tra gli indirizzi IP assegnati alle interfacce di rete di Azure. Se l'appliance deve instradare il traffico a un indirizzo IP pubblico, deve usare un proxy per il traffico o convertire con NAT (Network Address Translation) l'indirizzo IP privato dell'origine nel proprio indirizzo IP privato, che Azure convertirà quindi con NAT in un indirizzo IP pubblico prima di inviare il traffico in Internet. Per determinare le impostazioni necessarie nella macchina virtuale, vedere la documentazione del sistema operativo o dell'applicazione di rete. Per informazioni sulle connessioni in uscita, vedere [Informazioni sulle connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).<br>

        > [!NOTE]
        > Distribuire un'appliance virtuale in una subnet diversa da quella in cui sono distribuite le risorse instradate attraverso l'appliance stessa. La distribuzione di un'appliance virtuale nella stessa subnet, con la successiva applicazione di una tabella di route alla subnet che instrada il traffico attraverso l'appliance, può comportare loop di routing che impediscono al traffico di uscire dalla subnet.

    * L'indirizzo IP privato di un [servizio di bilanciamento del carico interno di Azure](../load-balancer/quickstart-load-balancer-standard-internal-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Un servizio di bilanciamento del carico viene spesso usato nell'ambito di una [strategia di disponibilità elevata per le appliance virtuali di rete](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).

    È possibile definire una route con 0.0.0.0/0 come prefisso degli indirizzi e un tipo di hop successivo Appliance virtuale, per consentire all'appliance di ispezionare il traffico e determinare se inoltrarlo o eliminarlo. Se si intende creare una route definita dall'utente che contiene il prefisso degli indirizzi 0.0.0.0/0, vedere prima [Prefisso degli indirizzi 0.0.0.0/0](#default-route).

* **Gateway di rete virtuale**: consente di specificare quando si vuole che il traffico destinato a prefissi degli indirizzi specifici venga indirizzato a un gateway di rete virtuale. Il gateway di rete virtuale deve essere creato con il tipo **VPN**. Non è possibile specificare un gateway di rete virtuale creato come tipo **ExpressRoute** in una route definita dall'utente perché con ExpressRoute è necessario usare BGP per le route personalizzate. È possibile definire una route che indirizzi il traffico destinato al prefisso degli indirizzi 0.0.0.0/0 a un gateway di rete virtuale [basato su route](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype). Nell'ambiente locale può essere presente un dispositivo che ispeziona il traffico e determina se inoltrarlo o eliminarlo. Se si intende creare una route definita dall'utente per il prefisso di indirizzo 0.0.0.0/0, vedere prima [Prefisso degli indirizzi 0.0.0.0/0](#default-route). Invece di configurare una route definita dall'utente per il prefisso degli indirizzi 0.0.0.0/0, è possibile annunciare una route con il prefisso 0.0.0.0/0 tramite BGP, se è stato [abilitato BGP per un gateway di rete virtuale VPN](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).<br>
* **Nessuna**: consente di specificare quando si vuole eliminare il traffico verso un prefisso degli indirizzi, invece di inoltrarlo a una destinazione. Se una funzionalità non è completamente configurata, Azure può elencare *Nessuno* per alcune delle route di sistema facoltative. Se ad esempio è indicato *Nessuno* come **Indirizzo IP hop successivo** con **Tipo hop successivo***Gateway di rete virtuale* o *Appliance virtuale*, è possibile che il dispositivo non sia in esecuzione o non sia completamente configurato. Azure crea [route predefinite](#default) di sistema per i prefissi degli indirizzi riservati con tipo hop successivo **Nessuno**.<br>
* **Rete virtuale**: consente di specificare quando si intende eseguire l'override del routing predefinito in una rete virtuale. Vedere [Esempio di routing](#routing-example) per un esempio che illustra perché creare una route con il tipo di hop **Rete virtuale**.<br>
* **Internet**: consente di specificare quando si vuole instradare esplicitamente a Internet il traffico destinato a un prefisso degli indirizzi oppure se si vuole che il traffico destinato ai servizi di Azure con indirizzi IP pubblici resti all'interno della rete backbone di Azure.

Non è possibile specificare **Peering di rete virtuale** o **VirtualNetworkServiceEndpoint** come tipo di hop successivo nelle route definite dall'utente. Le route con tipi di hop successivi **Peering di rete virtuale** o **VirtualNetworkServiceEndpoint** vengono create da Azure solo quando si configura un peering di rete virtuale oppure un endpoint di servizio.

### <a name="service-tags-for-user-defined-routes-preview"></a>Tag di servizio per le route definite dall'utente (anteprima)

È ora possibile specificare un [tag di servizio](service-tags-overview.md) come prefisso dell'indirizzo per una route definita dall'utente anziché un intervallo IP esplicito. Un tag di servizio rappresenta un gruppo di prefissi di indirizzi IP da un determinato servizio di Azure. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi, riducendo al minimo la complessità degli aggiornamenti frequenti alle route definite dall'utente e riducendo il numero di route che è necessario creare. È attualmente possibile creare 25 o meno route con tag di servizio in ogni tabella di route. </br>

> [!IMPORTANT]
> I tag di servizio per le route definite dall'utente sono attualmente in anteprima. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

#### <a name="exact-match"></a>Corrispondenza esatta
Quando esiste una corrispondenza esatta con prefisso tra una route con un prefisso IP esplicito e una route con un tag di servizio, la preferenza viene assegnata alla route con il prefisso esplicito. Quando più route con tag di servizio hanno prefissi IP corrispondenti, le route verranno valutate nell'ordine seguente: 

   1. Tag a livello di area (ad esempio Storage. Eastus, AppService. AustraliaCentral)
   2. Tag di primo livello (ad esempio Archiviazione, AppService)
   3. Tag internazionali di AzureCloud (ad esempio AzureCloud. canadacentral, AzureCloud. eastasia)
   4. Tag AzureCloud </br></br>

Per usare questa funzionalità, specificare un nome di tag di servizio per il parametro del prefisso dell'indirizzo nei comandi della tabella di route. Ad esempio, in PowerShell è possibile creare una nuova route per indirizzare il traffico inviato a un prefisso IP di archiviazione di Azure a un'appliance virtuale usando: </br>

```azurepowershell-interactive
New-AzRouteConfig -Name "StorageRoute" -AddressPrefix “Storage” -NextHopType "VirtualAppliance" -NextHopIpAddress "10.0.100.4"
```

Lo stesso comando per l'interfaccia della riga di comando sarà: </br>

```azurecli-interactive
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n StorageRoute --address-prefix Storage --next-hop-type VirtualAppliance --next-hop-ip-address 10.0.100.4
```
</br>


> [!NOTE] 
> Durante l'anteprima pubblica, esistono diverse limitazioni. La funzionalità non è attualmente supportata nel portale di Azure ed è disponibile solo tramite PowerShell e l'interfaccia della riga di comando. Non è disponibile alcun supporto per l'uso con i contenitori. 

## <a name="next-hop-types-across-azure-tools"></a>Tipi di hop successivi tra gli strumenti di Azure

Il nome visualizzato e a cui si fa riferimento per i tipi di hop successivi è diverso tra il portale di Azure e gli strumenti da riga di comando e tra i modelli di distribuzione Azure Resource Manager e classica. La tabella seguente elenca i nomi usati per fare riferimento a ogni tipo di hop successivo nei diversi strumenti e [modelli di distribuzione](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json):

|Tipo hop successivo                   |Interfaccia della riga di comando di Azure e PowerShell (Resource Manager) |Interfaccia della riga di comando classica di Azure e PowerShell (classica)|
|-------------                   |---------                                       |-----|
|Gateway di rete virtuale         |VirtualNetworkGateway                           |VPNGateway|
|Rete virtuale                 |VNetLocal                                       |VNETLocal (non disponibile nell'interfaccia della riga di comando classica in modalità asm)|
|Internet                        |Internet                                        |Internet (non disponibile nell'interfaccia della riga di comando classica in modalità asm)|
|Appliance virtuale               |VirtualAppliance                                |VirtualAppliance|
|nessuno                            |nessuno                                            |Null (non disponibile nell'interfaccia della riga di comando classica in modalità asm)|
|Peering di rete virtuale         |Peering reti virtuali                                    |Non applicabile|
|Endpoint servizio di rete virtuale|VirtualNetworkServiceEndpoint                   |Non applicabile|



### <a name="border-gateway-protocol"></a>Border Gateway Protocol

Un gateway di rete locale può scambiare le route con un gateway di rete virtuale di Azure tramite Border Gateway Protocol (BGP). L'uso di BGP con un gateway di rete virtuale di Azure dipende dal tipo selezionato al momento della creazione del gateway stesso. Se il tipo selezionato era:

* **ExpressRoute**: è necessario usare BGP per annunciare le route locali al router Microsoft Edge. Se si distribuisce un gateway di rete virtuale di tipo ExpressRoute, non è possibile creare route definite dall'utente per forzare il traffico verso il gateway di rete virtuale ExpressRoute. È possibile usare route definite dall'utente per forzare il traffico da ExpressRoute, ad esempio verso un'appliance virtuale di rete.<br>
* **VPN**: è possibile usare BGP. Per informazioni dettagliate, vedere [BGP con connessioni VPN da sito a sito](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Quando si scambiano le route con Azure tramite BGP, viene aggiunta una route separata alla tabella di route di tutte le subnet di una rete virtuale per ogni prefisso annunciato. La route viene aggiunta con *Gateway di rete virtuale* come origine e tipo di hop successivo. 

È possibile disabilitare la propagazione delle route ER e Gateway VPN su una subnet mediante una proprietà in una tabella di route. Quando si scambiano le route con Azure usando BGP, le route non vengono aggiunte alla tabella di route di tutte le subnet con propagazione della route del gateway di rete virtuale disabilitata. La connettività con connessioni VPN è ottenuta usando [route personalizzate](#custom-routes) con *gateway di rete virtuale* come tipo di hop successivo. **La propagazione della route non deve essere disabilitata in GatewaySubnet. Il gateway non funzionerà con questa impostazione disabilitata.** Per maggiori dettagli, vedere le indicazioni su [come disabilitare la propagazione della route del gateway di rete virtuale](manage-route-table.md#create-a-route-table).

## <a name="how-azure-selects-a-route"></a>Modalità di selezione di una route da parte di Azure

Quando il traffico in uscita viene inviato da una subnet, Azure seleziona una route in base all'indirizzo IP di destinazione, usando l'algoritmo di corrispondenza del prefisso più lungo. Ad esempio, una tabella di route ha due route: una specifica il prefisso degli indirizzi 10.0.0.0/24, l'altra specifica il prefisso degli indirizzi 10.0.0.0/16. Azure instrada il traffico destinato a 10.0.0.5 al tipo di hop successivo specificato nella route con il prefisso degli indirizzi 10.0.0.0/24, perché 10.0.0.0/24 è un prefisso più lungo di 10.0.0.0/16, anche se 10.0.0.5 è compreso in entrambi i prefissi degli indirizzi. Azure instrada il traffico destinato a 10.0.1.5 al tipo di hop successivo specificato nella route con il prefisso degli indirizzi 10.0.0.0/16, perché 10.0.1.5 non è incluso nel prefisso degli indirizzi 10.0.0.0/24 e quindi la route con il prefisso degli indirizzi 10.0.0.0/16 è il prefisso di corrispondenza più lungo.

Se più route contengono lo stesso prefisso degli indirizzi, Azure seleziona il tipo di route in base alla priorità seguente:

1. Route definita dall'utente
1. Route BGP
1. Route di sistema

> [!NOTE]
> Le route di sistema per il traffico correlato alla rete virtuale, ai peering di rete virtuale o agli endpoint servizio di rete virtuale sono le route preferite anche se le route BGP sono più specifiche.

Una tabella di route contiene ad esempio le route seguenti:


|Source (Sorgente)   |Prefissi degli indirizzi  |Tipo hop successivo           |
|---------|---------         |-------                 |
|Predefinito  | 0.0.0.0/0        |Internet                |
|Utente     | 0.0.0.0/0        |Gateway di rete virtuale |

Quando il traffico è destinato a un indirizzo IP non compreso nei prefissi degli indirizzi di qualsiasi altra route presente nella tabella di route, Azure seleziona la route con l'origine **Utente**, perché le route definite dall'utente hanno una priorità più elevata delle route predefinite di sistema.

Vedere [Esempio di routing](#routing-example) per una tabella di route completa con la spiegazione delle route contenute.

## <a name="00000-address-prefix"></a><a name="default-route"></a>Prefisso degli indirizzi 0.0.0.0/0

Una route con prefisso degli indirizzi 0.0.0.0/0 indica ad Azure come instradare il traffico destinato a un indirizzo IP che non è incluso nel prefisso degli indirizzi di qualsiasi altra route presente nella tabella di route della subnet. Quando si crea una subnet, Azure crea una route [predefinita](#default) per il prefisso degli indirizzi 0.0.0.0/0, con tipo di hop successivo **Internet**. Se non si esegue l'override di questa route, Azure instrada a Internet tutto il traffico destinato agli indirizzi IP non inclusi nel prefisso degli indirizzi di qualsiasi altra route. L'eccezione è data dal traffico verso gli indirizzi IP pubblici dei servizi di Azure, che rimane nella rete backbone di Azure e non viene instradato a Internet. Se si esegue l'override di questa route con una [route](#custom-routes) personalizzata, il traffico destinato a indirizzi non inclusi nei prefissi degli indirizzi di qualsiasi altra route presente nella tabella di route viene inviato a un'appliance virtuale di rete o a un gateway di rete virtuale, a seconda di quanto specificato nella route personalizzata.

Quando si esegue l'override del prefisso degli indirizzi 0.0.0.0/0, oltre all'instradamento del traffico in uscita dalla subnet attraverso il gateway di rete virtuale o l'appliance virtuale, si verificano le modifiche seguenti con il routing predefinito di Azure: 

* Azure invia tutto il traffico al tipo di hop successivo specificato nella route, incluso il traffico destinato agli indirizzi IP pubblici dei servizi di Azure. Quando il tipo di hop successivo per la route con il prefisso degli indirizzi 0.0.0.0/0 è **Internet**, il traffico in uscita dalla subnet destinato agli indirizzi IP pubblici dei servizi di Azure non lascia mai la rete backbone di Azure, indipendentemente dall'area di Azure in cui si trova la rete virtuale o il servizio di Azure. Quando tuttavia si crea una route definita dall'utente o BGP con un tipo di hop successivo **Gateway di rete virtuale** o **Appliance virtuale**, tutto il traffico (incluso quello inviato agli indirizzi IP pubblici dei servizi di Azure per i quali non sono stati abilitati [endpoint di servizio](virtual-network-service-endpoints-overview.md)) viene inviato al tipo di hop successivo specificato nella route. Se è stato abilitato un endpoint di servizio per un servizio, il traffico verso il servizio non viene instradato al tipo di hop successivo in una route con il prefisso degli indirizzi 0.0.0.0/0, perché i prefissi degli indirizzi per il servizio vengono specificati nella route che Azure crea quando si abilita l'endpoint di servizio e i prefissi degli indirizzi per il servizio sono più lunghi di 0.0.0.0/0.
* Non è più possibile accedere direttamente alle risorse della subnet da Internet. È possibile accedere indirettamente alle risorse della subnet da Internet, se il traffico in ingresso passa attraverso il dispositivo specificato dal tipo di hop successivo per una route con il prefisso degli indirizzi 0.0.0.0/0 prima di raggiungere la risorsa nella rete virtuale. Se la route contiene i valori seguenti per il tipo di hop successivo:<br>

    * **Appliance virtuale**: l'appliance deve:<br>

        * Essere accessibile da Internet<br>
        * Avere un indirizzo IP pubblico assegnato<br>
        * Non avere associata una regola del gruppo di sicurezza di rete che impedisca la comunicazione con il dispositivo<br>
        * Non negare la comunicazione<br>
        * Poter convertire l'indirizzo di rete, inoltrare il traffico alla risorsa di destinazione nella subnet e restituire il traffico a Internet.

    * **Gateway di rete virtuale**: se il gateway è un gateway di rete virtuale ExpressRoute, un dispositivo connesso a Internet in locale può convertire l'indirizzo di rete e inoltrare il traffico alla risorsa di destinazione nella subnet tramite [peering privato](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#privatepeering) di ExpressRoute. 

Se la rete virtuale è connessa a un gateway VPN di Azure, non associare una tabella di route alla [subnet del gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) che include una route con una destinazione 0.0.0.0/0. Ciò potrebbe impedire il corretto funzionamento del gateway. Per informazioni dettagliate, vedere la domanda *Perché determinate porte sono aperte nella mia gateway VPN?* nelle [Domande frequenti sul gateway VPN](../vpn-gateway/vpn-gateway-vpn-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gatewayports).

Vedere [Rete perimetrale tra Azure e il data center locale](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) per i dettagli di implementazione quando si usano i gateway di rete virtuale tra Internet e Azure.

## <a name="routing-example"></a>Esempio di routing

Per illustrare i concetti presentati in questo articolo, le sezioni seguenti descrivono:

* Uno scenario con requisiti<br>
* Le route personalizzate necessarie per soddisfare i requisiti<br>
* La tabella di route presente per una subnet che include le route predefinite e personalizzate necessarie per soddisfare i requisiti

> [!NOTE]
> Questo esempio non rappresenta l'implementazione consigliata. Viene fornito solo per illustrare i concetti di questo articolo.

### <a name="requirements"></a>Requisiti

1. Implementare due reti virtuali nella stessa area di Azure e consentire alle risorse di comunicare tra le reti virtuali.
1. Consentire a una rete locale di comunicare in modo sicuro con entrambe le reti virtuali tramite un tunnel VPN su Internet. *In alternativa è possibile usare una connessione ExpressRoute, ma in questo esempio viene usata una connessione VPN.*
1. Per una sola subnet in una sola rete virtuale:

    * Forzare tutto il traffico in uscita dalla subnet, ad eccezione di Archiviazione di Azure e del traffico interno alla subnet, attraverso un'appliance virtuale di rete per l'ispezione e la registrazione.<br>
    * Non ispezionare il traffico tra gli indirizzi IP privati all'interno della subnet; consentire il flusso del traffico direttamente tra tutte le risorse.<br>
    * Eliminare tutto il traffico in uscita destinato all'altra rete virtuale.<br>
    * Consentire al traffico in uscita verso Archiviazione di Azure di raggiungere direttamente l'archivio, senza forzarlo attraverso un'appliance virtuale di rete.

1. Consentire tutto il traffico tra tutte le altre subnet e reti virtuali.

### <a name="implementation"></a>Implementazione

La figura seguente illustra un'implementazione tramite il modello di distribuzione Azure Resource Manager che soddisfa i requisiti precedenti:

![Diagramma di rete](./media/virtual-networks-udr-overview/routing-example.png)

Le frecce indicano il flusso del traffico. 

### <a name="route-tables"></a>Tabelle di route

#### <a name="subnet1"></a>Subnet1

La tabella di route per *Subnet1* rappresentata nell'immagine contiene le route seguenti:

|ID  |Source (Sorgente) |State  |Prefissi degli indirizzi    |Tipo hop successivo          |Indirizzo IP hop successivo|Nome route definita dall'utente| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1   |Predefinito|Non valido|10.0.0.0/16         |Rete virtuale        |                   |              |
|2   |Utente   |Attivo |10.0.0.0/16         |Appliance virtuale      |10.0.100.4         |Within-VNet1  |
|3   |Utente   |Attivo |10.0.0.0/24         |Rete virtuale        |                   |Within-Subnet1|
|4   |Predefinito|Non valido|10.1.0.0/16         |Peering reti virtuali           |                   |              |
|5   |Predefinito|Non valido|10.2.0.0/16         |Peering reti virtuali           |                   |              |
|6   |Utente   |Attivo |10.1.0.0/16         |nessuno                   |                   |ToVNet2-1-Drop|
|7   |Utente   |Attivo |10.2.0.0/16         |nessuno                   |                   |ToVNet2-2-Drop|
|8   |Predefinito|Non valido|10.10.0.0/16        |Gateway di rete virtuale|[X.X.X.X]          |              |
|9   |Utente   |Attivo |10.10.0.0/16        |Appliance virtuale      |10.0.100.4         |To-On-Prem    |
|10  |Predefinito|Attivo |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Predefinito|Non valido|0.0.0.0/0           |Internet               |                   |              |
|12  |Utente   |Attivo |0.0.0.0/0           |Appliance virtuale      |10.0.100.4         |Default-NVA   |

Di seguito è riportata la spiegazione di ogni ID route:

1. Azure ha aggiunto automaticamente questa route a tutte le subnet di *Virtual-network-1* perché 10.0.0.0/16 è l'unico intervallo di indirizzi definito nello spazio degli indirizzi per la rete virtuale. Se la route ID2 definita dall'utente non venisse creata, il traffico inviato agli indirizzi compresi tra 10.0.0.1 e 10.0.255.254 verrebbe instradato all'interno della rete virtuale, perché il prefisso è più lungo di 0.0.0.0/0 e non rientra nei prefissi degli indirizzi delle altre route. Azure ha modificato automaticamente lo stato da *Attivo* a *Non valido* quando è stata aggiunta la route ID2 definita dall'utente perché la route ha lo stesso prefisso della route predefinita e le route definite dall'utente sostituiscono le route predefinite. Lo stato della route è ancora *Attivo* per *Subnet2*, perché la tabella di route in cui si trova la route ID2 definita dall'utente non è associata a *Subnet2*.
2. Azure ha aggiunto questa route quando una route definita dall'utente per il prefisso degli indirizzi 10.0.0.0/16 è stata associata alla subnet *Subnet1* nella rete virtuale *Virtual-network-1*. La route definita dall'utente specifica 10.0.100.4 come indirizzo IP dell'appliance virtuale, perché l'indirizzo è l'indirizzo IP privato assegnato alla macchina virtuale dell'appliance virtuale. La tabella di route in cui si trova questa route non è associata a *Subnet2*, quindi non appare nella tabella di route per *Subnet2*. Questa route sostituisce la route predefinita per il prefisso 10.0.0.0/16 (ID1), che instradava automaticamente il traffico indirizzato a 10.0.0.1 e 10.0.255.254 nella rete virtuale tramite il tipo di hop successivo Rete virtuale. Questa route esiste per soddisfare il [requisito](#requirements) 3, per forzare tutto il traffico in uscita attraverso un'appliance virtuale.
3. Azure ha aggiunto questa route quando una route definita dall'utente per il prefisso degli indirizzi 10.0.0.0/24 è stata associata alla subnet *Subnet1*. Il traffico destinato agli indirizzi compresi tra 10.0.0.1 e 10.0.0.254 rimane all'interno della subnet, piuttosto che essere indirizzato all'appliance virtuale specificata nella regola precedente (ID2), perché contiene un prefisso più lungo della route ID2. Questa route non era associata a *Subnet2*, quindi non appare nella tabella di route per *Subnet2*. Questa route sostituisce la route ID2 per il traffico all'interno di *Subnet1*. Questa route esiste per soddisfare il [requisito](#requirements) 3.
4. Azure ha aggiunto automaticamente queste route ID 4 e 5 per tutte le subnet di *Virtual-network-1* quando è stato eseguito il peering della rete virtuale con *Virtual-network-2.* *Virtual-network-2* ha due intervalli di indirizzi nello spazio degli indirizzi: 10.1.0.0/16 e 10.2.0.0/16, quindi Azure ha aggiunto una route per ciascuno. Se le route ID 6 e 7 definite dall'utente non venissero create, il traffico inviato agli indirizzi compresi tra 10.1.0.1-10.1.255.254 e 10.2.0.1-10.2.255.254 verrebbe indirizzato alla rete virtuale con peering, perché il prefisso è più lungo di 0.0.0.0/0 e non rientra nei prefissi degli indirizzi delle altre route. Azure ha modificato automaticamente lo stato da *Attivo* a *Non valido* quando sono state aggiunte le route ID 6 e 7 perché hanno lo stesso prefisso delle route ID 4 e 5 e le route definite dall'utente sostituiscono quelle predefinite. Lo stato delle route ID 4 e 5 è ancora *Attivo* per *Subnet2*, perché la tabella di route in cui si trovano le route ID 6 e 7 non è associata a *Subnet2*. È stato creato un peering di rete virtuale per soddisfare il [requisito](#requirements) 1.
5. Stessa spiegazione di ID4.
6. Azure ha aggiunto questa route e la route ID7 quando sono state associate route definite dall'utente per i prefissi degli indirizzi 10.1.0.0/16 e 10.2.0.0/16 alla subnet *Subnet1*. Il traffico destinato agli indirizzi compresi tra 10.1.0.1-10.1.255.254 e 10.2.0.1-10.2.255.254 viene eliminato da Azure, e non indirizzato alla rete virtuale con peering, perché le route definite dall'utente sostituiscono le route predefinite. Queste route non sono associate a *Subnet2*, quindi non appaiono nella tabella di route per *Subnet2*. Le route sostituiscono le route ID4 e ID5 per il traffico in uscita da *Subnet1*. Le route ID6 e ID7 esistono per soddisfare il [requisito](#requirements) 3 per eliminare il traffico destinato all'altra rete virtuale.
7. Stessa spiegazione di ID6.
8. Azure ha aggiunto automaticamente questa route per tutte le subnet di *Virtual-network-1* quando è stato creato un gateway di rete virtuale di tipo VPN nella rete virtuale. Azure ha aggiunto l'indirizzo IP pubblico del gateway di rete virtuale alla tabella di route. Il traffico inviato a qualsiasi indirizzo compreso tra 10.10.0.1 e 10.10.255.254 viene indirizzato al gateway di rete virtuale. Il prefisso è più lungo di 0.0.0.0/0 e non rientra nei prefissi degli indirizzi delle altre route. È stato creato un gateway di rete virtuale per soddisfare il [requisito](#requirements) 2.
9. Azure ha aggiunto questa route quando una route definita dall'utente per il prefisso degli indirizzi 10.10.0.0/16 è stata aggiunta alla tabella di route associata alla subnet *Subnet1*. Questa route sostituisce ID8. La route invia tutto il traffico destinato alla rete locale a un'appliance virtuale di rete per l'ispezione, piuttosto che indirizzare il traffico direttamente all'ambiente locale. Questa route è stata creata per soddisfare il [requisito](#requirements) 3.
10. Azure ha aggiunto automaticamente questa route alla subnet quando è stato abilitato un endpoint di servizio per un servizio di Azure per la subnet. Azure indirizza il traffico proveniente dalla subnet a un indirizzo IP pubblico del servizio, tramite la rete dell'infrastruttura di Azure. Il prefisso è più lungo di 0.0.0.0/0 e non rientra nei prefissi degli indirizzi delle altre route. Un endpoint di servizio è stato creato per soddisfare il [requisito](#requirements) 3, per consentire al traffico destinato ad Archiviazione di Azure di raggiungere direttamente Archiviazione di Azure.
11. Azure ha aggiunto automaticamente questa route alla tabella di route di tutte le subnet di *Virtual-network-1* e *Virtual-network-2.* Il prefisso degli indirizzi 0.0.0.0/0 è il prefisso più breve. Tutto il traffico inviato agli indirizzi di un prefisso degli indirizzi più lungo si basa su altre route. Per impostazione predefinita, Azure indirizza a Internet tutto il traffico destinato a indirizzi diversi da quelli specificati in una delle altre route. Azure ha modificato automaticamente lo stato da *Attivo* a *Non valido* per la subnet *Subnet1* quando alla subnet è stata associata una route definita dall'utente per il prefisso degli indirizzi 0.0.0.0/0 (ID12). Lo stato della route è ancora *Attivo* per tutte le altre subnet di entrambe le reti virtuali, perché la route non è associata ad altre subnet di altre reti virtuali.
12. Azure ha aggiunto questa route quando una route definita dall'utente per il prefisso degli indirizzi 0.0.0.0/0 è stata associata alla subnet *Subnet1*. La route definita dall'utente specifica 10.0.100.4 come indirizzo IP dell'appliance virtuale. Questa route non è associata a *Subnet2*, quindi non appare nella tabella di route per *Subnet2*. Tutto il traffico per gli indirizzi non inclusi nei prefissi degli indirizzi delle altre route viene inviato all'appliance virtuale. Con l'aggiunta di questa route, lo stato della route predefinita per il prefisso degli indirizzi 0.0.0.0/0 (ID11) è cambiato da *Attivo* a *Non valido* per *Subnet1*, perché una route definita dall'utente sostituisce una route predefinita. Questa route esiste per soddisfare il terzo [requisito](#requirements).

#### <a name="subnet2"></a>Subnet2

La tabella di route per *Subnet2* rappresentata nell'immagine contiene le route seguenti:

|Source (Sorgente)  |State  |Prefissi degli indirizzi    |Tipo hop successivo             |Indirizzo IP hop successivo|
|------- |-------|------              |-------                   |--------           
|Predefinito |Attivo |10.0.0.0/16         |Rete virtuale           |                   |
|Predefinito |Attivo |10.1.0.0/16         |Peering reti virtuali              |                   |
|Predefinito |Attivo |10.2.0.0/16         |Peering reti virtuali              |                   |
|Predefinito |Attivo |10.10.0.0/16        |Gateway di rete virtuale   |[X.X.X.X]          |
|Predefinito |Attivo |0.0.0.0/0           |Internet                  |                   |
|Predefinito |Attivo |10.0.0.0/8          |nessuno                      |                   |
|Predefinito |Attivo |100.64.0.0/10       |nessuno                      |                   |
|Predefinito |Attivo |192.168.0.0/16      |nessuno                      |                   |

La tabella di route per *Subnet2* contiene tutte le route predefinite create da Azure, il peering di rete virtuale facoltativo e le route facoltative del gateway di rete virtuale. Azure ha aggiunto le route facoltative a tutte le subnet della virtuale di rete quando sono stati aggiunti il gateway e il peering alla rete virtuale. Azure ha rimosso le route per i prefissi degli indirizzi 10.0.0.0/8, 192.168.0.0/16 e 100.64.0.0/10 dalla tabella di route *Subnet1* quando la route definita dall'utente per il prefisso degli indirizzi 0.0.0.0/0 è stata aggiunta a *Subnet1*.  

## <a name="next-steps"></a>Passaggi successivi

* [Creare una tabella di route definite dall'utente con route e un'appliance virtuale di rete](tutorial-create-route-table-portal.md)<br>
* [Configurare BGP per un gateway VPN di Azure](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
* [Usare BGP con ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits)<br>
* [Visualizzare tutte le route di una subnet](diagnose-network-routing-problem.md). Una tabella di route definite dall'utente mostra solo le route definite dall'utente, non le route BGP e predefinite di una subnet. La visualizzazione di tutte le route elenca le route predefinite, BGP e definite dall'utente per la subnet in cui si trova un'interfaccia di rete.<br>
* [Determinare il tipo di hop successivo](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tra una macchina virtuale e un indirizzo IP di destinazione. La funzionalità di hop successivo di Azure Network Watcher consente di determinare se il traffico è in uscita da una subnet e viene indirizzato dove previsto.
