---
title: Risolvere i problemi di SQL Server backup del database
description: Informazioni sulla risoluzione dei problemi relativi al backup di database di SQL Server eseguiti su macchine virtuali di Azure con Backup di Azure.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 2cf0ed0200de9b2787f5d9f38bd343f93648bc78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99557736"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Risolvere i problemi di SQL Server backup del database con backup di Azure

Questo articolo fornisce informazioni sulla risoluzione dei problemi per SQL Server database in esecuzione in macchine virtuali di Azure.

Per altre informazioni sul processo di backup e sulle limitazioni, vedere [informazioni su SQL Server backup in macchine virtuali di Azure](sql-support-matrix.md#feature-considerations-and-limitations).

## <a name="sql-server-permissions"></a>Autorizzazioni di SQL Server

Per configurare la protezione per un database di SQL Server in una macchina virtuale, è necessario installare l'estensione **AzureBackupWindowsWorkload** in tale macchina virtuale. Se si riceve l'errore **UserErrorSQLNoSysadminMembership**, significa che l'istanza di SQL Server non dispone delle autorizzazioni necessarie per il backup. Per correggere l'errore, seguire la procedura descritta in [impostare le autorizzazioni della macchina virtuale](backup-azure-sql-database.md#set-vm-permissions).

## <a name="troubleshoot-discover-and-configure-issues"></a>Risolvere i problemi di individuazione e configurazione

Dopo aver creato e configurato un insieme di credenziali di servizi di ripristino, l'individuazione dei database e la configurazione del backup sono un processo in due passaggi.<br>

![Obiettivo di backup-SQL Server in una macchina virtuale di Azure](./media/backup-azure-sql-database/sql.png)

Durante la configurazione del backup, se la macchina virtuale SQL e le relative istanze non sono visibili nei database **di individuazione nelle VM** e **configurare il backup** (vedere l'immagine precedente), verificare che:

### <a name="step-1-discovery-dbs-in-vms"></a>Passaggio 1: individuazione di database nelle macchine virtuali

- Se la macchina virtuale non è elencata nell'elenco di VM individuate e non è registrata per il backup SQL in un altro insieme di credenziali, seguire i passaggi di [individuazione SQL Server Backup](./backup-sql-server-database-azure-vms.md#discover-sql-server-databases) .

### <a name="step-2-configure-backup"></a>Passaggio 2: configurare il backup

- Se l'insieme di credenziali in cui è registrata la VM SQL nello stesso insieme di credenziali usato per proteggere i database, seguire le istruzioni per la [configurazione del backup](./backup-sql-server-database-azure-vms.md#configure-backup) .

Se la VM SQL deve essere registrata nel nuovo insieme di credenziali, è necessario annullarne la registrazione dall'insieme di credenziali precedente.  Per annullare la registrazione di una macchina virtuale SQL dall'insieme di credenziali, è necessario arrestare la protezione di tutte le origini dati protette e quindi eliminare i dati di cui è stato eseguito il backup. L'eliminazione dei dati di cui è stato eseguito il backup è un'operazione distruttiva.  Dopo aver esaminato ed eseguito tutte le precauzioni per annullare la registrazione della VM SQL, registrare la stessa VM con un nuovo insieme di credenziali e ripetere l'operazione di backup.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Risolvere i problemi di backup e ripristino  

In alcuni casi, è possibile che si verifichino errori casuali nelle operazioni di backup e ripristino oppure che tali operazioni rimangano bloccate. Questa operazione potrebbe essere dovuta a programmi antivirus nella macchina virtuale. Come procedura consigliata, è consigliabile eseguire le operazioni seguenti:

1. Escludere le cartelle seguenti dall'analisi antivirus:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.AzureBackupWindowsWorkload`

    Sostituire `C:\` con la lettera di *SystemDrive*.

1. Escludere i seguenti tre processi in esecuzione all'interno di una macchina virtuale dall'analisi antivirus:

    - IaasWLPluginSvc.exe
    - IaaSWorkloadCoordinatorService.exe
    - TriggerExtensionJob.exe

1. In SQL sono inoltre disponibili alcune linee guida per l'utilizzo dei programmi antivirus. Per informazioni dettagliate, vedere [questo articolo](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) .

## <a name="faulty-instance-in-a-vm-with-multiple-sql-server-instances"></a>Istanza difettosa in una macchina virtuale con più istanze di SQL Server

È possibile eseguire il ripristino in una macchina virtuale SQL solo se tutte le istanze di SQL in esecuzione nella macchina virtuale sono segnalate integre. Se una o più istanze sono "difettose", la macchina virtuale non verrà visualizzata come destinazione di ripristino. Questo potrebbe essere il motivo per cui è possibile che una macchina virtuale a istanze diverse non venga visualizzata nell'elenco a discesa "Server" durante l'operazione di ripristino.

È possibile convalidare la "conformità del backup" di tutte le istanze di SQL nella macchina virtuale, in **Configura backup**:

![Convalidare la conformità del backup](./media/backup-sql-server-azure-troubleshoot/backup-readiness.png)

Se si vuole attivare un ripristino nelle istanze di SQL integre, seguire questa procedura:

1. Accedere alla macchina virtuale SQL e andare a `C:\Program Files\Azure Workload Backup\bin` .
1. Creare un file JSON denominato, `ExtensionSettingsOverrides.json` se non è già presente. Se il file è già presente nella macchina virtuale, continuare a usarlo.
1. Aggiungere il contenuto seguente nel file JSON e salvare il file:

    ```json
    {
                  "<ExistingKey1>":"<ExistingValue1>",
                    …………………………………………………… ,
              "whitelistedInstancesForInquiry": "FaultyInstance_1,FaultyInstance_2"
            }
            
            Sample content:        
            { 
              "whitelistedInstancesForInquiry": "CRPPA,CRPPB "
            }

    ```

1. Attivare l'operazione di **riindividuazione** dei database nel server interessato dalla portale di Azure (la stessa posizione in cui è possibile visualizzare la preparazione per il backup). La macchina virtuale verrà avviata come destinazione per le operazioni di ripristino.

    ![Riindividuare i database](./media/backup-sql-server-azure-troubleshoot/rediscover-dbs.png)

1. Rimuovere la voce *whitelistedInstancesForInquiry* dal ExtensionSettingsOverrides.jssul file al termine dell'operazione di ripristino.

## <a name="error-messages"></a>messaggi di errore

### <a name="backup-type-unsupported"></a>Tipo di backup non supportato

| Gravità | Descrizione | Possibili cause | Azione consigliata |
|---|---|---|---|
| Avviso | Le impostazioni correnti per questo database non supportano determinati tipi di backup presenti nei criteri associati. | <li>Sul database master è possibile eseguire solo un'operazione di backup completo del database. Il backup differenziale e il backup del log delle transazioni non sono possibili. </li> <li>Qualsiasi database nel modello di recupero con registrazione minima non consente il backup dei log delle transazioni.</li> | Modificare le impostazioni del database in modo che tutti i tipi di backup nei criteri siano supportati. In alternativa, modificare i criteri correnti in modo da includere solo i tipi di backup supportati. In caso contrario, i tipi di backup non supportati verranno ignorati durante il backup pianificato oppure il processo di backup non riuscirà per il backup su richiesta.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non supporta il tipo di backup richiesto. | Si verifica quando il modello di recupero del database non consente il tipo di backup richiesto. L'errore può verificarsi nelle situazioni seguenti: <br/><ul><li>Un database che utilizza un modello di recupero con registrazione minima non consente il backup del log.</li><li>I backup differenziali e del log non sono consentiti per un database master.</li></ul>Per informazioni dettagliate, vedere la documentazione relativa ai [modelli di recupero SQL Server](/sql/relational-databases/backup-restore/recovery-models-sql-server) . | Se il backup del log non riesce per il database nel modello di recupero con registrazione minima, provare una delle opzioni seguenti:<ul><li>Se il database è in modalità di recupero con registrazione minima, disabilitare i backup del log.</li><li>Utilizzare la [documentazione di SQL Server](/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) per modificare il modello di recupero del database in completo o con registrazione minima delle operazioni bulk. </li><li> Se non si vuole modificare il modello di recupero ed esistono criteri standard per eseguire il backup più database che non possono essere modificati, ignorare l'errore. I backup completi e differenziali funzioneranno come da pianificazione. Verranno ignorati i backup del log, come previsto in questo caso.</li></ul>Se si tratta di un database master ed è stato configurato il backup differenziale o del log, usare uno dei passaggi seguenti:<ul><li>Usare il portale per modificare la pianificazione dei criteri di backup per il database master in full.</li><li>Se esistono criteri standard per eseguire il backup di più database che non possono essere modificati, ignorare l'errore. Il backup completo funzionerà come da pianificazione. Non verranno eseguiti i backup differenziali e del log, come previsto in questo caso.</li></ul> |
| L'operazione è stata annullata perché è già in esecuzione un'operazione in conflitto nello stesso database. | Vedere il [post di Blog sulle limitazioni di backup e ripristino](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) eseguite simultaneamente.| [Usare SQL Server Management Studio (SSMS) per monitorare i processi di backup](manage-monitor-sql-database-backup.md). Quando l'operazione in conflitto ha esito negativo, riavviare l'operazione.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non esiste. | Il database è stato eliminato o rinominato. | Controllare se il database è stato eliminato o rinominato accidentalmente.<br/><br/> Se il database è stato eliminato accidentalmente, per continuare con i backup, ripristinare il database nel percorso originale.<br/><br/> Se il database è stato eliminato e non sono necessari backup futuri, nell'insieme di credenziali di servizi di ripristino selezionare **Interrompi backup** con **Mantieni dati di backup** o **Elimina dati di backup**. Per ulteriori informazioni, vedere [gestire e monitorare i database SQL Server](manage-monitor-sql-database-backup.md)sottoposti a backup.

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| La catena di log è interrotta. | Viene eseguito il backup del database o della macchina virtuale tramite un'altra soluzione di backup, che tronca la catena di log.|<ul><li>Verificare se è in uso un'altra soluzione di backup o uno script. In tal caso, arrestare l'altra soluzione di backup. </li><li>Se il backup era un backup del log su richiesta, attivare un backup completo per avviare una nuova catena di log. Per i backup del log pianificati, non è necessaria alcuna azione perché il servizio backup di Azure attiverà automaticamente un backup completo per risolvere il problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il servizio Backup di Azure non riesce a connettersi all'istanza SQL. | Backup di Azure non è in grado di connettersi all'istanza di SQL Server. | Per restringere le cause principali, utilizzare i dettagli aggiuntivi disponibili nel menu portale di Azure Error. Fare riferimento a [Risolvere i problemi di connessione al motore di database di SQL Server](/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) per correggere l'errore.<br/><ul><li>Se le impostazioni SQL predefinite non consentono le connessioni remote, modificare le impostazioni. Per informazioni sulla modifica delle impostazioni, vedere gli articoli seguenti:<ul><li>[MSSQLSERVER_-1](/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Se si verificano problemi di accesso, usare i collegamenti seguenti per correggerli:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Manca il primo backup completo per questa origine dati. | Manca un backup completo per il database. I backup di log e differenziali sono elementi padre di un backup completo, quindi assicurarsi di eseguire backup completi prima di attivare backup differenziali o del log. | Attivare un backup completo su richiesta.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Cannot take backup as transaction log for the data source is full (Non è possibile eseguire il backup perché il log delle transazioni per l'origine dati è pieno). | Lo spazio del log delle transazioni del database è pieno. | Per risolvere il problema, fare riferimento alla [documentazione di SQL Server](/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Un database con lo stesso nome esiste già nel percorso di destinazione | Per la destinazione di ripristino di destinazione è già presente un database con lo stesso nome.  | <ul><li>Modificare il nome del database di destinazione.</li><li>In alternativa, usare l'opzione forza sovrascrittura nella pagina Ripristina.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il ripristino non è riuscito perché non è stato possibile portare offline il database. | Quando si esegue un ripristino, il database di destinazione deve essere portato offline. Backup di Azure non è in grado di portare offline questi dati. | Per restringere le cause principali, utilizzare i dettagli aggiuntivi disponibili nel menu portale di Azure Error. Per altre informazioni, vedere la [documentazione di SQL Server](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="wlextgenericiofaultusererror"></a>WlExtGenericIOFaultUserError

|Messaggio di errore |Possibili cause  |Azione consigliata  |
|---------|---------|---------|
|Si è verificato un errore di input/output durante l'operazione. Verificare la presenza di errori di IO comuni nella macchina virtuale.   |   Autorizzazioni di accesso o vincoli di spazio nella destinazione.       |  Verificare la presenza di errori di IO comuni nella macchina virtuale. Verificare che l'unità di destinazione o la condivisione di rete nel computer: <li> dispone dell'autorizzazione di lettura/scrittura per l'account NT AUTHORITY\SYSTEM nel computer. <li> dispone di spazio sufficiente per il corretto completamento dell'operazione.<br> Per ulteriori informazioni, vedere [Restore As files](restore-sql-database-azure-vm.md#restore-as-files).
       |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Cannot find the server certificate with thumbprint on the target (Non è possibile trovare il certificato del server con l'identificazione personale). | Il database master nell'istanza di destinazione non dispone di un'identificazione personale di crittografia valida. | Importare nell'istanza di destinazione l'identificazione personale del certificato valida utilizzata nell'istanza di di origine. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il backup del log usato per il ripristino contiene modifiche con registrazione minima delle operazioni bulk. Non può essere usato per arrestare in un punto nel tempo arbitrario in base alle linee guida di SQL. | Quando un database è in modalità di recupero con registrazione minima delle operazioni bulk, i dati tra una transazione con registrazione minima delle operazioni bulk e la transazione di log successiva non possono essere recuperati. | Scegliere un momento diverso per il ripristino. [Altre informazioni](/sql/relational-databases/backup-restore/recovery-models-sql-server)

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Non è possibile soddisfare le preferenze di backup per il gruppo di disponibilità AlwaysOn SQL perché alcuni nodi del gruppo di disponibilità non sono registrati. | I nodi necessari per eseguire i backup non sono registrati o non sono raggiungibili. | <ul><li>Verificare che tutti i nodi necessari per eseguire i backup del database siano registrati e integri, quindi ripetere l'operazione.</li><li>Modificare le preferenze di backup per il gruppo di disponibilità SQL Server Always On.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| L'istanza di SQL Server è stata arrestata e non è accessibile al servizio Backup di Azure. | La macchina virtuale viene arrestata. | Verificare che l'istanza di SQL Server sia in esecuzione. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il servizio Backup di Azure usa l'agente guest di macchine virtuali di Azure per l'esecuzione del backup ma l'agente guest non è disponibile nel server di destinazione. | L'agente guest non è abilitato o non è integro. | [Installare l'agente guest di macchine virtuali](../virtual-machines/extensions/agent-windows.md) manualmente. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| La finalità di protezione automatica è stata rimossa o non è più valida. | Quando si Abilita la protezione automatica in un'istanza di SQL Server, configurare i processi di **backup** eseguiti per tutti i database in tale istanza. Se si disabilita la protezione automatica mentre i processi sono in esecuzione, i processi **in corso** vengono annullati con questo codice di errore. | Abilitare di nuovo la protezione automatica per proteggere tutti i database rimanenti. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
L'operazione è bloccata perché è stato raggiunto il limite per il numero di operazioni consentite in 24 ore. | Quando è stato raggiunto il limite massimo consentito per un'operazione in un intervallo di 24 ore, viene visualizzato questo errore. <br> Ad esempio, se è stato raggiunto il limite per il numero di processi di backup di configurazione che possono essere attivati al giorno e si tenta di configurare il backup su un nuovo elemento, verrà visualizzato questo errore. | In genere, la ripetizione dell'operazione dopo 24 ore risolve questo problema. Tuttavia, se il problema persiste, è possibile contattare il supporto tecnico Microsoft per assistenza.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
L'operazione è bloccata perché l'insieme di credenziali ha raggiunto il limite massimo per le operazioni consentite in un intervallo di 24 ore. | Quando è stato raggiunto il limite massimo consentito per un'operazione in un intervallo di 24 ore, viene visualizzato questo errore. Questo errore viene in genere visualizzato quando sono presenti operazioni su larga scala, ad esempio la modifica dei criteri o la protezione automatica. A differenza del caso di CloudDosAbsoluteLimitReached, non è possibile eseguire molte operazioni per risolvere questo stato. In realtà, il servizio backup di Azure tenterà di ritentare le operazioni internamente per tutti gli elementi in questione.<br> Ad esempio, se si dispone di un numero elevato di origini dati protette con un criterio e si tenta di modificare tale criterio, verrà attivata la configurazione dei processi di protezione per ciascuno degli elementi protetti e talvolta potrebbe verificarsi il limite massimo consentito per tali operazioni al giorno.| Il servizio backup di Azure tenterà automaticamente di ripetere l'operazione dopo 24 ore.

### <a name="workloadextensionnotreachable"></a>WorkloadExtensionNotReachable

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
Operazione di estensione del carico di lavoro AzureBackup non riuscita. | La macchina virtuale viene arrestata o la macchina virtuale non riesce a contattare il servizio backup di Azure a causa di problemi di connettività Internet.| <li> Assicurarsi che la macchina virtuale sia in esecuzione e che disponga della connettività Internet.<li> [Ripetere la registrazione dell'estensione nella macchina virtuale SQL Server](manage-monitor-sql-database-backup.md#re-register-extension-on-the-sql-server-vm).


### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
La macchina virtuale non è in grado di contattare il servizio backup di Azure a causa di problemi di connettività Internet. | Per la macchina virtuale è necessaria la connettività in uscita al servizio backup di Azure, archiviazione di Azure o servizi Azure Active Directory.| <li> Se si usa NSG per limitare la connettività, è necessario usare il tag del servizio *AzureBackup* per consentire l'accesso in uscita al servizio backup di Azure e allo stesso modo per i servizi di Azure ad (*AzureActiveDirectory*) e archiviazione di Azure (*archiviazione*). Per concedere l'accesso, seguire questa [procedura](./backup-sql-server-database-azure-vms.md#nsg-tags) . <li> Verificare che il DNS stia risolvendo gli endpoint di Azure. <li> Controllare se la macchina virtuale si trova dietro un servizio di bilanciamento del carico che blocca l'accesso a Internet. Assegnando un indirizzo IP pubblico alle macchine virtuali, l'individuazione funzionerà. <li> Verificare che non esistano firewall/antivirus/proxy che bloccano le chiamate ai tre servizi di destinazione precedenti.

## <a name="re-registration-failures"></a>Errori di ripetizione della registrazione

Prima di attivare l'operazione di ripetizione della registrazione, verificare la presenza di uno o più dei sintomi seguenti:

- Tutte le operazioni, ad esempio backup, ripristino e configurazione del backup, non riescono nella macchina virtuale con uno dei codici di errore seguenti: **[WorkloadExtensionNotReachable](#workloadextensionnotreachable)**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
- Se nell'area **Stato backup** relativa all'elemento di backup è visualizzato il messaggio **Non raggiungibile**, escludere tutte le altre cause che potrebbero comportare lo stesso stato:

  - Mancanza di autorizzazioni per eseguire operazioni relative al backup nella macchina virtuale.
  - Arrestare la macchina virtuale, quindi non è possibile eseguire i backup.
  - [Problemi di rete](#usererrorvminternetconnectivityissue)

   ![ripetizione della registrazione della macchina virtuale](./media/backup-azure-sql-database/re-register-vm.png)

- Nel caso di un gruppo di disponibilità di Always On, i backup hanno avuto esito negativo dopo aver modificato la preferenza di backup o dopo un failover.

Questi sintomi possono comparire per uno o più dei motivi seguenti:

- Un'estensione è stata eliminata o disinstallata dal portale.
- Un'estensione è stata disinstallata dal **Pannello di controllo** nella macchina virtuale in **Disinstalla o modifica programma**.
- La macchina virtuale è stata ripristinata a un punto precedente tramite il ripristino del disco sul posto.
- La macchina virtuale si trova in uno stato di arresto da molto tempo e quindi la configurazione dell'estensione è scaduta.
- La macchina virtuale è stata eliminata e ne è stata creata un'altra con lo stesso nome e nello stesso gruppo di risorse di quella eliminata.
- Uno dei nodi del gruppo di disponibilità non ha ricevuto la configurazione del backup completo. Questo problema può verificarsi quando il gruppo di disponibilità viene registrato nell'insieme di credenziali o quando viene aggiunto un nuovo nodo.

Negli scenari precedenti è consigliabile attivare un'operazione di ripetizione della registrazione nella macchina virtuale. Per istruzioni su come eseguire questa attività in PowerShell, vedere [qui](./backup-azure-sql-automation.md#enable-backup) .

## <a name="size-limit-for-files"></a>Limite dimensioni per i file

Le dimensioni totali della stringa dei file dipendono non solo dal numero di file, ma anche dai rispettivi nomi e percorsi. Per ogni file di database, ottenere il nome e il percorso fisico del file logico. È possibile usare questa query SQL:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Ora è possibile disporli nel formato seguente:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Ecco un esempio:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Se le dimensioni della stringa del contenuto superano 20.000 byte, i file di database vengono archiviati in modo diverso. Durante il ripristino, non sarà possibile impostare il percorso del file di destinazione per il ripristino. I file verranno ripristinati nel percorso SQL predefinito fornito da SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Esegui override del percorso predefinito del file di ripristino di destinazione

È possibile eseguire l'override del percorso del file di ripristino di destinazione durante l'operazione di ripristino inserendo un file JSON che contiene il mapping del file di database al percorso di ripristino di destinazione. Creare un `database_name.json` file e inserirlo nella posizione `C:\Program Files\Azure Workload Backup\bin\plugins\SQL*` .

Il contenuto del file deve essere nel formato seguente:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Ecco un esempio:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

Nel contenuto precedente è possibile ottenere il nome logico del file di database usando la query SQL seguente:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
```

Questo file deve essere inserito prima di attivare l'operazione di ripristino.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su backup di Azure per le macchine virtuali SQL Server (anteprima pubblica), vedere [backup di Azure per macchine virtuali SQL](../azure-sql/virtual-machines/windows/backup-restore.md#azbackup).
