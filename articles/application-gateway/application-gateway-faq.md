---
title: Domande frequenti sul gateway applicazione Azure
description: Trovare le risposte alle domande frequenti su applicazione Azure gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/01/2020
ms.author: victorh
ms.openlocfilehash: 8acf04d26d0c69c426eb86f8a26a9f243c6f5ad2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136669"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Domande frequenti sul gateway applicazione

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Di seguito sono riportate le domande frequenti su applicazione Azure gateway.

## <a name="general"></a>Generale

### <a name="what-is-application-gateway"></a>Informazioni sul servizio Gateway applicazione

Applicazione Azure gateway fornisce un servizio di controller per la distribuzione di applicazioni (ADC) come servizio. Offre diverse funzionalità di bilanciamento del carico di livello 7 per le applicazioni. Questo servizio è altamente disponibile, scalabile e completamente gestito da Azure.

### <a name="what-features-does-application-gateway-support"></a>Quali funzionalità supporta il gateway applicazione?

Il gateway applicazione supporta la scalabilità automatica, l'offload TLS e TLS end-to-end, un web application firewall (WAF), l'affinità di sessione basata su cookie, il routing basato su percorso URL, l'hosting multisito e altre funzionalità. Per un elenco completo delle funzionalità supportate, vedere [Cos'è il gateway applicazione di Azure?](application-gateway-introduction.md)

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Quali sono le differenze tra il gateway applicazione e Azure Load Balancer?

Il gateway applicazione è un servizio di bilanciamento del carico di livello 7, che significa che funziona solo con il traffico Web (HTTP, HTTPS, WebSocket e HTTP/2). Supporta funzionalità quali la terminazione TLS, l'affinità di sessione basata su cookie e round robin per il bilanciamento del carico del traffico. Load Balancer bilancia il carico del traffico al livello 4 (TCP o UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quali protocolli supporta il gateway applicazione?

Il gateway applicazione supporta HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>In che modo il gateway applicazione supporta HTTP/2?

Vedere [supporto http/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Quali risorse sono supportate come parte di un pool back-end?

Vedere [risorse back-end supportate](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>In quali aree è disponibile il gateway applicazione?

Il gateway applicazione è disponibile in tutte le aree di Azure globale. È anche disponibile in [Azure Cina 21ViaNet](https://www.azure.cn/) e in [Azure per enti pubblici](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Questa distribuzione è dedicata alla mia sottoscrizione o è condivisa tra i clienti?

Il gateway applicazione è una distribuzione dedicata nella rete virtuale.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Il gateway applicazione supporta il Reindirizzamento da HTTP a HTTPS?

Il reindirizzamento è supportato. Vedere [Panoramica del reindirizzamento del gateway applicazione](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>In quale ordine vengono elaborati i listener?

Vedere l' [ordine di elaborazione del listener](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Dove è possibile trovare l'indirizzo IP e il DNS del gateway applicazione?

Se si usa un indirizzo IP pubblico come endpoint, le informazioni sull'indirizzo IP e sul DNS sono disponibili nella risorsa indirizzo IP pubblico. In alternativa, trovarlo nel portale, nella pagina Panoramica per il gateway applicazione. Se si usano indirizzi IP interni, trovare le informazioni nella pagina panoramica.

Per lo SKU V2 aprire la risorsa IP pubblico e selezionare **Configuration (configurazione**). Il campo **etichetta nome DNS (facoltativo)** è disponibile per configurare il nome DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quali sono le impostazioni per il timeout keep-alive e il timeout di inattività TCP?

Il *timeout keep-alive* determina per quanto tempo il gateway applicazione deve attendere che un client invii un'altra richiesta HTTP su una connessione permanente prima di riutilizzarla o chiuderla. Il *timeout di inattività TCP* determina per quanto tempo una connessione TCP viene mantenuta aperta in caso di assenza di attività. 

Il *timeout keep-alive* nello SKU del gateway applicazione v1 è 120 secondi e nello SKU v2 è 75 secondi. Il *timeout di inattività TCP* è un valore predefinito di 4 minuti nell'IP virtuale front-end (VIP) dello SKU V1 e V2 del gateway applicazione. Non è possibile modificare questi valori.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Il nome IP o DNS cambia per tutta la durata del gateway applicazione?

Nello SKU del gateway applicazione V1, l'indirizzo VIP può cambiare se si arresta e si avvia il gateway applicazione. Tuttavia, il nome DNS associato al gateway applicazione non cambia per tutta la durata del gateway. Poiché il nome DNS non cambia, è necessario usare un alias CNAME e puntare all'indirizzo DNS del gateway applicazione. Nello SKU del gateway applicazione V2, è possibile impostare l'indirizzo IP come statico, in modo che il nome IP e DNS non cambieranno nel corso della durata del gateway applicazione. 

### <a name="does-application-gateway-support-static-ip"></a>Il gateway applicazione supporta l'IP statico?

Sì, lo SKU del gateway applicazione V2 supporta indirizzi IP pubblici statici. Lo SKU versione 1 supporta indirizzi IP interni statici.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Il gateway applicazione supporta più indirizzi IP pubblici nel gateway?

Un gateway applicazione supporta solo un indirizzo IP pubblico.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quali dimensioni deve avere la subnet per il gateway applicazione?

Vedere [considerazioni sulle dimensioni della subnet del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>È possibile distribuire più di una risorsa di gateway applicazione a una singola subnet?

Sì. Oltre a più istanze di una determinata distribuzione del gateway applicazione, è possibile effettuare il provisioning di un'altra risorsa del gateway applicazione univoca a una subnet esistente che contiene una risorsa del gateway applicazione diversa.

Una singola subnet non può supportare contemporaneamente Standard_v2 e il gateway applicazione standard.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Il gateway applicazione V2 supporta le route definite dall'utente (UDR)?

Sì, ma solo scenari specifici. Per altre informazioni, vedere [Panoramica della configurazione del gateway applicazione](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Il gateway applicazione supporta le intestazioni x-forwarded-for?

Sì. Vedere le modifiche apportate [a una richiesta](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Quanto tempo è necessario per distribuire un gateway applicazione? Il gateway applicazione funzionerà durante l'aggiornamento?

Nuove distribuzioni di SKU versione 1 del gateway applicazione possono richiedere fino a 20 minuti per effettuare il provisioning. Le modifiche apportate alle dimensioni o al conteggio delle istanze non vengono interrotte e il gateway rimane attivo durante questo periodo di tempo.

Per la maggior parte delle distribuzioni che usano lo SKU V2 sono necessari circa 6 minuti per il provisioning. Tuttavia, può richiedere più tempo a seconda del tipo di distribuzione. Ad esempio, le distribuzioni in più zone di disponibilità con molte istanze possono richiedere più di 6 minuti. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>È possibile usare Exchange Server come back-end con il gateway applicazione?

No. Il gateway applicazione non supporta i protocolli di posta elettronica, ad esempio SMTP, IMAP e POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Sono disponibili linee guida per la migrazione dallo SKU V1 allo SKU V2?

Sì. Per informazioni dettagliate, vedere [eseguire la migrazione di applicazione Azure gateway e del Web Application Firewall da V1 a V2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Lo SKU V1 del gateway applicazione continuerà a essere supportato?

Sì. Lo SKU del gateway applicazione V1 continuerà a essere supportato. Tuttavia, si consiglia di passare alla versione V2 per sfruttare i vantaggi degli aggiornamenti delle funzionalità in tale SKU. Per altre informazioni, vedere [scalabilità automatica e gateway applicazione con ridondanza della zona V2](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>Il gateway applicazione V2 supporta l'inoltro delle richieste con l'autenticazione NTLM?

No. Il gateway applicazione V2 non supporta ancora le richieste proxy con l'autenticazione NTLM.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Il cookie di affinità del gateway applicazione supporta l'attributo navigava sullostesso sito?
Sì, l' [aggiornamento V80](https://chromiumdash.appspot.com/schedule) del [browser Chromium](https://www.chromium.org/Home) ha introdotto un mandato su cookie HTTP senza l'attributo navigava sullostesso sito da considerare come navigava sullostesso sito = LAX. Ciò significa che il cookie di affinità del gateway applicazione non verrà inviato dal browser in un contesto di terze parti. 

Per supportare questo scenario, il gateway applicazione inserisce un altro cookie denominato *ApplicationGatewayAffinityCORS* oltre al cookie *ApplicationGatewayAffinity* esistente.  Questi cookie sono simili, ma al cookie *ApplicationGatewayAffinityCORS* sono stati aggiunti altri due attributi: *navigava sullostesso sito = None; Sicurezza*. Questi attributi gestiscono sessioni permanenti anche per le richieste tra origini. Per ulteriori informazioni, vedere la [sezione affinità basata su cookie](configuration-overview.md#cookie-based-affinity) .

## <a name="performance"></a>Prestazioni

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>In che modo il gateway applicazione supporta la disponibilità elevata e la scalabilità?

Lo SKU V1 del gateway applicazione supporta scenari di disponibilità elevata quando sono state distribuite due o più istanze. Azure distribuisce queste istanze in domini di aggiornamento e di errore per assicurarsi che le istanze non abbiano tutti esito negativo nello stesso momento. Lo SKU versione 1 supporta la scalabilità tramite l'aggiunta di più istanze dello stesso gateway per ripartire il carico.

Lo SKU V2 garantisce automaticamente che le nuove istanze vengono distribuite tra domini di errore e domini di aggiornamento. Se si sceglie la ridondanza della zona, le istanze più recenti vengono distribuite anche tra le zone di disponibilità per offrire resilienza degli errori di zona.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Ricerca per categorie ottenere uno scenario di ripristino di emergenza nei Data Center usando il gateway applicazione?

Usare gestione traffico per distribuire il traffico tra più gateway applicazione in data center diversi.

### <a name="does-application-gateway-support-autoscaling"></a>Il gateway applicazione supporta la scalabilità automatica?

Sì, lo SKU versione 2 del gateway applicazione supporta la scalabilità automatica. Per altre informazioni, vedere [scalabilità automatica e gateway applicazione con ridondanza della zona](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>La scalabilità manuale o automatica aumenta o diminuisce i tempi di inattività?

No. Le istanze vengono distribuite tra domini di aggiornamento e domini di errore.

### <a name="does-application-gateway-support-connection-draining"></a>Il gateway applicazione supporta l'esaurimento delle connessioni?

Sì. È possibile impostare lo svuotamento della connessione per modificare i membri all'interno di un pool back-end senza alcuna distorsione. Per altre informazioni, vedere [la sezione svuotamento della connessione del gateway applicazione](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>È possibile modificare le dimensioni di un'istanza da medie a grandi senza interruzioni?

Sì.

## <a name="configuration"></a>Configurazione

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Il gateway applicazione viene sempre distribuito in una rete virtuale?

Sì. Il gateway applicazione viene sempre distribuito in una subnet di rete virtuale. Questa subnet può contenere solo gateway applicazione. Per ulteriori informazioni, vedere la pagina relativa [ai requisiti della rete virtuale e della subnet](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Il gateway applicazione può comunicare con istanze all'esterno della relativa rete virtuale o al di fuori della relativa sottoscrizione?

Fino a quando si dispone di connettività IP, il gateway applicazione può comunicare con istanze all'esterno della rete virtuale in cui si trova. Il gateway applicazione può anche comunicare con istanze all'esterno della sottoscrizione in cui si trova. Se si prevede di usare IP interni come membri del pool back-end, usare il [peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md) o il [gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>È possibile distribuire altri elementi nella subnet del gateway applicazione?

No. Tuttavia, è possibile distribuire altri gateway applicazione nella subnet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>I gruppi di sicurezza di rete sono supportati nella subnet del gateway applicazione?

Vedere [gruppi di sicurezza di rete nella subnet del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>La subnet del gateway applicazione supporta le route definite dall'utente?

Vedere [route definite dall'utente supportate nella subnet del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quali sono i limiti del gateway applicazione? È possibile aumentare questi limiti?

Vedere [limiti del gateway applicazione](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>È possibile usare contemporaneamente il gateway applicazione per il traffico esterno e interno?

Sì. Il gateway applicazione supporta un IP interno e un IP esterno per ogni gateway applicazione.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Il gateway applicazione supporta il peering di rete virtuale?

Sì. Il peering di rete virtuale consente di bilanciare il carico del traffico in altre reti virtuali.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>È possibile comunicare con I server locali quando sono connessi tramite ExpressRoute o tunnel VPN?

Sì, se il traffico è consentito.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Un pool back-end può gestire molte applicazioni su porte diverse?

L'architettura del microservizio è supportata. Per eseguire il probe su porte diverse, è necessario configurare più impostazioni HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>I probe personalizzati supportano i caratteri jolly o Regex nei dati di risposta?

No. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Come vengono elaborate le regole di routing nel gateway applicazione?

Vedere l' [ordine delle regole di elaborazione](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Per i probe personalizzati, cosa significa il campo host?

Il campo host specifica il nome a cui inviare il probe dopo aver configurato il multisito nel gateway applicazione. In caso contrario, usare ' 127.0.0.1'. Questo valore è diverso dal nome host della macchina virtuale. Il formato è \<protocollo\>://\<host\>:\<porta\>\<percorso\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>È possibile consentire l'accesso del gateway applicazione solo ad alcuni indirizzi IP di origine?

Sì. Vedere [limitare l'accesso a specifici IP di origine](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>È possibile usare la stessa porta per i listener pubblici e privati?

No.

### <a name="does-application-gateway-support-ipv6"></a>Il gateway applicazione supporta IPv6?

Il gateway applicazione V2 attualmente non supporta IPv6. Può funzionare in un VNet dual stack usando solo IPv4, ma la subnet del gateway deve essere solo IPv4. Il gateway applicazione V1 non supporta reti virtuali dual stack. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Ricerca per categorie usare il gateway applicazione V2 con solo indirizzi IP front-end privati?

Il gateway applicazione V2 attualmente non supporta solo la modalità IP privato. Supporta le seguenti combinazioni
* IP privato e indirizzo IP pubblico
* Solo IP pubblico

Tuttavia, se si vuole usare il gateway applicazione V2 con solo IP privato, è possibile seguire la procedura seguente:
1. Creare un gateway applicazione con un indirizzo IP front-end pubblico e privato
2. Non creare listener per l'indirizzo IP front-end pubblico. Il gateway applicazione non sarà in ascolto di alcun traffico sull'indirizzo IP pubblico se non viene creato alcun listener.
3. Creare e associare un [gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview) per la subnet del gateway applicazione con la seguente configurazione in ordine di priorità:
    
    a. Consentire il traffico dall'origine come tag del servizio **GatewayManager** e destinazione come **qualsiasi** porta di destinazione e come **65200-65535**. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) tramite l'autenticazione del certificato. Le entità esterne, inclusi gli amministratori utenti del gateway, non possono avviare modifiche su tali endpoint senza certificati appropriati.
    
    b. Consenti il traffico dall'origine come tag del servizio **AzureLoadBalancer** e destinazione e porta di destinazione come **qualsiasi**
    
    c. Negare tutto il traffico in ingresso dall'origine come tag del servizio **Internet** e la porta di destinazione e destinazione come **qualsiasi**. Assegnare a questa regola la *priorità minima* nelle regole in ingresso
    
    d. Mantieni le regole predefinite come consentire VirtualNetwork in ingresso in modo che l'accesso all'indirizzo IP privato non venga bloccato
    
    e. La connettività Internet in uscita non può essere bloccata. In caso contrario, si affronteranno problemi con la registrazione, le metriche e così via.

Esempio di configurazione di NSG per l'accesso solo ![IP privato: configurazione del gateway applicazione V2 NSG solo per l'accesso IP privato](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Configurazione-TLS

### <a name="what-certificates-does-application-gateway-support"></a>Quali certificati supporta il gateway applicazione?

Il gateway applicazione supporta i certificati autofirmati, i certificati dell'autorità di certificazione (CA), i certificati di convalida estesa (EV) e i certificati con caratteri jolly.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Quali suite di crittografia supporta il gateway applicazione?

Il gateway applicazione supporta i pacchetti di crittografia seguenti. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Per informazioni su come personalizzare le opzioni TLS, vedere [configurare le versioni dei criteri TLS e i pacchetti di crittografia nel gateway applicazione](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Il gateway applicazione supporta la ricrittografia del traffico nel back-end?

Sì. Il gateway applicazione supporta l'offload TLS e il protocollo TLS end-to-end, che consente di crittografare nuovamente il traffico nel back-end.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>È possibile configurare I criteri TLS per controllare le versioni del protocollo TLS?

Sì. È possibile configurare il gateway applicazione per negare TLS 1.0, TLS 1.1 e TLS 1.2. Per impostazione predefinita, SSL 2,0 e 3,0 sono già disabilitati e non sono configurabili.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>È possibile configurare l'ordine dei pacchetti di crittografia e dei criteri?

Sì. Nel gateway applicazione è possibile configurare i pacchetti di [crittografia](application-gateway-ssl-policy-overview.md). Per definire un criterio personalizzato, abilitare almeno uno dei seguenti pacchetti di crittografia. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Il gateway applicazione usa SHA256 per la gestione back-end.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Quanti certificati TLS/SSL supportano il gateway applicazione?

Il gateway applicazione supporta fino a 100 certificati TLS/SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Quanti certificati di autenticazione per la ricrittografia back-end supporta il gateway applicazione?

Il gateway applicazione supporta fino a 100 certificati di autenticazione.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Il gateway applicazione si integra in modo nativo con Azure Key Vault?

Sì, lo SKU del gateway applicazione V2 supporta Key Vault. Per altre informazioni, vedere [terminazione TLS con Key Vault Certificates](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Ricerca per categorie configurare i listener HTTPS per i siti com e .NET? 

Per più routing basato su dominio (basato su host), è possibile creare listener multisito, configurare listener che usano HTTPS come protocollo e associare i listener alle regole di routing. Per altre informazioni, vedere [hosting di più siti tramite il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>È possibile usare caratteri speciali nella password del file con estensione pfx?

No, usare solo caratteri alfanumerici nella password del file con estensione pfx.

## <a name="configuration---web-application-firewall-waf"></a>Configuration-web application firewall (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Lo SKU WAF offre tutte le funzionalità disponibili nello SKU standard?

Sì. WAF supporta tutte le funzionalità dello SKU standard.

### <a name="how-do-i-monitor-waf"></a>Come si monitora il Web application firewall?

Monitorare WAF tramite registrazione diagnostica. Per altre informazioni, vedere [registrazione diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>La modalità di rilevamento blocca il traffico?

No. La modalità di rilevamento registra solo il traffico che attiva una regola WAF.

### <a name="can-i-customize-waf-rules"></a>È possibile personalizzare le regole del Web application firewall (WAF)?

Sì. Per altre informazioni, vedere [Customize WAF Rule Groups and Rules](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Quali regole sono attualmente disponibili per WAF?

WAF supporta attualmente CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)e [3,1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Queste regole forniscono una sicurezza di base rispetto alla maggior parte delle 10 vulnerabilità che aprono il progetto di sicurezza dell'applicazione Web (OWASP): 

* Protezione dagli attacchi SQL injection
* Protezione tramite script tra siti
* Protezione da attacchi Web comuni, ad esempio l'inserimento di comandi, il contrabbando di richieste HTTP, la suddivisione della risposta HTTP e l'attacco di inclusione di file remoti
* Protezione dalle violazioni del protocollo HTTP
* Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header
* Prevenzione contro robot, crawler e scanner
* Rilevamento di errate configurazioni dell'applicazione comuni (ovvero Apache, IIS e così via)

Per altre informazioni, vedere [OWASP Top-10 vulnerabilità](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>WAF supporta la protezione DDoS?

Sì. È possibile abilitare la protezione DDoS nella rete virtuale in cui è distribuito il gateway applicazione. Questa impostazione garantisce che il servizio protezione DDoS di Azure protegga anche l'indirizzo IP virtuale (VIP) del gateway applicazione.

## <a name="configuration---ingress-controller-for-aks"></a>Configurazione-controller in ingresso per AKS

### <a name="what-is-an-ingress-controller"></a>Che cos'è un controller di ingresso?

Kubernetes consente la creazione `deployment` di `service` una risorsa e per esporre un gruppo di Pod internamente nel cluster. Per esporre lo stesso servizio esternamente, viene [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) definita una risorsa che fornisce bilanciamento del carico, terminazione TLS e hosting virtuale basato su nomi.
Per soddisfare questa `Ingress` risorsa, è necessario un controller di ingresso che rimane in ascolto delle modifiche alle `Ingress` risorse e configura i criteri del servizio di bilanciamento del carico.

Il controller di ingresso del gateway applicazione consente di usare [applicazione Azure gateway](https://azure.microsoft.com/services/application-gateway/) come ingresso per un [servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) , noto anche come cluster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Una singola istanza del controller di ingresso può gestire più gateway applicazione?

Attualmente, un'istanza del controller di ingresso può essere associata a un solo gateway applicazione.

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Quali tipi di log fornisce il gateway applicazione?

Il gateway applicazione fornisce tre log: 

* **ApplicationGatewayAccessLog**: il log di accesso contiene ogni richiesta inviata al front-end del gateway applicazione. I dati includono IP del chiamante, URL richiesto, latenza della risposta, codice restituito e byte in uscita e in uscita. Contiene un record per ogni gateway applicazione.
* **ApplicationGatewayPerformanceLog**: il log delle prestazioni acquisisce le informazioni sulle prestazioni per ogni gateway applicazione. Le informazioni includono la velocità effettiva in byte, le richieste totali gestite, il numero di richieste non riuscite e il numero di istanze back-end integre e non integre.
* **ApplicationGatewayFirewallLog**: per i gateway applicazione configurati con WAF, il log del firewall contiene le richieste registrate tramite la modalità di rilevamento o la modalità di prevenzione.

Tutti i log vengono raccolti ogni 60 secondi. Per altre informazioni, vedere [integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Come è possibile sapere se i membri del pool back-end sono integri?

Verificare lo stato di integrità usando il `Get-AzApplicationGatewayBackendHealth` cmdlet di PowerShell o il portale. Per altre informazioni, vedere [diagnostica del gateway applicazione](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Quali sono i criteri di conservazione per i log di diagnostica?

I log di diagnostica vengono propagati all'account di archiviazione del cliente. I clienti possono impostare i criteri di conservazione in base alle proprie preferenze. I log di diagnostica possono anche essere inviati a un hub eventi o ai log di monitoraggio di Azure. Per altre informazioni, vedere [diagnostica del gateway applicazione](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Come si ottengono i log di controllo per il gateway applicazione?

Nel pannello del menu di un gateway applicazione del portale selezionare **log attività** per accedere al log di controllo. 

### <a name="can-i-set-alerts-with-application-gateway"></a>È possibile impostare avvisi con il gateway applicazione?

Sì. Nel gateway applicazione gli avvisi vengono configurati sulle metriche. Per altre informazioni, vedere [metriche del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) e [ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Come si analizzano le statistiche sul traffico per il gateway applicazione?

È possibile visualizzare e analizzare i log di accesso in diversi modi. Usare i log di monitoraggio di Azure, Excel, Power BI e così via.

È anche possibile usare un modello di Gestione risorse che installa ed esegue il noto [GoAccess](https://goaccess.io/) Log Analyzer per i log di accesso del gateway applicazione. GoAccess fornisce importanti statistiche sul traffico HTTP, ad esempio i visitatori univoci, i file richiesti, gli host, i sistemi operativi, i browser e i codici di stato HTTP. Per ulteriori informazioni, vedere il [file Leggimi nella cartella del modello di gestione risorse](https://aka.ms/appgwgoaccessreadme)in GitHub.

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Cosa può fare in modo che l'integrità back-end restituisca uno stato sconosciuto?

Viene in genere visualizzato uno stato sconosciuto quando l'accesso al back-end è bloccato da un gruppo di sicurezza di rete (NSG), un DNS personalizzato o un routing definito dall'utente (UDR) nella subnet del gateway applicazione. Per altre informazioni, vedere [integrità back-end, registrazione diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Esistono casi in cui i log di flusso NSG non visualizzeranno il traffico consentito?

Sì. Se la configurazione corrisponde al seguente scenario, non verrà visualizzato il traffico consentito nei log del flusso di NSG:
- Il gateway applicazione V2 è stato distribuito
- Si dispone di un NSG nella subnet del gateway applicazione
- Sono stati abilitati i log di flusso NSG su tale NSG

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Ricerca per categorie usare il gateway applicazione V2 con solo indirizzi IP front-end privati?

Il gateway applicazione V2 attualmente non supporta solo la modalità IP privato. Supporta le seguenti combinazioni
* IP privato e indirizzo IP pubblico
* Solo IP pubblico

Tuttavia, se si vuole usare il gateway applicazione V2 con solo IP privato, è possibile seguire la procedura seguente:
1. Creare un gateway applicazione con un indirizzo IP front-end pubblico e privato
2. Non creare listener per l'indirizzo IP front-end pubblico. Il gateway applicazione non sarà in ascolto di alcun traffico sull'indirizzo IP pubblico se non viene creato alcun listener.
3. Creare e associare un [gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview) per la subnet del gateway applicazione con la seguente configurazione in ordine di priorità:
    
    a. Consentire il traffico dall'origine come tag del servizio **GatewayManager** e destinazione come **qualsiasi** porta di destinazione e come **65200-65535**. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) tramite l'autenticazione del certificato. Le entità esterne, inclusi gli amministratori utenti del gateway, non possono avviare modifiche su tali endpoint senza certificati appropriati.
    
    b. Consenti il traffico dall'origine come tag del servizio **AzureLoadBalancer** e porta di destinazione come **qualsiasi**
    
    c. Negare tutto il traffico in ingresso dall'origine come tag di servizio **Internet** e porta di destinazione come **qualsiasi**. Assegnare a questa regola la *priorità minima* nelle regole in ingresso
    
    d. Mantieni le regole predefinite come consentire VirtualNetwork in ingresso in modo che l'accesso all'indirizzo IP privato non sia bloccato
    
    e. La connettività Internet in uscita non può essere bloccata. In caso contrario, si affronteranno problemi con la registrazione, le metriche e così via.

Esempio di configurazione di NSG per l'accesso solo ![IP privato: configurazione del gateway applicazione V2 NSG solo per l'accesso IP privato](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Il cookie di affinità del gateway applicazione supporta l'attributo navigava sullostesso sito?
Sì, l' [aggiornamento V80](https://chromiumdash.appspot.com/schedule) del [browser Chromium](https://www.chromium.org/Home) ha introdotto un mandato su cookie HTTP senza l'attributo navigava sullostesso sito da considerare come navigava sullostesso sito = LAX. Ciò significa che il cookie di affinità del gateway applicazione non verrà inviato dal browser in un contesto di terze parti. Per supportare questo scenario, il gateway applicazione inserisce un altro cookie denominato *ApplicationGatewayAffinityCORS* oltre al cookie *ApplicationGatewayAffinity* esistente.  Questi cookie sono simili, ma al cookie *ApplicationGatewayAffinityCORS* sono stati aggiunti altri due attributi: *navigava sullostesso sito = None; Sicurezza*. Questi attributi gestiscono sessioni permanenti anche per le richieste tra origini. Per ulteriori informazioni, vedere la [sezione affinità basata su cookie](configuration-overview.md#cookie-based-affinity) .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul gateway applicazione, vedere [che cos'è applicazione Azure gateway?](overview.md).
