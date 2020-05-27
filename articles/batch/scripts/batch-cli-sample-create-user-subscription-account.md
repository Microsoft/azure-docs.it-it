---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un account Batch - Sottoscrizione utente
description: Questo script crea un account Azure Batch in modalità sottoscrizione utente. Questo account alloca i nodi di calcolo nella sottoscrizione.
ms.topic: sample
ms.date: 01/29/2018
ms.openlocfilehash: 9f3919574dd7dcbe932c71dfcea2ac52ec906c6c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681878"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>Esempio di interfaccia della riga di comando: creare un account Batch in modalità sottoscrizione utente

Questo script crea un account Azure Batch in modalità sottoscrizione utente. Un account che alloca nodi di calcolo nella sottoscrizione deve essere autenticato tramite un token di Azure Active Directory. I nodi di calcolo allocati vengono conteggiati nella quota di memoria centrale vCPU della sottoscrizione. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.20 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az role assignment create](/cli/azure/role) | Crea una nuova assegnazione di ruolo per un utente, un gruppo o un'entità servizio. |
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-create) | Crea un insieme di credenziali chiave. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-set-policy) | Aggiornare i criteri di sicurezza dell'insieme di credenziali delle chiavi specificato. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Crea l'account Batch.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Effettua l'autenticazione con l'account Batch specificato per un'ulteriore interazione con l'interfaccia della riga di comando.  |
| [az group delete](/cli/azure/group#az-group-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
