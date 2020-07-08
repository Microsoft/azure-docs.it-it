---
title: Eseguire il backup dei carichi di lavoro DPM tramite PowerShell
description: Informazioni su come distribuire e gestire Backup di Azure per Data Protection Manager (DPM) usando PowerShell
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: bbd03c17f9f46494d6fadb64e1d059d1b81cd9ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84234634"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Distribuire e gestire il backup in Azure per server Data Protection Manager (DPM) mediante PowerShell

Questo articolo illustra come usare PowerShell per configurare Backup di Azure in un server DPM, e per gestire le operazioni di backup e ripristino.

## <a name="setting-up-the-powershell-environment"></a>Configurazione dell'ambiente di PowerShell

Prima di poter usare PowerShell per gestire i backup da Data Protection Manager ad Azure, è necessario avere l'ambiente appropriato in PowerShell. All'inizio della sessione di PowerShell, assicurarsi di eseguire il comando seguente per importare i moduli appropriati e fare riferimento correttamente ai cmdlet DPM:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Installazione e registrazione

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per iniziare, [scaricare l'ultima versione di Azure PowerShell](/powershell/azure/install-az-ps).

Le attività di installazione e registrazione seguenti possono essere automatizzate tramite PowerShell:

* Creare un insieme di credenziali di Servizi di ripristino
* Installazione dell'agente di Backup di Azure
* Registrazione del servizio Backup di Azure
* Impostazioni di rete
* Impostazioni di crittografia

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali dei servizi di ripristino

Nei passaggi seguenti viene descritto come creare un insieme di credenziali dei servizi di ripristino. Un insieme di credenziali dei servizi di ripristino è diverso da un insieme di credenziali di backup.

1. Se si sta usando Backup di Azure per la prima volta, è necessario usare il cmdlet **Register-AzResourceProvider** per registrare il provider di Servizi di ripristino di Azure con la propria sottoscrizione.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. L'insieme di credenziali dei servizi di ripristino è una risorsa ARM, pertanto è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e percorso per il gruppo di risorse.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Usare il cmdlet **New-AzRecoveryServicesVault** per creare un nuovo insieme di credenziali. Assicurarsi di specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Specificare il tipo di ridondanza di archiviazione da usare; è possibile usare l' [archiviazione con ridondanza locale (con ridondanza locale)](../storage/common/storage-redundancy-lrs.md) o l' [archiviazione con ridondanza geografica (GRS)](../storage/common/storage-redundancy-grs.md). Nell'esempio seguente l'opzione BackupStorageRedundancy per testVault è impostata su GeoRedundant.

   > [!TIP]
   > Molti cmdlet di Backup di Azure richiedono l'oggetto dell'insieme di credenziali dei servizi di ripristino come input. Per questo motivo, è utile archiviare l'oggetto dell'insieme di credenziali dei servizi di ripristino di Backup in una variabile.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Usare **Get-AzRecoveryServicesVault** per visualizzare l'elenco di tutti gli insiemi di credenziali della sottoscrizione corrente. È possibile usare questo comando per verificare che sia stato creato un nuovo insieme di credenziali o per vedere quali insiemi di credenziali sono disponibili nell'abbonamento.

Eseguire il comando Get-AzRecoveryServicesVault e tutti gli insiemi di credenziali nella sottoscrizione verranno elencati.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Installazione dell'agente di Backup di Azure in un server DPM

Per installare l'agente di Backup di Azure, è necessario aver scaricato il programma di installazione nel server Windows. È possibile ottenere la versione più recente del programma di installazione dall' [Area download Microsoft](https://aka.ms/azurebackup_agent) o dalla pagina Dashboard dell'insieme di credenziali dei servizi di ripristino. Salvare il programma di installazione in un percorso facilmente accessibile come `C:\Downloads\*`.

Per installare l'agente, eseguire il comando seguente in una console di PowerShell con privilegi elevati **nel server DPM**:

```powershell
MARSAgentInstaller.exe /q
```

L'agente verrà installato con tutte le opzioni predefinite. L'installazione richiede alcuni minuti in background. Se non si specifica l'opzione */nu* , al termine dell'installazione verrà aperta la finestra **Windows Update** per verificare la presenza di eventuali aggiornamenti.

L'agente verrà visualizzato nell'elenco dei programmi installati. Per visualizzare l'elenco dei programmi installati, passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità**.

![Agente installato](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opzione di installazione

Per visualizzare tutte le opzioni disponibili tramite la riga di comando, usare il comando seguente:

```powershell
MARSAgentInstaller.exe /?
```

Le opzioni disponibili includono:

| Opzione | Dettagli | Predefinito |
| --- | --- | --- |
| /q |Installazione non interattiva |- |
| /p:"location" |Percorso della cartella di installazione per l'agente di Backup di Azure. |C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure |
| /s:"location" |Percorso della cartella della cache per l'agente di Backup di Azure. |C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure\Scratch |
| /m |Consenso esplicito a Microsoft Update |- |
| /nu |Al termine dell'installazione non vengono cercati gli aggiornamenti |- |
| /d |Disinstalla l'agente di Servizi di ripristino di Microsoft Azure |- |
| /ph |Indirizzo host proxy |- |
| /po |Numero porta host proxy |- |
| /pu |Nome utente host proxy |- |
| /pw |Password proxy |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrazione di Data Protection Manager (DPM) con l'insieme di credenziali dei servizi di ripristino

Dopo aver creato l'insieme di credenziali dei servizi di ripristino, scaricare l'ultimo agente e le credenziali dell'insieme di credenziali e archiviarli in un percorso semplice da ricordare, ad esempio C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Eseguire il cmdlet [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration) nel server DPM per registrare il computer con l'insieme di credenziali.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Impostazioni di configurazione iniziali

Dopo la registrazione con l'insieme di credenziali dei servizi di ripristino, il server DPM verrà avviato con le impostazioni di sottoscrizione predefinite. Tali impostazioni includono rete, crittografia e area di staging. Per modificare le impostazioni di sottoscrizione, è prima necessario ottenere un handle sulle impostazioni (predefinite) esistenti usando il cmdlet [Get-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) :

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Tutte le modifiche vengono apportate a questo oggetto locale PowerShell ```$setting``` e poi viene eseguito il commit dell'oggetto completo in DPM e in Backup di Azure eseguendo il salvataggio mediante il cmdlet [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019). È necessario usare il flag ```–Commit``` per garantire che le modifiche vengano mantenute. Le impostazioni vengono applicate e usate da Backup di Azure solo dopo il commit.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Rete

Se per la connettività del computer DPM al servizio Backup di Azure in Internet viene usato un server proxy, per consentire la corretta esecuzione dei backup è necessario specificare le impostazioni del server proxy. A tale scopo, usare i parametri ```-ProxyServer```, ```-ProxyPort```, ```-ProxyUsername``` e ```ProxyPassword``` con il cmdlet [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019). In questo esempio non esiste alcun server proxy, pertanto sono state eliminate tutte le informazioni relative al proxy.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

È anche possibile controllare l'utilizzo della larghezza di banda con le opzioni ```-WorkHourBandwidth``` e ```-NonWorkHourBandwidth``` per un determinato set di giorni della settimana. In questo esempio non viene impostata alcuna limitazione.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Configurazione dell'area di gestione temporanea

L'agente del servizio Backup di Azure in esecuzione nel server DPM deve disporre di archiviazione temporanea per i dati ripristinati dal cloud (area di staging locale). Configurare l'area di gestione temporanea usando il cmdlet [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) e il parametro ```-StagingAreaPath```.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

Nell'esempio precedente, l'area di gestione temporanea verrà impostata su *C:\StagingArea* nell'oggetto di PowerShell ```$setting```. Assicurarsi che la cartella specificata esista già, altrimenti il commit finale delle impostazioni di sottoscrizione avrà esito negativo.

### <a name="encryption-settings"></a>Impostazioni di crittografia

I dati di backup inviati a Backup di Azure vengono crittografati per proteggere la riservatezza dei dati. La passphrase di crittografia è la "password" per decrittografare i dati in fase di ripristino. È importante conservarla al sicuro e proteggerla dopo averla impostata.

Nel seguente esempio, il primo comando consente di convertire la passphrase della stringa ```passphrase123456789``` in una stringa sicura; inoltre, consente di assegnare la stringa sicura alla variabile denominata ```$Passphrase```. Il secondo comando imposta la stringa sicura in ```$Passphrase``` come password per la crittografia dei backup.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Dopo l'impostazione, conservare le informazioni sulla passphrase al sicuro. Non sarà possibile ripristinare i dati da Azure senza la passphrase.
>
>

A questo punto, sono state apportate tutte le modifiche necessarie all'oggetto ```$setting``` . Ricordarsi di eseguire il commit delle modifiche:

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Proteggere i dati in Backup di Azure

In questa sezione verrà aggiunto un server di produzione a DPM e i dati verranno protetti nell'archivio DPM locale e quindi in Backup di Azure. Negli esempi verrà descritto come eseguire il backup di file e cartelle. È possibile estendere in modo semplice la logica al fine di eseguire il backup di qualsiasi origine dati supportata da DPM. Tutti i backup DPM sono regolati da un gruppo protezione dati costituito da quattro parti:

1. **Membri del gruppo** : elenco di tutti gli oggetti che è possibile proteggere (anche detti *origini dati* in DPM) e che si desidera proteggere nello stesso gruppo protezione dati. Ad esempio, è possibile proteggere le macchine virtuali di produzione in un gruppo protezione dati e i database di SQL Server in un altro gruppo, in quanto potrebbero avere requisiti di backup diversi. Prima di eseguire il backup di qualsiasi origine dati in un server di produzione, è necessario assicurarsi che l'agente DPM sia installato nel server e gestito da DPM. Seguire la procedura di [installazione dell'agente DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) e collegamento dell'agente al server DPM appropriato.
2. **Metodo di protezione dati** : specifica le posizioni di destinazione dei backup, ad esempio nastro, disco e cloud. In questo esempio i dati verranno protetti nel disco locale e nel cloud.
3. **Pianificazione dei backup** : specifica quando devono essere eseguiti i backup e con quale frequenza i dati devono essere sincronizzati tra il server DPM e il server di produzione.
4. Una **pianificazione di conservazione** che indica per quanto tempo è necessario conservare i punti di ripristino in Azure.

### <a name="creating-a-protection-group"></a>Creazione di un gruppo di protezione

Creare innanzitutto un nuovo gruppo protezione dati usando il cmdlet [New-DPMProtectionGroup](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmprotectiongroup?view=systemcenter-ps-2019) .

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Il cmdlet precedente creerà un gruppo protezione dati denominato *ProtectGroup01*. Un gruppo protezione dati esistente può anche essere modificato in seguito per aggiungere il backup nel cloud Azure. Tuttavia, per apportare modifiche al gruppo protezione dati, nuovo o esistente, è necessario ottenere un handle su un oggetto *modificabile* usando il cmdlet [Get-DPMModifiableProtectionGroup](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup?view=systemcenter-ps-2019) .

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Aggiunta di membri al gruppo protezione dati

Ogni agente DPM conosce l'elenco di origini dati nel server in cui è installato. Per aggiungere un'origine dati al gruppo protezione dati, l'agente DPM deve innanzitutto inviare un elenco delle origini dati al server DPM. Vengono quindi selezionate una o più origini dati, che vengono aggiunte al gruppo protezione dati. La procedura di PowerShell da eseguire a questo scopo è la seguente:

1. Recuperare un elenco di tutti i server gestiti da DPM tramite l'agente DPM.
2. Scegliere un server specifico.
3. Recuperare un elenco di tutte le origini dati nel server.
4. Scegliere una o più origini dati e aggiungerle al gruppo protezione dati.

È possibile acquisire l'elenco di tutti i server in cui è installato l'agente DPM e che sono gestiti dal server DPM tramite il cmdlet [Get-DPMProductionServer](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmproductionserver?view=systemcenter-ps-2019) . In questo esempio verrà applicato un filtro e per il backup verrà configurato solo il server di produzione denominato *productionserver01* .

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Recuperare quindi l'elenco di origini dati in ```$server``` usando il cmdlet [Get-DPMDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmdatasource?view=systemcenter-ps-2019). In questo esempio viene filtrato il volume `D:\`, da configurare per il backup. L'origine dati viene quindi aggiunta al gruppo protezione dati usando il cmdlet [Add-DPMChildDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019). Ricordarsi di usare l'oggetto gruppo protezione dati *modificabile*```$MPG``` per effettuare le aggiunte.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Ripetere questo passaggio il numero di volte necessario per aggiungere tutte le origini dati scelte al gruppo protezione dati. È anche possibile iniziare con una sola origine dati e completare il flusso di lavoro per la creazione del gruppo protezione dati, quindi, in un secondo momento, aggiungere altre origini dati al gruppo protezione dati.

### <a name="selecting-the-data-protection-method"></a>Selezione del metodo di protezione dati

Una volta aggiunte le origini dati al gruppo protezione dati, il passaggio successivo consiste nello specificare il metodo di protezione usando il cmdlet [Set-DPMProtectionType](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiontype?view=systemcenter-ps-2019) . In questo esempio, il gruppo protezione dati viene configurato per il backup su disco locale e nel cloud. È inoltre necessario specificare l'origine dati che si desidera proteggere per il cloud utilizzando il cmdlet [Aggiungi DPMChildDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) con -flag Online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Impostazione del periodo di mantenimento dati

Impostare il periodo di conservazione per i punti di backup usando il cmdlet [Set-DPMPolicyObjective](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) . Anche se può sembrare strano impostare il periodo di conservazione prima di definire la pianificazione dei backup, usando il cmdlet ```Set-DPMPolicyObjective``` viene automaticamente impostata una pianificazione dei backup predefinita che può quindi essere modificata. È sempre possibile impostare prima la pianificazione dei backup e successivamente i criteri di conservazione.

Nell'esempio seguente il cmdlet imposta i parametri di conservazione per i backup su disco. I backup verranno conservati per 10 giorni e i dati verranno sincronizzati ogni 6 ore tra il server di produzione e il server DPM. ```SynchronizationFrequencyMinutes``` non definisce la frequenza di creazione di un punto di backup, ma la frequenza con cui i dati vengono copiati nel server DPM.  Questa impostazione impedisce che i backup diventino di dimensioni eccessive.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Per i backup destinati ad Azure (indicati in DPM come backup online), è possibile configurare i periodi di mantenimento dati per la [conservazione a lungo termine con uno schema GFS (Grandfather-Father-Son)](backup-azure-backup-cloud-as-tape.md). Ciò significa che è possibile definire un criterio di conservazione combinato che includa criteri giornalieri, settimanali, mensili e annuali. In questo esempio viene creata una matrice che rappresenta lo schema di conservazione complesso desiderato e quindi viene configurato il periodo di mantenimento dati usando il cmdlet [Set-DPMPolicyObjective](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) .

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Impostare la pianificazione dei backup

Se si specifica l'obiettivo di protezione usando il cmdlet ```Set-DPMPolicyObjective``` , DPM imposta automaticamente una pianificazione dei backup predefinita. Per modificare le pianificazioni predefinite, usare il cmdlet [Get DPMPolicySchedule](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmpolicyschedule?view=systemcenter-ps-2019), seguito da quello [Set DPMPolicySchedule](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyschedule?view=systemcenter-ps-2019).

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

Nell'esempio precedente, ```$onlineSch``` è una matrice con quattro elementi che contiene la pianificazione della protezione online esistente per il gruppo protezione dati nello schema GFS:

1. ```$onlineSch[0]``` contiene la pianificazione giornaliera
2. ```$onlineSch[1]``` contiene la pianificazione settimanale
3. ```$onlineSch[2]``` contiene la pianificazione mensile
4. ```$onlineSch[3]``` contiene la pianificazione annuale

Se pertanto è necessario modificare la pianificazione settimanale, è necessario fare riferimento a ```$onlineSch[1]```.

### <a name="initial-backup"></a>Backup iniziale

Quando si esegue il backup di un'origine dati per la prima volta, DPM deve creare una replica iniziale con cui viene creata una copia completa dell'origine dati da proteggere nel volume di replica DPM. È possibile pianificare questa attività a un orario specifico oppure attivarla manualmente, usando il cmdlet [Set-DPMReplicaCreationMethod](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod?view=systemcenter-ps-2019) con il parametro ```-NOW```.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Modifica delle dimensioni della replica DPM e volume del punto di ripristino

È inoltre possibile modificare le dimensioni del volume di replica DPM e del volume della copia shadow usando il cmdlet [Set-DPMDatasourceDiskAllocation](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation?view=systemcenter-ps-2019) come illustrato di seguito: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Commit delle modifiche nel gruppo protezione dati

Infine, è necessario eseguire il commit delle modifiche affinché DPM possa eseguire il backup in base alla nuova configurazione del gruppo protezione dati. A tale scopo, usare il cmdlet [Set-DPMProtectionGroup](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiongroup?view=systemcenter-ps-2019) .

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Visualizzare i punti di backup

È possibile usare il cmdlet [Get-DPMRecoveryPoint](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmrecoverypoint?view=systemcenter-ps-2019) per ottenere un elenco di tutti i punti di ripristino per un'origine dati. Nell'esempio seguente, vengono:

* recuperati tutti i gruppi protezione dati (PG) nel server DPM con archiviazione in una matrice ```$PG```
* ottenuti le origini dati corrispondenti alla matrice ```$PG[0]```
* ottenuti tutti i punti di ripristino per un'origine dati.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Ripristino dei dati protetti su Azure

Il ripristino dei dati è una combinazione tra un oggetto ```RecoverableItem``` e un oggetto ```RecoveryOption```. Nella sezione precedente è stato ottenuto un elenco dei punti di backup per un'origine dati.

Nell'esempio seguente viene illustrato come ripristinare una macchina virtuale Hyper-V da Backup di Azure mediante la combinazione di punti di backup con la destinazione per il ripristino. L'esempio include quanto segue:

* Creazione di un'opzione di ripristino usando il cmdlet [New-DPMRecoveryOption](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmrecoveryoption?view=systemcenter-ps-2019).
* Recupero della matrice di punti di backup usando il cmdlet ```Get-DPMRecoveryPoint``` .
* Scelta di un punto di backup da cui eseguire il ripristino.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

I comandi possono essere facilmente estesi per qualsiasi tipo di origine dati.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su DPM e Backup di Azure, vedere [Preparazione del backup dei carichi di lavoro in Azure con DPM](backup-azure-dpm-introduction.md)
