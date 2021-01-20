---
title: Domande frequenti sugli standard di protezione DDoS di Azure
description: Domande frequenti sullo standard di protezione DDoS di Azure, che consente di fornire una difesa contro gli attacchi DDoS.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: b53501bf5aa4bafb0229158edfe0cb244116b909
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600856"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Domande frequenti sugli standard di protezione DDoS di Azure

Questo articolo risponde a domande comuni su protezione DDoS di Azure standard. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>Che cos'è un attacco distribuito di tipo Denial of Service (DDoS)?
Un attacco di tipo Denial of Service, o DDoS, è un tipo di attacco in cui un utente malintenzionato invia più richieste a un'applicazione che l'applicazione è in grado di gestire. L'effetto risultante è l'esaurimento delle risorse, che influiscono sulla disponibilità e sulla capacità dell'applicazione di servire i clienti. Negli ultimi anni, il settore ha riscontrato un forte aumento degli attacchi, con attacchi che diventano più sofisticati e di dimensioni maggiori. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint raggiungibile pubblicamente tramite Internet.

## <a name="what-is-azure-ddos-protection-standard-service"></a>Che cos'è il servizio protezione DDoS standard di Azure?
Protezione DDoS di Azure standard, in combinazione con le procedure consigliate per la progettazione di applicazioni, fornisce funzionalità avanzate di mitigazione DDoS per la difesa da attacchi DDoS. Viene ottimizzata automaticamente per aiutare a proteggere le risorse di Azure specifiche in una rete virtuale. La protezione è semplice da abilitare in qualsiasi rete virtuale nuova o esistente e non richiede alcuna modifica di applicazioni o risorse. Presenta diversi vantaggi rispetto al servizio Basic, compresa la registrazione, gli avvisi e i dati di telemetria. Per altri dettagli, vedere [Panoramica di protezione DDoS di Azure standard](ddos-protection-overview.md) . 

## <a name="how-does-pricing-work"></a>Quali sono i prezzi?
I piani di protezione DDoS hanno un costo mensile fisso di $2.944 al mese che copre fino a 100 indirizzi IP pubblici. La protezione per le risorse aggiuntive costerà altri $30 per ogni risorsa al mese. 

In un tenant, un singolo piano di protezione DDoS può essere usato in più sottoscrizioni, quindi non è necessario creare più di un piano di protezione DDoS.

Per altri dettagli, vedere la pagina relativa ai [prezzi standard di protezione DDoS di Azure](https://azure.microsoft.com/pricing/details/ddos-protection/) .

## <a name="is-the-service-zone-resilient"></a>La resilienza della zona di servizio?
Sì. La protezione DDoS di Azure è con resilienza della zona per impostazione predefinita.

## <a name="how-do-i-configure-the-service-to-be-zone-resilient"></a>Ricerca per categorie configurare il servizio in modo che sia resiliente alla zona?
Non è necessaria alcuna configurazione del cliente per abilitare la resilienza della zona. La resilienza della zona per le risorse di protezione DDoS di Azure è disponibile per impostazione predefinita e gestita dal servizio stesso.

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>E per quanto riguarda la protezione a livello di servizio (livello 7)?
I clienti possono usare il servizio protezione DDoS di Azure in combinazione con un Web Application Firewall (WAF) a per la protezione a livello di rete (livello 3 e 4, offerto da Azure DDoS Protection standard) e a livello di applicazione (livello 7, offerto da WAF). Le offerte WAF includono lo [SKU WAF del gateway applicazione](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure e le offerte di Web Application Firewall di terze parti disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

## <a name="are-services-unsafe-in-azure-without-the-service"></a>I servizi non sono sicuri in Azure senza il servizio?
I servizi in esecuzione in Azure sono intrinsecamente protetti da protezione DDoS di Azure Basic, per proteggere l'infrastruttura di Azure. Tuttavia, la protezione che protegge l'infrastruttura ha una soglia molto più elevata rispetto alla capacità di gestione della maggior parte delle applicazioni e non fornisce dati di telemetria o avvisi, quindi mentre un volume di traffico può essere percepito come innocuo dalla piattaforma, può risultare devastante per l'applicazione che lo riceve. 

Con l'onboarding nel servizio protezione standard di Azure DDoS, l'applicazione ottiene il monitoraggio dedicato per rilevare gli attacchi e le soglie specifiche dell'applicazione. Un servizio verrà protetto con un profilo ottimizzato per il volume di traffico previsto, garantendo una difesa molto più restrittiva contro gli attacchi DDoS.

## <a name="what-are-the-supported-protected-resource-types"></a>Quali sono i tipi di risorse protette supportati?
Gli indirizzi IP pubblici nei reti virtuali basati su ARM sono attualmente l'unico tipo di risorsa protetta. I servizi PaaS (multi-tenant) non sono supportati nella presentazione. Per altri dettagli, vedere le [architetture di riferimento standard di protezione DDoS di Azure](ddos-protection-reference-architectures.md) .

## <a name="are-classicrdfe-protected-resources-supported"></a>Sono supportate le risorse protette classiche/RDFE?
In anteprima sono supportate solo le risorse protette basate su ARM. Le macchine virtuali nelle distribuzioni classiche/RDFE non sono supportate. Il supporto non è attualmente pianificato per le risorse classiche/RDFE. Per altri dettagli, vedere le [architetture di riferimento standard di protezione DDoS di Azure](ddos-protection-reference-architectures.md) .

## <a name="can-i-protect-my-paas-resources-using-ddos-protection"></a>È possibile proteggere le risorse PaaS usando la protezione DDoS?
Gli indirizzi IP pubblici collegati ai servizi multi-tenant Single VIP PaaS non sono attualmente supportati. Esempi di risorse non supportate includono i VIP di archiviazione, i VIP dell'hub eventi e le applicazioni di servizi cloud e app. Per altri dettagli, vedere le [architetture di riferimento standard di protezione DDoS di Azure](ddos-protection-reference-architectures.md) .

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>È possibile proteggere le risorse locali usando la protezione DDoS?
Per abilitare la protezione DDoS, è necessario che gli endpoint pubblici del servizio siano associati a un VNet in Azure. Le progettazioni di esempio includono:
- Siti Web (IaaS) in Azure e database back-end nel Data Center locale. 
- Il gateway applicazione in Azure (protezione DDoS abilitata sul gateway app/WAF) e i siti Web nei data center locali.

Per altri dettagli, vedere le [architetture di riferimento standard di protezione DDoS di Azure](ddos-protection-reference-architectures.md) .

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>È possibile registrare un dominio all'esterno di Azure e associarlo a una risorsa protetta, ad esempio VM o ELB?
Per gli scenari di indirizzi IP pubblici, il servizio protezione DDoS supporterà qualsiasi applicazione indipendentemente dalla posizione in cui il dominio associato è registrato o ospitato a condizione che l'indirizzo IP pubblico associato sia ospitato in Azure. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>È possibile configurare manualmente i criteri DDoS applicati agli indirizzi IP pubblici/reti virtuali?
No, sfortunatamente la personalizzazione dei criteri non è disponibile in questo momento.

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>È possibile consentire indirizzi IP specifici dell'utente o del blocco?
No, sfortunatamente la configurazione manuale non è disponibile in questo momento.

## <a name="how-can-i-test-ddos-protection"></a>Come è possibile testare la protezione DDoS?
Vedere [test tramite simulazioni](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Quanto tempo è necessario per il caricamento delle metriche nel portale?
Le metriche devono essere visibili nel portale entro 5 minuti. Se la risorsa è sotto attacco, altre metriche verranno visualizzate nel portale entro 5-7 minuti. 
    
