---
title: Crittografia del disco di Azure con Azure AD per macchine virtuali Windows (versione precedente)Azure Disk Encryption with Azure AD for Windows VMs (previous release)
description: Questo articolo offre informazioni sull'abilitazione di Crittografia dischi di Microsoft Azure per le macchine virtuali IaaS Windows.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8677fa2620c1edc646dcffe120938f03fd13a0e5
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085622"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Crittografia del disco di Azure con Azure AD per macchine virtuali Windows (versione precedente)Azure Disk Encryption with Azure AD for Windows VMs (previous release)

**La nuova versione di Crittografia disco di Azure elimina il requisito di fornire un parametro dell'applicazione Azure AD per abilitare la crittografia del disco della macchina virtuale. Con la nuova versione, non è più necessario fornire le credenziali di Azure AD durante il passaggio di abilitazione della crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza i parametri dell'applicazione Azure AD usando la nuova versione. Per visualizzare le istruzioni per abilitare la crittografia del disco della macchina virtuale usando la nuova versione, vedere Crittografia disco di [Azure per Windows VMS.](disk-encryption-windows.md) Le macchine virtuali che sono già state crittografate con i parametri dell'applicazione Azure AD sono ancora supportate e devono continuare a essere mantenute con la sintassi AAD.**


È possibile abilitare molti scenari di crittografia dei dischi e la procedura può variare in base allo scenario. Le sezioni seguenti illustrano in modo più dettagliato gli scenari per le macchine virtuali IaaS Windows. Prima di poter usare crittografia dei dischi, è necessario soddisfare i [prerequisiti di Crittografia dischi di Azure](disk-encryption-overview-aad.md). 


>[!IMPORTANT]
> - È necessario [creare uno snapshot](snapshot-copy-managed-disk.md) e/o creare un backup prima di crittografare i dischi. I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Le macchine virtuali con dischi gestiti richiedono il backup prima della crittografia. Una volta eseguito un backup, è possibile utilizzare il [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Per altre informazioni su come eseguire il backup e il ripristino delle macchine virtuali crittografate, vedere Eseguire il backup e il ripristino di macchine virtuali di [Azure crittografate.](../../backup/backup-azure-vms-encryption.md) 
>
> - La crittografia o la disabilitazione della crittografia può causare il riavvio di una macchina virtuale.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Abilitare la crittografia di nuove macchine virtuali IaaS create dal Marketplace
È possibile abilitare la crittografia dischi nella nuova macchina virtuale IaaS Windows dal Marketplace in Azure usando il modello di Resource Manager. Questo modello crea una nuova macchina virtuale Windows crittografata usando l'immagine della raccolta di Windows Server 2012.

1. Nel [modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) fare clic su **Deploy to Azure** (Distribuisci in Azure).

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, i parametri, i termini legali e il contratto. Fare clic su **Acquista** per distribuire una nuova macchina virtuale IaaS con la crittografia abilitata.

3. Dopo aver distribuito il modello, verificare lo stato di crittografia della macchina virtuale usando il metodo preferito:
     - Verificare con l'interfaccia della riga di comando di Azure usando il comando [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Verificare con Azure PowerShell utilizzando il cmdlet [Get-AzVmDiskEncryptionStatus.Verify](/powershell/module/az.compute/get-azvmdiskencryptionstatus) with Azure PowerShell by using the Get-AzVmDiskEncryptionStatus cmdlet. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Selezionare la macchina virtuale, quindi fare clic su **Dischi** sotto l'intestazione **Impostazioni** per verificare lo stato della crittografia nel portale. Nel grafico sotto **Crittografia** è indicato se lo stato è abilitato. 
           ![Azure portal - Disk Encryption Enabled](../media/disk-encryption/disk-encryption-fig2.png)

La tabella seguente elenca i parametri del modello di Resource Manager per lo scenario con nuove macchine virtuali dal Marketplace con ID client di Azure AD:

| Parametro | Descrizione | 
| --- | --- |
| adminUserName | Nome utente dell'amministratore per la macchina virtuale. |
| adminPassword | Password utente dell'amministratore per la macchina virtuale. |
| newStorageAccountName | Nome dell'account di archiviazione per archiviare i dischi rigidi virtuali di dati e del sistema operativo. |
| vmSize | Dimensioni della macchina virtuale. Attualmente sono supportate solo le serie A, D e G Standard. |
| virtualNetworkName | Nome della rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. |
| subnetName | Nome della subnet nella rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. |
| AADClientID | ID client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | Segreto client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultURL | URL dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo usando il cmdlet `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` o l'interfaccia della riga di comando di Azure `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata (facoltativo). </br> </br>KeyEncryptionKeyURL è un parametro facoltativo. È possibile specificare la chiave di crittografia della chiave (KEK) personalizzata per la protezione aggiuntiva della chiave DEK (segreto passphrase) nell'insieme di credenziali delle chiavi. |
| keyVaultResourceGroup | Gruppo di risorse dell'insieme di credenziali delle chiavi. |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Abilitare la crittografia nelle macchine virtuali IaaS Windows esistenti o in esecuzione
In questo scenario è possibile abilitare la crittografia usando un modello, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti illustrano in modo più approfondito come abilitare Crittografia dischi di Azure. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Abilitare la crittografia in una macchina virtuale esistente o in esecuzione con Azure PowerShell 
Usare il cmdlet Set-AzVMDiskEncryptionExtension per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure.Use the [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet to enable encryption on a running IaaS virtual machine in Azure. Per informazioni sull'abilitazione della crittografia con Crittografia dischi di Azure tramite cmdlet di PowerShell, vedere i post di blog [Explore Azure Disk Encryption with Azure PowerShell - Part 1](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) (Esplorare Crittografia dischi di Azure con Azure PowerShell - Parte 1) ed [Explore Azure Disk Encryption with Azure PowerShell - Part 2](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) (Esplorare Crittografia dischi di Azure con Azure PowerShell - Parte 2).

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:Encrypt** a running VM using a client secret: Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, l'app AAD e il segreto client sono già stati creati per impostazione predefinita. Sostituire MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret con i valori specificati.
     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Crittografare una macchina virtuale esistente usando una chiave di crittografia della chiave per eseguire il wrapping del segreto client:** Crittografia dischi di Azure consente di specificare una chiave esistente nell'insieme di credenziali delle chiavi per eseguire il wrapping dei segreti di crittografia dei dischi generati quando la crittografia è stata abilitata. Quando viene specificata una chiave di crittografia della chiave, Crittografia dischi di Azure la usa per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificare che i dischi siano crittografati:** Per verificare lo stato di crittografia di una macchina virtuale IaaS, utilizzare il cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Disabilitare la crittografia del disco:** per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Abilitare la crittografia in macchine virtuali esistenti o in esecuzione con l'interfaccia della riga di comando di Azure
Usare il comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure.

- **Crittografare una macchina virtuale in esecuzione usando un segreto client:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave per eseguire il wrapping del segreto client:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificare che i dischi siano crittografati:** Per verificare lo stato di crittografia di una macchina virtuale IaaS, usare il comando [az vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Disabilitare la crittografia:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Uso del modello di Resource Manager
È possibile abilitare la crittografia dei dischi nelle macchine virtuali IaaS Windows esistenti o in esecuzione in Azure usando il [modello di Resource Manager per crittografare una macchina virtuale Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. Nel modello di avvio rapido di Azure fare clic su **Deploy to Azure**.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, i parametri, i termini legali e il contratto. Fare clic su **Acquista** per abilitare la crittografia in una macchina IaaS esistente o in esecuzione.

La tabella seguente elenca i parametri del modello di Resource Manager per macchine virtuali esistenti o in esecuzione che usano un ID client di Azure AD:

| Parametro | Descrizione |
| --- | --- |
| AADClientID | ID client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | Segreto client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo usando il cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` o il comando dell'interfaccia della riga di comando di Azure `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata. Questo parametro è facoltativo se si seleziona **nokek** dall'elenco a discesa UseExistingKek. Se si seleziona **kek** dall'elenco a discesa UseExistingKek, è necessario immettere il valore _keyEncryptionKeyURL_. |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori validi sono _OS_, _Data_ e _All_. |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di crittografia dei dischi nella stessa VM. |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nuove macchine virtuali IaaS create da un disco rigido virtuale e chiavi di crittografia crittografate dal cliente
In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti descrivono in modo più dettagliato il modello di Resource Manager e i comandi dell'interfaccia della riga di comando. 

Usare le istruzioni nell'appendice per la preparazione delle immagini pre-crittografate che possono essere usate in Azure. Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM di Azure crittografata.

* [Preparare un disco rigido virtuale Windows pre-crittografato](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"> </a> Crittografare le macchine virtuali con dischi rigidi virtuali pre-crittografati con Azure PowerShell
È possibile abilitare la crittografia del disco nel disco rigido virtuale crittografato utilizzando il cmdlet di PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). L'esempio seguente offre alcuni parametri comuni. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Abilitare la crittografia in un disco dati appena aggiunto
È possibile [aggiungere un nuovo disco a una macchina virtuale Windows con PowerShell](attach-disk-ps.md) o [tramite il portale di Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Abilitare la crittografia in un disco appena aggiunto con Azure PowerShell
 Quando si usa Powershell per crittografare un nuovo disco per le macchine virtuali Windows, è necessario specificare una nuova versione della sequenza. La versione della sequenza deve essere univoca. Lo script seguente genera un GUID per la versione della sequenza. In alcuni casi un disco dati appena aggiunto potrebbe essere crittografato automaticamente dall'estensione di Crittografia dischi di Azure. La crittografia automatica viene in genere eseguita quando la macchina virtuale viene riavviata dopo che il nuovo disco viene portato online. Ciò è solitamente dovuto al fatto che è stata specificata l'opzione corrispondente a tutti i tipi di volume quando la crittografia dei dischi è stata eseguita in precedenza nella macchina virtuale. Se la crittografia automatica viene eseguita in un disco dati appena aggiunto, è consigliabile eseguire nuovamente il cmdlet Set-AzVmDiskEncryptionExtension con una nuova versione di sequenza. Se il nuovo disco dati viene crittografato automaticamente ma non si vuole che sia crittografato, decrittografare prima tutte le unità e quindi rieseguire la crittografia con una nuova versione della sequenza specificando il sistema operativo per il tipo di volume. 
 

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:Encrypt** a running VM using a client secret: Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, l'app AAD e il segreto client sono già stati creati per impostazione predefinita. Sostituire MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret con i valori specificati. Questo esempio usa "All" per il parametro -VolumeType, che include i volumi dei dati e del sistema operativo. Se si vuole crittografare solo il volume del sistema operativo, usare "OS" per il parametro -VolumeType. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Crittografare una macchina virtuale esistente usando una chiave di crittografia della chiave per eseguire il wrapping del segreto client:** Crittografia dischi di Azure consente di specificare una chiave esistente nell'insieme di credenziali delle chiavi per eseguire il wrapping dei segreti di crittografia dei dischi generati quando la crittografia è stata abilitata. Quando viene specificata una chiave di crittografia della chiave, Crittografia dischi di Azure la usa per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. Questo esempio usa "All" per il parametro -VolumeType, che include i volumi dei dati e del sistema operativo. Se si vuole crittografare solo il volume del sistema operativo, usare "OS" per il parametro -VolumeType. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Abilitare la crittografia in un disco appena aggiunto con l'interfaccia della riga di comando di Azure
  Il comando dell'interfaccia della riga di comando di Azure fornisce automaticamente una nuova versione della sequenza quando si esegue il comando per abilitare la crittografia. I valori accettabili per il parametro -volumetype sono All, OS e Data. Può essere necessario impostare il parametro volume-type su OS o Data se si sta crittografando solo un tipo di disco per la VM. Gli esempi usano "All" per il parametro volume-type. 

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave per eseguire il wrapping del segreto client:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Abilitare la crittografia usando l'autenticazione basata sul certificato client di Azure AD.
È possibile usare l'autenticazione del certificato client con o senza chiave di crittografia della chiave. Prima di usare gli script di PowerShell, il certificato dovrebbe già essere stato caricato nell'insieme di credenziali delle chiavi e distribuito nella macchina virtuale. Se si usa anche la chiave di crittografia della chiave, tale chiave dovrebbe esistere già. Per altre informazioni, vedere la sezione [Certificate-based authentication for Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) (Autenticazione basata sul certificato per Azure AD) dell'articolo sui prerequisiti.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Abilitare la crittografia usando l'autenticazione basata sul certificato con Azure PowerShell

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Abilitare la crittografia usando l'autenticazione basata sul certificato e una chiave di crittografia della chiave con Azure PowerShell

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Disabilitare la crittografia
È possibile disabilitare la crittografia usando Azure PowerShell, l'interfaccia della riga di comando di Azure oppure un modello di Resource Manager. 

- **Disabilitare la crittografia del disco con Azure PowerShell:** per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Disabilitare la crittografia con l'interfaccia della riga di comando di Azure:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Disabilitare la crittografia con un modello di Resource Manager:Disable encryption with a Resource Manager Template:** 

    1. Fare clic su **Distribuisci in Azure** dal modello [Disabilita crittografia disco in esecuzione macchina virtuale Windows.Click](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) Deploy to Azure from the Disable disk encryption on running Windows VM template.
    2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, la macchina virtuale, i termini legali e il contratto.
    3.  Fare clic su **Acquista** per disabilitare la crittografia dischi in una macchina virtuale Windows in esecuzione. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su Crittografia dischi di Azure](disk-encryption-overview.md)
