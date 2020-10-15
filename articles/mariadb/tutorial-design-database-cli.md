---
title: 'Esercitazione: Progettare un database di Azure per MariaDB - Interfaccia della riga di comando di Azure'
description: In questa esercitazione viene illustrato come creare e gestire il database e il server di Database di Azure per MariaDB tramite l'interfaccia della riga di comando di Azure dalla riga di comando.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f08f9065b2a7361294a2f6257c85be772d0f7119
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87496084"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-azure-cli"></a>Esercitazione: Progettare un'istanza di Database di Azure per MariaDB con l'interfaccia della riga di comando di Azure

Database di Azure per MariaDB è un servizio di database relazionale in Microsoft Cloud basato sul motore di database MariaDB Community Edition. In questa esercitazione, si usano l'interfaccia della riga di comando di Azure e altre utilità per informazioni su come:

> [!div class="checklist"]
> * Creare un database di Azure per MariaDB
> * Configurare il firewall del server
> * Usare lo [strumento della riga di comando di MySQL](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

È possibile usare Azure Cloud Shell nel browser o [installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli) nel computer in uso per eseguire i blocchi di codice di questa esercitazione.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

Se si dispone di più sottoscrizioni, scegliere la sottoscrizione appropriata in cui la risorsa esiste o per cui è configurata. Selezionare un ID di sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account#az-account-set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) con il comando [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

L'esempio seguente consente di creare un gruppo di risorse denominato `myresourcegroup` nell'area `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Creare un database di Azure per un server MariaDB
Creare un server di Database di Azure per MariaDB con il comando `az mariadb server create`. Un server può gestire più database. In genere, viene usato un database separato per ogni progetto o per ogni utente.

L'esempio seguente crea un server di Database di Azure per MariaDB disponibile in `westus` nel gruppo di risorse `myresourcegroup` con nome `mydemoserver`. Il server ha un account di accesso amministratore denominato `myadmin`. Si tratta di un server per utilizzo generico di quinta generazione con due vCore. Sostituire `<server_admin_password>` con il proprio valore.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```
Il valore del parametro sku-name segue la convenzione {piano tariffario}\_{generazione calcolo}\_{vCore} come illustrato nell'esempio seguente:
+ `--sku-name B_Gen5_4` esegue il mapping a Basic, Gen 5 e 4 vCore.
+ `--sku-name GP_Gen5_32` esegue il mapping a utilizzo generico, Gen 5 e 32 vCore.
+ `--sku-name MO_Gen5_2` esegue il mapping a ottimizzazione per la memoria, Gen 5 e 2 vCore.

Vedere la documentazione dei [piani tariffari](./concepts-pricing-tiers.md) per comprendere i valori validi per area e livello.

> [!IMPORTANT]
> L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa guida di avvio rapido. Prendere nota di queste informazioni per usarle in seguito.


## <a name="configure-firewall-rule"></a>Configurare una regola del firewall
Creare una regola del firewall a livello di server di Database di Azure per MariaDB con il comando `az mariadb server firewall-rule create`. Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio lo strumento della riga di comando **mysql** o MySQL Workbench, di connettersi al server tramite il firewall del servizio MariaDB di Azure.

L'esempio seguente crea una regola firewall denominata `AllowMyIP` che consente connessioni da un indirizzo IP specifico, 192.168.0.1. Sostituire con l'indirizzo IP o l'intervallo di indirizzi IP corrispondenti alla posizione da cui si effettuerà la connessione.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.
```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Il risultato è in formato JSON. Annotare il **fullyQualifiedDomainName** e l'**administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Connettersi al server usando mysql
Usare lo [strumento da riga di comando mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) per stabilire una connessione al server di Database di Azure per MariaDB. In questo esempio, il comando è:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Creazione di un database vuoto
Una volta connessi al server, creare un database vuoto.
```sql
mysql> CREATE DATABASE mysampledb;
```

Nel prompt, eseguire il comando seguente per cambiare la connessione nel database appena creato:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Creare tabelle nel database
Dopo aver appreso come connettersi al database di Database di Azure per MariaDB, completare alcune attività di base.

In primo luogo, creare una tabella e caricarvi alcuni dati. Creare una tabella che contenga le informazioni riguardanti l'inventario.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Caricare i dati nelle tabelle
Dopo aver creato una tabella, inserire alcuni dati. Nella finestra del prompt dei comandi aperta, eseguire la query seguente per inserire alcune righe di dati.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

A questo punto, ci sono due righe di dati di esempio nella tabella creata in precedenza.

## <a name="query-and-update-the-data-in-the-tables"></a>Eseguire una query e aggiornare i dati nelle tabelle
Eseguire la query seguente per recuperare informazioni dalla tabella del database.
```sql
SELECT * FROM inventory;
```

Si possono anche aggiornare query e aggiornare i dati nelle tabelle.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La riga viene aggiornata di conseguenza quando si recuperano dati.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Ripristinare un database a un momento precedente
Si supponga di aver eliminato accidentalmente questa tabella. Si tratta di un elemento che non è facile da ripristinare. Database di Azure per MariaDB consente di tornare in qualsiasi punto degli ultimi 35 giorni e di ripristinare questo punto nel tempo in un nuovo server. È possibile usare questo nuovo server per ripristinare i dati eliminati. La procedura seguente consente di ripristinare il server di esempio in un punto precedente all'aggiunta della tabella.

Per il ripristino sono necessarie le informazioni seguenti:

- Punto di ripristino: selezionare un punto nel tempo precedente la modifica del server. Il punto deve essere maggiore o equivalente al valore del backup meno recente del database di origine.
- Server di destinazione: specificare il nome del nuovo server in cui eseguire il ripristino
- Server di origine: specificare il nome del server da cui eseguire il ripristino
- Percorso: non è possibile selezionare l'area, per impostazione predefinita è la stessa del server di origine

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

Il comando `az mariadb server restore` richiede i parametri seguenti:

| Impostazione | Valore consigliato | Descrizione  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Il gruppo di risorse in cui si trova il server di origine.  |
| name | mydemoserver-restored | Il nome del nuovo server creato con il comando di ripristino. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Selezionare un punto di ripristino temporizzato. La data e l'ora devono trovarsi all'interno del periodo di memorizzazione dei backup del server di origine. Usare il formato ISO8601 per la data e l'ora. È possibile usare il fuso orario locale, ad esempio `2017-04-13T05:59:00-08:00`, o il formato UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Il nome o l'ID del server di origine da cui eseguire il ripristino. |

Il ripristino di un server in un punto nel tempo crea un nuovo server, ovvero una copia del server originale nel momento specificato. I valori relativi al percorso e al piano tariffario del server ripristinato sono gli stessi del server di origine.

Il comando è sincrono e il risultato verrà restituito dopo il ripristino del server. Una volta terminato il ripristino, individuare il nuovo server creato. Verificare che i dati siano stati ripristinati come previsto.

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:
> [!div class="checklist"]
> * Creare un database di Azure per un server MariaDB
> * Configurare il firewall del server
> * Usare lo [strumento della riga di comando di MySQL](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati
