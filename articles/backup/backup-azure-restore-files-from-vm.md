---
title: Ripristinare file e cartelle dal backup delle macchine virtuali di Azure
description: Questo articolo illustra come ripristinare file e cartelle da un punto di ripristino della macchina virtuale di Azure.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 0e3061ea8fc26adcf39fe415cd9a662de739543a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273306"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Ripristinare i file da un backup della macchina virtuale di Azure

Backup di Azure offre la possibilità di ripristinare [dischi e macchine virtuali (VM) di Azure](./backup-azure-arm-restore-vms.md) dai backup di VM di Azure, detti anche punti di recupero. Questo articolo illustra come ripristinare file e cartelle da un backup di VM di Azure. Il ripristino di file e cartelle è disponibile solo per le VM di Azure distribuite con il modello Resource Manager e protette in un insieme di credenziali dei servizi di ripristino.

> [!NOTE]
> Questa funzionalità è disponibile per le VM di Azure distribuite usando il modello Resource Manager e protette in un insieme di credenziali di Servizi di ripristino.
> Il ripristino di file da un backup della VM crittografato non è supportato.
>

## <a name="mount-the-volume-and-copy-files"></a>Montare il volume e copiare i file

Per ripristinare file o cartelle dal punto di recupero, passare alla macchina virtuale e scegliere il punto di recupero desiderato.

1. Accedere al [portale di Azure](https://portal.Azure.com) e, nel riquadro a sinistra, fare clic su **Macchine virtuali**. Nell'elenco delle macchine virtuali selezionare la macchina virtuale per aprirne il dashboard.

2. Nel menu della macchina virtuale fare clic su **Backup** per aprire il dashboard Backup.

    ![Aprire la voce di backup dell'insieme di credenziali di Servizi di ripristino](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. Nel menu del dashboard Backup fare clic su **Ripristino file**.

    ![Pulsante Ripristino file](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Viene aperto il menu **Ripristino file**.

    ![Menu Ripristino file](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Nel menu a discesa **Selezionare il punto di ripristino**, selezionare il punto di ripristino contenente i file desiderati. Per impostazione predefinita, il punto di ripristino più recente è già selezionato.

5. Per scaricare il software usato per copiare i file dal punto di ripristino, fare clic su **Scarica eseguibile** (per le macchine virtuali di Windows Azure) o su **Scarica script** (per le macchine virtuali Linux di Azure, viene generato uno script Python).

    ![Password generata](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure scarica il file eseguibile o lo script sul computer locale.

    ![Messaggio per il download del file eseguibile o dello script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Per eseguire l'eseguibile o lo script come amministratore, è consigliabile salvare il file scaricato nel computer.

6. Il file eseguibile o lo script è protetto da password, che viene quindi richiesta. Nel menu **Ripristino file** fare clic sul pulsante di copia per caricare la password in memoria.

    ![Password generata](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Nel percorso di download (in genere la cartella Download) fare clic con il pulsante destro del mouse sul file eseguibile o sullo script ed eseguirlo con credenziali di amministratore. Quando richiesto, digitare la password o incollare la password dalla memoria e premere **invio**. Dopo l'immissione della password valida, lo script si connette al punto di ripristino.

    ![Menu Ripristino file](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Per i computer Linux, viene generato uno script Python. Uno deve scaricare lo script e copiarlo nel server Linux pertinente/compatibile. Potrebbe essere necessario modificare le autorizzazioni per eseguirlo con ```chmod +x <python file name>```. Eseguire quindi il file Python con ```./<python file name>```.

Per assicurarsi che lo script venga eseguito correttamente, vedere la sezione [requisiti di accesso](#access-requirements) .

### <a name="identifying-volumes"></a>Identificazione di volumi

#### <a name="for-windows"></a>Per Windows

Quando si esegue il file eseguibile, il sistema operativo monta i nuovi volumi e assegna lettere di unità. È possibile usare Esplora risorse o Esplora file per individuare queste unità. Le lettere di unità assegnate ai volumi potrebbero non essere uguali a quelle della macchina virtuale originale. Il nome del volume viene tuttavia mantenuto. Ad esempio, se il volume nella macchina virtuale originale era "disco dati (E:`\`)", tale volume può essere collegato nel computer locale come "disco dati (' qualsiasi letterà:`\`). Esplorare tutti i volumi indicati nell'output dello script fino a trovare i file o la cartella.  

   ![Menu Ripristino file](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Per Linux

In Linux i volumi del punto di ripristino sono montati nella cartella in cui viene eseguito lo script. I dischi collegati, i volumi e i percorsi di montaggio corrispondenti vengono visualizzati di conseguenza. Questi percorsi di montaggio sono visibili agli utenti con accesso a livello radice. Esplorare i volumi indicati nell'output dello script.

  ![Menu Ripristino file per Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Chiusura della connessione

Dopo avere identificato i file e averli copiati in un percorso di archiviazione locale, rimuovere o smontare le unità aggiuntive. Per smontare le unità, nel menu **Ripristino file** del portale di Azure fare clic su **Unmount Disks** (Smonta dischi).

![Smontare i dischi](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Dopo che i dischi sono stati smontati, viene visualizzato un messaggio. L'aggiornamento della connessione in modo che sia possibile rimuovere i dischi potrebbe richiedere alcuni minuti.

In Linux, dopo che la connessione al punto di ripristino viene interrotta, il sistema operativo non rimuove automaticamente i percorsi di montaggio corrispondenti, I percorsi di montaggio sono disponibili come volumi "orfani" e sono visibili, ma generano un errore quando si accede ai file o li si scrive. Questi percorsi possono essere rimossi manualmente. Quando viene eseguito, lo script identifica tutti i volumi di questo tipo esistenti da eventuali punti di ripristino precedenti e li elimina dopo avere ottenuto il consenso.

## <a name="special-configurations"></a>Configurazioni speciali

### <a name="dynamic-disks"></a>Dischi dinamici

Se la VM di Azure protetta ha volumi con una o entrambe le caratteristiche seguenti, non è possibile eseguire lo script eseguibile nella stessa VM.

- Volumi che includono più dischi (volumi con spanning e con striping)
- Volumi a tolleranza di errore (volume RAID-5 e con mirroring) in dischi dinamici

Eseguire invece lo script eseguibile in qualsiasi altro computer con un sistema operativo compatibile.

### <a name="windows-storage-spaces"></a>Spazi di archiviazione di Windows

Spazi di archiviazione Windows è una tecnologia Windows che consente di virtualizzare le risorse di archiviazione. Con Spazi di archiviazione Windows è possibile raggruppare dischi standard di settore in pool di archiviazione e quindi usare lo spazio disponibile in tali pool per creare dischi virtuali, detti spazi di archiviazione.

Se la VM di Azure protetta usa Spazi di archiviazione Windows, non è possibile eseguire lo script eseguibile nella stessa VM. Eseguire invece lo script eseguibile in qualsiasi altro computer con un sistema operativo compatibile.

### <a name="lvmraid-arrays"></a>Matrici LVM/RAID

In Linux vengono usati la gestione dei volumi logici (LVM) e/o le matrici RAID software per gestire i volumi logici su più dischi. Se la VM Linux protetta usa array RAID e/o LVM, non è possibile eseguire lo script nella stessa VM. Eseguire invece lo script in qualsiasi altro computer con sistema operativo compatibile che supporti il file system della VM protetta.

L'output dello script seguente mostra dischi e volumi di array RAID e/o LVM con il tipo di partizione.

   ![Menu dell'output per LVM in Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Per portare online queste partizioni, eseguire i comandi riportati nelle sezioni successive.

#### <a name="for-lvm-partitions"></a>Per le partizioni LVM

Per elencare i nomi dei gruppi di volumi in un volume fisico:

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Per elencare tutti i volumi logici, i nomi e i relativi percorsi in un gruppo di volumi:

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command's results>
```

Per montare i volumi logici nel percorso desiderato:

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Per matrici RAID

Il comando seguente Visualizza i dettagli su tutti i dischi RAID:

```bash
#!/bin/bash
mdadm –detail –scan
```

 Il disco RAID pertinente viene visualizzato come `/dev/mdm/<RAID array name in the protected VM>`

Usare il comando di montaggio se il disco RAID dispone di volumi fisici:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Se nel disco RAID è stato configurato un altro LVM, usare la procedura precedente per le partizioni LVM, ma usare il nome del volume al posto del nome del disco RAID.

## <a name="system-requirements"></a>Requisiti di sistema

### <a name="for-windows-os"></a>Per il sistema operativo Windows

La tabella seguente illustra la compatibilità tra i sistemi operativi del server e del computer. Quando si esegue il ripristino di file, non è possibile ripristinare i file in una versione precedente o successiva del sistema operativo. Ad esempio, non è possibile ripristinare un file da una VM Windows Server 2016 a un computer Windows Server 2012 o Windows 8. È possibile ripristinare i file da una VM allo stesso sistema operativo server o al sistema operativo client compatibile.

|Sistema operativo del server | Sistema operativo compatibile del client  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Per il sistema operativo Linux

In Linux, il sistema operativo del computer usato per ripristinare i file deve supportare il file system della macchina virtuale protetta. Quando si seleziona un computer per l'esecuzione dello script, assicurarsi che abbia un sistema operativo compatibile e che usi una delle versioni indicate nella tabella seguente:

|Sistema operativo Linux | Versioni  |
| --------------- | ---- |
| Ubuntu | 12.04 e versioni successive |
| CentOS | 6.5 e versioni successive  |
| RHEL | 6.7 e versioni successive |
| Debian | 7 e versioni successive |
| Oracle Linux | 6.4 e versioni successive |
| SLES | 12 e versioni successive |
| openSUSE | 42.2 e versioni successive |

> [!NOTE]
> Sono stati rilevati alcuni problemi nell'esecuzione dello script di ripristino del file nei computer con sistema operativo SLES 12 SP4 ed è in corso l'analisi del team SLES.
> Attualmente, l'esecuzione dello script di ripristino del file funziona sui computer con versioni del sistema operativo SLES 12 SP2 e SP3.
>

Per l'esecuzione e la connessione sicura al punto di ripristino, lo script richiede anche componenti bash e Python.

|Componente | Version  |
| --------------- | ---- |
| bash | 4 e versioni successive |
| python | 2.6.6 e versioni successive  |
| TLS | 1.2 dovrebbe essere supportata  |

## <a name="access-requirements"></a>Requisiti per l'accesso

Se si esegue lo script in un computer con accesso limitato, verificare che sia disponibile l'accesso a:

- `download.microsoft.com`
- URL di servizi di ripristino (il nome geografico si riferisce all'area in cui si trova l'insieme di credenziali di servizi di ripristino)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com` (Per aree geografiche pubbliche di Azure)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`(Per Azure Cina 21Vianet)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us` (Per Azure US Gov)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de` (Per Azure Germania)
- Porte in uscita 53 (DNS), 443, 3260

> [!NOTE]
>
> - Il nome del file script scaricato avrà il **nome geografico** da compilare nell'URL. Per esempio: il nome dello script scaricato inizia \'con\'\_\'VMName\'geoname\'_\'GUID, ad esempio *ContosoVM_wcus_12345678*
> - L'URL sarà <https://pod01-rec2.wcus.backup.windowsazure.com>"
>

Per Linux, lo script richiede i componenti "open-iscsi" e "lshw" per la connessione al punto di ripristino. Se i componenti non esistono nel computer in cui viene eseguito lo script, lo script richiede l'autorizzazione per installare i componenti. Acconsentire all'installazione dei componenti necessari.

L'accesso a `download.microsoft.com` è necessario per scaricare i componenti usati per creare un canale sicuro tra il computer in cui viene eseguito lo script e i dati nel punto di ripristino.

È possibile eseguire lo script in qualsiasi computer con lo stesso sistema operativo (o compatibile) della macchina virtuale sottoposta a backup. Vedere la [tabella di sistemi operativi compatibili](backup-azure-restore-files-from-vm.md#system-requirements) per informazioni in proposito. Se la macchina virtuale di Azure protetta usa Spazi di archiviazione Windows (per VM Windows di Azure) o array RAID/LVM (per VM Linux), non è possibile eseguire il file eseguibile o lo script nella stessa macchina virtuale. Eseguire invece il file eseguibile o lo script in qualsiasi altro computer con un sistema operativo compatibile.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Ripristino di file dai backup di macchine virtuali con dischi di grandi dimensioni

Questa sezione illustra come eseguire il ripristino di file da backup di macchine virtuali di Azure con più di 16 dischi e ogni dimensione del disco è superiore a 32 TB.

Poiché il processo di recupero file connette tutti i dischi dal backup, quando vengono usati un numero elevato di dischi (>16) o dischi di grandi dimensioni (> 32 TB ciascuno), sono consigliati i punti di azione seguenti:

- Per il ripristino dei file, è necessario usare un server di ripristino separato (VM D2v3 VM di Azure). È possibile usarlo solo per il ripristino dei file e quindi per arrestarlo quando non è necessario. Il ripristino nel computer originale non è consigliato perché avrà un impatto significativo sulla macchina virtuale stessa.
- Eseguire quindi lo script una volta per verificare se l'operazione di ripristino del file riesce.
- Se il processo di ripristino file si blocca (i dischi non vengono mai montati o montati ma i volumi non vengono visualizzati), seguire questa procedura.
  - Se il server di ripristino è una macchina virtuale Windows:
    - Verificare che il sistema operativo sia WS 2012 o versione successiva.
    - Verificare che le chiavi del registro di sistema siano impostate come indicato di seguito nel server di ripristino e assicurarsi di riavviare il server. Il numero accanto al GUID può essere compreso tra 0001-0005. Nell'esempio seguente viene 0004. Spostarsi nel percorso della chiave del registro di sistema fino alla sezione Parameters.

    ![iSCSI-reg-key-changes. png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Se il server di ripristino è una VM Linux:
  - Nel file/etc/iSCSI/iscsid.conf modificare l'impostazione da:
    - node. Conn [0]. Timeo. noop_out_timeout = 5 a node. Conn [0]. Timeo. noop_out_timeout = 30
- Dopo avere apportato la modifica precedente, eseguire di nuovo lo script. Con queste modifiche, è molto probabile che il ripristino del file venga eseguito correttamente.
- Ogni volta che un utente Scarica uno script, backup di Azure avvia il processo di preparazione del punto di ripristino per il download. Con dischi di grandi dimensioni, questo processo può richiedere molto tempo. Se sono presenti picchi di richieste successivi, la preparazione di destinazione entra in una spirale di download. È quindi consigliabile scaricare uno script da portale/PowerShell/CLI, attendere 20-30 minuti (euristica) e quindi eseguirlo. A questo punto, è previsto che la destinazione sia pronta per la connessione dallo script.
- Dopo il ripristino del file, assicurarsi di tornare al portale e fare clic su **smontare i dischi** per i punti di ripristino in cui non è stato possibile montare i volumi. In pratica, questo passaggio eliminerà eventuali processi/sessioni esistenti e aumenterà la probabilità di ripristino.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi durante il ripristino di file dalle macchine virtuali, controllare la tabella seguente per informazioni aggiuntive.

| Messaggio di errore/scenario | Possibile causa | Azione consigliata |
| ------------------------ | -------------- | ------------------ |
| Output exe: *eccezione intercettata durante la connessione alla destinazione* | Lo script non è in grado di accedere al punto di ripristino    | Controllare se il computer soddisfa i [requisiti di accesso precedenti](#access-requirements). |  
| Output del file exe: *Accesso alla destinazione già eseguito mediante una sessione iSCSI.* | Lo script è stato già eseguito nella stessa macchina virtuale e le unità sono state associate | I volumi del punto di ripristino sono già stati associati. È possibile che NON siano installati con le stesse lettere di unità della VM originale. Esplorare tutti i volumi disponibili in Esplora file per il file. |
| Output del file exe: lo *script non è valido perché i dischi sono stati smontati tramite il portale o hanno superato il limite di 12 ore. Scaricare un nuovo script dal portale.* |    I dischi sono stati smontati dal portale o è stato superato il limite di 12 ore | Questo particolare file exe non è ora valido e non può essere eseguito. Se si desidera accedere ai file di tale punto di ripristino, visitare il portale per un nuovo file exe.|
| Nel computer in cui viene eseguito il file exe: i nuovi volumi non vengono smontati dopo aver fatto clic sul pulsante smontaggio | L'iniziatore iSCSI nel computer non risponde/aggiorna la connessione alla destinazione e mantiene la cache. |  Dopo aver fatto clic **Smontare**, attendere qualche minuto. Se i nuovi volumi non sono smontati, sfogliare tutti i volumi. L'esplorazione di tutti i volumi impone all'initiator di aggiornare la connessione e il volume viene smontato con un messaggio di errore che indica che il disco non è disponibile.|
| Output exe: lo script viene eseguito correttamente, ma "i nuovi volumi collegati" non vengono visualizzati nell'output dello script |    Si tratta di un errore temporaneo    | I volumi sono già collegati. Aprire Explorer per visualizzare lo stato. Se si usa lo stesso computer per l'esecuzione di script ogni volta, provare a riavviare il computer e l'elenco dovrebbe essere visualizzato nelle esecuzioni successive del file exe. |
| Specifico per Linux: non è possibile visualizzare i volumi desiderati | Il sistema operativo del computer in cui viene eseguito lo script potrebbe non riconoscere il file system sottostante della VM protetta | Controllare se il punto di ripristino è coerente con l'arresto anomalo del sistema o coerente con i file. Se il file è coerente, eseguire lo script in un altro computer il cui sistema operativo riconosca il file System della VM protetta. |
| Specifico per Windows: non è possibile visualizzare i volumi desiderati | I dischi potrebbero essere stati collegati ma i volumi non sono stati configurati | Dalla schermata Gestione disco, identificare i dischi aggiuntivi correlati al punto di recupero. Se uno di questi dischi è in uno stato offline, provare a portarli online facendo clic con il pulsante destro del mouse sul disco e scegliendo **online**.|

## <a name="security"></a>Sicurezza

Questa sezione illustra le varie misure di sicurezza adottate per l'implementazione del ripristino di file dai backup di macchine virtuali di Azure.

### <a name="feature-flow"></a>Flusso di funzionalità

Questa funzionalità è stata creata per accedere ai dati della macchina virtuale senza la necessità di ripristinare l'intera macchina virtuale o i dischi delle macchine virtuali e con il numero minimo di passaggi. L'accesso ai dati della VM viene fornito da uno script (che monta il volume di ripristino quando viene eseguito come illustrato di seguito) e costituisce il fondamento di tutte le implementazioni di sicurezza:

  ![Flusso delle funzionalità di sicurezza](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementazioni della sicurezza

#### <a name="select-recovery-point-who-can-generate-script"></a>Seleziona punto di ripristino (che può generare lo script)

Lo script consente di accedere ai dati della macchina virtuale, quindi è importante definire chi può generarlo in primo luogo. È necessario accedere al portale di Azure e avere l' [autorizzazione RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) per generare lo script.

Il ripristino del file richiede lo stesso livello di autorizzazione necessario per il ripristino delle macchine virtuali e il ripristino dei dischi. In altre parole, solo gli utenti autorizzati possono visualizzare i dati della macchina virtuale possono generare lo script.

Lo script generato è firmato con il certificato Microsoft ufficiale per il servizio backup di Azure. Eventuali manomissioni dello script indicano che la firma è interruppe e qualsiasi tentativo di eseguire lo script viene evidenziato come un potenziale rischio da parte del sistema operativo.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Montaggio del volume di ripristino (utente che può eseguire lo script)

Solo un amministratore può eseguire lo script e deve essere eseguito in modalità con privilegi elevati. Lo script esegue solo un set di passaggi pregenerati e non accetta input da un'origine esterna.

Per eseguire lo script, è necessaria una password che viene visualizzata solo all'utente autorizzato al momento della generazione dello script nella portale di Azure o in PowerShell/CLI. Questo consente di verificare che l'utente autorizzato che Scarica lo script sia anche responsabile dell'esecuzione dello script.

#### <a name="browse-files-and-folders"></a>Sfogliare file e cartelle

Per esplorare file e cartelle, lo script usa l'iniziatore iSCSI nel computer e si connette al punto di ripristino configurato come destinazione iSCSI. Qui è possibile immaginare scenari in cui si sta provando a imitare o eseguire lo spoofing di tutti i componenti.

Viene usato un meccanismo di autenticazione CHAP reciproca, in modo che ogni componente esegua l'autenticazione dell'altro. Ciò significa che è molto difficile per un iniziatore fasullo connettersi alla destinazione iSCSI e che una destinazione falsa venga connessa al computer in cui viene eseguito lo script.

Il flusso di dati tra il servizio di ripristino e il computer è protetto mediante la creazione di un tunnel TLS sicuro su TCP (è[necessario supportare tls 1,2](#system-requirements) nel computer in cui viene eseguito lo script).

Anche tutti gli elenchi di controllo di accesso (ACL) dei file presenti nella macchina virtuale padre/di cui è stato eseguito il backup vengono conservati nel file system montato.

Lo script concede l'accesso in sola lettura a un punto di ripristino ed è valido solo per 12 ore. Se si vuole rimuovere l'accesso in precedenza, accedere al portale di Azure/PowerShell/CLI ed eseguire **smontare i dischi** per quel particolare punto di ripristino. Lo script verrà invalidato immediatamente.

## <a name="next-steps"></a>Passaggi successivi

- Per eventuali problemi durante il ripristino di file, vedere la sezione [risoluzione dei](#troubleshooting) problemi
- Informazioni su come [ripristinare i file tramite PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- Informazioni su come [ripristinare file tramite l'interfaccia](https://docs.microsoft.com/azure/backup/tutorial-restore-files) della riga di comando di Azure
- Dopo il ripristino della macchina virtuale, informazioni su come [gestire i backup](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
