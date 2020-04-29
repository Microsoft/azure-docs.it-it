---
title: Domande frequenti su Firewall di Azure
description: Domande frequenti sul firewall di Azure. Servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: ea94e452b463fffc1800e09fa1302abacdf015cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383063"
---
# <a name="azure-firewall-faq"></a>Domande frequenti su Firewall di Azure

## <a name="what-is-azure-firewall"></a>Informazioni sul firewall di Azure

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti. È possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Quali funzionalità sono supportate nel Firewall di Azure?

* Firewall con stato come servizio
* Disponibilità elevata e scalabilità cloud senza limiti
* Filtro dei nomi di dominio completi
* Tag FQDN
* Regole di filtro per il traffico di rete
* Supporto SNAT in uscita
* Supporto DNAT in ingresso
* Possibilità di creare, applicare e registrare criteri di connettività di applicazioni e rete in modo centralizzato tra le reti virtuali e le sottoscrizioni di Azure
* Integrazione completa con Monitoraggio di Azure per la registrazione e l'analisi

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual è il modello di distribuzione tipico per Firewall di Azure?

È possibile distribuire Firewall di Azure in qualsiasi rete virtuale, ma i clienti distribuiscono in genere Firewall di Azure in una rete virtuale centrale e configurano il peering di altre reti virtuali verso tale rete in un modello di tipo hub-spoke. È quindi possibile impostare la route predefinita dalle reti virtuali con peering verso la rete virtuale centrale del firewall. Il peering VNet globale è supportato, ma non è consigliato a causa di potenziali problemi di prestazioni e latenza tra le aree. Per ottenere prestazioni ottimali, distribuire un firewall per ogni area.

Il vantaggio offerto da questo modello consiste nella possibilità di esercitare il controllo centralizzato su più reti virtuali spoke tramite diverse sottoscrizioni. È inoltre possibile usufruire di un risparmio sui costi perché non è necessario distribuire un firewall separatamente in ogni rete virtuale. Il risparmio sui costi deve essere misurato rispetto al costo di peering associato in base ai modelli di traffico dei clienti.

## <a name="how-can-i-install-the-azure-firewall"></a>Come si installa Firewall di Azure?

È possibile configurare Firewall di Azure tramite il portale di Azure, PowerShell o l'API REST oppure usando modelli. Per istruzioni dettagliate, vedere [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md).

## <a name="what-are-some-azure-firewall-concepts"></a>Su quali concetti si basa Firewall di Azure?

Firewall di Azure supporta regole e raccolte di regole. Una raccolta di regole è un set di regole con lo stesso ordine e la stessa priorità. Le raccolte di regole vengono eseguite in ordine di priorità. Quelle di rete hanno maggiore priorità rispetto a quelle di applicazione e tutte le regole sono di terminazione.

Sono disponibili tre tipi di raccolte di regole:

* *Regole dell'applicazione*: configurare nomi di dominio completi (FQDN) a cui è possibile accedere da una subnet.
* *Regole di rete*: configurare regole che contengono indirizzi di origine, protocolli, porte di destinazione e indirizzi di destinazione.
* *Regole NAT*: configurare le regole di DNAT per consentire le connessioni Internet in ingresso.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Firewall di Azure supporta il filtraggio del traffico in ingresso?

Firewall di Azure supporta i filtri in ingresso e in uscita. La protezione in ingresso viene in genere usata per i protocolli non HTTP/S. Ad esempio i protocolli RDP, SSH e FTP. Per la migliore protezione HTTP/S in ingresso, usare un web application firewall come [Azure Web Application Firewall (WAF)](../web-application-firewall/overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quali servizi di registrazione e analisi sono supportati da Firewall di Azure?

Firewall di Azure è integrato con Monitoraggio di Azure per la visualizzazione e l'analisi dei log di firewall. I log possono essere inviati a Log Analytics, Archiviazione di Azure o Hub eventi e possono essere analizzati in Log Analytics o con strumenti diversi, ad esempio Excel e Power BI. Per altre informazioni, vedere [Esercitazione: Monitorare i log di Firewall di Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Come funziona Firewall di Azure rispetto ad altri servizi esistenti, come le appliance virtuali di rete, nel marketplace?

Firewall di Azure è un servizio firewall di base che consente di risolvere determinati scenari dei clienti. Si prevede che sia presente una combinazione di appliance virtuali di terze parti e di un firewall di Azure. L'integrazione è una priorità fondamentale.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual è la differenza tra la funzionalità Web application firewall del gateway applicazione e Firewall di Azure?

Web application firewall (WAF) è una funzionalità del gateway applicazione che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Firewall di Azure fornisce la protezione in ingresso per i protocolli non HTTP/S (ad esempio, RDP, SSH, FTP), la protezione a livello di rete in uscita per tutte le porte e tutti i protocolli e la protezione a livello di applicazione per HTTP/S in uscita.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual è la differenza tra i gruppi di sicurezza di rete e Firewall di Azure?

Il servizio Firewall di Azure si integra con la funzionalità dei gruppi sicurezza di rete offrendo una migliore sicurezza di rete con strategie di difesa avanzate. I gruppi di sicurezza di rete forniscono un filtraggio del traffico distribuito a livello di rete per limitare il traffico verso le risorse all'interno delle reti virtuali in ogni sottoscrizione. Firewall di Azure è un firewall di rete centralizzato con stato completo, distribuito come servizio, che fornisce protezione a livello di rete e di applicazione in diverse sottoscrizioni e reti virtuali.

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>I gruppi di sicurezza di rete (gruppi) sono supportati in AzureFirewallSubnet?

Il firewall di Azure è un servizio gestito con più livelli di protezione, inclusa la protezione della piattaforma con gruppi a livello di NIC (non visualizzabile).  I gruppi a livello di subnet non sono necessari in AzureFirewallSubnet e sono disabilitati per evitare interruzioni del servizio.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Come si configura Firewall di Azure con gli endpoint di servizio?

Per l'accesso sicuro ai servizi PaaS, è consigliabile usare gli endpoint di servizio. È possibile scegliere di abilitare gli endpoint di servizio nella subnet di Firewall di Azure e disabilitarli nelle reti virtuali spoke connesse. In questo modo è possibile trarre vantaggio da entrambe le funzionalità: sicurezza degli endpoint di servizio e registrazione centrale per tutto il traffico.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Quali prezzi vengono applicati per Firewall di Azure?

Vedere [prezzi di Azure firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Come è possibile arrestare e avviare il Firewall di Azure?

È possibile usare i metodi di *deallocazione* e *allocazione* di Azure PowerShell.

Ad esempio:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> È necessario riassegnare un firewall e un indirizzo IP pubblico al gruppo di risorse e alla sottoscrizione originali.

## <a name="what-are-the-known-service-limits"></a>Quali sono i limiti noti del servizio?

Per i limiti del servizio firewall di Azure, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Firewall di Azure in una rete virtuale hub può inoltrare e filtrare il traffico di rete tra due reti virtuali spoke?

Sì, è possibile usare il Firewall di Azure in una rete virtuale hub per instradare e filtrare il traffico tra due reti virtuali spoke. Per il corretto funzionamento di questo scenario, le subnet in ognuna delle reti virtuali spoke devono avere un UDR che punta al firewall di Azure come gateway predefinito.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Firewall di Azure può inoltrare e filtrare il traffico di rete tra subnet della stessa rete virtuale o di reti virtuali con peering?

Sì. Tuttavia, la configurazione di UdR per reindirizzare il traffico tra subnet nella stessa VNET richiede un'ulteriore attenzione. Anche se l'uso dell'intervallo di indirizzi della rete virtuale come prefisso di destinazione per il routing definito dall'utente è sufficiente, in questo modo si instrada tutto il traffico da un computer a un altro computer nella stessa subnet tramite l'istanza di Firewall di Azure. Per evitare questo problema, includere una route per la subnet nel routing definito dall'utente con un hop successivo di tipo **VNET**. La gestione di queste route può essere complessa e soggetta a errori. Il metodo consigliato per la segmentazione della rete interna consiste nell'usare i gruppi di sicurezza di rete, che non richiedono UdR.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Azure firewall SNAT in uscita tra le reti private?

Il firewall di Azure non SNAT quando l'indirizzo IP di destinazione è un intervallo IP privato per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, il firewall di Azure SNATs il traffico verso uno degli indirizzi IP privati del firewall in AzureFirewallSubnet. È possibile configurare Firewall di Azure in modo da **non** usare SNAT per l'intervallo di indirizzi IP pubblici. Per altre informazioni, vedere [Intervalli di indirizzi IP privati SNAT di Firewall di Azure](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Il tunneling o il concatenamento forzato a un'appliance virtuale di rete è supportato?

Il tunneling forzato è supportato. Per altre informazioni, vedere il [tunneling forzato del firewall di Azure (anteprima)](forced-tunneling.md). 

Connettività diretta al Firewall di Azure. Se AzureFirewallSubnet apprende una route predefinita alla rete locale tramite BGP è necessario sostituirla con una route UDR 0.0.0.0/0 con il valore **NextHopType** impostato come **Internet** per mantenere connettività diretta a Internet.

Se la configurazione richiede il tunneling forzato in una rete locale ed è possibile determinare i prefissi IP di destinazione per le destinazioni Internet, è possibile configurare questi intervalli con la rete locale come hop successivo tramite una route definita dall'utente in AzureFirewallSubnet. In alternativa, per definire queste route, è possibile usare BGP.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Vi sono restrizioni relative al gruppo di risorse del firewall?

Sì. Il firewall, VNet, e l'indirizzo IP pubblico devono trovarsi tutti nello stesso gruppo di risorse.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Quando si configura DNAT per il traffico di rete Internet in ingresso, è necessario configurare anche una regola di rete corrispondente per consentire il traffico?

No. Le regole NAT aggiungono in modo implicito una regola di rete corrispondente per consentire il traffico convertito. È possibile sostituire questo comportamento aggiungendo in modo esplicito una raccolta regole di rete con regole di negazione corrispondenti al traffico convertito. Per altre informazioni sulla logica di elaborazione delle regole di Firewall di Azure, vedere [Azure Firewall rule processing logic](rule-processing.md) (Logica di elaborazione delle regole di Firewall di Azure).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Come funzionano i caratteri jolly in un nome di dominio completo di destinazione di una regola dell'applicazione?

Se si configura ***. contoso.com**, consente *anyvalue*. contoso.com, ma non contoso.com (il vertice del dominio). Se si vuole consentire l'apice del dominio, è necessario configurarlo in modo esplicito come FQDN di destinazione.

## <a name="what-does-provisioning-state-failed-mean"></a>Cosa significa *lo stato di provisioning: errore* ?

Ogni volta che viene applicata una modifica alla configurazione, il firewall di Azure tenta di aggiornare tutte le istanze back-end sottostanti. In rari casi, una di queste istanze back-end potrebbe non riuscire ad aggiornarsi con la nuova configurazione e il processo di aggiornamento si interrompe con uno stato di provisioning non riuscito. Il firewall di Azure è ancora operativo, ma la configurazione applicata potrebbe trovarsi in uno stato incoerente, in cui alcune istanze hanno la configurazione precedente, in cui altre hanno il set di regole aggiornato. In tal caso, provare ad aggiornare la configurazione ancora una volta fino a quando l'operazione ha esito positivo e il firewall si trova in uno stato di provisioning *riuscito* .

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>In che modo il firewall di Azure gestisce la manutenzione pianificata e gli errori non pianificati?
Il firewall di Azure è costituito da diversi nodi back-end in una configurazione Active-Active.  Per qualsiasi manutenzione pianificata, è disponibile la logica di svuotamento della connessione per aggiornare correttamente i nodi.  Gli aggiornamenti vengono pianificati durante le ore non lavorative per ogni area di Azure per limitare ulteriormente il rischio di rotture.  Per i problemi non pianificati, viene creata un'istanza di un nuovo nodo per sostituire il nodo che ha avuto esito negativo.  La connettività al nuovo nodo viene in genere ristabilita entro 10 secondi dal momento in cui si è verificato l'errore.

## <a name="how-does-connection-draining-work"></a>Come funziona lo svuotamento della connessione?

Per qualsiasi manutenzione pianificata, la logica di svuotamento della connessione aggiorna correttamente i nodi back-end. Il firewall di Azure attende 90 secondi per la chiusura delle connessioni esistenti. Se necessario, i client possono ristabilire automaticamente la connettività a un altro nodo back-end.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Esiste un limite di caratteri per un nome di firewall?

Sì. È previsto un limite di 50 caratteri per il nome di un firewall.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Perché il firewall di Azure necessita di una dimensione della subnet/26?

Il firewall di Azure deve effettuare il provisioning di più istanze di macchine virtuali in scala. Uno spazio degli indirizzi/26 garantisce che il firewall disponga di un numero sufficiente di indirizzi IP disponibili per la scalabilità.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>È necessario modificare la dimensione della subnet del firewall con la scalabilità del servizio?

No. Il firewall di Azure non necessita di una subnet maggiore di/26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Come è possibile aumentare la velocità effettiva del firewall?

La capacità di velocità effettiva iniziale del firewall di Azure è 2,5-3 Gbps ed è scalabile fino a 30 Gbps. Viene scalato automaticamente in base all'utilizzo della CPU e alla velocità effettiva.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Quanto tempo è necessario per la scalabilità orizzontale del firewall di Azure?

Il firewall di Azure si ridimensiona gradualmente quando il consumo medio della velocità effettiva o della CPU è al 60%. La scalabilità orizzontale richiede da cinque a sette minuti. Quando si esegue il test delle prestazioni, assicurarsi di eseguire il test per almeno 10-15 minuti e avviare nuove connessioni per sfruttare i vantaggi dei nodi firewall appena creati.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Il firewall di Azure consente l'accesso alle Active Directory per impostazione predefinita?

No. Il firewall di Azure blocca Active Directory accesso per impostazione predefinita. Per consentire l'accesso, configurare il tag del servizio AzureActiveDirectory. Per altre informazioni, vedere [tag del servizio firewall di Azure](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>È possibile escludere un nome di dominio completo o un indirizzo IP dal filtro basato su Intelligence per le minacce del firewall di Azure?

Sì, è possibile usare Azure PowerShell per eseguire questa operazione:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist.FQDNs = @("fqdn1", "fqdn2", …)
$fw.ThreatIntelWhitelist.IpAddress = @("ip1", "ip2", …)

Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Perché un ping TCP e strumenti simili si connettono correttamente a un FQDN di destinazione anche quando nessuna regola nel firewall di Azure consente il traffico?

Un ping TCP non si connette effettivamente al nome di dominio completo di destinazione. Questo problema si verifica perché il proxy trasparente del firewall di Azure è in ascolto sulla porta 80/443 per il traffico in uscita. Il ping TCP stabilisce una connessione con il firewall, che quindi Elimina il pacchetto e registra la connessione. Questo comportamento non ha alcun effetto sulla sicurezza. Tuttavia, per evitare confusione, verranno esaminate le potenziali modifiche apportate a questo comportamento.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Sono previsti limiti per il numero di indirizzi IP supportati da gruppi IP?

Sì. Per altre informazioni, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)
