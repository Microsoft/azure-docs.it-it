---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0eb509d543bc9b4a8846319126185eb1f27ec7a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953457"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP è supportato in tutti gli SKU del gateway VPN di Azure?

BGP è supportato in tutti gli SKU del gateway VPN ad eccezione dello SKU Basic.

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>È possibile usare BGP con i gateway VPN di Criteri di Azure?

No, BGP è supportato unicamente nei gateway VPN basati su route.

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>Quali numeri ASN (Autonomous System Number) è possibile usare?

È possibile usare i propri numeri ASN pubblici o quelli privati sia per le reti locali che per le reti virtuali di Azure. Non è possibile usare gli intervalli riservati da Azure o IANA.

I seguenti numeri ASN sono riservati da Azure o da IANA:
* Numeri ASN riservati da Azure:

  * ASN pubblici: 8074, 8075, 12076
  * ASN privati: 65515, 65517, 65518, 65519, 65520
* ASN [riservati da IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):

  * 23456, 64496-64511, 65535-65551 e 429496729

Non è possibile specificare questi numeri ASN per connettere i dispositivi VPN locali ai gateway VPN di Azure.

### <a name="can-i-use-32-bit-4-byte-asns"></a>È possibile usare numeri ASN a 32 bit (4 byte)?

Sì, il gateway VPN ora supporta ASN a 32 bit (4 byte). Per configurare l'uso di ASN in formato decimale, usare PowerShell, l'interfaccia della riga di comando di Azure o Azure SDK.

### <a name="what-private-asns-can-i-use"></a>Quali numeri ASN privati è possibile usare?

Gli intervalli utilizzabili di ASN privati sono:

* 64512-65514 e 65521-65534

Questi ASN non sono riservati per l'uso in IANA o Azure e possono quindi essere assegnati al gateway VPN di Azure.

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>Quale indirizzo viene usato dal gateway VPN per l'indirizzo IP del peer BGP?

Per impostazione predefinita, il gateway VPN alloca un singolo indirizzo IP dall'intervallo di *GatewaySubnet* per i gateway VPN di tipo attivo-standby oppure due indirizzi IP per i gateway VPN di tipo attivo-attivo. Questi indirizzi vengono allocati automaticamente durante la creazione del gateway VPN. È possibile ottenere l'indirizzo IP BGP effettivo allocato usando PowerShell o individuarlo nel portale di Azure. In PowerShell usare **Get-AzVirtualNetworkGateway** e cercare la proprietà **bgpPeeringAddress**. Nel portale di Azure, nella pagina **Configurazione gateway**, controllare la proprietà **Configura ASN BGP**.

Se i router VPN locali usano indirizzi IP **APIPA** (169.254.x.x) come indirizzi IP di BGP, è necessario specificare un **indirizzo IP di BGP aggiuntivo di Azure APIPA** nel gateway VPN di Azure. Il gateway VPN di Azure seleziona l'indirizzo APIPA da usare con il peer BGP APIPA locale specificato nel gateway di rete locale o un indirizzo IP privato per il peer BGP locale non APIPA. Per altre informazioni, vedere [Configurare BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quali sono i requisiti per gli indirizzi IP dei peer BGP nel dispositivo VPN?

L'indirizzo del peer BGP locale non deve essere uguale all'indirizzo IP pubblico del dispositivo VPN né essere incluso nello spazio indirizzi della rete virtuale del gateway VPN. Usare un indirizzo IP diverso nel dispositivo VPN per il peer BGP. Può essere un indirizzo assegnato all'interfaccia di loopback nel dispositivo, ossia un normale indirizzo IP o un indirizzo APIPA. Se il dispositivo usa l'indirizzo APIPA per BGP, è necessario specificare un indirizzo IP di BGP APIPA nel gateway VPN di Azure, come descritto in [Configurare BGP](../articles/vpn-gateway/bgp-howto.md). Specificare questo indirizzo nel gateway di rete locale corrispondente che rappresenta la posizione.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Cosa occorre specificare come prefissi di indirizzo per il gateway di rete locale quando si usa BGP?

> [!IMPORTANT]
>
>Si tratta di una modifica rispetto al requisito precedentemente documentato. Se si usa BGP per una connessione, lasciare il campo **Spazio indirizzi** vuoto per la risorsa gateway di rete locale corrispondente. Il gateway VPN di Azure aggiunge internamente una route host all'indirizzo IP del peer BGP locale tramite il tunnel IPsec. Non aggiungere la route /32 nel campo **Spazio indirizzi**. È ridondante e se si usa un indirizzo APIPA come indirizzo IP di BGP del dispositivo VPN locale, non è possibile aggiungerlo a questo campo. Se si aggiungono altri prefissi nel campo **Spazio indirizzi**, verranno aggiunti come route statiche sul gateway VPN di Azure, in aggiunta alle route acquisite tramite BGP.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>È possibile usare lo stesso numero ASN sia per le reti VPN locali che per le reti virtuali di Azure?

No, è necessario assegnare ASN diversi alle reti locali e alle reti virtuali di Azure, se vengono connesse insieme tramite BGP. Ai gateway VPN di Azure è assegnato un ASN predefinito 65515, sia che BGP sia abilitato o meno per la connettività cross-premise. È possibile sostituire questo valore predefinito assegnando un ASN diverso durante la creazione del gateway VPN. In alternativa è possibile cambiarlo dopo aver creato il gateway. Sarà necessario assegnare i numeri ASN locali ai gateway di rete locali di Azure corrispondenti.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Quali prefissi di indirizzo vengono segnalati all'utente dai gateway VPN di Azure?

I gateway annunciano le route seguenti ai dispositivi BGP locali:

* I prefissi degli indirizzi di rete virtuale.
* I prefissi degli indirizzi per ogni gateway di rete locale connesso al gateway VPN di Azure.
* Le route ottenute da altre sessioni di peering BGP connesse al gateway VPN di Azure, ad eccezione delle route predefinite o sovrapposte a qualsiasi prefisso di rete virtuale.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Quanti prefissi è possibile annunciare al gateway VPN di Azure?

Il gateway VPN di Azure supporta fino a 4000 prefissi. La sessione BGP viene eliminata se il numero di prefissi supera il limite.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>È possibile segnalare la route predefinita (0.0.0.0/0) ai gateway VPN di Azure?

Sì. Si noti che in questo modo tutto il traffico in ingresso nella rete virtuale viene forzato verso il sito locale. Si impedisce inoltre alle VM della rete virtuale di accettare comunicazioni pubbliche provenienti direttamente da Internet, come RDP o SSH da Internet alle VM.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>È possibile annunciare gli stessi prefissi della propria rete virtuale?

No, l'annuncio degli stessi prefissi degli indirizzi della rete virtuale verrà bloccato o filtrato da Azure. È tuttavia possibile annunciare un prefisso che rappresenta un superset di quello interno alla rete virtuale. 

Ad esempio, se la rete virtuale usa lo spazio di indirizzi 10.0.0.0/16, è possibile annunciare 10.0.0.0/8, ma non 10.0.0.0/16 o 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>È possibile usare BGP con le connessioni tra reti virtuali?

Sì, è possibile usare BGP sia per connessioni cross-premise che per connessioni tra reti virtuali.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>È possibile combinare connessioni BGP con connessioni non BGP per i gateway VPN di Azure?

Sì, è possibile combinare connessioni BGP e connessioni non BGP per lo stesso gateway VPN di Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Il gateway VPN di Azure supporta il routing di transito BGP?

Sì, il routing di transito BGP è supportato, con l'eccezione che i gateway VPN di Azure non annunciano le route predefinite ad altri peer BGP. Per abilitare il routing di transito tra più gateway VPN di Azure, è necessario abilitare BGP in tutte le connessioni intermedie tra reti virtuali. Per altre informazioni, vedere [About BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md) (Informazioni su BGP).

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>È possibile stabilire più di un tunnel tra il gateway VPN di Azure e la rete locale?

Sì, è possibile stabilire più di un tunnel VPN da sito a sito tra un gateway VPN di Azure e la rete locale. Si noti che tutti questi tunnel verranno conteggiati rispetto al numero totale di tunnel per i gateway VPN di Azure ed è necessario abilitare BGP in tutti.

Ad esempio, se sono presenti due tunnel ridondanti tra il gateway VPN di Azure e una delle reti locali, verranno consumati 2 tunnel della quota totale disponibile per il gateway VPN di Azure.

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>È possibile stabilire più tunnel tra due reti virtuali di Azure con BGP?

Sì, ma almeno uno dei gateway di rete virtuale deve avere la configurazione attiva/attiva.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>È possibile usare BGP per una VPN da sito a sito in una configurazione di coesistenza VPN da sito a sito e Azure ExpressRoute?

Sì.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Cosa è necessario aggiungere al dispositivo VPN locale per la sessione di peering BGP?

Aggiungere una route host dell'indirizzo IP del peer BGP di Azure nel dispositivo VPN. Questa route punta al tunnel VPN da sito a sito IPsec. Ad esempio, se l'indirizzo IP del peer VPN di Azure è 10.12.255.30, è necessario aggiungere una route host per 10.12.255.30 con un'interfaccia per hop successivo dell'interfaccia del tunnel IPsec corrispondente nel dispositivo VPN.

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>Il gateway di rete virtuale supporta BFD per le connessioni da sito a sito con BGP?

No. BFD (Bidirectional Forwarding Detection) è un protocollo che può essere usato con BGP per rilevare l'inattività di vicini più rapidamente rispetto ai "keepalive" BGP standard. BFD usa timer di frazioni di secondo progettati per funzionare in ambienti LAN, ma non in connessioni tramite Internet pubblico o reti WAN.

Per le connessioni tramite Internet pubblico, la presenza di alcuni pacchetti ritardati o anche eliminati non è insolita, di conseguenza l'introduzione di questi timer aggressivi potrebbe aggiungere instabilità che potrebbero causare la conseguente attenuazione delle route da parte di BGP. In alternativa, è possibile configurare il dispositivo locale con timer di durata inferiore all'intervallo di attesa di "keepalive" predefinito di 60 secondi e il timer di attesa di 180 secondi. In questo modo si ottiene un tempo di convergenza più rapido.

### <a name="do-azure-vpn-gateways-initiate-bgp-peering-sessions-or-connections"></a>I gateway VPN di Azure avviano sessioni o connessioni di peering BGP?

Il gateway avvierà sessioni di peering BGP agli indirizzi IP del peer BGP locali specificati nelle risorse del gateway di rete locale usando gli indirizzi IP privati sui gateway VPN. A prescindere dal fatto che gli indirizzi IP BGP locali si trovino nell'intervallo APIPA o negli indirizzi IP privati regolari. Se i dispositivi VPN locali usano indirizzi APIPA come IP BGP, è necessario configurare l'altoparlante BGP per avviare le connessioni.