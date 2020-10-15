---
title: 'Gateway VPN di Azure: requisiti di crittografia'
description: Informazioni su come configurare i gateway VPN di Azure in modo da soddisfare i requisiti crittografici per i tunnel VPN S2S cross-premise e per le connessioni da VNet a VNet di Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 499d184d373b896ba7bffcf990693ef5e6ac466b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88032425"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Informazioni sui requisiti di crittografia e i gateway VPN di Azure

Questo articolo illustra come configurare i gateway VPN di Azure per soddisfare i requisiti di crittografia per i tunnel VPN S2S cross-premise e le connessioni da rete virtuale a rete virtuale all'interno di Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Informazioni su IKEv1 e IKEv2 per le connessioni VPN di Azure

Tradizionalmente sono consentite solo le connessioni IKEv1 per SKU Basic e le connessioni IKEv2 consentite per tutti gli SKU del gateway VPN diversi dagli SKU Basic. Gli SKU di base consentono solo una connessione e, insieme ad altre limitazioni quali le prestazioni, i clienti che usano dispositivi legacy che supportano solo i protocolli IKEv1 hanno avuto esperienza limitata. Per migliorare l'esperienza dei clienti che usano i protocolli IKEv1, ora è possibile consentire le connessioni IKEv1 per tutti gli SKU del gateway VPN, ad eccezione dello SKU Basic. Per altre informazioni, vedere [SKU del gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku).

![Connessioni IKEv1 e IKEv2 del gateway VPN di Azure](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Quando si applicano le connessioni IKEv1 e IKEv2 allo stesso gateway VPN, il transito tra queste due connessioni viene abilitato automaticamente.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Informazioni sui parametri di criteri IPsec e IKE per gateway VPN di Azure

Lo standard di protocollo IPsec e IKE supporta un'ampia gamma di algoritmi di crittografia in varie combinazioni. Se non si richiede una combinazione specifica di algoritmi e parametri crittografici, i gateway VPN di Azure usano un set di proposte predefinite. I set di criteri predefiniti sono stati scelti per migliorare l'interoperabilità con un'ampia gamma di dispositivi VPN di terze parti in configurazioni predefinite. Di conseguenza, i criteri e il numero di proposte non possono coprire tutte le possibili combinazioni degli algoritmi di crittografia disponibili e della complessità delle chiavi.

Il set di criteri predefinito per il gateway VPN di Azure è riportato nell'articolo [informazioni sui dispositivi VPN e sui parametri IPSec/IKE per le connessioni del gateway VPN da sito a sito](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Requisiti per la crittografia

Per le comunicazioni che richiedono specifici algoritmi di crittografia o parametri, in genere a causa di requisiti di conformità o di sicurezza, è ora possibile configurare i gateway VPN di Azure per usare un criterio IPsec/IKE personalizzato con algoritmi di crittografia e attendibilità delle chiavi specifici, invece dei set di criteri predefiniti di Azure.

Ad esempio, i criteri della modalità principale di IKEv2 per i gateway VPN di Azure usano solo Diffie-Hellman Gruppo 2 (1024 bit), mentre potrebbe essere necessario specificare gruppi più avanzati da usare in IKE, ad esempio il gruppo 14 (2048-bit), il gruppo 24 (gruppo MODP a 2048 bit) o il bit ECP (gruppi a curva ellittica) 256 o 384 (rispettivamente gruppo 19 e 20). Simili requisiti si applicano anche ai criteri IPsec in modalità rapida.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Criteri IPsec/IKE personalizzati con i gateway VPN di Azure

I gateway VPN di Azure ora supportano i criteri IPsec/IKE personalizzati per connessione. Per una connessione da sito a sito o da rete virtuale a rete virtuale, è possibile scegliere una combinazione specifica di algoritmi di crittografia per IPsec e IKE con attendibilità della chiave, come illustrato nell'esempio seguente:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

È possibile creare un criterio IPsec/IKE e applicarlo a una connessione nuova o esistente.

### <a name="workflow"></a>Flusso di lavoro

1. Creare le reti virtuali, i gateway VPN o i gateway di rete locale per la topologia di connettività, come descritto in altri documenti sulle procedure
2. Creare un criterio IPsec/IKE
3. È possibile applicare i criteri quando si crea una connessione S2S o da rete virtuale a rete virtuale
4. Se la connessione è già stata creata, è possibile applicare o aggiornare i criteri per una connessione esistente

## <a name="ipsecike-policy-faq"></a>Domande frequenti sui criteri IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Vedere [Configurare i criteri IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) per istruzioni dettagliate sulla configurazione dei criteri IPsec/IKE personalizzato su una connessione.

Vedere anche [Connettere più dispositivi VPN basati su criteri](vpn-gateway-connect-multiple-policybased-rm-ps.md) per altre informazioni sull'opzione UsePolicyBasedTrafficSelectors.
