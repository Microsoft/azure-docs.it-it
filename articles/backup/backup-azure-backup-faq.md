---
title: Risposte alle domande comuni
description: 'Risposte alle domande frequenti su: funzionalità di Backup di Azure, inclusi insieme di credenziali di Servizi di ripristino, elementi di cui è possibile eseguire il backup, funzionamento, crittografia e limiti. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: 039666c4b54da6ac5444f5aa5acda5f1bb438782
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80156056"
---
# <a name="azure-backup---frequently-asked-questions"></a>Domande frequenti su Backup di Azure

Questo articolo risponde alle domande comuni sul servizio Backup di Azure.

## <a name="recovery-services-vault"></a>Insieme di credenziali dei servizi di ripristino

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Esistono limiti al numero degli insiemi di credenziali che è possibile creare in ogni sottoscrizione di Azure?

Sì. Si possono creare fino a 500 insiemi di credenziali di Servizi di ripristino per area di Backup di Azure supportata per ogni sottoscrizione. Se sono necessari più insiemi di credenziali, creare una sottoscrizione aggiuntiva.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Esistono limiti al numero di server/computer che possono essere registrati in ogni insieme di credenziali?

È possibile registrare fino a 1000 macchine virtuali di Azure per insieme di credenziali. Se si usa l'agente di Backup di Microsoft Azure, è possibile registrare fino a 50 agenti MARS per ogni insieme di credenziali. È possibile registrare 50 server MAB/server DPM in un insieme di credenziali.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Quante origini dati o quanti elementi è possibile proteggere in un insieme di credenziali?

In un insieme di credenziali è possibile proteggere fino a 2000 origini dati/elementi tra tutti i carichi di lavoro (macchine virtuali IaaS, SQL, AFS e così via).
Ad esempio, se sono già state protette 500 macchine virtuali e 400 condivisioni di File di Azure nell'insieme di credenziali, è possibile proteggere solo fino a 1100 database SQL.

### <a name="how-many-policies-can-i-create-per-vault"></a>Quanti criteri è possibile creare per ogni insieme di credenziali?

È possibile creare fino a 200 criteri per ogni insieme di credenziali.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Se l'organizzazione ha un insieme di credenziali, come è possibile isolare i dati di server diversi nell'insieme di credenziali durante il ripristino dei dati?

I dati del server che si desidera ripristinare insieme usano la stessa passphrase durante la configurazione del backup. Se si desidera isolare il ripristino in uno o più server specifici, usare una passphrase solo per questi server. Ad esempio, per i server del reparto risorse umane può essere usata una passphrase, per quelli dell'ufficio contabilità un'altra e per quelli di archiviazione un'altra ancora.

### <a name="can-i-move-my-vault-between-subscriptions"></a>È possibile spostare l'insieme di credenziali tra sottoscrizioni?

Sì. Per spostare un insieme di credenziali di Servizi di ripristino, vedere questo [articolo](backup-azure-move-recovery-services-vault.md).

### <a name="can-i-move-backup-data-to-another-vault"></a>È possibile spostare i dati di backup in un altro insieme di credenziali?

No. Non è possibile spostare i dati di backup archiviati in un insieme di credenziali in un insieme di credenziali diverso.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>È possibile passare dall'archiviazione con ridondanza geografica all'archiviazione con ridondanza locale dopo un backup?

No. Un insieme di credenziali di Servizi di ripristino può solo modificare le opzioni di archiviazione prima che un backup venga archiviato.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>È possibile eseguire un ripristino a livello di elemento per le macchine virtuali di cui è stato eseguito il backup in un insieme di credenziali di Servizi di ripristino?

- Il ripristino a livello di elemento è supportato per le macchine virtuali di Azure sottoposte a backup con il servizio Backup di Azure. Per altre informazioni, vedere [questo articolo](backup-azure-restore-files-from-vm.md).
- Il ripristino a livello di elemento non è supportato per i punti di ripristino online di macchine virtuali locali di cui è stato eseguito il backup con il server di Backup di Azure o System Center DPM.

## <a name="azure-backup-agent"></a>Agente di Backup di Azure

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Dove sono reperibili le domande comuni sull'agente di Backup di Azure per il backup della macchina virtuale di Azure?

- Per l'agente in esecuzione sulle macchine virtuali di Azure, leggere queste [domande frequenti](backup-azure-vm-backup-faq.md).
- Per l'agente usato per eseguire il backup delle cartelle di file di Azure, leggere queste [domande frequenti](backup-azure-file-folder-backup-faq.md).

## <a name="general-backup"></a>Backup generale

### <a name="are-there-limits-on-backup-scheduling"></a>Esistono limiti per la pianificazione del backup?

Sì.

- È possibile eseguire fino a tre backup dei computer Windows Server o Windows al giorno. È possibile impostare i criteri di pianificazione per pianificazioni giornaliere o settimanali.
- È possibile eseguire il backup di DPM fino a due volte al giorno. È possibile impostare i criteri di pianificazione per pianificazioni giornaliere, settimanali, mensili e annuali.
- Il backup delle macchine virtuali di Azure viene eseguito una volta al giorno.

### <a name="what-operating-systems-are-supported-for-backup"></a>Quali sistemi operativi sono supportati per il backup?

Backup di Azure supporta i sistemi operativi per il backup di file, cartelle e applicazioni protetti tramite server di Backup di Azure e DPM.

**DEL sistema operativo** | **SKU** | **Dettagli**
--- | --- | ---
Workstation | |
Windows 10 a 64 bit | Enterprise, Pro, Home | I computer devono eseguire i Service Pack e gli aggiornamenti più recenti.
Windows 8.1 a 64 bit | Enterprise, Pro | I computer devono eseguire i Service Pack e gli aggiornamenti più recenti.
Windows 8 a 64 bit | Enterprise, Pro | I computer devono eseguire i Service Pack e gli aggiornamenti più recenti.
Windows 7 a 64 bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | I computer devono eseguire i Service Pack e gli aggiornamenti più recenti.
Server | |
Windows Server 2019 a 64 bit | Standard, Datacenter, Essentials | Con i service pack o gli aggiornamenti più recenti.
Windows Server 2016 a 64 bit | Standard, Datacenter, Essentials | Con i service pack o gli aggiornamenti più recenti.
Windows Server 2012 R2 a 64 bit | Standard, Datacenter, Foundation | Con i service pack o gli aggiornamenti più recenti.
Windows Server 2012 a 64 bit | Datacenter, Foundation, Standard | Con i service pack o gli aggiornamenti più recenti.
Windows Storage Server 2016 a 64 bit | Standard, Workgroup | Con i service pack o gli aggiornamenti più recenti.
Windows Storage Server 2012 R2 a 64 bit | Standard, Workgroup, Essential | Con i service pack o gli aggiornamenti più recenti.
Windows Storage Server 2012 a 64 bit | Standard, Workgroup | Con i service pack o gli aggiornamenti più recenti.
Windows Server 2008 R2 SP1 a 64 bit | Standard, Enterprise, Datacenter, Foundation | Con gli aggiornamenti più recenti.
Windows Server 2008 a 64 bit | Standard, Enterprise, Datacenter | Con gli aggiornamenti più recenti.

Backup di Azure non supporta i sistemi operativi a 32 bit.

Per i backup Linux della macchina virtuale di Azure, Backup di Azure supporta [l'elenco di distribuzioni approvate da Azure](../virtual-machines/linux/endorsed-distros.md), ad eccezione di CoreOS Linux e del sistema operativo a 32 bit. È possibile usare altre distribuzioni Bring Your Own Linux, a condizione che l'agente di macchine virtuali sia disponibile nella macchina virtuale e che sia presente il supporto per Python.

### <a name="are-there-size-limits-for-data-backup"></a>Sono previsti limiti di dimensioni per il backup dei dati?

Di seguito sono riportati i limiti di dimensioni:

Computer o sistema operativo | Limite di dimensioni dell'origine dati
--- | ---
Windows 8 o versione successiva | 54400 GB
Windows 7 |1700 GB
Windows Server 2012 o versioni successive | 54400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Macchina virtuale Azure | 16 dischi dati<br/> Per iscriversi all'anteprima privata delle macchine virtuali con più di 16 dischi (massimo 32), scrivere all'indirizzo AskAzureBackupTeam@microsoft.com <br><br> Disco dati fino a 32 TB

### <a name="how-is-the-data-source-size-determined"></a>Come vengono determinate le dimensioni dell'origine dati?

La tabella seguente illustra come vengono determinate le dimensioni di ogni origine dati.

**Origine dati** | **Dettagli**
--- | ---
Volume |Quantità di dati sottoposti a backup della macchina virtuale a volume singolo sottoposta a backup.
Database SQL Server |Dimensioni di un singolo database SQL di cui viene eseguito il backup.
SharePoint | Somma dei database di contenuto e configurazione in una farm di SharePoint di cui viene eseguito il backup.
Exchange |Somma di tutti i database di Exchange in un server di Exchange di cui viene eseguito il backup.
Stato del sistema/ripristino bare metal |Ogni copia del ripristino bare metal o dello stato del sistema del computer di cui viene eseguito il backup.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>È previsto un limite per la quantità di dati sottoposti a backup con un insieme di credenziali di Servizi di ripristino?

Non esiste alcun limite alla quantità totale di dati di cui è possibile eseguire il backup usando un insieme di credenziali di servizi di ripristino. Le singole origini dati, diverse dalle macchine virtuali di Azure, possono avere una dimensione massima di 54.400 GB. Per altre informazioni sui limiti, vedere la [sezione limiti dell'insieme di credenziali nella matrice di supporto](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support).

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Perché le dimensioni dei dati trasferiti nell'insieme di credenziali di Servizi di ripristino sono inferiori a quelle dei dati selezionati per il backup?

Tutti i dati di cui viene eseguito il backup dall'agente di Backup di Azure, da DPM o dal server di Backup di Azure vengono compressi e crittografati prima di essere trasferiti. Dopo aver applicato la compressione e la crittografia, i dati nell'insieme di credenziali sono ridotti del 30-40%.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>È possibile eliminare singoli file da un punto di recupero nell'insieme di credenziali?

No, Backup di Azure non supporta l'eliminazione o la cancellazione di singoli elementi dai backup archiviati.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Se si annulla un processo di backup dopo averlo avviato, i dati di backup trasferiti vengono eliminati?

No. Tutti i dati trasferiti nell'insieme di credenziali prima dell'annullamento del processo di backup rimangono nell'insieme di credenziali.

- Backup di Azure usa un meccanismo di checkpoint per aggiungere occasionalmente checkpoint ai dati di backup durante il backup.
- Dato che sono presenti checkpoint nei dati di backup, il processo di backup successivo può convalidare l'integrità dei file.
- Il processo di backup successivo sarà incrementale nei backup di dati eseguiti in precedenza. I backup incrementali trasferiscono solo dati nuovi o modificati, il che equivale a un migliore utilizzo della larghezza di banda.

Se si annulla un processo di backup per una macchina virtuale di Azure, tutti i dati trasferiti vengono ignorati. Il processo di backup successivo trasferisce i dati incrementali dall'ultimo processo di backup riuscito.

## <a name="retention-and-recovery"></a>Conservazione e ripristino

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>I criteri di conservazione per i computer DPM e i computer Windows senza DPM sono gli stessi?

Sì, prevedono entrambi criteri di conservazione giornalieri, settimanali, mensili e annuali.

### <a name="can-i-customize-retention-policies"></a>È possibile personalizzare i criteri di conservazione?

Sì, è possibile personalizzare i criteri. Ad esempio, è possibile configurare requisiti di conservazione settimanali e giornalieri, ma non annuali e mensili.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>È possibile usare orari diversi per la pianificazione del backup e i criteri di conservazione?

No. I criteri di conservazione possono essere applicati solo ai punti di backup. Ad esempio, questa immagine mostra un criterio di conservazione per i backup eseguiti alle 12:00 e alle 18.00.

![Pianifica backup e conservazione](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Se un backup viene conservato a lungo, è necessario più tempo per ripristinare un punto dati meno recente?

No. Il tempo necessario per ripristinare il punto meno recente o il più recente è lo stesso. Ogni punto di ripristino si comporta come un punto completo.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Se ogni punto di ripristino si comporta come un punto completo, questo influisce sul totale dell'archiviazione di backup fatturabile?

I punti di conservazione tipici a lungo termine archiviano i dati di backup come punti completi.

- I punti completi sono *inefficienti* dal punto di vista dell'archiviazione, ma consentono un ripristino più facile e veloce.
- Le copie incrementali sono *efficienti* dal punto di vista dell'archiviazione, ma richiedono il ripristino di una catena di dati, che influisce sui tempi di ripristino

L'architettura di archiviazione di Backup di Azure offre il meglio dei due mondi, garantendo un'archiviazione dei dati ottimale per ripristini veloci e costi di archiviazione ridotti. Questo assicura che la larghezza di banda in ingresso e in uscita venga usata in modo efficiente. La quantità dell'archivio dati e il tempo necessario per ripristinare i dati vengono mantenuti a un livello minimo. Altre informazioni sui [backup incrementali](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Esiste un limite al numero di punti di ripristino che è possibile creare?

Per ogni istanza protetta, è possibile creare fino a 9999 punti di ripristino. Un'istanza protetta è un computer, un server fisico o virtuale o un carico di lavoro che esegue il backup in Azure.

- Altre informazioni sul [backup e la conservazione](./backup-support-matrix.md).

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Quante volte è possibile ripristinare i dati di cui viene eseguito il backup in Azure?

Non esistono limiti al numero di ripristini da Backup di Azure.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Quando si ripristinano i dati, vengono addebitati costi per il traffico in uscita da Azure?

No. Il ripristino è gratuito e non viene addebitato alcun costo per il traffico in uscita.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Che cosa succede quando si modificano i criteri di backup?

Quando vengono applicati nuovi criteri, vengono seguite la pianificazione e la conservazione stabilite dai nuovi criteri.

- Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati in modo che vengano mantenuti in base ai nuovi criteri.
- Se il periodo di conservazione viene ridotto, vengono contrassegnati per l'eliminazione ed eliminati nel successivo processo di pulizia.

## <a name="encryption"></a>Crittografia

### <a name="is-the-data-sent-to-azure-encrypted"></a>I dati inviati in Azure sono crittografati?

Sì. I dati vengono crittografati nel computer locale con AES256. I dati vengono inviati tramite un collegamento HTTPS protetto. I dati trasmessi nel cloud sono protetti dal collegamento HTTPS solo tra i servizi di archiviazione e ripristino. Il protocollo iSCSI consente di proteggere i dati trasmessi tra il servizio di ripristino e il computer utente. Il tunneling protetto viene usato per proteggere il canale iSCSI.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Anche i dati di backup in Azure sono crittografati?

Sì. I dati in Azure vengono crittografati a riposo.

- Per il backup in locale, la crittografia dei dati inattivi viene eseguita con l'uso della passphrase immessa durante il backup in Azure.
- Per le macchine virtuali di Azure, i dati inattivi sono crittografati usando la crittografia del servizio di archiviazione.

Microsoft non decrittografa mai i dati di backup.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Qual è la lunghezza minima di crittografia usata dalla chiave per crittografare i dati di backup?

Quando si usa l'agente di Backup di Azure, la chiave di crittografia deve contenere almeno 16 caratteri. Per le macchine virtuali di Azure non c'è un limite di lunghezza delle chiavi usate da Azure Key Vault.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Cosa accade se si smarrisce la chiave di crittografia? È possibile ripristinare i dati? Microsoft può ripristinare i dati?

La chiave usata per crittografare i dati di backup è presente solo sul posto. Microsoft non ne conserva una copia in Azure e non dispone dell'accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.

## <a name="next-steps"></a>Passaggi successivi

Leggere le altre domande frequenti:

- [Domande comuni](backup-azure-vm-backup-faq.md) sul backup della macchina virtuale di Azure.
- [Domande comuni](backup-azure-file-folder-backup-faq.md) sull'agente di Backup di Azure
