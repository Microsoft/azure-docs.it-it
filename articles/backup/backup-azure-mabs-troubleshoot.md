---
title: Risolvere i problemi del server di Backup di Azure
description: Risolvere i problemi di installazione e registrazione del server di Backup di Azure ed eseguire backup e ripristino dei carichi di lavoro delle applicazioni.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: cc0cf7e91c1aacbc637d33ab1e5546cc54836b28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673123"
---
# <a name="troubleshoot-azure-backup-server"></a>Risolvere i problemi del server di Backup di Azure

Fare riferimento alle informazioni elencate nelle tabelle seguenti possibile risolvere gli errori rilevati durante l'uso di server di Backup di Azure.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

Si consiglia di eseguire la convalida seguente, prima di avviare la risoluzione dei problemi di Microsoft Azure Backup Server (MABS):

- [Verificare che l'agente di Microsoft Azure Recovery Services (MARS) sia aggiornatoEnsure Microsoft Azure Recovery Services (MARS) Agent is up to date](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Verificare la presenza di connettività di rete tra l'agente dei Servizi di ripristino di Microsoft Azure e Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Verificare che i Servizi di ripristino di Microsoft Azure siano in esecuzione (nella console di Servizio). Se necessario, riavviare e ritentare l'operazione
- [Verificare che sia disponibile il 5-10% di volume libero nel percorso della cartella dei file temporanei](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- Se la registrazione non riesce, verificare che il server in cui si sta tentando di installare il server di Backup di Azure non sia già registrato con un altro insieme di credenziali
- Se l'installazione del push ha esito negativo, verificare se l'agente DPM è già presente. In caso affermativo, disinstallare l'agente e provare a ripetere l'installazione
- [Verificare che nessun altro processo o software antivirus interferisca con Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Verificare che il servizio SQL Agent sia in esecuzione automatica nel server MAB<br>

## <a name="invalid-vault-credentials-provided"></a>Sono state specificate credenziali dell'insieme di credenziali non valide

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Registrazione in un insieme di credenziali | Sono state specificate credenziali di insieme non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. | Azione consigliata: <br> <ul><li> Scaricare il file di credenziali più recente dall'insieme e riprovare. <br>OPPURE</li> <li> Se il problema non viene risolto, provare a scaricare le credenziali in una directory locale diversa o creare un nuovo insieme di credenziali. <br>OPPURE</li> <li> Provare ad aggiornare le impostazioni di data e ora come descritto in [questo articolo.](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#invalid-vault-credentials-provided) <br>OPPURE</li> <li> Controllare se il percorso c:\windows\temp contenga più di 65000 file. Spostare i file non aggiornati in un altro percorso o eliminare gli elementi nella cartella Temp. <br>OPPURE</li> <li> Controllare lo stato dei certificati. <br> a. Aprire **Gestisci i certificati computer** nel Pannello di controllo. <br> b. Espandere il nodo **Personale** e il relativo nodo figlio **Certificati**.<br> c.  Rimuovere il certificato **Strumenti di Windows Azure**. <br> d. Ripetere la registrazione nel client di Backup di Azure. <br> OPPURE </li> <li> Verificare se siano applicati eventuali criteri di gruppo. </li></ul> |

## <a name="replica-is-inconsistent"></a>La replica è incoerente

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | La replica è incoerente | Verificare che l'opzione relativa alla verifica di coerenza automatica nella procedura guidata Gruppo di protezione sia attivata. Per ulteriori informazioni sulle cause dell'incoerenza della replica e sui suggerimenti pertinenti, vedere l'articolo [Replica è incoerente.](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2006/cc161593(v=technet.10))<br> <ol><li> In caso di backup dello stato del sistema o di ripristino bare metal, verificare che Windows Server Backup sia installato nel server protetto.</li><li> Verificare la presenza di problemi di spazio nel pool di archiviazione DPM del server DPM o di Backup di Microsoft Azure e allocare memoria in base alle necessità.</li><li> Controllare lo stato del servizio Copia Shadow del volume nel server protetto. Se è disabilitato, impostarlo per l'avvio manuale. Avviare il servizio sul server. Tornare quindi alla console di DPM o del server di Backup di Microsoft Azure e avviare la sincronizzazione con il processo di verifica della coerenza.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Impossibile creare il punto di ripristino online

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | Impossibile creare il punto di ripristino online | **Messaggio di errore:** Impossibile creare uno snapshot del volume selezionato nell'agente backup di Windows Azure. <br> **Soluzione alternativa:** provare ad aumentare lo spazio nel volume di replica e punto di ripristino.<br> <br> **Messaggio di errore:** Impossibile connettersi all'agente di Windows Azure Backup al servizio OBEngine <br> **Soluzione alternativa**: verificare che OBEngine sia presente nell'elenco dei servizi in esecuzione nel computer. Se il servizio OBEngine non è in esecuzione, usare il comando "net start OBEngine" per avviarlo. <br> <br> **Messaggio di errore:** La passphrase di crittografia per questo server non è impostata. Configurare una passphrase di crittografia. <br> **Soluzione alternativa:** provare a configurare una passphrase di crittografia. Se l'operazione non riesce, seguire questa procedura: <br> <ol><li>Verificare che il percorso dei file temporanei esista. Questo è il percorso indicato nel Registro di sistema in **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config** con il nome **ScratchLocation**.</li><li> Se il percorso dei file temporanei esiste, provare di nuovo a eseguire la registrazione con la passphrase precedente. *Quando si configura una passphrase di crittografia, salvarla in un luogo sicuro.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>I server DPM originali ed esterni devono essere registrati nello stesso insieme di credenziali

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Restore | **Codice di errore**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Messaggio di errore:** I server DPM originali ed esterni devono essere registrati nello stesso insieme di credenziali | **Causa**: Questo problema si verifica quando si tenta di ripristinare i file al server alternativo dal server originale utilizzando l'opzione di ripristino DPM esterno e se il server che viene ripristinato e il server originale da cui viene eseguito il backup dei dati non sono associati allo stesso insieme di credenziali del servizio di ripristino.<br/> <br/>**Soluzione alternativa** Per risolvere questo problema, assicurarsi che il server originale e il server alternativo siano registrati nello stesso insieme di credenziali.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>I processi di creazione dei punti di ripristino online per VM VMware non riescono

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | I processi di creazione dei punti di recupero online per VM VMware non riescono. DPM ha riscontrato un errore di VMWare durante il tentativo di ottenere informazioni di rilevamento modifiche. ErrorCode - FileFaultFault (ID 33621) |  <ol><li> Reimpostare il rilevamento modifiche in VMWare per le VM interessate.</li> <li>Controllare che in VMWare non sia presente un disco indipendente.</li> <li>Arrestare la protezione per le macchine virtuali interessate e riproteggere con il pulsante **Aggiorna.Stop** protection for the affected VMs and reprotect with the Refresh button. </li><li>Eseguire una verifica di coerenza per le VM interessate.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operazione agente non riuscita a causa di un errore di comunicazione con il servizio coordinatore agenti DPM nel server

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Push degli agenti in server protetti | Operazione agente non riuscita a causa di un errore di comunicazione con il servizio Coordinatore agenti DPM in \<ServerName>. | **Se l'azione consigliata visualizzata nel prodotto non funziona, seguire questa procedura**: <ul><li> Se si collega un computer da un dominio non attendibile, attenersi alla [seguente procedura](https://docs.microsoft.com/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019). <br> OPPURE </li><li> Se si collega un computer da un dominio trusted, risolvere i problemi utilizzando la procedura descritta in [questo blog](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>OPPURE</li><li> Provare a disabilitare l'antivirus come passaggio per la risoluzione dei problemi. Se il problema viene risolto, modificare le impostazioni dell'antivirus come suggerito in [questo articolo](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12)).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Non è stato possibile aggiornare i metadati del Registro di sistema

| Operazione | Dettagli errore | Soluzione alternativa |
|-----------|---------------|------------|
|Installazione | Non è stato possibile aggiornare i metadati del Registro di sistema. Questo errore di aggiornamento potrebbe causare un utilizzo eccessivo dell'archiviazione. Per evitare questo aggiornamento della voce del Registro di sistema ReFS Trimming. | Regolare la chiave del Registro di sistema **SYSTEM , CurrentControlSet , Control , FileSystem , RefsEnableInlineTrim**. Impostare il valore DWORD su 1. |
|Installazione | Non è stato possibile aggiornare i metadati del Registro di sistema. Questo errore di aggiornamento potrebbe causare un utilizzo eccessivo dell'archiviazione. Per evitare questo problema, aggiornare la voce del Registro di sistema Volume SnapOptimization. | Creare la chiave del Registro di sistema **SOFTWARE , Microsoft Data Protection Manager , Configuration , VolSnapOptimization , WriteIds** con un valore di stringa vuoto. |

## <a name="registration-and-agent-related-issues"></a>Problemi relativi alla registrazione e all'agente

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Push degli agenti in server protetti | Le credenziali specificate per il server non sono valide. | **Se l'azione consigliata visualizzata nel prodotto non funziona, attenersi alla seguente procedura:** <br> Provare a installare manualmente l'agente protezione nel server di produzione come specificato in [questo articolo.](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)|
| L'agente di Backup di Azure non è stato in grado di connettersi al servizio Backup di Azure (ID: 100050) | L'agente di Backup di Azure non è stato in grado di connettersi al servizio Backup di Azure. | **Se l'azione consigliata visualizzata nel prodotto non funziona, attenersi alla seguente procedura:** <br>1. Eseguire il comando seguente da un prompt dei comandi con privilegi elevati: **psexec -i -s "c:"Program Files"Internet Explorer"internet explorer.exe**. Verrà aperta la finestra di Internet Explorer. <br/> 2. Andare su **Strumenti** > **Impostazioni** > Internet**Connections** > **LAN impostazioni**. <br/> 3. Modificare le impostazioni per utilizzare un server proxy. Indicare quindi i dettagli del server proxy.<br/> 4. Se la macchina ha un accesso a Internet limitato, verificare che le impostazioni del firewall sul computer o sul proxy [consentano](install-mars-agent.md#verify-internet-access) questi URL e [indirizzo IP.](install-mars-agent.md#verify-internet-access)|
| Installazione dell'agente di Backup di Azure non riuscita | L'installazione di Servizi di ripristino di Microsoft Azure non è riuscita. È stato eseguito il rollback di tutte le modifiche apportate al sistema dall'installazione di Servizi di ripristino di Microsoft Azure. (ID: 4024) | Installare manualmente l'agente di Azure.

## <a name="configuring-protection-group"></a>Configurazione di un gruppo protezione dati

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Configurazione di gruppi di protezione | DPM non è in grado di enumerare il componente dell'applicazione nel computer protetto (nome computer protetto). | Selezionare **Aggiorna** nella schermata dell'interfaccia utente per la configurazione del gruppo protezione dati al livello di origine dati/componente appropriato. |
| Configurazione di gruppi di protezione | Impossibile configurare la protezione | Se il server protetto è un server SQL, verificare che le autorizzazioni del ruolo sysadmin siano state fornite all'account di sistema (NTAuthority\System) del computer protetto come indicato in [questo articolo](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757977(v=sc.12)).
| Configurazione di gruppi di protezione | Non c'è spazio sufficiente nel pool di archiviazione per questo gruppo protezione dati. | I dischi aggiunti al pool di archiviazione [non devono contenere una partizione](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)). Eliminare gli eventuali volumi presenti sui dischi. Quindi aggiungerli al pool di archiviazione.|
| Modifica dei criteri |Non è possibile modificare i criteri di backup. Errore: impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x29834]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft. | **Causa:**<br/>Questo errore si verifica in tre situazioni: quando sono abilitate le impostazioni di sicurezza, quando si prova a ridurre l'intervallo di conservazione dei dati al di sotto dei valori minimi specificati in precedenza e quando si usa una versione non supportata. Le versioni non supportate sono quelle precedenti al server di Backup di Microsoft Azure 2.0.9052 e server di Backup di Azure aggiornamento 1. <br/>**Azione consigliata:**<br/> per procedere con gli aggiornamenti relativi ai criteri, impostare un periodo di conservazione superiore al periodo di conservazione minimo specificato. Il periodo di conservazione minimo è sette giorni per i backup giornalieri, quattro settimane per quelli settimanali, tre settimane per quelli mensili e un anno per quelli annuali. <br><br>Facoltativamente, per sfruttare tutti gli aggiornamenti della sicurezza, un altro approccio consigliato è aggiornare l'agente di backup e il server di Backup di Azure. |

## <a name="backup"></a>Backup

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | Si è verificato un errore imprevisto durante l'esecuzione del processo. Il dispositivo non è pronto. | **Se l'azione consigliata nel prodotto non funziona, seguire questa procedura:** <br> <ul><li>Impostare lo spazio di archiviazione della copia shadow come illimitato negli elementi del gruppo protezione dati e quindi avviare la verifica coerenza.<br></li> OPPURE <li>Provare a eliminare il gruppo protezione dati esistente e a creare più nuovi gruppi. Ogni nuovo gruppo protezione dati deve contenere un singolo elemento.</li></ul> |
| Backup | Se si sta eseguendo il backup solamente dello stato del sistema, verificare che ci sia spazio libero sufficiente nel computer protetto per archiviarlo. | <ol><li>Verificare che Windows Server Backup sia installato nel computer protetto.</li><li>Verificare che vi sia spazio sufficiente nel computer protetto per lo stato del sistema. Il modo più semplice per verificare questa condizione è passare al computer protetto, aprire Windows Server Backup, fare clic sulle selezioni e quindi selezionare il ripristino bare metal. Nell'interfaccia utente verrà indicato lo spazio necessario. Aprire **WSB** > **Backup** > schedule > **Select****Backup Configuration** > **Full server** (dimensioni visualizzate). Usare queste dimensioni per la verifica.</li></ol>
| Backup | Errore di backup per il supporto bare metal | Se le dimensioni del ripristino bare metal sono grandi, spostare alcuni file di applicazione nell'unità del sistema operativo e riprovare. |
| Backup | L'opzione per proteggere nuovamente una macchina virtuale VMware in un nuovo server di Backup di Microsoft Azure non viene visualizzata come disponibile per l'aggiunta. | Le proprietà VMware fanno riferimento a un'istanza precedente ritirata del server di Backup di Microsoft Azure. Per risolvere il problema:<br><ol><li>in VCenter (equivalente a SC VMM), passare alla scheda **Summary** e quindi a **Custom Attributes**.</li>  <li>Eliminare il vecchio nome del server di Backup di Microsoft Azure dal valore **DPMServer**.</li>  <li>Tornare al nuovo server di Backup di Microsoft Azure e modificare il PG.  Dopo aver selezionato il pulsante **Refresh**, la macchina virtuale verrà visualizzata con una casella di controllo come disponibile per l'aggiunta alla protezione.</li></ol> |
| Backup | Errore durante l'accesso a file o cartelle condivise | Provare a modificare le impostazioni antivirus come suggerito in questo articolo Eseguire il [software antivirus sul server DPM.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12))|

## <a name="change-passphrase"></a>Modificare la passphrase

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Modificare la passphrase |Il PIN di sicurezza immesso non è corretto. Specificare il PIN di sicurezza corretto per completare questa operazione. |**Causa:**<br/> Questo errore si verifica quando si immette un PIN di sicurezza non valido o scaduto durante l'esecuzione di un'operazione critica (ad esempio, se si modifica una passphrase). <br/>**Azione consigliata:**<br/> Per completare l'operazione, è necessario immettere un PIN di sicurezza valido. Per ottenere il PIN, accedere al portale di Azure e passare all'insieme di credenziali dei servizi di ripristino. Passare quindi a**Proprietà** >  **impostazioni** > **Genera PIN di sicurezza**. Usare questo PIN per modificare la passphrase. |
| Modificare la passphrase |Operazione non riuscita. ID: 120002 |**Causa:**<br/>Questo errore si verifica quando sono abilitate le impostazioni di sicurezza o quando si tenta di modificare la passphrase se si usa una versione non supportata.<br/>**Azione consigliata:**<br/> Per modificare la passphrase, è innanzitutto necessario aggiornare l'agente di backup con la versione minima, 2.0.9052. È anche necessario aggiornare il server di Backup di Azure per con la versione minima dell'aggiornamento 1 e quindi immettere un PIN di sicurezza valido. Per ottenere il PIN, accedere al portale di Azure e passare all'insieme di credenziali dei servizi di ripristino. Passare quindi a**Proprietà** >  **impostazioni** > **Genera PIN di sicurezza**. Usare questo PIN per modificare la passphrase. |

## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Impostazione delle notifiche di posta elettronica tramite un account di Office 365 |ID errore: 2013| **Causa:**<br> Tentativo di usare l'account di Office 365 <br>**Azione consigliata:**<ol><li> La prima cosa da assicurarsi è che "Consenti inoltro anonimo su un connettore di ricezione" per il server DPM sia configurato in Exchange. Per ulteriori informazioni su come configurare questa opzione, vedere [Consentire l'inoltro anonimo su un connettore](https://docs.microsoft.com/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019)di ricezione .</li> <li> Se non è possibile usare un inoltro SMTP interno ed è necessario eseguire la configurazione con il server di Office 365, è possibile configurare IIS come inoltro. Configurare il server DPM per [l'inoltro di SMTP a O365 tramite IIS](https://docs.microsoft.com/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019).<br><br>  Assicurarsi di utilizzare\@il formato domain.com utente e *non* dominio o utente.<br><br><li>Impostare DPM in modo da usare il nome del server locale come server SMTP e la porta 587. Quindi impostarlo con l'indirizzo di posta elettronica dell'utente da cui dovrebbero provenire i messaggi.<li> Il nome utente e la password nella pagina di configurazione dell'SMTP per DPM devono corrispondere a un account di dominio nel dominio in cui è attivo DPM. </li><br> quando si modifica l'indirizzo del server SMTP, assicurarsi di apportare le modifiche alle nuove impostazioni, quindi chiudere la finestra delle impostazioni e riaprirla per verificare che rifletta il nuovo valore.  Se ci si limita ad apportare la modifica ed eseguire il test, è possibile che le nuove impostazioni non vengano applicate, pertanto questo tipo di verifica è la procedura consigliata.<br><br>In qualsiasi momento durante questo processo è possibile deselezionare queste impostazioni chiudendo la console DPM e modificando le chiavi del Registro di sistema seguenti: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Eliminare le chiavi SMTPPassword e SMTPUserName**. Sarà possibile aggiungerle di nuovo nell'interfaccia utente all'avvio successivo.

## <a name="common-issues"></a>Problemi comuni

In questa sezione vengono illustrati gli errori comuni che possono verificarsi durante l'utilizzo del server di Backup di Azure.This section covers the common errors that you might encounter while using Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Messaggio di errore | Azione consigliata |
-- | --
Il backup non è riuscito perché la replica di backup su disco non è valido o è mancante. | Per risolvere questo problema, verificare i passaggi seguenti e ripetere l'operazione: <br/> 1. Creare un punto di ripristino del disco<br/> 2. Eseguire la verifica di coerenza sull'origine dati <br/> 3. Arrestare la protezione dell'origine dati e quindi riconfigurare la protezione per questa origine dati

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Messaggio di errore | Azione consigliata |
-- | --
Snapshot del volume di origine non riuscito perché i metadati nella replica non sono validi. | Creare un punto di ripristino del disco di questa origine dati e riprovare a eseguire il backup in lineaCreate a disk recovery point of this datasource and retry online backup again

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Messaggio di errore | Azione consigliata |
-- | --
Snapshot del volume di origine non riuscito a causa di una replica dell'origine dati incoerente. | Eseguire una verifica di coerenza su questa origine dati e riprovare

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Messaggio di errore | Azione consigliata |
-- | --
Il backup non è riuscito perché non è stato possibile clonare la replica di backup su disco.| Assicurarsi che tutti i file di replica di backup del disco precedenti (con estensione vhdx) siano stati disinstallati e che non sia in corso alcun backup da disco a disco durante i backup in linea
