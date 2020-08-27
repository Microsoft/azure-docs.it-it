---
title: Risolvere gli errori di backup delle macchine virtuali di Azure
description: Questo articolo illustra come risolvere gli errori riscontrati con il backup e il ripristino di macchine virtuali di Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: a5784aeb615c6d84048835bd6169f0819fad2f56
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892338"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Risoluzione degli errori di backup nelle macchine virtuali di Azure

È possibile risolvere gli errori rilevati durante l'uso di Backup di Azure con le informazioni elencate di seguito:

## <a name="backup"></a>Backup

Questa sezione descrive l'errore relativo all'operazione di backup della macchina virtuale di Azure.

### <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

* Verificare di disporre dell'[ultima versione](./backup-azure-arm-vms-prepare.md#install-the-vm-agent) dell'agente di macchine virtuali (WA Agent).
* Verificare che la versione del sistema operativo per le macchine virtuali Windows o Linux sia supportata. Fare riferimento a [Matrice di supporto per il backup di macchine virtuali IaaS](./backup-support-matrix-iaas.md).
* Verificare che un altro servizio di backup non sia in esecuzione.
  * Per assicurarsi che non siano presenti problemi di estensione degli snapshot, [disinstallare le estensioni per forzare il ricaricamento e riprovare a eseguire il backup](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).
* Verificare che la macchina virtuale disponga della connettività Internet.
  * Assicurarsi che un altro servizio di backup non sia in esecuzione.
* In `Services.msc` assicurarsi che il servizio **agente guest di Microsoft Azure** sia **in esecuzione**. Se il servizio **agente guest di Microsoft Azure** manca, installarlo da [Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* È possibile che nel **registro eventi** vengano visualizzati errori di backup provenienti da altri prodotti di backup, ad esempio Windows Server backup, e che non siano dovuti a backup di Azure. Seguire questa procedura per determinare se il problema è correlato a Backup di Azure:
  * Se si verifica un errore con una voce **Backup** nell'origine o nel messaggio dell'evento, controllare se i backup delle macchine virtuali IaaS di Azure sono stati completati e se è stato creato un punto di ripristino con il tipo di snapshot desiderato.
  * Se Backup di Azure è in funzione, il problema è probabilmente correlato a un'altra soluzione di backup.
  * Di seguito è riportato un esempio di errore 517 del visualizzatore eventi che indica il corretto funzionamento del backup di Azure, ma la presenza di un errore di "Windows Server Backup":<br>
    ![Errore di Windows Server Backup](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * In caso di errore relativo a Backup di Azure, cercare il codice errore corrispondente nella sezione relativa agli errori comuni di backup delle VM in questo articolo.

## <a name="common-issues"></a>Problemi comuni

Di seguito sono riportati i problemi comuni relativi agli errori di backup nelle macchine virtuali di Azure.

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>VMRestorePointInternalError-antivirus configurato nella macchina virtuale limita l'esecuzione dell'estensione di backup

Codice di errore: VMRestorePointInternalError

Se al momento del backup, nei **registri applicazioni Visualizzatore eventi** viene visualizzato il nome dell'applicazione per cui si è verificato l' **errore: IaaSBcdrExtension.exe** quindi il virus configurato nella VM limita l'esecuzione dell'estensione di backup.
Per risolvere questo problema, escludere le directory seguenti nella configurazione del programma antivirus, quindi ripetere l'operazione di backup.

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime - Timeout della copia di dati di backup dall'insieme di credenziali

Codice errore: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Messaggio di errore: Timeout della copia di dati di backup dall'insieme di credenziali

Ciò può verificarsi a causa di errori della risorsa di archiviazione temporanea o per operazioni di I/O al secondo dell'account di archiviazione insufficienti affinché il servizio di backup possa trasferire i dati all'insieme di credenziali entro il periodo di timeout. Configurare il backup della macchina virtuale usando queste [procedure consigliate](backup-azure-vms-introduction.md#best-practices) e ripetere l'operazione di backup.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState - Lo stato della macchina virtuale non consente i backup

Codice errore: UserErrorVmNotInDesirableState <br/>
Messaggio di errore: Lo stato della macchina virtuale non consente i backup.<br/>

L'operazione di backup non è riuscita perché la macchina virtuale è in stato di errore. Per il corretto completamento del backup, lo stato della macchina virtuale deve essere In esecuzione, Arrestato o Arrestato (deallocato).

* Se la macchina virtuale si trova in uno stato temporaneo tra **In esecuzione** e **Arresto**, attendere che lo stato cambi. Quindi attivare il processo di backup.
* Se la macchina virtuale è Linux e usa il modulo del kernel Security Enhanced Linux, escludere il percorso dell'agente Linux di Azure **/var/lib/waagent** dai criteri di sicurezza e assicurarsi che l'estensione di backup sia installata.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - Non è stato possibile bloccare uno o più punti di montaggio della macchina virtuale per creare uno snapshot coerente con il file system

Codice errore: UserErrorFsFreezeFailed <br/>
Messaggio di errore: Impossibile bloccare uno o più punti di montaggio della macchina virtuale per creare uno snapshot coerente con il file system.

* Smontare i dispositivi per cui lo stato del file system non è stato pulito, usando il comando **umount** .
* Eseguire una verifica di coerenza del file system in questi dispositivi usando il comando **fsck**.
* Montare di nuovo i dispositivi e provare a ripetere l'operazione di backup.</ol>

### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC - L'installazione dell'estensione/operazione non è riuscita a causa di un errore COM+

Codice errore: ExtensionSnapshotFailedCOM <br/>
Messaggio di errore: L'operazione di creazione snapshot non è riuscita a causa di un errore COM+

Codice errore: ExtensionInstallationFailedCOM  <br/>
Messaggio di errore: L'installazione dell'estensione/operazione non è riuscita a causa di un errore COM+

Codice errore: ExtensionInstallationFailedMDTC <br/>
Messaggio di errore: L'installazione dell'estensione non è riuscita con un errore che indica che il servizio COM+ non può comunicare con Microsoft Distributed Transaction Coordinator <br/>

L'operazione di backup non è riuscita a causa di un problema con il servizio di Windows **Applicazione di sistema COM+** .  Per risolvere il problema, seguire questa procedura:

* Provare ad avviare/riavviare il servizio di Windows **Applicazione di sistema COM+** (da un prompt dei comandi con privilegi elevati **- net start COMSysApp**).
* Assicurarsi che il servizio **Distributed Transaction Coordinator** sia in esecuzione come account **Servizio di rete**. In caso contrario, modificarlo in modo che venga eseguito come account **Servizio di rete** e riavviare **Applicazione di sistema COM+** .
* Se non è possibile riavviare il servizio, reinstallare **Distributed Transaction Coordinator** servizio attenendosi alla procedura seguente:
  * Arrestare il servizio MSDTC
  * Aprire un prompt dei comandi (cmd)
  * Eseguire il comando `msdtc -uninstall`
  * Eseguire il comando `msdtc -install`
  * Avviare il servizio MSDTC
* Avviare il servizio di Windows **Applicazione di sistema COM+** . Dopo che il servizio **Applicazione sistema COM+** è stato avviato, attivare un processo di backup dal portale di Azure.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - L'operazione di creazione snapshot non è riuscita perché lo stato di VSS writer non è valido

Codice errore: ExtensionFailedVssWriterInBadState <br/>
Messaggio di errore: L'operazione di creazione snapshot non è riuscita perché lo stato di VSS writer non è valido.

Riavviare i writer VSS in uno stato non valido. Da un prompt dei comandi con privilegi elevati, eseguire ```vssadmin list writers```. L'output contiene tutti i writer VSS con lo stato. Riavviare ogni VSS writer con stato diverso da **[1] Stable** eseguendo i comandi seguenti da un prompt dei comandi con privilegi elevati:

* ```net stop serviceName```
* ```net start serviceName```

Un'altra procedura che può essere utile consiste nell'usare il comando seguente da un prompt dei comandi con privilegi elevati (come amministratore).

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

Se si aggiunge questa chiave del registro di sistema, i thread non verranno creati per gli snapshot BLOB e si eviterà il timeout.

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - Errore durante l'analisi della configurazione per l'estensione di backup

Codice errore: ExtensionConfigParsingFailure<br/>
Messaggio di errore: Errore durante l'analisi della configurazione per l'estensione di backup.

Questo errore si verifica a causa della modifica delle autorizzazioni nella directory **MachineKeys**: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Eseguire il comando seguente e verificare che le autorizzazioni per la directory **MachineKeys** siano quelle predefinite: `icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys` .

Le autorizzazioni predefinite sono:

* Everyone: (R,W)
* BUILTIN\Administrators: (F)

Se nella directory **MachineKeys** vengono visualizzate autorizzazioni diverse da quelle predefinite, seguire la procedura riportata di seguito per correggere le autorizzazioni, eliminare il certificato e attivare il backup:

1. Correggere le autorizzazioni nella directory **MachineKeys**. Usando le impostazioni di sicurezza avanzate e le proprietà di sicurezza di Explorer nella directory, ripristinare le autorizzazioni ai valori predefiniti. Rimuovere tutti gli oggetti utente tranne quelli predefiniti dalla directory e assicurarsi che l'autorizzazione **Everyone** abbia accesso speciale per:

   * Visualizzazione cartella/lettura dati
   * Lettura attributi
   * Lettura attributi estesi
   * Creazione file/scrittura dati
   * Creazione cartelle/aggiunta dati
   * Scrittura attributi
   * Scrittura attributi estesi
   * Autorizzazioni di lettura
2. Eliminare tutti i certificati in cui **Rilasciato a** è il modello di distribuzione classica o **Windows Azure CRP Certificate Generator**:

   * [Aprire i certificati in una console del computer locale](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * In **Personale** > **Certificati** eliminare tutti i certificati in cui **Rilasciato a** o **Windows Azure CRP Certificate Generator** è il modello di distribuzione classica.
3. Attivare un processo di backup della macchina virtuale.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - Lo stato dell'estensione non è supportato dall'operazione di backup

Codice errore: ExtensionStuckInDeletionState <br/>
Messaggio di errore: Lo stato dell'estensione non è supportato dall'operazione di backup

L'operazione di backup non è riuscita a causa dello stato non coerente dell'estensione di backup. Per risolvere il problema, seguire questa procedura:

* Verificare che l'agente Guest sia installato e reattivo
* Nel portale di Azure passare a **Macchina virtuale** > **Tutte le impostazioni** > **Estensioni**
* Selezionare l'estensione di backup VmSnapshot o VmSnapshotLinux e fare clic su **Disinstalla**
* Dopo aver eliminato l'estensione di backup, ripetere l'operazione di backup
* L'operazione di backup successiva installerà la nuova estensione nello stato desiderato

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - L'operazione di creazione snapshot non è riuscita perché è stato superato il limite di snapshot per alcuni dischi collegati

Codice errore: ExtensionFailedSnapshotLimitReachedError  <br/>
Messaggio di errore: L'operazione di creazione snapshot non è riuscita perché è stato superato il limite di snapshot per alcuni dischi collegati

L'operazione di creazione snapshot non è riuscita poiché il limite di snapshot è stato superato per alcuni dischi collegati. Completare le seguenti procedure di risoluzione dei problemi, quindi ripetere l'operazione.

* Eliminare gli snapshot BLOB del disco che non sono necessari. Prestare attenzione a non eliminare i BLOB del disco. Solo i BLOB di snapshot devono essere eliminati.
* Se l'eliminazione temporanea è abilitata negli account di archiviazione su disco della macchina virtuale, configurare la conservazione dell'eliminazione temporanea in modo che gli snapshot esistenti siano inferiori al massimo consentito in qualsiasi momento.
* Se Azure Site Recovery è abilitato nella macchina virtuale di cui è stato eseguito il backup, attenersi questa procedura:

  * Verificare che il valore di **isanysnapshotfailed** sia impostato su false in /etc/azure/vmbackup.conf
  * Pianificare Azure Site Recovery in un momento diverso, pertanto non è in conflitto con l'operazione di backup.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - L'operazione di creazione snapshot non è riuscita a causa di risorse inadeguate della macchina virtuale

Codice errore: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Messaggio di errore: L'operazione di creazione snapshot non è riuscita a causa di risorse inadeguate della macchina virtuale.

L'operazione di backup sulla macchina virtuale ha avuto esito negativo a causa del ritardo nelle chiamate di rete durante l'operazione di creazione di snapshot. Per risolvere il problema, seguire il passaggio 1. Se il problema persiste, provare i passaggi 2 e 3.

**Passaggio 1**: Creare uno snapshot tramite host

Da un prompt dei comandi con privilegi elevati (amministratore), eseguire il comando seguente:

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Questo garantirà che gli snapshot vengano creati tramite host invece che guest. Ripetere l'operazione di backup.

**Passaggio 2**: provare a modificare la pianificazione del backup in un momento in cui la macchina virtuale è sotto carico inferiore (ad esempio meno CPU o IOPS)

**Passaggio 3**: provare ad [aumentare le dimensioni della macchina virtuale](https://azure.microsoft.com/blog/resize-virtual-machines/) e ripetere l'operazione

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001, ResourceNotFound-non è stato possibile eseguire l'operazione perché la macchina virtuale non esiste più/400094, BCMV2VMNotFound-la macchina virtuale non esiste/non è stata trovata una macchina virtuale di Azure

Codice errore: 320001, ResourceNotFound <br/> Messaggio di errore: Impossibile eseguire l'operazione perché la VM non esiste più. <br/> <br/> Codice errore: 400094, BCMV2VMNotFound <br/> Messaggio di errore: La macchina virtuale non esiste <br/>
La macchina virtuale di Azure non è stata trovata.

Questo errore si verifica quando la macchina virtuale primaria viene eliminata, ma i criteri di backup continuano a cercare una macchina virtuale di cui eseguire il backup. Per risolvere l'errore, procedere come segue:

* Ricreare la macchina virtuale con lo stesso nome e lo stesso nome del gruppo di risorse, **nome del servizio cloud**,<br>o
* Interrompere la protezione della macchina virtuale cancellando o senza eliminare i dati del backup. Per altre informazioni, vedere [Arrestare la protezione delle macchine virtuali](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>UserErrorBCMPremiumStorageQuotaError-non è stato possibile copiare lo snapshot della macchina virtuale a causa dello spazio disponibile insufficiente nell'account di archiviazione

Codice errore: UserErrorBCMPremiumStorageQuotaError<br/> Messaggio di errore: Non è stato possibile copiare lo snapshot della macchina virtuale a causa dello spazio disponibile insufficiente nell'account di archiviazione

 Per le macchine virtuali Premium nello stack V1 di backup di macchine virtuali, lo snapshot viene copiato nell'account di archiviazione, in modo da assicurare che il traffico di gestione dei backup, che funziona sullo snapshot, non limiti il numero di IOPS disponibili all'applicazione che usa i dischi Premium. <br><br>Si consiglia di allocare solo il 50%, 17,5 TB, dello spazio totale dell'account di archiviazione. In questo modo il servizio Backup di Azure può copiare lo snapshot nell'account di archiviazione e trasferire i dati da questa posizione copiata nell'account di archiviazione all'insieme di credenziali.

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008, AzureVmOffline: non è stato possibile installare l'estensione di servizi di ripristino di Microsoft perché la macchina virtuale non è in esecuzione

Codice errore: 380008, AzureVmOffline <br/> Messaggio di errore: Non è stato possibile installare l'estensione Servizi di ripristino di Microsoft perché la macchina virtuale non è in esecuzione

L'agente VM è un prerequisito dell'estensione Servizi di ripristino di Azure. Installare l'agente VM di Azure e riavviare l'operazione di registrazione. <br> <ol> <li>Controllare se l'agente di macchine virtuali è stato installato correttamente. <li>Assicurarsi che il flag sul file di configurazione della macchina virtuale sia impostato correttamente.</ol> Altre informazioni sull'installazione dell'agente di macchine virtuali e su come convalidarlo.

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensionSnapshotBitlockerError-l'operazione di snapshot non è riuscita con l'errore operazione Servizio Copia Shadow del volume (VSS)

Codice errore: ExtensionSnapshotBitlockerError <br/> Messaggio di errore: l'operazione di snapshot non è riuscita con l'errore operazione Servizio Copia Shadow del volume (VSS) **. l'unità è bloccata da crittografia unità BitLocker. È necessario sbloccare l'unità dal pannello di controllo.**

Disattivare BitLocker per tutte le unità della macchina virtuale e verificare se il problema relativo al Servizio Copia Shadow del volume è stato risolto.

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>VmNotInDesirableState: la macchina virtuale non è in uno stato che consente i backup

Codice errore: VmNotInDesirableState <br/> Messaggio di errore:  Lo stato della macchina virtuale non consente i backup.

* Se la macchina virtuale si trova in uno stato temporaneo tra **In esecuzione** e **Arresto**, attendere che lo stato cambi. Quindi attivare il processo di backup.
* Se la macchina virtuale è Linux e usa il modulo del kernel Security Enhanced Linux, escludere il percorso dell'agente Linux di Azure **/var/lib/waagent** dai criteri di sicurezza e assicurarsi che l'estensione di backup sia installata.

* L'agente di macchine virtuali non è presente nella macchina virtuale. <br>Installare i prerequisiti necessari e l'agente di macchine virtuali. Quindi ripetere l'operazione. | Altre informazioni sull' [installazione dell'agente di macchine virtuali e su come convalidare l'installazione dell'agente VM](#vm-agent).

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensionSnapshotFailedNoSecureNetwork-l'operazione di snapshot non è riuscita a causa di un errore nella creazione di un canale di comunicazione di rete sicuro

Codice errore: ExtensionSnapshotFailedNoSecureNetwork <br/> Messaggio di errore: L'operazione di creazione snapshot non è riuscita a causa di un errore durante la creazione del canale di comunicazione di rete protetta.

* Aprire l'editor del Registro di sistema eseguendo **regedit.exe** con privilegi elevati.
* Identificare tutte le versioni di .NET Framework presenti nel sistema, disponibili nella gerarchia della chiave del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**.
* Per ogni versione di .NET Framework presente nella chiave del Registro di sistema, aggiungere la chiave seguente: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensionVCRedistInstallationFailure-l'operazione di snapshot non è riuscita a causa di un errore di installazione Visual C++ Redistributable per Visual Studio 2012

Codice errore: ExtensionVCRedistInstallationFailure <br/> Messaggio di errore: L'operazione di creazione snapshot non è riuscita a causa di un errore durante l'installazione di Visual C++ Redistributable per Visual Studio 2012.

* Passare a `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion` e installare vcredist2013_x64.<br/>Assicurarsi che il valore della chiave del Registro di sistema che consente l'installazione del servizio sia impostato sul valore corretto. Impostare dunque il valore **Start** in **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** su **3** anziché su **4**. <br><br>Se i problemi di installazione persistono, riavviare il servizio di installazione eseguendo **MSIEXEC /UNREGISTER** e quindi **MSIEXEC /REGISTER** da un prompt dei comandi con privilegi elevati.
* Controllare il registro eventi per verificare se si è notato l'accesso ai problemi correlati. Ad esempio: *Prodotto: Microsoft Visual C++ 2013 x64 Minimum Runtime - 12.0.21005 -- Errore 1401 Impossibile creare la chiave: Software\Classes.  Errore di sistema 5.  Assicurarsi di disporre di diritti di accesso sufficienti per tale chiave oppure contattare il personale di supporto.* <br><br> Verificare che l'account amministratore o utente disponga di autorizzazioni sufficienti per aggiornare la chiave del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Classes**. Concedere autorizzazioni sufficienti e riavviare l'agente guest di Microsoft Azure.<br><br> <li> Se sono presenti prodotti antivirus, assicurarsi che dispongano delle regole di esclusione appropriate per consentire l'installazione.

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy: nella macchina virtuale che impedisce il funzionamento dello snapshot è stato configurato un criterio non valido

Codice errore:  UserErrorRequestDisallowedByPolicy <BR> Messaggio di errore: Nella macchina virtuale che impedisce l'operazione di Snapshot vengono configurati criteri non validi.

Se si dispone di un'istanza di Criteri di Azure che [governa i tag all'interno dell'ambiente](../governance/policy/tutorials/govern-tags.md), considerare di modificare i criteri dall'[effetto Deny](../governance/policy/concepts/effects.md#deny) all'[effetto Modify](../governance/policy/concepts/effects.md#modify) oppure creare manualmente il gruppo di risorse in base allo [schema di denominazione richiesto da Backup di Azure](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

## <a name="jobs"></a>Processi

| Dettagli errore | Soluzione alternativa |
| --- | --- |
| L'annullamento non è supportato per questo tipo di processo. <br>Attendere il completamento del processo. |nessuno |
| Il processo non si trova in uno stato annullabile. <br>Attendere il completamento del processo. <br>**or**<br> Il processo selezionato non si trova in uno stato annullabile. <br>Attendere il completamento del processo. |È probabile che il processo sia quasi terminato. Attendere fino al termine dell'esecuzione del processo.|
| Non è possibile annullare il processo perché non è in corso. <br>L'annullamento è supportato solo per i processi in corso. Provare ad annullare un processo in corso. |Questo errore si verifica a causa di uno stato temporaneo. Attendere un minuto e ripetere l'operazione di annullamento. |
| Non è stato possibile annullare il processo. <br>Attendere il completamento del processo. |nessuno |

## <a name="restore"></a>Restore

| Dettagli errore | Soluzione alternativa |
| --- | --- |
| Ripristino non riuscito con errore interno del cloud. |<ol><li>Il servizio cloud in cui si sta tentando di eseguire il ripristino è configurato con le impostazioni DNS. Verificare: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>Se è presente un **indirizzo** configurato, significa che le impostazioni DNS sono configurate.<br> <li>Il servizio cloud che si sta tentando di ripristinare è configurato con **ReservedIP** e le macchine virtuali esistenti nel servizio cloud sono in stato di arresto. È possibile controllare che il servizio cloud abbia un IP riservato usando i cmdlet di PowerShell seguenti: **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName**. <br><li>Si sta tentando di ripristinare una macchina virtuale con le configurazioni di rete speciali seguenti nello stesso servizio cloud: <ul><li>Macchine virtuali con configurazione del servizio di bilanciamento del carico, interno ed esterno.<li>Macchine virtuali con più indirizzi IP riservati. <li>Macchine virtuali con più schede di rete. </ul><li>Selezionare un nuovo servizio cloud nell'interfaccia utente o vedere le [considerazioni sul ripristino](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) per le macchine virtuali con configurazioni di rete speciali.</ol> |
| Il nome DNS selezionato è già utilizzato. <br>Specificare un nome DNS diverso e riprovare. |Il nome DNS fa riferimento al nome del servizio cloud, che in genere termina con **.cloudapp.net**. Questo nome deve essere univoco. Se si verifica questo errore, è necessario scegliere un altro nome di macchina virtuale durante il ripristino. <br><br> Questo errore viene visualizzato solo dagli utenti del portale di Azure. L'operazione di ripristino tramite PowerShell riesce perché ripristina solo i dischi e non crea la macchina virtuale. L'errore viene restituito quando la macchina virtuale viene creata in modo esplicito dall'utente dopo l'operazione di ripristino dei dischi. |
| La configurazione della rete virtuale specificata non è corretta. <br>Specificare una configurazione della rete virtuale diversa e riprovare. |nessuno |
| Il servizio cloud specificato usa un IP riservato che non corrisponde alla configurazione della macchina virtuale da ripristinare. <br>Specificare un servizio cloud diverso che non usa un IP riservato o scegliere un altro punto di ripristino da cui eseguire il ripristino. |nessuno |
| Il servizio cloud ha raggiunto il limite per il numero di endpoint di input. <br>Ripetere l'operazione specificando un servizio cloud diverso o usando un endpoint esistente. |nessuno |
| L'insieme di credenziali di Servizi di ripristino e l'account di archiviazione di destinazione si trovano in due aree diverse. <br>Verificare che l'account di archiviazione specificato nell'operazione di ripristino si trovi nella stessa area di Azure dell'insieme di credenziali di Servizi di ripristino. |nessuno |
| L'account di archiviazione specificato per l'operazione di ripristino non è supportato. <br>Sono supportati solo account di archiviazione Basic/Standard con impostazioni di replica con ridondanza locale o con ridondanza geografica. Selezionare un account di archiviazione supportato. |nessuno |
| Il tipo di account di archiviazione specificato per l'operazione di ripristino non è online. <br>Assicurarsi che l'account di archiviazione specificato nell'operazione di ripristino sia online. |Questo problema può essere provocato da un errore temporaneo in Archiviazione di Azure o da un'interruzione del servizio. Scegliere un altro account di archiviazione. |
| È stata raggiunta la quota di gruppi di risorse. <br>Eliminare alcuni gruppi di risorse dal portale di Azure o contattare il supporto tecnico di Azure per aumentare i limiti. |nessuno |
| La subnet selezionata non esiste. <br>Selezionare una subnet esistente. |nessuno |
| Il servizio Backup non ha l'autorizzazione per accedere alle risorse nella sottoscrizione. |Per risolvere questo errore, ripristinare prima di tutto i dischi seguendo la procedura illustrata in [Ripristinare i dischi di cui è stato eseguito il backup](backup-azure-arm-restore-vms.md#restore-disks). Seguire quindi la procedura di PowerShell descritta in [Creare una macchina virtuale da dischi ripristinati](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Il backup o il ripristino richiede del tempo

Se il backup impiega più di 12 ore o il ripristino impiega più di 6 ore, rivedere le [procedure consigliate](backup-azure-vms-introduction.md#best-practices) e le [considerazioni sulle prestazioni](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agente di macchine virtuali

### <a name="set-up-the-vm-agent"></a>Configurare l'agente di macchine virtuali

L'agente di VM è in genere già presente nelle VM create dalla raccolta di Azure. Nelle macchine virtuali di cui viene eseguita la migrazione da data center locali, tuttavia, l'agente di macchine virtuali non è installato. Per queste macchine virtuali è necessario installare esplicitamente l'agente VM.

#### <a name="windows-vms---set-up-the-agent"></a>Macchine virtuali Windows: configurare l'agente

* Scaricare e installare il file [MSI per l'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione è necessario disporre dei privilegi di amministratore.
* Per le macchine virtuali create con il modello di distribuzione classica, [aggiornare le proprietà della macchina virtuale](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms) per indicare che l'agente è stato installato. Questo passaggio non è necessario per le macchine virtuali di Azure Resource Manager.

#### <a name="linux-vms---set-up-the-agent"></a>VM Linux: configurare l'agente

* Installare la versione più recente dell'agente dal repository di distribuzione. Per informazioni dettagliate sul nome del pacchetto, vedere il [repository dell'agente Linux](https://github.com/Azure/WALinuxAgent).
* Per le macchine virtuali create con il modello di distribuzione classica, [aggiornare le proprietà della macchina virtuale](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms) e verificare che l'agente sia installato. Questo passaggio non è necessario per le macchine virtuali di Resource Manager.

### <a name="update-the-vm-agent"></a>Aggiornare l'agente di macchine virtuali

#### <a name="windows-vms---update-the-agent"></a>Macchine virtuali Windows: aggiornare l'agente

* Per aggiornare l'agente di macchine virtuali, reinstallare i [file binari dell'agente di macchine virtuali](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Prima di aggiornare l'agente, assicurarsi che non venga eseguita alcuna operazione di backup durante l'aggiornamento dell'agente di macchine virtuali.

#### <a name="linux-vms---update-the-agent"></a>Macchine virtuali Linux: aggiornare l'agente

* Per aggiornare l'agente di macchine virtuali Linux, seguire le istruzioni nell'articolo [Aggiornamento dell'agente di macchine virtuali Linux](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json).

    > [!NOTE]
    > Usare sempre il repository di distribuzione per aggiornare l'agente.

    Non scaricare il codice dell'agente da GitHub. Se l'agente più recente non è disponibile per la distribuzione, contattare il supporto per la distribuzione per istruzioni su come reperire l'agente più recente. È anche possibile trovare le informazioni più recenti sull'[agente Linux di Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) nel repository GitHub.

### <a name="validate-vm-agent-installation"></a>Convalidare l'installazione dell'agente di macchine virtuali

Verificare la versione dell'agente di macchine virtuali nelle macchine virtuali Windows:

1. Accedere alla macchina virtuale di Azure e passare alla cartella **C:\WindowsAzure\Packages**. La cartella dovrebbe contenere il file **WaAppAgent.exe**.
2. Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà**. Quindi selezionare la scheda **Dettagli**. Il campo **Versione prodotto** dovrebbe contenere 2.6.1198.718 o un numero più elevato.

## <a name="troubleshoot-vm-snapshot-issues"></a>Risoluzione dei problemi relativi agli snapshot delle macchine virtuali

Il backup delle macchine virtuali si basa sull'esecuzione dei comandi di snapshot sull'archiviazione sottostante. La mancanza di accesso all'archiviazione o ritardi nell'esecuzione dell'attività di snapshot possono causare il fallimento del processo di backup. Le condizioni seguenti possono causare errori dell'attività di snapshot:

* **Le macchine virtuali con il backup di SQL Server configurato possono causare ritardi nelle attività di snapshot**. Per impostazione predefinita, il backup delle macchine virtuali genera un backup completo Servizio Copia Shadow del volume nelle macchine virtuali Windows. Le macchine virtuali che eseguono SQL Server, con il backup di SQL Server configurato, possono sperimentare ritardi degli snapshot. Se i ritardi degli snapshot causano errori di backup, impostare la chiave del Registro di sistema seguente:

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **Lo stato della macchina virtuale viene segnalato in modo non corretto perché la macchina virtuale viene arrestata in RDP**. Se è stato usato il desktop remoto per arrestare la macchina virtuale, verificare che lo stato della macchina virtuale nel portale sia corretto. Se lo stato non è corretto, usare l'opzione **Arresto** nel dashboard della macchina virtuale del portale per arrestare la macchina virtuale.
* **Se più di quattro macchine virtuali condividono il servizio cloud, distribuirle tra più criteri di backup**. Distribuire gli orari del backup in modo che non più di quattro backup delle macchine virtuali vengano avviati nello stesso momento. Provare a separare di almeno un'ora gli orari di inizio dei criteri.
* **L'esecuzione della macchina virtuale fa un uso elevato della CPU o della memoria**. Se la macchina virtuale è in esecuzione con un uso elevato della memoria o della CPU, superiore al 90%, l'attività di snapshot viene messa in coda e ritardata. Infine, raggiunge il timeout. In questo caso, provare a eseguire un backup su richiesta.

## <a name="networking"></a>Rete

DHCP deve essere abilitato nel computer guest per consentire il funzionamento del backup delle macchine virtuali IaaS. Se è necessario un indirizzo IP privato statico, configurarlo tramite il portale di Azure o PowerShell. Assicurarsi che l'opzione DHCP all'interno della macchina virtuale sia abilitata.
Per altre informazioni su come configurare un indirizzo IP statico tramite PowerShell, vedere:

* [Come aggiungere un indirizzo IP interno statico a una macchina virtuale esistente](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [Modificare il metodo di allocazione per un indirizzo IP privato assegnato a un'interfaccia di rete](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
