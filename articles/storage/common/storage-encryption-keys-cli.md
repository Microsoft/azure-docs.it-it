---
title: Usare l'interfaccia della riga di comando di Azure per configurare le chiavi gestite dal cliente
titleSuffix: Azure Storage
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per configurare chiavi gestite dal cliente con Azure Key Vault per la crittografia di archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 893c953562e0d150bd5e8110e5473fd24a2aff83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176346"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Configurare chiavi gestite dal cliente con Azure Key Vault usando l'interfaccia della riga di comando di Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Questo articolo illustra come configurare un Azure Key Vault con chiavi gestite dal cliente usando l'interfaccia della riga di comando di Azure. Per informazioni su come creare un insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando di Azure, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia della](../../key-vault/secrets/quick-create-cli.md)

## <a name="assign-an-identity-to-the-storage-account"></a>Assegnare un'identità all'account di archiviazione

Per abilitare le chiavi gestite dal cliente per l'account di archiviazione, assegnare innanzitutto un'identità gestita assegnata dal sistema all'account di archiviazione. Questa identità gestita verrà usata per concedere le autorizzazioni dell'account di archiviazione per accedere all'insieme di credenziali delle chiavi.

Per assegnare un'identità gestita usando l'interfaccia della riga di comando di Azure, chiamare [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Per altre informazioni sulla configurazione delle identità gestite assegnate dal sistema con l'interfaccia della riga di comando di Azure, vedere [configurare le identità gestite per le risorse di Azure in una VM di Azure usando l'interfaccia](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)della riga di comando

## <a name="create-a-new-key-vault"></a>Creare un nuovo insieme di credenziali delle chiavi

L'insieme di credenziali delle chiavi usato per archiviare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure deve avere due impostazioni di protezione della chiave abilitate, l' **eliminazione** temporanea e non la **ripulitura**. Per creare un nuovo insieme di credenziali delle chiavi usando PowerShell o l'interfaccia della riga di comando di Azure con queste impostazioni abilitate, eseguire i comandi seguenti. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

Per creare un nuovo insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando di Azure, chiamare [AZ Key Vault create](/cli/azure/keyvault#az-keyvault-create) Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Per informazioni su come abilitare **l'eliminazione** temporanea e **non ripulire** in un insieme di credenziali delle chiavi esistente con l'interfaccia della riga di comando di Azure, vedere le sezioni relative all'abilitazione dell' **eliminazione** temporanea e all' **Abilitazione** dell'eliminazione della protezione in [come usare l'eliminazione temporanea con CLI](../../key-vault/general/soft-delete-cli.md).

## <a name="configure-the-key-vault-access-policy"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi

Configurare quindi i criteri di accesso per l'insieme di credenziali delle chiavi in modo che l'account di archiviazione disponga delle autorizzazioni di accesso. In questo passaggio si userà l'identità gestita precedentemente assegnata all'account di archiviazione.

Per impostare i criteri di accesso per l'insieme di credenziali delle chiavi, chiamare [AZ Key Vault set-Policy](/cli/azure/keyvault#az-keyvault-set-policy). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Creare una nuova chiave

Successivamente, creare una chiave nell'insieme di credenziali delle chiavi. Per creare una chiave, chiamare [AZ Key Vault Key create](/cli/azure/keyvault/key#az-keyvault-key-create). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

Con la crittografia di archiviazione di Azure sono supportate solo le chiavi RSA e RSA-HSM a 2048 bit. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurare la crittografia con chiavi gestite dal cliente

Per impostazione predefinita, la crittografia di archiviazione di Azure usa chiavi gestite da Microsoft. Configurare l'account di archiviazione di Azure per le chiavi gestite dal cliente e specificare la chiave da associare all'account di archiviazione.

Per aggiornare le impostazioni di crittografia dell'account di archiviazione, chiamare [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update), come illustrato nell'esempio seguente. Includere il `--encryption-key-source` parametro e impostarlo su `Microsoft.Keyvault` per abilitare le chiavi gestite dal cliente per l'account di archiviazione. Nell'esempio viene anche eseguita una query per l'URI dell'insieme di credenziali delle chiavi e la versione più recente della chiave, entrambi i quali sono necessari per associare la chiave all'account di archiviazione. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, è necessario aggiornare l'account di archiviazione per usare la nuova versione. Per prima cosa, eseguire una query per l'URI dell'insieme di credenziali delle chiavi chiamando [AZ Key Vault Show](/cli/azure/keyvault#az-keyvault-show)e per la versione della chiave chiamando [AZ Key Vault Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Chiamare quindi il comando [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update) per aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione della chiave, come illustrato nella sezione precedente.

## <a name="use-a-different-key"></a>Usare una chiave diversa

Per modificare la chiave usata per la crittografia di archiviazione di Azure, chiamare [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update) come illustrato in [configurare la crittografia con chiavi gestite dal cliente](#configure-encryption-with-customer-managed-keys) e specificare il nome e la versione della nuova chiave. Se la nuova chiave si trova in un insieme di credenziali delle chiavi diverso, aggiornare anche l'URI dell'insieme di credenziali delle chiavi.

## <a name="revoke-customer-managed-keys"></a>Revocare le chiavi gestite dal cliente

Se si ritiene che una chiave possa essere stata compromessa, è possibile revocare le chiavi gestite dal cliente rimuovendo i criteri di accesso di Key Vault. Per revocare una chiave gestita dal cliente, chiamare il comando [AZ Key Vault Delete-Policy](/cli/azure/keyvault#az-keyvault-delete-policy) , come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal cliente

Quando si disabilitano le chiavi gestite dal cliente, l'account di archiviazione viene nuovamente crittografato con le chiavi gestite da Microsoft. Per disabilitare le chiavi gestite dal `--encryption-key-source parameter` cliente, chiamare [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update) e impostare su `Microsoft.Storage`, come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con i propri valori e di usare le variabili definite negli esempi precedenti.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia di archiviazione di Azure per dati inattivi](storage-service-encryption.md) 
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
