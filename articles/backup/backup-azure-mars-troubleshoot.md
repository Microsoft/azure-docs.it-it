---
title: Risolvere i problemi dell'agente di backup di Azure
description: Questo articolo illustra come risolvere i problemi di installazione e registrazione dell'agente di backup di Azure.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 3203d5604f1bd5db9cf579af01b2ae6f34032d89
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467613"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Risolvere i problemi relativi all'agente Servizi di ripristino di Microsoft Azure (MARS)

Questo articolo descrive come risolvere gli errori che potrebbero essere visualizzati durante la configurazione, la registrazione, il backup e il ripristino.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

Prima di iniziare la risoluzione dei problemi relativi all'agente di servizi di ripristino di Azure (MARS), è consigliabile verificare quanto segue:

- [Verificare che l'agente Mars sia](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)aggiornato.
- [Assicurarsi di disporre della connettività di rete tra l'agente Mars e Azure](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Verificare che MARS sia in esecuzione nella console del servizio. Se necessario, riavviare e ripetere l'operazione.
- [Verificare che nel percorso della cartella dei file temporanei sia disponibile il 5% fino al 10% di spazio libero](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Controllare se un altro processo o un software antivirus interferisce con Backup di Azure](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Se il processo di backup è stato completato con avvisi, vedere [processi di backup completati con avviso](#backup-jobs-completed-with-warning)
- Se il backup pianificato non riesce ma il backup manuale funziona, vedere [backup non eseguiti in base alla pianificazione](#backups-dont-run-according-to-schedule).
- Verificare che il sistema operativo disponga degli aggiornamenti più recenti.
- [Verificare che le unità e i file non supportati con attributi non supportati siano esclusi dal backup](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Verificare che l'orologio del sistema protetto sia configurato sul fuso orario corretto.
- [Verificare che nel server sia installato .NET Framework 4.5.2 o versione successiva](https://www.microsoft.com/download/details.aspx?id=30653).
- Se si sta provando a registrare di nuovo il server in un insieme di credenziali:
  - Verificare che l'agente sia stato disinstallato sul server e che sia stato eliminato dal portale.
  - Utilizzare la stessa passphrase inizialmente utilizzata per registrare il server.
- Per i backup non in linea, verificare Azure PowerShell 3.7.0 sia installato sia nel computer di origine che in quello di copia prima di avviare il backup.
- Se l'agente di backup è in esecuzione in una macchina virtuale di Azure, vedere [questo articolo](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Sono state specificate credenziali dell'insieme di credenziali non valide

**Messaggio di errore**: non sono state specificate credenziali dell'insieme di credenziali. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. (ID: 34513)

| Causa | Azioni consigliate |
| ---     | ---    |
| **Le credenziali dell'insieme di credenziali non sono valide** <br/> <br/> È possibile che i file di credenziali dell'insieme di credenziali siano danneggiati, che siano scaduti o che abbiano un'estensione di file diversa da *. vaultCredentials*. (Ad esempio, potrebbero essere stati scaricati più di 10 giorni prima dell'ora di registrazione).| [Scaricare le nuove credenziali](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) dall'insieme di credenziali dei servizi di ripristino nel portale di Azure. Eseguire quindi la procedura seguente, in base alle esigenze: <ul><li> Se è già stato installato e registrato MARS, aprire la console MMC di Backup di Microsoft Azure Agent. Selezionare quindi **Registra server** nel riquadro **azioni** per completare la registrazione con le nuove credenziali. <br/> <li> Se la nuova installazione non riesce, provare a reinstallare con le nuove credenziali.</ul> **Nota**: se sono stati scaricati più file di credenziali dell'insieme di credenziali, solo il file più recente è valido per i successivi 10 giorni. Si consiglia di scaricare un nuovo file dell'insieme di credenziali.
| **Il server proxy/firewall sta bloccando la registrazione** <br/>oppure <br/>**Nessuna connettività Internet** <br/><br/> Se il computer o il server proxy ha una connettività Internet limitata e non si garantisce l'accesso per gli URL necessari, la registrazione avrà esito negativo.| Seguire questa procedura:<br/> <ul><li> Collaborare con il team IT per verificare che il sistema abbia la connettività Internet.<li> Se non si dispone di un server proxy, assicurarsi che l'opzione proxy non sia selezionata quando si registra l'agente. [Controllare le impostazioni del proxy](#verifying-proxy-settings-for-windows).<li> Se si dispone di un server proxy/firewall, collaborare con il team di rete per assicurarsi che gli URL e gli indirizzi IP abbiano accesso:<br/> <br> **URL**<br> `www.msftncsi.com` <br> . Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**Indirizzi IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>Ripetere la registrazione dopo aver completato i passaggi precedenti per la risoluzione dei problemi.<br></br> Se la connessione avviene tramite Azure ExpressRoute, verificare che le impostazioni siano configurate come descritto nel [supporto di Azure ExpressRoute](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Il software antivirus sta bloccando la registrazione** | Se nel server è installato un software antivirus, aggiungere le regole di esclusione necessarie all'analisi antivirus per i file e le cartelle seguenti: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> Cartella Scratch. Il percorso predefinito è c:\Programmi\Microsoft Azure Recovery Services Agent\Scratch. <li> La cartella bin in C:\Programmi\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Suggerimenti aggiuntivi

- Andare a C:/Windows/Temp e controllare se sono presenti più di 60.000 o 65.000 file con estensione tmp. Se sono presenti, eliminare questi file.
- Verificare che la data e l'ora del computer corrispondano al fuso orario locale.
- Assicurarsi che [questi siti](install-mars-agent.md#verify-internet-access) vengano aggiunti ai siti attendibili in Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verifica delle impostazioni proxy per Windows

1. Scaricare PsExec dalla pagina [Sysinternals](/sysinternals/downloads/psexec) .
1. Eseguire `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` da un prompt dei comandi con privilegi elevati.

   Tramite questo comando verrà aperto Internet Explorer.
1. Passare a **strumenti**  >  **Opzioni Internet**  >  **connessioni**  >  **Impostazioni LAN**.
1. Controllare le impostazioni proxy per l'account di sistema.
1. Se non è configurato alcun proxy e vengono forniti i dettagli del proxy, rimuovere i dettagli.
1. Se è stato configurato un proxy e i dettagli del proxy non sono corretti, verificare che i dettagli relativi all' **indirizzo IP** e alla **porta** del proxy siano corretti.
1. Chiudere Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Non è possibile scaricare il file di credenziali dell'insieme di credenziali

| Errore   | Azioni consigliate |
| ---     | ---    |
|Non è stato possibile scaricare il file dell'insieme di credenziali. (ID: 403) | <ul><li> Provare a scaricare le credenziali dell'insieme di credenziali usando un browser diverso o eseguire le operazioni seguenti: <ul><li> Avviare Internet Explorer. Selezionare F12. </li><li> Passare alla scheda **rete** e deselezionare la cache e i cookie. </li> <li> Aggiornare la pagina.<br></li></ul> <li> Controllare se la sottoscrizione è disabilitata/scaduta.<br></li> <li> Controllare se una regola del firewall sta bloccando il download. <br></li> <li> Assicurarsi di non aver esaurito il limite nell'insieme di credenziali (50 computer per ogni insieme di credenziali).<br></li>  <li> Verificare che l'utente disponga delle autorizzazioni di backup di Azure necessarie per scaricare le credenziali dell'insieme di credenziali e registrare un server con l'insieme di credenziali. Vedere [usare il controllo di accesso in base al ruolo di Azure per gestire i punti di ripristino di backup di Azure](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi a Backup di Microsoft Azure

| Errore  | Possibile causa | Azioni consigliate |
| ---     | ---     | ---    |
| <br /><ul><li>L'agente del servizio di ripristino Microsoft Azure non è riuscito a connettersi al Backup di Microsoft Azure. (ID: 100050) Controllare le impostazioni di rete e assicurarsi di essere in grado di connettersi a Internet.<li>(407) Necessaria autenticazione proxy. |Un proxy blocca la connessione. |  <ul><li>In Internet Explorer passare a **strumenti**  >  **Opzioni Internet**  >  **sicurezza**  >  **Internet**. Selezionare **livello personalizzato** e scorrere verso il basso fino alla sezione **download del file** . Selezionare **Abilita**.<p>Potrebbe inoltre essere necessario aggiungere [URL e indirizzi IP](install-mars-agent.md#verify-internet-access) ai siti attendibili in Internet Explorer.<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy.<li> Se il computer ha accesso a Internet limitato, verificare che le impostazioni del firewall nel computer o nel proxy consentano questi [URL e indirizzi IP](install-mars-agent.md#verify-internet-access). <li>Se nel server è installato un software antivirus, escludere questi file dall'analisi antivirus: <ul><li>CBengine.exe (anziché dpmra.exe).<li>CSC.exe (correlato a .NET Framework). È presente un CSC.exe per ogni versione di .NET Framework installata nel server. Escludere CSC.exe file per tutte le versioni di .NET Framework nel server interessato. <li>Percorso della cartella scratch o della cache. <br>Il percorso predefinito per la cartella scratch o il percorso della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch.<li>La cartella bin in C:\Programmi\Microsoft Azure Recovery Services Agent\Bin.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>Non è possibile usare il file di credenziali dell'insieme di credenziali specificato perché non è stato scaricato dall'insieme di credenziali associato a questo server

| Errore  | Possibile causa | Azioni consigliate |
| ---     | ---     | ---    |
| Non è possibile usare il file di credenziali dell'insieme di credenziali specificato perché non viene scaricato dall'insieme di credenziali associato a questo server. (ID: 100110) Fornire le credenziali appropriate per l'insieme di credenziali. | Il file delle credenziali dell'insieme di credenziali si trova in un insieme di credenziali diverso da quello in cui questo server è già registrato. | Assicurarsi che il computer di destinazione e il computer di origine siano registrati nello stesso insieme di credenziali dei servizi di ripristino. Se il server di destinazione è già stato registrato in un insieme di credenziali diverso, usare l'opzione **Registra server** per eseguire la registrazione nell'insieme di credenziali corretto.  

## <a name="backup-jobs-completed-with-warning"></a>Processi di backup completati con avviso

- Quando l'agente MARS esegue l'iterazione su file e cartelle durante il backup, potrebbero verificarsi varie condizioni che possono causare la contrassegnazione del backup come completato con avvisi. In queste condizioni, un processo viene visualizzato come completato con avvisi. Questo è un problema, ma significa che non è stato possibile eseguire il backup di almeno un file. Quindi, il processo ha ignorato il file, ma è stato eseguito il backup di tutti gli altri file in questione nell'origine dati.

  ![Processo di backup completato con avvisi](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- Di seguito sono riportate le condizioni che possono causare l'omissione dei file di backup:
  - Attributi di file non supportati (ad esempio, in una cartella OneDrive, flusso compresso, reparse point). Per l'elenco completo, fare riferimento alla [matrice di supporto](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup).
  - Un problema file system
  - Un altro processo interferisce (ad esempio, il software antivirus che gestisce i file può impedire all'agente MARS di accedere ai file)
  - File bloccati da un'applicazione  

- Il servizio di backup contrassegnerà questi file come non riusciti nel file di log, con la convenzione di denominazione seguente: *LastBackupFailedFilesxxxx.txt* nella cartella *C:\Programmi\Microsoft Azure Recovery Service Agent\temp*
- Per risolvere il problema, esaminare il file di log per comprendere la natura del problema:

  | Codice di errore             | Motivi                                             | Consigli                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | Il file o la directory è danneggiato e illeggibile. | Eseguire **chkdsk** nel volume di origine.                             |
  | 0x80070002, 0 x 80070003 | Il sistema non è in grado di trovare il file specificato.         | [Verificare che la cartella Scratch non sia piena](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)  <br><br>  Controllare se esiste un volume in cui è configurato lo spazio scratch (non eliminato)  <br><br>   [Assicurarsi che l'agente MARS sia escluso dall'antivirus installato nel computer](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | Accesso negato                                    | [Verificare se l'accesso è bloccato da un software antivirus o da un altro software di terze parti](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | L'accesso al file Cloud è stato negato.                | File OneDrive, file git o qualsiasi altro file che può trovarsi nello stato offline del computer |

- È possibile usare [Aggiungi regole di esclusione per i criteri esistenti](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) per escludere i file non supportati, mancanti o eliminati dal criterio di backup per garantire la riuscita dei backup.

- Evitare di eliminare e ricreare cartelle protette con gli stessi nomi nella cartella di primo livello. Questa operazione potrebbe comportare il completamento del backup con avvisi con l'errore *: è stata rilevata un'incoerenza critica, pertanto non è possibile replicare le modifiche.*  Se è necessario eliminare e ricreare le cartelle, provare a eseguire questa operazione nelle sottocartelle della cartella di primo livello protetta.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Impossibile impostare la chiave di crittografia per i backup protetti

| Errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| <br />Impossibile impostare la chiave di crittografia per i backup protetti. L'attivazione non è stata completata, ma la passphrase di crittografia è stata salvata nel file seguente. |<li>Il server è già registrato con un altro insieme di credenziali.<li>Durante la configurazione la passphrase è stata danneggiata.| Annullare la registrazione del server dall'insieme di credenziali e registrarlo nuovamente con una nuova passphrase.

## <a name="the-activation-did-not-complete-successfully"></a>L'attivazione non è stata completata

| Errore  | Possibili cause | Azioni consigliate |
|---------|---------|---------|
|<br />L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft.     | <li> La cartella Scratch si trova in un volume che non dispone di spazio sufficiente. <li> La cartella Scratch è stata spostata in modo errato. <li> Il file OnlineBackup.KEK è mancante.         | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'agente Mars.<li>Spostare la cartella scratch o la posizione della cache in un volume con spazio libero compreso tra 5% e 10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere i passaggi in [domande frequenti sul backup di file e cartelle](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito per la cartella scratch o il percorso della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>La passphrase di crittografia non è configurata correttamente

| Errore  | Possibili cause | Azioni consigliate |
|---------|---------|---------|
| <br />Errore 34506. La passphrase di crittografia archiviata nel computer non è configurata correttamente.    | <li> La cartella Scratch si trova in un volume che non dispone di spazio sufficiente. <li> La cartella Scratch è stata spostata in modo errato. <li> Il file OnlineBackup.KEK è mancante.        | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'Agente di Servizi di ripristino di Microsoft Azure.<li>Spostare la cartella scratch o la posizione della cache in un volume con spazio libero compreso tra 5% e 10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere i passaggi in [domande frequenti sul backup di file e cartelle](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito per la cartella scratch o il percorso della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>I backup non vengono eseguiti in base alla pianificazione

Se i backup pianificati non vengono attivati automaticamente, ma i backup manuali funzionano correttamente, provare a eseguire le azioni seguenti:

- Assicurarsi che la pianificazione di Windows Server backup non sia in conflitto con la pianificazione del backup di file e cartelle di Azure.

- Verificare che lo stato del backup online sia impostato su **Abilita**. Per verificare lo stato, seguire questa procedura:

  1. In Utilità di pianificazione espandere **Microsoft** e selezionare **backup online**.
  1. Fare doppio clic su **Microsoft-onlinebackup** e passare alla scheda **trigger** .
  1. Verificare che lo stato sia impostato su **abilitato**. In caso contrario, selezionare **modifica**, selezionare **abilitato** e quindi fare clic su **OK**.

- Verificare che l'account utente selezionato per l'esecuzione dell'attività sia il **gruppo di amministratori** di **sistema** o locale nel server. Per verificare l'account utente, passare alla scheda **generale** e controllare le opzioni di **sicurezza** .

- Verificare che nel server sia installato PowerShell 3,0 o versione successiva. Per controllare la versione di PowerShell, eseguire questo comando e verificare che il `Major` numero di versione sia 3 o successivo:

  `$PSVersionTable.PSVersion`

- Verificare che questo percorso faccia parte della `PSMODULEPATH` variabile di ambiente:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se i criteri di esecuzione di PowerShell per `LocalMachine` sono impostati su `restricted` , il cmdlet di PowerShell che attiva l'attività di backup potrebbe non riuscire. Eseguire questi comandi in modalità con privilegi elevati per controllare e impostare i criteri di esecuzione su `Unrestricted` o `RemoteSigned` :

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Assicurarsi che non siano presenti file MSOnlineBackup del modulo di PowerShell mancanti o danneggiati. Se sono presenti file danneggiati o mancanti, seguire questa procedura:

  1. Da qualsiasi computer in cui è presente un agente MARS che funziona correttamente, copiare la cartella MSOnlineBackup da C:\Programmi\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Nel computer problematico incollare i file copiati nello stesso percorso della cartella (C:\Programmi\Microsoft Azure Recovery Services Agent\bin\Modules).

     Se è già presente una cartella MSOnlineBackup nel computer, incollarvi i file o sostituire eventuali file esistenti.

> [!TIP]
> Per garantire che le modifiche vengano applicate in modo coerente, riavviare il server dopo aver eseguito i passaggi precedenti.

## <a name="resource-not-provisioned-in-service-stamp"></a>Risorsa di cui non è stato effettuato il provisioning nel timbro

Errore | Possibili cause | Azioni consigliate
--- | --- | ---
L'operazione corrente non è riuscita a causa di un errore di servizio interno "Impossibile eseguire il provisioning della risorsa nel timbro del servizio". Ripetere l'operazione dopo alcuni minuti. (ID: 230006) | Il server protetto è stato rinominato. | <li> Rinominare nuovamente il server con il nome originale come registrato con l'insieme di credenziali. <br> <li> Registrare nuovamente il server nell'insieme di credenziali con il nuovo nome.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>Non è stato possibile avviare il processo perché è in corso un altro processo

Se si nota un messaggio di avviso nella cronologia dei processi della **console Mars**  >  , ovvero "Impossibile avviare il processo perché è in corso un altro processo", il problema potrebbe essere dovuto a un'istanza duplicata del processo attivato dal utilità di pianificazione.

![Non è stato possibile avviare il processo perché è in corso un altro processo](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

Per risolvere il problema:

1. Avviare lo snap-in Utilità di pianificazione digitando *taskschd. msc* nella finestra Esegui
1. Nel riquadro sinistro passare a **utilità di pianificazione Library**  ->  **Microsoft**  ->  **onlinebackup**.
1. Per ogni attività in questa libreria, fare doppio clic sull'attività per aprire proprietà e seguire questa procedura:
    1. Passare alla scheda **Impostazioni** .

         ![Scheda Settings](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. Modificare l'opzione per **se l'attività è già in esecuzione, viene applicata la regola seguente**. Scegliere **non avviare una nuova istanza**.

         ![Modificare la regola in non avviare una nuova istanza](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Risolvere i problemi di ripristino

Backup di Azure potrebbe non montare il volume di ripristino in modo corretto, anche dopo diversi minuti. È possibile che vengano visualizzati messaggi di errore durante il processo. Per avviare normalmente il ripristino, seguire questa procedura:

1. Annullare il processo di montaggio se è in esecuzione per diversi minuti.

2. Controllare se è presente la versione più recente dell'agente di backup. Per controllare la versione, nel riquadro **azioni** della console Mars selezionare **about Servizi di ripristino di Microsoft Azure Agent**. Verificare che il numero di **versione** sia uguale a o superiore alla versione citata in [questo articolo](https://go.microsoft.com/fwlink/?linkid=229525). Selezionare questo collegamento per [scaricare la versione più recente](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Passare a **Gestione dispositivi**  >  **controller di archiviazione** e individuare **iniziatore iSCSI Microsoft**. Se viene individuato, andare direttamente al passaggio 7.

4. Se non è possibile individuare il servizio iniziatore iSCSI Microsoft, provare a trovare una voce in **Gestione dispositivi**  >  **controller di archiviazione** denominato **dispositivo sconosciuto** con ID hardware **ROOT\ISCSIPRT**.

5. Fare clic con il pulsante destro del mouse su **dispositivo sconosciuto** e selezionare **Aggiorna software driver**.

6. Aggiornare il driver selezionando l'opzione **Cerca automaticamente un driver aggiornato**. Questo aggiornamento deve modificare il **dispositivo sconosciuto** nell' **iniziatore iSCSI Microsoft**:

    ![Schermata di Gestione dispositivi di Backup di Azure, con l'opzione Controller di archiviazione evidenziata](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Passare a servizi di **Gestione attività**  >  **(locale)**  >  **servizio iniziatore iSCSI Microsoft**:

    ![Schermata di Gestione attività di Backup di Azure con l'opzione Servizi (computer locale) evidenziata](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Riavviare il servizio Iniziatore iSCSI Microsoft. A tale scopo, fare clic con il pulsante destro del mouse sul servizio e scegliere **Arresta**. Quindi fare di nuovo clic con il pulsante destro del mouse e scegliere **Avvia**.

9. Ripetere il ripristino usando l'opzione [Ripristino istantaneo](backup-instant-restore-capability.md).

Se il ripristino ha ancora esito negativo, riavviare il server o il client. Se non si vuole riavviare il sistema o se il ripristino ha esito negativo anche dopo il riavvio del server, provare a eseguire il ripristino [da un altro computer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Risolvere i problemi relativi alla cache

L'operazione di backup potrebbe non riuscire se la cartella della cache (detta anche cartella Scratch) non è configurata correttamente, mancano i prerequisiti o l'accesso è limitato.

### <a name="prerequisites"></a>Prerequisiti

Affinché le operazioni dell'agente MARS abbiano esito positivo, la cartella della cache deve rispettare i requisiti seguenti:

- [Verificare che il 5% del 10% di spazio disponibile nel percorso della cartella Scratch](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Verificare che il percorso della cartella Scratch sia valido e accessibile](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Verificare che gli attributi di file nella cartella della cache siano supportati](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Verificare che lo spazio di archiviazione della copia shadow allocato sia sufficiente per il processo di backup](#increase-shadow-copy-storage)
- [Assicurarsi che non vi siano altri processi (ad esempio, software antivirus) che limitano l'accesso alla cartella della cache](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Aumenta l'archiviazione delle copie shadow

Le operazioni di backup potrebbero non riuscire se non è disponibile spazio di archiviazione copia shadow sufficiente per proteggere l'origine dati. Per risolvere questo problema, aumentare lo spazio di archiviazione della copia shadow nel volume protetto usando **vssadmin** , come illustrato di seguito:

- Controllare lo spazio di archiviazione shadow corrente dal prompt dei comandi con privilegi elevati:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Aumentare lo spazio di archiviazione shadow usando il comando seguente:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Un altro processo o un software antivirus che blocca l'accesso alla cartella della cache

Se nel server è installato un software antivirus, aggiungere le regole di esclusione necessarie all'analisi antivirus per i file e le cartelle seguenti:  

- Cartella Scratch. Il percorso predefinito è `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- La cartella bin in `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Problemi comuni

Questa sezione descrive gli errori comuni che si verificano durante l'uso dell'agente MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Messaggio di errore | Azione consigliata
--|--
Agente di Servizi di ripristino di Microsoft Azure non è riuscito ad accedere al checksum di backup archiviato nell'area di lavoro | Per risolvere questo problema, eseguire la procedura seguente e riavviare il server <br/> - [Controllare se è presente un antivirus o altri processi che bloccano i file del percorso dei file temporanei](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controllare se il percorso dei file temporanei è valido e accessibile all'agente MARS.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Messaggio di errore | Azione consigliata
--|--
Agente di Servizi di ripristino di Microsoft Azure non è riuscito ad accedere al percorso dei file temporanei per inizializzare VHD | Per risolvere questo problema, eseguire la procedura seguente e riavviare il server <br/> - [Controllare se i file del percorso dei file temporanei sono stati bloccati da antivirus o altri processi](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controllare se il percorso dei file temporanei è valido e accessibile all'agente MARS.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Messaggio di errore | Azione consigliata
--|--
Il backup non è riuscito a causa di spazio di archiviazione insufficiente nel volume in cui si trova la cartella Scratch | Per risolvere questo problema, verificare i passaggi seguenti e ripetere l'operazione:<br/>- [Verificare che l'agente MARS sia più recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Verificare e risolvere i problemi di archiviazione che incidono sull'area scratch di backup](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Messaggio di errore | Azione consigliata
--|--
Impossibile trovare le modifiche in un file. Il problema potrebbe essere legato a vari motivi. Ripetere l'operazione | Per risolvere questo problema, verificare i passaggi seguenti e ripetere l'operazione:<br/> - [Verificare che l'agente MARS sia più recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Verificare e risolvere i problemi di archiviazione che incidono sull'area scratch di backup](#prerequisites)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [come eseguire il backup di Windows Server con l'agente di backup di Azure](tutorial-backup-windows-server-to-azure.md).
- Se è necessario ripristinare un backup, vedere [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).
