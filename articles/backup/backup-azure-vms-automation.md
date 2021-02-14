---
title: Eseguire il backup e il ripristino di VM di Azure con PowerShell
description: Viene descritto come eseguire il backup e il ripristino di macchine virtuali di Azure tramite backup di Azure con PowerShell
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 66b8fe0109a4dd2e054106b67f893def2ee596b0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095087"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Eseguire il backup e il ripristino di VM di Azure con PowerShell

Questo articolo illustra come eseguire il backup e il ripristino di una macchina virtuale di Azure in un insieme di credenziali di servizi di ripristino di [backup di Azure](backup-overview.md) usando i cmdlet di PowerShell.

In questo articolo viene spiegato come:

> [!div class="checklist"]
>
> * Creare un insieme di credenziali di Servizi di ripristino e impostare il contesto dell'insieme di credenziali.
> * Definire un criterio di backup
> * Applicare un criterio di backup per proteggere più macchine virtuali
> * Attivare un processo di backup su richiesta per le macchine virtuali protette Prima di poter eseguire il backup o proteggere una macchina virtuale, è necessario completare i [prerequisiti](backup-azure-arm-vms-prepare.md) per preparare l'ambiente per la protezione delle macchine virtuali.

## <a name="before-you-start"></a>Prima di iniziare

* [Altre](backup-azure-recovery-services-vault-overview.md) informazioni sugli insiemi di credenziali dei servizi di ripristino.
* [Esaminare](backup-architecture.md#architecture-built-in-azure-vm-backup) l'architettura per il backup delle macchine virtuali di Azure, [ottenere informazioni sul](backup-azure-vms-introduction.md) processo di backup ed [esaminare](backup-support-matrix-iaas.md) il supporto, le limitazioni e i prerequisiti.
* Esaminare la gerarchia di oggetti di PowerShell per i servizi di ripristino.

## <a name="recovery-services-object-hierarchy"></a>Gerarchia di oggetti dei servizi di ripristino

La gerarchia di oggetti viene riepilogata nel diagramma seguente.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Esaminare il riferimento al [cmdlet](/powershell/module/az.recoveryservices/) **AZ. RecoveryServices** nella libreria di Azure.

## <a name="set-up-and-register"></a>Configurare e registrare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per iniziare:

1. [Scaricare la versione più recente di PowerShell](/powershell/azure/install-az-ps)

2. Cercare i cmdlet PowerShell di Azure Backup disponibili digitando il comando seguente:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Verranno visualizzati alias e cmdlet per Backup di Azure, Azure Site Recovery e l'insieme di credenziali di Servizi di ripristino. L'immagine seguente è un esempio di quanto verrà visualizzato. Non si tratta dell'elenco completo dei cmdlet.

    ![Elenco di Servizi di ripristino](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Accedere all'account Azure tramite **Connect-AzAccount**. Questo cmdlet visualizza una pagina Web che richiede le credenziali dell'account:

    * In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet **Connect-AzAccount**, usando il parametro **-Credential**.
    * Se si è un partner CSP che lavora per conto di un tenant, specificare il cliente come tenant, usando il nome di dominio primario tenantID o tenant. Ad esempio: **Connect-AzAccount-tenant "fabrikam.com"**

4. Associare la sottoscrizione che si vuole usare all'account perché un account può avere molte sottoscrizioni:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Se si usa backup di Azure per la prima volta, è necessario usare il cmdlet **[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** per registrare il provider di servizi di ripristino di Azure con la sottoscrizione.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. È possibile verificare che i provider siano stati registrati correttamente usando i comandi seguenti:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    Nell'output del comando **RegistrationState** dovrebbe essere modificato in **Registered**. In caso contrario, è sufficiente eseguire di nuovo il cmdlet **[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** .

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Nei passaggi seguenti viene descritto come creare un insieme di credenziali dei servizi di ripristino. Un insieme di credenziali dei servizi di ripristino è diverso da un insieme di credenziali di backup.

1. L'insieme di credenziali dei Servizi di ripristino è una risorsa Resource Manager, pertanto è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o creare un gruppo di risorse con il cmdlet **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** . Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e il percorso per il gruppo di risorse.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Usare il cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) per creare un nuovo insieme di credenziali di Servizi di ripristino. Assicurarsi di specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Specificare il tipo di ridondanza di archiviazione da usare. È possibile usare l'archiviazione con ridondanza [locale (con ridondanza locale)](../storage/common/storage-redundancy.md#locally-redundant-storage), l' [archiviazione con ridondanza geografica (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)o l' [archiviazione con ridondanza della zona (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). Nell'esempio seguente l'opzione **-BackupStorageRedundancy** per *testvault* è impostata su **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Molti cmdlet di Backup di Azure richiedono l'oggetto dell'insieme di credenziali dei servizi di ripristino come input. Per questo motivo, è utile archiviare l'oggetto dell'insieme di credenziali dei servizi di ripristino di backup in una variabile.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Per visualizzare tutti gli insiemi di credenziali nella sottoscrizione, usare [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

L'output è simile all'esempio seguente. Si noti che vengono inclusi gli elementi ResourceGroupName e Location associati.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Eseguire il backup delle VM di Azure

Usare un insieme di credenziali di Servizi di ripristino per proteggere le macchine virtuali. Prima di applicare la protezione, impostare il contesto dell'insieme di credenziali (tipo di dati protetti nell'insieme di credenziali) e verificare i criteri di protezione. I criteri di protezione rappresentano la pianificazione per l'esecuzione dei processi di backup e il periodo di conservazione di ogni snapshot del backup.

### <a name="set-vault-context"></a>Impostazione del contesto dell'insieme di credenziali

Prima di abilitare la protezione in una macchina virtuale, usare [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) per impostare il contesto dell'insieme di credenziali. Una volta impostato il contesto dell'insieme di credenziali, si applica a tutti i cmdlet successivi. L'esempio seguente imposta il contesto per l'insieme di credenziali, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Recuperare l'ID dell'insieme di credenziali

Si prevede di deprecare l'impostazione del contesto dell'insieme di credenziali in base alle linee guida Azure PowerShell. È invece possibile archiviare o recuperare l'ID dell'insieme di credenziali e passarlo ai comandi pertinenti. Quindi, se il contesto dell'insieme di credenziali non è stato impostato o si vuole specificare il comando da eseguire per un determinato insieme di credenziali, passare l'ID dell'insieme di credenziali come "-vaultID" a tutti i comandi pertinenti, come indicato di seguito:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Oppure

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Modifica delle impostazioni di replica di archiviazione

Usare il comando [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) per impostare la configurazione della replica di archiviazione dell'insieme di credenziali su con ridondanza locale/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> La ridondanza dell'archiviazione può essere modificata solo se non ci sono elementi di backup protetti nell'insieme di credenziali.

### <a name="create-a-protection-policy"></a>Creare i criteri di protezione

Quando si crea un insieme di credenziali di Servizi di ripristino, questo è dotato di protezione predefinita e criteri di conservazione. I criteri di protezione predefinita attivano un processo di backup ogni giorno all'ora specificata. I criteri di conservazione predefiniti consentono di mantenere il punto di recupero giornaliero per 30 giorni. È possibile usare i criteri predefiniti per proteggere rapidamente la macchina virtuale e modificare i criteri in un secondo momento con dettagli diversi.

Usare **[Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** per visualizzare i criteri di protezione disponibili nell'insieme di credenziali. È possibile usare questo cmdlet per ottenere un criterio specifico o per visualizzare i criteri associati a un tipo di carico di lavoro. L'esempio seguente ottiene i criteri per il tipo di carico di lavoro AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

L'output è simile all'esempio seguente:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Il fuso orario del campo BackupTime in PowerShell è UTC. Tuttavia, l'orario di backup nel portale di Azure è allineato al fuso orario locale.
>
>

I criteri di protezione del backup sono associati almeno a un criterio di conservazione. Un criterio di conservazione definisce per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione.

* Per visualizzare il criterio di conservazione predefinito usare [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Analogamente, è possibile usare [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) per ottenere i criteri di pianificazione predefiniti.
* Il cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) crea un oggetto di PowerShell che contiene le informazioni relative ai criteri di backup.
* Gli oggetti dei criteri di conservazione e di pianificazione vengono usati come input per il cmdlet New-AzRecoveryServicesBackupProtectionPolicy.

Per impostazione predefinita, nell'oggetto Criteri di pianificazione è definita un'ora di inizio. Usare l'esempio seguente per modificare l'ora di inizio con l'ora di inizio desiderata. L'ora di inizio desiderata dovrebbe essere anche UTC. Nell'esempio seguente si presuppone che l'ora di inizio desiderata sia 01:00 UTC per i backup giornalieri.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> È necessario specificare l'ora di inizio solo in più di 30 minuti. Nell'esempio precedente può essere solo "01:00:00" o "02:30:00". L'ora di inizio non può essere "01:15:00"

Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. L'esempio usa tali variabili per definire i parametri durante la creazione di un criterio di protezione, *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

L'output è simile all'esempio seguente:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Abilitare la protezione

Dopo aver definito i criteri di protezione è necessario abilitarli per un elemento. Usare [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) per abilitare la protezione. Per abilitare la protezione sono necessari due oggetti, l'elemento e i criteri. Dopo aver associato i criteri all'insieme di credenziali, il flusso di lavoro di backup verrà attivato al momento definito nella pianificazione dei criteri.

> [!IMPORTANT]
> Quando si usa PowerShell per abilitare il backup per più macchine virtuali contemporaneamente, assicurarsi che a un singolo criterio non siano associate più di 100 macchine virtuali. Si tratta di una [procedura consigliata](./backup-azure-vm-backup-faq.yml#is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy). Attualmente, il client di PowerShell non si blocca in modo esplicito se sono presenti più di 100 macchine virtuali, ma il controllo dovrebbe essere aggiunto in futuro.

Gli esempi seguenti abilitano la protezione per l'elemento V2VM usando i criteri NewPolicy. Gli esempi variano a seconda del fatto che la macchina virtuale sia crittografata o meno e in base al tipo di crittografia.

Per abilitare la protezione in **macchine virtuali di Resource Manager non crittografate**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Per abilitare la protezione in macchine virtuali crittografate con BEK e KEK è necessario concedere al servizio Backup di Azure le autorizzazioni necessarie per la lettura di chiavi e segreti dall'insieme di credenziali delle chiavi.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Per abilitare la protezione in **macchine virtuali crittografate solo con BEK** è necessario concedere al servizio Backup di Azure le autorizzazioni necessarie per la lettura dei segreti dall'insieme di credenziali delle chiavi.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Se si usa il cloud di Azure per enti pubblici, usare il valore `ff281ffe-705c-4f53-9f37-a40e6f2c68f3` per il parametro **servicePrincipalName** nel cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .
>

Se si desidera eseguire il backup in modo selettivo di pochi dischi ed escluderne altri come indicato in [questi scenari](selective-disk-backup-restore.md#scenarios), è possibile configurare la protezione e il backup solo dei dischi pertinenti, come descritto [qui](selective-disk-backup-restore.md#enable-backup-with-powershell).

## <a name="monitoring-a-backup-job"></a>Monitoraggio di un processo di backup

È possibile monitorare le operazioni a esecuzione prolungata, ad esempio i processi di backup, senza usare il portale di Azure. Per ottenere lo stato di un processo in corso, usare il cmdlet [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) . Questo cmdlet ottiene i processi di backup per un insieme di credenziali specifico e tale insieme di credenziali è indicato nel relativo contesto. L'esempio seguente ottiene lo stato di un processo in corso sotto forma di matrice e archivia lo stato nella variabile $joblist.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

L'output è simile all'esempio seguente:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Anziché eseguire il polling di questi processi per il completamento, che non è un codice aggiuntivo superfluo, usare il cmdlet [wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) . Questo cmdlet sospende l'esecuzione fino al completamento del processo o fino a quando non viene raggiunto il valore di timeout specificato.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Gestire i backup delle macchine virtuali di Azure

### <a name="modify-a-protection-policy"></a>Modificare i criteri di protezione

Per modificare i criteri di protezione, usare [set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) per modificare gli oggetti SchedulePolicy o RetentionPolicy.

#### <a name="modifying-scheduled-time"></a>Modifica dell'ora pianificata

Quando si crea un criterio di protezione, per impostazione predefinita viene assegnata un'ora di inizio. Negli esempi seguenti viene illustrato come modificare l'ora di inizio di un criterio di protezione dati.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that you want to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Modifica della conservazione

Nell'esempio seguente viene modificato il punto di recupero a 365 giorni.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Configurazione della conservazione snapshot per il ripristino immediato

> [!NOTE]
> Da Azure PowerShell versione 1.6.0 in poi, è possibile aggiornare il periodo di conservazione degli snapshot di ripristino istantaneo nei criteri usando PowerShell

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

Il valore predefinito sarà 2. È possibile impostare il valore con un minimo di 1 e un massimo di 5. Per i criteri di backup settimanali, il periodo è impostato su 5 e non può essere modificato.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>Creazione del gruppo di risorse di backup di Azure durante la conservazione degli snapshot

> [!NOTE]
> A partire da Azure PowerShell versione 3.7.0, è possibile creare e modificare il gruppo di risorse creato per l'archiviazione degli snapshot istantanei.

Per ulteriori informazioni sulle regole di creazione del gruppo di risorse e altre informazioni rilevanti, vedere la documentazione relativa al [gruppo di risorse di backup di Azure per le macchine virtuali](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines) .

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="exclude-disks-for-a-protected-vm"></a>Escludere dischi per una macchina virtuale protetta

Il backup delle macchine virtuali di Azure consente di escludere o includere in modo selettivo dischi utili in [questi scenari](selective-disk-backup-restore.md#scenarios). Se la macchina virtuale è già protetta dal backup delle VM di Azure e se viene eseguito il backup di tutti i dischi, è possibile modificare la protezione per includere o escludere in modo selettivo i dischi come indicato [qui](selective-disk-backup-restore.md#modify-protection-for-already-backed-up-vms-with-powershell).

### <a name="trigger-a-backup"></a>Attivare un backup

Usare [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) per attivare un processo di backup. Se si tratta del backup iniziale, è un backup completo. I backup successivi saranno incrementali. Nell'esempio seguente viene mantenuto un backup della macchina virtuale per 60 giorni.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

L'output è simile all'esempio seguente:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Il fuso orario dei campi StartTime ed EndTime in PowerShell è UTC. Tuttavia, l'orario visualizzato nel portale di Azure è allineato al fuso orario locale.
>
>

### <a name="change-policy-for-backup-items"></a>Modificare i criteri per gli elementi di backup

È possibile modificare i criteri esistenti o modificare i criteri dell'elemento di cui è stato eseguito il backup da Policy1 a Policy2. Per modificare i criteri per un elemento di cui è stato eseguito il backup, recuperare i criteri pertinenti ed eseguire il backup dell'elemento e usare il comando [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) con l'elemento di backup come parametro.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

Il comando attende fino al completamento del backup di configurazione e restituisce il seguente output.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Arresta protezione

#### <a name="retain-data"></a>Mantenere i dati

Se si desidera arrestare la protezione dati, è possibile utilizzare il cmdlet di PowerShell [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) . Questa operazione arresterà i backup pianificati, ma i dati di cui è stato eseguito il backup fino a questo momento verranno conservati per sempre.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Elimina dati di backup

Per rimuovere completamente i dati di backup archiviati nell'insieme di credenziali, aggiungere il flag '-RemoveRecoveryPoints '/passare al [comando di protezione ' Disable '](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Ripristinare una macchina virtuale di Azure

Esiste una differenza importante tra il ripristino di una macchina virtuale usando il portale di Azure e il ripristino di una macchina virtuale con PowerShell. Con PowerShell l'operazione di ripristino è completata quando sono stati creati i dischi e le informazioni sulla configurazione dal punto di ripristino L'operazione di ripristino non crea la macchina virtuale. Per creare una macchina virtuale dal disco, vedere la sezione [Creare la macchina virtuale da dischi ripristinati](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Se non si vuole ripristinare l'intera macchina virtuale, ma si vuole ripristinare o recuperare alcuni file da un backup della macchina virtuale di Azure, vedere la [sezione relativa al recupero di file](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> L'operazione di ripristino non crea la macchina virtuale.
>
>

Il grafico seguente mostra la gerarchia degli oggetti da RecoveryServicesVault fino a BackupRecoveryPoint.

![Gerarchia di oggetti dei servizi di ripristino con BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Per ripristinare i dati di backup, identificare l'elemento sottoposto a backup e il punto di ripristino che contiene i dati temporizzati. Usare [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) per ripristinare i dati dall'insieme di credenziali al proprio account.

I passaggi di base per ripristinare una macchina virtuale di Azure sono:

* Selezionare la macchina virtuale.
* Scegliere un punto di ripristino.
* Ripristinare i dischi.
* Creare la macchina virtuale dai dischi archiviati.

### <a name="select-the-vm-when-restoring-files"></a>Selezionare la macchina virtuale (durante il ripristino dei file)

Per ottenere l'oggetto di PowerShell che identifica l'elemento di backup corretto, iniziare dal contenitore nell'insieme di credenziali e procedere verso il basso nella gerarchia degli oggetti. Per selezionare il contenitore che rappresenta la macchina virtuale, usare il cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) e inviarlo tramite pipe al cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-files"></a>Scegliere un punto di ripristino (durante il ripristino dei file)

Usare il cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) per elencare tutti i punti di recupero dell'elemento di backup. Quindi scegliere il punto di ripristino per ripristinare. Se non si è certi del punto di ripristino da usare, è consigliabile scegliere il punto più recente RecoveryPointType = AppConsistent nell'elenco.

Nello script seguente la variabile, **$RP**, è una matrice di punti di ripristino per l'elemento di backup selezionato negli ultimi sette giorni. La matrice viene ordinata in ordine inverso di tempo con il punto di ripristino più recente in posizione 0 nell'indice. Per scegliere il punto di ripristino, usare l'indicizzazione standard della matrice di PowerShell. Nell'esempio $rp [0] seleziona il punto di ripristino più recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

L'output è simile all'esempio seguente:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Ripristinare i dischi

Usare il cmdlet [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) per ripristinare i dati e la configurazione di un elemento di backup in un punto di ripristino. Dopo aver identificato un punto di ripristino, usarlo come valore per il parametro **-RecoveryPoint**. Nell'esempio precedente **$RP [0]** è il punto di ripristino da utilizzare. Nell'esempio di codice seguente **$rp[0]** è il punto di ripristino da usare per il ripristino del disco.

Per ripristinare i dischi e le informazioni di configurazione:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Ripristinare i dischi gestiti

> [!NOTE]
> Se la macchina virtuale supportata ha dischi gestiti e si vuole ripristinarli come Managed disks, è stata introdotta la funzionalità di Azure PowerShell RM Module v 6.7.0. a partire.
>
>

Indicare un parametro aggiuntivo **TargetResourceGroupName** per specificare il gruppo di risorse in cui verranno ripristinati i dischi gestiti.

> [!IMPORTANT]
> Si consiglia vivamente di usare il parametro **TargetResourceGroupName** per il ripristino dei dischi gestiti, in quanto comporta miglioramenti significativi delle prestazioni. Se questo parametro non è specificato, non è possibile trarre vantaggio dalla funzionalità di ripristino immediato e l'operazione di ripristino sarà più lenta rispetto a. Se lo scopo è ripristinare i dischi gestiti come dischi non gestiti, non specificare questo parametro e rendere chiara l'intenzione fornendo il `-RestoreAsUnmanagedDisks` parametro. Il `-RestoreAsUnmanagedDisks` parametro è disponibile dal Azure PowerShell 3.7.0 in poi. Nelle versioni future, sarà obbligatorio fornire uno di questi parametri per la corretta esperienza di ripristino.
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

Il file **VMConfig.JSON** verrà ripristinato nell'account di archiviazione e i dischi gestiti verranno ripristinati nel gruppo di risorse di destinazione specificato.

L'output è simile all'esempio seguente:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Usare il cmdlet [wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) per attendere il completamento del processo di ripristino.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Al termine del processo di ripristino, usare il cmdlet [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) per ottenere i dettagli dell'operazione di ripristino. La proprietà JobDetails contiene le informazioni necessarie per ricreare la macchina virtuale.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

#### <a name="restore-selective-disks"></a>Ripristinare i dischi selettivi

Un utente può ripristinare in modo selettivo pochi dischi anziché l'intero set di backup. Specificare i LUN del disco necessari come parametro per ripristinarli solo anziché l'intero set, come descritto [qui](selective-disk-backup-restore.md#restore-selective-disks-with-powershell).

> [!IMPORTANT]
> È necessario eseguire il backup selettivo dei dischi per ripristinare i dischi in modo selettivo. Altre informazioni sono disponibili [qui](selective-disk-backup-restore.md#selective-disk-restore).

Dopo aver ripristinato i dischi, passare alla sezione successiva per creare la macchina virtuale.

#### <a name="restore-disks-to-a-secondary-region"></a>Ripristinare i dischi in un'area secondaria

Se il ripristino tra aree è abilitato nell'insieme di credenziali con cui sono state protette le VM, i dati di backup vengono replicati nell'area secondaria. È possibile utilizzare i dati di backup per eseguire un ripristino. Per attivare un ripristino nell'area secondaria, seguire questa procedura:

1. [Recuperare l'ID dell'](#fetch-the-vault-id) insieme di credenziali con cui sono protette le VM.
1. Selezionare l' [elemento di backup corretto da ripristinare](#select-the-vm-when-restoring-files).
1. Selezionare il punto di ripristino appropriato nell'area secondaria che si vuole usare per eseguire il ripristino.

    Per completare questo passaggio, eseguire questo comando:

    ```powershell
    $rp=Get-AzRecoveryServicesBackupRecoveryPoint -UseSecondaryRegion -Item $backupitem -VaultId $targetVault.ID
    $rp=$rp[0]
    ```

1. Eseguire il cmdlet [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) con il `-RestoreToSecondaryRegion` parametro per attivare un ripristino nell'area secondaria.

    Per completare questo passaggio, eseguire questo comando:

    ```powershell
    $restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -VaultLocation $targetVault.Location -RestoreToSecondaryRegion -RestoreOnlyOSDisk
    ```

    L'output sarà simile all'esempio seguente:

    ```output
    WorkloadName     Operation             Status              StartTime                 EndTime          JobID
    ------------     ---------             ------              ---------                 -------          ----------
    V2VM             CrossRegionRestore   InProgress           4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
    ```

1. Eseguire il cmdlet [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) con il `-UseSecondaryRegion` parametro per monitorare il processo di ripristino.

    Per completare questo passaggio, eseguire questo comando:

    ```powershell
    Get-AzRecoveryServicesBackupJob -From (Get-Date).AddDays(-7).ToUniversalTime() -To (Get-Date).ToUniversalTime() -UseSecondaryRegion -VaultId $targetVault.ID
    ```

    L'output sarà simile all'esempio seguente:

    ```output
    WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
    ------------     ---------            ------               ---------                 -------                   -----
    V2VM             CrossRegionRestore   InProgress           2/8/2021 4:24:57 PM                                 2d071b07-8f7c-4368-bc39-98c7fb2983f7
    ```

## <a name="replace-disks-in-azure-vm"></a>Sostituire i dischi nella macchina virtuale di Azure

Per sostituire i dischi e le informazioni di configurazione, seguire questa procedura:

* Passaggio 1: [ripristinare i dischi](backup-azure-vms-automation.md#restore-the-disks)
* Passaggio 2: [scollegare il disco dati con PowerShell](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-powershell)
* Passaggio 3: [associare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md)

## <a name="create-a-vm-from-restored-disks"></a>Creare una macchina virtuale da dischi ripristinati

Dopo aver ripristinato i dischi, usare la procedura seguente per creare e configurare la macchina virtuale dal disco.

> [!NOTE]
>
> 1. Il modulo AzureAz 3.0.0 o versione successiva è obbligatorio. <br>
> 2. Per creare macchine virtuali crittografate da dischi ripristinati, il ruolo di Azure deve disporre dell'autorizzazione per eseguire l'azione, ovvero **Microsoft.KeyVault/vaults/deploy/action**. Se il ruolo non dispone di questa autorizzazione, creare un ruolo personalizzato con questa azione. Per altre informazioni, vedere [ruoli personalizzati di Azure](../role-based-access-control/custom-roles.md). <br>
> 3. Dopo aver ripristinato i dischi, è ora possibile ottenere un modello di distribuzione che può essere usato direttamente per creare una nuova macchina virtuale. Non sono necessari cmdlet di PowerShell diversi per creare macchine virtuali gestite/non gestite crittografate o non crittografate.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Creare una macchina virtuale usando il modello di distribuzione

I dettagli del processo risultante includono l'URI del modello che può essere sottoposto a query e distribuito.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

Il modello non è direttamente accessibile perché si trova nell'account di archiviazione del cliente e nel contenitore specificato. Per accedere a questo modello, è necessario l'URL completo (insieme a un token di firma di accesso condiviso temporaneo).

1. Estrarre innanzitutto il nome del modello dal templateBlobURI. Il formato è indicato di seguito. È possibile usare l'operazione Split in PowerShell per estrarre il nome del modello finale da questo URL.

    ```http
    https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
    ```

2. Quindi, è possibile generare l'URL completo come illustrato [qui](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-powershell#provide-sas-token-during-deployment).

    ```powershell
    Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
    $templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
    ```

3. Distribuire il modello per creare una nuova macchina virtuale, come illustrato [qui](../azure-resource-manager/templates/deploy-powershell.md).

    ```powershell
    New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
    ```

### <a name="create-a-vm-using-the-config-file"></a>Creare una macchina virtuale usando il file di configurazione

La sezione seguente elenca i passaggi necessari per creare una macchina virtuale usando il file "VMConfig".

> [!NOTE]
> È consigliabile usare il modello di distribuzione descritto in precedenza per creare una macchina virtuale. Questa sezione (punti 1-6) sarà presto deprecata.

1. Ricercare i dettagli del processo nelle proprietà del disco ripristinato.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Impostare il contesto di archiviazione di Azure e ripristinare il file di configurazione JSON.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Usare il file di configurazione JSON per creare la configurazione della macchina virtuale.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Collegare il disco del sistema operativo e i dischi dei dati. Questo passaggio include esempi di varie configurazioni di macchine virtuali gestite e crittografate. Usare l'esempio adatto alla configurazione della propria macchina virtuale.

    * **Macchine virtuali non gestite e non crittografate**: usare l'esempio seguente per macchine virtuali non gestite e non crittografate.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
            $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Macchine virtuali non gestite e crittografate con Azure AD (solo BEK)**: per le macchine virtuali non gestite e crittografate con Azure AD (solo con BEK) è necessario ripristinare il segreto nell'insieme di credenziali delle chiavi prima di collegare i dischi. Per altre informazioni, vedere [Ripristinare una macchina virtuale crittografata da un punto di ripristino di Backup di Azure](backup-azure-restore-key-secret.md). L'esempio seguente illustra come collegare dischi del sistema operativo e i dati per le macchine virtuali crittografate. Durante l'impostazione del disco del sistema operativo, assicurarsi di indicare il tipo di sistema operativo pertinente.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Macchine virtuali non gestite e crittografate con Azure AD (BEK e KEK)**: per le macchine virtuali non gestite e crittografate con Azure AD (con BEK e KEK) ripristinare la chiave e il segreto nell'insieme di credenziali delle chiavi prima di collegare i dischi. Per altre informazioni, vedere [Ripristinare una macchina virtuale crittografata da un punto di ripristino di Backup di Azure](backup-azure-restore-key-secret.md). L'esempio seguente illustra come collegare dischi del sistema operativo e i dati per le macchine virtuali crittografate.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Macchine virtuali non gestite e crittografate senza Azure AD (solo BEK)**: per le macchine virtuali non gestite e crittografate senza Azure AD (solo con BEK), se **l'insieme di credenziali delle chiavi e il segreto di origine non sono disponibili**, ripristinare i segreti nell'insieme di credenziali delle chiavi seguendo la procedura in [Restore an non-encrypted virtual machine from an Azure Backup recovery point](backup-azure-restore-key-secret.md) (Ripristinare una macchina virtuale non crittografata da un punto di ripristino di Backup di Azure). Eseguire quindi gli script seguenti per impostare i dettagli della crittografia nel BLOB del sistema operativo ripristinato. questo passaggio non è obbligatorio per un BLOB di dati. Recuperare $dekurl dall'insieme di credenziali delle chiavi ripristinato.

    È necessario eseguire lo script seguente solo quando il segreto o l'insieme di credenziali delle chiave di origine non è disponibile.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Quando i **segreti sono disponibili** e le informazioni dettagliate sulla crittografia sono impostate nel BLOB del sistema operativo, collegare i dischi usando lo script riportato di seguito.

    Se l'insieme di credenziali delle chiavi di origine e i segreti sono già disponibili, non è necessario eseguire lo script precedente.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Macchine virtuali non gestite e crittografate senza Azure AD (BEK e KEK)**: per le macchine virtuali non gestite e crittografate senza Azure AD (con BEK e KEK), se **l'insieme di credenziali delle chiavi, la chiave e il segreto di origine non sono disponibili**, ripristinare la chiave e i segreti nell'insieme di credenziali delle chiavi seguendo la procedura in [Restore an non-encrypted virtual machine from an Azure Backup recovery point](backup-azure-restore-key-secret.md) (Ripristinare una macchina virtuale non crittografata da un punto di ripristino di Backup di Azure). Eseguire quindi gli script seguenti per impostare i dettagli della crittografia nel BLOB del sistema operativo ripristinato. questo passaggio non è obbligatorio per un BLOB di dati. Recuperare $dekurl e $kekurl dall'insieme di credenziali delle chiavi ripristinato.

    Lo script seguente deve essere eseguito solo se l'insieme di credenziali delle chiavi/chiave/segreto di origine non è disponibile.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Quando **la chiave e i segreti sono disponibili** e le informazioni dettagliate sulla crittografia sono impostate nel BLOB del sistema operativo, collegare i dischi usando lo script riportato di seguito.

    Se l'insieme di credenziali delle chiavi/chiave/segreti di origine è disponibile, non è necessario eseguire lo script precedente.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Macchine virtuali gestite e non crittografate**: per le macchine virtuali gestite e non crittografate, collegare i dischi gestiti ripristinati. Per informazioni dettagliate, vedere [Collegare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Macchine virtuali gestite e crittografate con Azure AD (solo BEK)**: per le macchine virtuali gestite e crittografate con Azure AD (solo con BEK) collegare i dischi gestiti ripristinati. Per informazioni dettagliate, vedere [Collegare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Macchine virtuali gestite e crittografate con Azure AD (BEK e KEK)**: per le macchine virtuali gestite e crittografate con Azure AD (con BEK e KEK) collegare i dischi gestiti ripristinati. Per informazioni dettagliate, vedere [Collegare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Macchine virtuali gestite e crittografate senza Azure ad (solo per le** macchine virtuali)-per le VM gestite e crittografate senza Azure ad (solo crittografate con l'uso di solo la chiave di sicurezza), se l'insieme di credenziali delle chiavi di origine e il **segreto non sono disponibili** , ripristinare i segreti nell'insieme di credenziali delle chiavi usando la procedura descritta in [ripristinare una macchina virtuale non](backup-azure-restore-key-secret.md) Eseguire quindi gli script seguenti per impostare i dettagli della crittografia nel disco del sistema operativo ripristinato (questo passaggio non è necessario per un disco dati). Recuperare $dekurl dall'insieme di credenziali delle chiavi ripristinato.

    Lo script riportato di seguito deve essere eseguito solo se l'insieme di credenziali delle credenziali di origine o il segreto non è disponibile.  

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Quando i segreti sono disponibili e le informazioni dettagliate sulla crittografia sono impostate nel disco del sistema operativo, vedere [Collegare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md) per collegare i dischi gestiti ripristinati.

    * **Macchine virtuali gestite e crittografate senza Azure ad (per le** macchine virtuali non crittografate), per le VM gestite e crittografate senza Azure ad (crittografate con l'uso di un & KEK), se l'insieme di credenziali delle chiavi di origine **/chiave/segreto non è disponibile** , ripristinare la chiave e i segreti dell'insieme di credenziali delle chiavi usando la procedura descritta in [ripristinare una macchina virtuale non crittografata](backup-azure-restore-key-secret.md) Eseguire quindi gli script seguenti per impostare i dettagli della crittografia nel disco del sistema operativo ripristinato (questo passaggio non è necessario per i dischi dati). Recuperare $dekurl e $kekurl dall'insieme di credenziali delle chiavi ripristinato.

    È necessario eseguire lo script seguente solo se l'insieme di credenziali delle chiavi/chiave/segreto di origine non è disponibile.

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Quando la chiave e i segreti sono disponibili e le informazioni dettagliate sulla crittografia sono impostate nel disco del sistema operativo, vedere [Collegare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md) per collegare i dischi gestiti ripristinati.

5. Configurare le impostazioni di rete.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Creare la macchina virtuale.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Eseguire il push dell'estensione ADE.
   Se le estensioni ADE non vengono inserite, i dischi dati verranno contrassegnati come non crittografati, quindi è obbligatorio eseguire i passaggi seguenti:

   * **Per una macchina virtuale con Azure AD**: usare il comando seguente per abilitare manualmente la crittografia per i dischi dati  

     **Solo BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK e KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Per una macchina virtuale senza Azure AD**: usare il comando seguente per abilitare manualmente la crittografia per i dischi dati.

     Se durante l'esecuzione del comando viene richiesto AADClientID, è necessario aggiornare il Azure PowerShell.

     **Solo BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK e KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Assicurarsi di eliminare manualmente i file JASON creati come parte del processo di ripristino del disco della VM crittografato.

## <a name="restore-files-from-an-azure-vm-backup"></a>Ripristinare file da un backup della macchina virtuale di Azure

Oltre al ripristino di dischi è anche possibile ripristinare singoli file da un backup della macchina virtuale di Azure. La funzionalità di ripristino dei file consente di accedere a tutti i file inclusi in un punto di ripristino. Gestire i file tramite Esplora file come si farebbe con i file normali.

La procedura di base per il ripristino di un file dal backup di una macchina virtuale di Azure si articola nei passaggi seguenti:

* Selezionare la macchina virtuale
* Scegliere un punto di ripristino
* Montare i dischi del punto di ripristino
* Copiare i file necessari
* Smontare il disco

### <a name="select-the-vm-when-restoring-the-vm"></a>Selezionare la macchina virtuale (durante il ripristino della macchina virtuale)

Per ottenere l'oggetto di PowerShell che identifica l'elemento di backup corretto, iniziare dal contenitore nell'insieme di credenziali e procedere verso il basso nella gerarchia degli oggetti. Per selezionare il contenitore che rappresenta la macchina virtuale, usare il cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) e inviarlo tramite pipe al cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-the-vm"></a>Scegliere un punto di ripristino (durante il ripristino della macchina virtuale)

Usare il cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) per elencare tutti i punti di recupero dell'elemento di backup. Quindi scegliere il punto di ripristino per ripristinare. Se non si è certi del punto di ripristino da usare, è consigliabile scegliere il punto più recente RecoveryPointType = AppConsistent nell'elenco.

Nello script seguente la variabile, **$RP**, è una matrice di punti di ripristino per l'elemento di backup selezionato negli ultimi sette giorni. La matrice viene ordinata in ordine inverso di tempo con il punto di ripristino più recente in posizione 0 nell'indice. Per scegliere il punto di ripristino, usare l'indicizzazione standard della matrice di PowerShell. Nell'esempio $rp [0] seleziona il punto di ripristino più recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

L'output è simile all'esempio seguente:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Montare i dischi del punto di ripristino

Usare il cmdlet [Get-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) per ottenere lo script per montare tutti i dischi del punto di ripristino.

> [!NOTE]
> I dischi sono montati come dischi collegati iSCSI al computer in cui viene eseguito lo script. Il montaggio avviene immediatamente e non si incorre in alcun addebito.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

L'output è simile all'esempio seguente:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Eseguire lo script nel computer in cui si vogliono recuperare i file. Per eseguire lo script è necessario immettere la password specificata. Dopo aver collegato i dischi, usare Esplora risorse per esplorare i nuovi volumi e file. Per altre informazioni, vedere l'articolo di backup [ripristinare i file dal backup della macchina virtuale di Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Smontare i disco

Dopo aver copiato i file necessari, usare [Disable-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) per smontare i dischi. Assicurarsi di smontare i dischi in modo che l'accesso ai file del punto di ripristino venga rimosso.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Passaggi successivi

Se si preferisce usare PowerShell per coinvolgere le risorse di Azure, vedere l'articolo PowerShell [Distribuire e gestire il backup in Azure per server Windows/client Windows mediante PowerShell](backup-client-automation.md). Se si gestiscono i backup DPM, vedere l'articolo [Distribuire e gestire il backup per DPM](backup-dpm-automation.md).
