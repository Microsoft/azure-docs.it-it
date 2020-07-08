---
title: Usare PowerShell per configurare le chiavi gestite dal cliente
titleSuffix: Azure Storage
description: Informazioni su come usare PowerShell per configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 6b2983bbaf22ae1b9e09ff3362a4bc06e6658b33
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506200"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Configurare chiavi gestite dal cliente con Azure Key Vault tramite PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Questo articolo illustra come configurare un Azure Key Vault con chiavi gestite dal cliente tramite PowerShell. Per informazioni su come creare un insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando di Azure, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault usando PowerShell](../../key-vault/secrets/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Assegnare un'identità all'account di archiviazione

Per abilitare le chiavi gestite dal cliente per l'account di archiviazione, assegnare innanzitutto un'identità gestita assegnata dal sistema all'account di archiviazione. Questa identità gestita verrà usata per concedere le autorizzazioni dell'account di archiviazione per accedere all'insieme di credenziali delle chiavi.

Per assegnare un'identità gestita usando PowerShell, chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Per altre informazioni sulla configurazione di identità gestite assegnate dal sistema con PowerShell, vedere [configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure con PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Creare un nuovo insieme di credenziali delle chiavi

Per creare un nuovo insieme di credenziali delle chiavi usando PowerShell, chiamare [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). L'insieme di credenziali delle chiavi usato per archiviare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure deve avere due impostazioni di protezione della chiave abilitate, l' **eliminazione** temporanea e non la **ripulitura**.

Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Per informazioni su come abilitare **l'eliminazione** temporanea e **non ripulirla** in un insieme di credenziali delle chiavi esistente con PowerShell, vedere le sezioni intitolate **Abilitazione** dell'eliminazione temporanea e **Abilitazione della protezione di ripulitura** in [come usare l'eliminazione temporanea con PowerShell](../../key-vault/general/soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi

Configurare quindi i criteri di accesso per l'insieme di credenziali delle chiavi in modo che l'account di archiviazione disponga delle autorizzazioni di accesso. In questo passaggio si userà l'identità gestita precedentemente assegnata all'account di archiviazione.

Per impostare i criteri di accesso per l'insieme di credenziali delle chiavi, chiamare [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Creare una nuova chiave

Successivamente, creare una nuova chiave nell'insieme di credenziali delle chiavi. Per creare una nuova chiave, chiamare [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

La crittografia di archiviazione di Azure supporta chiavi RSA e RSA-HSM di dimensioni 2048, 3072 e 4096. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurare la crittografia con chiavi gestite dal cliente

Per impostazione predefinita, la crittografia di archiviazione di Azure usa chiavi gestite da Microsoft. In questo passaggio configurare l'account di archiviazione di Azure per l'uso delle chiavi gestite dal cliente e specificare la chiave da associare all'account di archiviazione.

Chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente. Includere l'opzione **-KeyvaultEncryption** per abilitare le chiavi gestite dal cliente per l'account di archiviazione. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, è necessario aggiornare l'account di archiviazione per usare la nuova versione. Prima di tutto, chiamare [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) per ottenere la versione più recente della chiave. Chiamare quindi [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione della chiave, come illustrato nella sezione precedente.

## <a name="use-a-different-key"></a>Usare una chiave diversa

Per modificare la chiave usata per la crittografia di archiviazione di Azure, chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , come illustrato nella pagina relativa alla [configurazione della crittografia con chiavi gestite dal cliente](#configure-encryption-with-customer-managed-keys) e fornire il nome e la versione della nuova chiave. Se la nuova chiave si trova in un insieme di credenziali delle chiavi diverso, aggiornare anche l'URI dell'insieme di credenziali delle chiavi.

## <a name="revoke-customer-managed-keys"></a>Revocare le chiavi gestite dal cliente

Se si ritiene che una chiave possa essere stata compromessa, è possibile revocare le chiavi gestite dal cliente rimuovendo i criteri di accesso di Key Vault. Per revocare una chiave gestita dal cliente, chiamare il comando [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal cliente

Quando si disabilitano le chiavi gestite dal cliente, l'account di archiviazione viene nuovamente crittografato con le chiavi gestite da Microsoft. Per disabilitare le chiavi gestite dal cliente, chiamare [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) con l' `-StorageEncryption` opzione, come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
