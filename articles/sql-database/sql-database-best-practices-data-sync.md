---
title: Procedure consigliate per la sincronizzazione dei dati
description: Informazioni sulle procedure consigliate per la configurazione e l'esecuzione della sincronizzazione dati SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: ee929fa227cb105b73bc929c13a768aabef37ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771684"
---
# <a name="best-practices-for-sql-data-sync"></a>Procedure consigliate per la sincronizzazione dati SQL 

Questo articolo descrive le procedure consigliate per la sincronizzazione dati SQL di Azure.

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> La sincronizzazione dati SQL di Azure **non** supporta l'istanza gestita del database SQL di Azure in questo momento.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a>Sicurezza e affidabilità

### <a name="client-agent"></a>Agente client

-   Installare l'agente client usando un account utente con privilegi minimi e con accesso ai servizi di rete.  
-   Installare l'agente client in un computer diverso dal computer locale con SQL Server.  
-   Non registrare un database locale con più di un agente.    
    -   Evitare questa operazione anche se si intende sincronizzare tabelle diverse relative a diversi gruppi di sincronizzazione.  
    -   La registrazione di un database locale con più agenti client può causare problemi quando si elimina uno dei gruppi di sincronizzazione.

### <a name="database-accounts-with-least-required-privileges"></a>Account di database con privilegi minimi

-   **Per la configurazione della sincronizzazione**. Create/Alter Table; Alter Database; Create Procedure; Select/ Alter Schema; Create User-Defined Type.

-   **Per la sincronizzazione in corso**. Selezionare/inserire/aggiornare/eliminare nelle tabelle selezionate per la sincronizzazione e nei metadati di sincronizzazione e nelle tabelle di rilevamento; Autorizzazione di esecuzione per le stored procedure create dal servizio; Autorizzazione di esecuzione per i tipi di tabella definiti dall'utente.

-   **Per il deprovisioning**. Alter su tabelle che fanno parte della sincronizzazione; Select/Delete su tabelle di metadati di sincronizzazione; Control su tabelle di rilevamento della sincronizzazione, stored procedure e tipi di tabelle definiti dall'utente.

Il database SQL di Azure supporta un solo set di credenziali. Per eseguire queste operazioni nei limiti di questo vincolo, considerare le opzioni seguenti:

-   Modificare le credenziali per le diverse fasi, ad esempio *credentials1* per la configurazione e *credentials2* per la sincronizzazione continua.  
-   Modificare l'autorizzazione delle credenziali, ovvero, modificare l'autorizzazione dopo aver configurato la sincronizzazione.

## <a name="setup"></a>Configurazione

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a>Vincoli e considerazioni sui database

#### <a name="sql-database-instance-size"></a>Dimensione dell'istanza del database SQL

Quando si crea una nuova istanza di database SQL, impostare la dimensione massima in modo che sia sempre maggiore rispetto al database da distribuire. Se la dimensione massima impostata non è maggiore rispetto al database distribuito, la sincronizzazione avrà esito negativo. Anche se la sincronizzazione dati SQL non offre funzioni di aumento automatico, è possibile eseguire il comando `ALTER DATABASE` per aumentare le dimensioni del database dopo che è stato creato. Verificare di rientrare nei limiti delle dimensioni delle istanze di database SQL.

> [!IMPORTANT]
> Sincronizzazione dati SQL archivia altri metadati con ogni database. Tenere conto di questi metadati quando si calcola lo spazio necessario. La maggiore quantità di overhead è correlata alla larghezza delle tabelle (ad esempio, le tabelle strette richiedono più overhead) e alla quantità di traffico.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a>Vincoli e considerazioni sulle tabelle

#### <a name="selecting-tables"></a>Selezione di tabelle

Non è necessario includere in un gruppo di sincronizzazione tutte le tabelle presenti in un database. Le tabelle che si includono in un gruppo di sincronizzazione incidono sui costi e sull'efficienza. Includere pertanto in un gruppo di sincronizzazione solo le tabelle necessarie all'azienda e quelle da cui le prime dipendono.

#### <a name="primary-keys"></a>Chiavi primarie

Ogni tabella inclusa in un gruppo di sincronizzazione deve avere una chiave primaria. Il servizio di sincronizzazione dati SQL non può sincronizzare una tabella che non dispone di una chiave primaria.

Prima di usare la sincronizzazione dati SQL in fase di produzione, testare le prestazioni della sincronizzazione iniziale e di quella continua.

#### <a name="empty-tables-provide-the-best-performance"></a>Le tabelle vuote offrono le migliori prestazioni

Le tabelle vuote offrono le migliori prestazioni al momento dell'inizializzazione. Se la tabella di destinazione è vuota, la sincronizzazione dati usa l'inserimento in blocco per caricare i dati. In caso contrario, la sincronizzazione dei dati esegue un confronto e l'inserimento riga per riga per verificare la presenza di conflitti. Se le prestazioni non sono un problema, tuttavia, è possibile impostare la sincronizzazione tra le tabelle che contengono già i dati.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a>Provisioning dei database di destinazione

La sincronizzazione dati SQL consente il provisioning automatico di base dei database.

Questa sezione descrive le limitazioni del provisioning nel servizio di sincronizzazione dati SQL.

#### <a name="autoprovisioning-limitations"></a>Limitazioni del provisioning automatico

Per quanto riguarda il provisioning automatico, la sincronizzazione dati SQL presenta le limitazioni seguenti:

-   Nella tabella di destinazione vengono create solo le colonne selezionate. Nelle tabelle di destinazione non viene eseguito il provisioning delle colonne che non fanno parte del gruppo di sincronizzazione.
-   Gli indici vengono creati solo per le colonne selezionate. Se gli indici della tabella di origine includono colonne che non fanno parte del gruppo di sincronizzazione, non verrà eseguito il provisioning di questi indici nelle tabelle di destinazione.  
-   Non viene eseguito il provisioning degli indici nelle colonne di tipo XML.  
-   Non viene eseguito il provisioning dei vincoli CHECK.  
-   Non viene eseguito il provisioning dei trigger esistenti nelle tabelle di origine.  
-   Non vengono create viste e stored procedure nel database di destinazione.
-   Le azioni ON UPDATE CASCADE e ON DELETE CASCADE su vincoli di chiave esterna non vengono ricreate nelle tabelle di destinazione.
-   Se si dispone di colonne decimali o numeriche con una precisione maggiore di 28, la sincronizzazione dati SQL potrebbe verificarsi un problema di overflow della conversione durante la sincronizzazione. È consigliabile limitare la precisione delle colonne decimali o numeriche a 28 o meno.

#### <a name="recommendations"></a>Consigli

-   Usare la funzionalità di provisioning automatico della sincronizzazione dati SQL solo per testare il servizio.  
-   Per la fase di produzione eseguire il provisioning dello schema del database.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a>Dove individuare il database hub

#### <a name="enterprise-to-cloud-scenario"></a>Scenario da azienda a cloud

Per ridurre al minimo la latenza, mantenere il database hub in prossimità della maggiore concentrazione del traffico del database del gruppo di sincronizzazione.

#### <a name="cloud-to-cloud-scenario"></a>Scenario da cloud a cloud

-   Quando tutti i database di un gruppo di sincronizzazione sono ubicati in un unico data center, l'hub deve trovarsi nello stesso data center. Questa configurazione riduce la latenza e il costo del trasferimento dei dati tra data center.
-   Quando i database di un gruppo di sincronizzazione sono ubicati in più data center, l'hub deve trovarsi nello stesso data center in cui si trova la maggior parte dei database e in cui è presente il maggior volume di traffico.

#### <a name="mixed-scenarios"></a>Scenari misti

Applicare le linee guida precedenti a configurazioni più complesse di gruppi di sincronizzazione, ad esempio a scenari misti da azienda a cloud e da cloud a cloud.

## <a name="sync"></a>Sincronizzazione

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a> Evitare una sincronizzazione iniziale lenta e dispendiosa

Questa sezione prende in esame la sincronizzazione iniziale di un gruppo di sincronizzazione e spiega come evitare che per tale sincronizzazione iniziale siano richiesti tempi più lunghi e costi più elevati del necessario.

#### <a name="how-initial-sync-works"></a>Funzionamento della sincronizzazione iniziale

Quando si crea un gruppo di sincronizzazione, iniziare con dati in un solo database. Se sono presenti dati in più database, la sincronizzazione dati SQL considera ogni riga come un conflitto da risolvere. La risoluzione dei conflitti causa un rallentamento della sincronizzazione iniziale, che può richiedere giorni o addirittura mesi a seconda delle dimensioni del database.

Se i database si trovano in data center diversi, ogni riga deve spostarsi da un data center a un altro, con il conseguente aumento dei costi della sincronizzazione iniziale.

#### <a name="recommendation"></a>Recommendation

Se possibile, iniziare con i dati in un unico database del gruppo di sincronizzazione.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a> Progettazione che evita i cicli di sincronizzazione

Si verifica un ciclo di sincronizzazione quando all'interno di un gruppo di sincronizzazione sono presenti riferimenti circolari. In uno scenario di questo tipo ogni modifica in un database viene replicata in modo circolare e all'infinito nei database del gruppo di sincronizzazione.   

È consigliabile evitare i cicli di sincronizzazione in quanto possono provocare una riduzione delle prestazioni e un aumento significativo dei costi.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a> Modifiche che non vengono propagate

#### <a name="reasons-that-changes-fail-to-propagate"></a>Cause della mancata propagazione delle modifiche

È possibile che le modifiche apportate non vengano propagate per uno dei motivi seguenti:

-   Incompatibilità dello schema o del tipo di dati.
-   Inserimento di valori null in colonne che non ammettono valori null.
-   Violazione di vincoli di chiavi esterne.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Cosa accade se le modifiche non vengono propagate?

-   Il gruppo di sincronizzazione indica che si trova in uno stato di **Warning** (Avviso).
-   I dettagli sono elencati nel visualizzatore log dell'interfaccia utente del portale.
-   Se il problema non viene risolto per 45 giorni, il database risulterà "Non aggiornato".

> [!NOTE]
> Queste modifiche non verranno mai propagate. L'unico modo per risolvere il problema è ricreare il gruppo di sincronizzazione.

#### <a name="recommendation"></a>Recommendation

Monitorare regolarmente l'integrità del database e del gruppo di sincronizzazione attraverso l'interfaccia del portale e del log.


## <a name="maintenance"></a>Manutenzione

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a>Evitare database e gruppi di sincronizzazione non aggiornati

È possibile che un gruppo di sincronizzazione o un database all'interno di un gruppo di sincronizzazione non sia più aggiornato. Quando lo stato di un gruppo di sincronizzazione è **Non aggiornato,** smette di funzionare. Quando lo stato di un database è **Out-of-date** (Non aggiornato), può verificarsi una perdita di dati. È consigliabile evitare questo scenario anziché provare a risolvere il problema.

#### <a name="avoid-out-of-date-databases"></a>Evitare database e gruppi di sincronizzazione non aggiornati

Lo stato di un database è impostato su **Non aggiornato** quando è stato offline per 45 giorni o più. Per evitare che lo stato di un database venga impostato su **Out-of-date** (Non aggiornato), verificare che nessun database rimanga offline per 45 giorni o più.

#### <a name="avoid-out-of-date-sync-groups"></a>Evitare i gruppi di sincronizzazione non aggiornati

Lo stato di un gruppo di sincronizzazione viene impostato su **Out-of-date** (Non aggiornato) quando le modifiche nel gruppo non vengono propagate al resto del gruppo di sincronizzazione per 45 giorni o più. Per evitare che lo stato di un gruppo di sincronizzazione venga impostato su **Out-of-date** (Non aggiornato), controllare regolarmente il log della cronologia del gruppo di sincronizzazione. Verificare che tutti i conflitti siano risolti e che le modifiche vengano propagate a tutti i database del gruppo di sincronizzazione.

È possibile che un gruppo di sincronizzazione non riesca ad applicare una modifica per uno dei motivi seguenti:

-   Incompatibilità dello schema tra tabelle.
-   Incompatibilità dei dati tra tabelle.
-   Inserimento di una riga con valore null in una colonna che non ammette valori null.
-   Aggiornamento di una riga con un valore che viola un vincolo di chiave esterna.

Per evitare che i gruppi di sincronizzazione non vengano aggiornati:

-   Aggiornare lo schema per includere i valori contenuti nelle righe con esito negativo.
-   Aggiornare i valori delle chiavi esterne per includere i valori contenuti nelle righe con esito negativo.
-   Aggiornare i valori dei dati nella riga con esito negativo in modo che siano compatibili con lo schema o con le chiavi esterne nel database di destinazione.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a>Evitare problemi di deprovisioning

In alcuni casi, l'annullamento della registrazione di un database con un agente client può causare un errore di sincronizzazione.

#### <a name="scenario"></a>Scenario

1. Il gruppo di sincronizzazione A è stato creato tramite un'istanza di database SQL e un database di SQL Server locale, associato all'agente locale 1.
2. Lo stesso database locale è registrato con l'agente locale 2, che non è associato ad alcun gruppo di sincronizzazione.
3. Se si annulla la registrazione del database locale dall'agente locale 2, vengono rimosse le tabelle di rilevamento e metadati del gruppo di sincronizzazione A per il database locale.
4. Le operazioni del gruppo di sincronizzazione A non riescono e viene visualizzato il messaggio di errore seguente: "The current operation could not be completed because the database is not provisioned for sync or you do not have permissions to the sync configuration tables" (Non è stato possibile completare l'operazione corrente perché non è stato eseguito il provisioning del database per la sincronizzazione o non si dispone delle autorizzazioni per accedere alle tabelle di configurazione della sincronizzazione).

#### <a name="solution"></a>Soluzione

Per evitare questo scenario, non registrare un database con più agenti.

Per risolvere il problema:

1. Rimuovere il database da ogni gruppo di sincronizzazione a cui appartiene.  
2. Riaggiungere il database a ogni gruppo di sincronizzazione da cui è stato rimosso.  
3. Distribuire ogni gruppo di sincronizzazione interessato (questa operazione esegue il provisioning del database).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a>Modifica di un gruppo di sincronizzazione

Non provare a rimuovere un database da un gruppo di sincronizzazione e quindi a modificare il gruppo senza aver prima distribuito una delle modifiche.

Rimuovere prima un database da un gruppo di sincronizzazione. Distribuire quindi la modifica e attendere che venga completato il deprovisioning. Al termine di questa operazione, è possibile modificare il gruppo di sincronizzazione e distribuire le modifiche.

Se si prova a rimuovere un database e quindi a modificare un gruppo di sincronizzazione senza aver prima distribuito una delle modifiche, una delle due operazioni avrà esito negativo. L'interfaccia del portale può essere visualizzata in modo incoerente. In questo caso, aggiornare la pagina per ripristinare lo stato corretto.

### <a name="avoid-schema-refresh-timeout"></a>Evitare il timeout di aggiornamento dello schemaAvoid schema refresh timeout

Se si dispone di uno schema complesso da sincronizzare, è possibile riscontrare un "timeout di operazione" durante un aggiornamento dello schema se il database dei metadati di sincronizzazione ha uno SKU inferiore (ad esempio: basic). 

#### <a name="solution"></a>Soluzione

Per ridurre questo problema, aumentare il database dei metadati di sincronizzazione per avere uno SKU più alto, ad esempio S3. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   Panoramica: [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md)
-   Configurare la sincronizzazione dati
    - Nel portale: [Esercitazione: Configurare la sincronizzazione dati SQL per sincronizzare i dati tra il database SQL di Azure e SQL Server in locale](sql-database-get-started-sql-data-sync.md)
    - Con PowerShell
        -  [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente di sincronizzazione dei dati: [Agente di sincronizzazione dei dati per la sincronizzazione dati SQL di Azure](sql-database-data-sync-agent.md)
-   Monitoraggio: [Monitorare la sincronizzazione dati SQL con i log di Monitoraggio di Azure](sql-database-sync-monitor-oms.md)
-   Risoluzione dei problemi: [Risolvere i problemi della sincronizzazione dati SQL di Azure](sql-database-troubleshoot-data-sync.md)
-   Aggiornare lo schema di sincronizzazione
    -   Con Transact-SQL: [Automatizzare la replica delle modifiche dello schema nella sincronizzazione dati SQL di Azure](sql-database-update-sync-schema.md)
    -   Con PowerShell: [Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente](scripts/sql-database-sync-update-schema.md)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
