---
title: Aumentare automaticamente lo spazio di archiviazione - Interfaccia della riga di comando di Azure - Database di Azure per MySQL
description: Questo articolo descrive come abilitare l'estensione automatica dello spazio di archiviazione usando l'interfaccia della riga di comando di Azure in Database di Azure per MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ae22a80829ecaaff84c308ec9059d398b1ccbfb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365060"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Aumentare automaticamente le dimensioni dell'archiviazione di Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure
Questo articolo descrive come configurare l'aumento delle dimensioni dell'archiviazione del server di Database di Azure per MySQL senza alcun impatto sul carico di lavoro.

Il server [che raggiunge il limite di archiviazione](./concepts-pricing-tiers.md#reaching-the-storage-limit)è impostato su sola lettura. Se l'aumento automatico dello spazio di archiviazione è abilitato per i server con meno di 100 GB di spazio di archiviazione con provisioning, le dimensioni di archiviazione di cui è stato effettuato il provisioning vengono aumentate di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o al 10% dello spazio di archiviazione di cui è stato effettuato il provisioning. Per i server con più di 100 GB di spazio di archiviazione con provisioning, le dimensioni di archiviazione di cui è stato effettuato il provisioning aumentano del 5% quando lo spazio di archiviazione disponibile è inferiore a 10 GB delle dimensioni di archiviazione di cui è stato effettuato il provisioning. Si applicano i limiti di archiviazione massimi [specificati qui.](./concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica:

- È necessario un [database di Azure per il server MySQL.](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Questo articolo richiede la versione 2.0 dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="enable-mysql-server-storage-auto-grow"></a>Abilitare la crescita automatica dell'archiviazione del server MySQL

Abilitare l'aumentare automatico delle dimensioni dello spazio di archiviazione del server in un server esistente con il comando seguente:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Abilitare l'aumentare automatico dello spazio di archiviazione del server durante la creazione di un nuovo server con il comando seguente:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi sulle metriche.](howto-alert-on-metric.md)
