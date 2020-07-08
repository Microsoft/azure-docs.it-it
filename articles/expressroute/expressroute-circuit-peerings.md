---
title: 'Azure ExpressRoute: circuiti e peering'
description: Questa pagina fornisce una panoramica dei circuiti e dei domini di routing/peering di ExpressRoute.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85847040"
---
# <a name="expressroute-circuits-and-peering"></a>Circuiti e peering ExpressRoute

I circuiti ExpressRoute connettono un'infrastruttura locale a Microsoft tramite un provider di connettività. Questo articolo illustra i circuiti e i domini di routing/peering ExpressRoute. La figura riportata di seguito fornisce una rappresentazione logica della connettività fra la rete WAN e Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Il peering pubblico di Azure è stato deprecato e non è disponibile per i nuovi circuiti ExpressRoute. I nuovi circuiti supportano il peering Microsoft e il peering privato.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>Circuiti ExpressRoute

Un circuito ExpressRoute rappresenta una connessione logica tra un'infrastruttura locale e i servizi cloud Microsoft tramite un provider di connettività. È possibile ordinare più circuiti ExpressRoute. Ciascun circuito può essere nella stessa regione o in regioni diverse, e può essere collegato ai locali tramite provider di connettività diversi.

I circuiti ExpressRoute non sono mappati ad alcuna entità fisica. Un circuito è identificato in modo univoco da un GUID standard denominato chiave di servizio. La chiave di servizio è l'unica informazione scambiata tra Microsoft, il provider di connettività e l'utente. Non è un segreto usato ai fini della sicurezza. Esiste un mapping 1:1 tra un circuito ExpressRoute e la chiave di servizio.

I nuovi circuiti ExpressRoute possono includere due peering indipendenti: peering privato e peering Microsoft. I circuiti ExpressRoute esistenti possono invece contenere fino a tre peering: Pubblico di Azure, Privato di Azure e Microsoft. Ogni peering è una coppia di sessioni BGP indipendenti, ognuna configurata in modo ridondante per garantire la disponibilità elevata. Esiste un mapping 1:N (1 <= N <= 3) tra un circuito ExpressRoute e i domini di routing. Per un circuito ExpressRoute può essere abilitato uno, due o tutti e tre i peering.

Ogni circuito prevede una larghezza di banda fissa (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) ed è mappato a un provider di connettività e a una località di peering. La larghezza di banda selezionata viene condivisa tra tutti i peering del circuito

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Quote, limiti e limitazioni

Per ogni circuito ExpressRoute si applicano quote e limiti predefiniti. Per informazioni aggiornate sulle quote, vedere la pagina [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](../azure-resource-manager/management/azure-subscription-service-limits.md) .

## <a name="expressroute-peering"></a><a name="routingdomains"></a>Peering ExpressRoute

A un circuito ExpressRoute sono associati più domini di routing/peering: pubblico di Azure, privato di Azure e Microsoft. Ogni peering è configurato in modo identico in una coppia di router (in una configurazione di tipo attivo-attivo o di condivisione del carico) per offrire una disponibilità elevata. Per rappresentare gli schemi di indirizzamento IP, i servizi di Azure sono classificati come *pubblici di Azure* e *privati di Azure*.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Peering privato di Azure

I servizi di calcolo di Azure, ovvero le macchine virtuali (IaaS) e i servizi cloud (PaaS), che sono distribuiti all'interno di una rete virtuale possono essere connessi tramite il dominio di peering privato. Il dominio di peering privato viene considerato un'estensione attendibile della rete di base in Microsoft Azure. È possibile configurare la connettività bidirezionale tra la rete di base e le reti virtuali (VNet) di Azure. Questo peering permette la connessione diretta a macchine virtuali e servizi cloud nei rispettivi indirizzi IP privati.  

È possibile connettere più di una rete virtuale al dominio di peering privato. Per informazioni su limiti e limitazioni, vedere la [pagina relativa alle domande frequenti](expressroute-faqs.md) . Per informazioni aggiornate sui limiti, visitare la pagina [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](../azure-resource-manager/management/azure-subscription-service-limits.md) .  Per informazioni sulla configurazione del routing, vedere la pagina relativa al [routing](expressroute-routing.md) .

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Peering Microsoft

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

La connettività a Microsoft Servizi online (Office 365 e i servizi PaaS di Azure) si verifica tramite il peering Microsoft. La connettività bidirezionale tra la rete WAN e i servizi cloud Microsoft verrà abilitata tramite il dominio di routing di peering Microsoft. È necessario connettersi ai servizi cloud Microsoft solo tramite indirizzi IP pubblici di cui si è proprietari o di proprietà del proprio provider di connettività ed è necessario rispettare tutte le regole definite. Per altre informazioni, vedere la pagina [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).

Per altre informazioni sui servizi supportati, sui costi e sui dettagli per la configurazione, vedere la [pagina relativa alle domande frequenti](expressroute-faqs.md) . Per informazioni sull'elenco di provider di connettività che offrono supporto per il peering Microsoft, vedere la pagina relativa alle [località per ExpressRoute](expressroute-locations.md) .

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Confronto tra i peering

La tabella seguente confronta i tre peering:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

È possibile scegliere di abilitare uno o più domini di routing come parte del circuito ExpressRoute. È possibile scegliere di posizionare tutti i domini di routing nella stessa rete VPN se si vuole combinarli in un singolo dominio di routing. È anche possibile posizionarli in domini di routing diversi, in modo analogo a quanto illustrato nel diagramma. La configurazione consigliata consiste nel connettere il peering privato direttamente alla rete di base e i collegamenti del peering pubblico e Microsoft alla rete perimetrale.

Ogni peering richiede sessioni BGP separati (una coppia per ogni tipo di peering). Le coppie di sessioni BGP forniscono un collegamento a disponibilità elevata. Se si stabilisce la connessione tramite provider di connettività di livello 2, l'utente è responsabile della configurazione e della gestione del routing. È possibile ottenere più informazioni esaminando i [flussi di lavoro](expressroute-workflows.md) per la configurazione di ExpressRoute.

## <a name="expressroute-health"></a><a name="health"></a>Integrità di ExpressRoute

La disponibilità, la connettività alle reti virtuali e l'utilizzo della larghezza di banda dei circuiti ExpressRoute possono essere monitorati usando [Monitoraggio prestazioni di rete](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

Monitoraggio prestazioni rete verifica l'integrità del peering privato di Azure e del peering Microsoft. Per altre informazioni, vedere questo [post](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/).

## <a name="next-steps"></a>Passaggi successivi

* Trovare un provider di servizi. Vedere l'articolo relativo ai [provider di servizi e alle località ExpressRoute](expressroute-locations.md).
* Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).
* Configurare la connessione ExpressRoute.
  * [Creare e gestire circuiti ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configurare il routing (peering) per circuiti ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
