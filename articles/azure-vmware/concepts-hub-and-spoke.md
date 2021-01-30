---
title: "Concetto: integrare una distribuzione della soluzione VMware di Azure in un'architettura Hub e spoke"
description: Informazioni sull'integrazione di una distribuzione della soluzione VMware di Azure in un'architettura Hub e spoke in Azure.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: bfc442e569572349b1323500fbd0b2f912ebbc62
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062746"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integrare la soluzione VMware di Azure in un'architettura Hub e spoke

Questo articolo fornisce consigli per l'integrazione di una distribuzione della soluzione VMware di Azure in una nuova [architettura di hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology) in Azure. 


Lo scenario Hub e spoke presuppone un ambiente cloud ibrido con carichi di lavoro in:

* Azure nativo con servizi IaaS o PaaS
* Soluzione Azure VMware 
* vSphere locale

## <a name="architecture"></a>Architettura

L' *Hub* è una rete virtuale di Azure che funge da punto centrale di connettività al cloud privato della soluzione VMware locale e di Azure. I *spoke* sono reti virtuali con peering con l'hub per abilitare la comunicazione tra reti virtuali.

Il traffico tra il Data Center locale, il cloud privato della soluzione VMware di Azure e l'hub passa attraverso le connessioni di Azure ExpressRoute. Le reti virtuali spoke contengono in genere carichi di lavoro basati su IaaS, ma possono avere servizi PaaS come [ambiente del servizio app](../app-service/environment/intro.md), che ha l'integrazione diretta con la rete virtuale o altri servizi PaaS con [collegamento privato di Azure](../private-link/index.yml) abilitato.

>[!IMPORTANT]
>È possibile usare un gateway ExpressRoute esistente per connettersi alla soluzione Azure VMware, purché non si superi il limite di quattro circuiti ExpressRoute per rete virtuale.  Tuttavia, per accedere alla soluzione Azure VMware dall'ambiente locale tramite ExpressRoute è necessario avere Copertura globale ExpressRoute, in quanto il gateway ExpressRoute non fornisce il routing transitivo tra i circuiti connessi.

Il diagramma mostra un esempio di una distribuzione Hub e spoke in Azure connessa alla soluzione VMware locale e Azure tramite ExpressRoute Copertura globale.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Distribuzione dell'hub della soluzione VMware e dell'integrazione spoke di Azure" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

L'architettura include i componenti principali seguenti:

- **Sito locale:** I data center locali del cliente sono connessi ad Azure tramite una connessione ExpressRoute.

- **Cloud privato della soluzione VMware di Azure:** Soluzione VMware di Azure SDDC formato da uno o più cluster vSphere, ognuno con un massimo di 16 host.

- **Gateway ExpressRoute:** Abilita la comunicazione tra il cloud privato della soluzione VMware di Azure, i servizi condivisi nella rete virtuale Hub e i carichi di lavoro in esecuzione su reti virtuali spoke.

- **Copertura globale ExpressRoute:** Abilita la connettività tra il cloud privato della soluzione VMware di Azure e l'ambiente locale. La connettività tra la soluzione VMware di Azure e l'infrastruttura di Azure avviene solo tramite ExpressRoute Copertura globale. Non è possibile selezionare alcuna opzione al di fuori di ExpressRoute Fast Path.  ExpressRoute Direct non è supportato.


- **Considerazioni sulla VPN S2S:** Per le distribuzioni di produzione di soluzioni VMware di Azure, la VPN S2S di Azure non è supportata a causa dei requisiti di rete per VMware HCX. Tuttavia, è possibile usarlo per una distribuzione PoC.


- **Rete virtuale Hub:** Funge da punto centrale di connettività alla rete locale e al cloud privato della soluzione VMware di Azure.

- **Rete virtuale spoke**

    - **IaaS spoke:** Un IaaS spoke ospita carichi di lavoro basati su IaaS di Azure, inclusi i set di disponibilità di VM e i set di scalabilità di macchine virtuali e i componenti di rete corrispondenti.

    - **PaaS spoke:** Un PaaS spoke ospita i servizi PaaS di Azure usando l'indirizzamento privato grazie all' [endpoint privato](../private-link/private-endpoint-overview.md) e al [collegamento privato](../private-link/private-link-overview.md).

- **Firewall di Azure:** Funge da componente centrale per segmentare il traffico tra i spoke e la soluzione VMware di Azure.

- **Gateway applicazione:** Espone e protegge le app Web che vengono eseguite in macchine virtuali (VM) Azure IaaS/PaaS o Azure VMware Solution. Si integra con altri servizi, ad esempio gestione API.

## <a name="network-and-security-considerations"></a>Considerazioni sulla rete e sulla sicurezza

Le connessioni ExpressRoute consentono il flusso del traffico tra l'ambiente locale, la soluzione VMware di Azure e l'infrastruttura di rete di Azure. La soluzione VMware di Azure usa [ExpressRoute copertura globale](../expressroute/expressroute-global-reach.md) per implementare questa connettività.

Poiché un gateway ExpressRoute non fornisce il routing transitivo tra i circuiti connessi, la connettività locale deve anche usare ExpressRoute Copertura globale per la comunicazione tra l'ambiente vSphere locale e la soluzione VMware di Azure. 

* **Flusso del traffico della soluzione VMware da sito locale ad Azure**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="Flusso del traffico della soluzione VMware da sito locale ad Azure" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Flusso del traffico di VNET di Azure da soluzione VMware a hub**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Flusso di traffico della rete virtuale di Azure da soluzione VMware a hub" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


Per altre informazioni sui concetti relativi alla rete e alla connettività della soluzione VMware di Azure, vedere la [documentazione del prodotto della soluzione VMware di Azure](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentazione del traffico

Il [firewall di Azure](../firewall/index.yml) è la parte centrale della topologia hub e spoke, distribuita nella rete virtuale dell'hub. Usare il firewall di Azure o un'altra appliance virtuale di rete supportata da Azure per stabilire le regole di traffico e segmentare la comunicazione tra i diversi spoke e i carichi di lavoro della soluzione VMware di Azure.

Creare tabelle di route per indirizzare il traffico al firewall di Azure.  Per le reti virtuali spoke, creare una route che imposta la route predefinita sull'interfaccia interna del firewall di Azure. In questo modo, quando un carico di lavoro nella rete virtuale deve raggiungere lo spazio di indirizzi della soluzione VMware di Azure, il firewall può valutarlo e applicare la regola di traffico corrispondente per consentire o negare l'accesso.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Creare tabelle di route per indirizzare il traffico al firewall di Azure" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> Una route con prefisso dell'indirizzo 0.0.0.0/0 nell'impostazione **GatewaySubnet** non è supportata.

Impostare le route per reti specifiche nella tabella di route corrispondente. Ad esempio, le route per raggiungere i prefissi IP di gestione delle soluzioni VMware e dei carichi di lavoro di Azure dai carichi di lavoro spoke e viceversa.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Impostare le route per reti specifiche nella tabella di route corrispondente" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

Un secondo livello di segmentazione del traffico che usa i gruppi di sicurezza di rete nei spoke e nell'hub per creare criteri di traffico più granulari.

> [!NOTE]
> **Traffico dalla soluzione VMware da sito locale ad Azure:** Il traffico tra i carichi di lavoro locali, basati su vSphere o altri, viene abilitato da Copertura globale, ma il traffico non passa attraverso il firewall di Azure nell'hub. In questo scenario è necessario implementare i meccanismi di segmentazione del traffico, in locale o in una soluzione VMware di Azure.

### <a name="application-gateway"></a>Gateway applicazione

Applicazione Azure gateway V1 e V2 sono stati testati con app Web eseguite in macchine virtuali della soluzione VMware di Azure come pool back-end. Il gateway applicazione è attualmente l'unico metodo supportato per esporre le app Web in esecuzione su macchine virtuali della soluzione VMware di Azure a Internet. Può inoltre esporre le app agli utenti interni in modo sicuro.

Per altre informazioni, vedere l'articolo specifico della soluzione VMware di Azure nel [gateway applicazione](./protect-azure-vmware-solution-with-application-gateway.md).

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Secondo livello di segmentazione del traffico con i gruppi di sicurezza di rete" border="false":::


### <a name="jump-box-and-azure-bastion"></a>Jump box e Azure Bastion

Accedere all'ambiente della soluzione VMware di Azure con una finestra di salto, ovvero una macchina virtuale Windows 10 o Windows Server distribuita nella subnet del servizio condiviso all'interno della rete virtuale dell'hub.

>[!IMPORTANT]
>Azure Bastion è il servizio consigliato per connettersi alla casella di salto per impedire l'esposizione della soluzione VMware di Azure a Internet. Non è possibile usare Azure Bastion per connettersi alle macchine virtuali della soluzione VMware di Azure perché non sono oggetti IaaS di Azure.  

Come procedura di sicurezza consigliata, distribuire [Microsoft Azure servizio Bastion](../bastion/index.yml) all'interno della rete virtuale dell'hub. Azure Bastion offre accesso RDP e SSH semplice alle macchine virtuali distribuite in Azure senza la necessità di effettuare il provisioning di indirizzi IP pubblici a tali risorse. Dopo aver effettuato il provisioning del servizio Azure Bastion, è possibile accedere alla macchina virtuale selezionata dalla portale di Azure. Dopo aver stabilito la connessione, viene visualizzata una nuova scheda che mostra il desktop di Jump box e da tale desktop è possibile accedere al piano di gestione del cloud privato della soluzione VMware di Azure.

> [!IMPORTANT]
> Non assegnare un indirizzo IP pubblico alla macchina virtuale jump box o esporre la porta 3389/TCP alla rete Internet pubblica. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Rete virtuale dell'hub Bastion di Azure" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Considerazioni sulla risoluzione DNS di Azure

Per la risoluzione DNS di Azure sono disponibili due opzioni:

-   Usare i controller di dominio distribuiti nell'hub (descritti in [considerazioni sulle identità](#identity-considerations)) come server dei nomi.

-   Distribuire e configurare una zona privata di DNS di Azure.

L'approccio migliore consiste nel combinare entrambi per offrire una risoluzione dei nomi affidabile per la soluzione VMware di Azure, in locale e in Azure.

Come raccomandazione di progettazione generale, usare l'infrastruttura DNS di Azure esistente (in questo caso, Active Directory DNS integrato) distribuita in almeno due macchine virtuali di Azure distribuite nella rete virtuale dell'hub e configurate nelle reti virtuali spoke per usare i server DNS di Azure nelle impostazioni DNS.

È possibile usare DNS privato di Azure, in cui la zona di DNS privato di Azure è collegata alla rete virtuale.  I server DNS vengono usati come sistemi di risoluzione ibrido con l'invio condizionale a una soluzione VMware locale o di Azure che esegue DNS usando l'infrastruttura di Azure DNS privato del cliente. 

Per gestire automaticamente il ciclo di vita dei record DNS per le macchine virtuali distribuite nelle reti virtuali spoke, abilitare la registrazione automatica. Se abilitata, il numero massimo di zone DNS private è solo uno. Se disabilitato, il numero massimo è 1000.

I server della soluzione VMware locale e di Azure possono essere configurati con i server di trasmissione condizionali per le macchine virtuali resolver in Azure per la zona DNS privato di Azure.

## <a name="identity-considerations"></a>Considerazioni sull'identità

Per motivi di identità, l'approccio migliore consiste nel distribuire almeno un controller di dominio nell'hub. Usare due subnet del servizio condiviso in modalità distribuita a livello di area o in un set di disponibilità di macchine virtuali. Per ulteriori informazioni sull'estensione del dominio Active Directory (AD) locale in Azure, vedere [centro architetture di Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

Inoltre, distribuire un altro controller di dominio nel lato della soluzione VMware di Azure per fungere da identità e origine DNS nell'ambiente vSphere.

Come procedura consigliata, integrare [dominio ad con Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
