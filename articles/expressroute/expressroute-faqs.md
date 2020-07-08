---
title: Domande frequenti - Azure ExpressRoute | Microsoft Docs
description: In Domande frequenti su ExpressRoute sono disponibili informazioni su servizi di Azure supportati, costi, dati e connessioni, Contratto di servizio, provider e località, larghezza di banda e altri dettagli tecnici.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: c9b109fe12b709649adaa05d62b3d1255605986e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987311"
---
# <a name="expressroute-faq"></a>Domande frequenti su ExpressRoute

## <a name="what-is-expressroute"></a>Che cos'è ExpressRoute?

ExpressRoute è un servizio di Azure che consente di creare connessioni private tra i data center Microsoft e l'infrastruttura presente nella struttura locale o in una struttura di condivisione. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica e offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quali sono i vantaggi dell'uso di ExpressRoute e delle connessioni di rete private?

Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. Esse offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e latenze minori e coerenti rispetto alle connessioni Internet tradizionali. In alcuni casi, l'uso di connessioni ExpressRoute per trasferire dati tra dispositivi locali e Azure può produrre vantaggi significativi in termini di costi.

### <a name="where-is-the-service-available"></a>Dov'è disponibile il servizio?

Vedere questa pagina per la posizione e la disponibilità del servizio: [Partner e località per ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>In che modo è possibile usare ExpressRoute per connettersi a Microsoft in assenza di una relazione con uno dei partner del gestore ExpressRoute?

Si può selezionare un gestore regionale e connessioni Ethernet via terra in una delle sedi supportate del provider di Exchange. È quindi possibile connettersi a Microsoft nella sede del provider. Per verificare se il provider di servizio è presente in una delle località di Exchange, vedere l'ultima sezione della pagina [Partner e località per ExpressRoute](expressroute-locations.md) . Sarà quindi possibile ordinare un circuito ExpressRoute tramite il provider di servizi per connettersi ad Azure.

### <a name="how-much-does-expressroute-cost"></a>Quanto costa ExpressRoute?

Per informazioni sui prezzi, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se si acquista un circuito ExpressRoute di una determinata larghezza di banda, la connessione VPN acquistata dal provider di servizi di rete deve essere della stessa velocità?

No. È possibile acquistare una connessione VPN di qualsiasi velocità dal provider di servizi. Tuttavia, la connessione ad Azure è limitata alla larghezza di banda del circuito ExpressRoute acquistato.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Se si paga per un circuito ExpressRoute di una determinata larghezza di banda, si ha la possibilità di potenziarlo fino a velocità più elevate, se necessario?

Sì. I circuiti ExpressRoute sono configurati per consentire di potenziare fino al doppio il limite di larghezza di banda acquistato, senza alcun costo aggiuntivo. Contattare il provider di servizi per vedere se supporta questa funzionalità. Non è destinata a un periodo di tempo prolungato e non è garantita.  Se il traffico viene trasmesso attraverso un gateway ExpressRoute, la larghezza di banda per lo SKU è fissa e non consente il burst.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Si possono usare contemporaneamente la stessa connessione di rete privata con la rete virtuale e altri servizi di Azure?

Sì. Dopo l'installazione, il circuito ExpressRoute consente di accedere ai servizi all'interno di una rete virtuale e ad altri servizi di Azure contemporaneamente. La connessione alle reti virtuali viene eseguita tramite il percorso di peering privato e ad altri servizi tramite il percorso di peering Microsoft.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>In che modo vengono annunciate le reti virtuali nel peering privato ExpressRoute?

Il gateway ExpressRoute annuncerà lo *spazio indirizzi* della rete virtuale di Azure, quindi non è possibile includere/escludere a livello di subnet. Viene annunciato sempre lo spazio indirizzi della rete virtuale. Inoltre, se viene usato il peering reti virtuali e nella rete virtuale con peering è abilitato "Usa gateway remoto", viene annunciato anche lo spazio indirizzi della rete virtuale con peering.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Quanti prefissi è possibile annunciare da una rete virtuale alla rete locale nel peering privato ExpressRoute?

In una connessione ExpressRoute singola o tramite il peering reti virtuali è possibile annunciare un massimo di 200 prefissi usando il transito tramite gateway. Se, ad esempio, si dispone di 199 di spazi di indirizzi in una singola rete virtuale connessa a un circuito ExpressRoute, tutti e 199 i prefissi vengono annunciati nella rete locale. In alternativa, se si dispone di una rete virtuale abilitata per consentire il transito tramite gateway con 1 spazio indirizzi e 150 reti virtuali spoke abilitate con l'opzione "Consenti gateway remoto", la rete virtuale distribuita con il gateway annuncerà 151 prefissi nella rete locale.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Cosa accade se si supera il limite di prefissi in una connessione ExpressRoute?

La connessione tra il circuito ExpressRoute e il gateway (e le reti virtuali con peering che usano il transito tramite gateway) si arresterà. Verrà ristabilita quando il limite di prefissi non sarà più superato.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>È possibile filtrare le route provenienti dalla rete locale?

L'unico modo per filtrare/includere le route è nel router perimetrale locale. È possibile aggiungere alla rete virtuale alcune route definite dall'utente che influiscano su un routing specifico, ma sarà un'operazione statica e non parte dell'annuncio BGP.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute offre un contratto di servizio?

Per informazioni, vedere [Contratto di servizio di ExpressRoute](https://azure.microsoft.com/support/legal/sla/).

## <a name="supported-services"></a>Servizi supportati

ExpressRoute supporta [tre domini di routing](expressroute-circuit-peerings.md) per vari tipi di servizi: peering privato, peering Microsoft e peering pubblico (deprecato).

### <a name="private-peering"></a>Peering privato

**Supportati:**

* Reti virtuali, inclusi tutti i servizi cloud e tutte le macchine virtuali

### <a name="microsoft-peering"></a>Peering Microsoft

Se il circuito ExpressRoute è abilitato per il peering Azure Microsoft, è possibile accedere agli [intervalli di indirizzi IP pubblici](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) usati in Azure nel circuito. Il peering Azure Microsoft garantirà accesso ai servizi attualmente ospitati in Azure (con restrizioni geografiche in base allo SKU del circuito). Per verificare la disponibilità di un servizio specifico, è possibile controllare la documentazione relativa a tale servizio per verificare se è stato pubblicato un intervallo riservato per il servizio. Quindi cercare gli intervalli di indirizzi IP del servizio di destinazione e confrontarli con quelli elencati in [Azure IP Ranges and Service Tags – Public Cloud XML file](https://www.microsoft.com/download/details.aspx?id=56519). In alternativa, è possibile aprire un ticket di supporto per il servizio in questione per ottenere chiarimenti.

**Supportati:**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI - Disponibile tramite una community regionale di Azure. Vedere [qui](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) per trovare informazioni su come scoprire la regione del tenant Power BI.
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (community di Servizi globali di Azure)
* Indirizzi IP pubblici di Azure per IaaS (macchine virtuali, gateway di rete virtuale, servizio di bilanciamento del carico e così via)  
* È supportata anche la maggior parte degli altri servizi di Azure. Contattare direttamente il servizio che si vuole usare per verificarne il supporto.

**Non supportati:**

* RETE CDN
* Frontdoor di Azure
* [Desktop virtuale Windows](https://azure.microsoft.com/services/virtual-desktop/)
* Server Multi-Factor Authentication Server (legacy)
* Gestione traffico

### <a name="public-peering"></a>Peering pubblico

Il peering pubblico è stato disabilitato sui circuiti nuovi di ExpressRoute. I servizi di Azure sono ora disponibili nel peering Microsoft. Se un circuito è stato creato prima che il peering pubblico fosse deprecato, è possibile scegliere di usare il peering Microsoft o il peering pubblico a seconda dei servizi desiderati.

Per conoscere altre informazioni e i passaggi di configurazione per il peering pubblico, vedere [Peering pubblico di ExpressRoute](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Perché durante la configurazione del peering Microsoft viene visualizzato lo stato "Convalida necessaria" per "Prefissi pubblici annunciati"?

Microsoft verifica se all'utente sono assegnati "Prefissi pubblici annunciati" e "ASN peer" (o "ASN cliente") nel registro di sistema di routing Internet. Se si ricevono prefissi pubblici da un'altra entità e l'assegnazione non viene registrata con il registro di sistema di routing, la convalida automatica non verrà completata e sarà richiesta la convalida manuale. Se la convalida automatica ha esito negativo, verrà visualizzato il messaggio "Convalida necessaria".

Se viene visualizzato il messaggio "Convalida necessaria", raccogliere i documenti che mostrano i prefissi pubblici assegnati all'organizzazione dall'entità elencata come proprietaria dei prefissi nel registro di sistema di routing e inviarli per la convalida manuale aprendo un ticket di supporto come illustrato di seguito.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Dynamics 365 è supportato in ExpressRoute?

Gli ambienti Dynamics 365 e Common Data Service (CDS) sono ospitati in Azure, di conseguenza i clienti traggono vantaggio dal supporto ExpressRoute sottostante per le risorse di Azure. È possibile connettersi agli endpoint servizio se il filtro del router include le aree di Azure in cui sono ospitati gli ambienti Dynamics 365/CDS.

> [!NOTE]
> [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) **non** è necessario per la connettività Dynamics 365 tramite Azure ExpressRoute se il circuito ExpressRoute viene distribuito nella stessa [area geopolitica](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers#expressroute-locations).

## <a name="data-and-connections"></a>Dati e connessioni

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Esistono limiti alla quantità di dati trasferibili usando ExpressRoute?

Non esistono limiti alla quantità di dati trasferibili. Per informazioni sulle tariffe relative alle velocità della larghezza di banda, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Quali sono le velocità di connessione supportate da ExpressRoute?

Offerte relative alle larghezze di banda supportate:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Quali provider del servizio sono disponibili?

Per l'elenco dei provider di servizi e delle relative località, vedere [Partner e località per ExpressRoute](expressroute-locations.md) .

## <a name="technical-details"></a>Dettagli tecnici

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Quali sono i requisiti tecnici per la connessione della posizione locale ad Azure?

Per informazioni sui requisiti, vedere la [pagina dei prerequisiti di ExpressRoute](expressroute-prerequisites.md).

### <a name="are-connections-to-expressroute-redundant"></a>Le connessioni a ExpressRoute sono ridondanti?

Sì. Ogni circuito ExpressRoute ha una coppia ridondante di connessioni incrociate configurate per garantire una disponibilità elevata.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>In caso di errore di un collegamento a ExpressRoute, verrà persa la connettività?

In caso di errore di una delle connessioni incrociate, la connettività non verrà persa. È attualmente disponibile una connessione ridondante per supportare il carico della rete e offrire disponibilità elevata per il circuito ExpressRoute. Per ottenere resilienza a livello di circuito, è possibile anche creare un circuito in una sede di peering diversa.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Come è possibile implementare la ridondanza nel peering privato?

Più circuiti ExpressRoute da diverse località di peering o fino a quattro connessioni dalla stessa località di peering possono essere connesse alla stessa rete virtuale per offrire disponibilità elevata nel caso in cui un singolo circuito non sia più disponibile. È quindi possibile [assegnare pesi più elevati](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) a una delle connessioni locali per preferire un circuito specifico. È vivamente consigliabile configurare almeno due circuiti ExpressRoute per evitare singoli punti di guasto. 

Vedere [qui](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) per la progettazione della disponibilità elevata e [qui](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) per la progettazione del ripristino di emergenza.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Come è possibile implementare la ridondanza nel peering Microsoft?

Quando i clienti usano il peering Microsoft per accedere ai servizi pubblici di Azure come Archiviazione o SQL di Azure oppure per Office 365, è vivamente consigliabile implementare più circuiti in varie località di peering per evitare singoli punti di guasto. I clienti possono annunciare lo stesso prefisso in entrambi i circuiti e [anteporre il PERCORSO AS](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) o annunciare vari prefissi per determinare il percorso dalla rete locale.

Per la progettazione per la disponibilità elevata, vedere [qui](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute).

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Come è possibile garantire la disponibilità elevata in una rete virtuale connessa a ExpressRoute?

È possibile ottenere la disponibilità elevata connettendosi a un massimo di quattro circuiti ExpressRoute nella stessa località di peering alla rete virtuale o connettendo circuiti ExpressRoute in posizioni di peering diverse (ad esempio, Singapore, Singapore2) alla rete virtuale. Se si arresta un circuito ExpressRoute, la connettività eseguirà il failover su un altro circuito ExpressRoute. Per impostazione predefinita, il traffico in uscita dalla rete virtuale viene instradato sul routing ECMP (Equal-Cost-Multi-Path). È possibile usare il peso della connessione per preferire un circuito a un altro. Per altre informazioni, vedere [Optimizing ExpressRoute Routing](expressroute-optimize-routing.md) (Ottimizzazione del routing di ExpressRoute).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Come è possibile assicurarsi che il traffico destinato a servizi pubblici di Azure come Archiviazione e SQL di Azure nel peering Microsoft o nel peering pubblico sia quello preferito nel percorso ExpressRoute?

Per assicurarsi che il percorso da locale ad Azure sia sempre il preferito nel circuito ExpressRoute, è necessario implementare l'attributo *Preferenza locale* nei router.

Vedere [qui](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) i dettagli aggiuntivi sulla selezione del percorso BGP e sulle configurazioni comuni del router. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Se non si condivide un percorso in un'infrastruttura Cloud Exchange e il provider di servizi offre una connessione punto a punto, è necessario ordinare due connessioni fisiche tra la rete locale e Microsoft?

Se il provider di servizi è in grado di stabilire due circuiti virtuali Ethernet sulla connessione fisica, è sufficiente una connessione fisica. La connessione fisica, ad esempio una fibra ottica, viene terminata su un dispositivo di livello 1 (L1) (vedere l'immagine). I due circuiti virtuali Ethernet sono contrassegnati con ID VLAN diversi, uno per il circuito primario e uno per quello secondario. Gli ID VLAN sono nell'intestazione Ethernet 802.1Q esterna. L'intestazione Ethernet 802.1Q interna, non indicata nella figura, viene mappata a un [dominio di routing ExpressRoute](expressroute-circuit-peerings.md)specifico.

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Si può estendere una delle reti VLAN ad Azure tramite ExpressRoute?

No. Non sono supportate estensioni alla connettività di livello 2 in Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>La sottoscrizione può includere più di un circuito ExpressRoute?

Sì. La sottoscrizione può includere più di un circuito ExpressRoute. Il limite predefinito è impostato a 10. Se necessario, contattare il supporto tecnico Microsoft per aumentare il limite.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Si possono usare circuiti ExpressRoute di diversi provider di servizi?

Sì. È possibile usare i circuiti ExpressRoute con molti provider di servizi. Ogni circuito ExpressRoute è associato a un solo provider di servizi. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Sono presenti due località di peering per ExpressRoute nella stessa area metropolitana, ad esempio Singapore e Singapore2. Quale località di peering devo scegliere per creare il mio circuito ExpressRoute?
Se il provider di servizi offre ExpressRoute in entrambe le località, è possibile contattare il provider e selezionare uno dei due siti per configurare ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>È possibile avere più circuiti ExpressRoute nella stessa area metropolitana? È possibile collegarli alla stessa rete virtuale?

Sì. È possibile avere più circuiti ExpressRoute con provider di servizi uguali o diversi. Se nell'area metropolitana sono presenti più località peer per ExpressRoute e i circuiti vengono creati in località peer diverse, è possibile collegarli alla stessa rete virtuale. Se i circuiti vengono creati nella stessa posizione di peering, è possibile collegare fino a quattro circuiti alla stessa rete virtuale.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Come si connettono le reti virtuali a un circuito ExpressRoute?

I passaggi di base sono:

* Stabilire un circuito ExpressRoute e richiedere al provider di servizi di abilitarlo.
* L'utente o il provider deve configurare i peering BGP.
* Collegare la rete virtuale al circuito ExpressRoute.

Per altre informazioni, vedere [Flussi di lavoro e stati di provisioning di un circuito ExpressRoute](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Sono previsti limiti di connettività per il circuito ExpressRoute?

Sì. L'articolo [Partner e località per ExpressRoute](expressroute-locations.md) offre una panoramica dei limiti di connettività per un circuito ExpressRoute. La connettività per un circuito ExpressRoute è limitata a una singola area geopolitica. È possibile espandere la connettività per superare le aree geopolitiche abilitando la funzionalità Premium di ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Un circuito ExpressRoute può essere collegato a più reti virtuali?

Sì. È possibile avere fino a 10 connessioni di reti virtuali in un circuito ExpressRoute Standard e fino a 100 in un [circuito ExpressRoute Premium](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Sono disponibili più sottoscrizioni di Azure che contengono reti virtuali. È possibile connettere reti virtuali di sottoscrizioni distinte a un singolo circuito ExpressRoute?

Sì. È possibile collegare fino a 10 reti virtuali nella stessa sottoscrizione del circuito o diverse sottoscrizioni usando un singolo circuito ExpressRoute. Per aumentare questo limite, abilitare la funzionalità Premium di ExpressRoute.

Per altre informazioni, vedere [Condivisione di un circuito ExpressRoute tra più sottoscrizioni](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Sono disponibili più sottoscrizioni di Azure associate a diversi tenant di Azure Active Directory o le registrazioni di Enterprise Agreement. È possibile connettere reti virtuali presenti in tenant separati e registrazioni a un singolo circuito ExpressRoute che non si trova nello stesso tenant o registrazione?

Sì. Le autorizzazioni di ExpressRoute possono estendersi a sottoscrizione, tenant e registrazione, senza richiedere alcuna configurazione aggiuntiva. 

Per altre informazioni, vedere [Condivisione di un circuito ExpressRoute tra più sottoscrizioni](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Le reti virtuali connesse allo stesso circuito sono isolate tra loro?

No. Da un punto di vista del routing, tutte le reti virtuali collegate allo stesso circuito ExpressRoute appartengono allo stesso dominio di routing e non sono isolate le une dalle altre. Se è necessario l'isolamento delle route, creare un circuito ExpressRoute separato.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>È possibile connettere una sola rete virtuale a più circuiti ExpressRoute?

Sì. È possibile collegare una singola rete virtuale con un massimo di 4 circuiti ExpressRoute nella stessa località di peering o in località diverse. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Si può accedere a Internet usando le reti virtuali connesse a circuiti ExpressRoute?

Sì. Se non è stata eseguita la pubblicazione di route predefinite (0.0.0.0/0) o prefissi di route Internet tramite la sessione BGP, è possibile connettersi a Internet da una rete virtuale collegata a un circuito ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Si può bloccare la connettività Internet per le reti virtuali connesse a circuiti ExpressRoute?

Sì. È possibile pubblicare route predefinite (0.0.0.0/0) per bloccare tutta la connettività Internet per le macchine virtuali distribuite in una rete virtuale e indirizzare tutto il traffico in uscita attraverso il circuito ExpressRoute.

Se si pubblicano route predefinite, il traffico verso i servizi offerti tramite peering Microsoft, ad esempio l'archiviazione di Azure e database SQL, viene forzato verso l'istanza locale. Sarà necessario configurare i router in modo che restituiscano traffico ad Azure tramite il percorso di peering Microsoft su Internet. Se è stato abilitato un endpoint di servizio per il servizio, il traffico verso il servizio non viene forzato in locale, ma rimane all'interno della rete backbone di Azure. Per altre informazioni sugli endpoint del servizio, vedere [Endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json).

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Le reti virtuali collegate allo stesso circuito ExpressRoute possono comunicare tra loro?

Sì. Le macchine virtuali distribuite in reti virtuali connesse allo stesso circuito ExpressRoute possono comunicare tra loro.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>È possibile usare la connettività da sito per reti virtuali insieme a ExpressRoute?

Sì. ExpressRoute può coesistere con VPN da sito a sito. Vedere [Configurare connessioni coesistenti ExpressRoute e da sito a sito](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Perché esiste un indirizzo IP pubblico associato al gateway ExpressRoute in una rete virtuale?

L'indirizzo IP pubblico viene usato solo per la gestione interna e non costituisce un rischio per la sicurezza della rete virtuale.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Sono previsti limiti per i numeri di route pubblicabili?

Sì. Sono accettati al massimo 4000 prefissi di route per il peering privato e 200 per il peering Microsoft. Se si abilita la funzionalità Premium di ExpressRoute, sarà possibile aumentare questo valore fino a 10.000 route per il peering privato.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Sono previste limitazioni per gli intervalli IP pubblicabili tramite la sessione BGP?

I prefissi privati (RFC1918) non sono accettati nella sessione BGP per il peering Microsoft. È accettata qualsiasi dimensione di prefisso (fino a 32) sia nel peering Microsoft che nel peering privato.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Cosa succede se si superano i limiti per BGP?

Le sessioni BGP saranno rimosse. Saranno ripristinate quando il numero di prefissi tornerà sotto il limite consentito.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Che cos'è il tempo di attesa BGP ExpressRoute? È possibile regolarlo?

Il tempo di attesa è 180. I messaggi keep-alive vengono inviati ogni 60 secondi. Queste sono impostazioni fisse sul lato Microsoft che non è possibile modificare. È possibile configurare diversi timer, e i parametri della sessione BGP verranno negoziati di conseguenza.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>È possibile modificare la larghezza di banda di un circuito ExpressRoute?

Sì, è possibile provare ad aumentare la larghezza di banda del circuito ExpressRoute nel portale di Azure o con PowerShell. Se è disponibile capacità sulla porta fisica in cui è stato creato il circuito, la modifica ha esito positivo. 

Se la modifica ha esito negativo, significa che la capacità residua sulla porta corrente non è sufficiente e che è necessario creare un nuovo circuito ExpressRoute con maggiore larghezza di banda, oppure che la posizione non ha altra capacità. In quest'ultimo caso non è possibile aumentare la larghezza di banda. 

Sarà anche necessario contattare il provider di connettività per assicurare che aggiorni le limitazioni della propria rete per supportare l'incremento della larghezza di banda. Non è tuttavia possibile ridurre la larghezza di banda del circuito ExpressRoute. È necessario creare un nuovo circuito ExpressRoute con larghezza di banda inferiore ed eliminare il circuito precedente.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Come si modifica la larghezza di banda di un circuito ExpressRoute?

È possibile aggiornare la larghezza di banda del circuito ExpressRoute usando l'API REST dedicata o il cmdlet di PowerShell.

## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>Che cos'è ExpressRoute Premium?

ExpressRoute Premium è una raccolta delle funzionalità seguenti:

* Aumento del limite delle tabelle di routing da 4000 a 10.000 route per il peering privato.
* Incremento del numero di reti virtuali e connessioni di Copertura globale di ExpressRoute che possono essere abilitate per un circuito ExpressRoute (il valore predefinito è 10). Per altre informazioni, vedere la tabella [Limiti di ExpressRoute](#limits).
* Connettività a Office 365
* Connettività globale sulla rete di base Microsoft. È ora possibile collegare una VNet in un'area geopolitica a un circuito ExpressRoute in un'altra area.<br>
    **Esempi:**

    *  È possibile collegare una VNet creata in Europa occidentale a un circuito ExpressRoute creato a Silicon Valley. 
    *  Nel peering Microsoft vengono pubblicati i prefissi di altre aree geopolitiche, in modo che sia possibile, ad esempio, connettersi a SQL Azure in Europa occidentale da un circuito di Silicon Valley.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Quante reti virtuali e connessioni di Copertura globale di ExpressRoute è possibile abilitare su un circuito ExpressRoute se è stato abilitato ExpressRoute Premium?

Le tabelle seguenti illustrano i limiti di ExpressRoute e il numero di reti virtuali e connessioni di Copertura globale di ExpressRoute per ogni circuito ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Come si abilita ExpressRoute Premium?

Le funzionalità di ExpressRoute Premium possono essere abilitate quando si attiva la funzionalità e possono essere arrestate tramite l'aggiornamento dello stato del circuito. È possibile abilitare ExpressRoute Premium in fase di creazione del circuito oppure chiamare l'API REST o il cmdlet di PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Come si disabilita ExpressRoute Premium?

È possibile disabilitare ExpressRoute Premium chiamando l'API REST o il cmdlet di PowerShell. È necessario assicurarsi di avere ridimensionato le esigenze di connettività per soddisfare i limiti predefiniti prima di disabilitare ExpressRoute Premium. Se l'uso supera i limiti predefiniti, la richiesta di disabilitazione di ExpressRoute Premium avrà esito negativo.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>È possibile scegliere solo alcune funzionalità di ExpressRoute Premium?

No. Non è possibile selezionare le funzionalità. Quando si attiva ExpressRoute Premium, vengono abilitate tutte le funzionalità.

### <a name="how-much-does-expressroute-premium-cost"></a>Quanto costa ExpressRoute Premium?

Per informazioni sui costi, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>È necessario un pagamento aggiuntivo per ExpressRoute Premium, oltre agli addebiti per la versione standard di ExpressRoute?

Sì. Gli addebiti per ExpressRoute Premium vengono sommati agli addebiti per ExpressRoute e agli addebiti richiesti dal provider di connettività.

## <a name="expressroute-local"></a>ExpressRoute Local
### <a name="what-is-expressroute-local"></a>Che cos'è ExpressRoute Local?
ExpressRoute Local è uno SKU del circuito ExpressRoute che si aggiunge agli SKU Standard e Premium. Una funzionalità chiave di Local è che questo circuito in una località di peering ExpressRoute fornisce accesso solo a una o due aree di Azure nella stessa metropolitana o in una vicina. Al contrario, un circuito Standard offre accesso a tutte le aree di Azure in un'area geopolitica, mentre un circuito Premium fornisce accesso a tutte le aree di Azure a livello globale. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Quali sono i vantaggi di ExpressRoute Local?
Anche se per il circuito ExpressRoute Standard o Premium è necessario pagare il trasferimento dei dati in uscita, per il circuito ExpressRoute Local non occorre farlo separatamente. In altre parole, il prezzo di ExpressRoute Local include le tariffe per il trasferimento dei dati. ExpressRoute Local è una soluzione più economica se si dispone di una grande quantità di dati da trasferire ed è possibile spostare i dati tramite una connessione privata a una località di peering ExpressRoute vicino alle aree di Azure desiderate. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Quali funzionalità sono disponibili e quali non sono disponibili in ExpressRoute Local?
Un circuito ExpressRoute Local ha la stessa serie di funzionalità del circuito Standard, tranne:
* Ambito di accesso alle aree di Azure come descritto in precedenza
* Copertura globale di ExpressRoute non è disponibile nel circuito Local

ExpressRoute Local ha anche gli stessi limiti di Standard per quanto riguarda le risorse (ad esempio il numero di reti virtuali per circuito). 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Dove è disponibile ExpressRoute Local e a quali aree di Azure viene mappata ogni località di peering?
ExpressRoute Local è disponibile nelle località di peering in cui ci sono una o due aree di Azure vicine. Non è disponibile in una località di peering in cui non ci sono aree di Azure nello stato, nella provincia, nel paese o nella regione. Vedere i mapping esatti nella [pagina Località](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>ExpressRoute per Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Come si crea un circuito di ExpressRoute per la connessione ai servizi di Office 365?

1. Per assicurarsi di soddisfare i requisiti previsti, vedere la pagina sui [prerequisiti di ExpressRoute](expressroute-prerequisites.md).
2. Per assicurarsi di soddisfare le esigenze specifiche a livello di connettività, vedere l'elenco di provider di servizi e località nell'articolo [Partner e località per ExpressRoute](expressroute-locations.md).
3. Per pianificare i requisiti relativi alla capacità, vedere la pagina relativa alla [pianificazione e al perfezionamento delle prestazioni di rete per Office 365](https://aka.ms/tune/).
4. Seguire la procedura indicata nei flussi di lavoro per configurare la connettività dei [flussi di lavoro ExpressRoute per provisioning e stati di un circuito](expressroute-workflows.md).

> [!IMPORTANT]
> Quando si configura la connettività ai servizi di Office 365, assicurarsi che sia stato abilitato il componente aggiuntivo ExpressRoute Premium.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>I circuiti di ExpressRoute esistenti possono supportare la connettività ai servizi di Office 365?

Sì. È possibile configurare i circuiti ExpressRoute esistenti per supportare la connettività ai servizi di Office 365. Assicurarsi di avere una capacità sufficiente a connettersi ai servizi di Office 365 e che sia stato abilitato il componente aggiuntivo Premium. [Pianificazione e al perfezionamento delle prestazioni di rete per Office 365](https://aka.ms/tune/) aiuta a pianificare le esigenze di connettività. Vedere inoltre [Creare e modificare un circuito ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>A quali servizi di Office 365 è possibile accedere tramite una connessione ExpressRoute?

Per un elenco aggiornato dei servizi supportati con ExpressRoute, vedere la pagina [URL e intervalli di indirizzi IP per Office 365](https://aka.ms/o365endpoints).

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Quanto costa ExpressRoute per i servizi di Office 365?

Per i servizi di Office 365 è necessario abilitare il componente aggiuntivo Premium. Vedere la [pagina dei dettagli sui prezzi](https://azure.microsoft.com/pricing/details/expressroute/) per informazioni sui prezzi.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>In quali aree è supportato ExpressRoute per Office 365?

Vedere [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md) per ottenere informazioni.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>È possibile accedere a Office 365 tramite Internet anche se ExpressRoute è stato configurato per l'organizzazione specifica?

Sì. Gli endpoint del servizio di Office 365 sono raggiungibili tramite Internet anche se ExpressRoute è stato configurato per la rete specifica. Verificare con il team di rete dell'organizzazione se la rete della propria sede è configurata per la connessione ai servizi di Office 365 attraverso ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Come si pianifica la disponibilità elevata per il traffico di rete di Office 365 in Azure ExpressRoute?
Per consigli, vedere [Disponibilità elevata e failover con Azure ExpressRoute](https://aka.ms/erhighavailability).

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>È possibile accedere ai servizi US Government Community (GCC) di Office 365 tramite un circuito ExpressRoute di Azure per enti pubblici statunitensi?

Sì. Gli endpoint del servizio GCC di Office 365 sono raggiungibili tramite ExpressRoute di Azure US Gov. Tuttavia, è innanzitutto necessario aprire un ticket di supporto nel portale di Azure per fornire i prefissi che si desidera segnalare a Microsoft. Verrà stabilita la connettività ai servizi GCC di Office 365 dopo aver risolto il ticket di supporto. 

## <a name="route-filters-for-microsoft-peering"></a>Filtri di route per il peering Microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Sto attivando il peering Microsoft per la prima volta, quale sarà la route visualizzata?

Non verrà visualizzata nessuna route. È necessario allegare un filtro di route per il circuito per avviare gli annunci di prefisso. Per istruzioni, vedere [Configurare i filtri di route per il peering Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Ho attivato il peering Microsoft e ora sto cercando di selezionare Exchange Online, ma compare un messaggio di errore che indica che non sono autorizzato a eseguire l'operazione.

Quando si usano filtri di route, tutti i clienti possono attivare il peering Microsoft. Tuttavia, per l'uso dei servizi di Office 365, è comunque necessario ottenere l'autorizzazione da Office 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Il peering Microsoft è stato abilitato prima del 1° agosto 2017. Come posso usare al meglio i filtri di route?

Il circuito esistente continuerà ad annunciare i prefissi per Office 365. Per aggiungere annunci di prefissi pubblici di Azure tramite lo stesso peering Microsoft, è possibile creare un filtro di route, selezionare i servizi che si vuole annunciare (inclusi i servizi di Office 365 necessari) e collegare il filtro al peering Microsoft in uso. Per istruzioni, vedere [Configurare i filtri di route per il peering Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Ho un peering Microsoft in una località, ora sto cercando di abilitarlo in un'altra località e non vengono visualizzati i prefissi.

* Il peering Microsoft dei circuiti ExpressRoute che sono stati configurati prima del 1° agosto 2017, avranno tutti i prefissi di servizio pubblicati tramite il peering Microsoft, anche se non sono definiti i filtri di route.

* Il peering Microsoft dei circuiti ExpressRoute che vengono configurati dopo il 1° agosto 2017 non avrà alcun prefisso annunciato fino a quando non viene associato un filtro di route per il circuito. Per impostazione predefinita, non verrà visualizzato alcun prefisso.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Copertura globale

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
