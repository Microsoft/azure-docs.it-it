---
title: DATABASE SQL nel backup di VM di Azure & ripristino tramite PowerShell
description: Eseguire il backup e il ripristino di database SQL in macchine virtuali di Azure con backup di Azure e PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 0a3467ffa3a67ac9ad593748948cea8da59e3e6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97734539"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Eseguire il backup e il ripristino di database SQL in macchine virtuali di Azure con PowerShell

Questo articolo descrive come usare Azure PowerShell per eseguire il backup e il ripristino di un database SQL in una VM di Azure usando l'insieme di credenziali di servizi di ripristino di [backup di Azure](backup-overview.md) .

Questo articolo spiega come:

> [!div class="checklist"]
>
> * Configurare PowerShell e registrare il provider di servizi di ripristino di Azure.
> * Creare un insieme di credenziali dei servizi di ripristino.
> * Configurare il backup per il database SQL in una macchina virtuale di Azure.
> * Eseguire un processo di backup.
> * Ripristinare un database SQL di cui è stato eseguito il backup.
> * Monitorare i processi di backup e ripristino.

## <a name="before-you-start"></a>Prima di iniziare

* [Altre](backup-azure-recovery-services-vault-overview.md) informazioni sugli insiemi di credenziali dei servizi di ripristino.
* Informazioni sulle funzionalità per il backup di database [SQL in macchine virtuali di Azure](backup-azure-sql-database.md#before-you-start).
* Esaminare la gerarchia di oggetti di PowerShell per i servizi di ripristino.

### <a name="recovery-services-object-hierarchy"></a>Gerarchia di oggetti dei servizi di ripristino

La gerarchia di oggetti viene riepilogata nel diagramma seguente.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Esaminare il riferimento al [cmdlet](/powershell/module/az.recoveryservices) **AZ. RecoveryServices** nella libreria di Azure.

### <a name="set-up-and-install"></a>Configurare e installare

Configurare PowerShell nel modo seguente:

1. [Scaricare la versione più recente di Az PowerShell](/powershell/azure/install-az-ps). La versione minima richiesta è la versione 1.5.0.

2. Trovare i cmdlet di PowerShell per backup di Azure con questo comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Esaminare gli alias e i cmdlet per backup di Azure e l'insieme di credenziali di servizi di ripristino. Ecco un esempio di ciò che è possibile vedere. Non si tratta di un elenco completo di cmdlet.

    ![Elenco di cmdlet dei Servizi di ripristino](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Accedere al proprio account Azure con **Connect-AzAccount**.
5. Nella pagina Web visualizzata verrà richiesto di immettere le credenziali dell'account.

    * In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet **Connect-AzAccount** con **-Credential**.
    * Se si è un partner CSP che lavora per un tenant, specificare il cliente come tenant, usando il nome di dominio primario tenantID o tenant. Un esempio è **Connect-AzAccount -Tenant** fabrikam.com.

6. Associare la sottoscrizione che si vuole usare all'account perché un account può avere più sottoscrizioni.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se si sta usando Backup di Azure per la prima volta, usare il cmdlet **Register-AzResourceProvider** per registrare il provider di Servizi di ripristino di Azure con la propria sottoscrizione.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verificare che i provider siano stati registrati correttamente:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Nell'output del comando verificare che **RegistrationState** venga modificato in **registrato**. In caso contrario, eseguire di nuovo il cmdlet **Register-AzResourceProvider** .

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Seguire questa procedura per creare un insieme di credenziali di Servizi di ripristino.

L'insieme di credenziali di Servizi di ripristino è una risorsa di Resource Manager, quindi è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno con il cmdlet **New-AzResourceGroup**. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e il percorso per il gruppo di risorse.

1. Un insieme di credenziali viene inserito in un gruppo di risorse. Se non si dispone di un gruppo di risorse esistente, crearne uno nuovo con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In questo esempio viene creato un nuovo gruppo di risorse nell'area Stati Uniti occidentali.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Usare il cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) per creare l'insieme di credenziali. Specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Specificare il tipo di ridondanza da usare per l'archiviazione dell'insieme di credenziali.

    * È possibile usare l'archiviazione con ridondanza [locale](../storage/common/storage-redundancy.md#locally-redundant-storage), l'archiviazione con [ridondanza geografica](../storage/common/storage-redundancy.md#geo-redundant-storage) o l' [archiviazione con ridondanza della zona](../storage/common/storage-redundancy.md#zone-redundant-storage) .
    * Nell'esempio seguente viene impostata l'opzione **-BackupStorageRedundancy** per [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd per **testvault** impostato su **georidondante**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Per visualizzare tutti gli insiemi di credenziali disponibili nella sottoscrizione, usare [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault).

```powershell
Get-AzRecoveryServicesVault
```

L'output è simile al seguente. Vengono forniti il gruppo di risorse e la località associati.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Impostare il contesto dell'insieme di credenziali

Archiviare l'oggetto insieme di credenziali in una variabile e impostare il contesto dell'insieme di credenziali.

* Molti cmdlet di backup di Azure richiedono l'oggetto insieme di credenziali dei servizi di ripristino come input, quindi è consigliabile archiviare l'oggetto insieme di credenziali in una variabile.
* Il contesto dell'insieme di credenziali definisce il tipo di dati protetti nell'insieme di credenziali. Impostarla con [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). Una volta impostato il contesto, si applica a tutti i cmdlet successivi.

L'esempio seguente imposta il contesto per **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Recuperare l'ID dell'insieme di credenziali

Si prevede di deprecare l'impostazione del contesto dell'insieme di credenziali in base alle linee guida Azure PowerShell. È invece possibile archiviare o recuperare l'ID dell'insieme di credenziali e passarlo ai comandi pertinenti, come indicato di seguito:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

I criteri di backup specificano la pianificazione per i backup e il tempo di mantenimento dei punti di ripristino del backup:

* I criteri di backup sono associati ai criteri di conservazione. Un criterio di conservazione definisce per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione.
* Visualizzare il periodo di conservazione predefinito dei criteri di backup usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Visualizzare la pianificazione predefinita dei criteri di backup utilizzando [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject).
* Usare il cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) per creare un nuovo criterio di backup. Vengono inseriti gli oggetti Criteri di pianificazione e conservazione.

Per impostazione predefinita, nell'oggetto Criteri di pianificazione è definita un'ora di inizio. Usare l'esempio seguente per modificare l'ora di inizio con l'ora di inizio desiderata. L'ora di inizio desiderata dovrebbe essere anche UTC. Nell'esempio seguente si presuppone che l'ora di inizio desiderata sia 01:00 UTC per i backup giornalieri.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> È necessario specificare l'ora di inizio solo in più di 30 minuti. Nell'esempio precedente può essere solo "01:00:00" o "02:30:00". L'ora di inizio non può essere "01:15:00".

Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. USA quindi tali variabili come parametri per un nuovo criterio (**NewSQLPolicy**). **NewSQLPolicy** esegue un backup giornaliero "completo", lo conserva per 180 giorni ed esegue un backup del log ogni 2 ore

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

L'output è simile al seguente.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Abilita backup

### <a name="registering-the-sql-vm"></a>Registrazione della macchina virtuale SQL

Per i backup di macchine virtuali di Azure e le condivisioni file di Azure, il servizio di backup può connettersi a queste risorse Azure Resource Manager e recuperare i dettagli pertinenti. Poiché SQL è un'applicazione all'interno di una macchina virtuale di Azure, il servizio di backup richiede l'autorizzazione per accedere all'applicazione e recuperare i dettagli necessari. A tale scopo, è necessario *registrare* la macchina virtuale di Azure che contiene l'applicazione SQL con un insieme di credenziali di servizi di ripristino. Dopo la registrazione di una VM SQL con un insieme di credenziali, è possibile proteggere solo i database SQL in tale insieme di credenziali. Usare il cmdlet [Register-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) di PowerShell per registrare la macchina virtuale.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Il comando restituirà' backup container ' di questa risorsa e lo stato sarà' registered '

> [!NOTE]
> Se il parametro Force non è specificato, viene chiesto di confermare con il testo "disabilitare la protezione per questo contenitore". Ignorare questo testo e pronunciare "Y" per confermare. Si tratta di un problema noto e si sta lavorando per rimuovere il testo e il requisito per il parametro Force.

### <a name="fetching-sql-dbs"></a>Recupero di database SQL

Al termine della registrazione, il servizio backup sarà in grado di elencare tutti i componenti SQL disponibili all'interno della macchina virtuale. Per visualizzare tutti i componenti SQL ancora di cui eseguire il backup in questo insieme di credenziali, usare il cmdlet [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) di PowerShell

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

L'output visualizzerà tutti i componenti SQL non protetti in tutte le macchine virtuali SQL registrate in questo insieme di credenziali con tipo di elemento e ServerName. È possibile filtrare ulteriormente per una particolare VM SQL passando il parametro '-container ' o usare la combinazione di ' name ' è ServerName ' insieme al flag ItemType per giungere a un elemento SQL univoco.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Configurazione del backup

Ora che si dispone del database SQL necessario e dei criteri con cui è necessario eseguirne il backup, è possibile usare il cmdlet [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) per configurare il backup per questo database SQL.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

Il comando attende fino al completamento del backup di configurazione e restituisce il seguente output.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Recupero di nuovi database SQL

Al termine della registrazione del computer, il servizio di backup recupererà i dettagli dei database disponibili. Se i database SQL o le istanze SQL vengono aggiunti al computer registrato in un secondo momento, è necessario attivare manualmente il servizio di backup per eseguire un'indagine aggiornata per ottenere nuovamente **tutti** i database non protetti, inclusi quelli appena aggiunti. Usare il cmdlet di PowerShell [Initialize-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/initialize-azrecoveryservicesbackupprotectableitem) nella macchina virtuale SQL per eseguire una nuova richiesta. Il comando attende fino al completamento dell'operazione. Usare quindi il cmdlet di PowerShell [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) per ottenere l'elenco dei componenti SQL non protetti più recenti.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Una volta recuperati gli elementi da proteggere pertinenti, abilitare i backup come indicato nella [sezione precedente](#configuring-backup).
Se non si desidera rilevare manualmente i nuovi database, è possibile optare per la protezione, come illustrato di [seguito](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Abilita protezione dati

È possibile configurare il backup in modo che tutti i database aggiunti in futuro vengano protetti automaticamente con un determinato criterio. Per abilitare la protezione dati, usare il cmdlet [Enable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection) di PowerShell.

Poiché l'istruzione consiste nel eseguire il backup di tutti i database futuri, l'operazione viene eseguita a livello di SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Una volta assegnato lo scopo di protezione automatica, la richiesta di recupero dei database appena aggiunti viene eseguita come attività in background pianificata ogni 8 ore.

## <a name="restore-sql-dbs"></a>Ripristinare database SQL

Backup di Azure è in grado di ripristinare SQL Server database in esecuzione in macchine virtuali di Azure, come indicato di seguito:

* Ripristinare una data o un'ora specifica (al secondo) utilizzando i backup del log delle transazioni. Backup di Azure determina automaticamente il backup completo differenziale appropriato e la catena di backup del log necessari per il ripristino in base all'ora selezionata.
* Ripristinare un backup completo o differenziale specifico per eseguire il ripristino in un punto di ripristino specifico.

Verificare i prerequisiti indicati [qui](restore-sql-database-azure-vm.md#restore-prerequisites) prima di ripristinare database SQL.

Recuperare prima di tutto il database SQL di cui è stato eseguito il backup usando il cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) di PowerShell.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Recuperare il tempo di ripristino pertinente

Come descritto in precedenza, è possibile ripristinare il database SQL di cui è stato eseguito il backup in una copia completa **o** differenziale o in un log temporizzato.

#### <a name="fetch-distinct-recovery-points"></a>Recupera punti di ripristino distinti

Usare [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) per recuperare punti di ripristino distinti (completi/differenziali) per un database SQL di cui è stato eseguito il backup.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

L'output è simile all'esempio seguente:

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Usare il filtro ' RecoveryPointId ' o un filtro di matrice per recuperare il punto di ripristino pertinente.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Recupera punto di ripristino temporizzato

Se si vuole ripristinare il database in un determinato punto nel tempo, usare il cmdlet [Get-AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) di PowerShell. Il cmdlet restituisce un elenco di date che rappresentano l'ora di inizio e di fine di una catena di log continua non interruppe per tale elemento di backup SQL. Il punto nel tempo desiderato deve essere compreso in questo intervallo.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -VaultId $targetVault.ID
```

L'output sarà simile all'esempio seguente.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

L'output precedente indica che è possibile eseguire il ripristino in qualsiasi punto nel tempo tra l'ora di inizio e l'ora di fine visualizzate. Gli orari sono in formato UTC. Costruire qualsiasi punto nel tempo in PowerShell incluso nell'intervallo illustrato in precedenza.

> [!NOTE]
> Quando si seleziona un log temporizzato per il ripristino, non è necessario specificare il punto di partenza, ovvero il backup completo da cui viene ripristinato il database. Il servizio backup di Azure si occuperà dell'intero piano di ripristino, ovvero del backup completo da scegliere, dei backup del log da applicare e così via.

### <a name="determine-recovery-configuration"></a>Determinare la configurazione del ripristino

Nel caso di un ripristino del database SQL, sono supportati i seguenti scenari di ripristino.

* Override del database SQL di cui è stato eseguito il backup con i dati di un altro punto di ripristino-OriginalWorkloadRestore
* Ripristino del database SQL come nuovo database nella stessa istanza di SQL-AlternateWorkloadRestore
* Ripristino del database SQL come nuovo database in un'altra istanza di SQL in un'altra VM SQL-AlternateWorkloadRestore
* Ripristino del database SQL come file con estensione bak-RestoreAsFiles

Dopo aver recuperato il punto di ripristino pertinente (separato o temporizzato del log), usare il cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) di PowerShell per recuperare l'oggetto di configurazione di ripristino in base al piano di ripristino desiderato.

#### <a name="original-workload-restore"></a>Ripristino originale del carico di lavoro

Per eseguire l'override del database di cui è stato eseguito il backup con i dati del punto di ripristino, è sufficiente specificare il flag destro e il punto di ripristino pertinente, come illustrato negli esempi seguenti.

##### <a name="original-restore-with-distinct-recovery-point"></a>Ripristino originale con punto di ripristino distinto

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Ripristino originale con temporizzazione log

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Ripristino del carico di lavoro alternativo

> [!IMPORTANT]
> Un database SQL di cui è stato eseguito il backup può essere ripristinato come nuovo database in un'altra istanza di SQLInstance, in una macchina virtuale di Azure ' registered ' in questo insieme di credenziali.

Come illustrato in precedenza, se l'istanza di SQLInstance di destinazione si trova all'interno di un'altra macchina virtuale di Azure, assicurarsi che sia [registrata in questo](#registering-the-sql-vm) insieme di credenziali e che la relativa istanza di SQLInstance venga visualizzata come elemento da proteggere.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Quindi, è sufficiente passare il punto di ripristino pertinente, l'istanza di SQL di destinazione con il flag right, come illustrato di seguito.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Ripristino alternativo con punto di ripristino distinto

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Ripristino alternativo con log temporizzato

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Ripristina come file

Per ripristinare i dati di backup come file con estensione bak anziché come database, scegliere l'opzione **Ripristina come file** . Il database SQL di cui è stato eseguito il backup può essere ripristinato in qualsiasi macchina virtuale di destinazione registrata in questo insieme di credenziali.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Ripristina come file con un punto di ripristino distinto

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Ripristina come file con log temporizzato dall'ultima versione completa

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Ripristina come file con log temporizzato da un completo specificato

Se si vuole assegnare uno specifico completo da usare per il ripristino, usare il comando seguente:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

L'oggetto di configurazione del punto di ripristino finale ottenuto dal cmdlet di PowerShell [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) contiene tutte le informazioni rilevanti per il ripristino ed è illustrato di seguito.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

È possibile modificare i campi nome database ripristinato, OverwriteWLIfpresent, NoRecoveryMode e targetPhysicalPath. Per ulteriori informazioni sui percorsi dei file di destinazione, vedere di seguito.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Impostare le proprietà di PowerShell rilevanti come valori stringa come illustrato di seguito.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Verificare che l'oggetto di configurazione del ripristino finale includa tutti i valori necessari e appropriati, in quanto l'operazione di ripristino sarà basata sull'oggetto config.

### <a name="restore-with-relevant-configuration"></a>Ripristino con la configurazione pertinente

Una volta ottenuto e verificato l'oggetto di configurazione di ripristino pertinente, usare il cmdlet di PowerShell [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) per avviare il processo di ripristino.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

L'operazione di ripristino restituisce un processo da rilevare.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Gestire i backup SQL

### <a name="on-demand-backup"></a>Backup su richiesta

Una volta abilitato il backup per un database, è anche possibile attivare un backup su richiesta per il database usando il cmdlet [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) di PowerShell. Nell'esempio seguente viene attivato un backup completo in un database SQL con la compressione abilitata e il backup completo deve essere mantenuto per 60 giorni.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Il comando di backup su richiesta restituisce un processo da rilevare.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Se l'output viene perso o se si vuole ottenere l'ID del processo pertinente, [ottenere l'elenco dei processi](#track-azure-backup-jobs) dal servizio backup di Azure e quindi tenerne traccia e i relativi dettagli.

### <a name="change-policy-for-backup-items"></a>Modificare i criteri per gli elementi di backup

È possibile modificare i criteri dell'elemento di cui è stato eseguito il backup da *Policy1* a *Policy2*. Per modificare i criteri per un elemento di cui è stato eseguito il backup, recuperare i criteri pertinenti ed eseguire il backup dell'elemento e usare il comando [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) con l'elemento di backup come parametro.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

Il comando attende fino al completamento del backup di configurazione e restituisce il seguente output.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="edit-an-existing-backup-policy"></a>Modificare un criterio di backup esistente

Per modificare un criterio esistente, usare il comando [set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) .

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```

Controllare i processi di backup dopo che è trascorso del tempo per tenere traccia degli eventuali errori. In tal caso, è necessario risolvere i problemi. Eseguire quindi nuovamente il comando Edit Policy con il parametro **FixForInconsistentItems** per riprovare a modificare i criteri in tutti gli elementi di backup per i quali l'operazione non è riuscita in precedenza.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>Registrare di nuovo le VM SQL

> [!WARNING]
> Prima di provare a ripetere la registrazione, assicurarsi di leggere questo [documento](backup-sql-server-azure-troubleshoot.md#re-registration-failures) per comprendere i sintomi e le cause dell'errore.

Per attivare la ripetizione della registrazione della macchina virtuale SQL, recuperare il contenitore di backup pertinente e passarlo al cmdlet Register.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Arresta protezione

#### <a name="retain-data"></a>Mantenere i dati

Se si desidera arrestare la protezione dati, è possibile utilizzare il cmdlet di PowerShell [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) . Questa operazione arresterà i backup pianificati, ma i dati di cui è stato eseguito il backup fino a questo momento verranno conservati per sempre.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Elimina dati di backup

Per rimuovere completamente i dati di backup archiviati nell'insieme di credenziali, è sufficiente aggiungere il flag '-RemoveRecoveryPoints '/passare al [comando di protezione ' Disable '](#retain-data).

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Disabilitare la protezione automatica

Se la protezione con autoprotection è stata configurata su un'istanza di SQLInstance, è possibile disabilitarla utilizzando il cmdlet [Disable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) di PowerShell.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Annulla registrazione macchina virtuale SQL

Se tutti i database di SQL Server [non sono più protetti e non esistono dati di backup](#delete-backup-data), è possibile annullare la registrazione della macchina virtuale SQL da questo insieme di credenziali. Solo in questo modo è possibile proteggere i database in un altro insieme di credenziali. Usare il cmdlet [Unregister-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) di PowerShell per annullare la registrazione della macchina virtuale SQL.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Tenere traccia dei processi di backup di Azure

È importante notare che backup di Azure tiene traccia solo dei processi attivati dall'utente nel backup SQL. I backup pianificati, inclusi i backup del log, non sono visibili nel portale o in PowerShell. Tuttavia, in caso di esito negativo di un processo pianificato, viene generato un [avviso di backup](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) che viene visualizzato nel portale. [Usare monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md) per tenere traccia di tutti i processi pianificati e altre informazioni rilevanti.

Gli utenti possono tenere traccia delle operazioni attivate su richiesta/utente con il JobID restituito nell' [output](#on-demand-backup) dei processi asincroni, ad esempio il backup. Usare il cmdlet [Get-AzRecoveryServicesBackupJobDetail](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjobdetail) di PowerShell per tenere traccia del processo e dei relativi dettagli.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Per ottenere l'elenco dei processi su richiesta e i relativi stati dal servizio backup di Azure, usare il cmdlet [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) di PowerShell. Nell'esempio seguente vengono restituiti tutti i processi SQL in corso.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Per annullare un processo in corso, usare il cmdlet di PowerShell [Stop-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) .

## <a name="managing-sql-always-on-availability-groups"></a>Gestione dei gruppi di disponibilità di SQL Always On

Per i gruppi di disponibilità SQL Always On, assicurarsi di [registrare tutti i nodi](#registering-the-sql-vm) del gruppo di disponibilità (AG). Una volta eseguita la registrazione per tutti i nodi, un oggetto gruppo di disponibilità SQL viene creato logicamente in elementi da proteggere. I database di SQL AG verranno elencati come ' SqlDatabase '. I nodi verranno visualizzati come istanze autonome e i database SQL predefiniti in essi contenuti verranno elencati anche come database SQL.

Si supponga, ad esempio, che un gruppo di disponibilità di SQL Server disponga di due nodi: *SQL-Server-0* e *SQL-Server-1* e 1 SQL AG DB. Una volta registrati entrambi questi nodi, se si [elencano gli elementi da proteggere](#fetching-sql-dbs), vengono elencati i componenti seguenti

* Tipo di elemento da proteggere con oggetti di SQL AG come SQLAvailabilityGroup
* Tipo di elemento da proteggere con database di SQL AG come SQLDatabase
* tipo di elemento da proteggere con SQL-Server-0 come SQLInstance
* tipo di elemento da proteggere con SQL-Server-1 come SQLInstance
* Qualsiasi database SQL predefinito (Master, Model, msdb) nel tipo di elemento da proteggere con SQL-Server-0 come SQLDatabase
* Qualsiasi database SQL predefinito (Master, Model, msdb) nel tipo di elemento da proteggere con SQL-Server-1 come SQLDatabase

SQL-Server-0, SQL-Server-1 verrà elencato anche come "AzureVMAppContainer" quando [vengono elencati i contenitori di backup](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).

È sufficiente recuperare il database pertinente per [abilitare il backup](#configuring-backup) e i cmdlet di [PowerShell](#restore-sql-dbs) per il backup e il ripristino [su richiesta](#on-demand-backup) sono identici.
