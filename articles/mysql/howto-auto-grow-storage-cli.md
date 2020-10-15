---
title: Espansione automatica dell'archiviazione-interfaccia della riga di comando di Azure-database di Azure per MySQL
description: Questo articolo descrive come abilitare l'aumento automatico delle dimensioni di archiviazione usando l'interfaccia della riga di comando di Azure in database di Azure per MySQL.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 91b4455c9485389f71d42448617668167579f437
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87504280"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Espandere automaticamente l'archiviazione di database di Azure per MySQL usando l'interfaccia della riga di comando di Azure
Questo articolo descrive come configurare l'archiviazione di un database di Azure per il server MySQL in modo che cresca senza compromettere il carico di lavoro.

Il server che [raggiunge il limite di archiviazione](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit)è impostato su sola lettura. Se l'opzione di aumento automatico delle dimensioni di archiviazione è abilitata per i server con archiviazione con provisioning inferiore a 100 GB, le dimensioni di archiviazione di cui è stato effettuato il provisioning vengono aumentate di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o 10% dell'archiviazione con provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, la dimensione di archiviazione con provisioning viene aumentata del 5% quando lo spazio di archiviazione disponibile è inferiore al 5% delle dimensioni di archiviazione con provisioning. Si applicano i limiti di archiviazione massimi come specificato [qui](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) .

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Questa guida dettagliata richiede l'uso dell'interfaccia della riga di comando di Azure 2.0 o versioni successive. Per verificare la versione, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere `az --version`. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="enable-mysql-server-storage-auto-grow"></a>Abilitare l'aumento automatico delle dimensioni di archiviazione del server MySQL

Abilitare l'espansione automatica del server in un server esistente con il comando seguente:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Abilitare l'aumento di dimensioni dell'archiviazione automatica del server durante la creazione di un nuovo server con il comando seguente:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-on-metric.md).
