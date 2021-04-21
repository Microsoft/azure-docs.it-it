---
title: Accedere ai log di query lente - Interfaccia della riga di comando di Azure - Database di Azure per MySQL
description: Questo articolo descrive come accedere ai log di query lente in Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d5fc2b14a655251e59a9209e078b0534f08baf9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763236"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Configurare e accedere ai log di query lente usando l'interfaccia della riga di comando di Azure
È possibile scaricare i log di query lente di Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure, l'utilità della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o Azure Cloud Shell nel browser

## <a name="configure-logging"></a>Configurare la registrazione
Per configurare il server per l'accesso al log delle query lente di MySQL, seguire questa procedura:
1. Attivare la registrazione delle query lente impostando il **parametro del log delle query \_ \_ lente** su ON.
2. Selezionare la posizione in cui eseguire l'output dei log in usando **\_ l'output del log**. Per inviare log sia all'archiviazione locale che Monitoraggio di Azure di diagnostica, selezionare **File**. Per inviare i log solo Monitoraggio di Azure, selezionare **Nessuno**
3. Regolare gli altri parametri, ad esempio **long\_query\_time** e **log\_slow\_admin\_statements**.

Per informazioni su come impostare il valore di questi parametri tramite l'interfaccia della riga di comando di Azure, vedere [Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).

Il comando dell'interfaccia della riga di comando seguente, ad esempio, attiva il log delle query lente, imposta la durata di una query prolungata su 10 secondi e quindi disattiva la registrazione dell'istruzione per un amministratore lento. Infine elenca le opzioni di configurazione da verificare.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Elencare i log per il database di Azure per il server MySQL
Se **log_output** è configurato su "File", è possibile accedere ai log direttamente dall'archiviazione locale del server. Per elencare i file di log di query lente disponibili per il server, eseguire [il comando az mysql server-logs list.](/cli/azure/mysql/server-logs#az_mysql_server_logs_list)

È possibile elencare i file di log per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**. Quindi indirizzare l'elenco dei file di log a un file di testo denominato **log\_files\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Scaricare i log dal server
Se **log_output** è configurato su "File", è possibile scaricare singoli file di log dal server con [il comando az mysql server-logs download.](/cli/azure/mysql/server-logs#az_mysql_server_logs_download)

Usare l'esempio seguente per scaricare il file di log specifico per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup** nell'ambiente locale.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [log di query lente in Database di Azure per MySQL.](concepts-server-logs.md)
