---
title: Domande frequenti - Azure ExpressRoute | Microsoft Docs
description: In Domande frequenti su ExpressRoute sono disponibili informazioni su servizi di Azure supportati, costi, dati e connessioni, Contratto di servizio, provider e località, larghezza di banda e altri dettagli tecnici.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 845c53ec970777901ae8d1c0abf5032ac705d3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264921"
---
# <a name="expressroute-faq"></a>Domande frequenti su ExpressRoute

## <a name="what-is-expressroute"></a>Che cos'è ExpressRoute?

ExpressRoute è un servizio di Azure che permette di creare connessioni private tra i data center Microsoft e l'infrastruttura disponibile localmente o in una struttura con percorso condiviso. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica e offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quali sono i vantaggi dell'uso di ExpressRoute e delle connessioni di rete private?

Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. Esse offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e latenze minori e coerenti rispetto alle connessioni Internet tradizionali. In alcuni casi, l'uso di connessioni ExpressRoute per trasferire dati tra dispositivi locali e Azure può produrre vantaggi significativi in termini di costi.

### <a name="where-is-the-service-available"></a>Dov'è disponibile il servizio?

Per informazioni sulla località e la disponibilità del servizio, vedere [Partner e località per ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>In che modo è possibile usare ExpressRoute per connettersi a Microsoft se non è disponibile una relazione con uno dei partner del gestore ExpressRoute?

Si può selezionare un gestore regionale e connessioni Ethernet via terra in una delle sedi supportate del provider di Exchange. È quindi possibile connettersi a Microsoft nella sede del provider. Per verificare se il provider di servizio è presente in una delle località di Exchange, vedere l'ultima sezione della pagina [Partner e località per ExpressRoute](expressroute-locations.md) . Sarà quindi possibile ordinare un circuito ExpressRoute tramite il provider di servizi per connettersi ad Azure.

### <a name="how-much-does-expressroute-cost"></a>Quanto costa ExpressRoute?

Per informazioni sui prezzi, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se si acquista un circuito ExpressRoute di una determinata larghezza di banda, la connessione VPN acquistata dal provider di servizi di rete deve essere della stessa velocità?

No. È possibile acquistare una connessione VPN di qualsiasi velocità dal provider di servizi. Tuttavia, la connessione ad Azure è limitata alla larghezza di banda del circuito ExpressRoute acquistato.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Se si paga per un circuito ExpressRoute di una determinata larghezza di banda, si ha la possibilità di potenziarlo fino a velocità più elevate, se necessario?

Sì. I circuiti ExpressRoute sono configurati per consentire di potenziare fino al doppio il limite di larghezza di banda acquistato, senza alcun costo aggiuntivo. Contattare il provider di servizi per vedere se supporta questa funzionalità. Non si tratta di un periodo di tempo prolungato e non è garantito.  Se il traffico viene trasmesso attraverso un gateway ExpressRoute, la larghezza di banda per lo SKU è fissa e non può essere scaricata.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Si possono usare contemporaneamente la stessa connessione di rete privata con la rete virtuale e altri servizi di Azure?

Sì. Dopo l'installazione, il circuito ExpressRoute consente di accedere ai servizi all'interno di una rete virtuale e ad altri servizi di Azure contemporaneamente. La connessione alle reti virtuali viene eseguita tramite il percorso di peering privato e ad altri servizi tramite il percorso di peering Microsoft.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>In che modo reti virtuali si annuncia sul peering privato ExpressRoute?

Il gateway di ExpressRoute annuncia gli *spazi di indirizzi* della VNet di Azure, non è possibile includere/escludere a livello di subnet. Si tratta sempre dello spazio degli indirizzi VNet annunciato. Inoltre, se viene usato il peering VNet e il VNet con peering ha abilitato "use Remote Gateway", verrà annunciato anche lo spazio degli indirizzi del VNet con peering.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Quanti prefissi possono essere annunciati da un VNet locale al peering privato ExpressRoute?

È disponibile un massimo di 200 prefissi annunciati in una singola connessione ExpressRoute o tramite il peering VNet con il transito del gateway. Se, ad esempio, si dispone di 199 di spazi di indirizzi in un singolo VNet connesso a un circuito ExpressRoute, tutti i 199 di tali prefissi verranno annunciati in locale. In alternativa, se si dispone di un VNet abilitato per consentire il transito del gateway con 1 spazio di indirizzi e 150 spoke reti virtuali abilitato con l'opzione "Consenti gateway remoto", il VNet distribuito con il gateway annuncia i prefissi 151 in locale.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Cosa accade se si supera il limite di prefisso per una connessione ExpressRoute?

La connessione tra il circuito ExpressRoute e il gateway (e il reti virtuali con peering che usa il transito del gateway, se applicabile) si arresterà. Verrà ristabilito quando il limite del prefisso non verrà più superato.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>È possibile filtrare le route provenienti dalla rete locale?

L'unico modo per filtrare/includere le route è il router perimetrale locale. Le route definite dall'utente possono essere aggiunte in VNet per influire su un routing specifico, ma questo sarà statico e non fa parte dell'annuncio BGP.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute offre un contratto di servizio?

Per informazioni, vedere [Contratto di servizio di ExpressRoute](https://azure.microsoft.com/support/legal/sla/).

## <a name="supported-services"></a>Servizi supportati

ExpressRoute supporta [tre domini di routing](expressroute-circuit-peerings.md) per diversi tipi di servizi: peering privato, peering Microsoft e peering pubblico (deprecato).

### <a name="private-peering"></a>Peering privato

**Supportato**

* Reti virtuali, inclusi tutti i servizi cloud e tutte le macchine virtuali

### <a name="microsoft-peering"></a>Peering Microsoft

Se il circuito ExpressRoute è abilitato per il peering Microsoft di Azure, è possibile accedere agli [intervalli di indirizzi IP pubblici](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) usati in Azure tramite il circuito. Il peering Microsoft di Azure fornirà l'accesso ai servizi attualmente ospitati in Azure (con restrizioni geografiche a seconda dello SKU del circuito). Per convalidare la disponibilità per un servizio specifico, è possibile controllare la documentazione relativa a tale servizio per verificare se è stato pubblicato un intervallo riservato per il servizio. Cercare quindi gli intervalli IP del servizio di destinazione e confrontarli con gli intervalli elencati negli [intervalli IP di Azure e nei tag del servizio-file XML del cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519). In alternativa, è possibile aprire un ticket di supporto per il servizio in questione per chiarimenti.

**Supportato**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI-disponibile tramite una community regionale di Azure, vedere [qui](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) per informazioni su come individuare l'area del tenant di Power bi.
* Azure Active Directory
* [Desktop virtuale Windows](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (community di Servizi globali di Azure)
* Indirizzi IP pubblici di Azure per IaaS (macchine virtuali, gateway di rete virtuale, servizio di bilanciamento del carico e così via)  
* Sono supportate anche la maggior parte degli altri servizi di Azure. Contattare direttamente il servizio che si vuole usare per verificarne il supporto.

**Non supportato:**

* RETE CDN
* Frontdoor di Azure
* Server di autenticazione a più fattori (legacy)
* Gestione traffico

### <a name="public-peering"></a>Peering pubblico

Il peering pubblico è stato disabilitato sui circuiti nuovi di ExpressRoute. I servizi di Azure sono ora disponibili nel peering Microsoft. Se un circuito creato prima del peering pubblico è deprecato, è possibile scegliere di usare il peering Microsoft o il peering pubblico, a seconda dei servizi desiderati.

Per altre informazioni e procedure di configurazione per il peering pubblico, vedere [peering pubblico di ExpressRoute](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Perché viene visualizzato lo stato ' prefissi pubblici annunciati ' come ' convalida necessaria ' durante la configurazione del peering Microsoft?

Microsoft verifica se i ' prefissi pubblici annunciati ' è peer ASN ' (o ' Customer ASN ') specificati sono assegnati all'utente nel registro di sistema di routing Internet. Se si ricevono i prefissi pubblici da un'altra entità e se l'assegnazione non viene registrata con il registro di routing, la convalida automatica non verrà completata e richiederà la convalida manuale. Se la convalida automatica ha esito negativo, verrà visualizzato il messaggio "convalida necessaria".

Se viene visualizzato il messaggio "convalida richiesta", raccogliere i documenti che mostrano i prefissi pubblici vengono assegnati all'organizzazione dall'entità elencata come proprietario dei prefissi nel registro di sistema di routing e inviare questi documenti per la convalida manuale aprendo un ticket di supporto, come illustrato di seguito.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Dynamics 365 è supportato in ExpressRoute?

Gli ambienti Dynamics 365 e Common Data Service (CDS) sono ospitati in Azure e quindi i clienti traggono vantaggio dal supporto ExpressRoute sottostante per le risorse di Azure. È possibile connettersi agli endpoint del servizio se il filtro del router include le aree di Azure in cui sono ospitati gli ambienti Dynamics 365/CDS.

> [!NOTE]
> [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) **non** è necessario per la connettività Dynamics 365 tramite Azure ExpressRoute.

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

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Ricerca per categorie implementare la ridondanza nel peering privato?

Più circuiti ExpressRoute da località di peering diverse possono essere connessi alla stessa rete virtuale per offrire disponibilità elevata nel caso in cui un singolo circuito non sia più disponibile. È quindi possibile [assegnare pesi più elevati](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) alla connessione locale per preferire un circuito specifico. Si consiglia vivamente ai clienti di configurare almeno due circuiti ExpressRoute per evitare singoli punti di errore. 

Vedere [qui](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) per la progettazione per la disponibilità elevata e [qui](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) per la progettazione per il ripristino di emergenza.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Come implementare la ridondanza nel peering Microsoft?

Si consiglia vivamente quando i clienti usano il peering Microsoft per accedere ai servizi pubblici di Azure, ad esempio archiviazione di Azure o Azure SQL, nonché ai clienti che usano il peering Microsoft per Office 365 che implementano più circuiti in posizioni di peering diverse per evitare singoli punti di errore. I clienti possono annunciare lo stesso prefisso in entrambi i circuiti e usare [come percorso in sospeso](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) o annunciare prefissi diversi per determinare il percorso dall'ambiente locale.

Per la progettazione per la disponibilità elevata, vedere [qui](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) .

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Come è possibile garantire la disponibilità elevata in una rete virtuale connessa a ExpressRoute?

È possibile ottenere la disponibilità elevata connettendo alla rete virtuale circuiti ExpressRoute presenti in posizioni di peering diverse, ad esempio Singapore e Singapore2. Se si arresta un circuito ExpressRoute, la connettività eseguirà il failover su un altro circuito ExpressRoute. Per impostazione predefinita, il traffico in uscita dalla rete virtuale viene instradato sul routing ECMP (Equal-Cost-Multi-Path). È possibile usare il peso della connessione per preferire un circuito a un altro. Per altre informazioni, vedere [Optimizing ExpressRoute Routing](expressroute-optimize-routing.md) (Ottimizzazione del routing di ExpressRoute).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Ricerca per categorie assicurarsi che il traffico destinato ai servizi pubblici di Azure, ad esempio archiviazione di Azure e Azure SQL sul peering Microsoft o sul peering pubblico, sia preferibile nel percorso ExpressRoute?

È necessario implementare l'attributo *preferenza locale* nei router per assicurarsi che il percorso dall'ambiente locale ad Azure sia sempre preferito nei circuiti ExpressRoute in uso.

Per [ulteriori informazioni,](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) vedere la selezione del percorso BGP e le configurazioni del router comune. 

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

Sì. È possibile avere più circuiti ExpressRoute con provider di servizi uguali o diversi. Se nell'area metropolitana sono presenti più località peer per ExpressRoute e i circuiti vengono creati in località peer diverse, è possibile collegarli alla stessa rete virtuale. Se i circuiti vengono creati nella stessa posizione di peering, è possibile collegare fino a 4 circuiti alla stessa rete virtuale.

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

Sì. È possibile collegare una singola rete virtuale con un massimo di quattro circuiti ExpressRoute in posizioni di peering uguali o diverse. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Si può accedere a Internet usando le reti virtuali connesse a circuiti ExpressRoute?

Sì. Se non è stata eseguita la pubblicazione di route predefinite (0.0.0.0/0) o prefissi di route Internet tramite la sessione BGP, è possibile connettersi a Internet da una rete virtuale collegata a un circuito ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Si può bloccare la connettività Internet per le reti virtuali connesse a circuiti ExpressRoute?

Sì. È possibile pubblicare route predefinite (0.0.0.0/0) per bloccare tutta la connettività Internet per le macchine virtuali distribuite in una rete virtuale e indirizzare tutto il traffico in uscita attraverso il circuito ExpressRoute.

Se si pubblicano route predefinite, il traffico verso i servizi offerti tramite peering Microsoft, ad esempio l'archiviazione di Azure e database SQL, viene forzato verso l'istanza locale. Sarà necessario configurare i router in modo che restituiscano traffico ad Azure tramite il percorso di peering Microsoft su Internet. Se è stato abilitato un endpoint di servizio per il servizio, il traffico verso il servizio non viene forzato in locale, ma rimane all'interno della rete backbone di Azure. Per altre informazioni sugli endpoint di servizio, vedere [endpoint del servizio rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Le reti virtuali collegate allo stesso circuito ExpressRoute possono comunicare tra loro?

Sì. Le macchine virtuali distribuite in reti virtuali connesse allo stesso circuito ExpressRoute possono comunicare tra loro.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>È possibile usare la connettività da sito per reti virtuali insieme a ExpressRoute?

Sì. ExpressRoute può coesistere con VPN da sito a sito. Vedere [Configurare connessioni coesistenti ExpressRoute e da sito a sito](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Perché esiste un indirizzo IP pubblico associato al gateway ExpressRoute in una rete virtuale?

L'indirizzo IP pubblico viene usato solo per la gestione interna e non costituisce un rischio per la sicurezza della rete virtuale.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Sono previsti limiti per i numeri di route pubblicabili?

Sì. Sono accettati al massimo 4000 prefissi di route per il peering privato e 200 per il peering Microsoft. Se si abilita la funzionalità Premium di ExpressRoute, sarà possibile aumentare questo valore fino a 10.000 route per il peering privato.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Sono previste limitazioni per gli intervalli IP pubblicabili tramite la sessione BGP?

I prefissi privati (RFC1918) non sono accettati nella sessione BGP per il peering Microsoft. Accettiamo qualsiasi dimensione del prefisso (fino a/32) sia per il peering privato che per Microsoft.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Cosa succede se si superano i limiti per BGP?

Le sessioni BGP saranno rimosse. Saranno ripristinate quando il numero di prefissi tornerà sotto il limite consentito.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Che cos'è il tempo di attesa BGP ExpressRoute? È possibile regolarlo?

Il tempo di attesa è 180. I messaggi keep-alive vengono inviati ogni 60 secondi. Queste sono impostazioni fisse sul lato Microsoft che non è possibile modificare. È possibile configurare diversi timer, e i parametri della sessione BGP verranno negoziati di conseguenza.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>È possibile modificare la larghezza di banda di un circuito ExpressRoute?

Sì, è possibile provare ad aumentare la larghezza di banda del circuito ExpressRoute nel portale di Azure o con PowerShell. Se è disponibile capacità sulla porta fisica in cui è stato creato il circuito, la modifica ha esito positivo. 

Se la modifica ha esito negativo, significa che la capacità residua sulla porta corrente non è sufficiente e che è necessario creare un nuovo circuito ExpressRoute con maggiore larghezza di banda, oppure che la località non ha altra capacità. In quest'ultimo caso non è possibile aumentare la larghezza di banda. 

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

## <a name="expressroute-local"></a>ExpressRoute locale
### <a name="what-is-expressroute-local"></a>Che cos'è ExpressRoute local?
ExpressRoute local è uno SKU del circuito ExpressRoute, oltre allo SKU standard e allo SKU Premium. Una funzionalità chiave di local è che un circuito locale in una località di peering ExpressRoute consente di accedere solo a una o due aree di Azure in o vicino alla stessa metropolitana. Al contrario, un circuito standard consente di accedere a tutte le aree di Azure in un'area geopolitica e un circuito Premium a tutte le aree di Azure a livello globale. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Quali sono i vantaggi di ExpressRoute local?
Anche se è necessario pagare il trasferimento dei dati in uscita per il circuito ExpressRoute standard o Premium, non si paga il trasferimento dei dati in uscita separatamente per il circuito locale di ExpressRoute. In altre parole, il prezzo di ExpressRoute local include tariffe per il trasferimento dei dati. ExpressRoute local è una soluzione più economica se si dispone di una grande quantità di dati da trasferire ed è possibile trasferire i dati tramite una connessione privata a una località di peering ExpressRoute vicino alle aree di Azure desiderate. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Quali funzionalità sono disponibili e cosa non si trova in ExpressRoute local?
Rispetto a un circuito ExpressRoute standard, un circuito locale ha lo stesso set di funzionalità eccetto:
* Ambito di accesso alle aree di Azure come descritto in precedenza
* ExpressRoute Copertura globale non è disponibile in locale

ExpressRoute local ha anche gli stessi limiti per le risorse (ad esempio, il numero di reti virtuali per circuito) come standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Dove è disponibile ExpressRoute locale e a quali aree di Azure è mappata ogni località di peering?
ExpressRoute local è disponibile nelle località di peering in cui una o due aree di Azure sono vicine. Non è disponibile in una località di peering in cui non esiste alcuna area di Azure in tale stato o provincia o paese. Vedere i mapping esatti nella [pagina percorsi](expressroute-locations-providers.md).  

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

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>I circuiti ExpressRoute esistenti possono supportare la connettività ai servizi di Office 365?

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

Il circuito esistente continuerà a annunciare i prefissi per Office 365. Se si vuole aggiungere annunci di prefissi pubblici di Azure tramite lo stesso peering Microsoft, è possibile creare un filtro di route, selezionare i servizi necessari annunciati (inclusi i servizi di Office 365 necessari) e collegarlo al peering Microsoft. Per istruzioni, vedere [Configurare i filtri di route per il peering Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Ho un peering Microsoft in una località, ora sto cercando di abilitarlo in un'altra località e non vengono visualizzati i prefissi.

* Il peering Microsoft dei circuiti ExpressRoute che sono stati configurati prima del 1° agosto 2017, avranno tutti i prefissi di servizio pubblicati tramite il peering Microsoft, anche se non sono definiti i filtri di route.

* Il peering Microsoft dei circuiti ExpressRoute che vengono configurati dopo il 1° agosto 2017 non avrà alcun prefisso annunciato fino a quando non viene associato un filtro di route per il circuito. Per impostazione predefinita, non verrà visualizzato alcun prefisso.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Copertura globale

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
