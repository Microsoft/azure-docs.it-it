---
title: Panoramica dell'istanza gestita di SQL
description: Questo articolo descrive l'istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 04/02/2020
ms.openlocfilehash: 65bce50665b6dd99662e99ca57569f906f3af208
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598539"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Che cos'è l'istanza gestita di database SQL di Azure?

L'istanza gestita è una nuova opzione di distribuzione del database SQL di Azure che offre quasi il 100% di compatibilità con il più recente motore di database SQL Server in locale (Enterprise Edition), fornendo un'implementazione della [rete virtuale](../virtual-network/virtual-networks-overview.md) nativa che risolve problemi di sicurezza comuni e un [modello aziendale](https://azure.microsoft.com/pricing/details/sql-database/) favorevole per i clienti di SQL Server in locale. Il modello di distribuzione dell'istanza gestita consente ai clienti di SQL Server esistenti di spostare nel cloud le proprie applicazioni locali con modifiche minime nelle applicazioni e nel database. Allo stesso tempo, l'opzione di distribuzione dell'istanza gestita consente di mantenere tutte le funzionalità PaaS (applicazione automatica di patch e aggiornamenti di versione, [backup automatici](sql-database-automated-backups.md), [disponibilità elevata](sql-database-high-availability.md)), che riducono drasticamente il carico di gestione e il costo totale di proprietà.

> [!IMPORTANT]
> Per un elenco delle aree in cui l'opzione di distribuzione dell'istanza gestita è attualmente disponibile, vedere [Aree supportate](sql-database-managed-instance-resource-limits.md#supported-regions).

Nel diagramma seguente vengono descritte le funzionalità principali delle istanze gestite:

![funzionalità principali](./media/sql-database-managed-instance/key-features.png)

Il modello di distribuzione dell'istanza gestita è progettato per i clienti che desiderano eseguire la migrazione di un numero maggiore di app da un ambiente locale o IaaS, creato personalmente o un ambiente fornito da un ISV per la gestione completa dell'ambiente cloud PaaS, con il minor numero di operazioni di migrazione possibile. Usando il servizio di [migrazione dei dati](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) completamente automatizzato in Azure, i clienti possono trasferire la SQL Server locale a un'istanza gestita che offre la compatibilità con SQL Server in locale e l'isolamento completo delle istanze dei clienti con supporto VNet nativo.  Con Software Assurance è possibile scambiare le licenze esistenti con tariffe scontate per un'istanza gestita usando il [vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  Un'istanza gestita è la destinazione di migrazione migliore nel cloud per le istanze di SQL Server che richiedono un livello di sicurezza elevato e una superficie di programmazione avanzata.

L'opzione di distribuzione dell'istanza gestita mira a offrire una compatibilità della superficie di attacco prossima al 100% con l'ultima versione di SQL Server in locale tramite un piano di rilascio a fasi.

Per la scelta tra le opzioni di distribuzione di database SQL di Azure, database singolo, database in pool, istanza gestita e SQL Server ospitati in macchine virtuali, vedere [come scegliere la versione corretta di SQL Server in Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Funzionalità e le caratteristiche chiave

L'istanza gestita combina le migliori funzionalità disponibili sia nel database SQL di Azure che nel motore di database di SQL Server.

> [!IMPORTANT]
> Un'istanza gestita viene eseguita con tutte le funzionalità della versione più recente di SQL Server, incluse le operazioni online, le correzioni automatiche del piano e altri miglioramenti delle prestazioni Enterprise. Per un confronto tra le funzionalità disponibili, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).

| **Vantaggi di PaaS** | **Continuità aziendale** |
| --- | --- |
|Acquisto e gestione di hardware non necessari <br>Nessun sovraccarico per la gestione dell'infrastruttura sottostante <br>Provisioning rapido e scalabilità del servizio <br>Applicazione automatica di patch e aggiornamento della versione <br>Integrazione con altri servizi dati PaaS |Contratto di servizio relativo al tempo di attività 99,99%  <br>[Disponibilità elevata](sql-database-high-availability.md) integrata <br>Dati protetti con [backup automatici](sql-database-automated-backups.md) <br>Periodo di conservazione dei backup configurabile dal cliente <br>[Backup](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) avviati dall'utente <br>Funzionalità di [ripristino temporizzato di un database](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Sicurezza e conformità** | **Gestione**|
|Ambiente isolato ([integrazione della rete virtuale](sql-database-managed-instance-connectivity-architecture.md), servizio a tenant singolo, calcolo e archiviazione dedicati) <br>[Transparent Data Encryption (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticazione di Azure AD](sql-database-aad-authentication.md), supporto di Single Sign-On <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Entità di Azure AD server (account di accesso)</a>  <br>Soddisfa gli standard di conformità del database SQL di Azure <br>[Controllo SQL](sql-database-managed-instance-auditing.md) <br>[Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) |API di Azure Resource Manager per l'automazione del provisioning e della scalabilità del servizio <br>Funzionalità del portale di Azure per provisioning e scalabilità del servizio manuali <br>Servizio di migrazione dei dati

> [!IMPORTANT]
> Il database SQL di Azure (tutte le opzioni di distribuzione) è stato certificato rispetto a una serie di standard di conformità. Per ulteriori informazioni, vedere la [Microsoft Azure Centro protezione](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità del database SQL.

Nella tabella seguente sono elencate le principali funzionalità delle istanze gestite:

|Funzionalità | Descrizione|
|---|---|
| Versione/build di SQL Server | Motore di database di SQL Server (ultima versione stabile) |
| Backup automatici gestiti | Sì |
| Monitoraggio predefinito e metriche dell'istanza e del database | Sì |
| Applicazione automatica di patch software | Sì |
| Funzionalità più recenti del motore di database | Sì |
| Numero di file di dati (RIGHE) per il database | Più elementi |
| Numero di file di log (LOG) per il database | 1 |
| Rete virtuale: distribuzione di Azure Resource Manager | Sì |
| Rete virtuale: modello di distribuzione classica | No |
| Supporto del portale | Sì|
| Integration Services (SSIS) incorporato | No - SSIS fa parte di [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Analysis Services (SSAS) incorporato | No - SSAS fa parte di una [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) distinta |
| Reporting Services (SSRS) incorporato | No-Use [Power bi report impaginati](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) o host SSRS in una macchina virtuale di Azure. Sebbene Istanza gestita non possa eseguire SSRS come servizio, può ospitare i database di catalogo di SSRS 2019 per un server di report esterno utilizzando l'autenticazione SQL Server. |
|||

## <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

Il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md) per le istanze gestite offre flessibilità, controllo e trasparenza, consentendo di convertire con facilità i requisiti dei carichi di lavoro locali per il cloud. Questo modello consente di cambiare le risorse di calcolo, memoria e archiviazione in base ai requisiti dei carichi di lavoro. Il modello vCore è inoltre idoneo per un risparmio fino al 55% con la [vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

Nel modello vCore è possibile scegliere tra diverse generazioni di hardware.

- **Gen4** Le CPU logiche sono basate su processori Intel E5-2673 V3 (Haswell) a 2,4 GHz, unità SSD collegata, core fisici, 7 GB di RAM per core e dimensioni di calcolo comprese tra 8 e 24 vcore.
- **Quinta generazione** Le CPU logiche sono basate su processori Intel E5-2673 V4 (Broadwell) a 2,3 GHz e Intel SP-8160 (Skylake), unità SSD NVMe veloce, core logico con Hyper-Threading e dimensioni di calcolo tra 4 e 80 core.

Trovare altre informazioni sulla differenza tra le generazioni hardware nei [limiti delle risorse dell'istanza gestita](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Livelli di servizio dell'istanza gestita

L'istanza gestita è disponibile in due livelli di servizio:

- **Utilizzo generico**: progettato per applicazioni con requisiti di latenza di i/o di prestazioni tipici.
- **Business critical**: progettato per applicazioni con requisiti di latenza di i/o basso e con un minimo effetto delle operazioni di manutenzione sottostanti sul carico di lavoro.

Entrambi i livelli di servizio garantiscono una disponibilità del 99,99% e consentono di selezionare le dimensioni di archiviazione e capacità di calcolo in modo indipendente. Per altre informazioni sull'architettura di disponibilità elevata del database SQL di Azure, vedere [High Availability and Azure SQL Database](sql-database-high-availability.md) (Disponibilità elevata e database SQL di Azure).

### <a name="general-purpose-service-tier"></a>Livello di servizio Utilizzo generico

L'elenco seguente descrive le caratteristiche principali del livello di servizio Utilizzo generico:

- Progettazione della maggior parte delle applicazioni aziendali con requisiti di prestazioni tipici
- Archiviazione BLOB di Azure ad alte prestazioni (8 TB)
- [Disponibilità elevata](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) basata sull'affidabilità di Archiviazione BLOB di Azure e su [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Per altre informazioni, vedere [storage layer in general purpose tier](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) (Livello Archiviazione nel livello di servizio Utilizzo generico) e [storage performance best practices and considerations for managed instances (general purpose)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/) (Procedure consigliate e considerazioni sulle prestazioni di archiviazione per le istanze gestite (Utilizzo generico)).

Trovare altre informazioni sulla differenza tra i livelli di servizio nei [limiti delle risorse dell'istanza gestita](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Livello di servizio business critical

Il livello di servizio business critical è progettato per applicazioni con requisiti I/O elevati. Offre la massima resilienza agli errori tramite diverse repliche isolate.

L'elenco seguente descrive le caratteristiche principali del livello di servizio per business critical:

- Progettato per applicazioni aziendali con le prestazioni e i requisiti di disponibilità più elevati
- Viene fornito con l'archiviazione SSD locale estremamente rapida (fino a 1 TB nella quarta generazione e fino a 4 TB nella quinta generazione)
- [Disponibilità elevata](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) incorporata in base ai [gruppi di disponibilità always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e [Service fabric di Azure](../service-fabric/service-fabric-overview.md).
- Replica aggiuntiva di [database](sql-database-read-scale-out.md) di sola lettura incorporata che può essere usata per la creazione di report e altri carichi di lavoro di sola lettura
- [OLTP in memoria](sql-database-in-memory.md) che può essere usato per il carico di lavoro con requisiti di prestazioni elevate  

Trovare altre informazioni sulla differenza tra i livelli di servizio nei [limiti delle risorse dell'istanza gestita](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Operazioni di gestione di istanze gestite

Il database SQL di Azure consente di eseguire operazioni di gestione per distribuire automaticamente nuove istanze gestite, aggiornare le proprietà di un'istanza ed eliminare le istanze quando non sono più necessarie. In questa sezione vengono fornite informazioni sulle operazioni di gestione e le relative durate tipiche.

Per supportare le [distribuzioni nelle reti virtuali di Azure (reti virtuali)](../virtual-network/virtual-network-for-azure-services.md) e garantire l'isolamento e la sicurezza per i clienti, l'istanza gestita si basa su [cluster virtuali](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture), che rappresentano un set dedicato di macchine virtuali isolate distribuite all'interno della subnet della rete virtuale del cliente. In pratica, tutte le distribuzioni di istanze gestite in una subnet vuota generano una nuova compilazione del cluster virtuale.

Anche le operazioni successive sulle istanze gestite distribuite potrebbero avere effetti sul cluster virtuale sottostante. Ciò influiscono sulla durata delle operazioni di gestione, in quanto la distribuzione di macchine virtuali aggiuntive comporta un sovraccarico da tenere in considerazione quando si pianificano nuove distribuzioni o aggiornamenti per le istanze gestite esistenti.

Tutte le operazioni di gestione possono essere suddivise per categoria, come indicato di seguito:

- Distribuzione istanza (creazione nuova istanza). 
- Aggiornamento dell'istanza (modifica delle proprietà dell'istanza, ad esempio Vcore o archiviazione riservata.
- Eliminazione dell'istanza.

In genere, le operazioni sui cluster virtuali hanno più tempo. La durata delle operazioni nei cluster virtuali varia: di seguito sono riportati i valori che in genere è possibile prevedere, in base ai dati di telemetria del servizio esistenti:

- Creazione di un cluster virtuale. Si tratta di un passaggio sincrono nelle operazioni di gestione delle istanze. **90% delle operazioni terminano tra 4 ore**.
- Ridimensionamento del cluster virtuale (espansione o compattazione). L'espansione è un passaggio sincrono, mentre la compattazione viene eseguita in modo asincrono (senza alcun effetto sulla durata delle operazioni di gestione dell'istanza). **90% delle espansioni del cluster completate in meno di 2,5 ore**.
- Eliminazione del cluster virtuale. L'eliminazione è un passaggio asincrono, ma può anche essere [avviato manualmente](sql-database-managed-instance-delete-virtual-cluster.md) in un cluster virtuale vuoto, nel qual caso viene eseguito in modo sincrono. **90% delle eliminazioni di cluster virtuali completate tra 1,5 ore**.

Inoltre, la gestione delle istanze può includere anche una delle operazioni sui database ospitati, che comporta una durata più lunga:

- Connessione dei file di database dall'archiviazione di Azure. Si tratta di un passaggio sincrono, ad esempio calcolo (vCore), o di aumento o riduzione delle risorse di archiviazione nel livello di servizio per utilizzo generico. **90% di queste operazioni vengono completate in 5 minuti**.
- Always On il seeding del gruppo di disponibilità. Si tratta di un passaggio sincrono, ad esempio calcolo (vCore), o di ridimensionamento delle risorse di archiviazione nel livello di servizio business critical e nella modifica del livello di servizio da per utilizzo generico a business critical (o viceversa). La durata dell'operazione è proporzionale alle dimensioni totali del database e all'attività del database corrente (numero di transazioni attive). L'attività del database durante l'aggiornamento di un'istanza può introdurre una varianza significativa per la durata totale. il **90% di queste operazioni viene eseguito a 220 GB/ora o superiore**.

Nella tabella seguente sono riepilogate le operazioni e le durate generali tipiche:

|Categoria  |Operazione  |Segmento con esecuzione prolungata  |Durata stimata  |
|---------|---------|---------|---------|
|**Distribuzione** |Prima istanza in una subnet vuota|Creazione di un cluster virtuale|90% di operazioni completate tra 4 ore|
|Distribuzione |Prima istanza di un'altra generazione di hardware in una subnet non vuota (ad esempio, la prima istanza di generazione 5 in una subnet con istanze di generazione 4)|Creazione di un cluster virtuale *|90% di operazioni completate tra 4 ore|
|Distribuzione |Creazione della prima istanza di 4 Vcore, in una subnet vuota o non vuota|Creazione di un cluster virtuale * *|90% di operazioni completate tra 4 ore|
|Distribuzione |Creazione dell'istanza successiva all'interno della subnet non vuota (2a, terza e così via)|Ridimensionamento di cluster virtuali|90% di operazioni completate tra 2,5 ore|
|**Aggiornamento** |Modifica proprietà istanza (password amministratore, accesso ad AAD, flag di Vantaggio Azure Hybrid)|N/D|Fino a 1 minuto|
|Aggiornamento |Scalabilità verticale/orizzontale per l'archiviazione delle istanze (per utilizzo generico livello di servizio)|Associazione di file di database|90% di operazioni completate in 5 minuti|
|Aggiornamento |Scalabilità verticale/orizzontale per l'archiviazione delle istanze (business critical livello di servizio)|-Ridimensionamento cluster virtuale<br>-Always On seeding del gruppo di disponibilità|90% di operazioni completate tra 2,5 ore + tempo per il seeding di tutti i database (220 GB/ora)|
|Aggiornamento |Scalabilità verticale (VCore) di calcolo dell'istanza (per utilizzo generico)|-Ridimensionamento cluster virtuale<br>-Connessione dei file di database|90% di operazioni completate tra 2,5 ore|
|Aggiornamento |Scalabilità verticale (VCore) di calcolo dell'istanza (business critical)|-Ridimensionamento cluster virtuale<br>-Always On seeding del gruppo di disponibilità|90% di operazioni completate tra 2,5 ore + tempo per il seeding di tutti i database (220 GB/ora)|
|Aggiornamento |Ridimensionamento dell'istanza fino a 4 Vcore (per utilizzo generico)|-Il ridimensionamento del cluster virtuale (se eseguito per la prima volta, potrebbe richiedere la creazione di un cluster virtuale * *)<br>-Connessione dei file di database|90% di operazioni completate in 4 h 5 min * *|
|Aggiornamento |Ridimensionamento dell'istanza fino a 4 Vcore (business critical)|-Il ridimensionamento del cluster virtuale (se eseguito per la prima volta, potrebbe richiedere la creazione di un cluster virtuale * *)<br>-Always On seeding del gruppo di disponibilità|90% di operazioni completate tra 4 ore + tempo per il seeding di tutti i database (220 GB/ora)|
|Aggiornamento |Modifica del livello di servizio dell'istanza (per utilizzo generico business critical e viceversa)|-Ridimensionamento cluster virtuale<br>-Always On seeding del gruppo di disponibilità|90% di operazioni completate tra 2,5 ore + tempo per il seeding di tutti i database (220 GB/ora)|
|**Eliminazione**|Eliminazione di un'istanza|Backup della parte finale del log per tutti i database|90% le operazioni vengono completate fino a un minuto.<br>Nota: se viene eliminata l'ultima istanza della subnet, questa operazione pianifica l'eliminazione del cluster virtuale dopo 12 ore * * *|
|Eliminazione|Eliminazione di un cluster virtuale (operazione avviata dall'utente)|Eliminazione cluster virtuale|90% di operazioni completate in un massimo di 1,5 ore|

\*Il cluster virtuale è compilato per ogni generazione di hardware.

\*\*L'opzione di distribuzione vcore di 4 è stata rilasciata nel 2019 giugno e richiede una nuova versione del cluster virtuale. Se nella subnet di destinazione sono presenti istanze che sono state create prima del 12 giugno, un nuovo cluster virtuale verrà distribuito automaticamente per ospitare 4 istanze vCore.

\*\*\*12 ore è la configurazione corrente, ma potrebbe cambiare in futuro, quindi non assumere una dipendenza difficile. Se è necessario eliminare un cluster virtuale in precedenza (per rilasciare la subnet, ad esempio), vedere [eliminare una subnet dopo l'eliminazione di un'istanza gestita di database SQL di Azure](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management-operations"></a>Disponibilità dell'istanza durante le operazioni di gestione

L'istanza gestita non è disponibile per le applicazioni client durante le operazioni di distribuzione ed eliminazione.

Istanza gestita è disponibile durante le operazioni di aggiornamento, ad eccezione di un breve tempo di inattività causato dal failover che si verifica alla fine dell'aggiornamento. In genere, dura fino a 10 secondi anche in caso di transazioni a esecuzione prolungata interrotte, grazie al [recupero accelerato del database](sql-database-accelerated-database-recovery.md).

> [!TIP]
> L'aggiornamento dello spazio di archiviazione riservato in per utilizzo generico livello di servizio non comporta il failover né influisce sulla disponibilità dell'istanza.

> [!IMPORTANT]
> Non è consigliabile ridimensionare le risorse di calcolo o di archiviazione dell'istanza gestita di database SQL di Azure o per modificare il livello di servizio contemporaneamente con le transazioni a esecuzione prolungata (importazione di dati, processi di elaborazione dei dati, ricompilazione dell'indice e così via). Il failover del database che verrà eseguito al termine dell'operazione cancellerà tutte le transazioni in corso.


### <a name="management-operations-cross-impact"></a>Effetti incrociati sulle operazioni di gestione

Le operazioni di gestione di istanze gestite possono influire su altre operazioni di gestione delle istanze posizionate all'interno dello stesso cluster virtuale. Sono incluse le seguenti:

- **Le operazioni di ripristino con esecuzione prolungata** in un cluster virtuale manterranno un'altra operazione di creazione o ridimensionamento dell'istanza nella stessa subnet.<br/>**Esempio:** se è presente un'operazione di ripristino a esecuzione prolungata ed è presente una richiesta di creazione o ridimensionamento nella stessa subnet, la richiesta richiederà più tempo perché attenderà il completamento dell'operazione di ripristino prima di continuare.
    
- La creazione o l'operazione di **ridimensionamento di un'istanza successiva** viene messa in attesa dalla creazione o dalla scala dell'istanza avviata in precedenza che ha avviato il ridimensionamento del cluster virtuale.<br/>**Esempio:** se sono presenti più richieste di creazione e/o scalabilità nella stessa subnet nello stesso cluster virtuale e una di esse avvia il ridimensionamento di un cluster virtuale, tutte le richieste inviate 5 + minuti dopo quella che ha richiesto il ridimensionamento del cluster virtuale avranno più tempo del previsto perché queste richieste dovranno attendere il completamento del ridimensionamento prima di riprendere.

- **Le operazioni di creazione/ridimensionamento inviate in un intervallo di 5 minuti** verranno eseguite in batch ed eseguite in parallelo.<br/>**Esempio:** Verrà eseguito un solo ridimensionamento del cluster virtuale per tutte le operazioni inviate in un intervallo di 5 minuti (misurazione dal momento dell'esecuzione della prima richiesta di operazione). Se un'altra richiesta viene inviata più di 5 minuti dopo l'invio del primo, attende il completamento del ridimensionamento del cluster virtuale prima che l'esecuzione venga avviata.

> [!IMPORTANT]
> Le operazioni di gestione messe in attesa a causa di un'altra operazione in corso verranno riavviate automaticamente una volta soddisfatte le condizioni per continuare. Non è necessaria alcuna azione dell'utente per riprendere temporaneamente l'operazione di gestione sospesa.

### <a name="canceling-management-operations"></a>Annullamento di operazioni di gestione

Nella tabella seguente è riepilogata la possibilità di annullare operazioni di gestione specifiche e le durate generali tipiche:

Categoria  |Operazione  |Annullabile  |Durata stimata annullamento  |
|---------|---------|---------|---------|
|Distribuzione |Creazione di istanze |No |  |
|Aggiornamento |Scalabilità verticale/orizzontale di archiviazione dell'istanza (per utilizzo generico) |No |  |
|Aggiornamento |Scalabilità verticale/orizzontale di archiviazione dell'istanza (business critical) |Sì |90% di operazioni completate in 5 minuti |
|Aggiornamento |Scalabilità verticale (VCore) di calcolo dell'istanza (per utilizzo generico) |Sì |90% di operazioni completate in 5 minuti |
|Aggiornamento |Scalabilità verticale (VCore) di calcolo dell'istanza (business critical) |Sì |90% di operazioni completate in 5 minuti |
|Aggiornamento |Modifica del livello di servizio dell'istanza (per utilizzo generico business critical e viceversa) |Sì |90% di operazioni completate in 5 minuti |
|Elimina |Eliminazione di un'istanza |No |  |
|Elimina |Eliminazione di un cluster virtuale (operazione avviata dall'utente) |No |  |

Per annullare l'operazione di gestione, passare al pannello panoramica e fare clic sulla casella di notifica dell'operazione in corso. Dal lato destro verrà visualizzata una schermata con l'operazione in corso e sarà presente un pulsante per annullare l'operazione. Dopo aver eseguito il primo clic, verrà chiesto di fare nuovamente clic e confermare che si desidera annullare l'operazione.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

Dopo l'invio e l'elaborazione della richiesta di annullamento, si riceverà una notifica in caso di esito positivo dell'annullamento dell'invio. 

In caso di annullamento dell'esito positivo, l'operazione di gestione verrà annullata in pochi minuti causando un errore.

![annullamento del risultato dell'operazione](./media/sql-database-managed-instance/canceling-operation-result.png)

Se la richiesta di annullamento ha esito negativo o il pulsante Annulla non è attivo, significa che l'operazione di gestione non è stata annullata e che verrà completata in pochi minuti. L'esecuzione dell'operazione di gestione continuerà fino al completamento.

> [!IMPORTANT]
> L'operazione di annullamento è attualmente supportata solo nel portale.

## <a name="advanced-security-and-compliance"></a>Sicurezza e conformità avanzate

L'opzione di distribuzione dell'istanza gestita combina le funzionalità avanzate di sicurezza fornite dal cloud di Azure e il motore di database di SQL Server.

### <a name="managed-instance-security-isolation"></a>Isolamento di sicurezza dell'istanza gestita

Un'istanza gestita offre isolamento di sicurezza aggiuntivo dagli altri tenant nel cloud di Azure. L'isolamento di sicurezza include:

- L' [implementazione della rete virtuale nativa](sql-database-managed-instance-connectivity-architecture.md) e la connettività all'ambiente locale mediante Azure Express route o gateway VPN.
- In una distribuzione predefinita, l'endpoint SQL viene esposto solo tramite un indirizzo IP privato, consentendo una connettività sicura da reti private di Azure o ibride.
- Tenant singolo con infrastruttura sottostante dedicata (calcolo, archiviazione).

Il diagramma seguente illustra diverse opzioni di connettività per le applicazioni:

![disponibilità elevata](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Per altre informazioni dettagliate sull'integrazione della rete virtuale e sull'applicazione di criteri di rete a livello di subnet, vedere [Architettura della rete virtuale per le istanze gestite](sql-database-managed-instance-connectivity-architecture.md) e [Connettere l'applicazione a un'istanza gestita](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Inserire più istanze gestite nella stessa subnet, ogniqualvolta tale operazione è consentita dai requisiti di sicurezza, in quanto comporterà vantaggi aggiuntivi. La collocazione di istanze nella stessa subnet semplificherà notevolmente la manutenzione dell'infrastruttura di rete e ridurrà il tempo di provisioning dell'istanza, poiché una durata prolungata di provisioning è associata al costo della distribuzione della prima istanza gestita in una subnet.

### <a name="azure-sql-database-security-features"></a>Funzionalità di sicurezza del database SQL di Azure

Database SQL di Azure fornisce un set di funzionalità di sicurezza avanzato che può essere usato per proteggere i dati.

- Il [controllo dell'istanza gestita](sql-database-managed-instance-auditing.md) tiene traccia degli eventi di database e li scrive in un file di log di controllo inserito nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.
- Crittografia dei dati in transito: un'istanza gestita protegge i dati fornendo la crittografia per i dati in transito tramite Transport Layer Security. Oltre alla sicurezza a livello di trasporto, l'opzione di distribuzione dell'istanza gestita offre protezione per i dati sensibili in movimento, inattivi e durante l'elaborazione di query con [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted è una tecnologia leader del settore che offre un livello di sicurezza dei dati senza uguali, per la protezione da violazioni che implicano il furto di dati critici. Con Always Encrypted, ad esempio, i numeri delle carte di credito sono sempre archiviati in forma crittografata nel database, anche durante l'elaborazione di query, e la decrittografia è consentita nella posizione di utilizzo da parte di personale o applicazioni autorizzati che devono elaborare tali dati.
- [Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) è complementare al [controllo](sql-database-managed-instance-auditing.md) fornendo un ulteriore livello di intelligence per la sicurezza incorporato nel servizio che rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. L'utente viene avvisato di attività sospette, vulnerabilità potenziali e attacchi SQL injection, nonché di modelli anomali di accesso al database. Gli avvisi di Advanced Threat Protection possono essere visualizzati dal [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) e forniscono informazioni dettagliate sulle attività sospette e consigliano azioni su come analizzare e mitigare la minaccia.  
- La [maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking) limita l'esposizione dei dati sensibili mediante la maschera a utenti senza privilegi. La maschera dati dinamica impedisce l'accesso non autorizzato ai dati sensibili consentendo di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi di database designati, senza modificare i dati nel database.
- La [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) consente di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query, ad esempio l'appartenenza a un gruppo o il contesto di esecuzione. La sicurezza a livello di riga semplifica la progettazione e la codifica della sicurezza nell'applicazione. Consente di implementare limitazioni per l'accesso alle righe di dati, assicurando ad esempio che i collaboratori possano accedere solo alle righe di dati pertinenti per il proprio reparto o limitando l'accesso ai dati ai soli dati di pertinenti.
- La funzione [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) esegue la crittografia dei file di dati delle istanze gestite, noti anche come dati inattivi crittografati. TDE esegue la crittografia e la decrittografia delle operazioni di I/O di file di dati e log in tempo reale. La crittografia usa una chiave di crittografia del database (DEK) che viene archiviata nel record di avvio del database per la disponibilità durante il ripristino. È possibile proteggere tutti i database in un'istanza gestita con la crittografia dei dati trasparente. Transparent Data Encryption è la collaudata tecnologia inattiva della crittografia di SQL Server richiesta da molti standard di conformità per la protezione da furti di supporti di archiviazione.

La migrazione di un database crittografato in un'istanza gestita è supportata tramite il Servizio Migrazione del database di Azure o il ripristino nativo. Se si prevede di eseguire la migrazione di un database crittografato usando il ripristino nativo, la migrazione del certificato Transparent Data Encryption esistente dal SQL Server locale o SQL Server in una macchina virtuale a un'istanza gestita è un passaggio obbligatorio. Per altre informazioni sui vari metodi di migrazione, vedere [Migrazione di un'istanza di SQL Server a un'istanza gestita](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integrazione di Azure Active Directory

L'opzione di distribuzione dell'istanza gestita supporta i tradizionali account di accesso del motore di database di SQL Server e gli account di accesso integrati con Azure Active Directory (AAD). Le entità server (account di accesso) di Azure AD (**anteprima pubblica**) sono una versione cloud di Azure degli account di accesso ai database di Windows usati nell'ambiente locale. Azure AD entità server (account di accesso) consentono di specificare gli utenti e i gruppi dal tenant di Azure Active Directory come entità con ambito di istanza reale, in grado di eseguire qualsiasi operazione a livello di istanza, incluse le query tra database all'interno della stessa istanza gestita.

Viene introdotta una nuova sintassi per creare Azure AD entità server (account di accesso), **dal provider esterno**. Per altre informazioni sulla sintassi, vedere <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">creare un account di accesso</a>ed esaminare l'articolo eseguire il [provisioning di un amministratore Azure Active Directory per l'istanza gestita](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) .

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrazione in Azure Active Directory e autenticazione a più fattori

L'opzione di distribuzione dell'istanza gestita consente di gestire a livello centralizzato le identità degli utenti di database e altri servizi Microsoft grazie all'[integrazione in Azure Active Directory](sql-database-aad-authentication.md). Questa funzionalità semplifica la gestione delle autorizzazioni e ottimizza la sicurezza. Azure Active Directory supporta l'[autenticazione a più fattori](sql-database-ssms-mfa-authentication-configure.md) (MFA) per una maggiore sicurezza di dati e applicazioni, supportando allo stesso tempo un processo di accesso singolo.

### <a name="authentication"></a>Authentication

Per autenticazione dell'istanza gestita si intende il modo in cui l'utente dimostra la propria identità durante la connessione al database. Il database SQL supporta due tipi di autenticazione:  

- **Autenticazione SQL**:

  Questo metodo di autenticazione usa nome utente e password.
- **Autenticazione di Azure Active Directory**

  Questo metodo di autenticazione usa identità gestite da Azure Active Directory ed è supportato per domini gestiti e integrati. [Quando possibile](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), usare l'autenticazione di Active Directory (sicurezza integrata).

### <a name="authorization"></a>Autorizzazione

Per autorizzazione si intendono le operazioni che l'utente può eseguire in un database SQL di Azure, che sono controllate dalle appartenenze ai ruoli del database e dalle autorizzazioni a livello di oggetto dell'account utente. Un'istanza gestita ha le stesse funzionalità di autorizzazione di SQL Server 2017.

## <a name="database-migration"></a>Migrazione di database

L'opzione di distribuzione dell'istanza gestita è destinata a scenari utente con migrazione di massa di database da implementazioni di database locali o IaaS. L'istanza gestita supporta diverse opzioni di migrazione di database:

### <a name="back-up-and-restore"></a>Backup e ripristino  

L'approccio di migrazione sfrutta i backup di SQL per l'archiviazione BLOB di Azure. I backup archiviati in BLOB del servizio di archiviazione di Azure possono essere ripristinati direttamente in un'istanza gestita usando il [comando T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Per una guida introduttiva che illustra come ripristinare il file di backup di database Wide World Importers, vedere [ripristinare un file di backup in un'istanza gestita](sql-database-managed-instance-get-started-restore.md). Questa Guida introduttiva mostra che è necessario caricare un file di backup nell'archiviazione BLOB di Azure e proteggerlo usando una chiave di firma di accesso condiviso (SAS).
- Per informazioni sul ripristino dall'URL, vedere [Ripristino nativo da URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> I backup da un'istanza gestita possono essere ripristinati solo in un'altra istanza gestita. Non possono essere ripristinati in SQL Server locale o in database singolo o pool elastico.

### <a name="data-migration-service"></a>Servizio di migrazione dei dati

Il Servizio Migrazione del database di Azure è un servizio completamente gestito progettato per abilitare le migrazioni senza interruzioni da più origini di database alle piattaforme di dati di Azure con tempi di inattività minimi. Questo servizio semplifica le attività necessarie per spostare database di SQL Server e di terze parti nel database SQL di Azure (database singoli, database in pool di pool elastici e database dell'istanza in un'istanza gestita) e SQL Server nella macchina virtuale di Azure. Vedere [come eseguire la migrazione del database locale in un'istanza gestita tramite Servizio Migrazione del database](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Funzionalità di SQL supportate

L'opzione di distribuzione dell'istanza gestita mira a offrire una compatibilità della superficie di attacco prossima al 100% con SQL Server in locale disponibile a fasi fino alla disponibilità generale del servizio. Per un elenco di funzionalità e confronto, vedere confronto tra le [funzionalità del database SQL](sql-database-features.md)e per un elenco delle differenze di t-SQL nelle istanze gestite rispetto a SQL Server, vedere [differenze di t-SQL per le istanze gestite da SQL Server](sql-database-managed-instance-transact-sql-information.md).

L'opzione di distribuzione dell'istanza gestita supporta la compatibilità con le versioni precedenti per i database di SQL 2008. È supportata la migrazione diretta da server di database di SQL 2005, il livello di compatibilità per i database di SQL 2005 migrati viene aggiornato a SQL 2008.
  
Il diagramma seguente illustra la compatibilità della superficie di attacco in un'istanza gestita:  

![migrazione](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Differenze principali tra SQL Server in locale e in un'istanza gestita

L'opzione di distribuzione dell'istanza gestita offre vantaggi derivanti dall'essere sempre aggiornati nel cloud, mentre alcune funzionalità di SQL Server in locale possono essere obsolete, essere state ritirate o sostituite con alternative. Esistono casi specifici in cui gli strumenti devono riconoscere che una particolare funzionalità funziona in modo leggermente diverso o che il servizio è in esecuzione in un ambiente che non è completamente controllato:

- La disponibilità elevata è integrata e preconfigurata mediante una tecnologia simile a [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Backup automatici e ripristino temporizzato. Il cliente può avviare backup `copy-only` che non interferiscono con la catena di backup automatica.
- L'istanza gestita non consente l'uso di percorsi fisici completi, in modo che tutti gli scenari corrispondenti debbano essere supportati in modo diverso: RESTOre DB non supporta WITH MOVE, CREATE DB non consente percorsi fisici, BULK INSERT funziona solo con i BLOB di Azure e così via.
- Istanza gestita supporta [l'autenticazione Azure ad](sql-database-aad-authentication.md) come alternativa cloud all'autenticazione di Windows.
- L'istanza gestita gestisce automaticamente filegroup XTP e i file per i database contenenti oggetti di OLTP in memoria
- L'istanza gestita supporta SQL Server Integration Services e può ospitare il catalogo SSIS, ovvero SSISDB, che archivia i pacchetti SSIS, che però vengono eseguiti in un'istanza gestita di Azure-SSIS Integration Runtime in Azure Data Factory. Per informazioni, vedere [Creare Azure-SSIS Integration Runtime in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Per confrontare le funzionalità SSIS nel database SQL, vedere [confrontare un database singolo di database SQL di Azure, un pool elastico e un'istanza gestita](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).

### <a name="managed-instance-administration-features"></a>Funzionalità di amministrazione dell'istanza gestita

L'opzione di distribuzione dell'istanza gestita consente all'amministratore di sistema di risparmiare tempo nelle attività amministrative perché vengono automatizzate o semplificate dal servizio database SQL. Ad esempio, [installazione e patch del sistema operativo/RDBMS](sql-database-high-availability.md), [ridimensionamento e configurazione di istanze dinamiche](sql-database-single-database-scale.md), [backup](sql-database-automated-backups.md), [replica di database](replication-with-sql-database-managed-instance.md) (inclusi i database di sistema), [configurazione della disponibilità elevata](sql-database-high-availability.md)e configurazione di flussi di dati di monitoraggio dell'integrità e [delle prestazioni](../azure-monitor/insights/azure-sql.md) .

> [!IMPORTANT]
> Per un elenco delle funzionalità supportate, parzialmente supportate e non supportate, vedere [Funzionalità del Database SQL](sql-database-features.md). Per un elenco delle differenze di T-SQL nelle istanze gestite rispetto a SQL Server, vedere [differenze t-SQL dell'istanza gestita da SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Come identificare un'istanza gestita a livello di codice

La tabella seguente mostra diverse proprietà, accessibili tramite Transact SQL, che è possibile usare per rilevare se l'applicazione funziona con l'istanza gestita e recuperare proprietà importanti.

|Proprietà|valore|Commento|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Questo valore è uguale a quello del database SQL. Questa operazione **non** indica il motore SQL versione 12 (SQL Server 2014). Istanza gestita esegue sempre la versione più recente del motore SQL stabile, che è uguale o superiore alla versione RTM più recente disponibile di SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Questo valore è uguale a quello del database SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Questo valore identifica l'istanza gestita in modo univoco.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS completo dell'istanza nel formato seguente:`<instanceName>`.`<dnsPrefix>`.database.Windows.net, dove `<instanceName>` è il nome fornito dal cliente, mentre `<dnsPrefix>` è la parte del nome generata automaticamente che garantisce l'univocità del DNS globale (ad esempio "wcus17662feb9ce98")|Esempio: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare la prima istanza gestita, vedere [Guida introduttiva](sql-database-managed-instance-get-started.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
- Per altre informazioni sulla configurazione della rete virtuale, vedere l'articolo relativo alla [configurazione di una rete virtuale per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Per una guida introduttiva per la creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per un'esercitazione sull'uso del servizio migrazione del database di Azure (DMS) per la migrazione, vedere [migrazione di istanze gestite tramite DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Per il monitoraggio avanzato delle prestazioni del database dell'istanza gestita con l'Intelligence per la risoluzione dei problemi incorporata, vedere [monitorare il database SQL di Azure con analisi SQL di Azure](../azure-monitor/insights/azure-sql.md).
- Per informazioni sui prezzi, vedere [prezzi dell'istanza gestita di database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
