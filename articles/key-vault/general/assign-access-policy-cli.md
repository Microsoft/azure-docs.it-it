---
title: Assegnare un criterio di accesso Azure Key Vault (CLI)
description: Come usare l'interfaccia della riga di comando di Azure per assegnare un criterio di accesso Key Vault a un'entità servizio o un'identità dell'applicazione.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 0c7910ac149c8de43eeac92913a0d314fcc1854e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934578"
---
# <a name="assign-a-key-vault-access-policy"></a>Assegnare un criterio di accesso Key Vault

Un criterio di accesso Key Vault determina se una determinata entità servizio, ovvero un'applicazione o un gruppo di utenti, può eseguire diverse operazioni su Key Vault [segreti](../secrets/index.yml), [chiavi](../keys/index.yml)e [certificati](../certificates/index.yml). È possibile assegnare i criteri di accesso usando il [portale di Azure](assign-access-policy-portal.md), l'interfaccia della riga di comando di Azure (questo articolo) o [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Per altre informazioni sulla creazione di gruppi in Azure Active Directory usando l'interfaccia della riga di comando di Azure, vedere [AZ ad Group create](/cli/azure/ad/group#az-ad-group-create) e [AZ ad Group member Add](/cli/azure/ad/group/member#az-ad-group-member-add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Configurare l'interfaccia della riga di comando di Azure ed accedere

1. Per eseguire i comandi di Azure CLI localmente, installare l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli).
 
    Per eseguire i comandi direttamente nel cloud, usare il [Azure cloud Shell](../../cloud-shell/overview.md).

1. Solo interfaccia della riga di comando locale: accedere ad Azure usando `az login` :

    ```bash
    az login
    ```

    Il `az login` comando apre una finestra del browser per raccogliere le credenziali, se necessario.

## <a name="acquire-the-object-id"></a>Acquisire l'ID oggetto

Determinare l'ID oggetto dell'applicazione, del gruppo o dell'utente a cui si desidera assegnare i criteri di accesso:

- Applicazioni e altre entità servizio: usare il comando [AZ ad SP list](/cli/azure/ad/sp#az-ad-sp-list) per recuperare le entità servizio. Esaminare l'output del comando per determinare l'ID oggetto dell'entità di sicurezza a cui si desidera assegnare i criteri di accesso.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Gruppi: usare il comando [AZ ad Group List](/cli/azure/ad/group#az-ad-group-list) , filtrando i risultati con il `--display-name` parametro:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Utenti: usare il comando [AZ ad User Show](/cli/azure/ad/user#az-ad-user-show) , passando l'indirizzo di posta elettronica dell'utente nel `--id` parametro:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Assegnare i criteri di accesso
    
Usare il comando [AZ Vault set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) per assegnare le autorizzazioni desiderate:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Sostituire `<object-id>` con l'ID oggetto dell'entità servizio.

È necessario includere `--secret-permissions` , `--key-permissions` e solo quando si `--certificate-permissions` assegnano autorizzazioni a tali tipi particolari. I valori consentiti per `<secret-permissions>` , `<key-permissions>` e `<certificate-permissions>` sono specificati nella documentazione [AZ di Vault set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) .

## <a name="next-steps"></a>Passaggi successivi

- [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](security-overview.md#identity-management)
- [Proteggere l'](secure-your-key-vault.md)insieme di credenziali delle chiavi.
- [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)