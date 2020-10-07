---
title: Rete virtuale di Azure | Microsoft Docs
description: Informazioni sui concetti e le funzionalità di Rete virtuale di Azure, inclusi lo spazio degli indirizzi, le subnet, le aree e le sottoscrizioni.
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 480e22b319edd03dc9bb9d666dd43718fb3c841b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88507031"
---
# <a name="what-is-azure-virtual-network"></a>Che cos'è Rete virtuale di Azure?

Rete virtuale di Azure (VNet) è il blocco predefinito fondamentale per la rete privata in Azure. VNet consente a diversi tipi di risorse di Azure, ad esempio Macchine virtuali di Azure, di comunicare in modo sicuro tra di esse, con Internet e con le reti locali. Simile alle reti tradizionali che si eseguono nei data center, VNet offre anche i vantaggi dell'infrastruttura di Azure, tra cui scalabilità, disponibilità e isolamento.

## <a name="vnet-concepts"></a>Concetti di VNet

- **Spazio indirizzi**: quando si crea una rete virtuale, è necessario specificare uno spazio indirizzi IP privato personalizzato con indirizzi pubblici e privati (RFC 1918). Azure assegna alle risorse di una rete virtuale un indirizzo IP privato dello spazio indirizzi specificato. Se, ad esempio, si distribuisce una macchina virtuale in una rete virtuale con spazio indirizzi 10.0.0.0/16, alla macchina virtuale verrà assegnato un indirizzo IP privato come 10.0.0.4.
- **Subnet:** le subnet consentono di segmentare la rete virtuale in una o più reti secondarie e di allocare una parte dello spazio indirizzi della rete virtuale a ogni subnet. È quindi possibile distribuire le risorse di Azure in una subnet specifica. Proprio come le reti tradizionali, le subnet consentono di segmentare lo spazio indirizzi della rete virtuale in segmenti appropriati per la rete interna dell'organizzazione. In questo modo, anche l'allocazione degli indirizzi risulta più efficiente. È possibile proteggere le risorse all'interno delle subnet con i gruppi di sicurezza di rete. Per altre informazioni, vedere [Gruppi di sicurezza di rete](security-overview.md).
- **Aree di Azure**: l'ambito di una rete virtuale è una singola area/località; tuttavia, è possibile connettere più reti virtuali da diverse aree di Azure usando il peering di rete virtuale.
- **Sottoscrizione:** l'ambito di una rete virtuale è una sottoscrizione. È possibile implementare più reti virtuali in ogni [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [area](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) di Azure.

## <a name="best-practices"></a>Procedure consigliate

Durante la creazione della rete in Azure, è importante tenere presente i principi di progettazione universale seguenti:

- Verificare che gli spazi indirizzi non si sovrappongano. Assicurarsi che lo spazio indirizzi della rete virtuale (blocco CIDR) non si sovrapponga con altri intervalli di rete dell'organizzazione.
- È importante che le subnet non coprano l'intero spazio indirizzi della rete virtuale, in modo da poter riservare una parte dello spazio indirizzi per esigenze future.
- È preferibile avere poche reti virtuali di grandi dimensioni invece di tante reti virtuali di piccole dimensioni. In questo modo, si eviterà infatti un sovraccarico di gestione.
- Proteggere le reti virtuali assegnando gruppi di sicurezza di rete alle subnet sottostanti.

## <a name="communicate-with-the-internet"></a>Comunicare con Internet

Per impostazione predefinita, tutte le risorse in una rete virtuale possono comunicare verso l'esterno su Internet. Per la comunicazione in ingresso con una risorsa, è possibile assegnarle un indirizzo IP pubblico o un servizio di bilanciamento del carico pubblico. Si può usare un indirizzo IP pubblico o un servizio di bilanciamento del carico pubblico anche per gestire le connessioni in uscita.  Per altre informazioni sulle connessioni in uscita in Azure, vedere [Connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md), [Indirizzi IP pubblici](virtual-network-public-ip-address.md) e [Informazioni su Azure Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Quando si usa solo un'istanza di [Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md) interna, la connettività in uscita non è disponibile finché non si definisce la modalità di funzionamento desiderata per le [connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md) con un IP pubblico a livello di istanza o un servizio di bilanciamento del carico pubblico.

## <a name="communicate-between-azure-resources"></a>Comunicare tra risorse di Azure

Le risorse di Azure comunicano in modo sicuro tra di esse in uno dei modi seguenti:

- **Tramite una rete virtuale**: è possibile distribuire macchine virtuali e diversi altri tipi di risorse di Azure in una rete virtuale, ad esempio ambienti del servizio app di Azure, servizio Azure Kubernetes e set di scalabilità di macchine virtuali di Azure. Per visualizzare un elenco completo delle risorse di Azure che è possibile distribuire in una rete virtuale, vedere [Integrazione del servizio di rete virtuale](virtual-network-for-azure-services.md).
- **Tramite un endpoint servizio di rete virtuale**: estendere lo spazio di indirizzi privato della rete virtuale e l'identità della rete virtuale alle risorse dei servizi di Azure, ad esempio gli account di archiviazione di Azure e i database SQL di Azure, tramite una connessione diretta. Gli endpoint servizio consentono di associare le risorse critiche dei servizi di Azure solo a una rete virtuale. Per altre informazioni, vedere [Panoramica degli endpoint servizio di rete virtuale](virtual-network-service-endpoints-overview.md).
- **Tramite il peering di reti virtuali**: È possibile connettere le reti virtuali tra di esse, per poter consentire alle risorse in qualsiasi rete virtuale di comunicare con le altre usando il peering di rete virtuale. Le reti virtuali connesse possono essere in aree di Azure uguali o diversi. Per altre informazioni, vedere [Peering di rete virtuale](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Comunicare con le risorse locali

È possibile connettere i computer e le reti locali a una rete virtuale usando qualsiasi combinazione delle opzioni seguenti:

- **Rete privata virtuale (VPN) da punto a sito:** viene stabilita tra una rete virtuale e un singolo computer nella rete. Per ogni computer per cui si vuole stabilire la connettività con una rete virtuale è necessario configurare la connessione. Questo tipo di connessione è ideale se si sta appena iniziando a usare Azure o per gli sviluppatori, perché richiede modifiche minime o nessuna modifica alla rete esistente. La comunicazione tra il computer e una rete virtuale viene inviata attraverso un tunnel crittografato tramite Internet. Per altre informazioni, vedere [VPN da punto a sito](../vpn-gateway/point-to-site-about.md?toc=%2fazure%2fvirtual-network%2ftoc.json#).
- **VPN da sito a sito:** viene stabilita tra il dispositivo VPN locale e un gateway VPN di Azure distribuito in una rete virtuale. Questo tipo di connessione consente a qualsiasi risorsa locale autorizzata dall'utente di accedere a una rete virtuale. La comunicazione tra il dispositivo VPN locale e un gateway VPN di Azure viene inviata attraverso un tunnel crittografato tramite Internet. Per altre informazioni, vedere [VPN da sito a sito](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** viene stabilita una connessione tra la rete e Azure tramite un partner ExpressRoute. La connessione è privata. Il traffico non passa da Internet. Per altre informazioni, vedere [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="filter-network-traffic"></a>Filtrare il traffico di rete

È possibile filtrare il traffico di rete tra subnet usando una o entrambe le opzioni seguenti.

- **Gruppi di sicurezza di rete:** gruppi di sicurezza di rete e gruppi di sicurezza delle applicazioni possono contenere più regole di sicurezza in ingresso e in uscita che consentono di filtrare il traffico verso e dalle risorse per protocollo, porta e indirizzo IP di origine e di destinazione. Per altre informazioni, consultare [Gruppi di sicurezza di rete](security-overview.md#network-security-groups) e [Gruppi di sicurezza delle applicazioni](security-overview.md#application-security-groups).
- **Appliance virtuali di rete:** un'appliance virtuale di rete è una macchina virtuale che esegue una funzione di rete, ad esempio un firewall, un'ottimizzazione WAN o un'altra funzione di rete. Per visualizzare un elenco delle appliance virtuali di rete disponibili che è possibile distribuire in una rete virtuale di Azure, vedere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Indirizzare il traffico di rete

Azure instrada il traffico tra subnet, reti virtuali connesse, reti locali e Internet, per impostazione predefinita. Per sostituire le route predefinite create da Azure, è possibile implementare una o entrambe le opzioni seguenti.

- **Tabelle di route:** è possibile creare tabelle di route personalizzate con route che controllano dove viene instradato il traffico per ogni subnet. Altre informazioni sulle [tabelle di route](virtual-networks-udr-overview.md#user-defined).
- **Route Border Gateway Protocol (BGP):** se si connette la rete virtuale alla rete locale con un gateway VPN di Azure o una connessione ExpressRoute, è possibile propagare le route BGP locali alle reti virtuali. Altre informazioni sull'uso di BGP con [Gateway VPN di Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ed [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="virtual-network-integration-for-azure-services"></a>Integrazione della rete virtuale per i servizi di Azure

L'integrazione dei servizi di Azure in una rete virtuale di Azure consente di accedere privatamente ai servizi dalle macchine virtuali o da risorse di calcolo nella rete virtuale.
È possibile integrare i servizi di Azure nella rete virtuale con le opzioni seguenti:
- Distribuendo [istanze dedicate del servizio](virtual-network-for-azure-services.md) in una rete virtuale. I servizi sono accessibili privatamente all'interno della rete virtuale e da reti locali.
- Usando [Collegamento privato](../private-link/private-link-overview.md) per accedere privatamente a un'istanza specifica del servizio dalla rete virtuale e dalle reti locali.
- È anche possibile accedere al servizio usando endpoint pubblici estendendo una rete virtuale al servizio tramite [endpoint di servizio](virtual-network-service-endpoints-overview.md). Gli endpoint di servizio consentono di proteggere le risorse del servizio nella rete virtuale.
 

## <a name="azure-vnet-limits"></a>Limiti della rete virtuale di Azure

Esistono alcuni limiti in merito al numero di risorse di Azure che è possibile distribuire. La maggior parte dei limiti relativi alla rete di Azure sono impostati ai rispettivi valori massimi. È possibile tuttavia [aumentare alcuni limiti di rete](../azure-portal/supportability/networking-quota-requests.md), come specificato nella [pagina relativi ai limiti della rete virtuale](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Prezzi

Non sono previsti addebiti per l'uso della rete virtuale di Azure: è un servizio gratuito. Sono invece previsti addebiti standard per le risorse, quali macchine virtuali e altri prodotti. Per altre informazioni, vedere [Prezzi di Rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network/) e il [calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/) di Azure.

## <a name="next-steps"></a>Passaggi successivi

 Per iniziare a usare una rete virtuale, crearne una, distribuirvi alcune macchine virtuali e comunicare tra le macchine virtuali. Per informazioni sulla procedura, vedere la guida introduttiva [Creare una rete virtuale](quick-create-portal.md).
