---
title: Creare un'app per le funzioni con la distribuzione da GitHub - Interfaccia della riga di comando di Azure
description: Creare un'app per le funzioni e distribuire il codice di funzione da un repository GitHub usando Funzioni di Azure.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 219e993ad7132c90de6db680facc9b8f815947cc
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934391"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Creare un'app per le funzioni in Azure distribuita da GitHub

Questo script di esempio di Funzioni di Azure crea un'app per le funzioni usando il [Piano a consumo](../consumption-plan.md) con le relative risorse. Lo script configura anche il codice di funzione per la distribuzione continua dal repository GitHub. 

In questo esempio è necessario:

* Un archivio GitHub contenente il codice di funzione per il quale si hanno autorizzazioni amministrative.
* [Token di accesso personale](https://help.github.com/articles/creating-an-access-token-for-command-line-use) per il proprio account GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="sample-script"></a>Script di esempio

Questo esempio crea un'app per le funzioni di Azure e distribuisce il codice di funzione da GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Ogni comando della tabella include collegamenti alla documentazione specifica del comando. Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crea l'account di archiviazione necessario per l'app per le funzioni. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Crea un'app per le funzioni nel [Piano a consumo](../consumption-plan.md) serverless e la associa a un repository Git o Mercurial. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).
