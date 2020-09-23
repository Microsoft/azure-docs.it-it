---
title: Informazioni sul servizio database SQL di Azure
description: 'Introduzione al database SQL: dettagli tecnici e funzionalità del sistema di gestione di database relazionali Microsoft (RDBMS) nel cloud.'
keywords: introduzione a sql,intro a sql,informazioni sul database sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: 38f52178ec9c736f3ee51839678401753365d48d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907062"
---
# <a name="what-is-azure-sql-database"></a>Che cos'è il database SQL di Azure?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Il database SQL di Azure è un motore di database di piattaforma distribuita come servizio (PaaS) completamente gestito che gestisce la maggior parte delle funzioni di gestione del database, ad esempio l'aggiornamento, l'applicazione di patch, i backup e il monitoraggio senza coinvolgimento dell'utente. Il database SQL di Azure è sempre in esecuzione nella versione stabile più recente del motore di database SQL Server e del sistema operativo con patch con disponibilità del 99,99%. Le funzionalità di PaaS integrate nel database SQL di Azure consentono di concentrarsi sulle attività di amministrazione e ottimizzazione del database specifiche del dominio cruciali per l'azienda.

Con il database SQL di Azure è possibile creare un livello di archiviazione dei dati a disponibilità elevata e a elevate prestazioni per le applicazioni e le soluzioni in Azure. Il database SQL può essere la scelta ideale per un'ampia gamma di applicazioni cloud moderne, perché consente di elaborare sia i dati relazionali sia le [strutture non relazionali](../multi-model-features.md), ad esempio grafici, JSON, spaziali e XML.

Il database SQL di Azure è basato sulla versione stabile più recente del [motore di database di Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). È possibile utilizzare le funzionalità avanzate di elaborazione delle query, ad esempio le [tecnologie in memoria a prestazioni elevate](../in-memory-oltp-overview.md) e l' [elaborazione intelligente delle query](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json). Infatti, le funzionalità più recenti di SQL Server vengono rilasciate prima al database SQL e quindi a SQL Server stesso. Si ottengono le più recenti funzionalità di SQL Server senza sovraccarico per l'applicazione di patch o l'aggiornamento, testati in milioni di database. 

Il database SQL consente di definire e ridimensionare facilmente le prestazioni in due diversi modelli di acquisto: un [modello di acquisto basato su vCore](service-tiers-vcore.md) e un [modello di acquisto basato su DTU](service-tiers-dtu.md). Il database SQL è un servizio completamente gestito che dispone di disponibilità elevata, backup e altre operazioni di manutenzione comuni incorporati. Microsoft gestisce tutte le patch e l'aggiornamento del codice SQL e del sistema operativo. Non è necessario gestire l'infrastruttura sottostante.

Se non si ha familiarità con il database SQL di Azure, vedere il video di *panoramica sul database SQL di Azure* della [serie di video di Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)approfondita:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

## <a name="deployment-models"></a>Modelli di distribuzione

Il database SQL di Azure offre le opzioni di distribuzione seguenti per un database:

- Il [database singolo](single-database-overview.md) rappresenta un database completamente gestito e isolato. È possibile usare questa opzione se si dispone di applicazioni cloud moderne e microservizi che necessitano di una singola origine dati affidabile. Un database singolo è simile a un [database indipendente](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) nel [motore di database SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- Il [pool elastico](elastic-pool-overview.md) è una raccolta di [database singoli](single-database-overview.md) con un set condiviso di risorse, ad esempio CPU o memoria. I database singoli possono essere spostati all'interno e all'esterno di un pool elastico.

> [!IMPORTANT]
> Per comprendere le differenze di funzionalità tra il database SQL e SQL Server, nonché le differenze tra le diverse opzioni del database SQL di Azure, vedere [funzionalità del database SQL](features-comparison.md).

Il database SQL offre prestazioni prevedibili con più tipi di risorse, livelli di servizio e dimensioni di calcolo. Offre scalabilità dinamica senza tempi di inattività, ottimizzazione intelligente predefinita, scalabilità e disponibilità globali e opzioni di sicurezza avanzate. Queste funzionalità consentono di concentrarsi sullo sviluppo rapido di app e accelerare il time-to-Market, anziché sulla gestione delle macchine virtuali e dell'infrastruttura. Il database SQL è attualmente in 38 Data Center in tutto il mondo, quindi puoi eseguire il tuo database in un Data Center vicino a te.

## <a name="scalable-performance-and-pools"></a>Prestazioni e pool scalabili

È possibile definire la quantità di risorse assegnate. 
- Con i database singoli, ogni database è isolato dagli altri ed è portabile. Ognuno ha una propria quantità garantita di risorse di calcolo, memoria e archiviazione. La quantità di risorse assegnate al database è dedicata a tale database e non è condivisa con altri database in Azure. È possibile ridimensionare in modo dinamico [le risorse del database singolo](single-database-scale.md) . L'opzione database singolo fornisce risorse di calcolo, memoria e archiviazione diverse per esigenze diverse. Ad esempio, è possibile ottenere da 1 a 80 Vcore o da 32 GB a 4 TB. Il [livello di servizio con iperscalabilità](service-tier-hyperscale.md) per database singoli consente di ridimensionare fino a 100 TB, con funzionalità di backup e ripristino rapide.
- Con i pool elastici è possibile assegnare le risorse condivise da tutti i database nel pool. È possibile creare un nuovo database o spostare i database singoli esistenti in un pool di risorse per massimizzare l'utilizzo delle risorse e risparmiare denaro. Questa opzione offre inoltre la possibilità di ridimensionare in modo dinamico [le risorse del pool elastico](elastic-pool-scale.md) .

Puoi creare la tua prima app in un database di piccole dimensioni e a un costo ridotto al mese nel livello di servizio per utilizzo generico. È quindi possibile modificare il livello di servizio manualmente o a livello di codice in qualsiasi momento per il livello di servizio cruciale per l'azienda, in modo da soddisfare le esigenze della soluzione. È possibile regolare le prestazioni senza tempi di inattività per l'app o per i clienti. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Paghi solo per le risorse necessarie quando ti servono.

La scalabilità *dinamica* è diversa dalla *scalabilità*automatica. Con la scalabilità automatica, un servizio viene ridimensionato automaticamente in base a vari criteri, mentre la scalabilità dinamica consente di gestire manualmente il ridimensionamento senza tempi di inattività. L'opzione database singolo supporta la scalabilità dinamica manuale, ma non la scalabilità automatica. Per un'esperienza più automatica, valutare la possibilità di usare i pool elastici, che consentono ai database di condividere le risorse in un pool in base alle esigenze dei singoli database. Un'altra opzione consiste nell'usare script che consentono di automatizzare la scalabilità per un singolo database. Per un esempio, vedere [Usare PowerShell per monitorare e ridimensionare un database singolo SQL](scripts/monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Modelli di acquisto

Il database SQL offre i seguenti modelli di acquisto:
- Il [modello di acquisto basato su vCore](service-tiers-vcore.md) consente di scegliere il numero di Vcore, la quantità di memoria e la quantità e la velocità di archiviazione. Il modello di acquisto basato su vCore offre inoltre la possibilità di usare [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) per un risparmio in termini di costi. Per altre informazioni sulla Vantaggio Azure Hybrid, vedere la sezione "domande frequenti" più avanti in questo articolo.
- Il [modello di acquisto basato su DTU](service-tiers-dtu.md) offre una combinazione di risorse di calcolo, memoria e I/O in tre livelli di servizio, per supportare carichi di lavoro di database leggeri e pesanti. Le dimensioni di calcolo di ogni livello forniscono una diversa combinazione di queste risorse, a cui è possibile aggiungere altre risorse di archiviazione.
- Il [modello senza server](serverless-tier-overview.md) consente di ridimensionare automaticamente le risorse di calcolo in base alla domanda del carico di lavoro e le fatture per la quantità di calcolo usata al secondo. Il livello di calcolo senza server inoltre sospende automaticamente i database durante i periodi di inattività quando viene fatturata solo l'archiviazione e riprende automaticamente i database quando l'attività restituisce.

### <a name="service-tiers"></a>Livelli di servizio

Il database SQL di Azure offre tre livelli di servizio progettati per diversi tipi di applicazioni:
- Livello di servizio [per utilizzo generico/standard](service-tier-general-purpose.md) progettato per i carichi di lavoro comuni. Offre opzioni di calcolo e di archiviazione bilanciate e orientate al budget.
- Livello di servizio [business critical/Premium](service-tier-business-critical.md) progettato per le applicazioni OLTP con frequenza di transazioni elevata e I/O con latenza minima. Offre la massima resilienza agli errori usando diverse repliche isolate.
- Livello di servizio con [iperscalabilità](service-tier-hyperscale.md) progettato per database OLTP di grandi dimensioni e per la scalabilità automatica dell'archiviazione e la scalabilità delle risorse di calcolo.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>I pool elastici ottimizzano l'utilizzo delle risorse

Per molte aziende e applicazioni, la possibilità di creare singoli database e aumentare o ridurre le prestazioni all'occorrenza è sufficiente, specialmente se i modelli d'utilizzo sono relativamente prevedibili. I modelli di utilizzo imprevedibili possono rendere difficile la gestione dei costi e del modello aziendale. I [pool elastici](elastic-pool-overview.md) sono progettati per risolvere questo problema. Le risorse relative alle prestazioni vengono allocate a un pool anziché a un singolo database. Si paga per le risorse di prestazioni collettive del pool anziché per le prestazioni di un singolo database.

   ![Immagine che mostra i pool elastici nelle edizioni Basic, standard e Premium](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

Con i pool elastici, non è necessario concentrarsi sulla connessione delle prestazioni del database in base alla variazione della richiesta di risorse. I database in pool utilizzano le risorse relative alle prestazioni del pool elastico in base alla necessità. I database in pool utilizzano ma non superano i limiti del pool, quindi il costo rimane prevedibile anche se l'utilizzo del singolo database non lo è.

È possibile [aggiungere e rimuovere database nel pool](elastic-pool-overview.md), ridimensionando l'app da un numero limitato di database a migliaia, il tutto entro un budget controllato. È anche possibile controllare le risorse minime e massime disponibili per i database nel pool, per garantire che nessun database nel pool usi tutte le risorse del pool e che ogni database in pool disponga di una quantità minima di risorse garantita. Per altre informazioni sui modelli di progettazione per le applicazioni SaaS (Software as a Service) che usano i pool elastici, vedere [modelli di progettazione per applicazioni SaaS multi-tenant con il database SQL](saas-tenancy-app-design-patterns.md).

Gli script possono essere utili per il monitoraggio e il ridimensionamento dei pool elastici. Per un esempio, vedere [usare PowerShell per monitorare e ridimensionare un pool elastico nel database SQL di Azure](scripts/monitor-and-scale-pool-powershell.md).


### <a name="blend-single-databases-with-pooled-databases"></a>Unire database singoli e database in pool

È possibile combinare database singoli con pool elastici e modificare i livelli di servizio di database singoli e pool elastici per adattarsi alla situazione. È anche possibile combinare e abbinare altri servizi di Azure con il database SQL per soddisfare le esigenze di progettazione di app univoche, ridurre i costi e l'efficienza delle risorse e sbloccare nuove opportunità di business.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Funzionalità complete di monitoraggio e avviso

Il database SQL di Azure offre funzionalità avanzate di monitoraggio e risoluzione dei problemi che consentono di ottenere informazioni più approfondite sulle caratteristiche del carico di lavoro. Questi strumenti e funzionalità includono:
 - Funzionalità di monitoraggio predefinite fornite dalla versione più recente del motore di database di SQL Server. Consentono di trovare informazioni dettagliate sulle prestazioni in tempo reale. 
 - Funzionalità di monitoraggio PaaS fornite da Azure che consentono di monitorare e risolvere i problemi di un numero elevato di istanze di database.

[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store), una funzionalità predefinita di monitoraggio SQL Server, registra le prestazioni delle query in tempo reale e consente di identificare i potenziali problemi di prestazioni e i principali consumer di risorse. Le indicazioni e l'ottimizzazione automatica forniscono consigli sulle query con prestazioni regressioni e indici mancanti o duplicati. L'ottimizzazione automatica nel database SQL consente di applicare manualmente gli script che possono risolvere i problemi o consentire al database SQL di applicare la correzione. Il database SQL può anche testare e verificare che la correzione offra un certo vantaggio e mantenere o annullare la modifica a seconda del risultato. Oltre a Query Store e alle funzionalità di ottimizzazione automatica, è possibile usare [DMV e XEvent](monitoring-with-dmvs.md) standard per monitorare le prestazioni del carico di lavoro.

Azure offre strumenti [integrati di monitoraggio delle prestazioni](performance-guidance.md) e [avvisi](alerts-insights-configure-portal.md) , combinati con valutazioni delle prestazioni, che consentono di monitorare lo stato di migliaia di database. Grazie a questi strumenti, è possibile valutare rapidamente l'effetto della scalabilità verso l'alto o verso il basso, in base alle esigenze di prestazioni correnti o proiettate. Inoltre, il database SQL può [emettere metriche e log delle risorse](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) per semplificare il monitoraggio. È possibile configurare il database SQL per archiviare l'utilizzo delle risorse, ruoli di lavoro, sessioni e connettività in una delle risorse di Azure seguenti:

- **Archiviazione di Azure**: per l'archiviazione di enormi quantità di dati di telemetria a un costo conveniente.
- **Hub eventi di Azure**: per l'integrazione dei dati di telemetria del database SQL con la soluzione di monitoraggio personalizzata o le pipeline sensibili.
- **Log**di monitoraggio di Azure: per una soluzione di monitoraggio integrata con funzionalità di Reporting, avviso e mitigazione.

![Diagramma dell'architettura di monitoraggio di Azure](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>Funzionalità per la disponibilità

Il database SQL di Azure consente alle aziende di continuare a funzionare durante le rotture. In un ambiente di SQL Server tradizionale, in genere si hanno almeno due computer impostati localmente. Questi computer hanno copie dei dati esatte e mantenute in modo sincrono per la protezione da un errore di un singolo computer o componente. Questo ambiente fornisce disponibilità elevata, ma non protegge da un'emergenza naturale che distrugge il Data Center.

Il ripristino di emergenza presuppone che un evento irreversibile sia geograficamente localizzato abbastanza da avere un altro computer o un set di computer con una copia dei dati lontani. In SQL Server, è possibile usare Always On i gruppi di disponibilità in esecuzione in modalità asincrona per ottenere questa funzionalità. Spesso non si vuole attendere che la replica venga eseguita prima di eseguire il commit di una transazione, quindi è possibile che si verifichi una perdita di dati quando si eseguono failover non pianificati.

I database nei livelli di servizio Premium e business critical già [eseguono un risultato simile](high-availability-sla.md#premium-and-business-critical-service-tier-availability) alla sincronizzazione di un gruppo di disponibilità. I database nei livelli di servizio più bassi forniscono la ridondanza tramite l'archiviazione usando un [meccanismo diverso ma equivalente](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability). La logica incorporata consente di proteggersi da un singolo errore del computer. La funzionalità di replica geografica attiva offre la possibilità di proteggersi da situazioni di emergenza in cui viene distrutta un'intera area.

Zone di disponibilità di Azure tenta di proteggersi da un'interruzione di una singola compilazione di data center in una singola area. Consente di proteggersi dalla perdita di energia elettrica o rete a un edificio. Nel database SQL si inseriscono le diverse repliche in zone di disponibilità diverse (edifici diversi, in modo efficace).

Infatti, il contratto di servizio di Azure, basato su una rete globale di data center gestiti da Microsoft, [consente di garantire](https://azure.microsoft.com/support/legal/sla/) l'esecuzione dell'app 24/7. La piattaforma Azure gestisce completamente tutti i database e non garantisce alcuna perdita di dati e una percentuale elevata di disponibilità dei dati. Azure gestisce automaticamente l'applicazione di patch, i backup, la replica, il rilevamento degli errori, i possibili errori di hardware, software o rete sottostanti, la distribuzione di correzioni di bug, i failover, gli aggiornamenti del database e altre attività di manutenzione. La disponibilità standard viene ottenuta separando i livelli di calcolo e archiviazione. La disponibilità Premium viene eseguita integrando risorse di calcolo e archiviazione in un singolo nodo per le prestazioni e quindi implementando una tecnologia simile a Always On gruppi di disponibilità. Per una descrizione completa delle funzionalità a disponibilità elevata del database SQL di Azure, vedere la [disponibilità del database SQL](high-availability-sla.md). 

Inoltre, il database SQL offre funzionalità integrate per la [continuità aziendale e la scalabilità globale](business-continuity-high-availability-disaster-recover-hadr-overview.md) . Sono inclusi:

- [Backup automatici](automated-backups-overview.md):

  Il database SQL esegue automaticamente backup completi, differenziali e del log delle transazioni dei database per consentire il ripristino in qualsiasi momento. Per i database singoli e i database in pool, è possibile configurare il database SQL per archiviare i backup completi del database in archiviazione di Azure per la conservazione dei backup a lungo termine. Per le istanza gestite, è anche possibile eseguire backup solo di copia per la conservazione a lungo termine.

- [Ripristini temporizzati](recovery-using-backups.md):

  Tutte le opzioni di distribuzione del database SQL supportano il ripristino fino a qualsiasi punto nel tempo entro il periodo di memorizzazione automatico dei backup per qualsiasi database.
- [Replica geografica attiva](active-geo-replication-overview.md):

  Le opzioni database singolo e database in pool consentono di configurare fino a quattro database secondari leggibili nello stesso o in data center di Azure distribuiti a livello globale. Se, ad esempio, si dispone di un'applicazione SaaS con un database del catalogo con un volume elevato di transazioni di sola lettura simultanee, usare la replica geografica attiva per abilitare la scalabilità in lettura globale. Questa operazione rimuove i colli di bottiglia nel database primario a causa di carichi di lavoro di lettura. Per le istanze gestite, usare gruppi di failover automatico.
- [Gruppi di failover automatico](auto-failover-group-overview.md):

  Tutte le opzioni di distribuzione del database SQL consentono di usare i gruppi di failover per abilitare la disponibilità elevata e il bilanciamento del carico su scala globale. Sono incluse la replica geografica trasparente e il failover di grandi set di database, pool elastici e istanze gestite. I gruppi di failover consentono la creazione di applicazioni SaaS distribuite a livello globale, con un sovraccarico minimo amministrativo. In questo modo vengono mantenute tutte le complesse orchestrazioni di monitoraggio, routing e failover al database SQL.
- [Database con ridondanza della zona](high-availability-sla.md):

  Il database SQL consente di effettuare il provisioning di database o pool elastici Premium o business critical in più zone di disponibilità. Poiché questi database e pool elastici hanno più repliche ridondanti per la disponibilità elevata, l'inserimento di tali repliche in più zone di disponibilità garantisce una maggiore resilienza. Ciò include la possibilità di eseguire il ripristino automatico dagli errori di scalabilità dei Data Center, senza perdita di dati.

## <a name="built-in-intelligence"></a>Intelligenza incorporata

Con il database SQL si ottiene un'intelligenza integrata che consente di ridurre in modo significativo i costi di esecuzione e gestione dei database e di ottimizzare le prestazioni e la sicurezza dell'applicazione. Il database SQL raccoglie ed elabora una grande quantità di dati di telemetria, rispettando al tempo stesso la privacy dei clienti. Vari algoritmi valutano continuamente i dati di telemetria in modo che il servizio possa apprendere e adattarsi all'applicazione.

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitoraggio e ottimizzazione automatici delle prestazioni

Il database SQL offre informazioni dettagliate per le query che richiedono monitoraggio. Il database SQL apprende i modelli di database e consente di adattare lo schema del database al carico di lavoro. Il database SQL offre [raccomandazioni per ottimizzare le prestazioni](database-advisor-implement-performance-recommendations.md), nelle quali è possibile verificare le azioni di ottimizzazione e applicarle.

Tuttavia, il monitoraggio costante di un database è un'attività complessa e noiosa, soprattutto quando si lavora con molti database. [Intelligent Insights](intelligent-insights-overview.md) esegue automaticamente questo processo monitorando le prestazioni del database SQL su larga scala. Informa l'utente di problemi di riduzione delle prestazioni, identifica la causa principale di ogni problema e fornisce consigli sul miglioramento delle prestazioni quando possibile.

La gestione di un numero elevato di database potrebbe non essere possibile in modo efficiente, anche con tutti gli strumenti e i report disponibili forniti da database SQL e Azure. Anziché eseguire manualmente il monitoraggio e l'ottimizzazione del database, è consigliabile delegare alcune delle azioni di monitoraggio e ottimizzazione al database SQL tramite l' [ottimizzazione automatica](automatic-tuning-overview.md). Il database SQL applica automaticamente le raccomandazioni, i test e la verifica di ogni azione di ottimizzazione per garantire un miglioramento delle prestazioni. In questo modo, il database SQL si adatta automaticamente al carico di lavoro in modo controllato e sicuro. L'ottimizzazione automatica indica che le prestazioni del database vengono attentamente monitorate e confrontate prima e dopo ogni azione di ottimizzazione. Se le prestazioni non sono migliori, viene ripristinata l'azione di ottimizzazione.

Molti dei nostri partner che eseguono [app SaaS multi-tenant](saas-tenancy-app-design-patterns.md) sul database SQL si basano sull'ottimizzazione automatica delle prestazioni per garantire che le applicazioni abbiano sempre prestazioni stabili e prevedibili. Questa funzionalità consente loro di ridurre notevolmente il rischio di problemi di prestazioni durante la notte. Inoltre, poiché parte della loro clientela USA anche SQL Server, usano le stesse raccomandazioni di indicizzazione fornite dal database SQL per aiutare i clienti SQL Server.

[Nel database SQL sono disponibili](automatic-tuning-overview.md)due aspetti di ottimizzazione automatica:

- **Gestione automatica degli indici**: consente di identificare gli indici da aggiungere al database e quelli che è consigliabile rimuovere.
- **Correzione automatica**dei piani: identifica i piani problematici e corregge i problemi di prestazioni dei piani SQL.

### <a name="adaptive-query-processing"></a>Elaborazione di query adattive

È possibile utilizzare l' [elaborazione di query adattive](/sql/relational-databases/performance/intelligent-query-processing), inclusa l'esecuzione Interleaved per le funzioni con valori di tabella con più istruzioni, il feedback delle concessioni di memoria in modalità batch e i join adattivi in modalità batch. Ognuna di queste funzionalità di elaborazione di query adattive applica tecniche di apprendimento e adattamento simili, che consentono di risolvere ulteriormente i problemi di prestazioni correlati a problemi di ottimizzazione delle query in modo cronologico.

## <a name="advanced-security-and-compliance"></a>Sicurezza e conformità avanzate

Il database SQL offre una gamma di [funzionalità di sicurezza e conformità incorporate](../../active-directory/identity-protection/security-overview.md) che consentono all'applicazione di soddisfare i diversi requisiti di sicurezza e conformità.

> [!IMPORTANT]
> Microsoft ha certificato il database SQL di Azure (tutte le opzioni di distribuzione) rispetto a diversi standard di conformità. Per ulteriori informazioni, vedere il [Centro protezione Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità del database SQL.

### <a name="advance-threat-protection"></a>Protezione avanzata dalle minacce

Azure Defender per SQL è un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. Sono incluse le funzionalità per la gestione delle vulnerabilità del database e il rilevamento di attività anomale che potrebbero indicare una minaccia per il database. Consente di abilitare e gestire queste funzionalità da un'unica posizione.

- [Valutazione della vulnerabilità](sql-vulnerability-assessment.md):

  Questo servizio consente di individuare, monitorare e risolvere potenziali vulnerabilità del database. Consente di visualizzare lo stato di sicurezza e prevede passaggi utili per risolvere i problemi di sicurezza e migliorare la protezione del database.
- [Rilevamento minacce](threat-detection-configure.md):

  Questa funzionalità rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare il database. Monitora in modo continuo il database in caso di attività sospette e fornisce avvisi di sicurezza immediati su potenziali vulnerabilità, attacchi SQL injection e in caso di criteri anomali di accesso ai database. Gli avvisi di Threat Protection includono dettagli sulle attività sospette e consigliano azioni per l'analisi e la mitigazione della minaccia.

### <a name="auditing-for-compliance-and-security"></a>Controllo per conformità e sicurezza

Il [controllo](../../azure-sql/database/auditing-overview.md) tiene traccia degli eventi di database e li scrive in un log di controllo nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere l'attività del database e ottenere informazioni dettagliate su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

### <a name="data-encryption"></a>Crittografia dei dati

Il database SQL consente di proteggere i dati fornendo la crittografia. Per i dati in movimento, viene utilizzata la [sicurezza a livello di trasporto](https://support.microsoft.com/kb/3135244). Per i dati inattivi, usa la [crittografia dei dati trasparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Per i dati in uso, USA [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="data-discovery-and-classification"></a>Individuazione e classificazione dei dati

[Individuazione e classificazione dei dati](data-discovery-and-classification-overview.md) offre funzionalità integrate nel database SQL di Azure per l'individuazione, la classificazione, l'assegnazione di etichette e la protezione dei dati sensibili nei database. Consente di visualizzare lo stato di classificazione del database e di tenere traccia dell'accesso ai dati sensibili all'interno del database e oltre i relativi bordi.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrazione in Azure Active Directory e autenticazione a più fattori

Il database SQL consente di gestire a livello centralizzato le identità degli utenti di database e altri servizi Microsoft grazie all'[integrazione in Azure Active Directory](authentication-aad-overview.md). Questa funzionalità semplifica la gestione delle autorizzazioni e ottimizza la sicurezza. Azure Active Directory supporta la [funzionalità di autenticazione](authentication-mfa-ssms-overview.md) a più fattori per aumentare la sicurezza dei dati e delle applicazioni, supportando al tempo stesso un processo di accesso singolo.

## <a name="easy-to-use-tools"></a>Strumenti facili da usare

Il database SQL consente di creare e gestire le applicazioni in modo più facile e produttivo. Con il database SQL è possibile concentrarsi sull'attività principale, ovvero creare app straordinarie. È possibile gestire e sviluppare nel database SQL usando gli strumenti e le competenze già disponibili.

|Strumento|Descrizione|
|:---|:---|
|[Il portale di Azure](https://portal.azure.com/)|Un'applicazione basata sul Web per la gestione di tutti i servizi di Azure.|
|[Azure Data Studio](/sql/azure-data-studio/)|Uno strumento di database multipiattaforma eseguito in Windows, macOS e Linux.|
|[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)|Applicazione client gratuita e scaricabile per la gestione di qualsiasi infrastruttura SQL, dal SQL Server al database SQL.|
|[SQL Server Data Tools in Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)|Applicazione client gratuita e scaricabile per lo sviluppo di database relazionali SQL Server, database nel database SQL di Azure, pacchetti di Integration Services, modelli di dati Analysis Services e report di Reporting Services.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|Un editor di codice open source gratuito e scaricabile per Windows, macOS e Linux. Supporta le estensioni, tra cui l' [estensione MSSQL](https://aka.ms/mssql-marketplace) per l'esecuzione di query Microsoft SQL Server, il database SQL di Azure e Azure sinapsi Analytics di Azure (in precedenza SQL Data Warehouse).|

Il database SQL supporta la creazione di applicazioni con Python, Java, Node.js, PHP, Ruby e .NET in macOS, Linux e Windows. Il database SQL supporta le stesse [librerie di connessione](connect-query-content-reference-guide.md#libraries) di SQL Server.

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Domande frequenti sul database SQL

### <a name="can-i-control-when-patching-downtime-occurs"></a>È possibile controllare quando si verifica il tempo di inattività dell'applicazione di patch?

No. L'impatto dell'applicazione di patch non è in genere rilevante se si [impiega la logica di ripetizione dei tentativi](develop-overview.md#resiliency) nell'app. Per altre informazioni, vedere [pianificazione degli eventi di manutenzione di Azure nel database SQL di Azure](planned-maintenance.md).



## <a name="engage-with-the-sql-server-engineering-team"></a>Comunicare con il team di progettazione di SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): domande sull'amministrazione del database.
- [Stack overflow](https://stackoverflow.com/questions/tagged/sql-server): porre domande sullo sviluppo.
- [Microsoft Q&una pagina di](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)domande: porre domande tecniche.
- [Commenti e suggerimenti](https://aka.ms/sqlfeedback): Segnala bug e funzionalità richiesta.
- [Reddit](https://www.reddit.com/r/SQLServer/): discutere SQL Server.

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/sql-database/) per confrontare i costi e i calcolatori per i database singoli e i pool elastici.
- Per iniziare, vedere le guide introduttive seguenti:

  - [Creare un database nel portale di Azure](single-database-create-quickstart.md)  
  - [Creare un database con l'interfaccia della riga di comando di Azure](az-cli-script-samples-content-guide.md)
  - [Creare un database usando PowerShell](powershell-script-content-guide.md)

- Per un set di esempi dell'interfaccia della riga di comando di Azure e di PowerShell, vedere:
  - [Esempi dell'interfaccia della riga di comando di Azure per database SQL](az-cli-script-samples-content-guide.md)
  - [Esempi di Azure PowerShell per database SQL](powershell-script-content-guide.md)

- Per informazioni sulle nuove funzionalità così come sono state annunciate, vedere [Roadmap di Azure per il database SQL](https://azure.microsoft.com/roadmap/?category=databases).
- Vedi il [Blog del database SQL di Azure](https://azure.microsoft.com/blog/topics/database), in cui SQL Server Blog sui membri del team del prodotto sulle novità e sulle funzionalità del database SQL.

