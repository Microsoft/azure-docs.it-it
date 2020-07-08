---
title: Creazione di un account che supporta chiavi gestite dal cliente per tabelle e code
titleSuffix: Azure Storage
description: Informazioni su come creare un account di archiviazione che supporta la configurazione delle chiavi gestite dal cliente per tabelle e code. Usare l'interfaccia della riga di comando di Azure o un modello di Azure Resource Manager per creare un account di archiviazione che si basa sulla chiave di crittografia dell'account per la crittografia di archiviazione di Azure. È quindi possibile configurare le chiavi gestite dal cliente per l'account.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 7e47440a54d733d0b5d849123633bf7e067fcd81
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84805704"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Creazione di un account che supporta chiavi gestite dal cliente per tabelle e code

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, l'archiviazione delle code e l'archiviazione tabelle usano una chiave con ambito limitato al servizio e gestita da Microsoft. È inoltre possibile scegliere di utilizzare chiavi gestite dal cliente per crittografare i dati della coda o della tabella. Per usare chiavi gestite dal cliente con code e tabelle, è necessario creare prima un account di archiviazione che usi una chiave di crittografia con ambito per l'account anziché per il servizio. Dopo aver creato un account che usa la chiave di crittografia dell'account per i dati della coda e della tabella, è possibile configurare le chiavi gestite dal cliente con Azure Key Vault per tale account di archiviazione.

Questo articolo descrive come creare un account di archiviazione che si basa su una chiave con ambito definito per l'account. Quando l'account viene creato per la prima volta, Microsoft usa la chiave dell'account per crittografare i dati nell'account e Microsoft gestisce la chiave. È quindi possibile configurare le chiavi gestite dal cliente per l'account per sfruttare i vantaggi offerti, inclusa la possibilità di fornire chiavi personalizzate, aggiornare la versione della chiave, ruotare le chiavi e revocare i controlli di accesso.

## <a name="about-the-feature"></a>Informazioni sulla funzionalità

Per creare un account di archiviazione che si basa sulla chiave di crittografia dell'account per l'archiviazione di code e tabelle, è necessario prima registrarsi per usare questa funzionalità con Azure. A causa della capacità limitata, tenere presente che l'approvazione delle richieste di accesso potrebbe richiedere alcuni mesi.

È possibile creare un account di archiviazione che si basa sulla chiave di crittografia dell'account per l'archiviazione delle code e tabelle nelle aree seguenti:

- Stati Uniti orientali
- Stati Uniti centro-meridionali
- Stati Uniti occidentali 2  

### <a name="register-to-use-the-account-encryption-key"></a>Eseguire la registrazione per usare la chiave di crittografia dell'account

Per eseguire la registrazione per usare la chiave di crittografia dell'account con l'archiviazione code o tabelle, usare PowerShell o l'interfaccia della riga di comando di Azure

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per eseguire la registrazione con PowerShell, chiamare il comando [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire la registrazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

### <a name="check-the-status-of-your-registration"></a>Verificare lo stato della registrazione

Per verificare lo stato della registrazione per la coda o l'archiviazione tabelle, usare PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per verificare lo stato della registrazione con PowerShell, chiamare il comando [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per verificare lo stato della registrazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Registrare di nuovo il provider di risorse di archiviazione di Azure

Dopo che la registrazione è stata approvata, è necessario registrare di nuovo il provider di risorse di archiviazione di Azure. Usare PowerShell o l'interfaccia della riga di comando di Azure per registrare nuovamente il provider di risorse.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per registrare di nuovo il provider di risorse con PowerShell, chiamare il comando [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per registrare di nuovo il provider di risorse con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Creare un account che usa la chiave di crittografia dell'account

È necessario configurare un nuovo account di archiviazione per usare la chiave di crittografia dell'account per le code e le tabelle al momento della creazione dell'account di archiviazione. L'ambito della chiave di crittografia non può essere modificato dopo la creazione dell'account.

L'account di archiviazione deve essere di tipo generico V2. È possibile creare l'account di archiviazione e configurarlo in modo che si basi sulla chiave di crittografia dell'account usando l'interfaccia della riga di comando di Azure o un modello di Azure Resource Manager.

> [!NOTE]
> Quando viene creato l'account di archiviazione, è possibile configurare facoltativamente solo archiviazione code e tabelle per crittografare i dati con la chiave di crittografia dell'account. Archiviazione BLOB e File di Azure utilizzano sempre la chiave di crittografia dell'account per crittografare i dati.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per usare PowerShell per creare un account di archiviazione che si basa sulla chiave di crittografia dell'account, verificare di aver installato il modulo Azure PowerShell, versione 3.4.0 o successiva. Per altre informazioni, vedere [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

Successivamente, creare un account di archiviazione per utilizzo generico V2 chiamando il comando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) con i parametri appropriati:

- Includere l' `-EncryptionKeyTypeForQueue` opzione e impostarne il valore su `Account` per usare la chiave di crittografia dell'account per crittografare i dati nell'archivio code.
- Includere l' `-EncryptionKeyTypeForTable` opzione e impostarne il valore su `Account` per usare la chiave di crittografia dell'account per crittografare i dati nell'archivio tabelle.

L'esempio seguente illustra come creare un account di archiviazione per utilizzo generico V2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e che usa la chiave di crittografia dell'account per crittografare i dati sia per l'archiviazione code che per quella tabella. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per usare l'interfaccia della riga di comando di Azure per creare un account di archiviazione che si basa sulla chiave di crittografia dell'account, verificare di aver installato l'interfaccia della riga di comando di Azure versione 2.0.80 o successiva. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Successivamente, creare un account di archiviazione per utilizzo generico V2 chiamando il comando [AZ storage account create](/cli/azure/storage/account#az-storage-account-create) con i parametri appropriati:

- Includere l' `--encryption-key-type-for-queue` opzione e impostarne il valore su `Account` per usare la chiave di crittografia dell'account per crittografare i dati nell'archivio code.
- Includere l' `--encryption-key-type-for-table` opzione e impostarne il valore su `Account` per usare la chiave di crittografia dell'account per crittografare i dati nell'archivio tabelle.

L'esempio seguente illustra come creare un account di archiviazione per utilizzo generico V2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e che usa la chiave di crittografia dell'account per crittografare i dati sia per l'archiviazione code che per quella tabella. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Modello](#tab/template)

Nell'esempio JSON seguente viene creato un account di archiviazione per utilizzo generico V2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) che usa la chiave di crittografia dell'account per crittografare i dati sia per l'archiviazione code che per quella di tabella. Ricordarsi di sostituire i valori segnaposto tra parentesi angolari con valori personalizzati:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Dopo aver creato un account che si basa sulla chiave di crittografia dell'account, vedere uno degli articoli seguenti per configurare le chiavi gestite dal cliente con Azure Key Vault:

- [Configurare chiavi gestite dal cliente con Azure Key Vault usando il portale di Azure](storage-encryption-keys-portal.md)
- [Configurare chiavi gestite dal cliente con Azure Key Vault tramite PowerShell](storage-encryption-keys-powershell.md)
- [Configurare chiavi gestite dal cliente con Azure Key Vault usando l'interfaccia della riga di comando di Azure](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Verificare la chiave di crittografia dell'account

Per verificare che un servizio in un account di archiviazione usi la chiave di crittografia dell'account, chiamare l'interfaccia della riga di comando di Azure [AZ storage account](/cli/azure/storage/account#az-storage-account-show) Command. Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Cercare il `keyType` campo per ogni servizio all'interno della proprietà di crittografia e verificare che sia impostato su `Account` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per verificare che un servizio in un account di archiviazione usi la chiave di crittografia dell'account, chiamare il comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) . Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Cercare il `KeyType` campo per ogni servizio all'interno della `Encryption` proprietà e verificare che sia impostato su `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per verificare che un servizio in un account di archiviazione usi la chiave di crittografia dell'account, chiamare il comando [AZ storage account](/cli/azure/storage/account#az-storage-account-show) . Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Cercare il `keyType` campo per ogni servizio all'interno della proprietà di crittografia e verificare che sia impostato su `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md) 
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
