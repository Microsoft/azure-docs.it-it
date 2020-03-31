---
title: Configurare il ripristino di emergenza di Hyper-V (con VMM) in un sito secondario con Azure Site Recovery/PowerShellSet up Hyper-V (with VMM) disaster recovery to a secondary site with Azure Site Recovery/PowerShell
description: Questo articolo descrive come configurare il ripristino di emergenza di macchine virtuali Hyper-V di cloud VMM in un sito VMM secondario usando Azure Site Recovery e PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: deef7bfdbc28d744cb81da59d3ffc13a1abee54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048607"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Configurare il ripristino di emergenza di macchine virtuali Hyper-V in un sito secondario con PowerShell (Resource Manager)

Questo articolo illustra come automatizzare la procedura per la replica di macchine virtuali Hyper-V in cloud System Center Virtual Machine Manager in un cloud Virtual Machine Manager in un sito locale secondario usando [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Esaminare [l'architettura e i componenti dello scenario](hyper-v-vmm-architecture.md).
- Esaminare i [requisiti di supporto](site-recovery-support-matrix-to-sec-site.md) per tutti i componenti.
- Assicurarsi che i server Virtual Machine Manager e gli host Hyper-V rispettino i [requisiti di supporto](site-recovery-support-matrix-to-sec-site.md).
- Assicurarsi che le macchine virtuali da replicare siano conformi al [supporto del computer replicato](site-recovery-support-matrix-to-sec-site.md).

## <a name="prepare-for-network-mapping"></a>Preparare il mapping di rete

[Mapping di rete](hyper-v-vmm-network-mapping.md) esegue il mapping tra le reti delle macchine virtuali di Virtual Machine Manager locali nei cloud di origine e destinazione. Il mapping esegue queste operazioni:

- Connette le macchine virtuali a reti di macchine virtuali di destinazione appropriate dopo il failover.
- Posiziona in modo ottimale le macchine virtuali di replica nei server host Hyper-V di destinazione.
- Se non si configura il mapping di rete, le macchine virtuali di replica non verranno connesse alla rete delle macchine virtuali dopo il failover.

Preparare Virtual Machine Manager come segue:

- Assicurarsi di avere [reti logiche di Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) nei server di Virtual Machine Manager di origine e di destinazione:
  - La rete logica nel server di origine deve essere associata al cloud di origine in cui si trovano gli host Hyper-V.
  - La rete logica nel server di destinazione deve essere associata al cloud di destinazione.
- Assicurarsi di avere [reti delle macchine virtuali](https://docs.microsoft.com/system-center/vmm/network-virtual) nei server di Virtual Machine Manager di origine e di destinazione. Le reti VM devono essere collegate alla rete logica in ogni posizione.
- Connettere le macchine virtuali negli host Hyper-V di origine alla rete VM di origine.

## <a name="prepare-for-powershell"></a>Eseguire le operazioni preliminari per PowerShell

Assicurarsi che Azure PowerShell sia pronto all'uso:

- Se si usa già PowerShell, eseguire l'aggiornamento alla versione 0.8.10 o versione successiva. [Altre informazioni](/powershell/azureps-cmdlets-docs) su come configurare PowerShell.
- Dopo aver impostato e configurato PowerShell, visualizzare i [cmdlet dei servizi](/powershell/azure/overview).
- Per altre informazioni su come usare i valori dei parametri, gli input e gli output in Azure PowerShell, leggere la guida [Introduzione](/powershell/azure/get-started-azureps).

## <a name="set-up-a-subscription"></a>Configurare una sottoscrizione

1. Da PowerShell accedere al proprio account di Azure.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Recuperare un elenco delle sottoscrizioni con i relativi ID. Prendere nota dell'ID della sottoscrizione in cui si vuole creare l'insieme di credenziali di Servizi di ripristino.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Configurare la sottoscrizione per l'insieme di credenziali.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

1. Creare un gruppo di risorse di Azure Resource Manager, se non ne è già disponibile uno.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Creare un nuovo insieme di credenziali di Servizi di ripristino. Salvare l'oggetto insieme di credenziali in una variabile da usare in seguito.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   È possibile recuperare l'oggetto vault dopo `Get-AzRecoveryServicesVault` averlo creato utilizzando il cmdlet.

## <a name="set-the-vault-context"></a>Impostare il contesto dell'insieme di credenziali

1. Recuperare un insieme di credenziali esistente.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Impostare il contesto dell'insieme di credenziali.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Installare il provider di Site Recovery

1. Nel computer Virtual Machine Manager creare una directory eseguendo il comando seguente:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Estrarre i file usando il file di installazione del provider scaricato.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Installare il provider e attendere il completamento dell'installazione.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. Registrare il server nell'insieme di credenziali.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Creare e associare criteri di replica

1. Creare i criteri di replica, in questo caso per Hyper-V 2012 R2, come indicato di seguito:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > Il cloud Virtual Machine Manager può contenere host Hyper-V che eseguono versioni diverse di Windows Server, ma i criteri di replica sono specifici di una determinata versione di un sistema operativo. Se sono disponibili vari host in esecuzione su sistemi operativi diversi, creare criteri di replica separati per ogni sistema. Ad esempio, se sono disponibili cinque host in esecuzione su Windows Server 2012 e tre su Windows Server 2012 R2, creare due criteri di replica, uno per ogni tipo di sistema operativo.

1. Recuperare il contenitore di protezione principale (cloud Virtual Machine Manager primario) e il contenitore di protezione di ripristino (cloud Virtual Machine Manager di ripristino).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Recuperare i criteri di replica creati usando il nome descrittivo.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Avviare l'associazione del contenitore di protezione (cloud Virtual Machine Manager) al criterio di replica.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Attendere la fine del processo di associazione dei criteri. Per controllare se il processo è stato completato, usare il frammento di PowerShell seguente:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Al termine dell'elaborazione del processo, eseguire il comando seguente:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

Per verificare il completamento dell'operazione, attenersi alla procedura descritta in [Monitorare l'attività](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurare il mapping di rete

1. Usare questo comando per recuperare i server per l'insieme di credenziali corrente. Il comando memorizza i `$Servers` server di Site Recovery nella variabile array.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Eseguire questo comando per recuperare le reti per il server Virtual Machine Manager di origine e il server Virtual Machine Manager di destinazione.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > Il server Virtual Machine Manager di origine può essere il primo o secondo nella matrice di server. Controllare i nomi dei server Virtual Machine Manager e recuperare le reti in modo appropriato.

1. Il cmdlet crea un mapping tra la rete primaria e la rete di ripristino. Specifica la rete primaria come primo `$PrimaryNetworks`elemento di . Specifica la rete di ripristino come `$RecoveryNetworks`primo elemento di .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Abilitare la protezione per le macchine virtuali

Dopo aver configurato correttamente server, cloud e reti, abilitare la protezione per le macchine virtuali nel cloud.

1. Per abilitare la protezione, eseguire il comando seguente per recuperare il contenitore di protezione:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. Ottenere l'entità di protezione (macchina virtuale) come segue:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Abilitare la replica per la macchina virtuale.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Se si desidera eseguire la replica nei dischi gestiti abilitati per CMK in Azure, eseguire la procedura seguente usando Az PowerShell 3.3.0 in poi:If you wish to replicate to CMK enabled managed disks in Azure, do the following steps using Az PowerShell 3.3.0 onwards:
>
> 1. Abilitare il failover su dischi gestiti aggiornando le proprietà della macchina virtualeEnable failover to managed disks by updating VM properties
> 1. Utilizzare `Get-AzRecoveryServicesAsrReplicationProtectedItem` il cmdlet per recuperare l'ID del disco per ogni disco dell'elemento protetto
> 1. Creare un oggetto `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` dizionario utilizzando il cmdlet per contenere il mapping dell'ID disco al set di crittografia del disco. Questi set di crittografia del disco devono essere pre-creati dall'utente nell'area di destinazione.
> 1. Aggiornare le `Set-AzRecoveryServicesAsrReplicationProtectedItem` proprietà della macchina virtuale utilizzando il cmdlet passando l'oggetto dizionario nel parametro **DiskIdToDiskEncryptionSetMap.**

## <a name="run-a-test-failover"></a>Eseguire un failover di test

Per testare la distribuzione, eseguire un failover di test per una singola macchina virtuale. È anche possibile creare un piano di ripristino che contiene più macchine virtuali ed eseguire un failover di test per il piano. Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata.

1. Recuperare la macchina virtuale in cui verrà eseguito il failover delle macchine virtuali.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Eseguire un failover di test.

   Per una singola macchina virtuale:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   Per un piano di ripristino:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

Per verificare il completamento dell'operazione, attenersi alla procedura descritta in [Monitorare l'attività](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Eseguire failover pianificati e non pianificati

1. Eseguire un failover pianificato.

   Per una singola macchina virtuale:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Per un piano di ripristino:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Eseguire un failover non pianificato.

   Per una singola macchina virtuale:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Per un piano di ripristino:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Monitorare l'attività

Usare i comandi seguenti per monitorare l'attività di failover. Attendere la fine dell'elaborazione tra i processi.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](/powershell/module/az.recoveryservices) su Site Recovery con i cmdlet di PowerShell per Resource Manager.
