---
title: Panoramica dei servizi di rete di Azure
description: 'Informazioni sui servizi di rete in Azure e sulle relative funzionalità: Servizi di connettività, servizi di protezione delle applicazioni, servizi per la distribuzione di applicazioni e monitoraggio della rete.'
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 42d3360b7defaab2ff0a62dc125a213860b13a6a
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133602"
---
# <a name="azure-networking-services-overview"></a>Panoramica dei servizi di rete di Azure

I servizi di rete in Azure offrono un'ampia gamma di funzionalità di rete che possono essere usate insieme o separatamente. Per altre informazioni, fare clic su una qualsiasi delle funzionalità chiave seguenti:
- [**Servizi di connettività**](#connect): connettere le risorse di Azure e le risorse locali usando una o più combinazioni di questi servizi di rete in Azure-rete virtuale (VNet), WAN virtuale, ExpressRoute, gateway VPN, gateway NAT di rete virtuale, DNS di Azure, servizio di peering e Azure Bastion.
- [**Servizi di protezione delle applicazioni**](#protect) Proteggi le tue applicazioni usando una combinazione di questi servizi di rete in Azure-collegamento privato, protezione DDoS, firewall, gruppi di sicurezza di rete, Web Application Firewall e endpoint di rete virtuale.
- [**Servizi**](#deliver) per la distribuzione di applicazioni Distribuisci le applicazioni nella rete di Azure usando una o più combinazioni di questi servizi di rete in Azure-rete per la distribuzione di contenuti (CDN), servizio di Azure front door, gestione traffico, gateway applicazione, Internet Analyzer e Load Balancer.
- [**Monitoraggio della rete**](#monitor) : monitorare le risorse di rete usando una o più combinazioni di questi servizi di rete in Azure-Network Watcher, monitoraggio ExpressRoute, monitoraggio di Azure o punto di accesso terminale VNET (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Servizi di connettività
 
Questa sezione descrive i servizi che forniscono la connettività tra le risorse di Azure, la connettività da una rete locale alle risorse di Azure e la creazione di Branch per la connettività a branch in Azure-rete virtuale (VNet), WAN virtuale, ExpressRoute, gateway VPN, gateway NAT di rete virtuale, DNS di Azure, servizio peering di Azure e Bastion di Azure.

|Servizio|Perché usare?|Scenari|
|---|---|---|
|[Rete virtuale](#vnet)|Consente alle risorse di Azure di comunicare in modo sicuro tra loro, con Internet e con le reti locali.| <p>[Filtrare il traffico di rete](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Indirizzare il traffico di rete](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Limitare l'accesso di rete alle risorse](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Connettere reti virtuali](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Estende le reti locali nel cloud Microsoft tramite una connessione privata facilitata da un provider di connettività.|<p>[Creare e modificare un circuito ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Creare e modificare i peering per un circuito ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Collegare una rete virtuale a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Configurare e gestire i filtri di route per circuiti ExpressRoute](../expressroute/how-to-routefilter-portal.md)</p>|
|[Gateway VPN](#vpngateway)|Invia il traffico crittografato tra una rete virtuale di Azure e una posizione locale tramite la rete Internet pubblica.|<p>[Connessioni da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Connessioni da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Connessioni da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Rete WAN virtuale](#virtualwan)|Consente di ottimizzare e automatizzare la connettività del ramo a e tramite Azure. Le aree di Azure fungono da hub a cui è possibile scegliere di connettere i rami.|<p>[Connessioni da sito a sito](../virtual-wan/virtual-wan-site-to-site-portal.md), [connessioni ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Ospita domini DNS che forniscono la risoluzione dei nomi usando Microsoft Azure infrastruttura.|<p>[Ospitare il dominio in DNS di Azure](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Creare record DNS per un'app Web](../dns/dns-web-sites-custom-domain.md)</p> <p>[Creare un record alias per gestione traffico](../dns/tutorial-alias-tm.md)</p> <p>[Creare un record alias per l'indirizzo IP pubblico](../dns/tutorial-alias-pip.md)</p> <p>[Creare un record alias per il record di risorse della zona](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Configurare la connettività RDP/SSH sicura e semplice per le macchine virtuali direttamente nel portale di Azure tramite TLS. Quando ci si connette tramite Azure Bastion, le macchine virtuali non necessitano di un indirizzo IP pubblico|<p>[Creare un host Azure Bastion](../bastion/bastion-create-host-portal.md)</p><p>[Connettersi con SSH a una VM Linux](../bastion/bastion-connect-vm-ssh.md)</p><p>[Connettersi tramite RDP a una macchina virtuale Windows](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Gateway NAT della rete virtuale](#nat)|Creare un gateway NAT per fornire la connettività in uscita per una macchina virtuale.|<p>[Creare un gateway NAT](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Servizio peering di Azure (anteprima)](#azurepeeringservice)|Collaborare con i provider di servizi per il routing ottimale e affidabile al cloud Microsoft attraverso la rete pubblica.|<p>[Registrare il servizio di peering di Azure](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Rete virtuale

Rete virtuale di Azure (VNet) è il blocco predefinito fondamentale per la rete privata in Azure. È possibile usare un reti virtuali per:
- **Comunicazione tra le risorse di Azure**: è possibile distribuire VM e diversi altri tipi di risorse di Azure in una rete virtuale, ad esempio app Azure ambienti del servizio, Azure Kubernetes Service (AKS) e set di scalabilità di macchine virtuali di Azure. Per visualizzare un elenco completo delle risorse di Azure che è possibile distribuire in una rete virtuale, vedere [Integrazione del servizio di rete virtuale](../virtual-network/virtual-network-for-azure-services.md).
- **Comunicare tra loro**: è possibile connettere le reti virtuali tra loro, consentendo alle risorse in entrambe le reti virtuali di comunicare tra loro, usando il peering di rete virtuale. Le reti virtuali connesse possono essere in aree di Azure uguali o diversi. Per altre informazioni, vedere [peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md).
- **Comunicazione con Internet**: per impostazione predefinita, tutte le risorse in un VNet possono comunicare in uscita verso Internet. Per la comunicazione in ingresso con una risorsa, è possibile assegnarle un indirizzo IP pubblico o un servizio di bilanciamento del carico pubblico. Per gestire le connessioni in uscita, è anche possibile usare [indirizzi IP pubblici](../virtual-network/virtual-network-public-ip-address.md) o [Load Balancer](../load-balancer/load-balancer-overview.md) pubblici.
- **Comunicare con le reti locali**: è possibile connettere i computer e le reti locali a una rete virtuale usando il [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ExpressRoute](../expressroute/expressroute-introduction.md).

Per altre informazioni, vedere informazioni su [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata facilitata da un provider di connettività. La connessione è privata. Il traffico non passa da Internet. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e Dynamics 365.  Per ulteriori informazioni, vedere [che cos'è ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>Gateway VPN
Il gateway VPN consente di creare connessioni cross-premise crittografate alla rete virtuale da percorsi locali o di creare connessioni crittografate tra reti virtuali. Sono disponibili diverse configurazioni per le connessioni del gateway VPN, ad esempio da sito a sito, da punto a sito o da VNet a VNet.
Il diagramma seguente illustra più connessioni VPN da sito a sito alla stessa rete virtuale.

![Connessioni gateway VPN di Azure da sito a sito](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Per altre informazioni sui diversi tipi di connessioni VPN, vedere [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Rete WAN virtuale
La rete WAN virtuale di Azure è un servizio di rete che offre connettività tra rami ottimizzata e automatizzata verso e tramite Azure. Le aree di Azure fungono da hub a cui è possibile scegliere di connettere i rami. È possibile sfruttare il backbone di Azure per anche connettere i rami e sfruttare i vantaggi della connettività da ramo a VNet. La rete WAN virtuale di Azure combina molti servizi di connettività cloud di Azure, ad esempio VPN da sito a sito, ExpressRoute, VPN utente da punto a sito in un'unica interfaccia operativa. La connettività con le reti virtuali di Azure viene stabilita tramite connessioni di rete virtuali. Per altre informazioni, vedere [che cos'è la rete WAN virtuale di Azure?](../virtual-wan/virtual-wan-about.md).

![Diagramma della rete WAN virtuale](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
DNS di Azure è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. L'hosting dei domini in Azure consente di gestire i record DNS usando gli stessi strumenti, credenziali, API e fatturazione usati per altri servizi Azure. Per altre informazioni, vedere informazioni su [DNS di Azure](../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion è un nuovo servizio PaaS completamente gestito dalla piattaforma di cui è possibile eseguire il provisioning all'interno della rete virtuale. Offre connettività RDP/SSH sicura e ininterrotta con le macchine virtuali direttamente nel portale di Azure tramite TLS. Quando ci si connette tramite Azure Bastion, per le macchine virtuali non è necessario un indirizzo IP pubblico. Per altre informazioni, vedere [che cos'è Azure Bastion?](../bastion/bastion-overview.md).

![Architettura Bastion di Azure](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Gateway NAT della rete virtuale
NAT (Network Address Translation) di rete virtuale semplifica la connettività Internet solo in uscita per le reti virtuali. Quando viene configurato in una subnet, per tutta la connettività in uscita vengono usati gli indirizzi IP pubblici statici specificati. La connettività in uscita è possibile senza bilanciamento del carico o indirizzi IP pubblici collegati direttamente alle macchine virtuali. Per ulteriori informazioni, vedere [che cos'è il gateway NAT di rete virtuale?](../virtual-network/nat-overview.md) 

![Gateway NAT della rete virtuale](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Servizio peering di Azure
Il servizio peering di Azure migliora la connettività dei clienti ai servizi cloud Microsoft, ad esempio Office 365, Dynamics 365, Software as a Service (SaaS) Services, Azure o tutti i servizi Microsoft accessibili tramite la rete Internet pubblica. Per altre informazioni, vedere [che cos'è il servizio peering di Azure?](../peering-service/about.md).

## <a name="application-protection-services"></a><a name="protect"></a>Servizi di protezione delle applicazioni

Questa sezione descrive i servizi di rete in Azure che consentono di proteggere le risorse di rete. proteggere le applicazioni usando qualsiasi o una combinazione di questi servizi di rete in Azure-collegamento privato, protezione DDoS, firewall, gruppi di sicurezza di rete, Web Application Firewall e endpoint di rete virtuale.

|Servizio|Perché usare?|Scenario|
|---|---|---|
|[Protezione DDoS](#ddosprotection) |Disponibilità elevata per le applicazioni con protezione da addebiti eccessivi per il traffico IP|[Gestire la protezione DDoS di Azure](../virtual-network/manage-ddos-protection.md)|
|[Web Application Firewall](#waf)|<p>[Azure WAF con il gateway applicazione fornisce la](../web-application-firewall/ag/ag-overview.md) protezione a livello di area alle entità nello spazio degli indirizzi pubblico e privato</p><p>[Azure WAF con sportello anteriore](../web-application-firewall/afds/afds-overview.md) fornisce la protezione sul perimetro della rete agli endpoint pubblici.</p>|<p>[Configurare le regole di protezione bot](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Configurare il codice di risposta personalizzato](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Configurare le regole di restrizione IP](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Configura regola limite di velocità](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Firewall di Azure](#firewall)|Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti.|<p>[Distribuire un firewall di Azure in una VNET](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[-Distribuire un firewall di Azure in una rete ibrida](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrare il traffico in ingresso con il firewall di Azure DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Gruppi di sicurezza di rete](#nsg)|Controllo del nodo end distribuito completamente granulare alla macchina virtuale/subnet per tutti i flussi di traffico di rete|[Filtrare il traffico di rete con i gruppi di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)|
|[Endpoint servizio di rete virtuale](#serviceendpoints)|Consente di limitare l'accesso di rete ad alcune risorse dei servizi di Azure a una subnet di rete virtuale|[Limitare l'accesso alla rete alle risorse PaaS](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Collegamento privato](#privatelink)|Consente di accedere ai servizi PaaS di Azure, ad esempio archiviazione di Azure e database SQL, e ai servizi di proprietà/partner del cliente ospitati in Azure tramite un endpoint privato nella rete virtuale.|<p>[Creare un endpoint privato](../private-link/create-private-endpoint-portal.md)</p><p>[Creare un servizio Collegamento privato](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>Protezione DDoS 
[Protezione DDoS di Azure](../virtual-network/manage-ddos-protection.md) fornisce contromisure contro le minacce DDoS più sofisticate. Il servizio fornisce funzionalità avanzate di mitigazione DDoS per l'applicazione e le risorse distribuite nelle reti virtuali. Inoltre, i clienti che usano la protezione DDoS di Azure hanno accesso al supporto di risposta rapida DDoS per coinvolgere gli esperti DDoS durante un attacco attivo.

![Protezione DDoS](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall

Il [Web Application Firewall](../web-application-firewall/overview.md) (WAF) di Azure garantisce la protezione delle applicazioni Web da exploit e vulnerabilità Web comuni, come SQL injection e cross site scripting. Azure WAF offre la protezione predefinita da OWASP prime 10 vulnerabilità tramite regole gestite. Inoltre, i clienti possono configurare regole personalizzate, ovvero regole gestite dal cliente per fornire protezione aggiuntiva in base all'intervallo di indirizzi IP di origine e attributi di richiesta quali intestazioni, cookie, campi dati di form o parametri di stringa di query.

I clienti possono scegliere di distribuire [Azure WAF con il gateway applicazione](../application-gateway/waf-overview.md) , che fornisce la protezione a livello di area alle entità nello spazio degli indirizzi pubblico e privato. I clienti possono anche scegliere di distribuire [Azure WAF con la porta anteriore](../frontdoor/waf-overview.md) , che fornisce la protezione sul perimetro della rete agli endpoint pubblici.

![Web application firewall](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Firewall di Azure
Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. Con il firewall di Azure è possibile creare, applicare e registrare in modo centralizzato i criteri di connettività di rete e di applicazione tra sottoscrizioni e reti virtuali. Firewall di Azure usa un indirizzo IP pubblico statico per le risorse della rete virtuale consentendo ai firewall esterni di identificare il traffico proveniente dalla rete virtuale. 

Per ulteriori informazioni sul firewall di Azure, vedere la [documentazione del firewall di Azure](../firewall/overview.md).

![Panoramica del firewall](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Gruppi di sicurezza di rete
È possibile filtrare il traffico di rete da e verso le risorse di Azure in una rete virtuale di Azure con un gruppo di sicurezza di rete. Per altre informazioni, vedere [Panoramica della sicurezza](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Endpoint di servizio
Gli endpoint del servizio Rete virtuale estendono lo spazio di indirizzi privato della rete virtuale e l'identità della rete virtuale ai servizi di Azure tramite una connessione diretta. Gli endpoint consentono di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure. Per altre informazioni, vedere [Endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).

![Endpoint servizio di rete virtuale](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Collegamento privato di Azure
Il [collegamento privato di Azure](../private-link/private-link-overview.md) consente di accedere ai servizi PaaS di Azure, ad esempio archiviazione di Azure e al database SQL, e ai servizi di proprietà/partner del cliente ospitati in Azure tramite un endpoint privato nella rete virtuale.
Il traffico tra la rete virtuale e il servizio attraversa la rete del backbone Microsoft. L'esposizione del servizio sulla rete Internet pubblica non è più necessaria. È possibile creare un servizio Collegamento privato personale nella rete virtuale e distribuirlo ai clienti.

![Panoramica dell'endpoint privato](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Servizi per la distribuzione di applicazioni

Questa sezione descrive i servizi di rete in Azure che consentono di fornire applicazioni: Network Watcher, monitoraggio ExpressRoute, monitoraggio di Azure o punto di accesso terminale VNet (TAP).

|Servizio|Perché usare?|Scenario|
|---|---|---|
|[Rete per la distribuzione di contenuti (CDN)](#cdn)|Offre contenuto a larghezza di banda elevata agli utenti. CDNs archivia il contenuto memorizzato nella cache nei server perimetrali in posizioni di punti di presenza (POP) vicine agli utenti finali, per ridurre al minimo la latenza|<p>[Aggiungere la rete CDN a un'app Web](../cdn/cdn-add-to-web-app.md)</p> <p>[-Accedere ai BLOB di archiviazione usando un dominio personalizzato della rete CDN di Azure tramite HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Configurare HTTPS in un dominio personalizzato della rete CDN di Azure](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Servizio Frontdoor di Azure](#frontdoor)|Consente di definire, gestire e monitorare il routing globale per il traffico Web ottimizzando per prestazioni ottimali e failover globale istantaneo per la disponibilità elevata.|<p>[Aggiungere un dominio personalizzato al servizio Frontdoor di Azure](../frontdoor/front-door-custom-domain.md)</p> <p>[Configurare HTTPS per un dominio personalizzato di Frontdoor](../frontdoor/front-door-custom-domain-https.md)</p><p>[Configurare i criteri del firewall applicazione Web di filtro geografico](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Gestione traffico](#trafficmanager)|Distribuisce il traffico in base al DNS ai servizi nelle aree globali di Azure, garantendo al tempo stesso disponibilità elevata e velocità di risposta|<p> [Indirizzare il traffico per una bassa latenza](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Indirizzare il traffico a un endpoint prioritario](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Controllare il traffico con endpoint ponderati](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Instradare il traffico in base alla posizione geografica dell'endpoint](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Indirizzare il traffico in base alla subnet dell'utente](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Bilanciamento del carico](#loadbalancer)|Fornisce il bilanciamento del carico a livello di area tramite il routing del traffico tra le zone di disponibilità e nei reti virtuali. Fornisce il bilanciamento del carico interno tramite il routing del traffico tra le risorse per compilare l'applicazione a livello di area.|<p> [Bilanciare il carico del traffico Internet verso le macchine virtuali](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Bilanciare il carico del traffico tra le macchine virtuali all'interno di una rete virtuale](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Trasferire il traffico verso una porta specifica in macchine virtuali specifiche](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Configurare il bilanciamento del carico e le regole in uscita](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Gateway applicazione](#applicationgateway)|Il gateway applicazione di Azure è un servizio di bilanciamento del carico del traffico Web che consente di gestire il traffico verso le applicazioni Web.|<p>[Indirizzare il traffico Web con un gateway applicazione Azure](../application-gateway/quick-create-portal.md)</p><p>[Esercitazione: Configurare un gateway applicazione con la terminazione TLS tramite il portale di Azure](../application-gateway/create-ssl-portal.md)</p><p>[Creare un gateway applicazione con reindirizzamento basato su percorsi URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Rete per la distribuzione di contenuti (CDN)
La rete per la distribuzione di contenuti (rete CDN) offre agli sviluppatori una soluzione globale per distribuire rapidamente contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione di tale contenuto nella cache in nodi fisici strategici ubicati in tutto il mondo. Per altre informazioni sulla rete CDN di Azure, vedere rete per la [distribuzione di contenuti di Azure](../cdn/cdn-overview.md)

![Rete CDN di Azure](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Servizio front door di Azure
Il servizio Frontdoor di Azure consente di definire, gestire e monitorare il routing globale del traffico Web, rendendo le prestazioni ottimali e il failover globale immediato per una disponibilità elevata. Con Frontdoor è possibile trasformare il consumer globale (multi-area) e le applicazioni aziendali in applicazioni moderne, solide, personalizzate e dalle alte prestazioni, in API, con contenuti che raggiungono un audience globale grazie ad Azure. Per altre informazioni, vedere [sportello anteriore di Azure](../frontdoor/front-door-overview.md).


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Gestione traffico

Gestione traffico di Azure è un servizio di bilanciamento del carico basato su DNS che consente di distribuire il traffico in modo ottimale ai servizi nelle aree globali di Azure, offrendo al tempo stesso disponibilità e velocità di risposta elevate. Gestione traffico offre una gamma di metodi di routing del traffico per distribuire il traffico, ad esempio priorità, ponderata, prestazioni, geografico, multivalore o subnet. Per altre informazioni sui metodi di routing del traffico, vedere [metodi di routing di gestione traffico](../traffic-manager/traffic-manager-routing-methods.md).

Il diagramma seguente mostra il routing basato sulle priorità degli endpoint con gestione traffico:

![Metodo di routing del traffico "Priorità" di Gestione traffico di Azure](./media/networking-overview/priority.png)

Per altre informazioni su Gestione traffico, vedere [che cos'è gestione traffico di Azure?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Bilanciamento del carico
Azure Load Balancer offre servizi di bilanciamento del carico di livello 4 a elevate prestazioni e bassa latenza per tutti i protocolli UDP e TCP. Gestisce le connessioni in ingresso e in uscita. È possibile configurare endpoint con carico bilanciato pubblici e interni e definire regole per il mapping delle connessioni in ingresso a destinazioni del pool back-end, usando opzioni di probe dell'integrità TCP e HTTP per gestire la disponibilità del servizio. Per altre informazioni su Azure Load Balancer, vedere l'articolo [Panoramica del servizio di bilanciamento del carico](../load-balancer/load-balancer-overview.md).

La figura seguente mostra un'applicazione multilivello con connessione Internet che usa servizi di bilanciamento del carico sia interni che esterni:

![Esempio di Azure Load Balancer](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Gateway applicazione
Il gateway applicazione di Azure è un servizio di bilanciamento del carico del traffico Web che consente di gestire il traffico verso le applicazioni Web. Si tratta di un servizio di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller), che offre diverse funzionalità di bilanciamento del carico di livello 7 per le applicazioni. Per ulteriori informazioni, vedere [che cos'è applicazione Azure gateway?](../application-gateway/overview.md).

Il diagramma seguente mostra il routing basato su percorso URL con il gateway applicazione.

![Esempio di gateway applicazione](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Servizi di monitoraggio di rete
Questa sezione descrive i servizi di rete in Azure che consentono di monitorare le risorse di rete: Network Watcher, monitoraggio ExpressRoute, monitoraggio di Azure e TAP di rete virtuale.

|Servizio|Perché usare?|Scenario|
|---|---|---|
|[Network Watcher](#networkwatcher)|Consente di monitorare e risolvere i problemi di connettività, consente di diagnosticare i problemi relativi a VPN, NSG e routing, acquisire pacchetti nella macchina virtuale, automatizzare l'attivazione di strumenti di diagnostica con funzioni di Azure e app per la logica|<p>[Diagnosticare un problema di filtro del traffico delle macchine virtuali](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnosticare un problema di routing delle macchine virtuali](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Monitorare le comunicazioni tra le macchine virtuali](../network-watcher/connection-monitor.md)</p><p>[Diagnosticare problemi di comunicazione tra le reti](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Registrare il traffico di rete da e verso una macchina virtuale](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[Monitoraggio di ExpressRoute](#expressroutemonitor)|Fornisce il monitoraggio in tempo reale delle prestazioni, della disponibilità e dell'utilizzo della rete, facilita l'individuazione automatica della topologia di rete, fornisce un isolamento degli errori più veloce, rileva problemi di rete temporanei, consente di analizzare le caratteristiche storiche delle prestazioni della rete, supporta più sottoscrizioni|<p>[Configurare Monitoraggio prestazioni rete per ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Monitoraggio, metriche e avvisi di ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Monitoraggio di Azure](#azuremonitor)|Consente di comprendere in che modo le applicazioni eseguono e identifica in modo proattivo i problemi che interessano le risorse e le risorse da cui dipendono.|<p>[Metriche e avvisi di Gestione traffico](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Diagnostica di monitoraggio di Azure per Load Balancer Standard](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Monitorare i log e le metriche di Firewall di Azure](../firewall/tutorial-diagnostics.md)</p><p>[Monitoraggio e registrazione di Azure Web application firewall](../frontdoor/waf-front-door-monitor.md)</p>|
|[TOCCO rete virtuale](#vnettap)|Fornisce lo streaming continuo del traffico di rete della macchina virtuale a raccolta pacchetti, Abilita le soluzioni di gestione delle prestazioni di rete e delle applicazioni e gli strumenti di analisi|[Creare una risorsa VNet TAP](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher fornisce gli strumenti per il monitoraggio, la diagnostica, la visualizzazione delle metriche e l'abilitazione o la disabilitazione dei log per le risorse in una rete virtuale di Azure. Per ulteriori informazioni, vedere [che cos'è Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Monitoraggio di ExpressRoute
Per informazioni su come visualizzare le metriche del circuito ExpressRoute, i log delle risorse e gli avvisi, vedere [monitoraggio, metriche e avvisi di ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Monitoraggio di Azure
Monitoraggio di Azure ottimizza la disponibilità e le prestazioni delle applicazioni in uso offrendo una soluzione completa per raccogliere e analizzare la telemetria e intervenire di conseguenza dal cloud e dagli ambienti locali. È utile per ottenere informazioni sulle prestazioni delle applicazioni e identificare in modo proattivo i problemi delle applicazioni e delle risorse da cui dipendono. Per altre informazioni, vedere [Panoramica di monitoraggio di Azure](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>TOCCO rete virtuale
Un TAP (Terminal Access Point) di rete virtuale di Azure consente di trasmettere il traffico di rete della macchina virtuale come flusso continuo a un agente di raccolta di pacchetti di rete o a uno strumento di analisi. Lo strumento di raccolta dati o analisi viene fornito da un partner di [appliance virtuale di rete](https://azure.microsoft.com/solutions/network-appliances/) . 

L'immagine seguente illustra come funziona un TAP di rete virtuale. 

![Come funziona il TAP di rete virtuale](./media/networking-overview/virtual-network-tap-architecture.png)

Per ulteriori informazioni, vedere la pagina relativa al [tocco della rete virtuale](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Creare la prima rete virtuale e connettervi alcune VM seguendo la procedura descritta nell'articolo [Creare la prima rete virtuale](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Connettere il computer a un VNet completando i passaggi descritti nell' [articolo configurare una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Bilanciare il carico del traffico Internet verso server pubblici seguendo la procedura descritta nell'articolo [Creazione del servizio di bilanciamento del carico Internet attraverso il portale di Azure](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
 
 
   
