---
title: Creare un'app per le funzioni con archiviazione connessa - Interfaccia della riga di comando di Azure
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una funzione di Azure che si connette ad Archiviazione di Azure
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 539c3a7dd95045b2e569dbb339be0e5a0c845902
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786285"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Creare un'app per le funzioni con una connessione a un account di archiviazione specificato 

Questo script di esempio di Funzioni di Azure crea un'app per le funzioni e connette la funzione a un account di archiviazione di Azure. L'impostazione dell'app creata che contiene la connessione può essere usata con un [trigger o un binding di archiviazione](../functions-bindings-storage-blob.md). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="sample-script"></a>Script di esempio

In questo esempio si crea un'app per le funzioni di Azure e si aggiunge la stringa di connessione di archiviazione a un'impostazione dell'app.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un gruppo di risorse con una località. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Creare un account di archiviazione. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Crea un'app per le funzioni nel [Piano a consumo](../consumption-plan.md) serverless. |
| [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string) | Ottiene la stringa di connessione per l'account. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | Imposta la stringa di connessione come impostazione dell'app nell'app per le funzioni. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).
