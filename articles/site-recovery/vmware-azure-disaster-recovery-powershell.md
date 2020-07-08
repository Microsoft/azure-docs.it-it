---
title: Configurare il ripristino di emergenza di VMware con PowerShell in Azure site Revo
description: Informazioni su come configurare la replica e il failover in Azure per il ripristino di emergenza delle macchine virtuali VMware tramite PowerShell in Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: d2dfaab3d01ea29b0f9ecba1e9d748415bed2edc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84704387"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Configurare il ripristino di emergenza di VM VMware in Azure con PowerShell

Questo articolo illustra come eseguire la replica e il failover di macchine virtuali VMware in Azure tramite Azure PowerShell.

Si apprenderà come:

> [!div class="checklist"]
> - Creare un insieme di credenziali di Servizi di ripristino e impostare il contesto dell'insieme di credenziali.
> - Convalidare la registrazione di server nell'insieme di credenziali.
> - Configurare la replica, tra cui un criterio di replica. Aggiungere il server vCenter e individuare le macchine virtuali.
> - Aggiungere un server vCenter e individuare
> - Creare gli account di archiviazione per conservare i log o i dati di replica e replicare le macchine virtuali.
> - Eseguire un failover. Configurare le impostazioni di failover, eseguire le impostazioni per la replica delle macchine virtuali.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](vmware-azure-architecture.md).
- Esaminare i [requisiti di supporto](site-recovery-support-matrix-to-azure.md) per tutti i componenti.
- Si dispone del `Az` modulo Azure PowerShell. Se è necessario installare o aggiornare Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure usando il cmdlet Connect-AzAccount:

```azurepowershell
Connect-AzAccount
```
Selezionare la sottoscrizione di Azure in cui si vogliono replicare le macchine virtuali VMware. Usare il cmdlet Get-AzSubscription per ottenere l'elenco delle sottoscrizioni di Azure a cui si ha accesso. Selezionare la sottoscrizione di Azure da usare con il cmdlet Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Configurare un insieme di credenziali dei Servizi di ripristino

1. Creare il gruppo di risorse in cui creare l'insieme di credenziali di Servizi di ripristino. Nell'esempio seguente, il gruppo di risorse è denominato VMwareDRtoAzurePS ed è stato creato nell'area Asia orientale.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Creare un insieme di credenziali di Servizi di ripristino. Nell'esempio seguente, l'insieme di credenziali di Servizi di ripristino è denominato VMwareDRToAzurePs ed è stato creato nell'area Asia orientale, nel gruppo di risorse creato nel passaggio precedente.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. Scaricare la chiave di registrazione dell'insieme di credenziali, che consente di registrare il server di configurazione locale nell'insieme di credenziali. La registrazione è parte integrante del processo di installazione del software del server di configurazione.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Usare la chiave di registrazione dell'insieme di credenziali scaricata e seguire le procedure descritte negli articoli elencati di seguito per completare l'installazione e la registrazione del server di configurazione.
   - [Scegliere gli obiettivi della protezione](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Configurare l'ambiente di origine](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Impostare il contesto dell'insieme di credenziali

Impostare il contesto dell'insieme di credenziali usando il cmdlet Set-ASRVaultContext. In questo modo, le successive operazioni di Azure Site Recovery nella sessione di PowerShell verranno eseguite nel contesto dell'insieme di credenziali selezionato.

> [!TIP]
> Il modulo Azure Site Recovery PowerShell (AZ. RecoveryServices Module) è dotato di alias facili da usare per la maggior parte dei cmdlet. I cmdlet nel modulo hanno il formato * \<Operation> - **AzRecoveryServicesAsr** \<Object> * e hanno alias equivalenti che hanno il formato * \<Operation> - **ASR** \<Object> *. È possibile sostituire gli alias dei cmdlet per facilitarne l'uso.

Nell'esempio seguente, vengono usati i dettagli dell'insieme di credenziali ottenuti dalla variabile $vault per specificare il contesto dell'insieme di credenziali per la sessione di PowerShell.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

In alternativa al cmdlet Set-ASRVaultContext, è anche possibile usare il cmdlet Import-AzRecoveryServicesAsrVaultSettingsFile per impostare il contesto dell'insieme di credenziali. Specificare il percorso in cui si trova il file della chiave di registrazione dell'insieme di credenziali come parametro-Path del cmdlet Import-AzRecoveryServicesAsrVaultSettingsFile. Ad esempio:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Nelle sezioni successive di questo articolo si presuppone che il contesto dell'insieme di credenziali per le operazioni di Azure Site Recovery sia stato impostato.

## <a name="validate-vault-registration"></a>Convalidare la registrazione dell'insieme di credenziali

Per questo esempio, è disponibile quanto segue:

- Un server di configurazione (**ConfigurationServer**) è stato registrato in questo insieme di credenziali.
- Un server di elaborazione aggiuntivo (**ScaleOut-ProcessServer**) è stato registrato in *ConfigurationServer*
- Account (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) siano stati impostati nel server di configurazione. Questi account consentono di aggiungere il server vCenter per individuare le macchine virtuali ed eseguire l'installazione push del software del servizio Mobility nei server Windows e Linux che devono essere replicati.

1. I server di configurazione registrati sono rappresentati in Site Recovery da un oggetto di infrastruttura. Ottenere l'elenco degli oggetti di infrastruttura presenti nell'insieme di credenziali e identificare il server di configurazione.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. Identificare i server di elaborazione che è possibile usare per la replica dei computer.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   In base all'output precedente, ***$ProcessServers[0]*** corrisponde a *ScaleOut-ProcessServer* e ***$ProcessServers[1]*** corrisponde al ruolo del server di elaborazione in *ConfigurationServer*

3. Identificare gli account configurati nel server di configurazione.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   In base all'output precedente, ***$AccountHandles[0]*** corrisponde all'account *vCenter_account*, ***$AccountHandles[1]*** all'account *WindowsAccount* e ***$AccountHandles[2]*** all'account *LinuxAccount*

## <a name="create-a-replication-policy"></a>Creare un criterio di replica

In questo passaggio vengono creati due gruppi di criteri di replica, i primi per replicare le macchine virtuali VMware in Azure, i secondi per replicare di nuovo nel sito VMware locale le macchine virtuali sottoposte a failover in esecuzione in Azure.

> [!NOTE]
> La maggior parte delle operazioni di Azure Site Recovery viene eseguita in modo asincrono. Quando si avvia un'operazione, viene inviato un processo di Azure Site Recovery e viene restituito un oggetto di monitoraggio del processo, che consente di monitorare lo stato dell'operazione.

1. Creare criteri di replica denominati *ReplicationPolicy* per replicare in Azure le macchine virtuali VMware con le proprietà specificate.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Creare i criteri di replica da usare per il failback da Azure al sito VMware locale.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Usare i dettagli del processo presenti in *$Job_FailbackPolicyCreate* per tenere traccia dell'operazione fino al completamento.

   * Creare un mapping del contenitore di protezione per eseguire il mapping dei criteri di replica con il server di configurazione.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Aggiungere un server vCenter e individuare le macchine virtuali

Aggiungere un server vCenter in base all'indirizzo IP o al nome host. Il parametro **-port** specifica la porta del server vCenter a cui connettersi, il parametro **-Name** specifica un nome descrittivo da usare per il server vCenter e il parametro **-Account** specifica l'handle di account del server di configurazione da usare per individuare le macchine virtuali gestite dal server vCenter.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>Creare gli account di archiviazione per la replica

**Per scrivere nel disco gestito, usare il [modulo RecoveryServices di PowerShell 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) e versioni successive.** Richiede solo la creazione di un account di archiviazione di log. Si consiglia di utilizzare un tipo di account standard e una ridondanza con ridondanza locale poiché viene utilizzata per archiviare solo i log temporanei. Verificare che l'account di archiviazione sia stato creato nella stessa area di Azure dell'insieme di credenziali.

Se si usa una versione del modulo AZ. RecoveryServices precedente alla 2.0.0, seguire questa procedura per creare gli account di archiviazione. Questi account di archiviazione verranno usati in un secondo momento per replicare le macchine virtuali. Verificare che gli account di archiviazione vengano creati nella stessa area di Azure in cui è stato creato l'insieme di credenziali. È possibile ignorare questo passaggio se si prevede di usare un account di archiviazione esistente per la replica.

> [!NOTE]
> Durante la replica di macchine virtuali locali in un account di archiviazione Premium, è necessario specificare un account di archiviazione Standard aggiuntivo (account di archiviazione log). L'account di archiviazione log svolge la funzione di archivio intermedio in cui vengono conservati i log di replica fino a quando non possono essere applicati nella destinazione di archiviazione Premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replicare VM VMware

Per individuare le macchine virtuali, il server vCenter impiega un intervallo di circa 15-20 minuti, al termine del quale, per ogni macchina virtuale individuata, viene creato in Azure Site Recovery un oggetto di elemento da proteggere. In questo passaggio, tre delle macchine virtuali individuate vengono replicate negli account di archiviazione di Azure creati nel passaggio precedente.

Per proteggere una macchina virtuale individuata sono necessari i dettagli seguenti:

* L'elemento da proteggere che deve essere replicato.
* L'account di archiviazione in cui replicare la macchina virtuale (solo se si sta eseguendo la replica nell'account di archiviazione). 
* Per proteggere le macchine virtuali in un account di archiviazione Premium o in un disco gestito, è necessario disporre di un archivio log.
* Il server di elaborazione usato per la replica. L'elenco dei server di elaborazione disponibili è stato recuperato e salvato nelle variabili ***$ProcessServers[0]***  *(ScaleOut-ProcessServer)* e ***$ProcessServers[1]*** *(ConfigurationServer)*.
* L'account da usare per eseguire nei computer l'installazione push del software del servizio Mobility. L'elenco degli account disponibili è stato recuperato e archiviato nella variabile ***$AccountHandles***.
* Il mapping del contenitore di protezione per i criteri di replica da usare per la replica.
* Il gruppo di risorse in cui devono essere create le macchine virtuali in caso di failover.
* Facoltativamente, la rete virtuale e la subnet di Azure a cui devono essere connesse le macchine virtuali sottoposte a failover.

Replicare ora le macchine virtuali seguenti usando le impostazioni specificate in questa tabella


|Macchina virtuale  |Server di elaborazione        |Account di archiviazione              |Account di archiviazione log  |Policy           |Account per l'installazione del servizio Mobility|Gruppo di risorse di destinazione  | Rete virtuale di destinazione  |Subnet di destinazione  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |N/D| logstorageaccount1                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| N/D                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Al termine del processo di abilitazione della replica, viene avviata la replica iniziale per le macchine virtuali. È possibile che la replica iniziale richieda qualche minuto, in base alla quantità di dati da replicare e alla larghezza di banda disponibile per la replica. Al termine della replica iniziale, la macchina virtuale viene impostata su uno stato protetto. A questo punto, è possibile eseguire un failover di test per la macchina virtuale, aggiungerlo ai piani di ripristino e così via.

Per controllare lo stato di replica e l'integrità della replica della macchina virtuale, è possibile usare il cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Configurare le impostazioni di failover

Le impostazioni di failover per i computer protetti possono essere aggiornate usando il cmdlet Set-ASRReplicationProtectedItem. Di seguito sono elencate alcune delle impostazioni che è possibile aggiornare con questo cdmlet:
* Nome della macchina virtuale da creare in caso di failover
* Dimensione della macchina virtuale da creare in caso di failover
* Rete virtuale e subnet di Azure a cui devono connettersi le schede di interfaccia di rete della macchina virtuale in caso di failover
* Failover su Managed Disks
* Applicazione del vantaggio Azure Hybrid Use
* Assegnazione di un indirizzo IP statico dalla rete virtuale di destinazione alla macchina virtuale in caso di failover.

In questo esempio viene aggiornata la dimensione della macchina virtuale da creare in caso di failover della macchina virtuale *Win2K12VM1* e si specifica che la macchina virtuale deve usare Managed Disks in caso di failover.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>Eseguire un failover di test

1. Eseguire un' esercitazione sul ripristino di emergenza (failover di test) come segue:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Al termine del processo di failover di test, si noterà che in Azure è stata creata una macchina virtuale con suffisso *"-Test"* (in questo caso, Win2K12VM1-Test).
3. È ora possibile connettersi alla macchina virtuale sottoposta al failover di test e convalidare il failover di test.
4. Eliminare il failover di test usando il cmdlet Start-ASRTestFailoverCleanupJob. Questa operazione elimina la macchina virtuale creata nell'ambito dell'operazione di failover di test.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Failover in Azure

In questo passaggio viene eseguito il failover della macchina virtuale Win2K12VM1 in un punto di ripristino specifico.

1. Ottenere un elenco di punti di ripristino disponibili da usare per il failover:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Al termine del failover, è possibile eseguire il commit dell'operazione di failover e configurare la replica inversa da Azure al sito VMware locale.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come automatizzare più attività usando il [riferimento Azure Site Recovery PowerShell](https://docs.microsoft.com/powershell/module/Az.RecoveryServices).
