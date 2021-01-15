---
title: Panoramica dei servizi di rete di Azure
description: Informazioni sui servizi di rete in Azure, tra cui connettività, protezione delle applicazioni, distribuzione di applicazioni e servizi di monitoraggio di rete.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: kumud
ms.openlocfilehash: d02f85647d947c2f28527ba6f0397c2a98e73840
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234189"
---
# <a name="azure-networking-services-overview"></a>Panoramica dei servizi di rete di Azure

I servizi di rete in Azure offrono un'ampia gamma di funzionalità di rete che possono essere usate insieme o separatamente. Per altre informazioni, fare clic su una qualsiasi delle funzionalità chiave seguenti:
- [**Servizi di connettività**](#connect): connettere le risorse di Azure e le risorse locali usando una o più combinazioni di questi servizi di rete in Azure-rete virtuale (VNet), WAN virtuale, ExpressRoute, gateway VPN, gateway NAT di rete virtuale, DNS di Azure, servizio di peering e Azure Bastion.
- [**Servizi di protezione**](#protect)delle applicazioni: Proteggi le tue applicazioni usando qualsiasi o una combinazione di questi servizi di rete in Azure-collegamento privato, protezione DDoS, firewall, gruppi di sicurezza di rete, Web Application Firewall e endpoint di rete virtuale.
- [**Servizi**](#deliver)per la distribuzione di applicazioni: distribuzione di applicazioni nella rete di Azure usando una o più combinazioni di questi servizi di rete in Azure-rete per la distribuzione di contenuti (CDN), servizio di Azure front door, gestione traffico, gateway applicazione, Internet Analyzer e Load Balancer.
- [**Monitoraggio di rete**](#monitor): monitorare le risorse di rete usando una o più combinazioni di questi servizi di rete in Azure-Network Watcher, monitoraggio ExpressRoute, monitoraggio di Azure o punto di accesso terminale VNET (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Servizi di connettività
 
Questa sezione descrive i servizi che forniscono la connettività tra le risorse di Azure, la connettività da una rete locale alle risorse di Azure e la creazione di Branch per la connettività a branch in Azure-rete virtuale (VNet), ExpressRoute, gateway VPN, rete WAN virtuale, gateway NAT di rete virtuale, DNS di Azure, servizio di peering di Azure e Bastion di Azure.


### <a name="virtual-network"></a><a name="vnet"></a>Rete virtuale

Rete virtuale di Azure (VNet) è il blocco predefinito fondamentale per la rete privata in Azure. È possibile usare un reti virtuali per:
- **Comunicazione tra le risorse di Azure**: è possibile distribuire VM e diversi altri tipi di risorse di Azure in una rete virtuale, ad esempio app Azure ambienti del servizio, Azure Kubernetes Service (AKS) e set di scalabilità di macchine virtuali di Azure. Per visualizzare un elenco completo delle risorse di Azure che è possibile distribuire in una rete virtuale, vedere [Integrazione del servizio di rete virtuale](../virtual-network/virtual-network-for-azure-services.md).
- **Comunicare tra loro**: è possibile connettere le reti virtuali tra loro, consentendo alle risorse in entrambe le reti virtuali di comunicare tra loro, usando il peering di rete virtuale. Le reti virtuali connesse possono essere in aree di Azure uguali o diversi. Per altre informazioni, vedere [Peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md).
- **Comunicazione con Internet**: per impostazione predefinita, tutte le risorse in un VNet possono comunicare in uscita verso Internet. Per la comunicazione in ingresso con una risorsa, è possibile assegnarle un indirizzo IP pubblico o un servizio di bilanciamento del carico pubblico. Per gestire le connessioni in uscita, è anche possibile usare [indirizzi IP pubblici](../virtual-network/virtual-network-public-ip-address.md) o [Load Balancer](../load-balancer/load-balancer-overview.md) pubblici.
- **Comunicare con le reti locali**: è possibile connettere i computer e le reti locali a una rete virtuale usando il [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ExpressRoute](../expressroute/expressroute-introduction.md).

Per altre informazioni, vedere informazioni su [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata facilitata da un provider di connettività. La connessione è privata. Il traffico non passa da Internet. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Microsoft 365 e Dynamics 365.  Per ulteriori informazioni, vedere [che cos'è ExpressRoute?](../expressroute/expressroute-introduction.md).

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>Gateway VPN
Il gateway VPN consente di creare connessioni cross-premise crittografate alla rete virtuale da percorsi locali o di creare connessioni crittografate tra reti virtuali. Sono disponibili diverse configurazioni per le connessioni del gateway VPN, ad esempio da sito a sito, da punto a sito o da VNet a VNet.
Il diagramma seguente illustra più connessioni VPN da sito a sito alla stessa rete virtuale.

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Connessioni gateway VPN di Azure da sito a sito":::

Per altre informazioni sui diversi tipi di connessioni VPN, vedere [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Rete WAN virtuale
La rete WAN virtuale di Azure è un servizio di rete che offre connettività tra rami ottimizzata e automatizzata verso e tramite Azure. Le aree di Azure fungono da hub a cui è possibile scegliere di connettere i rami. È possibile sfruttare il backbone di Azure per anche connettere i rami e sfruttare i vantaggi della connettività da ramo a VNet. La rete WAN virtuale di Azure combina molti servizi di connettività cloud di Azure, ad esempio VPN da sito a sito, ExpressRoute, VPN utente da punto a sito in un'unica interfaccia operativa. La connettività con le reti virtuali di Azure viene stabilita tramite connessioni di rete virtuali. Per altre informazioni, vedere [che cos'è la rete WAN virtuale di Azure?](../virtual-wan/virtual-wan-about.md).

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Diagramma della rete WAN virtuale":::

### <a name="azure-dns"></a><a name="dns"></a>DNS di Azure
DNS di Azure è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. L'hosting dei domini in Azure consente di gestire i record DNS usando gli stessi strumenti, credenziali, API e fatturazione usati per altri servizi Azure. Per altre informazioni, vedere informazioni su [DNS di Azure](../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion è un nuovo servizio PaaS completamente gestito dalla piattaforma di cui è possibile eseguire il provisioning all'interno della rete virtuale. Offre connettività RDP/SSH sicura e ininterrotta con le macchine virtuali direttamente nel portale di Azure tramite TLS. Quando ci si connette tramite Azure Bastion, per le macchine virtuali non è necessario un indirizzo IP pubblico. Per altre informazioni, vedere [che cos'è Azure Bastion?](../bastion/bastion-overview.md).

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Architettura Bastion di Azure":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Gateway NAT della rete virtuale
NAT (Network Address Translation) di rete virtuale semplifica la connettività Internet solo in uscita per le reti virtuali. Quando viene configurato in una subnet, per tutta la connettività in uscita vengono usati gli indirizzi IP pubblici statici specificati. La connettività in uscita è possibile senza bilanciamento del carico o indirizzi IP pubblici collegati direttamente alle macchine virtuali. Per ulteriori informazioni, vedere [che cos'è il gateway NAT di rete virtuale?](../virtual-network/nat-overview.md).

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Gateway NAT della rete virtuale":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Servizio peering di Azure
Il servizio peering di Azure migliora la connettività dei clienti ai servizi cloud Microsoft, ad esempio Microsoft 365, Dynamics 365, Software as a Service (SaaS) Services, Azure o qualsiasi servizio Microsoft accessibile tramite la rete Internet pubblica. Per altre informazioni, vedere [che cos'è il servizio peering di Azure?](../peering-service/about.md).

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Zone perimetrali di Azure

Area perimetrale di Azure è una famiglia di offerte da Microsoft Azure che consente l'elaborazione dei dati vicino all'utente. È possibile distribuire macchine virtuali, contenitori e altri servizi di Azure selezionati in zone perimetrali per risolvere i requisiti di bassa latenza e velocità effettiva elevata delle applicazioni. Per altre informazioni, vedere informazioni sulle [zone perimetrali di Azure](edge-zones-overview.md).

### <a name="azure-orbital"></a><a name="orbital"></a>Orbital di Azure

Azure Orbital è una stazione di terra basata sul cloud completamente gestita come servizio che consente di comunicare con i veicoli spaziali o con le costellazioni di satelliti, eseguire il downlink o l'uplink dei dati, elaborare i dati nel cloud, concatenare i servizi con i servizi di Azure in scenari peculiari e generare prodotti per i clienti. Questo sistema si basa sull'infrastruttura globale di Azure e sulla rete in fibra globale a bassa latenza. Per altre informazioni, vedere [che cos'è Orbital di Azure?](azure-orbital-overview.md).

:::image type="content" source="./media/azure-orbital-overview/orbital-communications-use-flow.png" alt-text="Diagramma delle comunicazioni orbitali di Azure":::

## <a name="application-protection-services"></a><a name="protect"></a>Servizi di protezione delle applicazioni

Questa sezione descrive i servizi di rete in Azure che consentono di proteggere le risorse di rete. proteggere le applicazioni usando uno o più di questi servizi di rete in Azure-protezione DDoS, collegamento privato, firewall, Web Application Firewall, gruppi di sicurezza di rete e endpoint di servizio di rete virtuale.

### <a name="ddos-protection"></a><a name="ddosprotection"></a>Protezione DDoS 
[Protezione DDoS di Azure](../ddos-protection/manage-ddos-protection.md) fornisce contromisure contro le minacce DDoS più sofisticate. Il servizio fornisce funzionalità avanzate di mitigazione DDoS per l'applicazione e le risorse distribuite nelle reti virtuali. Inoltre, i clienti che usano la protezione DDoS di Azure hanno accesso al supporto di risposta rapida DDoS per coinvolgere gli esperti DDoS durante un attacco attivo.

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="Protezione DDoS":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Collegamento privato di Azure
Il [collegamento privato di Azure](../private-link/private-link-overview.md) consente di accedere ai servizi PaaS di Azure, ad esempio archiviazione di Azure e al database SQL, e ai servizi di proprietà/partner del cliente ospitati in Azure tramite un endpoint privato nella rete virtuale.
Il traffico tra la rete virtuale e il servizio attraversa la rete del backbone Microsoft. L'esposizione del servizio sulla rete Internet pubblica non è più necessaria. È possibile creare un servizio Collegamento privato personale nella rete virtuale e distribuirlo ai clienti.

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Panoramica dell'endpoint privato":::

### <a name="azure-firewall"></a><a name="firewall"></a>Firewall di Azure
Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. Con il firewall di Azure è possibile creare, applicare e registrare in modo centralizzato i criteri di connettività di rete e di applicazione tra sottoscrizioni e reti virtuali. Firewall di Azure usa un indirizzo IP pubblico statico per le risorse della rete virtuale consentendo ai firewall esterni di identificare il traffico proveniente dalla rete virtuale. 

Per ulteriori informazioni sul firewall di Azure, vedere la [documentazione del firewall di Azure](../firewall/overview.md).

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Panoramica del firewall":::

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall
Il [Web Application Firewall](../web-application-firewall/overview.md) (WAF) di Azure garantisce la protezione delle applicazioni Web da exploit e vulnerabilità Web comuni, come SQL injection e cross site scripting. Azure WAF offre la protezione predefinita da OWASP prime 10 vulnerabilità tramite regole gestite. Inoltre, i clienti possono configurare regole personalizzate, ovvero regole gestite dal cliente per fornire protezione aggiuntiva in base all'intervallo di indirizzi IP di origine e attributi di richiesta quali intestazioni, cookie, campi dati di form o parametri di stringa di query.

I clienti possono scegliere di distribuire [Azure WAF con il gateway applicazione](../web-application-firewall/ag/ag-overview.md) , che fornisce la protezione a livello di area alle entità nello spazio degli indirizzi pubblico e privato. I clienti possono anche scegliere di distribuire [Azure WAF con la porta anteriore](../web-application-firewall/afds/afds-overview.md) , che fornisce la protezione sul perimetro della rete agli endpoint pubblici.

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Web application firewall":::

### <a name="network-security-groups"></a><a name="nsg"></a>Gruppi di sicurezza di rete
È possibile filtrare il traffico di rete da e verso le risorse di Azure in una rete virtuale di Azure con un gruppo di sicurezza di rete. Per altre informazioni, vedere [Gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Endpoint di servizio
Gli endpoint del servizio Rete virtuale estendono lo spazio di indirizzi privato della rete virtuale e l'identità della rete virtuale ai servizi di Azure tramite una connessione diretta. Gli endpoint consentono di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure. Per altre informazioni, vedere [Endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="Endpoint servizio di rete virtuale":::

## <a name="application-delivery-services"></a><a name="deliver"></a>Servizi per la distribuzione di applicazioni

Questa sezione descrive i servizi di rete in Azure che consentono di fornire applicazioni: rete per la distribuzione di contenuti, servizio front door di Azure, gestione traffico, Load Balancer e gateway applicazione.

### <a name="content-delivery-network"></a><a name="cdn"></a>Rete per la distribuzione di contenuti (CDN)
La rete per la distribuzione di contenuti (rete CDN) offre agli sviluppatori una soluzione globale per distribuire rapidamente contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione di tale contenuto nella cache in nodi fisici strategici ubicati in tutto il mondo. Per altre informazioni sulla rete CDN di Azure, vedere rete per la [distribuzione di contenuti di Azure](../cdn/cdn-overview.md).

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Rete CDN di Azure":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Servizio Frontdoor di Azure
Il servizio Frontdoor di Azure consente di definire, gestire e monitorare il routing globale del traffico Web, rendendo le prestazioni ottimali e il failover globale immediato per una disponibilità elevata. Con Frontdoor è possibile trasformare il consumer globale (multi-area) e le applicazioni aziendali in applicazioni moderne, solide, personalizzate e dalle alte prestazioni, in API, con contenuti che raggiungono un audience globale grazie ad Azure. Per altre informazioni, vedere [sportello anteriore di Azure](../frontdoor/front-door-overview.md).

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="Panoramica sul servizio front door":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Gestione traffico

Gestione traffico di Azure è un servizio di bilanciamento del carico basato su DNS che consente di distribuire il traffico in modo ottimale ai servizi nelle aree globali di Azure, offrendo al tempo stesso disponibilità e velocità di risposta elevate. Gestione traffico offre una gamma di metodi di routing del traffico per distribuire il traffico, ad esempio priorità, ponderata, prestazioni, geografico, multivalore o subnet. Per altre informazioni sui metodi di routing del traffico, vedere [metodi di routing di gestione traffico](../traffic-manager/traffic-manager-routing-methods.md).

Il diagramma seguente mostra il routing basato sulle priorità degli endpoint con gestione traffico:

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Metodo di routing del traffico &quot;Priorità&quot; di Gestione traffico di Azure":::

Per altre informazioni su Gestione traffico, vedere [che cos'è gestione traffico di Azure?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Azure Load Balancer offre servizi di bilanciamento del carico di livello 4 a elevate prestazioni e bassa latenza per tutti i protocolli UDP e TCP. Gestisce le connessioni in ingresso e in uscita. È possibile configurare endpoint con carico bilanciato pubblici e interni e definire regole per il mapping delle connessioni in ingresso a destinazioni del pool back-end, usando opzioni di probe dell'integrità TCP e HTTP per gestire la disponibilità del servizio. Per altre informazioni su Azure Load Balancer, vedere l'articolo [Panoramica del servizio di bilanciamento del carico](../load-balancer/load-balancer-overview.md).

La figura seguente mostra un'applicazione multilivello con connessione Internet che usa servizi di bilanciamento del carico sia interni che esterni:

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Esempio di Azure Load Balancer":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Gateway applicazione
Il gateway applicazione di Azure è un servizio di bilanciamento del carico del traffico Web che consente di gestire il traffico verso le applicazioni Web. Si tratta di un servizio di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller), che offre diverse funzionalità di bilanciamento del carico di livello 7 per le applicazioni. Per ulteriori informazioni, vedere [che cos'è applicazione Azure gateway?](../application-gateway/overview.md).

Il diagramma seguente mostra il routing basato su percorso URL con il gateway applicazione.

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Esempio di gateway applicazione":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>Servizi di monitoraggio di rete
Questa sezione descrive i servizi di rete in Azure che consentono di monitorare le risorse di rete: Network Watcher, monitoraggio di Azure per le reti, monitoraggio ExpressRoute, monitoraggio di Azure e TAP di rete virtuale.

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher fornisce gli strumenti per il monitoraggio, la diagnostica, la visualizzazione delle metriche e l'abilitazione o la disabilitazione dei log per le risorse in una rete virtuale di Azure. Per ulteriori informazioni, vedere [che cos'è Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="azure-monitor-for-networks-preview"></a>Anteprima di monitoraggio di Azure per le reti
Monitoraggio di Azure per le reti offre una panoramica completa dell'integrità e delle metriche per tutte le risorse di rete distribuite, senza richiedere alcuna configurazione. Fornisce anche l'accesso alle funzionalità di monitoraggio della rete, come il [monitoraggio della connessione](../network-watcher/connection-monitor-overview.md), [la registrazione dei flussi per i gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-overview.md)e [analisi del traffico](../network-watcher/traffic-analytics.md). Per altre informazioni, vedere [Anteprima di monitoraggio di Azure per le reti](../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Monitoraggio di ExpressRoute
Per informazioni su come visualizzare le metriche del circuito ExpressRoute, i log delle risorse e gli avvisi, vedere [monitoraggio, metriche e avvisi di ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Monitoraggio di Azure
Monitoraggio di Azure ottimizza la disponibilità e le prestazioni delle applicazioni in uso offrendo una soluzione completa per raccogliere e analizzare la telemetria e intervenire di conseguenza dal cloud e dagli ambienti locali. È utile per ottenere informazioni sulle prestazioni delle applicazioni e identificare in modo proattivo i problemi delle applicazioni e delle risorse da cui dipendono. Per altre informazioni, vedere [Panoramica di monitoraggio di Azure](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>TOCCO rete virtuale
Un TAP (Terminal Access Point) di rete virtuale di Azure consente di trasmettere il traffico di rete della macchina virtuale come flusso continuo a un agente di raccolta di pacchetti di rete o a uno strumento di analisi. Lo strumento di raccolta dati o analisi viene fornito da un partner di [appliance virtuale di rete](https://azure.microsoft.com/solutions/network-appliances/) .

La figura seguente illustra il funzionamento di TAP di rete virtuale:

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="Come funziona il TAP di rete virtuale":::

Per ulteriori informazioni, vedere la pagina relativa al [tocco della rete virtuale](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Creare la prima rete virtuale e connettervi alcune macchine virtuali, completando i passaggi descritti nell'articolo [creare la prima rete virtuale](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .
- Connettere il computer a una rete virtuale completando la procedura descritta nell' [articolo configurare una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Bilanciare il carico del traffico Internet verso server pubblici seguendo la procedura descritta nell'articolo [Creazione del servizio di bilanciamento del carico Internet attraverso il portale di Azure](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).