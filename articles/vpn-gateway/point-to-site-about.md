---
title: Informazioni sulle connessioni VPN da punto a sito di Azure | Gateway VPN
description: Questo articolo descrive le connessioni da punto a sito e consente di stabilire quale tipo di autenticazione gateway VPN da punto a sito usare.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 795b6f13913590041b463115c0be65a6201fedab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654065"
---
# <a name="about-point-to-site-vpn"></a>Informazioni sulla VPN da punto a sito

Una connessione gateway VPN da punto a sito (P2S) consente di creare una connessione sicura alla rete virtuale da un singolo computer client. Una connessione da punto a sito viene stabilita avviandola dal computer client. Questa soluzione è utile per i telelavoratori che intendono connettersi alle reti virtuali di Azure da una posizione remota, ad esempio da casa o durante una riunione. Una VPN da punto a sito è anche una soluzione utile da usare al posto di una VPN da sito a sito quando solo pochi client devono connettersi a una rete virtuale. Questo articolo si applica al modello di distribuzione di Azure Resource Manager.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Quale protocollo viene usato da P2S?

Per la VPN da punto a sito può essere usato uno dei protocolli seguenti:

* **Protocollo di® OpenVPN**, un protocollo VPN basato su SSL/TLS. Una soluzione VPN TLS può penetrare i firewall, perché la maggior parte dei firewall apre la porta TCP 443 in uscita, che viene usata da TLS. OpenVPN può essere usato per la connessione da Android, iOS (versioni 11,0 e successive), da dispositivi Windows, Linux e Mac (versioni OSX 10,13 e successive).

* Protocollo SSTP (Secure Socket Tunneling Protocol), un protocollo VPN proprietario basato su TLS. Una soluzione VPN TLS può penetrare i firewall, perché la maggior parte dei firewall apre la porta TCP 443 in uscita, che viene usata da TLS. SSTP è supportato solo nei dispositivi Windows. Azure supporta tutte le versioni di Windows che hanno SSTP (Windows 7 e versioni successive).

* VPN IKEv2, una soluzione VPN IPsec basata su standard. VPN IKEv2 può essere usato per connettersi da dispositivi Mac (versioni OSX 10.11 e successive).


>[!NOTE]
>IKEv2 e OpenVPN per la connessione da punto a sito sono disponibili solo per il modello di distribuzione Resource Manager. Non sono disponibili per il modello di distribuzione classica.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>Modalità di autenticazione del client VPN da punto a sito

L'utente deve essere autenticato prima che Azure possa accettare una connessione VPN da punto a sito. Azure offre due meccanismi per autenticare un utente che esegue la connessione.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autenticazione del certificato nativa di Azure

Quando si usa l'autenticazione del certificato nativa di Azure, per autenticare l'utente che esegue la connessione viene usato un certificato client presente nel dispositivo. I certificati client vengono generati da un certificato radice attendibile e quindi installati in ogni computer client. È possibile usare un certificato radice generato tramite una soluzione aziendale oppure generare un certificato autofirmato.

La convalida del certificato client viene eseguita dal gateway VPN quando viene stabilita la connessione VPN da punto a sito. Il certificato radice è necessario per la convalida e deve essere caricato in Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Eseguire l'autenticazione con l'autenticazione Azure Active Directory nativa

Azure AD autenticazione consente agli utenti di connettersi ad Azure usando le credenziali Azure Active Directory. L'autenticazione Azure AD nativa è supportata solo per il protocollo OpenVPN e Windows 10 e richiede l'uso del [client VPN di Azure](https://go.microsoft.com/fwlink/?linkid=2117554).

Con l'autenticazione Azure AD nativa, è possibile sfruttare le funzionalità di accesso condizionale di Azure AD e Multi-Factor Authentication (multi-factor authentication) per VPN.

A livello generale, è necessario eseguire i passaggi seguenti per configurare l'autenticazione Azure AD:

1. [Configurare un tenant di Azure Active Directory](openvpn-azure-ad-tenant.md)

2. [Abilitare l'autenticazione Azure AD sul gateway](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Scaricare e configurare il client VPN di Azure](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autenticazione con server di dominio Active Directory (AD)

L'autenticazione con un dominio di AD consente agli utenti di connettersi ad Azure usando le credenziali di dominio dell'organizzazione. Richiede un server RADIUS che si integra con il server AD. Le organizzazioni possono anche sfruttare la distribuzione RADIUS esistente.
  
Il server RADIUS può essere distribuito in locale o nella VNet di Azure. Durante l'autenticazione, il gateway VPN di Azure funge da pass-through e inoltra i messaggi di autenticazione tra il server RADIUS e il dispositivo che si connette e viceversa. È quindi importante che il gateway possa raggiungere il server RADIUS. Se il server RADIUS si trova in locale, per la raggiungibilità è necessaria una connessione VPN da sito a sito da Azure al sito locale.  
  
È anche possibile integrare il server RADIUS con Servizi certificati AD. Ciò consente di usare il server RADIUS e la distribuzione di certificati dell'organizzazione per l'autenticazione del certificato da punto a sito in alternativa all'autenticazione del certificato di Azure. Il vantaggio è che non è necessario caricare i certificati radice e i certificati revocati in Azure.

Un server RADIUS può anche integrarsi con altri sistemi di identità esterni, offrendo così molte opzioni di autenticazione per le VPN da punto a sito, incluse le opzioni a più fattori.

![Diagramma che mostra una VPN da punto a sito con un sito locale.](./media/point-to-site-about/p2s.png)

## <a name="what-are-the-client-configuration-requirements"></a>Requisiti di configurazione per i client

>[!NOTE]
>Per i client Windows, è necessario avere i diritti di amministratore nel dispositivo client per avviare la connessione VPN dal dispositivo client ad Azure.
>

Gli utenti usano i client VPN nativi nei dispositivi Windows e Mac per la connessione da punto a sito. Azure fornisce un file ZIP per la configurazione del client VPN contenente le impostazioni necessarie per la connessione di questi client nativi ad Azure.

* Per i dispositivi Windows, la configurazione del client VPN è costituita da un pacchetto di installazione che gli utenti installano nei propri dispositivi.
* Per i dispositivi Mac è costituita dal file mobileconfig che gli utenti installano nei propri dispositivi.

Il file ZIP fornisce anche i valori di alcune impostazioni importanti sul lato Azure che è possibile usare per creare il proprio profilo per questi dispositivi. Alcuni dei valori includono l'indirizzo del gateway VPN, i tipi di tunnel configurati, le route e il certificato radice per la convalida del gateway.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Quali SKU del gateway supportano la VPN P2S?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Per consigli sugli SKU del gateway, vedere [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Lo SKU Basic non supporta l'autenticazione IKEv2 o RADIUS.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Quali criteri IKE/IPsec sono configurati nei gateway VPN per P2S?


**IKEv2**

| **Crittografia** | **Integrità** | **PRF** | **Gruppo DH** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **Crittografia** | **Integrità** | **Gruppo PFS** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Quali criteri TLS sono configurati nei gateway VPN per P2S?
**TLS**

|**Criteri** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Come si configura una connessione da punto a sito?

La configurazione di una connessione da punto a sito richiede alcuni passaggi specifici. Gli articoli seguenti illustrano le procedure di configurazione di una connessione da punto a sito e contengono collegamenti per la configurazione dei dispositivi client VPN:

* [Configurare una connessione da punto a sito usando l'autenticazione RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurare una connessione da punto a sito usando l'autenticazione del certificato nativa di Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configurare OpenVPN](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>Per rimuovere la configurazione di una connessione P2S

Per i passaggi, vedere le [domande frequenti](#removeconfig)più avanti.
 
## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Domande frequenti per l'autenticazione del certificato di Azure nativo

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Domande frequenti per l'autenticazione RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Configurare una connessione da punto a sito usando l'autenticazione RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurare una connessione da punto a sito usando l'autenticazione del certificato nativa di Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" è un marchio di OpenVPN Inc.**
