---
title: Come funziona Gestione traffico di Azure | Microsoft Docs
description: Questo articolo aiuterà a comprendere come Gestione traffico instrada il traffico per garantire prestazioni e disponibilità elevate delle applicazioni Web
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: duau
ms.openlocfilehash: a1e1bd107e8b3b9209f99d1abfc4d7e391c3c4a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184340"
---
# <a name="how-traffic-manager-works"></a>Funzionamento di Gestione traffico

Gestione traffico di Azure consente di controllare in che modo il traffico viene distribuito tra gli endpoint dell'applicazione. Un endpoint è un servizio con connessione Internet ospitato all'interno o all'esterno di Azure.

Gestione traffico offre due vantaggi principali:

- Distribuzione del traffico in base a uno dei diversi [metodi di routing del traffico](traffic-manager-routing-methods.md)
- [Monitoraggio continuo dell'integrità degli endpoint](traffic-manager-monitoring.md) e failover automatico quando si verificano errori sugli endpoint.

Quando un client tenta di connettersi a un servizio, è necessario prima risolvere il nome DNS del servizio a un indirizzo IP. Il client si connette quindi a questo indirizzo IP per accedere al servizio.

**Il punto più importante da comprendere è che Gestione traffico lavora a livello di DNS.**  Gestione traffico usa DNS per indirizzare i client a specifici endpoint di servizio in base alle regole del metodo di routing del traffico. I client si connettono quindi **direttamente** all'endpoint selezionato. Gestione traffico non è un proxy o un gateway. Gestione traffico non visualizza il traffico tra il client e il servizio.

## <a name="traffic-manager-example"></a>Esempio di Gestione traffico

Contoso Corp ha sviluppato un nuovo portale per i partner. L'URL per questo portale è `https://partners.contoso.com/login.aspx`. L'applicazione è ospitata in tre aree di Azure. Per migliorare la disponibilità e ottimizzare le prestazioni globali, viene usato Gestione traffico per distribuire il traffico client all'endpoint disponibile più vicino.

Per ottenere questa configurazione, si completa la seguente procedura:

1. Distribuire tre istanze del servizio. I nomi DNS di queste distribuzioni sono 'contoso-us.cloudapp.net', 'contoso-eu.cloudapp.net' e 'contoso-asia.cloudapp.net'.
1. Creare un profilo di Gestione traffico con il nome "contoso.trafficmanager.net", e configurarlo per usare il metodo di routing del traffico "Prestazioni" tra i 3 endpoint.
1. Configurare il nome di dominio personale "partners.contoso.com" in modo che punti a "contoso.trafficmanager.net" tramite il record DNS CNAME.

![Configurazione DNS di Gestione traffico][1]

> [!NOTE]
> Quando si usa un dominio personale con Gestione traffico di Azure, è necessario usare un record CNAME per scegliere il nome del dominio personale sul nome di dominio di Gestione traffico. Gli standard DNS non consentono di creare un record CNAME al "vertice" (o alla radice) di un dominio. Non è quindi possibile creare un record CNAME per "contoso.com", detto anche dominio "naked". È possibile creare solo un record CNAME per un dominio in "contoso.com", ad esempio "www.contoso.com". Per aggirare questa limitazione, è consigliabile ospitare il dominio DNS nel [DNS di Azure](../dns/dns-overview.md) e usare i [record Alias](../dns/tutorial-alias-tm.md) in modo da puntare al profilo di Gestione traffico. In alternativa usare un semplice reindirizzamento HTTP per indirizzare le richieste di "contoso.com" su un nome alternativo, ad esempio "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Come si connettono i client tramite Gestione traffico

Per continuare l'esempio precedente, quando un client richiede la pagina `https://partners.contoso.com/login.aspx`, il client esegue i passaggi seguenti per risolvere il nome DNS e stabilire una connessione:

![Stabilire una connessione tramite Gestione traffico][2]

1. Il client invia una query DNS al relativo servizio DNS ricorsivo configurato per risolvere il nome "partners.contoso.com". Un servizio DNS ricorsivo, denominato anche servizio "DNS locale", non ospita direttamente i domini DNS, ma delega il lavoro di contattare i vari servizi DNS autorevoli su Internet necessari per risolvere un nome DNS.
2. Per risolvere il nome DNS, il servizio DNS ricorsivo cerca i server dei nomi per il dominio "contoso.com". A questo punto, il servizio contatta i server dei nomi per richiedere il record DNS di "partners.contoso.com". I server DNS contoso.com restituiscono il record CNAME che punta a contoso.trafficmanager.net.
3. Il servizio DNS ricorsivo quindi trova i server dei nomi per il dominio "trafficmanager, net", che vengono forniti dal servizio Gestione traffico di Azure. Quindi invia una richiesta per il record DNS "contoso.trafficmanager.net" a questi server DNS.
4. I server dei nomi di Gestione traffico ricevono la richiesta. Scelgono un endpoint in base a:

    - Lo stato configurato di ogni endpoint (gli endpoint disabilitati non vengono restituiti)
    - L'integrità corrente di ogni endpoint, determinata dai controlli di integrità di Gestione traffico. Per altre informazioni, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md).
    - Il metodo di routing del traffico scelto. Per altre informazioni, vedere [metodi di routing di gestione traffico](traffic-manager-routing-methods.md).

5. L'endpoint scelto viene restituito come un altro record DNS CNAME. In questo caso, supponiamo che venga restituito contoso-eu.cloudapp.net.
6. Il servizio DNS ricorsivo cerca quindi i server dei nomi per il dominio "cloudapp.net". Contatta questi server dei nomi per richiedere il record DNS "contoso-eu.cloudapp.net". Viene restituito un record DNS "A" contenente l'indirizzo IP dell'endpoint di servizio basato su UE.
7. Il servizio DNS ricorsivo consente di consolidare i risultati e restituisce una singola risposta DNS al client.
8. Il client riceve i risultati DNS e si connette all'indirizzo IP specificato. Il client si connette all'endpoint di servizio dell'applicazione in modo diretto, senza passare per Gestione traffico. Poiché si tratta di un endpoint HTTPS, il client esegue l'handshake SSL/TLS necessario e quindi esegue una richiesta HTTP GET per la pagina "/login.aspx".

Il servizio DNS ricorsivo memorizza nella cache le risposte DNS ricevute. Il resolver DNS nel dispositivo client memorizza nella cache anche il risultato. Il caching consente alle query DNS successive di ricevere risposte più rapide usando i dati dalla cache al posto di query ad altri server dei nomi. La durata della memorizzazione nella cache è determinata dalla proprietà "Durata" (TTL) di ogni record DNS. Valori più bassi implicano una scadenza della cache più breve e pertanto più round trip ai server dei nomi di Gestione traffico. Valori più alti indicano che potrebbe essere necessario più tempo per indirizzare il traffico da un endpoint non riuscito. Gestione traffico consente di configurare la durata (TTL) delle risposte DNS di Gestione traffico affinché siano di un minimo di 0 secondi e di un massimo di 2.147.483.647 secondi (con l'intervallo massimo conforme [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)), per permettere all'utente di scegliere il valore ideale per bilanciare meglio le esigenze dell'applicazione.

## <a name="faqs"></a>Domande frequenti

* [Quale indirizzo IP viene usato da Gestione traffico?](./traffic-manager-faqs.md#what-ip-address-does-traffic-manager-use)

* [Quali tipi di traffico è possibile indirizzare tramite Gestione traffico?](./traffic-manager-faqs.md#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Gestione traffico supporta le sessioni "permanenti"?](./traffic-manager-faqs.md#does-traffic-manager-support-sticky-sessions)

* [Quando si usa Gestione traffico, viene visualizzato un errore HTTP. Perché?](./traffic-manager-faqs.md#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Qual è l'impatto sulle prestazioni dell'uso di Gestione traffico?](./traffic-manager-faqs.md#what-is-the-performance-impact-of-using-traffic-manager)

* [Quali protocolli di applicazione possono essere usati con Gestione traffico?](./traffic-manager-faqs.md#what-application-protocols-can-i-use-with-traffic-manager)

* [È possibile usare gestione traffico con un nome di dominio "Naked"?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-a-naked-domain-name)

* [Gestione traffico tiene conto dell'indirizzo della subnet client quando si gestiscono query DNS?](./traffic-manager-faqs.md#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Cos'è la durata TTL del DNS e che impatto ha sugli utenti?](./traffic-manager-faqs.md#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Come impostare una durata TTL maggiore o minore per le risposte di Gestione traffico?](./traffic-manager-faqs.md#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Come si può verificare il volume delle query destinate al profilo personale?](./traffic-manager-faqs.md#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [monitoraggio degli endpoint e sul failover automatico](traffic-manager-monitoring.md)di Gestione traffico.

Altre informazioni sui [metodi di routing](traffic-manager-routing-methods.md)di Gestione traffico.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png