---
title: Procedure consigliate per la sicurezza di rete - Microsoft Azure
description: Questo articolo fornisce una serie di procedure consigliate per la sicurezza di rete usando le funzionalità integrate di Azure.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 4793216a12b17c4e4ea03f62d5a0ba512febc232
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735727"
---
# <a name="azure-best-practices-for-network-security"></a>Procedure consigliate di Azure per la sicurezza di rete
Questo articolo illustra un insieme di procedure consigliate di Azure per migliorare la sicurezza della rete, derivate dalla nostra esperienza con la rete di Azure e dalle esperienze di altri clienti.

Per ogni procedura consigliata questo articolo spiega:

* Qual è la procedura consigliata
* Il motivo per cui si vuole abilitare tale procedura consigliata
* Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
* Alternative possibili alla procedura consigliata
* Come imparare ad abilitare la procedura consigliata

Le procedure consigliate si basano su un parere condiviso, nonché sulle capacità e sui set di funzionalità della piattaforma di Azure esistenti al momento della presente redazione. Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

## <a name="use-strong-network-controls"></a>Usare controlli di rete avanzati
È possibile connettere i dispositivi e le [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/) ad altri dispositivi di rete inserendoli in [reti virtuali di Azure](../../virtual-network/index.yml). Vale a dire che si possono connettere le schede di interfaccia di rete virtuale a una rete virtuale per consentire le comunicazioni basate su TCP/IP tra dispositivi di rete abilitati. Le macchine virtuali connesse a una rete virtuale di Azure possono connettersi ai dispositivi nella stessa rete virtuale, in reti virtuali diverse, su Internet o persino in reti locali.

Quando si pianifica la rete e la sicurezza della rete, è consigliabile centralizzare gli elementi seguenti:

- Gestione delle funzioni di rete core, come ExpressRoute, provisioning di rete virtuale e subnet e indirizzi IP.
- Governance degli elementi di sicurezza di rete, ad esempio funzioni di appliance virtuali di rete come ExpressRoute, provisioning della rete virtuale e delle subnet e indirizzamento IP.

Se si usa un insieme comune di strumenti di gestione per monitorare la rete e la sicurezza della rete, si ottiene una visibilità ottimale su entrambe. Una strategia di sicurezza semplice e unificata riduce gli errori, perché aumenta il riconoscimento umano e l'affidabilità dell'automazione.

## <a name="logically-segment-subnets"></a>Segmentare logicamente le subnet
Le reti virtuali di Azure sono simili a reti LAN nella rete locale. Una rete virtuale di Azure prevede la creazione di una rete basata su un singolo spazio indirizzi IP privato, in cui è possibile inserire tutte le macchine virtuali di Azure. Gli spazi indirizzi IP privati disponibili sono negli intervalli di Classe A (10.0.0.0/8), Classe B (172.16.0.0/12) e Classe C (192.168.0.0/16).

Le procedure consigliate per suddividere logicamente le subnet includono:

**Procedura consigliata**: non assegnare regole di assenso con intervalli ampi (ad esempio, da 0.0.0.0 a 255.255.255.255).  
**Dettagli**: assicurarsi che le procedure di risoluzione dei problemi impediscano o vietino la configurazione di questi tipi di regole. Queste regole di assenso trasmettono un falso senso di sicurezza e vengono spesso trovate e sfruttate dai Red Team.

**Procedura consigliata**: segmentare lo spazio di indirizzi più ampio in subnet.   
**Dettagli**: usare i principi di suddivisione in subnet basati su [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) per creare le subnet.

**Procedura consigliata**: creare controlli di accesso di rete tra subnet. Il routing tra le subnet viene eseguito automaticamente e non è necessario configurare manualmente le tabelle di routing. Per impostazione predefinita, non sono presenti controlli di accesso di rete tra le subnet create in una rete virtuale di Azure.   
**Dettagli**: usare un [gruppo di sicurezza di rete](../../virtual-network/virtual-network-vnet-plan-design-arm.md) per proteggersi da traffico non richiesto in entrata nelle subnet di Azure. I gruppi di sicurezza di rete sono semplici dispositivi di ispezione dei pacchetti con stato, che usano l'approccio a 5 tuple (indirizzo IP di origine, porta di origine, IP di destinazione, porta di destinazione e protocollo di livello 4) per creare regole allow/deny per il traffico di rete. È possibile consentire o negare il traffico da e verso un singolo indirizzo IP, da e verso più indirizzi IP o da e verso intere subnet.

L'uso di gruppi di sicurezza di rete per il controllo di accesso di rete tra subnet consente di inserire nelle proprie subnet risorse che appartengono alla stessa area di protezione o allo stesso ruolo.

**Procedura consigliata**: evitare reti virtuali e subnet di piccole dimensioni, per garantire semplicità e flessibilità.   
**Dettagli**: la maggior parte delle organizzazioni aggiunge più risorse di quelle pianificate inizialmente e la riallocazione degli indirizzi richiede molto lavoro. L'uso di subnet di piccole dimensioni aggiunge un valore di sicurezza limitato e il mapping di un gruppo di sicurezza di rete a ogni subnet comporta un sovraccarico di lavoro. Definire subnet di grandi dimensioni per garantire la flessibilità necessaria per la crescita.

**Procedura consigliata**: semplificare la gestione delle regole del gruppo di sicurezza di rete definendo [gruppi di sicurezza delle applicazioni](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Dettagli**: definire un gruppo di sicurezza delle applicazioni per elenchi di indirizzi IP che si ritiene potrebbero cambiare in futuro o essere usati in molti gruppi di sicurezza di rete. Assicurarsi di assegnare nomi espliciti ai gruppi di sicurezza delle applicazioni, in modo che gli altri utenti possano comprenderne il contenuto e lo scopo.

## <a name="adopt-a-zero-trust-approach"></a>Adottare un approccio Zero Trust
Le reti basate sul perimetro operano in base al presupposto che tutti i sistemi all'interno di una rete possono essere considerati attendibili. Tuttavia, nelle organizzazioni moderne i dipendenti accedono alle risorse da qualsiasi posizione su una vasta gamma di dispositivi e app, rendendo irrilevanti i controlli di sicurezza perimetrali. I criteri di controllo di accesso che si concentrano solo su chi può accedere a una risorsa non sono sufficienti. Per gestire in modo ottimale l'equilibrio tra sicurezza e produttività, gli amministratori della sicurezza devono anche considerare *come* si accede a una risorsa.

Le reti devono aggiungere nuove soluzioni alle difese tradizionali, perché possono diventare vulnerabili alle violazioni: un utente malintenzionato può compromettere un singolo endpoint entro il perimetro attendibile e quindi espandere il controllo all'intera rete. Le reti [Zero Trust](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) eliminano il concetto di attendibilità basato sul percorso di rete all'interno di un perimetro. Le architetture Zero Trust usano invece le attestazioni di attendibilità del dispositivo e dell'utente per controllare l'accesso ai dati e alle risorse dell'organizzazione. Per le nuove iniziative è consigliabile adottare approcci Zero Trust che convalidano l'attendibilità al momento dell'accesso.

Procedure consigliate:

**Procedura consigliata**: offrire l'accesso condizionale alle risorse in base a dispositivo, identità, garanzia, percorso di rete e altro ancora.  
**Dettagli**: l'[accesso condizionale di Azure AD](../../active-directory/conditional-access/overview.md) consente di applicare i controlli di accesso appropriati implementando decisioni di controllo di accesso automatiche sulla base delle condizioni richieste. Per altre informazioni, vedere [Gestire l'accesso alla gestione di Azure con accesso condizionale](../../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).

**Procedura consigliata**: abilitare l'accesso alla porta solo dopo l'approvazione del flusso di lavoro.  
**Dettagli**: l'[accesso JIT alle macchine virtuali nel Centro sicurezza di Azure](../../security-center/security-center-just-in-time.md) può essere usato per bloccare il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e offrendo al tempo stesso un accesso facile per connettersi alle macchine virtuali quando necessario.

**Procedura consigliata**: concedere autorizzazioni temporanee per l'esecuzione di attività con privilegi, per impedire a utenti non autorizzati o malintenzionati di ottenere l'accesso dopo la scadenza delle autorizzazioni. L'accesso viene concesso solo quando l'utente ne ha necessità.  
**Dettagli**: usare l'accesso JIT in Azure AD Privileged Identity Management o in una soluzione di terze parti per concedere le autorizzazioni per l'esecuzione di attività con privilegi.

Zero Trust è la nuova fase nell'evoluzione della sicurezza di rete. Lo stato degli attacchi cibernetici spinge le organizzazioni ad adottare la mentalità "presunta violazione", ma questo approccio non dovrebbe essere limitante. Le reti Zero Trust proteggono i dati e le risorse aziendali, garantendo nel contempo alle organizzazioni la possibilità di creare uno spazio di lavoro moderno tramite tecnologie che consentono ai dipendenti di essere produttivi in qualsiasi momento, ovunque e con qualsiasi modalità.

## <a name="control-routing-behavior"></a>Controllare il comportamento di routing
Quando si inserisce una macchina virtuale in una rete virtuale di Azure, la macchina virtuale può connettersi a qualsiasi altra macchina virtuale nella stessa rete virtuale e ad altre macchine virtuali in subnet diverse. Questo è possibile perché un insieme di route di sistema, abilitate per impostazione predefinita, consente questo tipo di comunicazione. Queste route predefinite consentono alle macchine virtuali nella stessa rete virtuale di avviare le connessioni tra loro e con Internet (per le comunicazioni in uscita solo con Internet).

Nonostante le route di sistema predefinite siano utili per molti scenari di distribuzione, in alcuni casi si vuole personalizzare la configurazione del routing per le distribuzioni. È consentito configurare l'indirizzo hop successivo in modo da raggiungere destinazioni specifiche.

È consigliabile configurare [route definite dall'utente](../../virtual-network/virtual-networks-udr-overview.md) quando si distribuisce un dispositivo di sicurezza per una rete virtuale. Questo aspetto viene affrontato in una sezione successiva intitolata [Associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Le route definite dall'utente non sono necessarie e le route di sistema predefinite generalmente funzionano.
>
>

## <a name="use-virtual-network-appliances"></a>Usare i dispositivi di rete virtuale
I gruppi di sicurezza di rete e il routing definito dall'utente possono garantire un certo grado di sicurezza della rete nei livelli di rete e trasporto del [modello OSI](https://en.wikipedia.org/wiki/OSI_model). Ma in alcune situazioni, è preferibile o necessario abilitare la sicurezza ai livelli alti dello stack. In tali situazioni, è consigliabile distribuire i dispositivi di sicurezza di rete virtuale forniti dai partner di Azure.

I dispositivi di sicurezza di rete di Azure possono offrire livelli di sicurezza migliori rispetto a quanto offerto dai controlli a livello di rete. Le funzionalità di sicurezza di rete fornite da dispositivi di sicurezza di rete virtuale includono:


* Funzionalità di firewall
* Rilevamento intrusioni/Prevenzione intrusioni
* Gestione vulnerabilità
* Controllo applicazione
* Rilevamento anomalie basato su rete
* Filtro Web
* Antivirus
* Protezione botnet

Per trovare i dispositivi di sicurezza di rete virtuale di Azure, visitare [Azure Marketplace](https://azure.microsoft.com/marketplace/) e cercare "sicurezza" e "sicurezza di rete".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Distribuire reti perimetrali per le aree di sicurezza
Una [rete perimetrale](/azure/architecture/vdc/networking-virtual-datacenter) è un segmento di rete fisica o logica che fornisce un ulteriore livello di sicurezza tra le risorse e Internet. I dispositivi specializzati per il controllo di accesso alla rete dislocati al margine di una rete perimetrale lasciano entrare nella rete virtuale solo il traffico desiderato.

Le reti perimetrali sono utili perché permettono di concentrare le operazioni di gestione, monitoraggio, registrazione e creazione di report del controllo di accesso alla rete sui dispositivi della rete virtuale di Azure. Una rete perimetrale è lo scenario in cui sono in genere abilitati la prevenzione DDoS (Distributed Denial of Service), i sistemi di rilevamento intrusione/prevenzione intrusioni (IDS/IPS), le regole e i criteri dei firewall, il filtro Web, il software antimalware per la rete e molto altro. I dispositivi di sicurezza di rete sono posizionati tra Internet e la rete virtuale di Azure e hanno un'interfaccia su entrambe le reti.

Questo è un modello di rete perimetrale base, ma esistono molti modelli diversi di rete perimetrale, ad esempio le reti back to back, tri-homed e multihomed.

In base al concetto Zero Trust indicato in precedenza, è consigliabile usare una rete perimetrale per tutte le implementazioni con sicurezza elevata, per migliorare il livello di sicurezza di rete e il controllo di accesso per le risorse di Azure. È possibile usare Azure o una soluzione di terze parti per garantire un livello di sicurezza aggiuntivo tra gli asset dell'organizzazione e Internet:

- Controlli nativi di Azure. Il [Firewall di Azure](../../firewall/overview.md) e il [web application firewall nel gateway applicazione](../../application-gateway/features.md#web-application-firewall) offrono la sicurezza di base grazie a un firewall con stato come servizio, disponibilità elevata incorporata, scalabilità cloud illimitata, filtro FQDN, supporto per set di regole core OWASP e installazione e configurazione intuitive.
- Offerte di terzi. Cercare in [Azure Marketplace](https://azuremarketplace.microsoft.com/) firewall di nuova generazione (NGFW) e altre offerte di terze parti che includono strumenti di sicurezza noti e livelli di sicurezza di rete notevolmente migliorati. La configurazione può essere più complessa, ma un'offerta di terze parti potrebbe consentire l'uso di funzionalità e set di competenze esistenti.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitare l'esposizione a Internet con collegamenti WAN dedicati
Molte organizzazioni hanno scelto la strada dell'IT ibrido. Nell'ambiente IT ibrido, alcuni asset di informazioni dell'organizzazione si trovano in Azure e altri rimangono in locale. In molti casi alcuni componenti di un servizio sono eseguiti in Azure mentre altri componenti restano in locale.

In uno scenario IT ibrido è in genere presente un certo grado di connettività cross-premise, che consente alla società di connettere le proprie reti locali alle reti virtuali di Azure. Sono disponibili due soluzioni di connettività cross-premise:

* [VPN da sito a sito](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). È una tecnologia attendibile, affidabile e consolidata, ma la connessione avviene tramite Internet. La larghezza di banda è vincolata a un massimo di circa 1,25 Gbps. La VPN da sito a sito è un'opzione ottimale in determinati scenari.
* **Azure ExpressRoute**. Per la connettività cross-premise, [ExpressRoute](../../expressroute/expressroute-introduction.md) è la scelta consigliata. ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, ad esempio Azure, Microsoft 365 e Dynamics 365. ExpressRoute è un collegamento WAN dedicato tra il percorso locale e un provider di hosting di Microsoft Exchange. Dal momento che si tratta di una connessione di telecomunicazioni, i dati non vengono trasmessi via Internet e quindi non sono esposti a potenziali rischi legati alle comunicazioni Internet.

Il percorso della connessione ExpressRoute può influire sulla capacità del firewall, la scalabilità, l'affidabilità e la visibilità del traffico di rete. È necessario identificare la posizione in cui terminare ExpressRoute nelle reti esistenti (locali). È possibile:

- Terminare all'esterno del firewall (paradigma della rete perimetrale) se è necessaria visibilità sul traffico, se è necessario mantenere una prassi esistente di isolamento dei data center o se si stanno inserendo in Azure solo risorse extranet.
- Terminare all'interno del firewall (paradigma dell'estensione di rete). Questa è la raccomandazione predefinita. In tutti gli altri casi è consigliabile considerare Azure come un ennesimo data center.

## <a name="optimize-uptime-and-performance"></a>Ottimizzare il tempo di attività e le prestazioni
Se un servizio non è attivo, non è possibile accedere alle informazioni. Se le prestazioni sono talmente insufficienti da rendere i dati inutilizzabili, è possibile considerare che i dati siano inaccessibili. Dal punto di vista della sicurezza, è necessario fare del proprio meglio per assicurarsi che i servizi offrano sempre prestazioni e tempi di attività ottimali.

Uno dei metodi più diffusi ed efficaci per migliorare le prestazioni e la disponibilità consiste nel ricorrere al bilanciamento del carico. Il bilanciamento del carico è un metodo di distribuzione del traffico di rete tra server che fanno parte di un servizio. Ad esempio, se dei server Web front-end fanno parte del servizio, è possibile usare il bilanciamento del carico per distribuire il traffico tra più server Web front-end.

Questa distribuzione del traffico aumenta la disponibilità perché se uno dei server Web non è più disponibile, il servizio di bilanciamento del carico interrompe l'invio di traffico a tale server, reindirizzandolo verso i server che sono ancora online. Il bilanciamento del carico favorisce anche le prestazioni, perché il sovraccarico di processore, rete e memoria per l'elaborazione delle richieste viene distribuito tra tutti server con carico bilanciato.

È consigliabile impiegare il bilanciamento del carico ogni volta possibile e quando adeguato ai servizi. Di seguito sono descritti degli scenari a livello di rete virtuale di Azure e altri a livello globale, con le opzioni di bilanciamento del carico per ognuno.

**Scenario**: è presente un'applicazione che:

- Necessita che le richieste provengano dalla stessa sessione utente/client per raggiungere la stessa macchina virtuale back-end. Esempi di queste applicazioni sono i carrelli dei siti di acquisti e i server di posta Web.
- Accetta solo connessioni protette, pertanto le comunicazioni non crittografate verso il server non rappresentano un'opzione accettabile.
- Necessita che più richieste HTTP nella stessa connessione TCP con esecuzione prolungata vengano instradate/bilanciate in server back-end diversi.

**Opzione di bilanciamento del carico**: usare il [gateway applicazione di Azure](../../application-gateway/overview.md), un servizio di bilanciamento del carico correlato al traffico Web HTTP. Il gateway applicazione supporta la crittografia TLS e la [terminazione TLS](../../application-gateway/overview.md) sul gateway. I server Web possono quindi essere liberati dal sovraccarico prodotto dai processi di crittografia e decrittografia e il traffico può essere trasmesso in formato non crittografato ai server back-end.

**Scenario**: è necessario applicare il bilanciamento del carico alle connessioni in ingresso da Internet tra i server che si trovano in una rete virtuale di Azure. Scenari in cui:

- Sono presenti applicazioni senza stato che accettano le richieste in ingresso da Internet.
- Non sono richieste sessioni permanenti o l'offload TLS. La combinazione di sessioni permanenti e del bilanciamento del carico delle applicazioni è un metodo per ottenere l'affinità dei server.

**Opzione di bilanciamento del carico**: usare il portale di Azure per [creare un servizio di bilanciamento del carico esterno](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) che distribuisca le richieste in ingresso tra più macchine virtuali per offrire un livello superiore di disponibilità.

**Scenario**: è necessario applicare il bilanciamento del carico alle connessioni provenienti da macchine virtuali che non si trovano su Internet. Nella maggior parte dei casi, le connessioni che sono accettate per il bilanciamento del carico possono essere avviate da dispositivi nella rete virtuale di Azure, quali le istanze SQL Server o i server Web interni.   
**Opzione di bilanciamento del carico**: usare il portale di Azure per [creare un servizio di bilanciamento del carico interno](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) che distribuisca le richieste in ingresso tra più macchine virtuali per offrire un livello superiore di disponibilità.

**Scenario**: è necessario il bilanciamento del carico globale perché:

- Si dispone di una soluzione cloud ampiamente distribuita tra più aree e che richiede il livello massimo di tempo di attività (disponibilità) possibile.
- Si necessita del massimo livello di tempo di attività possibile per assicurarsi che il servizio sia disponibile anche se un intero data center diventa non disponibile.

**Opzione di bilanciamento del carico**: usare Gestione traffico di Azure. Gestione traffico rende possibile bilanciare il carico delle connessioni ai servizi in base alla posizione dell'utente.

Ad esempio, se l'utente effettua una richiesta al servizio dall'Unione europea, la connessione viene indirizzata ai servizi che si trovano in un data center dell'Unione europea. Questa parte bilanciamento del carico globale di Gestione traffico consente di migliorare le prestazioni perché la connessione al data center più vicino è più veloce rispetto alla connessione ai data center più lontani.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Disabilitare l'accesso RDP/SSH alle macchine virtuali
È possibile raggiungere le macchine virtuali di Azure usando i protocolli [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) e [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Questi protocolli consentono di gestire le macchine virtuali da postazioni remote e sono standard nel computing dei data center.

L'uso di questi protocolli in Internet può provocare tuttavia un potenziale problema di sicurezza perché gli utenti malintenzionati possono usare tecniche di [attacco di forza bruta](https://en.wikipedia.org/wiki/Brute-force_attack) per ottenere l'accesso alle macchine virtuali di Azure. Una volta che gli utenti malintenzionati avranno ottenuto l'accesso, potranno usare la macchina virtuale come punto di partenza per compromettere gli altri computer nella rete virtuale o persino per attaccare i dispositivi di rete all'esterno di Azure.

È consigliabile disabilitare l'accesso diretto RDP e SSH alle macchine virtuali di Azure da Internet. Dopo aver disabilitato l'accesso diretto RDP e SSH da Internet, sono disponibili altre opzioni per accedere a queste macchine virtuali per la gestione remota.

**Scenario**: consentire a un singolo utente di connettersi a una rete virtuale di Azure via Internet.   
**Opzione**: [VPN da punto a sito](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) è un altro termine per indicare una connessione client/server VPN con accesso remoto. Dopo aver stabilito la connessione da punto a sito, l'utente può usare RDP o SSH per connettersi a qualsiasi macchina virtuale presente nella rete virtuale Azure e a cui l'utente si è connesso con VPN da punto a sito. Ciò presuppone che l'utente sia autorizzato a raggiungere tali macchine virtuali.

La VPN da punto a sito è più sicura delle connessioni dirette RDP o SSH perché l'utente deve autenticarsi due volte prima di potersi connettere a una macchina virtuale. In primo luogo, l'utente deve eseguire l'autenticazione, e ottenere l'autorizzazione, per stabilire la connessione VPN da punto a sito. In secondo luogo, l'utente deve eseguire l'autenticazione, e ottenere l'autorizzazione, per stabilire la connessione RDP o SSH.

**Scenario**: consentire agli utenti della rete locale di connettersi alle macchine virtuali nella rete virtuale di Azure.   
**Opzione**: una [VPN da sito a sito](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) connette un'intera rete a un'altra rete via Internet. È possibile usare una VPN da sito a sito per connettere la rete locale a una rete virtuale di Azure. Gli utenti della rete locale si connettono usando il protocollo RDP o SSH tramite la connessione VPN da sito a sito. Non è necessario consentire l'accesso diretto RDP o SSH via Internet.

**Scenario**: usare un collegamento WAN dedicato per fornire funzionalità simili alla VPN da sito a sito.   
**Opzione**: usare [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Offre funzionalità simili a quelle della VPN da sito a sito. Le differenze principali sono le seguenti:

- Il collegamento WAN dedicato non attraversa Internet.
- I collegamenti WAN dedicati sono in genere più stabili e più efficienti.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali
Usare il collegamento privato di Azure per accedere ai servizi PaaS di Azure, ad esempio archiviazione di Azure e database SQL, tramite un endpoint privato nella rete virtuale. Gli endpoint privati consentono di proteggere le risorse critiche dei servizi di Azure solo per le reti virtuali. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure. L'esposizione della rete virtuale alla rete Internet pubblica non è più necessaria per l'utilizzo dei servizi PaaS di Azure. 

Il collegamento privato di Azure offre i vantaggi seguenti:
- **Sicurezza migliorata per le risorse dei servizi di Azure**: con collegamento privato di Azure, le risorse del servizio di Azure possono essere protette per la rete virtuale usando un endpoint privato. La protezione delle risorse dei servizi a un endpoint privato nella rete virtuale offre una maggiore sicurezza rimuovendo completamente l'accesso Internet pubblico alle risorse e consentendo il traffico solo dall'endpoint privato nella rete virtuale.
- **Accedere privatamente alle risorse dei servizi di Azure nella piattaforma Azure**: connettere la rete virtuale ai servizi in Azure usando endpoint privati. Non è necessario un indirizzo IP pubblico. La piattaforma di Collegamento privato gestirà la connettività tra l'utente e i servizi tramite la rete backbone di Azure.
- **Accesso da reti locali e con peering**: accesso ai servizi in esecuzione in Azure da locale tramite peering privato di ExpressRoute, tunnel VPN e reti virtuali con peering usando endpoint privati. Non è necessario configurare il peering Microsoft ExpressRoute o attraversare Internet per raggiungere il servizio. Collegamento privato offre un modo sicuro per eseguire la migrazione dei carichi di lavoro ad Azure.
- **Protezione dall'esfiltrazione di dati**: un endpoint privato viene mappato a un'istanza di una risorsa PaaS invece che all'intero servizio. Gli utenti possono connettersi solo alla risorsa specifica. L'accesso a qualsiasi altra risorsa del servizio è bloccato. Questo meccanismo offre protezione dai rischi di perdita dei dati.
- **Copertura globale**: è possibile connettersi privatamente a servizi in esecuzione in altre aree. La rete virtuale del consumer potrebbe trovarsi nell'area A ed è in grado di connettersi ai servizi nell'area B.
- **Semplicità di configurazione e gestione**: non sono più necessari indirizzi IP pubblici riservati nelle reti virtuali per proteggere le risorse di Azure tramite un firewall IP. Non sono necessari dispositivi NAT o gateway per configurare gli endpoint privati. Gli endpoint privati vengono configurati tramite un semplice flusso di lavoro. Sul lato del servizio è anche possibile gestire facilmente le richieste di connessione nella risorsa del servizio di Azure. Il collegamento privato di Azure funziona anche per gli utenti e i servizi appartenenti a diversi tenant di Azure Active Directory. 
    
Per altre informazioni sugli endpoint privati e i servizi e le aree di Azure per i quali sono disponibili endpoint privati, vedere il [collegamento privato di Azure](../../private-link/private-link-overview.md).


## <a name="next-steps"></a>Passaggi successivi
Per altre procedure di sicurezza consigliate da usare nella progettazione, distribuzione e gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](best-practices-and-patterns.md).