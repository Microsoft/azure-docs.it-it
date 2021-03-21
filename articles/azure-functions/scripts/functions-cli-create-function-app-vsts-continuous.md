---
title: Creare un'app per le funzioni con la distribuzione da DevOps - Interfaccia della riga di comando di Azure
description: Creare un'app per le funzioni e distribuire il codice di funzione da Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f89da9fc146d753442f2a8c8aa38861e66c9a3d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934375"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Creare una funzione in Azure distribuita da Azure DevOps

Questo argomento illustra come usare Funzioni di Azure per creare un'app per le funzioni [serverless](https://azure.microsoft.com/solutions/serverless/) che usa il [Piano a consumo](../consumption-plan.md). L'app per le funzioni, che rappresenta un contenitore per le funzioni, viene distribuita in modo continuo da un repository di Azure DevOps. 

Per completare questo argomento sono necessari:

* Un repository di Azure DevOps che contiene il progetto dell'app per le funzioni e per cui si dispone delle autorizzazioni amministrative.
* Un [token di accesso personale](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) per accedere al repository di Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="sample-script"></a>Script di esempio

Questo esempio crea un'app per le funzioni di Azure e distribuisce il codice di funzione da Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, un account di archiviazione, un'app per le funzioni e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crea l'account di archiviazione necessario per l'app per le funzioni. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Crea un'app per le funzioni nel [Piano a consumo](../consumption-plan.md) serverless. |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Associa un'app per le funzioni con un archivio Git o Mercurial. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).
