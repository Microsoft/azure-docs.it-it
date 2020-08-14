---
title: Domande frequenti sulla rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Risposte alle domande più frequenti sulla rete virtuale di Microsoft Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2020
ms.author: kumud
ms.openlocfilehash: c242b08c598b04c84ab330e846704eace23c4858
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88205470"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Domande frequenti sulla rete virtuale di Azure

## <a name="virtual-network-basics"></a>Nozioni di base sulla rete virtuale

### <a name="what-is-an-azure-virtual-network-vnet"></a>Che cos'è una rete virtuale di Azure?
Una rete virtuale (VNet) di Azure è una rappresentazione della rete personalizzata nel cloud. È un isolamento logico del cloud di Azure dedicato alla sottoscrizione. È possibile usare le reti virtuali per eseguire il provisioning e gestire reti virtuali private (VPN) in Azure e facoltativamente collegare le reti virtuali con altre reti virtuali in Azure o con l'infrastruttura IT locale per creare soluzioni ibride o cross-premise. Ogni VNet creato ha un proprio blocco CIDR e può essere collegato ad altre reti virtuali e reti locali, purché i blocchi CIDR non si sovrappongano. È anche possibile controllare le impostazioni del server DNS per le reti virtuali e la segmentazione della rete virtuale in subnet.

Usare le reti virtuali per:

* Creare una VNet privata dedicata solo cloud. Per una soluzione personalizzata, non è sempre necessaria una configurazione cross-premise. Quando si crea una rete virtuale, i servizi e le macchine virtuali all'interno della rete virtuale possono comunicare direttamente e in modo sicuro tra loro nel cloud. È ancora possibile configurare connessioni di endpoint per le VM e i servizi che richiedono la comunicazione Internet come parte della soluzione.

* Estendi in modo sicuro il data center. Con le reti virtuali è possibile creare reti virtuali private da sito a sito (S2S) tradizionali per la scalabilità sicura della capacità del data center. Le reti virtuali private S2S usano IPSEC per fornire una connessione sicura tra il gateway VPN della rete virtuale privata aziendale e Azure.

* Abilitare scenari cloud ibridi. Le reti virtuali offrono la flessibilità per supportare una gamma di scenari cloud ibridi. È possibile connettere applicazioni basate sul cloud in modo sicuro a qualsiasi tipo di sistema locale, come mainframe e sistemi Unix.

### <a name="how-do-i-get-started"></a>Come iniziare?
Per iniziare, visitare la [documentazione relativa alla rete virtuale](https://docs.microsoft.com/azure/virtual-network/) . che contiene una panoramica e informazioni sulla distribuzione per tutte le funzionalità di rete virtuale.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>È possibile usare reti virtuali senza connettività cross-premise?
Sì. È possibile usare una rete virtuale senza connetterla all'ambiente locale. Ad esempio, è possibile eseguire controller di dominio Active Directory di Windows Server e farm di SharePoint esclusivamente in una rete virtuale di Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>È possibile eseguire l'ottimizzazione WAN tra reti virtuali o tra una rete virtuale e il data center locale?
Sì. È possibile distribuire [appliance virtuali di rete per l'ottimizzazione WAN](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) di diversi fornitori tramite Azure Marketplace.

## <a name="configuration"></a>Configurazione

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quali strumenti si usano per creare una rete virtuale?
Per creare o configurare una rete virtuale, è possibile usare gli strumenti seguenti:

* Portale di Azure
* PowerShell
* Interfaccia della riga di comando di Azure
* Un file di configurazione di rete (netcfg - solo per reti virtuali classiche). Vedere l'articolo [Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Quali intervalli di indirizzi è possibile usare nelle reti virtuali?
Si consiglia di usare gli intervalli di indirizzi enumerati in [RFC 1918](https://tools.ietf.org/html/rfc1918), che sono stati accantonati dall'IETF per spazi di indirizzi privati e non instradabili:
* 10.0.0.0 - 10.255.255.255 (prefisso 10/8)
* 172.16.0.0-172.31.255.255 (prefisso 172.16/12)
* 192.168.0.0 - 192.168.255.255 (prefisso 192.168/16)

Altri spazi di indirizzi possono funzionare, ma possono avere effetti collaterali indesiderati.

Non è inoltre possibile aggiungere gli intervalli di indirizzi seguenti:
* 224.0.0.0/4 (multicast)
* 255.255.255.255/32 (broadcast)
* 127.0.0.0/8 (loopback)
* 169.254.0.0/16 (locale rispetto al collegamento)
* 168.63.129.16/32 (DNS interno)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>È possibile avere indirizzi IP pubblici nelle reti virtuali?
Sì. Per altre informazioni sugli intervalli di indirizzi IP pubblici, vedere [Create a virtual network](manage-virtual-network.md#create-a-virtual-network) (Creare una rete virtuale). Gli indirizzi IP pubblici non sono accessibili direttamente da Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Esiste un limite al numero di subnet nella rete virtuale?
Sì. Vedere [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) per informazioni dettagliate. Gli spazi degli indirizzi della subnet non possono sovrapporsi.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?
Sì. Azure riserva 5 indirizzi IP all'interno di ogni subnet. Sono x. x.x. x. 0-x. x.x. x. 3 e l'ultimo indirizzo della subnet. x. x.x. x. 1-x.x. x. 3 è riservato in ogni subnet per i servizi di Azure.   
- x. x. x. 0: indirizzo di rete
- x. x. x. 1: riservato da Azure per il gateway predefinito
- x. x.x. x. 2, x.x. x. 3: riservato da Azure per eseguire il mapping degli indirizzi IP DNS di Azure allo spazio VNet
- x. x.x. x. 255: indirizzo di trasmissione di rete

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Quanto piccole o grandi possono essere le reti virtuali e le subnet?
La subnet IPv4 più piccola supportata è/29 e la più grande è/8 (usando le definizioni di subnet CIDR).  Le subnet IPv6 devono avere una dimensione esatta/64.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>È possibile trasferire le reti VLAN in Azure usando reti virtuali?
No. Le reti virtuali sono sovrapposizioni di livello 3. Azure non supporta alcuna semantica di livello 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>È possibile specificare criteri di routing personalizzati nelle reti virtuali e nelle subnet?
Sì. È possibile creare una tabella di route e associarla a una subnet. Per altre informazioni sul routing in Azure, vedere [Panoramica sul routing](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Le reti virtuali supportano la distribuzione multicast o broadcast?
No. La distribuzione multicast o broadcast non è supportata.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quali protocolli è possibile usare all'interno delle reti virtuali?
All'interno delle reti virtuali è possibile usare i protocolli TCP, UDP e ICMP TCP/IP. L'unicast è supportato all'interno di reti virtuali, ad eccezione di Dynamic Host Configuration Protocol (DHCP) tramite unicast (porta di origine UDP/68/porta di destinazione UDP/67) e la porta di origine UDP 65330 riservata per l'host. I pacchetti incapsulati IP in IP, multicast e broadcast e i pacchetti Generic Routing Encapsulation (GRE) sono bloccati all'interno delle reti virtuali. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>È possibile eseguire il ping dei router predefiniti all'interno di una rete virtuale?
No.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>È possibile usare tracert per diagnosticare la connettività?
No.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>È possibile aggiungere subnet dopo la creazione della rete virtuale?
Sì. Le subnet possono essere aggiunte alle reti virtuali in qualsiasi momento purché l'intervallo di indirizzi della subnet non faccia parte di un'altra subnet e vi sia sufficiente spazio disponibile nell'intervallo di indirizzi della rete virtuale.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>È possibile modificare le dimensioni della subnet dopo averla creata?
Sì. È possibile aggiungere, rimuovere, espandere o compattare una subnet se non sono presenti macchine virtuali o servizi distribuiti al suo interno.

### <a name="can-i-modify-vnet-after-i-created-them"></a>È possibile modificare VNET dopo averli creati?
Sì. È possibile aggiungere, rimuovere e modificare i blocchi CIDR utilizzati da una rete virtuale.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Se si eseguono i servizi in una rete virtuale, è possibile connettersi a Internet?
Sì. Tutti i servizi distribuiti all'interno di una rete virtuale possono effettuare la connessione in uscita a Internet. Per altre informazioni sulle connessioni Internet in uscita in Azure, vedere [Connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se si vuole effettuare la connessione in ingresso a una risorsa distribuita tramite Resource Manager, la risorsa deve avere un indirizzo IP pubblico assegnato. Per altre informazioni sugli indirizzi IP pubblici, vedere [Indirizzi IP pubblici](virtual-network-public-ip-address.md). A ciascun servizio cloud di Azure distribuito in Azure viene assegnato un indirizzo VIP indirizzabile pubblicamente. Per abilitare tali servizi in modo che accettino le connessioni da Internet, è necessario definire gli endpoint di input per i ruoli PaaS e gli endpoint per le macchine virtuali.

### <a name="do-vnets-support-ipv6"></a>Le reti virtuali supportano IPv6?
Sì, reti virtuali può essere solo IPv4 o doppio stack (IPv4 + IPv6).  Per informazioni dettagliate, vedere [Panoramica di IPv6 per le reti virtuali di Azure](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Una rete virtuale può estendersi a più aree?
No. Una rete virtuale è limitata a una singola area. Tuttavia, una rete virtuale si estende su zone di disponibilità. Per altre informazioni sulle zone di disponibilità, vedere [Panoramica delle zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). È possibile connettere reti virtuali in diverse aree con il peering di rete virtuale. Per informazioni dettagliate, vedere [Panoramica del peering di rete virtuale](virtual-network-peering-overview.md).

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>È possibile connettere una rete virtuale a un'altra rete virtuale in Azure?
Sì. È possibile connettere una rete virtuale a un'altra usando:
- **Peering di rete virtuale**: per informazioni dettagliate, vedere [Panoramica del peering di rete virtuale](virtual-network-peering-overview.md).
- **Gateway VPN di Azure**: per informazioni dettagliate, vedere [Configurare una connessione da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Risoluzione del nome (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quali sono le opzioni DNS per le reti virtuali?
Usare la tabella delle decisioni nella pagina [Risoluzione dei nomi per le VM e le istanze del ruolo](virtual-networks-name-resolution-for-vms-and-role-instances.md) come guida per tutte le opzioni DNS disponibili.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>È possibile specificare i server DNS per una rete virtuale?
Sì. È possibile specificare gli indirizzi IP del server DNS nelle impostazioni della rete virtuale. L'impostazione viene applicata come server DNS predefiniti per tutte le macchine virtuali nella rete virtuale.

### <a name="how-many-dns-servers-can-i-specify"></a>Quanti server DNS è possibile specificare?
Fare riferimento a [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>È possibile modificare i server DNS dopo aver creato la rete?
Sì. È possibile modificare l'elenco dei server DNS per la rete virtuale in qualsiasi momento. Se si modifica l'elenco dei server DNS, è necessario eseguire un rinnovo del lease DHCP in tutte le macchine virtuali interessate nella VNet per rendere effettive le nuove impostazioni DNS. Per le macchine virtuali che eseguono il sistema operativo Windows è possibile eseguire questa operazione digitando `ipconfig /renew` direttamente nella macchina virtuale. Per altri tipi di sistema operativo, fare riferimento alla documentazione relativa al rinnovo dei lease DHCP per il tipo di sistema operativo specifico. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Qual è il DNS fornito da Azure e funziona con le reti virtuali?
Il DNS fornito da Azure è un servizio DNS multi-tenant offerto da Microsoft. Azure registra tutte le macchine virtuali e le istanze del ruolo del servizio cloud in questo servizio. Questo servizio fornisce la risoluzione dei nomi dal nome host per le macchine virtuali e le istanze del ruolo contenute all'interno dello stesso servizio cloud e da FQDN per le macchine virtuali e le istanze del ruolo nella stessa rete virtuale. Per altre informazioni sul servizio DNS, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo di Servizi cloud](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Esiste una limitazione ai primi 100 servizi cloud nella rete virtuale per la risoluzione dei nomi cross-tenant tramite il DNS fornito da Azure. Se si utilizza il proprio server DNS, questa limitazione non viene applicata.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>È possibile ignorare le impostazioni DNS in base alla macchina virtuale o al servizio cloud?
Sì. È possibile impostare i server DNS in base alla macchina virtuale o al servizio cloud per ignorare le impostazioni di rete predefinite. Tuttavia, è consigliabile usare quanto più possibile DNS a livello di rete.

### <a name="can-i-bring-my-own-dns-suffix"></a>È possibile trasferire il suffisso DNS personalizzato?
No. Non è possibile specificare un suffisso DNS personalizzato per le reti virtuali.

## <a name="connecting-virtual-machines"></a>Connessione di macchine virtuali

### <a name="can-i-deploy-vms-to-a-vnet"></a>È possibile distribuire le macchine virtuali su una rete virtuale?
Sì. Tutte le scheda di interfaccia di rete collegate a una macchina virtuale distribuita con il modello di distribuzione Resource Manager devono essere connesse a una rete virtuale. Facoltativamente, è possibile connettere le macchine virtuali distribuite con il modello di distribuzione classica a una rete virtuale.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quali tipi di indirizzi IP è possibile assegnare alle macchine virtuali?
* **Privato:** assegnato a ogni scheda di interfaccia di rete all'interno di ogni macchina virtuale. L'indirizzo viene assegnato con il metodo statico o dinamico. Gli indirizzi IP privati vengono assegnati dall'intervallo specificato nelle impostazioni della subnet della rete virtuale. Alle risorse distribuite con il modello di distribuzione classica vengono assegnati indirizzi IP privati, anche se non sono connesse a una rete virtuale. Il comportamento del metodo di allocazione è diverso a seconda del fatto che una risorsa sia stata distribuita con il modello di distribuzione Resource Manager o classica: 

  - **Resource Manager**: un indirizzo IP privato assegnato con il metodo dinamico o statico rimane assegnato a una macchina virtuale (Resource Manager) finché la risorsa non viene eliminata. La differenza consiste nel fatto che quando si usa il metodo statico si seleziona l'indirizzo da assegnare, mentre quando si usa quello dinamico sceglie Azure. 
  - **Classica**: un indirizzo IP privato assegnato con il metodo dinamico può cambiare quando una macchina virtuale (classica) viene riavviata dopo essere stata arrestata (deallocata). Se è necessario assicurarsi che l'indirizzo IP privato per una risorsa distribuita tramite il modello di distribuzione classica non cambi mai, assegnare un indirizzo IP privato con il metodo statico.

* **Pubblico:** assegnato facoltativamente alle schede di interfaccia di rete collegate a macchine virtuali distribuite con il modello di distribuzione Azure Resource Manager. L'indirizzo può essere assegnato con il metodo di allocazione statica o dinamica. Tutte le macchine virtuali e le istanze del ruolo dei servizi cloud distribuite con il modello di distribuzione classica esistono all'interno di un servizio cloud, a cui viene assegnato un indirizzo IP virtuale (VIP) pubblico *dinamico*. Facoltativamente, è possibile assegnare un indirizzo IP pubblico *statico*, detto [indirizzo IP riservato](virtual-networks-reserved-public-ip.md), come indirizzo VIP. Gli indirizzi IP pubblici possono essere assegnati a singole macchine virtuali o istanze del ruolo dei servizi cloud distribuite con il modello di distribuzione classica. Questi sono detti [indirizzi IP pubblici a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md) e possono essere assegnati in modo dinamico.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>È possibile riservare un indirizzo IP privato per una macchina virtuale che verrà creata in un secondo momento?
No. Non è possibile riservare un indirizzo IP privato. Se un indirizzo IP privato è disponibile, viene assegnato a una macchina virtuale o a un'istanza del ruolo dal server DHCP, indipendentemente dal fatto che la macchina virtuale sia o meno quella a cui si vuole assegnare l'indirizzo IP privato. È possibile tuttavia cambiare l'indirizzo IP privato di una macchina virtuale già creata con qualsiasi indirizzo IP privato disponibile.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Gli indirizzi IP privati vengono modificati per le macchine virtuali in una rete virtuale?
Dipende. Se la macchina virtuale è stata distribuita tramite Resource Manager, non vengono modificati, indipendentemente dal fatto che l'indirizzo IP sia stato assegnato con il metodo di allocazione statica o dinamica. Se la macchina virtuale è stata distribuita tramite il modello di distribuzione classica, gli indirizzi IP possono essere modificati quando una macchina virtuale viene avviata dopo essere stata arrestata (deallocata). L'indirizzo viene rilasciato da una macchina virtuale distribuita tramite uno dei modelli di distribuzione quando la macchina virtuale viene eliminata.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>È possibile assegnare manualmente indirizzi IP alle schede di interfaccia di rete all'interno del sistema operativo di una macchina virtuale?
Sì, ma non è consigliabile a meno che non sia necessario, ad esempio quando si assegnano più indirizzi IP a una macchina virtuale. Per informazioni dettagliate, vedere [Aggiunta di più indirizzi IP a una macchina virtuale](virtual-network-multiple-ip-addresses-portal.md#os-config). Se l'indirizzo IP assegnato a una scheda di interfaccia di rete di Azure collegata a una macchina virtuale viene modificato e l'indirizzo IP all'interno del sistema operativo della macchina virtuale è differente, si perde la connessione alla macchina virtuale.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Se si arresta il funzionamento di uno slot di distribuzione del servizio cloud o di una macchina virtuale dall'interno del sistema operativo, cosa accade agli indirizzi IP?
Nessun pacchetto. Gli indirizzi IP, pubblici, privati o indirizzi VIP pubblici, rimangano assegnati alla relativa macchina virtuale o allo slot di distribuzione del servizio cloud.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>È possibile spostare le macchine virtuali da una subnet a un'altra in una rete virtuale senza ripetere la distribuzione?
Sì. Per altre informazioni, vedere l'articolo [Come spostare una macchina virtuale o un'istanza del ruolo in un'altra subnet](virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>È possibile configurare un indirizzo MAC statico per la macchina virtuale?
No. Un indirizzo MAC non può essere configurato in modo statico.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>L'indirizzo MAC rimarrà invariato per la macchina virtuale dopo averla creata?
Sì, l'indirizzo MAC rimane invariato sia che la macchina virtuale venga distribuita con il modello di distribuzione classica o Resource Manager, finché non viene eliminata. In precedenza se la macchina virtuale veniva arrestata, ovvero deallocata, l'indirizzo MAC veniva rilasciato. Ora viene mantenuto anche quando la macchina virtuale è in stato deallocato. L'indirizzo MAC rimarrà assegnato all'interfaccia di rete finché l'interfaccia non viene eliminata oppure non viene modificato l'indirizzo IP privato assegnato alla configurazione IP primaria dell'interfaccia di rete primaria. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>È possibile eseguire la connessione a Internet da una macchina virtuale in una rete virtuale?
Sì. Tutte le macchine virtuali e le istanze del ruolo dei servizi cloud distribuite all'interno di una rete virtuale possono connettersi a Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Servizi di Azure che si connettono a reti virtuali

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>È possibile usare le app Web del servizio app di Azure con una rete virtuale?
Sì. È possibile distribuire app Web all'interno di una VNet usando un ambiente del servizio app (ambiente del servizio app), connettere il back-end delle app al reti virtuali con l'integrazione VNet e bloccare il traffico in ingresso all'app con gli endpoint di servizio. Per altre informazioni, vedere gli articoli seguenti:

* [Funzionalità di rete del servizio app](../app-service/networking-features.md)
* [Creare app Web in un ambiente del servizio app](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrare l'app con una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Restrizioni di accesso al servizio app](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>È possibile distribuire i servizi cloud con ruolo di lavoro e Web (PaaS) in una rete virtuale?
Sì. Facoltativamente, è possibile distribuire istanze del ruolo dei servizi cloud all'interno di reti virtuali. A tale scopo, specificare il nome della rete virtuale e i mapping dei ruoli e delle subnet nella sezione di configurazione della rete della configurazione del servizio. Non è necessario aggiornare i file binari.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>È possibile connettere un set di scalabilità di macchine virtuali a una VNet?
Sì. È necessario connettere un set di scalabilità di macchine virtuali a un VNet.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>È disponibile un elenco completo dei servizi di Azure da cui è possibile distribuire le risorse in una rete virtuale?
Sì. Per informazioni dettagliate, vedere [Integrazione della rete virtuale per i servizi di Azure](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Come è possibile limitare l'accesso alle risorse di Azure PaaS da una VNet?

Le risorse distribuite tramite alcuni servizi PaaS di Azure, ad esempio archiviazione di Azure e il database SQL di Azure, possono limitare l'accesso di rete a VNet tramite l'uso di endpoint del servizio di rete virtuale o di un collegamento privato di Azure. Per informazioni dettagliate, vedere [Panoramica degli endpoint del servizio rete virtuale](virtual-network-service-endpoints-overview.md), [Panoramica del collegamento privato di Azure](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>È possibile spostare i servizi all’interno e all’esterno delle reti virtuali?
No. Non è possibile spostare i servizi all'interno e all'esterno delle reti virtuali. Per spostare una risorsa in un'altra rete virtuale, è necessario eliminarla e ridistribuirla.

## <a name="security"></a>Sicurezza

### <a name="what-is-the-security-model-for-vnets"></a>Che cos'è il modello di sicurezza per le reti virtuali?
Le reti virtuali sono isolate l'una dall'altra e gli altri servizi sono ospitati nell'infrastruttura di Azure. Una rete virtuale è un limite di attendibilità.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>È possibile limitare il flusso del traffico in ingresso o in uscita alle risorse connesse alla rete virtuale?
Sì. È possibile applicare [gruppi di sicurezza di rete](security-overview.md) a singole subnet all'interno di una rete virtuale e/o a schede di interfaccia di rete collegate a una rete virtuale.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>È possibile implementare un firewall tra le risorse connesse alla rete virtuale?
Sì. È possibile distribuire [appliance virtuali di rete](https://azure.microsoft.com/marketplace/?term=firewall) di diversi fornitori tramite Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Sono disponibili informazioni sulla protezione di reti virtuali?
Sì. Per informazioni dettagliate, vedere [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>API, schemi e strumenti

### <a name="can-i-manage-vnets-from-code"></a>È possibile gestire le reti virtuali dal codice?
Sì. È possibile usare le API REST per reti virtuali nei modelli di distribuzione [Azure Resource Manager](/rest/api/virtual-network) e [classica](https://go.microsoft.com/fwlink/?LinkId=296833) .

### <a name="is-there-tooling-support-for-vnets"></a>È disponibile il supporto degli strumenti per le reti virtuali?
Sì. Altre informazioni:
- Uso del portale di Azure per distribuire reti virtuali con i modelli di distribuzione [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) e [classica](virtual-networks-create-vnet-classic-pportal.md).
- Uso di PowerShell per gestire reti virtuali distribuite con i modelli di distribuzione [Resource Manager](/powershell/module/az.network) e [classica](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0).
- Uso dell'interfaccia della riga di comando di Azure per distribuire e gestire reti virtuali distribuite con i modelli di distribuzione [Resource Manager](/cli/azure/network/vnet) e [classica](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources).  

## <a name="vnet-peering"></a>Peering reti virtuali

### <a name="what-is-vnet-peering"></a>Che cos'è il peering di reti virtuali?
Il peering di reti virtuali consente di connettere le reti virtuali. Usando una connessione di peering di reti virtuali è possibile instradare il traffico tra le reti in modo privato tramite indirizzi IPv4. Le macchine virtuali nelle reti virtuali con peering possono comunicare tra loro come se si trovassero nella stessa rete. Queste reti virtuali possono trovarsi in aree geografiche uguali o diverse. In questo secondo caso, si parla di peering di reti virtuali globale. Le connessioni di peering di reti virtuali possono essere create anche tra sottoscrizioni di Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>È possibile creare una connessione di peering per una rete virtuale in un'area diversa?
Sì. Il peering di reti virtuali globale consente di eseguire il peering di reti virtuali in aree diverse. Il peering VNet globale è disponibile in tutte le aree pubbliche di Azure, nelle aree del cloud Cina e nelle aree del cloud per enti pubblici. Non è possibile eseguire il peering a livello globale dalle aree pubbliche di Azure alle aree del cloud nazionale.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Quali sono i vincoli correlati al peering VNet globale e ai bilanciamenti del carico?
Se le due reti virtuali in due aree diverse eseguono il peering sul peering VNet globale, non è possibile connettersi a risorse che si trovano dietro un Load Balancer di base tramite l'indirizzo IP front-end della Load Balancer. Questa restrizione non esiste per un Load Balancer Standard.
Le risorse seguenti possono usare i bilanciamenti del carico di base, che significa che non è possibile raggiungerli tramite l'indirizzo IP Front-End Load Balancer rispetto al peering VNet globale. È tuttavia possibile usare il peering VNet globale per raggiungere le risorse direttamente tramite gli IP VNet privati, se consentito. 
- Macchine virtuali dietro i bilanciamenti del carico Basic
- Set di scalabilità di macchine virtuali con bilanciamento del carico di base 
- Cache Redis 
- SKU del gateway applicazione (V1)
- Service Fabric
- MI SQL
- Gestione API
- Servizio Dominio di Active Directory (aggiunge)
- App per la logica
- HDInsight
-   Azure Batch
- Ambiente del servizio app

È possibile connettersi a queste risorse tramite ExpressRoute o da VNet a VNet tramite gateway VNet.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>È possibile abilitare il peering reti virtuali se le reti virtuali appartengono a sottoscrizioni all'interno di diversi tenant di Azure Active Directory?
Sì. È possibile stabilire il peering reti virtuali (sia locale che globale) se le sottoscrizioni appartengono a tenant di Azure Active Directory diversi. Questa operazione può essere eseguita tramite PowerShell o l'interfaccia della riga di comando. L'uso del portale non è ancora supportato.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>La connessione al peering rete virtuale è nello stato *Avviato*, per quale motivo non è possibile connettersi?
Se la connessione di peering è in uno stato *avviato* , significa che è stato creato un solo collegamento. È necessario creare un collegamento bidirezionale per stabilire una connessione. Ad esempio, per eseguire il peering di rete virtuale A a rete virtuale B, un collegamento deve essere creato da rete virtuale A a rete virtuale B e da rete virtuale B a rete virtuale A. La creazione di entrambi i collegamenti comporterà la modifica dello stato su *Connected*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>La connessione di peering di reti virtuali è in stato *Disconnesso*. Per quale motivo non è possibile connettersi?
Se la connessione di peering di VNet è in uno stato *disconnesso* , significa che uno dei collegamenti creati è stato eliminato. Per ristabilire una connessione peering, sarà necessario eliminare il collegamento e ricrearlo.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>È possibile eseguire il peering di rete virtuale con una rete virtuale in una sottoscrizione diversa?
Sì. È possibile eseguire il peering di reti virtuali tra sottoscrizioni e aree.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>È possibile eseguire il peering di due reti virtuali con gli intervalli di indirizzi sovrapposti o corrispondenti?
No. Gli spazi indirizzi non devono sovrapporsi per consentire il peering reti virtuali.

### <a name="how-much-do-vnet-peering-links-cost"></a>Quanto costa eseguire il peering di più collegamenti di reti virtuali?
Non sono previsti addebiti per la creazione di una connessione peering di reti virtuali. Viene addebitato il trasferimento dei dati tra connessioni peering. [Vedere qui](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Il traffico peering di rete virtuale è crittografato?
No. Il traffico tra le risorse in reti virtuali con peering è privato e isolato. Il messaggio rimane completamente nel Backbone di Microsoft.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Perché la connessione peering è in uno stato *disconnesso* ?
Le connessioni peering di rete virtuale passano allo stato *Disconnesso* quando viene eliminato un collegamento di peering della rete virtuale. È necessario eliminare entrambi i collegamenti per ristabilire una connessione peering.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Se si esegue il peering di rete virtuale A a rete virtuale B e il peering di rete virtuale B a rete virtuale C, significa che è stato eseguito il peering delle reti virtuali A e C?
No. Il peering transitivo non è supportato. Si dovrà eseguire il peering delle reti virtuali A e C affinché questo si verifichi.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Sono presenti limitazioni relative alla larghezza di banda per le connessioni peering?
No. La rete virtuale di peering, sia locale che globale, non impone restrizioni relative alla larghezza di banda. La larghezza di banda è limitata solo dalla macchina virtuale o dalla risorsa di calcolo.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Come è possibile risolvere I problemi relativi al peering di VNet?
Ecco una guida per la [risoluzione dei problemi](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) che è possibile provare.

## <a name="virtual-network-tap"></a>TAP di rete virtuale

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Quali aree di Azure sono disponibili per la TAP di rete virtuale?
L'anteprima di TAP per la rete virtuale è disponibile in tutte le aree di Azure. Le interfacce di rete monitorate, la risorsa punto di accesso terminale di rete virtuale e l'agente di raccolta o la soluzione di analisi devono essere distribuiti nella stessa area.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>La TAP di rete virtuale supporta qualsiasi funzionalità di filtraggio dei pacchetti con mirroring?
Le funzionalità di filtro non sono supportate con l'anteprima TAP di rete virtuale. Quando si aggiunge una configurazione TAP a un'interfaccia di rete, una deep copy di tutto il traffico in ingresso e in uscita sull'interfaccia di rete viene inviata in streaming alla destinazione TAP.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>È possibile aggiungere più configurazioni TAP a un'interfaccia di rete monitorata?
Un'interfaccia di rete monitorata può avere solo una configurazione TAP. Verificare con la soluzione dei singoli [partner](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) la possibilità di trasmettere più copie del traffico tap agli strumenti di analisi di propria scelta.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Può la stessa risorsa TAP di rete virtuale aggregare il traffico dalle interfacce di rete monitorate in più di una rete virtuale?
Sì. La stessa risorsa TAP di rete virtuale può essere utilizzata per aggregare il traffico con mirroring da interfacce di rete monitorate in reti virtuali con peering nella stessa sottoscrizione o in una sottoscrizione diversa. La risorsa TAP di rete virtuale e il bilanciamento del carico di destinazione o l'interfaccia di rete di destinazione devono essere nella stessa sottoscrizione. Tutte le sottoscrizioni devono trovarsi nello stesso tenant di Azure Active Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Ci sono considerazioni sulle prestazioni del traffico di produzione se abilito una configurazione TAP di rete virtuale su un'interfaccia di rete?

Il tocco della rete virtuale è in anteprima. Durante l'anteprima, non esiste alcun contratto di servizio. La funzionalità non deve essere utilizzata per i carichi di lavoro di produzione. Quando un'interfaccia di rete della macchina virtuale è abilitata con una configurazione TAP, le stesse risorse nell'host di Azure allocato alla macchina virtuale per l'invio del traffico di produzione vengono usate per eseguire la funzione di mirroring e inviare i pacchetti con mirroring. Selezionare la dimensione corretta della macchina virtuale [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per assicurarsi che siano disponibili risorse sufficienti affinché la macchina virtuale possa inviare il traffico di produzione e il traffico con mirroring.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>Il networking accelerato per [Linux](create-vm-accelerated-networking-cli.md) o [Windows](create-vm-accelerated-networking-powershell.md) è supportato con la TAP di rete virtuale?

Sarà possibile aggiungere una configurazione TAP a un'interfaccia di rete collegata a una macchina virtuale con networking accelerato abilitato. Ma le prestazioni e la latenza sulla macchina virtuale saranno influenzate dall'aggiunta della configurazione TAP, dato che l'offload per il mirroring del traffico non è attualmente supportato dal networking accelerato di Azure.

## <a name="virtual-network-service-endpoints"></a>Endpoint servizio di rete virtuale

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Qual è la sequenza corretta di operazioni per la configurazione di endpoint di servizio in un servizio di Azure?
Esistono due passaggi per proteggere una risorsa del servizio di Azure tramite gli endpoint del servizio:
1. Attivare gli endpoint di servizio per il servizio di Azure.
2. Configurare elenchi di controllo di accesso di rete virtuale nel servizio di Azure.

Il primo passaggio è un'operazione sul lato rete e il secondo passaggio è un'operazione sul lato risorsa del servizio. Entrambi i passaggi possono essere eseguiti dallo stesso amministratore o da amministratori diversi in base alle autorizzazioni di Controllo degli accessi in base al ruolo concesse al ruolo amministratore. È consigliabile attivare gli endpoint di servizio per la rete virtuale prima di configurare gli elenchi di controllo di accesso di rete virtuale sul lato servizio di Azure. Di conseguenza, i passaggi devono essere eseguiti nella sequenza elencata in precedenza per configurare gli endpoint del servizio VNet.

>[!NOTE]
> Entrambe le operazioni descritte in precedenza devono essere completate prima di poter limitare l'accesso al servizio di Azure alla rete virtuale e alla subnet consentite. Se si attivano esclusivamente gli endpoint di servizio per il servizio di Azure sul lato rete, non è possibile limitare l'accesso. È inoltre necessario configurare elenchi di controllo di accesso di rete virtuale sul lato servizio di Azure.

Alcuni servizi, ad esempio SQL e CosmosDB, consentono eccezioni alla sequenza precedente tramite il flag **IgnoreMissingVnetServiceEndpoint** . Quando il flag è impostato su **true**, gli ACL VNet possono essere impostati sul lato servizio di Azure prima di configurare gli endpoint del servizio sul lato rete. I servizi di Azure forniscono questo flag per aiutare i clienti nei casi in cui sono configurati firewall IP specifici nei servizi di Azure e l'attivazione degli endpoint di servizio sul lato rete può causare una perdita di connettività perché l'indirizzo IP di origine cambia passando da indirizzo IPv4 pubblico a indirizzo privato. La configurazione di elenchi di controllo di accesso di rete virtuale sul lato servizio di Azure prima dell'impostazione degli endpoint di servizio sul lato rete può aiutare a evitare la perdita di connettività.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Tutti i servizi di Azure si trovano nella rete virtuale di Azure fornita dal cliente? Come funzionano gli endpoint di servizio di rete virtuale con i servizi di Azure?

No, non tutti i servizi di Azure si trovano nella rete virtuale del cliente. La maggior parte dei servizi dati di Azure, ad esempio archiviazione di Azure, SQL di Azure e Azure Cosmos DB, è costituita da servizi multi-tenant a cui è possibile accedere tramite indirizzi IP pubblici. Per altre informazioni sull'integrazione della rete virtuale per i servizi di Azure, vedere [qui](virtual-network-for-azure-services.md). 

Quando si usa la funzionalità di endpoint di servizio di rete virtuale (attivando l'endpoint di servizio di rete virtuale sul lato rete e configurando elenchi di controllo di accesso di rete virtuale appropriati sul lato servizio di Azure), l'accesso a un servizio di Azure è consentito solo da una rete virtuale e una subnet consentite.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>In che modo gli endpoint di servizio di rete virtuale forniscono la sicurezza?

La funzionalità dell'endpoint del servizio VNet (che attiva l'endpoint del servizio VNet sul lato rete e la configurazione degli ACL VNet appropriati sul lato servizio di Azure) limita l'accesso ai servizi di Azure al VNet e alla subnet consentiti, fornendo in tal modo una sicurezza a livello di rete e un isolamento del traffico del servizio di Azure. Tutto il traffico che usa gli endpoint di servizio di rete virtuale transita attraverso il backbone Microsoft, fornendo così un altro livello di isolamento dalla rete Internet pubblica. Inoltre, i clienti possono scegliere di rimuovere completamente l'accesso a Internet pubblico dalle risorse dei servizi di Azure e di consentire il traffico solo dalla propria rete virtuale tramite una combinazione di firewall IP ed elenchi di controllo di accesso di rete virtuale, proteggendo così le risorse dei servizi di Azure dall'accesso non autorizzato.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Che cosa viene protetto dall'endpoint di servizio di rete virtuale, le risorse di rete virtuale o il servizio di Azure?
Gli endpoint di servizio di rete virtuale consentono di proteggere le risorse dei servizi di Azure. Le risorse di rete virtuale vengono protette tramite gruppi di sicurezza di rete (NSG, Network Security Group).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Sono previsti costi per l'uso di endpoint di servizio di rete virtuale?

No, non sono previsti costi aggiuntivi per l'uso di endpoint di servizio di rete virtuale.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>È possibile attivare gli endpoint di servizio di rete virtuale e configurare elenchi di controllo di accesso di rete virtuale se la rete virtuale e le risorse dei servizi di Azure fanno parte di sottoscrizioni diverse?

Sì, è possibile. Le reti virtuali e le risorse dei servizi di Azure possono trovarsi nella stessa sottoscrizione o in sottoscrizioni diverse. L'unico requisito è che sia la rete virtuale che le risorse dei servizi di Azure si trovino nello stesso tenant di Active Directory (AD).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>È possibile attivare gli endpoint di servizio di rete virtuale e configurare elenchi di controllo di accesso di rete virtuale se la rete virtuale e le risorse dei servizi di Azure fanno parte di tenant di AD diversi?
Sì, è possibile quando si usano gli endpoint di servizio per archiviazione di Azure e Azure Key Vault. Per i servizi rimanenti, gli endpoint di servizio VNet e gli ACL VNet non sono supportati nei tenant di AD.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Un indirizzo IP di un dispositivo locale connesso tramite il gateway di rete virtuale di Azure (VPN) o il gateway ExpressRoute può accedere al servizio Azure PaaS tramite gli endpoint del servizio VNet?
per impostazione predefinita, le risorse del servizio associate alle reti virtuali non sono raggiungibili da reti locali. Se si vuole consentire il traffico dall'ambiente locale, è necessario autorizzare anche gli indirizzi IP pubblici (generalmente NAT) dall'ambiente locale o da ExpressRoute. Questi indirizzi IP possono essere aggiunti tramite la configurazione del firewall IP per le risorse dei servizi di Azure.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>È possibile usare la funzionalità endpoint del servizio VNet per proteggere il servizio di Azure in più subnet all'interno di una rete virtuale o in più reti virtuali?
Per proteggere i servizi di Azure in più subnet all'interno di una rete virtuale o in più reti virtuali, abilitare gli endpoint di servizio sul lato rete in ognuna delle subnet in modo indipendente e quindi proteggere le risorse dei servizi di Azure in tutte le subnet impostando gli ACL VNet appropriati sul lato del servizio di Azure.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>In che modo è possibile filtrare il traffico in uscita da una rete virtuale verso i servizi di Azure continuando a usare gli endpoint di servizio?
Se si vuole verificare o filtrare il traffico destinato a un servizio di Azure da una rete virtuale, è possibile distribuire un'appliance virtuale di rete nella rete virtuale. È quindi possibile applicare gli endpoint di servizio alla subnet in cui è distribuita l'appliance virtuale di rete e proteggere le risorse del servizio di Azure solo in questa subnet tramite elenchi di controllo di accesso di rete virtuale. Questo scenario può essere utile anche se si vuole limitare l'accesso al servizio di Azure dalla rete virtuale solo a risorse di Azure specifiche, usando il filtro dell'appliance virtuale di rete. Per altre informazioni, vedere il [traffico in uscita con le appliance virtuali di rete](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Cosa accade quando si accede a un account del servizio di Azure con un elenco di controllo di accesso (ACL) di rete virtuale abilitato dall'esterno del VNet?
Viene restituito l'errore HTTP 403 o HTTP 404.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Le subnet di una rete virtuale create in aree diverse possono accedere a un account del servizio di Azure in un'altra area? 
Sì, per la maggior parte dei servizi di Azure, le reti virtuali create in aree diverse possono accedere ai servizi di Azure in un'altra area tramite gli endpoint di servizio di rete virtuale. Se, ad esempio un account di Azure Cosmos DB si trova nell'area Stati Uniti occidentali o Stati Uniti orientali e le reti virtuali si trovano in più aree, la rete virtuale può accedere ad Azure Cosmos DB. I servizi Archiviazione e SQL rappresentano eccezioni e per natura sono legati all'area, quindi sia la rete virtuale che il servizio di Azure devono trovarsi nella stessa area.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Un servizio di Azure può avere sia un ACL VNet che un firewall IP?
Sì, un ACL VNet e un firewall IP possono coesistere. Queste due funzionalità si completano reciprocamente per garantire isolamento e sicurezza.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Cosa accade se si elimina una rete virtuale o una subnet con un endpoint di servizio attivato per il servizio di Azure?
L'eliminazione delle reti virtuali e quella delle subnet sono operazioni indipendenti e sono supportate anche quando gli endpoint di servizio sono attivati per i servizi di Azure. Nei casi in cui per i servizi di Azure sono configurati ACL VNet, per le reti virtuali e le subnet, le informazioni relative all'ACL VNet associate al servizio di Azure vengono disabilitate quando viene eliminato un VNet o una subnet con l'endpoint del servizio VNet attivato.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Cosa accade se viene eliminato un account del servizio di Azure con un endpoint del servizio VNet?
L'eliminazione di un account del servizio di Azure è un'operazione indipendente ed è supportata anche quando l'endpoint del servizio è abilitato sul lato rete e gli ACL VNet sono configurati sul lato servizio di Azure. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Cosa accade all'indirizzo IP di origine di una risorsa, come una macchina virtuale in una subnet, in cui viene abilitato un endpoint di servizio di rete virtuale?
Quando gli endpoint servizio di rete virtuale vengono abilitati, gli indirizzi IP di origine delle risorse nella subnet della rete virtuale passano dall'usare indirizzi IPV4 pubblici a indirizzi privati della rete virtuale di Azure per il traffico verso il servizio di Azure. Si noti che questo può causare un errore nei servizi di Azure che possono essere impostati su un indirizzo IPV4 pubblico precedente nei servizi di Azure. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>La route dell'endpoint di servizio ha sempre la precedenza?
Gli endpoint di servizio aggiungono una route di sistema che ha la precedenza sulle route BGP e fornisce un routing ottimale per il traffico dell'endpoint del servizio. Gli endpoint di servizio instradano sempre il traffico del servizio direttamente dalla rete virtuale al servizio nella rete backbone di Microsoft Azure. Per ulteriori informazioni su come Azure seleziona una route, vedere [routing del traffico di rete virtuale di Azure](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Come funziona un gruppo di sicurezza di rete in una subnet con gli endpoint di servizio?
Per raggiungere il servizio di Azure, i gruppi di sicurezza di rete devono consentire la connettività in uscita. Se i gruppi di sicurezza di rete sono aperti a tutto il traffico Internet in uscita, il traffico dell'endpoint di servizio dovrebbe funzionare. È anche possibile limitare il traffico in uscita solo agli IP del servizio che usano i tag del servizio.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Quali autorizzazioni sono necessarie per configurare gli endpoint di servizio?
Gli endpoint di servizio possono essere configurati in una rete virtuale in modo indipendente, da un utente con accesso in scrittura alla rete virtuale. Per proteggere le risorse dei servizi di Azure in una VNet, l'utente deve disporre dell'autorizzazione **Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action** per le subnet da aggiungere. Questa autorizzazione è inclusa nel ruolo di amministratore del servizio predefinito e può essere modificata creando ruoli personalizzati. Altre informazioni sui ruoli predefiniti e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>È possibile filtrare il traffico di rete virtuale verso i servizi di Azure consentendo l'accesso solo a risorse specifiche dei servizi di Azure tramite gli endpoint di servizio di rete virtuale? 

I criteri degli endpoint di servizio di rete virtuale permettono di filtrare il traffico di rete virtuale verso i servizi di Azure, consentendo l'accesso solo a risorse specifiche dei servizi di Azure tramite gli endpoint di servizio. I criteri degli endpoint forniscono un controllo di accesso granulare per il traffico di rete virtuale verso i servizi di Azure. È possibile ottenere altre informazioni sui criteri degli endpoint di servizio [qui](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Azure Active Directory (Azure AD) supportano gli endpoint del servizio VNet?

Azure Active Directory (Azure AD) non supporta gli endpoint di servizio in modo nativo. Per un elenco completo dei servizi di Azure che supportano gli endpoint di servizio VNet, vedere [qui](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Si noti che il tag "Microsoft. AzureActiveDirectory" elencato in servizi che supportano gli endpoint di servizio viene usato per supportare gli endpoint di servizio per ADLS generazione 1. Per ADLS gen 1, l'integrazione della rete virtuale per Azure Data Lake Storage Gen1 usa la sicurezza degli endpoint di servizio della rete virtuale tra la rete virtuale e la Azure Active Directory (Azure AD) per generare attestazioni di sicurezza aggiuntive nel token di accesso. Queste attestazioni vengono quindi usate per autenticare la rete virtuale nell'account Data Lake Storage Gen1 e consentire l'accesso. Altre informazioni sull' [integrazione di Azure Data Lake Store generazione 1 VNet](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Ci sono limiti per il numero di endpoint di servizio di rete virtuale che è possibile configurare dalla rete virtuale?
Non ci sono limiti per il numero totale di endpoint di servizio di rete virtuale in una rete virtuale. Per una risorsa del servizio di Azure, ad esempio un account di archiviazione di Azure, i servizi possono applicare limiti al numero di subnet usate per la protezione della risorsa. La tabella seguente illustra alcuni limiti di esempio: 

|Servizio di Azure| Limiti per le regole della rete virtuale|
|---|---|
|Archiviazione di Azure| 100|
|SQL di Azure| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Hub eventi di Azure|   128|
|Bus di servizio di Azure| 128|
|Azure Data Lake Store V1|  100|
 
>[!NOTE]
> I limiti sono soggetti a modifiche a discrezione del servizio di Azure. Per i dettagli, fare riferimento alla documentazione del servizio interessato. 




  



