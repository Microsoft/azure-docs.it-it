---
title: Panoramica di protezione DDoS di Azure standard
description: Informazioni sul servizio Protezione DDoS di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: fc47e1f4fbdb48e6e0abc1f2a7e32127b0325f47
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82130965"
---
# <a name="azure-ddos-protection-standard-overview"></a>Panoramica di Protezione DDoS di Azure Standard

Gli attacchi Distributed Denial of Service (DDoS) sono tra le principali preoccupazioni che riguardano la disponibilità e la sicurezza per quei clienti che spostano le loro applicazioni nel cloud. Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet.

Protezione DDoS di Azure, insieme alle procedure consigliate di progettazione delle applicazioni, offre un meccanismo di difesa dagli attacchi DDoS. Protezione DDos di Azure offre i livelli di servizio seguenti:

- **Basic**: attivata automaticamente come parte della piattaforma di Azure. Il monitoraggio del traffico always on e la mitigazione in tempo reale di attacchi comuni a livello di rete forniscono le stesse difese usate dai Servizi online di Microsoft.L'intera scala della rete globale di Azure può essere usata per distribuire e mitigare il traffico degli attacchi tra le aree.La protezione viene fornita per gli [indirizzi IP pubblici](virtual-network-public-ip-address.md) di Azure IPv4 e IPv6.
- **Standard**: offre funzionalità aggiuntive di mitigazione tramite il livello di servizio Basic ottimizzate in modo specifico per le risorse di Rete virtuale di Microsoft Azure. Protezione DDoS Standard è semplice da abilitare e non richiede alcuna modifica alle applicazioni. I criteri di protezione sono ottimizzati tramite il monitoraggio del traffico dedicato e algoritmi di apprendimento automatico. I criteri vengono applicati agli indirizzi IP pubblici associati alle risorse distribuite nelle reti virtuali, ad esempio le istanze di Azure Load Balancer, del gateway applicazione di Azure e di Azure Service Fabric, ma questa protezione non si applica agli ambienti del servizio app.La telemetria in tempo reale è disponibile tramite le viste di Monitoraggio di Azure durante un attacco e come informazione cronologica. Sono disponibili funzionalità complete di analisi della mitigazione degli attacchi tramite le impostazioni di diagnostica. È possibile aggiungere protezioni a livello dell'applicazione tramite il [web application firewall del gateway applicazione di Azure](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o installando un firewall di terze parti da Microsoft Azure Marketplace. La protezione viene fornita per gli [indirizzi IP pubblici](virtual-network-public-ip-address.md) di Azure IPv4 e IPv6.

|Funzionalità                                         |Protezione DDoS Basic                 |Protezione DDoS Standard                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Monitoraggio del traffico attivo & rilevamento always on |Sì                                   |Sì                                           |
|Mitigazioni di attacchi automatici                    |Sì                                   |Sì                                           |
|Garanzia di disponibilità                          |Area di Azure                          |Applicazione                                   |
|Criteri di mitigazione                             |Ottimizzato per il volume dell'area di traffico di Azure |Ottimizzato per il volume di traffico dell'applicazione          |
|Metriche & avvisi                                |No                                    |Metriche di attacco in tempo reale & log delle risorse tramite monitoraggio di Azure                                 |
|Report di mitigazione                              |No                                    |Report sulla mitigazione degli attacchi post                |
|Log del flusso di mitigazione                            |No                                    |Flusso di log NRT per l'integrazione SIEM           |
|Personalizzazione dei criteri di mitigazione                 |No                                    |Coinvolgere gli esperti DDoS                           |
|Supporto tecnico                                         |Massimo sforzo                           |Accesso agli esperti DDoS durante un attacco attivo|
|Contratto di servizio                                             |Area di Azure                          |Protezione dei costi & delle applicazioni       |
|Prezzi                                         |Gratuito                                  |Utilizzo mensile & basato su                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipi di attacchi DDoS mitigati da Protezione DDoS Standard

Protezione DDoS Standard consente di mitigare i seguenti tipi di attacchi:

- **Attacchi volumetrici**: l'obiettivo dell'attacco è quello di intasare il livello di rete con una notevole quantità di traffico in apparenza legittimo. Sono inclusi attacchi flood UDP, attacchi flood con amplificazione e attacchi flood con pacchetti falsificati. Protezione DDoS standard attenua questi potenziali attacchi a più gigabyte grazie all'assorbimento e alla pulitura, con la scalabilità di rete globale di Azure, in modo automatico.
- **Attacchi ai protocolli**: questi attacchi rendono inaccessibile una destinazione sfruttando una vulnerabilità nello stack di protocolli di livello 3 e di livello 4. Sono inclusi attacchi di tipo SYN flood, attacchi di tipo reflection e altri attacchi contro i protocolli. Protezione DDoS Standard mitiga questi attacchi distinguendo tra traffico dannoso e legittimo tramite l'interazione con il client e bloccando il traffico dannoso. 
- **Attacchi a livello di risorsa (applicazione)**: questi attacchi colpiscono i pacchetti di applicazioni Web per interrompere la trasmissione dei dati tra host. Gli attacchi includono violazioni del protocollo HTTP, attacchi SQL injection, script intersito e altri attacchi di livello 7. Usare un Web Application Firewall, ad esempio il [gateway applicazione](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)di Azure Web Application Firewall, oltre a protezione DDoS standard per offrire una difesa contro tali attacchi. Sono disponibili anche offerte di terze parti di web application firewall nel [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

Protezione DDoS Standard protegge le risorse in una rete virtuale, inclusi gli indirizzi IP pubblici associati alle macchine virtuali, i bilanciamenti del carico e i gateway applicazione. Una volta abbinato il gateway applicazione web application firewall o una web application firewall di terze parti distribuita in una rete virtuale con un indirizzo IP pubblico, protezione DDoS standard può fornire funzionalità di mitigazione complete di livello 3 a livello 7.

## <a name="ddos-protection-standard-features"></a>Funzionalità di Protezione DDoS Standard

![Funzionalità DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Le funzionalità di Protezione DDoS Standard includono:

- **Integrazione di piattaforma nativa:** integrato in modo nativo in Azure. Include la configurazione tramite il portale di Azure. Protezione DDoS Standard comprende le risorse e la loro configurazione.
- **Protezione pronta all'uso:** la configurazione semplificata consente di proteggere immediatamente tutte le risorse in una rete virtuale non appena il servizio Protezione DDoS Standard viene abilitato. Non è necessaria alcuna definizione dell'utente o intervento. Il servizio Protezione DDoS Standard mitiga istantaneamente e automaticamente gli attacchi non appena vengono rilevati.
- **Monitoraggio del traffico sempre attivo:** i modelli di traffico delle applicazioni vengono monitorati 24 ore su 24, 7 giorni su 7, cercando gli indicatori di attacchi DDoS. La mitigazione viene applicata quando vengono superati i criteri di protezione.
- **Ottimizzazione adattiva:** La profilatura del traffico intelligente apprende il traffico dell'applicazione nel tempo e seleziona e aggiorna il profilo più adatto per il servizio. Il profilo viene modificato in base ai cambiamenti del traffico nel tempo.
- **Protezione su più livelli:** offre protezione DDoS dello stack completo se usata con un web application firewall.
- **Scala di mitigazione completa:** è possibile mitigare più di 60 tipi diversi di attacchi con capacità globale, per una protezione dai più noti attacchi DDoS.
- **Analisi degli attacchi:** ottieni report dettagliati con incrementi di cinque minuti durante un attacco e un riepilogo completo al termine dell'attacco. Esegui lo streaming dei log del flusso di mitigazione in un sistema di informazioni di sicurezza e gestione degli eventi offline per il monitoraggio quasi in tempo reale durante un attacco.
- **Metriche degli attacchi:** tramite Monitoraggio di Azure è possibile accedere al riepilogo delle metriche per ogni attacco.
- **Avviso di attacco:** Gli avvisi possono essere configurati in fase di avvio e arresto di un attacco e sulla durata dell'attacco, usando le metriche di attacco predefinite. Gli avvisi si integrano nel software operativo, come Microsoft Azure log di monitoraggio, Splunk, archiviazione di Azure, posta elettronica e il portale di Azure.
- **Garanzia di costo:** Crediti di servizio di trasferimento dati e scalabilità orizzontale dell'applicazione per gli attacchi DDoS documentati.

## <a name="ddos-protection-standard-mitigation"></a>Mitigazione di Protezione DDoS Standard

Protezione DDoS Standard monitora l'utilizzo effettivo del traffico e lo confronta continuamente con le soglie definite nei criteri DDoS. Quando la soglia di traffico viene superata, viene avviata automaticamente la mitigazione DDoS. Quando il traffico torna sotto la soglia, la mitigazione viene rimossa.

![Strategia di riduzione del rischio](./media/ddos-protection-overview/mitigation.png)

Durante la mitigazione il traffico inviato alla risorsa protetta viene reindirizzato dal servizio Protezione DDoS e vengono eseguiti diversi controlli, ad esempio i controlli seguenti:

- Verificare che i pacchetti siano conformi alle specifiche Internet e abbiano un formato valido.
- Interagire con il client per determinare se il traffico è stato potenzialmente falsificato, ad esempio usando una tecnica SYN Auth o SYN Cookie oppure eliminando un pacchetto in modo che l'origine lo trasmetta di nuovo.
- Limitare la frequenza dei pacchetti se non è possibile usare altri metodi di imposizione.

Protezione DDoS blocca il traffico degli attacchi e inoltra il traffico rimanente verso la destinazione prevista. Entro pochi minuti dal rilevamento degli attacchi, si riceve una notifica in base alle metriche di Monitoraggio di Azure. Configurando la registrazione per la telemetria di Protezione DDoS Standard, è possibile scrivere log disponibili per analisi future. I dati delle metriche di Monitoraggio di Azure per Protezione DDoS Standard vengono mantenuti per 30 giorni.

Microsoft ha collaborato con [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) per compilare un'interfaccia in cui è possibile generare il traffico per abilitare la protezione DDoS agli indirizzi IP pubblici per simulazioni. La simulazione BreakPoint Cloud consente di:

- Convalidare come la Protezione DDoS Standard di Microsoft Azure consente di proteggere le risorse di Azure da attacchi DDoS
- Ottimizzare il processo di risposta agli eventi imprevisti durante un attacco DDoS
- Documentare la conformità DDoS
- Eseguire il training dei team di sicurezza di rete

## <a name="next-steps"></a>Passaggi successivi

- [Configurare Protezione DDoS Standard](manage-ddos-protection.md)
