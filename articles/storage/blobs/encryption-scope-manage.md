---
title: Creare e gestire gli ambiti di crittografia
description: Informazioni su come creare un ambito di crittografia per isolare i dati BLOB a livello di contenitore o BLOB.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: c29282637f6854248c98dff59f8fae46ad1a9d39
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640570"
---
# <a name="create-and-manage-encryption-scopes"></a>Creare e gestire gli ambiti di crittografia

Gli ambiti di crittografia consentono di gestire la crittografia a livello di singolo BLOB o contenitore. È possibile usare gli ambiti di crittografia per creare confini sicuri tra i dati che si trovano nello stesso account di archiviazione, ma appartenenti a clienti diversi. Per ulteriori informazioni sugli ambiti di crittografia, vedere [ambiti di crittografia per l'archiviazione BLOB](encryption-scope-overview.md).

Questo articolo illustra come creare un ambito di crittografia. Viene inoltre illustrato come specificare un ambito di crittografia quando si crea un BLOB o un contenitore.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Creare un ambito di crittografia

È possibile creare un ambito di crittografia protetto con una chiave gestita da Microsoft o con una chiave gestita dal cliente, archiviata in un Azure Key Vault o in un modulo di protezione hardware (HSM) gestito Azure Key Vault (anteprima). Per creare un ambito di crittografia con una chiave gestita dal cliente, è prima necessario creare un insieme di credenziali delle chiavi o un modulo di protezione hardware gestito e aggiungere la chiave che si intende usare per l'ambito. L'insieme di credenziali delle chiavi o il modulo di protezione hardware gestito deve avere la protezione ripulitura abilitata e deve trovarsi nella stessa area dell'account di archiviazione.

Un ambito di crittografia viene abilitato automaticamente al momento della creazione. Dopo aver creato l'ambito di crittografia, è possibile specificarlo quando si crea un BLOB. È anche possibile specificare un ambito di crittografia predefinito quando si crea un contenitore, che viene applicato automaticamente a tutti i BLOB nel contenitore.

# <a name="portal"></a>[Portale](#tab/portal)

Per creare un ambito di crittografia nella portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione nel portale di Azure.
1. Selezionare l'impostazione di **crittografia** .
1. Selezionare la scheda **ambiti di crittografia** .
1. Fare clic sul pulsante **Aggiungi** per aggiungere un nuovo ambito di crittografia.
1. Nel riquadro **Crea ambito di crittografia** immettere un nome per il nuovo ambito.
1. Selezionare il tipo di supporto della chiave di crittografia desiderato, ovvero **chiavi gestite da Microsoft** o **chiavi gestite dal cliente**.
    - Se è stata selezionata l'opzione **chiavi gestite da Microsoft**, fare clic su **Crea** per creare l'ambito di crittografia.
    - Se sono state selezionate **chiavi gestite dal cliente**, selezionare una sottoscrizione e specificare un insieme di credenziali delle chiavi o un modulo di protezione hardware gestito e una chiave da usare per questo ambito di crittografia, come illustrato nella figura seguente.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Screenshot che illustra come creare un ambito di crittografia in portale di Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per creare un ambito di crittografia con PowerShell, installare il modulo [AZ. storage](https://www.powershellgallery.com/packages/Az.Storage) PowerShell, versione 3.4.0 o successiva.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Creare un ambito di crittografia protetto da chiavi gestite da Microsoft

Per creare un nuovo ambito di crittografia protetto da chiavi gestite da Microsoft, chiamare il comando **New-AzStorageEncryptionScope** con il `-StorageEncryption` parametro.

Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Creare un ambito di crittografia protetto da chiavi gestite dal cliente

Per creare un nuovo ambito di crittografia protetto da chiavi gestite dal cliente archiviate in un insieme di credenziali delle chiavi o un modulo di protezione hardware gestito, configurare prima le chiavi gestite dal cliente per l'account di archiviazione. È necessario assegnare un'identità gestita all'account di archiviazione e quindi usare l'identità gestita per configurare i criteri di accesso per l'insieme di credenziali delle chiavi o per il modulo di protezione hardware gestito in modo che l'account di archiviazione disponga delle autorizzazioni di accesso.

Per configurare le chiavi gestite dal cliente per l'uso con un ambito di crittografia, è necessario abilitare l'eliminazione della protezione nell'insieme di credenziali delle chiavi o nel modulo di protezione hardware gestito. L'insieme di credenziali delle chiavi o il modulo di protezione hardware gestito deve trovarsi nella stessa area dell'account di archiviazione.

Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri>"
$scopeName2 = "customer2scope"

# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Chiamare quindi il comando **New-AzStorageEncryptionScope** con il `-KeyvaultEncryption` parametro e specificare l'URI della chiave. L'inclusione della versione della chiave sull'URI della chiave è facoltativa. Se si omette la versione della chiave, l'ambito di crittografia userà automaticamente la versione più recente della chiave. Se si include la versione della chiave, è necessario aggiornare manualmente la versione della chiave per usare una versione diversa.

Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Per creare un ambito di crittografia con l'interfaccia della riga di comando di Azure, installare prima l'interfaccia della riga di comando di Azure 2.20.0

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Creare un ambito di crittografia protetto da chiavi gestite da Microsoft

Per creare un nuovo ambito di crittografia protetto da chiavi gestite da Microsoft, chiamare il comando [AZ storage account Encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) , specificando il `--key-source` parametro come `Microsoft.Storage` . Ricordarsi di sostituire i valori segnaposto con valori personalizzati:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Creare un ambito di crittografia protetto da chiavi gestite dal cliente

Per creare un nuovo ambito di crittografia protetto da chiavi gestite da Microsoft, chiamare il comando [AZ storage account Encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) , specificando il `--key-source` parametro come `Microsoft.Storage` . Ricordarsi di sostituire i valori segnaposto con valori personalizzati:

Per creare un nuovo ambito di crittografia protetto da chiavi gestite dal cliente in un insieme di credenziali delle chiavi o un modulo di protezione hardware gestito, configurare prima le chiavi gestite dal cliente per l'account di archiviazione. È necessario assegnare un'identità gestita all'account di archiviazione e quindi usare l'identità gestita per configurare i criteri di accesso per l'insieme di credenziali delle chiavi in modo che l'account di archiviazione disponga delle autorizzazioni per l'accesso. Per altre informazioni, vedere [Chiavi gestite dal cliente per la crittografia di archiviazione di Azure](../common/customer-managed-keys-overview.md).

Per configurare le chiavi gestite dal cliente per l'uso con un ambito di crittografia, è necessario abilitare l'eliminazione della protezione nell'insieme di credenziali delle chiavi o nel modulo di protezione hardware gestito. L'insieme di credenziali delle chiavi o il modulo di protezione hardware gestito deve trovarsi nella stessa area dell'account di archiviazione.

Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Chiamare quindi il comando **AZ storage account Encryption-scope create** con il `--key-uri` parametro e specificare l'URI della chiave. L'inclusione della versione della chiave sull'URI della chiave è facoltativa. Se si omette la versione della chiave, l'ambito di crittografia userà automaticamente la versione più recente della chiave. Se si include la versione della chiave, è necessario aggiornare manualmente la versione della chiave per usare una versione diversa.

Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Per informazioni su come configurare la crittografia di archiviazione di Azure con chiavi gestite dal cliente in un insieme di credenziali delle chiavi o un modulo di protezione hardware gestito, vedere gli articoli seguenti:

- [Configurare la crittografia con le chiavi gestite dal cliente archiviate in Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md)
- [Configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault HSM gestito (anteprima)](../common/customer-managed-keys-configure-key-vault-hsm.md).

## <a name="list-encryption-scopes-for-storage-account"></a>Elencare gli ambiti di crittografia per l'account di archiviazione

# <a name="portal"></a>[Portale](#tab/portal)

Per visualizzare gli ambiti di crittografia per un account di archiviazione nel portale di Azure, passare all'impostazione **ambiti di crittografia** per l'account di archiviazione. Da questo riquadro è possibile abilitare o disabilitare un ambito di crittografia o modificare la chiave per un ambito di crittografia.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Screenshot che mostra l'elenco degli ambiti di crittografia in portale di Azure":::

Per visualizzare i dettagli di una chiave gestita dal cliente, inclusi l'URI e la versione della chiave e se la versione della chiave viene aggiornata automaticamente, seguire il collegamento nella colonna **chiave** .

:::image type="content" source="media/encryption-scope-manage/customer-managed-key-details-portal.png" alt-text="Screenshot che mostra i dettagli di una chiave usata con un ambito di crittografia":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per elencare gli ambiti di crittografia disponibili per un account di archiviazione con PowerShell, chiamare il comando **Get-AzStorageEncryptionScope** . Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Per elencare tutti gli ambiti di crittografia in un gruppo di risorse in base all'account di archiviazione, usare la sintassi della pipeline:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Per elencare gli ambiti di crittografia disponibili per un account di archiviazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ storage account Encryption-scope list](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) . Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Creare un contenitore con un ambito di crittografia predefinito

Quando si crea un contenitore, è possibile specificare un ambito di crittografia predefinito. Per impostazione predefinita, i BLOB in tale contenitore utilizzeranno tale ambito.

Un singolo BLOB può essere creato con un ambito di crittografia specifico, a meno che il contenitore non sia configurato in modo da richiedere che tutti i BLOB usino l'ambito predefinito. Per altre informazioni, vedere [ambiti di crittografia per contenitori e BLOB](encryption-scope-overview.md#encryption-scopes-for-containers-and-blobs).

# <a name="portal"></a>[Portale](#tab/portal)

Per creare un contenitore con un ambito di crittografia predefinito nel portale di Azure, creare prima l'ambito di crittografia come descritto in [creare un ambito di crittografia](#create-an-encryption-scope). Seguire quindi questa procedura per creare il contenitore:

1. Passare all'elenco dei contenitori nell'account di archiviazione e selezionare il pulsante **Aggiungi** per creare un nuovo contenitore.
1. Espandere le impostazioni **Avanzate** nel riquadro **nuovo contenitore** .
1. Nell'elenco a discesa **ambito crittografia** selezionare l'ambito di crittografia predefinito per il contenitore.
1. Per richiedere che tutti i BLOB nel contenitore usino l'ambito di crittografia predefinito, selezionare la casella di controllo per **usare questo ambito di crittografia per tutti i BLOB nel contenitore**. Se questa casella di controllo è selezionata, un singolo BLOB nel contenitore non può eseguire l'override dell'ambito di crittografia predefinito.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Screenshot che illustra il contenitore con ambito di crittografia predefinito":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per creare un contenitore con un ambito di crittografia predefinito con PowerShell, chiamare il comando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) , specificando l'ambito per il `-DefaultEncryptionScope` parametro. Per forzare tutti i BLOB in un contenitore a usare l'ambito predefinito del contenitore, impostare il `-PreventEncryptionScopeOverride` parametro su `true` .

```powershell
$containerName1 = "container1"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a container with a default encryption scope that cannot be overridden.
New-AzStorageContainer -Name $containerName1 `
    -Context $ctx `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Per creare un contenitore con un ambito di crittografia predefinito con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ Storage container create](/cli/azure/storage/container#az-storage-container-create) , specificando l'ambito per il `--default-encryption-scope` parametro. Per forzare tutti i BLOB in un contenitore a usare l'ambito predefinito del contenitore, impostare il `--prevent-encryption-scope-override` parametro su `true` .

Nell'esempio seguente viene usato l'account Azure AD per autorizzare l'operazione di creazione del contenitore. È anche possibile usare la chiave di accesso dell'account. Per altre informazioni, vedere [Autorizzare l'accesso ai dati di BLOB o code con l'interfaccia della riga di comando di Azure](./authorize-data-operations-cli.md).

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Se un client tenta di specificare un ambito durante il caricamento di un BLOB in un contenitore con un ambito di crittografia predefinito e il contenitore è configurato per impedire che i BLOB eseguano l'override dell'ambito predefinito, l'operazione ha esito negativo con un messaggio che indica che la richiesta non è consentita dai criteri di crittografia del contenitore.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Caricare un BLOB con un ambito di crittografia

Quando si carica un BLOB, è possibile specificare un ambito di crittografia per il BLOB o usare l'ambito di crittografia predefinito per il contenitore, se ne è stato specificato uno.

# <a name="portal"></a>[Portale](#tab/portal)

Per caricare un BLOB con un ambito di crittografia tramite il portale di Azure, creare prima l'ambito di crittografia come descritto in [creare un ambito di crittografia](#create-an-encryption-scope). Quindi, attenersi alla procedura seguente per creare il BLOB:

1. Passare al contenitore in cui si vuole caricare il BLOB.
1. Selezionare il pulsante **upload (carica** ) e individuare il BLOB da caricare.
1. Espandere le impostazioni **Avanzate** nel riquadro **Carica BLOB** .
1. Individuare la sezione elenco a discesa **ambito di crittografia** . Per impostazione predefinita, il BLOB viene creato con l'ambito di crittografia predefinito per il contenitore, se ne è stato specificato uno. Se il contenitore richiede che i BLOB usino l'ambito di crittografia predefinito, questa sezione è disabilitata.
1. Per specificare un ambito diverso per il BLOB da caricare, selezionare **Scegli un ambito esistente**, quindi selezionare l'ambito desiderato dall'elenco a discesa.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Screenshot che illustra come caricare un BLOB con un ambito di crittografia":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per caricare un BLOB con un ambito di crittografia tramite PowerShell, chiamare il comando [set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) e specificare l'ambito di crittografia per il BLOB.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx

# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx `
    -Container $containerName2 `
    -File $localSrcFile `
    -Blob "helloworld.txt" `
    -BlobType Block `
    -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Per caricare un BLOB con un ambito di crittografia tramite l'interfaccia della riga di comando di Azure, chiamare il comando [AZ storage BLOB upload](/cli/azure/storage/blob#az-storage-blob-upload) e specificare l'ambito di crittografia per il BLOB.

Se si usa Azure Cloud Shell, seguire i passaggi descritti in [caricare un BLOB](storage-quickstart-blobs-cli.md#upload-a-blob) per creare un file nella directory radice. È quindi possibile caricare il file in un BLOB usando l'esempio seguente.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Modificare la chiave di crittografia per un ambito

Per modificare la chiave che protegge un ambito di crittografia da una chiave gestita da Microsoft a una chiave gestita dal cliente, assicurarsi prima di aver abilitato le chiavi gestite dal cliente con Azure Key Vault o Key Vault HSM per l'account di archiviazione. Per altre informazioni, vedere [configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md) o [configurare la crittografia con chiavi gestite dal cliente archiviate nel Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md).

# <a name="portal"></a>[Portale](#tab/portal)

Per modificare la chiave che protegge un ambito nella portale di Azure, attenersi alla seguente procedura:

1. Passare alla scheda **ambiti di crittografia** per visualizzare l'elenco degli ambiti di crittografia per l'account di archiviazione.
1. Selezionare il pulsante **altro** accanto all'ambito che si desidera modificare.
1. Nel riquadro **Modifica ambito di crittografia** è possibile modificare il tipo di crittografia da chiave gestita da Microsoft a chiave gestita dal cliente o viceversa.
1. Per selezionare una nuova chiave gestita dal cliente, selezionare **Usa una nuova chiave** e specificare l'insieme di credenziali delle chiavi, la chiave e la versione della chiave.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per modificare la chiave che protegge un ambito di crittografia da una chiave gestita dal cliente a una chiave gestita da Microsoft con PowerShell, chiamare il comando **Update-AzStorageEncryptionScope** e passare il `-StorageEncryption` parametro:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Chiamare quindi il comando **Update-AzStorageEncryptionScope** e passare i `-KeyUri` `-KeyvaultEncryption` parametri e:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Per modificare la chiave che protegge un ambito di crittografia da una chiave gestita dal cliente a una chiave gestita da Microsoft con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ storage account Encryption-scope Update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) e passare il `--key-source` parametro con il valore `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Chiamare quindi il comando **AZ storage account Encryption-scope Update** , passare il `--key-uri` parametro e passare il `--key-source` parametro con il valore `Microsoft.KeyVault` :

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Disabilitare un ambito di crittografia

Quando un ambito di crittografia è disabilitato, non viene più fatturato. Disabilitare gli ambiti di crittografia non necessari per evitare addebiti superflui. Per altre informazioni, vedere [Crittografia di Archiviazione di Azure per dati inattivi](../common/storage-service-encryption.md).

# <a name="portal"></a>[Portale](#tab/portal)

Per disabilitare un ambito di crittografia nella portale di Azure, passare all'impostazione **ambiti di crittografia** per l'account di archiviazione, selezionare l'ambito di crittografia desiderato e selezionare **Disabilita**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per disabilitare un ambito di crittografia con PowerShell, chiamare il comando Update-AzStorageEncryptionScope e includere il `-State` parametro con il valore `disabled` , come illustrato nell'esempio seguente. Per abilitare di nuovo un ambito di crittografia, chiamare lo stesso comando con il `-State` parametro impostato su `enabled` . Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Per disabilitare un ambito di crittografia con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ storage account Encryption-scope Update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) e includere il `--state` parametro con il valore `Disabled` , come illustrato nell'esempio seguente. Per abilitare di nuovo un ambito di crittografia, chiamare lo stesso comando con il `--state` parametro impostato su `Enabled` . Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

> [!IMPORTANT]
> Non è possibile eliminare un ambito di crittografia. Per evitare costi imprevisti, assicurarsi di disabilitare gli ambiti di crittografia attualmente non necessari.

---

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](../common/storage-service-encryption.md)
- [Ambiti di crittografia per l'archiviazione BLOB](encryption-scope-overview.md)
- [Chiavi gestite dal cliente per la crittografia di archiviazione di Azure](../common/customer-managed-keys-overview.md)