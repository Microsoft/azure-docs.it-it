---
title: includere file
description: includere file
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 234407e6973657ba3ad0d78189e7cb1d363c15e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95554530"
---
L'Internet of Things (IoT) pone numerose difficoltà in termini di sicurezza, privacy e conformità per le aziende di tutto il mondo. A differenza delle tecnologie informatiche tradizionali in cui questi problemi riguardano il software e la relativa implementazione, l'IoT riguarda le sfide poste dalla convergenza tra il mondo fisico e quello informatico. Per proteggere le soluzioni IoT, è necessario garantire il provisioning sicuro dei dispositivi, la connettività protetta tra questi dispositivi e il cloud e la protezione dei dati nel cloud durante l'elaborazione e l'archiviazione. A sfavore di queste funzionalità, tuttavia, giocano fattori quali dispositivi con risorse limitate, la distribuzione geografica delle implementazioni e un numero elevato di dispositivi all'interno di un'unica soluzione.

Questo articolo descrive come Solution Accelerator di IoT offre una soluzione cloud di Internet delle cose sicura e privata. Solution Accelerator offre una soluzione end-to-end completa con protezione integrata in ogni fase, dall'inizio alla fine. In Microsoft, lo sviluppo di software protetti è integrato nelle prassi di progettazione del software e trova origine nella decennale esperienza di sviluppo di software protetti. In quest'ambito, Security Development Lifecycle (SDL) costituisce la metodologia di sviluppo di base, associata a un host di servizi di sicurezza a livello di infrastruttura tra cui Operational Security Assurance (OSA) e Microsoft Digital Crimes Unit, nonché Microsoft Security Response Center e Microsoft Malware Protection Center.

Solution Accelerator offre eccezionali funzionalità che semplificano e rendono il provisioning, la connessione e l'archiviazione dei dati dei dispositivi IoT trasparenti e, soprattutto, sicuri. Questo articolo esamina le funzionalità di sicurezza e le strategie di distribuzione di Solution Accelerator di Azure IoT per garantire la risoluzione dei problemi relativi alla sicurezza, alla privacy e alla conformità.

## <a name="introduction"></a>Introduzione

L'Internet of Things (IoT) è la tendenza del futuro e offre alle aziende opportunità concrete e immediate per ridurre i costi, aumentare i ricavi e trasformare la propria attività. Molte aziende, tuttavia, esitano a distribuire IoT nelle proprie organizzazioni a causa dei problemi relativi alla sicurezza, alla privacy e alla conformità. Una delle principali preoccupazioni è data dalla peculiarità dell'infrastruttura IoT, che unisce il mondo fisico e il mondo informatico sommando i rischi di entrambi. La sicurezza IoT consiste nel garantire l'integrità del codice in esecuzione sui dispositivi, l'autenticazione di utenti e dispositivi, la definizione trasparente della proprietà dei dispositivi (nonché i dati generati da tali dispositivi) e la resistenza agli attacchi fisici e informatici.

È poi da considerare il problema della privacy. Le società richiedono trasparenza nella raccolta dei dati, nella tipologia e nel motivo per cui i dati sono raccolti, negli utenti che accedono ai dati e che controllano gli accessi, e così via. Infine, esistono problemi generali relativi alla sicurezza delle attrezzature e degli utenti che li usano, nonché problemi nella gestione della conformità agli standard di settore.

Considerati i problemi di sicurezza, privacy, trasparenza e conformità, scegliere il provider giusto per la soluzione IoT rappresenta una sfida. Unire singole parti di software IoT e servizi offerti da un'ampia gamma di fornitori causa lacune in termini di sicurezza, privacy, trasparenza e conformità che possono essere difficili da rilevare, e quindi da correggere. La scelta del provider giusto per il software e i servizi IoT si basa sulla ricerca di un provider di consolidata esperienza nella fornitura di servizi che variano in verticalizzazioni e aree geografiche, ma che allo stesso tempo sia in grado di ridurre in modo sicuro e trasparente questa tendenza. Analogamente, è opportuno che il provider scelto vanti diversi anni di esperienza nello sviluppo di applicazioni software protette in esecuzione su miliardi di computer nel mondo e che abbia la possibilità di valutare il panorama delle minacce posto da questo nuovo mondo dell'Internet of Things.

## <a name="secure-infrastructure-from-the-ground-up"></a>Proteggere l'infrastruttura fin dall'inizio

L'infrastruttura [Microsoft Cloud](https://azure.microsoft.com) supporta più di 1 miliardo clienti in 127 paesi/aree geografiche. Grazie alla decennale esperienza nella creazione di software aziendali e nell'offerta di alcuni dei servizi online più ampi al mondo, Microsoft Cloud è in grado di garantire livelli più elevati di sicurezza, privacy, conformità e procedure per la mitigazione delle minacce che la maggior parte dei clienti può raggiungere in modo autonomo.

Il [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/) fornisce una procedura di sviluppo aziendale obbligatoria che comprende i requisiti di protezione per l'intero ciclo di vita del software. Per garantire che le attività operative rispettino lo stesso livello di procedure di sicurezza, SDL ha sviluppato rigorose linee guida per la sicurezza, delineate nel processo Microsoft Operational Security Assurance (OSA). Microsoft collabora anche con società di controllo di terze parti per la verifica continua del rispetto degli obblighi di conformità e abbraccia il tema della sicurezza tramite la creazione di centri di eccellenza, tra cui Microsoft Digital Crimes Unit, Microsoft Security Response Center e Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure: protezione dell'infrastruttura IoT aziendale

Microsoft Azure offre una soluzione cloud completa, che combina una raccolta in continua crescita di servizi cloud integrati (analisi, machine learning, archiviazione, protezione, rete e web) con l'impegno, leader nel settore, verso la protezione e la privacy dei dati. La strategia di [presunta violazione](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) definita da Microsoft usa un *red team* dedicato, composto da esperti di sicurezza software che simulano attacchi, verificano la capacità di rilevamento di Azure, proteggono dalle minacce emergenti e ripristinano l'operatività a seguito di violazioni della sicurezza. Il team Microsoft per la [risposta globale agli eventi imprevisti](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) lavora giorno e notte per mitigare gli effetti di attacchi e attività dannose. Il team segue procedure stabilite per la gestione degli incidenti, delle comunicazioni e del ripristino e usa interfacce individuabili e prevedibili con partner interni ed esterni.

I sistemi Microsoft offrono prevenzione e rilevamento intrusione continui, prevenzione da attacchi ai servizi, test di penetrazione regolari e strumenti forensi che permettono di identificare e mitigare le minacce. [Multi-Factor Authentication](../articles/active-directory/authentication/concept-mfa-howitworks.md) fornisce un livello di sicurezza aggiuntivo per l'accesso alla rete da parte degli utenti finali. Per il provider dell'applicazione e dell'host, Microsoft offre controllo di accesso, monitoraggio, anti-malware, analisi delle vulnerabilità, patch e gestione della configurazione.

Solution Accelerator consente di sfruttare la sicurezza e la privacy integrate nella piattaforma di Azure insieme ai processi SDL e OSA per lo sviluppo e il funzionamento sicuro di tutti i software Microsoft. Queste procedure forniscono funzionalità di protezione dell'infrastruttura, protezione della rete, identità e gestione, essenziali per la sicurezza di qualsiasi soluzione.

L'[hub IoT di Azure](../articles/iot-hub/about-iot-hub.md), compreso in [Solution Accelerator di IoT](../articles/iot-fundamentals/iot-introduction.md), offre un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra i dispositivi IoT e i servizi di Azure, ad esempio [Azure Machine Learning](../articles/machine-learning/classic/index.yml) e [Analisi di flusso di Azure](../articles/stream-analytics/stream-analytics-introduction.md) usando credenziali di sicurezza e controllo di accesso specifici per ogni dispositivo.

Per descrivere al meglio le funzionalità di sicurezza e privacy incorporate in Solution Accelerator di Azure IoT, in questo articolo la soluzione è stata scomposta nelle tre principali aree di sicurezza.

![Solution Accelerator di Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Proteggere il provisioning dei dispositivi e l'autenticazione

Solution Accelerator protegge i dispositivi durante l'operatività, fornendo una chiave di identità univoca per ogni dispositivo, che l'infrastruttura IoT può usare per comunicare con il dispositivo mentre è in funzione. Il processo è rapido e semplice da configurare. La chiave generata con un ID dispositivo scelto dall'utente costituisce la base di un token usato in tutte le comunicazioni tra il dispositivo e l'hub IoT di Azure.

L'ID dispositivo può essere associato a un dispositivo durante la produzione (ad esempio all'interno del trust module dell'hardware) o può usare un'identità fissa esistente come proxy (ad esempio i numeri di serie della CPU). Poiché la modifica di queste informazioni di identificazione del dispositivo non è semplice, è importante usare ID di dispositivo logici nell'eventualità che l'hardware del dispositivo cambi, ma che il dispositivo logico rimanga lo stesso. In alcuni casi, l'associazione dell'identità di un dispositivo può verificarsi in fase di distribuzione del dispositivo (ad esempio, un ingegnere autenticato configura fisicamente un nuovo dispositivo durante la comunicazione con il back-end della soluzione). Il [Registro delle identità dell'hub IoT di Azure](../articles/iot-hub/iot-hub-devguide.md) offre l'archiviazione protetta delle identità e delle chiavi di protezione del dispositivo per una soluzione. Le identità dei dispositivi, singolarmente o in gruppo, possono essere aggiunte a un elenco Consenti o Blocca, favorendo il controllo completo dell'accesso al dispositivo.

I criteri di controllo di accesso dell'hub IoT di Azure nel cloud consentono l'attivazione e la disattivazione dell'identità di qualsiasi dispositivo, offrendo un modo per annullare l'associazione di un dispositivo da una distribuzione IoT quando necessario. Associazione e dissociazione dei dispositivi sono basate su ogni identità dispositivo.

Di seguito sono elencate funzionalità aggiuntive per la protezione dei dispositivi:

* I dispositivi non accettano connessioni di rete non richieste. I dispositivi stabiliscono tutte le connessioni e le route in modalità solo in uscita. Per ricevere un comando dal back-end, il dispositivo deve avviare una connessione per cercare eventuali comandi in sospeso da elaborare. Una volta stabilita una connessione sicura tra il dispositivo e l'hub IoT, le comunicazioni dal cloud al dispositivo e dal dispositivo al cloud possono essere inviate in modo trasparente.

* I dispositivi in genere stabiliscono una connessione o una route solo con i servizi noti con peering, ad esempio l'hub IoT di Azure.

* L'autorizzazione e l'autenticazione a livello di sistema usano le identità di ogni dispositivo; le credenziali e le autorizzazioni di accesso devono essere revocabili quasi immediatamente.

### <a name="secure-connectivity"></a>Proteggere la connettività

La durata delle comunicazioni è una caratteristica importante per qualsiasi soluzione IoT. La necessità di inviare comandi e/o ricevere dati dal dispositivo sul lungo termine è dovuta al fatto che i dispositivi IoT sono connessi a Internet o ad altre reti simili potenzialmente non affidabili. L'hub IoT di Azure offre resistenza delle comunicazioni tra cloud e dispositivi tramite un sistema di acknowledgment in risposta ai messaggi. Una resistenza aggiuntiva per la comunicazione avviene memorizzando i messaggi nell'hub IoT fino a sette giorni per i dati di telemetria e due giorni per i comandi.

L'efficienza è importante per garantire la conservazione delle risorse e l'operatività in un ambiente con risorse limitate. HTTPS (HTTP Secure), la versione sicura dello standard del settore per il protocollo http comune, è supportato dall'hub Iot di Azure, consentendo una comunicazione efficiente. I servizi Advanced Message Queuing Protocol (AMQP) e Message Queuing Telemetry Transport (MQTT), supportati dall'hub IoT di Azure, sono progettati non solo per una maggiore efficienza in termini di uso delle risorse, ma anche per il recapito affidabile dei messaggi. 

La scalabilità richiede la capacità di interagire in modo sicuro con una vasta gamma di dispositivi. L'hub IoT di Azure consente una connessione protetta ai dispositivi abilitati e non abilitati per IP. I dispositivi abilitati per IP sono in grado di connettersi direttamente e comunicare con l'hub IoT tramite una connessione protetta. I dispositivi non abilitati per IP non dispongono di risorse limitate e si connettono solo tramite protocolli di comunicazione a distanza breve, ad esempio Zwave, ZigBee e Bluetooth. Viene usato un gateway sul campo per aggregare tali dispositivi ed eseguire la conversione del protocollo, che abilita comunicazioni bidirezionali sicure con il cloud.

Di seguito sono elencate funzionalità aggiuntive per la protezione delle connessioni:

* Il percorso di comunicazione tra i dispositivi e l'hub IoT di Azure o tra i gateway e l'hub IoT di Azure è protetto tramite il protocollo Transport Layer Security (TLS) standard del settore con l'hub IoT di Azure autenticato tramite il protocollo X.509.

* Per proteggere i dispositivi da connessioni in ingresso non richieste, l'hub IoT di Azure non apre tutte le connessioni al dispositivo. Il dispositivo avvia tutte le connessioni.

* L'hub IoT di Azure archivia i messaggi per i dispositivi sul lungo termine e attende che il dispositivo si connetta. Questi comandi vengono archiviati per due giorni, consentendo ai dispositivi di connettersi sporadicamente per ricevere i comandi, a causa di problemi di alimentazione o connettività. L'hub IoT di Azure mantiene una coda specifica per dispositivo.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Proteggere l'elaborazione e l'archiviazione nel cloud

Dalle comunicazioni crittografate all'elaborazione dei dati nel cloud, Solution Accelerator aiuta a mantenere i dati sicuri. La suite fornisce flessibilità per implementare un livello aggiuntivo di crittografia e gestione delle chiavi di sicurezza.

Usando Azure Active Directory, ovvero AAD, per l'autenticazione e l'autorizzazione degli utenti, Solution Accelerator di Azure IoT è in grado di fornire un modello di autorizzazione basato su criteri per i dati nel cloud, favorendo una semplice gestione degli accessi che possono essere controllati e modificati. Questo modello consente anche la revoca quasi istantanea dell'accesso ai dati nel cloud e dei dispositivi connessi a Solution Accelerator di Azure IoT.

Una volta che i dati si trovano nel cloud, possono essere elaborati e archiviati in qualsiasi flusso di lavoro definito dall'utente. L'accesso a ogni parte dei dati viene controllato con Azure Active Directory, a seconda del servizio di archiviazione in uso.

Tutte le chiavi usate dall'infrastruttura IoT vengono archiviate nel cloud in un archivio protetto, con la possibilità di eseguire il rollover nel caso in cui le chiavi debbano essere sottoposte a nuovo provisioning. I dati possono essere archiviati in [Azure Cosmos DB](../articles/cosmos-db/introduction.md) o nel [database SQL](../articles/azure-sql/database/sql-database-paas-overview.md), consentendo la definizione del livello di sicurezza desiderato. In aggiunta, Azure offre un modo per monitorare e controllare tutti gli accessi ai dati, ricevendo avvertenze su qualsiasi intrusione o accesso non autorizzato.

## <a name="conclusion"></a>Conclusione

L'Internet of Things inizia dagli elementi più importanti per le aziende. L'IoT può offrire all'azienda un valore incredibile riducendo i costi, aumentando i ricavi e trasformando l'azienda stessa. Il successo di questa trasformazione dipende in larga misura dalla scelta del provider giusto per il software e i servizi IoT. Ciò significa che la ricerca di un provider non solo catalizza questa trasformazione per comprendere le esigenze e i requisiti dell'azienda, ma fornisce anche servizi e software realizzati tenendo conto di aspetti di importanza fondamentale quali sicurezza, privacy, trasparenza e conformità. Microsoft vanta una grande esperienza nello sviluppo e nella distribuzione di servizi e software protetti e continua ad attestarsi leader in questa nuova era dell'Internet of Things.

Solution Accelerator include misure di sicurezza per impostazione predefinita e consente il monitoraggio sicuro delle risorse per aumentare l'efficienza, migliorare le prestazioni operative per consentirne l'innovazione e usare l'analisi avanzata dei dati per trasformare le aziende. Grazie all'approccio a più livelli verso la sicurezza, numerose funzionalità di sicurezza e modelli di progettazione, Solution Accelerator consente di distribuire un'infrastruttura che può essere considerata attendibile per trasformare qualsiasi azienda.

## <a name="additional-information"></a>Informazioni aggiuntive

Ciascun Solution Accelerator crea istanze dei servizi di Azure, ad esempio:

* [**Hub IoT di Azure**](https://azure.microsoft.com/services/iot-hub/): il gateway che connette i dispositivi al cloud. È possibile arrivare a milioni di connessioni per hub ed elaborare grandi volumi di dati con supporto per l'autenticazione specifica per dispositivo che aiuta a proteggere la soluzione.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): un servizio di database scalabile e completamente indicizzato per i dati semistrutturati che gestiscono metadati per i dispositivi di cui si effettua il provisioning, ad esempio attributi, configurazione e proprietà di sicurezza. Azure Cosmos DB offre elaborazione ad alte prestazioni e velocità effettiva elevata, indicizzazione dei dati senza schema e un'interfaccia avanzata di query SQL.

* [**Analisi di flusso di Azure**](https://azure.microsoft.com/services/stream-analytics/): elaborazione del flusso in tempo reale nel cloud per sviluppare e distribuire rapidamente una soluzione di analisi a basso costo che consenta di rilevare informazioni approfondite in tempo reale da dispositivi, sensori, infrastruttura e applicazioni. I dati di questo servizio completamente gestito possono raggiungere qualsiasi volume anche in condizioni di velocità effettiva elevata, bassa latenza e resilienza.

* [**Servizi app Azure**](https://azure.microsoft.com/services/app-service/): una piattaforma cloud per la creazione di potenti app Web e per dispositivi mobili che si connettono ai dati ovunque, nel cloud o in locale. Creare app per dispositivi mobili coinvolgenti per iOS, Android e Windows. Eseguire l'integrazione con applicazioni Software as a Service (SaaS) e aziendali, grazie a connettività integrata a dozzine di applicazioni aziendali e servizi basati sul cloud. Scrivere codice usando IDE e il linguaggio preferito, .NET, Node.js, PHP, Python o Java, per creare app Web e API più rapidamente che mai.

* [**App**](https://azure.microsoft.com/services/app-service/logic/)per la logica: la funzionalità app per la logica del servizio app Azure consente di integrare la soluzione Internet delle cose nei sistemi line-of-business esistenti e di automatizzare i processi del flusso di lavoro. App per la logica consente agli sviluppatori di progettare flussi di lavoro che vengono avviati da un trigger e quindi di eseguire una serie di passaggi, regole e azioni che usano potenti connettori per l'integrazione con i processi aziendali. App per la logica offre connettività integrata per un vasto ecosistema di applicazioni SaaS, basate sul cloud e locali.

* [**Archiviazione BLOB di Azure**](https://azure.microsoft.com/services/storage/): archiviazione cloud affidabile ed economica per i dati che i dispositivi inviano al cloud.