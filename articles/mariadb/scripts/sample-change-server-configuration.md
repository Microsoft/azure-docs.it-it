---
title: Script dell'interfaccia della riga di comando - Modificare i parametri del server - Database di Azure per MariaDB
description: Questo script dell'interfaccia della riga di comando di esempio elenca tutti gli aggiornamenti e le configurazioni del server disponibili di un'istanza di Database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: ce27dc3008803faf548c10f4fc2ab3b579af2ba0
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90525032"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Elencare e aggiornare le configurazioni di un server di Database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure
Questo script dell'interfaccia della riga di comando di esempio elenca tutti i parametri di configurazione disponibili, nonché i relativi valori consentiti per il server di Database di Azure per MariaDB e imposta *innodb_lock_wait_timeout* su un valore diverso da quello predefinito.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di eseguire l'interfaccia della riga di comando in locale, per questo articolo è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Controllare la versione eseguendo `az --version`. Per installare l'interfaccia della riga di comando di Azure o aggiornarne la versione, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio
In questo script di esempio modificare le righe evidenziate per aggiornare il nome utente e la password amministratore con i propri valori.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Crea un server MariaDB che ospita i database. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Elencare le configurazioni di un server Database di Azure per MariaDB. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Aggiornare la configurazione di un server Database di Azure per MariaDB. |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Mostrare la configurazione di un server Database di Azure per MariaDB. |
| [az group delete](/cli/azure/group#az-group-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'interfaccia della riga di comando di Azure: [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
- Provare altri script: [Esempi dell'interfaccia della riga di comando di Azure per Database di Azure per MariaDB](../sample-scripts-azure-cli.md)
- Per altre informazioni sui parametri di server, vedere [Come configurare i parametri del server nel Database di Azure per MariaDB](../howto-server-parameters.md).
