---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un account Batch - Servizio Batch
description: Questo script crea un account Azure Batch in modalità servizio Batch e mostra come eseguire query o aggiornamenti su varie proprietà dell'account.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3d978cf56e92abf9824c116e51f94d53bbe14bbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768329"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>Esempio dell'interfaccia della riga di comando: creare un account Batch in modalità servizio Batch

Questo script crea un account Azure Batch in modalità servizio Batch e mostra come eseguire query o aggiornamenti su varie proprietà dell'account. Quando si crea un account Batch in modalità servizio Batch predefinita, i nodi di calcolo vengono assegnati internamente dal servizio Batch. I nodi di calcolo allocati sono soggetti a una quota di memoria centrale vCPU separata e l'account può essere autenticato tramite credenziali con chiave condivisa o un token di Azure Active Directory.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Per questa esercitazione è necessaria la versione 2.0.20 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Crea l'account Batch. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Crea un account di archiviazione. |
| [az batch account set](/cli/azure/batch/account#az_batch_account_set) | Aggiorna le proprietà dell'account Batch.  |
| [az batch account show](/cli/azure/batch/account#az_batch_account_show) | Recupera i dettagli dell'account Batch specificato.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az_batch_account_keys_list) | Recupera le chiavi d'accesso dell'account Batch specificato.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Effettua l'autenticazione con l'account Batch specificato per un'ulteriore interazione con l'interfaccia della riga di comando.  |
| [az group delete](/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
