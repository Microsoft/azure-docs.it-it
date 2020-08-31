---
title: 'Panoramica di Azure ExpressRoute: come connettersi tramite una connessione privata'
description: Questa panoramica tecnica di ExpressRoute illustra il funzionamento di una connessione ExpressRoute per l'estensione della rete locale in Azure tramite una connessione privata.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: overview
ms.date: 08/25/2020
ms.author: mialdrid
ms.openlocfilehash: 6621a9778025d67046722b7cd60e32a62b574ee5
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852936"
---
# <a name="what-is-azure-expressroute"></a>Che cos'è Azure ExpressRoute?
ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure e Office 365.

La connettività può essere stabilita da una rete (IP VPN) any-to-any, da una rete Ethernet punto a punto o da una Cross Connection virtuale tramite un provider di connettività presso una struttura di condivisione del percorso. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. In questo modo possono offrire un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e latenze coerenti rispetto alle connessioni Internet tradizionali. Per informazioni sulla procedura di connessione della rete a Microsoft tramite ExpressRoute, vedere [Modelli di connettività di ExpressRoute](expressroute-connectivity-models.md).

![Panoramica sulla connessione ExpressRoute](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Vantaggi principali

* Connettività di livello 3 tra la rete locale e il cloud Microsoft tramite un provider di connettività. La connettività può essere stabilita da una rete (IPVPN) any-to-any, da una connessione Ethernet punto a punto o con una Cross Connection virtuale tramite scambio Ethernet.
* Connettività ai servizi cloud Microsoft in tutte le aree all'interno dell'area geopolitica.
* Connettività globale ai servizi Microsoft di tutte le aree con il componente aggiuntivo ExpressRoute Premium.
* Routing dinamico tra la rete e Microsoft tramite BGP.
* Ridondanza incorporata in ogni località di peering per una maggiore affidabilità.
* [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/)per i tempi di attività delle connessioni.
* Supporto QoS per Skype for Business.

Per altre informazioni, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).

## <a name="features"></a>Funzionalità

### <a name="layer-3-connectivity"></a>Connettività di livello 3
Microsoft usa BGP, il protocollo di routing dinamico di standard del settore, per lo scambio di route tra la rete locale, le istanze in Azure e gli indirizzi pubblici Microsoft. Vengono stabilite più sessioni BGP con la rete per profili di traffico diversi. Per altre informazioni, vedere l'articolo relativo [al circuito e ai domini di routing ExpressRoute](expressroute-circuit-peerings.md) .

### <a name="redundancy"></a>Ridondanza
Ogni circuito ExpressRoute è costituito da due connessioni con due router perimetrali di Microsoft Enterprise (MSEE) in una [posizione di ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-locations#expressroute-locations) provenienti dal provider di connettività o dal perimetro della rete. Microsoft richiede una connessione BGP doppia dal provider di connettività o dalla rete perimetrale, una per ogni MSEE. È possibile scegliere di non distribuire dispositivi ridondanti/circuiti Ethernet sul lato dell'utente. I provider di connettività tuttavia usano dispositivi ridondanti per garantire che le connessioni vengono passate a Microsoft in modo ridondante. Una connettività di livello 3 ridondante è un requisito per la validità del [contratto di servizio](https://azure.microsoft.com/support/legal/sla/) .

### <a name="connectivity-to-microsoft-cloud-services"></a>Connettività ai servizi cloud Microsoft
Le connessioni ExpressRoute abilitano l'accesso ai servizi seguenti:
* Servizi di Microsoft Azure
* Servizi di Microsoft Office 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Per un elenco dettagliato dei servizi supportati per ExpressRoute, visitare la pagina [Domande frequenti su ExpressRoute](expressroute-faqs.md).

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Connettività a tutte le aree all'interno di un'area geopolitica
È possibile connettersi a Microsoft in una delle [località di peering](expressroute-locations.md) e accedere alle aree incluse nell'area geopolitica.

Se ad esempio si esegue la connessione a Microsoft da Amsterdam tramite ExpressRoute, sarà possibile accedere a tutti i servizi cloud Microsoft ospitati in Europa settentrionale e occidentale. Per una panoramica delle aree geopolitiche, delle aree cloud Microsoft associate e delle località di peering ExpressRoute corrispondenti, vedere l'articolo [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md).

### <a name="global-connectivity-with-expressroute-premium"></a>Connettività globale con ExpressRoute Premium
È possibile abilitare [ExpressRoute Premium](expressroute-faqs.md) per estendere la connettività tra confini geopolitici. Se ad esempio si esegue la connessione a Microsoft da Amsterdam tramite ExpressRoute, sarà possibile accedere a tutti i servizi cloud Microsoft ospitati in tutte le aree del mondo (cloud nazionali esclusi). È possibile accedere ai servizi distribuiti nell'America del Sud o in Australia nello stesso modo in cui si accede alle aree dell'Europa settentrionale e occidentale.

### <a name="local-connectivity-with-expressroute-local"></a>Connettività locale con ExpressRoute Local
È possibile trasferire i dati in modo conveniente abilitando lo [SKU Local](expressroute-faqs.md) se i dati possono essere archiviati in una località di ExpressRoute vicina all'area di Azure desiderata. Con il circuito Local, il trasferimento dei dati è incluso nell'addebito relativo alle porte di ExpressRoute. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Utilizzo della connettività locale con la copertura globale di ExpressRoute
È possibile abilitare la copertura globale di ExpressRoute per lo scambio di dati tra i siti locali tramite la connessione dei circuiti ExpressRoute. Ad esempio, se si dispone di un data center privato in California connesso a ExpressRoute nella Silicon Valley e di un altro data center privato in Texas connesso a ExpressRoute a Dallas, con la copertura globale di ExpressRoute, è possibile connettere tra loro i data center privati tramite due circuiti ExpressRoute. Il traffico tra data center passerà attraverso la rete Microsoft.

Per altre informazioni, vedere [Copertura globale di ExpressRoute](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Ecosistema di partner di connettività avanzata
ExpressRoute presenta un ecosistema di partner di connettività e system integrator in continua crescita. Per le informazioni più aggiornate, vedere l'articolo [Partner e località di peering per ExpressRoute](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Connettività a cloud nazionali
Microsoft gestisce ambienti cloud isolati per aree geopolitiche speciali e segmenti di clienti. Per un elenco dei provider e dei cloud nazionali, vedere la pagina [Partner e località di peering per ExpressRoute](expressroute-locations.md).

### <a name="expressroute-direct"></a>ExpressRoute Direct
ExpressRoute Direct offre ai clienti l'opportunità di connettersi direttamente alla rete globale di Microsoft in località peer distribuite in modo strategico in tutto il mondo. ExpressRoute Direct fornisce doppia connettività a 100 Gbps, che supporta la connettività attivo/attivo su larga scala.

Le funzionalità chiave di ExpressRoute Direct includono, tra le altre:

* Inserimento dati massiccio in servizi quali Archiviazione e Cosmos DB
* Isolamento fisico per i settori regolamentati che richiedono una connettività dedicata e isolata, ad esempio banche, enti pubblici e punti vendita al dettaglio
* Controllo granulare della distribuzione del circuito in base alla business unit

Per altre informazioni, vedere [Informazioni su ExpressRoute Direct](https://go.microsoft.com/fwlink/?linkid=2022973).

### <a name="bandwidth-options"></a>Opzioni di larghezza di banda
È possibile acquistare circuiti ExpressRoute per un'ampia gamma di larghezze di banda. Le larghezze di banda supportate sono elencate di seguito. Rivolgersi al provider di connettività per verificare le larghezze di banda supportate.

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Scalabilità dinamica della larghezza di banda
È possibile aumentare la larghezza di banda del circuito ExpressRoute (in base ad approssimazioni ottimali) senza dover chiudere le connessioni. Per altre informazioni, vedere [Modifica di un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Modelli di fatturazione flessibili
È possibile selezionare il modello di fatturazione più adatto per le proprie esigenze. Scegliere tra i modelli di fatturazione elencati di seguito. Per altre informazioni, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).

* **Dati illimitati**. La fatturazione è basata su una tariffa mensile che include il trasferimento di tutti i dati in ingresso e in uscita.
* **Dati a consumo**. La fatturazione è basata su una tariffa mensile che include il trasferimento di tutti i dati in ingresso. Il trasferimento dei dati in uscita invece viene addebitato per GB di dati trasferiti. Le tariffe per il trasferimento dei dati variano in base all'area.
* **Componente aggiuntivo ExpressRoute Premium**. ExpressRoute Premium è un componente aggiuntivo del circuito ExpressRoute. Offre le funzionalità seguenti: 
  * Estensione dei limiti di route per i peering pubblico e privato di Azure da 4.000 a 10.000 route.
  * Connettività globale per i servizi. Un circuito ExpressRoute creato in un'area qualsiasi (esclusi i cloud nazionali) potrà accedere alle risorse di tutte le altre aree del mondo. Ad esempio, una rete virtuale creata nell'Europa occidentale è accessibile attraverso un circuito ExpressRoute fornito nella Silicon Valley.
  * Aumento del numero di collegamenti di reti virtuali consentiti per circuito ExpressRoute da 10 a un numero superiore, a seconda della larghezza di banda del circuito.

## <a name="faq"></a>Domande frequenti
Per le domande frequenti su ExpressRoute, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).

## <a name="whats-new"></a><a name="new"></a>Novità

Sottoscrivere il feed RSS e visualizzare gli aggiornamenti più recenti di ExpressRoute nella pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sui [Modelli di connettività di ExpressRoute](expressroute-connectivity-models.md).
* Informazioni sulle connessioni e i domini di routing ExpressRoute. Vedere [Circuiti e domini di routing ExpressRoute](expressroute-circuit-peerings.md).
* Trovare un provider di servizi. Vedere [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md).
* Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).
* Vedere i requisiti per [Routing](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurare la connessione ExpressRoute.
  * [Creare e modificare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Creare e modificare i peering per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
  * [Connettere una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Informazioni su alcune delle altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
