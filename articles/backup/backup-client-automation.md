---
title: Usare PowerShell per eseguire il backup di Windows Server in Azure
description: Questo articolo illustra come usare PowerShell per la configurazione di backup di Azure in Windows Server o un client Windows e la gestione di backup e ripristino.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 3b9bcf8e777244cec11383619d145e3a99ff46d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193021"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Distribuire e gestire il backup in Azure per server Windows/client Windows mediante PowerShell

Questo articolo illustra come usare PowerShell per configurare Backup di Azure in un server o un client Windows e per gestire le operazioni di backup e ripristino.

## <a name="install-azure-powershell"></a>Installare Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per iniziare, [installare la versione più recente di PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali dei servizi di ripristino

Nei passaggi seguenti viene descritto come creare un insieme di credenziali dei servizi di ripristino. Un insieme di credenziali dei servizi di ripristino è diverso da un insieme di credenziali di backup.

1. Se si sta usando Backup di Azure per la prima volta, è necessario usare il cmdlet **Register-AzResourceProvider** per registrare il provider di Servizi di ripristino di Azure con la propria sottoscrizione.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. L'insieme di credenziali dei servizi di ripristino è una risorsa ARM, pertanto è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e percorso per il gruppo di risorse.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Usare il cmdlet **New-AzRecoveryServicesVault** per creare il nuovo insieme di credenziali. Assicurarsi di specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Specificare il tipo di ridondanza di archiviazione da usare, ad esempio [archiviazione con ridondanza locale (LRS)](../storage/common/storage-redundancy-lrs.md) o [archiviazione con ridondanza geografica (GRS)](../storage/common/storage-redundancy-grs.md). Nell'esempio seguente l'opzione BackupStorageRedundancy per testVault è impostata su GeoRedundant.

   > [!TIP]
   > Molti cmdlet di Backup di Azure richiedono l'oggetto dell'insieme di credenziali dei servizi di ripristino come input. Per questo motivo, è utile archiviare l'oggetto dell'insieme di credenziali dei servizi di ripristino di Backup in una variabile.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Usare **Get-AzRecoveryServicesVault** per visualizzare l'elenco di tutti gli insiemi di credenziali nella sottoscrizione corrente. È possibile usare questo comando per verificare che sia stato creato un nuovo insieme di credenziali o per vedere quali insiemi di credenziali sono disponibili nell'abbonamento.

Eseguire il comando **Get-AzRecoveryServicesVault**e tutti gli insiemi di credenziali nella sottoscrizione sono elencati.

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

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Installazione dell'agente di Backup di Azure

Per installare l'agente di Backup di Azure, è necessario aver scaricato il programma di installazione nel server Windows. È possibile ottenere la versione più recente del programma di installazione dall' [Area download Microsoft](https://aka.ms/azurebackup_agent) o dalla pagina Dashboard dell'insieme di credenziali dei servizi di ripristino. Salvare il programma di installazione in una posizione facilmente `C:\Downloads\*`accessibile, ad esempio.

In alternativa, usare PowerShell per ottenere il downloader:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Per installare l'agente, eseguire il comando seguente in una console di Azure PowerShell con privilegi elevati:

```powershell
MARSAgentInstaller.exe /q
```

L'agente verrà installato con tutte le opzioni predefinite. L'installazione richiede alcuni minuti in background. Se non si specifica l'opzione */nu* , alla fine dell'installazione verrà visualizzata la finestra **Windows Update** per verificare la presenza di eventuali aggiornamenti. Dopo essere stato installato, l'agente verrà visualizzato nell'elenco dei programmi installati.

Per visualizzare l'elenco dei programmi installati, passare a **Pannello** > **Programs** > di controllo programmi programmi**e funzionalità**.

![Agente installato](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opzione di installazione

Per visualizzare tutte le opzioni disponibili tramite la riga di comando, usare il comando seguente:

```powershell
MARSAgentInstaller.exe /?
```

Le opzioni disponibili includono:

| Opzione | Dettagli | Impostazione predefinita |
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

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registrazione di Windows Server o di un client Windows con l'insieme di credenziali dei servizi di ripristino

Dopo aver creato l'insieme di credenziali dei servizi di ripristino, scaricare l'ultimo agente e le credenziali dell'insieme di credenziali e archiviarli in un percorso semplice da ricordare, ad esempio C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Registrazione tramite il modulo PS AZ

> [!NOTE]
> Un bug con la generazione del certificato dell'insieme di credenziali è corretto in AZ 3.5.0 Release. Usare il comando AZ 3.5.0 Release Version o versione successiva per scaricare un certificato dell'insieme di credenziali.

Nel modulo AZ di PowerShell più recente, a causa delle limitazioni della piattaforma sottostanti, il download delle credenziali dell'insieme di credenziali richiede un certificato autofirmato. Nell'esempio seguente viene illustrato come fornire un certificato autofirmato e scaricare le credenziali dell'insieme di credenziali.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

In Windows Server o in un client Windows, eseguire il cmdlet [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) per registrare il computer con l'insieme di credenziali.
Questo e altri cmdlet usati per il backup sono dal modulo MSONLINE, che è stato aggiunto da Mars Agent Installer come parte del processo di installazione.

Il programma di installazione dell'agente non aggiorna la variabile $Env:PSModulePath. Il caricamento automatico del modulo avrà quindi esito negativo. Per risolvere questo problema, è possibile eseguire le operazioni seguenti:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

In alternativa, è possibile caricare manualmente il modulo nello script, come segue:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Dopo il caricamento dei cmdlet di Online Backup, registrare le credenziali dell'insieme di credenziali:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Non utilizzare percorsi relativi per specificare il file dell'insieme di credenziali. È necessario fornire un percorso assoluto come input per il cmdlet.
>
>

## <a name="networking-settings"></a>Impostazioni di rete

Quando il computer Windows si connette a Internet mediante un server proxy, le impostazioni del proxy possono essere fornite anche all'agente. In questo esempio non è presente alcun server proxy, pertanto sono state eliminate tutte le informazioni relative al proxy.

È anche possibile controllare l'utilizzo della larghezza di banda con le opzioni `work hour bandwidth` e `non-work hour bandwidth` per un dato set di giorni della settimana.

L'impostazione dei dettagli relativi a proxy e larghezza di banda viene eseguita mediante il cmdlet [Set-OBMachineSetting](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obmachinesetting?view=winserver2012-ps) :

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Impostazioni di crittografia

I dati di backup inviati a Backup di Azure vengono crittografati per proteggere la riservatezza dei dati. La passphrase di crittografia è la "password" per decrittografare i dati in fase di ripristino.

È necessario generare un pin di sicurezza selezionando **genera**, in **Impostazioni** > **Proprietà** > **pin di sicurezza** nella sezione dell'insieme di credenziali dei **servizi di ripristino** del portale di Azure. Usare quindi come `generatedPIN` nel comando:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Dopo l'impostazione, conservare le informazioni sulla passphrase al sicuro. Non è possibile ripristinare dati da Azure senza la passphrase.
>
>

## <a name="back-up-files-and-folders"></a>Eseguire il backup di file e cartelle

Tutti i backup dei server e dei client Windows in Backup di Azure sono regolati da un criterio, costituito da tre parti:

1. Una **pianificazione dei backup** che specifica quando è necessario eseguire i backup e sincronizzarli con il servizio.
2. Una **pianificazione di conservazione** che indica per quanto tempo è necessario conservare i punti di ripristino in Azure.
3. Una **specifica di inclusione/esclusione di file** che determina i contenuti di cui eseguire il backup.

Dal momento che in questo documento si esegue un backup automatico, si presuppone che non siano stati configurati elementi. Si inizia creando un nuovo criterio di backup tramite il cmdlet [New-OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obpolicy?view=winserver2012-ps) .

```powershell
$NewPolicy = New-OBPolicy
```

In questo momento il criterio è vuoto e sono necessari altri cmdlet per definire quali elementi verranno inclusi o esclusi, quando verranno eseguiti i backup e dove verranno archiviati.

### <a name="configuring-the-backup-schedule"></a>Configurazione della pianificazione dei backup

La prima delle tre parti di un criterio è la pianificazione del backup, creata con il cmdlet [New-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obschedule?view=winserver2012-ps) . La pianificazione dei backup definisce quando è necessario eseguire i backup. Quando si crea una pianificazione, è necessario specificare due parametri di input:

* **Giorni della settimana** in cui deve essere eseguito il backup. È possibile eseguire il processo di backup in un solo giorno oppure tutti i giorni della settimana o specificando qualsiasi combinazione di giorni.
* **Orari della giornata** in cui deve essere eseguito il backup. È possibile definire fino a tre orari diversi del giorno in cui verrà attivato il backup.

Ad esempio, è possibile configurare un criterio di backup eseguito alle 16.00 ogni sabato e domenica.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

La pianificazione dei backup deve essere associata a un criterio ed è possibile eseguire questa operazione tramite il cmdlet [Set-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obschedule?view=winserver2012-ps) .

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Configurazione di un criterio di conservazione

Il criterio di conservazione definisce il periodo di conservazione dei punti di ripristino creati dai processi di backup. Quando si crea un nuovo criterio di conservazione usando il cmdlet [New-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obretentionpolicy?view=winserver2012-ps) , è possibile specificare il numero di giorni per cui conservare i punti di ripristino dei backup con Backup di Azure. Nell'esempio seguente vengono impostati i criteri di conservazione di sette giorni.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Il criterio di conservazione deve essere associato al criterio principale usando il cmdlet [Set-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obretentionpolicy?view=winserver2012-ps):

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="including-and-excluding-files-to-be-backed-up"></a>Inclusione ed esclusione di file per il backup

Un oggetto `OBFileSpec` definisce i file da includere o escludere in un backup. Si tratta di un set di regole che definiscono l'ambito di cartelle e file protetti in un computer. È possibile disporre del numero desiderato di regole di inclusione o esclusione di file e associare le regole a un criterio. Quando si crea un nuovo oggetto OBFileSpec, è possibile:

* Specificare file e cartelle da includere
* Specificare file e cartelle da escludere
* Specificare un backup ricorsivo dei dati in una cartella (o) se eseguire il backup solo dei file di livello principale nella cartella specificata.

Quest'ultima impostazione si ottiene usando il flag -NonRecursive nel comando New-OBFileSpec.

Nell'esempio seguente viene eseguito il backup dei volumi C: e D: e vengono esclusi i file binari del sistema operativo nella cartella Windows e nelle cartelle temporanee. A tale scopo, verranno create due specifiche di file usando il cmdlet [New-OBFileSpec](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obfilespec?view=winserver2012-ps) , una per l'inclusione e l'altra per l'esclusione. Dopo essere state create, le specifiche dei file vengono associate al criterio usando il cmdlet [Add-OBFileSpec](https://docs.microsoft.com/powershell/module/msonlinebackup/add-obfilespec?view=winserver2012-ps) .

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Applicazione del criterio

A questo punto l'oggetto criterio è completo e associato a una pianificazione dei backup, un criterio di conservazione e un elenco di inclusione o esclusione di file. È ora possibile eseguire il commit del criterio affinché venga usato in Backup di Azure. Prima di applicare il criterio appena creato, assicurarsi che non siano presenti criteri di backup esistenti associati al server tramite il cmdlet [Remove-OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/remove-obpolicy?view=winserver2012-ps) . Per la rimozione del criterio verrà richiesta una conferma. Per ignorare la conferma, usare il `-Confirm:$false` flag con il cmdlet.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Il commit dell'oggetto criterio viene eseguito usando il cmdlet [Set-OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obpolicy?view=winserver2012-ps) . Anche in questo caso verrà richiesto di confermare. Per ignorare la conferma, usare il `-Confirm:$false` flag con il cmdlet.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

È possibile visualizzare i dettagli del criterio di backup esistente usando il cmdlet [Get-OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obpolicy?view=winserver2012-ps) . È possibile eseguire un drill-down più approfondito usando il cmdlet [Get-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obschedule?view=winserver2012-ps) per la pianificazione dei backup e il cmdlet[Get-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obretentionpolicy?view=winserver2012-ps) per i criteri di conservazione

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-on-demand-backup"></a>Esecuzione di un backup su richiesta

Una volta impostati i criteri di backup, i backup vengono eseguiti in base alla pianificazione. Per attivare un backup su richiesta, è anche possibile usare il cmdlet [Start-OBBackup](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obbackup?view=winserver2012-ps) :

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>Eseguire il backup dello stato del sistema Windows Server nell'agente MAB

Questa sezione descrive il comando di PowerShell per configurare lo stato del sistema nell'agente MAB

### <a name="schedule"></a>Pianificazione

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Conservazione

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Configurazione della pianificazione e della conservazione

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Verifica del criterio

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Ripristinare i dati da Backup di Azure

Questa sezione illustra i passaggi per l'automazione del ripristino dei dati da Backup di Azure. A tale scopo, sono necessari i passaggi seguenti:

1. Selezionare il volume di origine
2. Scegliere un punto di backup da ripristinare
3. Specificare un elemento da ripristinare
4. Attivare il processo di ripristino

### <a name="picking-the-source-volume"></a>Selezione del volume di origine

Per ripristinare un elemento da Backup di Azure, è necessario innanzitutto identificare l'origine dell'elemento. Poiché i comandi vengono eseguiti nel contesto di un server o un client Windows, il computer è già identificato. Il passaggio successivo nell'identificazione dell'origine consiste nell'identificare il volume che la contiene. È possibile recuperare un elenco dei volumi o delle origini di cui viene eseguito il backup per il computer eseguendo il cmdlet [Get-OBRecoverableSource](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverablesource?view=winserver2012-ps) . Questo comando restituisce una matrice di tutte le origini di cui viene eseguito il backup nel server/client.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Scelta di un punto di backup da cui eseguire il ripristino

Per recuperare un elenco di punti di backup, eseguire il cmdlet [Get-OBRecoverableItem](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverableitem?view=winserver2012-ps) con i parametri appropriati. In questo esempio si sceglierà il punto di backup più recente per il volume di origine *C:* e lo si userà per ripristinare un file specifico.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

L'oggetto `$Rps` è una matrice di punti di backup. Il primo elemento è il punto più recente e l'elemento N è il punto meno recente. Per scegliere il punto più recente, si userà `$Rps[0]`.

### <a name="specifying-an-item-to-restore"></a>Specifica di un elemento da ripristinare

Per ripristinare un file specifico, specificare il nome del file relativo al volume radice. Per recuperare C:\Test\Cat.job, ad esempio, eseguire il comando seguente.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>Attivazione del processo di ripristino

Per attivare il processo di ripristino, è prima necessario specificare le opzioni di ripristino. A tale scopo, è possibile usare il cmdlet [New-OBRecoveryOption](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obrecoveryoption?view=winserver2012-ps) . Per questo esempio, supponiamo di voler ripristinare i file in *C:\Temp*. Si supponga inoltre di voler ignorare i file già esistenti nella cartella di destinazione *C:\Temp*. Per creare un'opzione di ripristino di questo tipo, utilizzare il comando seguente:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Attivare quindi il processo di ripristino usando il comando [Start-OBRecovery](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obrecovery?view=winserver2012-ps) sull'oggetto `$Item` selezionato dall'output del cmdlet `Get-OBRecoverableItem`:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Disinstallazione dell'agente di Backup di Azure

La disinstallazione dell'agente di Backup di Azure può essere eseguita mediante il seguente comando:

```powershell
.\MARSAgentInstaller.exe /d /q
```

La disinstallazione dei file binari dell'agente dal computer comporta alcune conseguenze da tenere in considerazione:

* Il filtro di file viene rimosso dal computer e il rilevamento delle modifiche viene arrestato.
* Tutte le informazioni sui criteri vengono rimosse dal computer, ma continuano a essere archiviate nel servizio.
* Tutte le pianificazioni dei backup vengono rimosse e non vengono eseguiti ulteriori backup.

Tuttavia, i dati archiviati in Azure continueranno a rimanere presenti e verranno conservati in base alla configurazione del criterio di conservazione specificata. I punti meno recenti scadono automaticamente.

## <a name="remote-management"></a>Gestione remota

Tutte le operazioni di gestione di origini dati, criteri e agente di Backup di Azure possono essere eseguite in remoto mediante PowerShell. Il computer che verrà gestito in remoto deve essere preparato correttamente.

Per impostazione predefinita, il servizio Gestione remota Windows è configurato per l'avvio manuale. Il tipo di avvio deve essere impostato su *Automatico* e il servizio deve essere avviato. Per verificare che il servizio Gestione remota Windows sia in esecuzione, il valore della proprietà Status deve essere *In esecuzione*.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell deve essere configurato per la comunicazione remota.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

È ora possibile gestire il computer in remoto, a partire dall'installazione dell'agente. Ad esempio, il seguente script copia l'agente nel computer remoto e lo installa.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su backup di Azure per Windows Server/client:

* [Introduzione a Backup di Azure](backup-introduction-to-azure-backup.md)
* [Backup di server Windows](backup-windows-with-mars-agent.md)
