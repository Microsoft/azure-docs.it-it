---
title: Introduzione alla sicurezza di Azure | Microsoft Docs
description: Introduzione alla sicurezza di Azure, ai suoi vari servizi e al relativo funzionamento leggendo questa panoramica.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: TomSh
ms.openlocfilehash: 1159736d59e15fa36c0feb7e5d5b5553953fb6b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564512"
---
# <a name="introduction-to-azure-security"></a>Introduzione alla sicurezza di Azure

## <a name="overview"></a>Panoramica

La sicurezza è la priorità principale nel cloud ed è importante che l'utente trovi informazioni accurate e tempestive sulla sicurezza di Azure. Uno dei motivi migliori per usare Azure per le proprie applicazioni e i propri servizi consiste nella possibilità di sfruttare una vasta gamma di strumenti e funzionalità per la sicurezza. Questi strumenti e queste funzionalità consentono di creare soluzioni sicure sulla piattaforma Azure protetta. Microsoft Azure garantisce la riservatezza, l'integrità e la disponibilità dei dati dei clienti, assicurando anche al tempo stesso una rendicontazione trasparente.

Questo articolo fornisce una panoramica completa della sicurezza disponibile con Azure.

### <a name="azure-platform"></a>Piattaforma Azure

Azure è una piattaforma di servizi cloud pubblici che supporta un'ampia gamma di sistemi operativi, linguaggi di programmazione, framework, strumenti, database e dispositivi. Può eseguire contenitori Linux con integrazione con Docker, compilare app con JavaScript, Python, .NET, PHP, Java e Node.js e creare back-end per dispositivi iOS, Android e Windows.

I servizi cloud pubblici di Azure supportano le stesse tecnologie già considerate affidabili e usate da milioni di sviluppatori e professionisti IT. Creando asset IT o eseguendone la migrazione in un provider di servizi cloud pubblici, si dipende dalla capacità di tale organizzazione di proteggere le applicazioni e i dati con i servizi e i controlli offerti per gestire la sicurezza degli asset basati sul cloud.

L'infrastruttura di Azure è stata progettata, dalla struttura fino alle applicazioni, per ospitare simultaneamente milioni di clienti e garantisce alle aziende una solida base per poterne soddisfare i requisiti di sicurezza.

Azure, inoltre, offre una vasta gamma di opzioni di sicurezza configurabili e la possibilità di controllarle in modo da personalizzare la sicurezza in base ai requisiti univoci delle distribuzioni della propria organizzazione. Questo documento consente di comprendere come le funzionalità di sicurezza di Azure consentono di soddisfare tali requisiti.

> [!Note]
> L'argomento principale di questo documento sono i controlli destinati ai clienti che è possibile usare per personalizzare e aumentare la sicurezza delle proprie applicazioni e dei propri servizi.
>
> Per informazioni sul modo in cui Microsoft protegge la piattaforma Azure, vedere [sicurezza dell'infrastruttura di Azure](infrastructure.md).

## <a name="summary-of-azure-security-capabilities"></a>Riepilogo delle funzionalità di sicurezza di Azure

A seconda del modello di servizio cloud, la responsabilità per le persone incaricate della gestione della sicurezza per l'applicazione o il servizio può variare. Nella piattaforma Azure sono disponibili funzionalità che consentono di adempiere a tali responsabilità con caratteristiche predefinite e tramite soluzioni di partner che possono essere distribuite in una sottoscrizione di Azure.

Le funzionalità predefinite sono organizzate in sei aree funzionali: operazioni, applicazioni, archiviazione, rete, calcolo e identità. Ulteriori dettagli sulle funzionalità e sulle funzionalità disponibili nella piattaforma Azure in queste sei aree sono fornite tramite le informazioni di riepilogo.

## <a name="operations"></a>Gestione operativa

Questa sezione contiene informazioni aggiuntive sulle caratteristiche principali per le operazioni di sicurezza e informazioni di riepilogo su tali funzionalità.

### <a name="azure-security-center"></a>Centro sicurezza di Azure

Il [Centro sicurezza](../../security-center/security-center-introduction.md) consente di prevenire, rilevare e rispondere alle minacce con maggiore visibilità e controllo sulla sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Inoltre, il Centro sicurezza aiuta a usare le operazioni di sicurezza fornendo un unico dashboard che presenta avvisi e indicazioni su cui è possibile agire immediatamente. Spesso è possibile correggere i problemi con un solo clic nella console del Centro sicurezza.

### <a name="azure-resource-manager"></a>Azure Resource Manager

[Azure Resource Manager](../../azure-resource-manager/management/overview.md) consente di usare le risorse nella soluzione come gruppo. È possibile distribuire, aggiornare o eliminare tutte le risorse della soluzione con un'unica operazione coordinata. Per la distribuzione viene usato un [modello di Azure Resource Manager](../../azure-resource-manager/templates/overview.md) che può essere usato per diversi ambienti, ad esempio di testing, di staging e di produzione. Gestione risorse offre funzionalità di sicurezza, controllo e categorizzazione che semplificano la gestione delle risorse dopo la distribuzione.

Le distribuzioni basate su un modello di Azure Resource Manager consentono di migliorare la sicurezza delle soluzioni distribuite in Azure perché le impostazioni sono controllate dalla sicurezza standard, che può essere integrata in distribuzioni basate su modelli standardizzate. In questo modo si riduce il rischio di errori di configurazione della sicurezza che potrebbero verificarsi in caso di distribuzioni manuali.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) è un servizio estendibile di gestione delle prestazioni delle applicazioni (APM) per gli sviluppatori Web. con cui è possibile monitorare le applicazioni Web live e rilevare automaticamente le anomalie nelle prestazioni. Application Insights include avanzati strumenti di analisi che consentono di diagnosticare i problemi e conoscere come vengono effettivamente usate le app dagli utenti. Esegue il monitoraggio dell'applicazione per tutto il tempo che è in esecuzione, sia durante il test che dopo la pubblicazione o la distribuzione.

Application Insights crea grafici e tabelle che illustrano, ad esempio, in quali ore del giorno si ottengono più utenti, i tempi di risposta dell'app e come funzionano i servizi esterni da cui dipende.

Se sono presenti arresti anomali del sistema, errori o problemi di prestazioni, è possibile cercare i dati di telemetria in dettaglio per diagnosticare la causa. Il servizio invia anche messaggi di posta elettronica in caso di variazioni nella disponibilità e nelle prestazioni dell'app. Application Insights diventa quindi uno strumento utile per la sicurezza perché contribuisce alla disponibilità nell'ambito della triade di sicurezza: riservatezza, integrità e disponibilità.

### <a name="azure-monitor"></a>Monitoraggio di Azure

[Monitoraggio di Azure](/azure/monitoring-and-diagnostics/) offre visualizzazione, query, routing, avvisi, ridimensionamento automatico e automazione dei dati sia dalla sottoscrizione di Azure ([log attività](../../azure-monitor/essentials/platform-logs-overview.md)) che da ogni singola risorsa di Azure (log di[risorse](../../azure-monitor/essentials/platform-logs-overview.md)). È possibile usare Monitoraggio di Azure per ricevere avvisi sugli eventi relativi alla sicurezza generati nei log di Azure.

### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

[Log di monitoraggio di Azure](../../azure-monitor/logs/log-query-overview.md) : offre una soluzione di gestione IT per l'infrastruttura locale e basata sul cloud di terze parti, ad esempio AWS, oltre alle risorse di Azure. I dati di monitoraggio di Azure possono essere indirizzati direttamente ai log di monitoraggio di Azure, in modo da poter visualizzare le metriche e i log dell'intero ambiente in un'unica posizione.

I log di monitoraggio di Azure possono essere uno strumento utile per analisi forensi e altre analisi della sicurezza, in quanto lo strumento consente di eseguire rapidamente ricerche in grandi quantità di voci correlate alla sicurezza con un approccio di query flessibile. Inoltre, [i log del proxy e del firewall locali possono essere esportati in Azure e resi disponibili per l'analisi usando i log di monitoraggio di Azure.](../../azure-monitor/agents/agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../../advisor/advisor-overview.md) è un servizio di consulenza personalizzato per il cloud che consente di ottimizzare le distribuzioni di Azure. Analizza la configurazione e la telemetria delle risorse Sono quindi consigliate soluzioni che consentono di migliorare le [prestazioni](../../advisor/advisor-performance-recommendations.md), la [sicurezza](../../advisor/advisor-security-recommendations.md)e l' [affidabilità](../../advisor/advisor-high-availability-recommendations.md) delle risorse, cercando al tempo stesso la possibilità di [ridurre la spesa complessiva di Azure](../../advisor/advisor-cost-recommendations.md). Azure Advisor offre consigli che possono migliorare notevolmente lo stato di sicurezza complessivo delle soluzioni distribuite in Azure. Tali raccomandazioni vengono ricavate dall'analisi della sicurezza eseguita dal [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).

## <a name="applications"></a>Applicazioni

Questa sezione contiene informazioni aggiuntive sulle caratteristiche principali per la sicurezza delle applicazioni e informazioni di riepilogo su tali funzionalità.

### <a name="web-application-vulnerability-scanning"></a>Analisi delle vulnerabilità delle applicazioni Web

Uno dei modi più semplici per iniziare a testare le vulnerabilità di un'[app del servizio app](../../app-service/overview.md) consiste nell'usare l'[integrazione con Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) per eseguire sull'app un'analisi delle vulnerabilità con un solo clic. È possibile visualizzare i risultati del test in un report di facile comprensione e imparare a risolvere ogni vulnerabilità con istruzioni dettagliate.

### <a name="penetration-testing"></a>Test di penetrazione

I test di [penetrazione](./pen-testing.md) dell'applicazione non vengono eseguiti per l'utente, ma è importante comprendere che si vuole ed è necessario eseguire il test delle applicazioni. Questo è un aspetto positivo, perché quando si migliora la sicurezza delle applicazioni, è possibile rendere più sicuro l'intero ecosistema di Azure. Quando si invia una notifica a Microsoft delle attività di test di Pen, i clienti devono comunque rispettare le [regole di test di penetrazione Microsoft Cloud di engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement).

### <a name="web-application-firewall"></a>Web application firewall

Il Web application firewall (WAF) del [gateway applicazione di Azure](../../application-gateway/features.md#web-application-firewall) consente di proteggere le applicazioni Web da attacchi basati sul Web comuni come SQL injection, cross-site scripting e hijack della sessione. Include la protezione preconfigurata dalle minacce identificate da [Open Web Application Security Project (OWASP) come le 10 principali vulnerabilità comuni](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticazione e autorizzazione nel servizio app di Azure

La funzionalità di [autenticazione/autorizzazione del servizio app](../../app-service/overview-authentication-authorization.md) consente all'applicazione di eseguire la procedura di accesso degli utenti in modo che non sia necessario modificare il codice nel back-end dell'app. Fornisce un modo semplice per proteggere l'applicazione e utilizzare dati per-utente.

### <a name="layered-security-architecture"></a>Architettura di sicurezza su più livelli

Dato che gli [ambienti del servizio app](../../app-service/environment/app-service-app-service-environment-intro.md) forniscono un ambiente di runtime isolato distribuito in una [rete virtuale di Azure](../../virtual-network/virtual-networks-overview.md), gli sviluppatori possono creare un'architettura di sicurezza su più livelli offrendo livelli diversi di accesso alla rete per ogni livello applicazione. Un'esigenza comune è quella di nascondere i back-end delle API all'accesso a Internet generale e consentire alle API di essere chiamate solo dalle app Web upstream. I [gruppi di sicurezza di rete (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) possono essere usati nelle subnet delle reti virtuali di Azure contenenti ambienti del servizio app per limitare l'accesso pubblico alle applicazioni API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnostica del server Web e diagnostica applicazioni
Le [app Web del servizio app](../../app-service/troubleshoot-diagnostic-logs.md) forniscono funzionalità di diagnostica per la registrazione di informazioni sia dal server Web sia dall'applicazione Web. logicamente separate in diagnostica del server Web e diagnostica dell'applicazione. Il server Web include due importanti progressi per la diagnosi e la risoluzione dei problemi di siti e applicazioni.

La prima nuova funzionalità è rappresentata dalle informazioni sullo stato in tempo reale per pool di applicazioni, processi di lavoro, siti, domini applicazione e richieste in esecuzione. Il secondo nuovo vantaggio risiede negli eventi di traccia dettagliati che tengono traccia di una richiesta nell'intero processo di richiesta e risposta.

Per abilitare la raccolta di tali eventi di traccia, è possibile configurare IIS 7 per l'acquisizione automatica di log di traccia completi in formato XML per specifiche richieste, in base al tempo trascorso o ai codici di risposta di errore.

## <a name="storage"></a>Archiviazione
Questa sezione contiene informazioni aggiuntive sulle caratteristiche principali per la sicurezza delle risorse di archiviazione di Azure e informazioni di riepilogo su tali funzionalità.

### <a name="azure-role-based-access-control-azure-rbac"></a>Controllo degli accessi in base al ruolo di Azure
È possibile proteggere l'account di archiviazione con il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md). Per le organizzazioni che desiderano applicare criteri di sicurezza per l'accesso ai dati è fondamentale limitare l'accesso in base ai principi di sicurezza di cui è [necessario essere a conoscenza](https://en.wikipedia.org/wiki/Need_to_know) e ai [privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege) . Questi diritti di accesso vengono concessi assegnando il ruolo di Azure appropriato a gruppi e applicazioni in un determinato ambito. È possibile usare i [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md), ad esempio collaboratore account di archiviazione, per assegnare privilegi agli utenti. L'accesso alle chiavi di archiviazione per un account di archiviazione con il modello di [Azure Resource Manager](../../storage/blobs/security-recommendations.md#data-protection) può essere controllato tramite il controllo degli accessi in base al ruolo.

### <a name="shared-access-signature"></a>Firma di accesso condiviso
Una [firma di accesso condiviso (SAS)](../../storage/common/storage-sas-overview.md) fornisce accesso delegato alle risorse nell'account di archiviazione. La firma di accesso condiviso consente di concedere a un client autorizzazioni limitate per gli oggetti nell'account di archiviazione per un periodo di tempo e con un set di autorizzazioni specificati. È possibile concedere queste autorizzazioni limitate senza la necessità di condividere le chiavi di accesso all'account.

### <a name="encryption-in-transit"></a>Crittografia in transito
La crittografia in transito è un meccanismo di protezione dei dati durante la trasmissione tra le reti. Con Archiviazione di Azure è possibile proteggere i dati con:
- [Crittografia a livello di trasporto](../../storage/blobs/security-recommendations.md), ad esempio HTTPS quando si trasferiscono dati all'interno o all'esterno di archiviazione di Azure.

- [Crittografia di rete](../../storage/blobs/security-recommendations.md), ad esempio la [crittografia SMB 3,0](../../storage/blobs/security-recommendations.md) per le [condivisioni file di Azure](../../storage/files/storage-dotnet-how-to-use-files.md).

- Crittografia lato client, per crittografare i dati prima che vengano trasferiti nella risorsa di archiviazione e decrittografarli dopo il trasferimento da tale risorsa.

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi
Per molte organizzazioni, la crittografia dei dati inattivi è un passaggio obbligatorio per assicurare la privacy dei dati, la conformità e la sovranità dei dati. Esistono tre funzionalità di sicurezza delle risorse di archiviazione di Azure che consentono di crittografare dati inattivi:

- [Crittografia del servizio di archiviazione](../../storage/common/storage-service-encryption.md) consente di richiedere che il servizio di archiviazione crittografi automaticamente i dati durante la scrittura in Archiviazione di Azure.

- La [crittografia lato client](../../storage/common/storage-client-side-encryption.md) fornisce anche la funzionalità di crittografia dei componenti inattivi.

- [Crittografia dischi di Azure](./azure-disk-encryption-vms-vmss.md) consente di crittografare i dischi dati e del sistema operativo usati da una macchina virtuale IaaS.

### <a name="storage-analytics"></a>di Analisi archiviazione

[Analisi archiviazione di Azure](/rest/api/storageservices/fileservices/storage-analytics) esegue la registrazione e fornisce i dati di metrica per un account di archiviazione. È possibile utilizzare questi dati per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione. Analisi archiviazione registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo. Vengono registrati i tipi di richieste autenticate seguenti:

- Richieste riuscite
- Richieste non riuscite, tra cui timeout, limitazione, rete, autorizzazione e altri errori
- Richieste tramite una firma di accesso condiviso, incluse le richieste riuscite e non riuscite
- Richieste ai dati di analisi

### <a name="enabling-browser-based-clients-using-cors"></a>Abilitazione dei client basati su browser con CORS

[Condivisione risorse tra le origini (CORS)](/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) è un meccanismo che consente ai domini di concedersi reciprocamente l'autorizzazione ad accedere alle rispettive risorse. L'agente utente invia intestazioni aggiuntive affinché al codice JavaScript caricato da un determinato dominio venga consentito l'accesso alle risorse che si trovano in un altro dominio. Questo secondo dominio risponde quindi con intestazioni aggiuntive che consentono o negano al dominio originale l'accesso alle risorse.

Dato che i servizi di archiviazione di Azure supportano ora CORS, una volta impostate le regole CORS per il servizio, una richiesta correttamente autenticata inviata al servizio da un diverso dominio verrà valutata per determinare se è consentita in base alle regole che sono state specificate.

## <a name="networking"></a>Rete

Questa sezione contiene informazioni aggiuntive sulle caratteristiche principali per la sicurezza di rete di Azure e informazioni di riepilogo su tali funzionalità.

### <a name="network-layer-controls"></a>Controlli a livello rete

Il controllo di accesso alla rete comporta la limitazione della connettività da e verso subnet o dispositivi specifici e rappresenta la base della sicurezza di rete. L'obiettivo del controllo di accesso alla rete consiste nel garantire che le macchine virtuali e i servizi siano accessibili solo agli utenti e ai dispositivi a cui si vuole concedere l'accesso.

#### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Un [gruppo di sicurezza di rete (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md#security) è un firewall di base per il filtro di pacchetti con stato che consente di controllare l'accesso in base a una tupla con 5 tuple. Gli NSG non forniscono ispezione a livello dell'applicazione o controlli di accesso autenticato. Possono essere usati per controllare il traffico tra subnet all'interno di una rete virtuale di Azure e il traffico tra una rete virtuale di Azure e Internet.

#### <a name="route-control-and-forced-tunneling"></a>Controllo di route e tunneling forzato

La possibilità di controllare il comportamento di routing nella rete virtuale di Azure è una funzionalità critica per il controllo di accesso e la sicurezza di rete. Se si vuole essere certi che tutto il traffico da e verso la rete virtuale di Azure passi attraverso una determinata appliance di sicurezza virtuale, ad esempio, è necessario poter controllare e personalizzare il comportamento di routing. A tale scopo è possibile configurare route definite dall'utente in Azure.

Le [route definite dall'utente](../../virtual-network/virtual-networks-udr-overview.md#custom-routes) consentono di personalizzare i percorsi in ingresso e in uscita del traffico da e verso singole macchine virtuali o subnet per garantire la route più sicura possibile. Il [tunneling forzato](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) è un meccanismo che è possibile usare per garantire che i servizi non siano autorizzati a avviare una connessione ai dispositivi su Internet.

Questo approccio è diverso dalla possibilità di accettare connessioni in ingresso e quindi rispondere. I server Web front-end devono rispondere alle richieste dagli host in Internet e di conseguenza il traffico originato da Internet è consentito in ingresso verso questi server Web, che sono autorizzati a rispondere.

Il tunneling forzato viene in genere usato per imporre al traffico in uscita verso Internet di passare attraverso firewall e proxy di sicurezza locali.

#### <a name="virtual-network-security-appliances"></a>Appliance di sicurezza di rete virtuale

Nonostante i gruppi di sicurezza di rete, le route definite dall'utente e il tunneling forzato offrano un certo grado di sicurezza ai livelli di rete e trasporto del [modello OSI](https://en.wikipedia.org/wiki/OSI_model), a volte può essere consigliabile abilitare la sicurezza a livelli superiori dello stack. È possibile accedere a queste funzionalità di sicurezza di rete avanzate usando una soluzione di appliance di sicurezza di rete dei partner di Azure. Per trovare le soluzioni di sicurezza di rete dei partner Azure più aggiornate, visitare [Azure Marketplace](https://azure.microsoft.com/marketplace/) e cercare "sicurezza" e "sicurezza di rete".

### <a name="azure-virtual-network"></a>Rete virtuale di Azure

Una rete virtuale di Azure (VNet) è una rappresentazione della propria rete personalizzata nel cloud. È un isolamento logico dell'infrastruttura di rete di Azure dedicato alla sottoscrizione. È possibile controllare completamente i blocchi di indirizzi IP, le impostazioni DNS, i criteri di sicurezza e le tabelle di route in questa rete. La rete virtuale può essere segmentata in subnet e si possono inserire macchine virtuali (VM) IaaS di Azure e/o [servizi cloud (istanze del ruolo PaaS)](../../cloud-services/cloud-services-choose-me.md) nelle reti virtuali di Azure.

È anche possibile connettere la rete virtuale alla rete locale usando una delle [opzioni di connettività](../../vpn-gateway/index.yml) disponibili in Azure. In pratica è possibile espandere la rete ad Azure, con il controllo completo sui blocchi di indirizzi IP con tutti i vantaggi di livello aziendale offerti da Azure.

La rete di Azure supporta vari scenari di accesso remoto sicuro, tra cui:

- [Connettere singole workstation a una rete virtuale di Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

- [Connettere la rete locale a una rete virtuale di Azure con una VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Connettere la rete locale a una rete virtuale di Azure con un collegamento WAN dedicato](../../expressroute/expressroute-introduction.md)

- [Connettere tra loro reti virtuali di Azure](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="azure-private-link"></a>Collegamento privato di Azure

Il [collegamento privato di Azure](https://azure.microsoft.com/services/private-link/) consente di accedere ai servizi PaaS di Azure, ad esempio archiviazione di Azure e database SQL, e ai servizi di proprietà del cliente/partner ospitati di Azure privatamente nella rete virtuale tramite un [endpoint privato](https://docs.microsoft.com/azure/private-link/private-endpoint-overview). Collegamento privato di Azure offre un'esperienza coerente di configurazione e utilizzo per i servizi PaaS di Azure, i servizi di proprietà dei clienti e quelli condivisi dei partner. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure.

Gli [endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) consentono di proteggere le risorse critiche dei servizi di Azure solo per le reti virtuali. L'endpoint privato di Azure usa un indirizzo IP privato della VNet per connettersi privatamente e in modo sicuro a un servizio basato su collegamento privato di Azure, portando il servizio nella VNet. L'esposizione della rete virtuale alla rete Internet pubblica non è più necessaria per l'utilizzo dei servizi in Azure. 

È anche possibile creare un servizio di collegamento privato nella rete virtuale. Il [servizio di collegamento privato di Azure](https://docs.microsoft.com/azure/private-link/private-link-service-overview) è il riferimento al servizio personalizzato che è basato sul collegamento privato di Azure. Il servizio in esecuzione dietro Azure Load Balancer Standard può essere abilitato per l'accesso ai collegamenti privati, in modo che i consumer del servizio possano accedervi privatamente dalle proprie reti virtuali. I clienti possono creare un endpoint privato all'interno della rete virtuale e associarlo a questo servizio. L'esposizione del servizio alla rete Internet pubblica non è più necessaria per il rendering dei servizi in Azure. 

### <a name="vpn-gateway"></a>Gateway VPN

Per inviare il traffico di rete tra la rete virtuale di Azure e il sito locale, è necessario creare un gateway VPN per la rete virtuale di Azure. Un [gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) è un tipo di gateway di rete virtuale che invia traffico crittografato tramite una connessione pubblica. È anche possibile usare i gateway VPN per inviare il traffico tra le reti virtuali di Azure tramite l'infrastruttura di rete di Azure.

### <a name="express-route"></a>Express Route

Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) è un collegamento WAN dedicato che consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata dedicata supportata da un provider di connettività.

![Express Route](./media/overview/azure-security-figure-1.png)

Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, ad esempio Microsoft Azure, Microsoft 365 e CRM online. La connettività può essere stabilita da una rete (IP VPN) any-to-any, da una rete Ethernet punto a punto o da una Cross Connection virtuale tramite un provider di connettività presso una struttura di condivisione del percorso.

Le connessioni ExpressRoute non usano la rete Internet pubblica e possono quindi essere considerate più sicure delle soluzioni basate su VPN. In questo modo possono offrire un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

### <a name="application-gateway"></a>Gateway applicazione

Il [gateway applicazione di Microsoft Azure](../../application-gateway/overview.md) offre un servizio di [controller per la distribuzione di applicazioni](https://en.wikipedia.org/wiki/Application_delivery_controller) con numerose funzionalità di bilanciamento del carico di livello 7 per l'applicazione.

![Gateway applicazione](./media/overview/azure-security-figure-2.png)

Consente di ottimizzare la produttività Web farm eseguendo l'offload della terminazione TLS a elevato utilizzo di CPU al gateway applicazione (noto anche come "offload TLS" o "bridging TLS"). Offre anche altre funzionalità di routing di livello 7, tra cui la distribuzione round robin del traffico in ingresso, l'affinità di sessione basata su cookie, il routing basato su percorso URL e la possibilità di ospitare più siti Web dietro un unico gateway applicazione. Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7.

Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale.

L'applicazione offre numerose funzionalità di controller per la distribuzione di applicazioni (ADC), tra cui bilanciamento del carico HTTP, affinità di sessione basata su cookie, [offload TLS](../../web-application-firewall/ag/tutorial-restrict-web-traffic-powershell.md), Probe di integrità personalizzati, supporto per più siti e molti altri.

### <a name="web-application-firewall"></a>Web application firewall

Web application firewall è una funzionalità del [gateway applicazione di Azure](../../application-gateway/overview.md) che consente di proteggere le applicazioni Web che usano il gateway applicazione per funzioni standard di controller per la distribuzione di applicazioni. Web application firewall protegge infatti le applicazioni dalla maggior parte delle 10 vulnerabilità Web OWASP più diffuse.

![Web application firewall](./media/overview/azure-security-figure-3.png)

- Protezione dagli attacchi SQL injection

- Protezione dai comuni attacchi Web, ad esempio attacchi di iniezione di comandi, richieste HTTP non valide, attacchi HTTP Response Splitting e Remote File Inclusion

- Protezione dalle violazioni del protocollo HTTP

- Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header

- Prevenzione contro robot, crawler e scanner

- Rilevamento di errori di configurazione dell'applicazione comuni (ad esempio, Apache, IIS e così via)

L'uso di un Web application firewall centralizzato per la protezione dagli attacchi Web semplifica notevolmente la gestione della sicurezza e offre all'applicazione migliori garanzie contro le minacce di intrusione. Una soluzione WAF è anche in grado di reagire più velocemente a una minaccia alla sicurezza tramite l'applicazione di patch su una vulnerabilità nota in una posizione centrale, anziché proteggere ogni singola applicazione Web. È possibile convertire facilmente i gateway applicazione esistenti in gateway applicazione con Web application firewall.

### <a name="traffic-manager"></a>Gestione traffico

[Gestione traffico di Microsoft Azure](../../traffic-manager/traffic-manager-overview.md) consente di controllare la distribuzione del traffico utente per gli endpoint di servizio in diversi data center. Gli endpoint di servizio supportati da Gestione traffico includono servizi cloud, app Web e VM di Azure. È anche possibile usare Gestione traffico con endpoint esterni, non di Azure. Gestione traffico usa il sistema DNS (Domain Name System) per indirizzare le richieste del client all'endpoint più appropriato in base a un [metodo di routing del traffico](../../traffic-manager/traffic-manager-routing-methods.md) e all'integrità degli endpoint.

Gestione traffico offre diversi metodi di routing del traffico per soddisfare le diverse esigenze delle applicazioni. Offre inoltre [monitoraggio](../../traffic-manager/traffic-manager-monitoring.md) dell'integrità degli endpoint e failover automatico. Gestione traffico è resiliente agli errori, incluso l'errore di un'intera area di Azure.

### <a name="azure-load-balancer"></a>Azure Load Balancer

[Azure Load Balancer](../../load-balancer/load-balancer-overview.md) offre elevati livelli di disponibilità e prestazioni di rete per le applicazioni. Si tratta di un servizio di bilanciamento del carico di livello 4 (TCP, UDP) che distribuisce il traffico in ingresso tra istanze integre di servizi definiti in un set con carico bilanciato. Azure Load Balancer può essere configurato per:

- Bilanciare il carico del traffico Internet in ingresso nelle macchine virtuali. Questa configurazione è nota come [bilanciamento del carico pubblico](../../load-balancer/components.md#frontend-ip-configurations).

- Bilanciare il carico del traffico tra macchine virtuali in una rete virtuale, tra macchine virtuali nei servizi cloud o tra computer locali e macchine virtuali in una rete virtuale cross-premise. Questa configurazione è nota come [bilanciamento del carico interno](../../load-balancer/components.md#frontend-ip-configurations).

- Inoltrare il traffico esterno a una macchina virtuale specifica

### <a name="internal-dns"></a>DNS interno

È possibile gestire l'elenco dei server DNS usati in una rete virtuale nel portale di gestione o nel file di configurazione di rete. Il cliente può aggiungere fino a 12 server DNS per ogni rete virtuale. Quando si specificano i server DNS, è importante verificare che i server DNS del cliente siano elencati nell'ordine corretto per l'ambiente specifico. Gli elenchi dei server DNS non supportano il round robin. Vengono usati nell'ordine in cui sono specificati. Se il primo server DNS nell'elenco è raggiungibile, il client usa tale server DNS indipendentemente dal fatto che funzioni correttamente o meno. Per modificare l'ordine dei server DNS per la rete virtuale del cliente, rimuovere i server DNS dall'elenco e aggiungerli nuovamente nell'ordine desiderato. Il sistema DNS supporta l'aspetto della disponibilità della triade di sicurezza "riservatezza, integrità e disponibilità".

### <a name="azure-dns"></a>DNS di Azure

Il nome DNS (Domain Name System) è responsabile della conversione (o risoluzione) del nome di un sito Web o del servizio nel relativo indirizzo IP. [DNS di Azure](../../dns/dns-overview.md) è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure. Il sistema DNS supporta l'aspetto della disponibilità della triade di sicurezza "riservatezza, integrità e disponibilità".

### <a name="azure-monitor-logs-nsgs"></a>Log di monitoraggio di Azure gruppi

È possibile abilitare le seguenti categorie di log di diagnostica per i gruppi di sicurezza di rete:

- Evento: contiene voci relative alle regole dei gruppi di sicurezza di rete applicate alle VM e ai ruoli delle istanze in base all'indirizzo MAC. Lo stato di queste regole viene raccolto ogni 60 secondi.

- Contatore di regole: contiene voci che indicano quante volte ogni regola dei gruppi di sicurezza di rete viene applicata per rifiutare o consentire il traffico.

### <a name="security-center"></a>Centro sicurezza

Il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md) analizza continuamente lo stato di sicurezza delle risorse di Azure per le procedure consigliate per la sicurezza della rete. Quando il Centro sicurezza identifica le potenziali vulnerabilità di sicurezza, vengono creati [consigli](../../security-center/security-center-recommendations.md) che guidano l'utente nel processo di configurazione dei controlli necessari per rafforzare e proteggere le risorse.

## <a name="compute"></a>Calcolo
Questa sezione contiene informazioni aggiuntive sulle caratteristiche principali in quest'area e informazioni di riepilogo su tali funzionalità.

### <a name="antimalware--antivirus"></a>Antimalware e antivirus
Con Azure IaaS è possibile usare software antimalware di fornitori di soluzioni di sicurezza come Microsoft, Symantec, Trend Micro, McAfee e Kaspersky per proteggere le macchine virtuali da file dannosi, adware e altre minacce. [Microsoft Antimalware](antimalware.md) per Servizi cloud e Macchine virtuali di Azure è una funzionalità di protezione che consente di identificare e rimuovere virus, spyware e altro software dannoso. Microsoft Antimalware offre avvisi configurabili quando software dannoso o indesiderato tenta l'installazione o l'esecuzione nei sistemi di Azure. Microsoft Antimalware può anche essere distribuito usando il Centro sicurezza di Azure.

### <a name="hardware-security-module"></a>Modulo di protezione hardware
La crittografia e l'autenticazione non migliorano la sicurezza a meno che le chiavi stesse non siano protette. È possibile semplificare la gestione e la sicurezza dei segreti e delle chiavi critiche archiviando questi elementi in [Azure Key Vault](../../key-vault/general/overview.md). Key Vault consente di archiviare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 livello 2. Le chiavi di crittografia di SQL Server per backup o [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) possono essere tutte archiviate nell'insieme di credenziali delle chiavi con qualsiasi chiave o segreto delle applicazioni. Le autorizzazioni e l'accesso per questi elementi protetti vengono gestiti tramite [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Backup di una macchina virtuale
[Backup di Azure](../../backup/backup-overview.md) è una soluzione che protegge i dati delle applicazioni senza investimenti di capitale e con costi operativi minimi. Gli errori delle applicazioni possono danneggiare i dati e gli errori umani possono introdurre nelle applicazioni bug che potrebbero causare problemi di sicurezza. Con Backup di Azure, le macchine virtuali che eseguono Windows e Linux sono protette.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Una parte importante della [strategia di continuità aziendale e ripristino di emergenza (BCDR)](../../best-practices-availability-paired-regions.md) dell'organizzazione consiste nel capire come mantenere operativi i carichi di lavoro e le app aziendali quando si verificano interruzioni pianificate e non pianificate. [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) consente di orchestrare la replica, il failover e il ripristino di carichi di lavoro e app in modo che siano disponibili da una località secondaria in caso di inattività di quella primaria.

### <a name="sql-vm-tde"></a>TDE in VM SQL
Transparent Data Encryption (TDE) e la crittografia a livello di colonna sono funzionalità di crittografia di SQL Server che richiedono ai clienti di gestire e archiviare le chiavi crittografiche usate per la crittografia.

Il servizio dell'insieme di credenziali delle chiavi di Azure (AKV) è progettato per migliorare la sicurezza e la gestione di queste chiavi in una posizione sicura e a elevata disponibilità. Il connettore SQL Server consente a SQL Server di usare queste chiavi da Azure Key Vault.

Se si esegue SQL Server con computer locali, è possibile seguire la procedura per accedere Azure Key Vault dall'istanza di SQL Server locale. Se si esegue SQL Server in VM di Azure, invece, è possibile risparmiare tempo usando la funzionalità Integrazione di Azure Key Vault. Con alcuni cmdlet di Azure PowerShell che abilitano questa funzionalità, è possibile automatizzare la configurazione necessaria per l'accesso all'insieme di credenziali delle chiavi di una macchina virtuale di SQL.

### <a name="vm-disk-encryption"></a>Crittografia dei dischi delle VM
[Crittografia dischi di Azure](./azure-disk-encryption-vms-vmss.md) è una nuova funzionalità che consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Applica la funzionalità standard di settore BitLocker di Windows e la funzionalità DM-Crypt di Linux per offrire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione è integrata con Azure Key Vault per consentire il controllo e la gestione dei segreti e delle chiavi di crittografia dei dischi nella sottoscrizione di Key Vault. Questa soluzione assicura anche che tutti i dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure.

### <a name="virtual-networking"></a>Reti virtuali
La connettività di rete è indispensabile per le macchine virtuali. Per supportare questo requisito, Azure richiede che le macchine virtuali siano connesse a una rete virtuale di Azure. Una rete virtuale di Azure è un costrutto logico basato sull'infrastruttura di rete fisica di Azure. Ogni [rete virtuale di Azure](../../virtual-network/virtual-networks-overview.md) logica è isolata da tutte le altre reti virtuali di Azure. L'isolamento garantisce che il traffico di rete nelle distribuzioni di un utente non sia accessibile da altri clienti di Microsoft Azure.

### <a name="patch-updates"></a>Patch di aggiornamento
Le patch di aggiornamento offrono la base per trovare e correggere potenziali problemi e semplificare il processo di gestione degli aggiornamenti software, sia riducendo il numero di aggiornamenti software che è necessario distribuire nell'organizzazione, sia migliorando la capacità di monitorare la conformità.

### <a name="security-policy-management-and-reporting"></a>Gestione e reporting dei criteri di sicurezza
Il [Centro sicurezza](../../security-center/security-center-introduction.md) consente di prevenire, rilevare e rispondere alle minacce e offre un livello maggiore di visibilità e controllo della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri nelle sottoscrizioni di Azure, consente di rilevare minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso
I controlli di accesso basati sull'identità sono il punto di partenza della protezione di sistemi, applicazioni e dati. Le funzionalità di gestione delle identità e dell'accesso incorporate nei prodotti e nei servizi aziendali Microsoft consentono di proteggere le informazioni personali e dell'organizzazione dall'accesso non autorizzato rendendole al tempo stesso disponibili per gli utenti legittimi ovunque e in qualsiasi momento ne abbiano bisogno.

### <a name="secure-identity"></a>Proteggere l'identità
Per gestire le identità e l'accesso, Microsoft usa più procedure e tecnologie di sicurezza nei propri prodotti e servizi.

- [Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) richiede agli utenti di usare più metodi di accesso, in locale e nel cloud. Offre autenticazione avanzata con diverse facili opzioni di verifica, garantendo al tempo stesso agli utenti un processo di accesso semplice.

- [Microsoft Authenticator](https://aka.ms/authenticator) semplifica l'uso di Multi-Factor Authentication, è applicabile ad account sia Microsoft che Microsoft Azure Active Directory e include il supporto per dispositivi indossabili e approvazioni basate sull'impronta digitale.

- L'[applicazione di criteri password](../../active-directory/authentication/concept-sspr-policy.md) aumenta la sicurezza delle password tradizionali imponendo requisiti di lunghezza e complessità, la rotazione periodica forzata e il blocco account dopo tentativi di autenticazione non riusciti.

- L'[autenticazione basata su token](../../active-directory/develop/authentication-vs-authorization.md) consente l'autenticazione tramite Azure Active Directory.

- Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/built-in-roles.md) consente di concedere l'accesso in base al ruolo assegnato all'utente, semplificando la concessione agli utenti solo della quantità di accesso necessaria per svolgere le proprie mansioni. Puoi personalizzare il controllo degli accessi in base al ruolo di Azure per il modello aziendale e la tolleranza al rischio.

- La [soluzione ibrida (integrata) di gestione delle identità](../../active-directory/hybrid/plan-hybrid-identity-design-considerations-overview.md) consente di mantenere il controllo sull'accesso degli utenti nei data center interni e nelle piattaforme cloud creando una singola identità utente per l'autenticazione e l'autorizzazione in tutte le risorse.

### <a name="secure-apps-and-data"></a>Proteggere app e dati
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) è una soluzione cloud completa per la gestione delle identità e dell'accesso che consente di proteggere l'accesso ai dati nelle applicazioni in locale e nel cloud e semplifica la gestione di gruppi e utenti. Combina servizi directory di base, governance avanzata delle identità, sicurezza e gestione dell'accesso alle applicazioni e consente agli sviluppatori di incorporare facilmente la gestione delle identità basata su criteri nelle proprie app. Per migliorare Azure Active Directory, è possibile aggiungere funzionalità a pagamento mediante Azure Active Directory Basic Edition, Premium P1 Edition e Premium P2 Edition.

| Funzionalità comuni/gratuite     | Funzionalità Basic    |Funzionalità Premium P1 |Funzionalità Premium P2 | Aggiunta ad Azure Active Directory - Solo funzionalità correlate a Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Oggetti directory](../../active-directory/fundamentals/active-directory-whatis.md),    [gestione di utenti/gruppi (aggiunta/aggiornamento/eliminazione)/provisioning basato sull'utente, registrazione del dispositivo](../../active-directory/fundamentals/active-directory-whatis.md),  [Single Sign-On (SSO)](../../active-directory/fundamentals/active-directory-whatis.md),     [modifica delle password self-service per gli utenti cloud](../../active-directory/fundamentals/active-directory-whatis.md),     [Connect (motore di sincronizzazione che estende le directory locali per Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md),     [report di sicurezza/utilizzo](../../active-directory/fundamentals/active-directory-whatis.md)       |  [Gestione/provisioning degli accessi in base al gruppo](../../active-directory/fundamentals/active-directory-whatis.md), [reimpostazione della password self-service per gli utenti cloud](../../active-directory/fundamentals/active-directory-whatis.md), informazioni personalizzate distintive dell'  [azienda (pagine di accesso/personalizzazione del pannello di accesso)](../../active-directory/fundamentals/active-directory-whatis.md),    [proxy dell'applicazione](../../active-directory/fundamentals/active-directory-whatis.md),    [SLA 99,9%](../../active-directory/fundamentals/active-directory-whatis.md) |  [Gestione self-service dei gruppi e delle app/aggiunta self-service di applicazioni/gruppi dinamici](../../active-directory/fundamentals/active-directory-whatis.md),   [reimpostazione della password self-service/modifica/sblocco con writeback locale](../../active-directory/fundamentals/active-directory-whatis.md),    [multi-factor authentication (cloud e locale (server](../../active-directory/fundamentals/active-directory-whatis.md)multi-factor authentication), [Cal MIM + server Mim](../../active-directory/fundamentals/active-directory-whatis.md),     [cloud app Discovery](../../active-directory/fundamentals/active-directory-whatis.md),  [Connect Health](../../active-directory/fundamentals/active-directory-whatis.md),   [rollover automatico delle password per gli account di gruppo](../../active-directory/fundamentals/active-directory-whatis.md)|   [Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md),  [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Aggiungere un dispositivo a Azure ad, SSO desktop, Microsoft Passport per Azure ad, ripristino BitLocker con privilegi di amministratore](../../active-directory/fundamentals/active-directory-whatis.md),    [registrazione automatica MDM, ripristino Self-Service BitLocker, amministratori locali aggiuntivi per dispositivi Windows 10 tramite Azure ad join](../../active-directory/fundamentals/active-directory-whatis.md)|

- [Cloud App Discovery](/cloud-app-security/set-up-cloud-discovery) è una funzionalità Premium di Azure Active Directory che consente di identificare le applicazioni cloud usate dai dipendenti dell'organizzazione.

- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) è un servizio di sicurezza che usa Azure Active Directory funzionalità di rilevamento delle anomalie per offrire una visualizzazione consolidata dei rilevamenti dei rischi e potenziali vulnerabilità che potrebbero influire sulle identità dell'organizzazione.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) consente di aggiungere VM di Azure a un dominio senza che sia necessario distribuire controller di dominio. Gli utenti accedono a queste VM usando le credenziali aziendali di Active Directory e possono accedere alle risorse con facilità.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) è un servizio di gestione delle identità globale a disponibilità elevata per app rivolte agli utenti, scalabile fino a centinaia di milioni di identità e integrabile con piattaforme Web e per dispositivi mobili. I clienti possono accedere a tutte le applicazioni tramite esperienze personalizzabili con account dei social media esistenti oppure con creando nuove credenziali autonome.

- [Collaborazione B2B di Azure Active Directory](../../active-directory/external-identities/what-is-b2b.md) è una soluzione di integrazione dei partner che supporta le relazioni tra aziende consentendo ai partner di accedere in modo selettivo alle applicazioni e ai dati aziendali usando le proprie identità autogestite.

- [Azure Active Directory aggiunto](../../active-directory/devices/overview.md) consente di estendere le funzionalità del cloud ai dispositivi Windows 10 per la gestione centralizzata. Gli utenti possono così connettersi al cloud dell'azienda o dell'organizzazione tramite Azure Active Directory usufruendo di un accesso semplificato alle app e alle risorse.

- Il [proxy di applicazione di Azure Active Directory](../../active-directory/manage-apps/application-proxy.md) fornisce l'accesso remoto sicuro e SSO per le applicazioni Web ospitate in locale.

## <a name="next-steps"></a>Passaggi successivi

- Comprendere la [responsabilità condivisa nel cloud](shared-responsibility.md).

- Scopri in che modo il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md) ti aiuta a prevenire, rilevare e rispondere alle minacce con maggiore visibilità e controllo sulla sicurezza delle risorse di Azure.
