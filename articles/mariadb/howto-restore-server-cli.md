---
title: Backup e ripristino-interfaccia della riga di comando di Azure-database di Azure per MariaDB
description: Informazioni su come eseguire la procedura di backup e ripristino di un server in Database di Azure per MariaDB tramite l'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.openlocfilehash: 3b1eb164d367aa968b599c6d3e92c48fd94b0098
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118714"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-cli"></a>Come eseguire la procedura di backup e ripristino di un server in Database di Azure per MariaDB tramite l'interfaccia della riga di comando di Azure

Il backup dei server Database di Azure per MariaDB viene eseguito periodicamente per abilitare le funzionalità di ripristino. L'uso di questa funzionalità consente di ripristinare il server e tutti i suoi database a un momento precedente nel nuovo server.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Un [Database di Azure per server MariaDB e database](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Questa guida dettagliata richiede l'uso dell'interfaccia della riga di comando di Azure 2.0 o versioni successive. Per verificare la versione, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere `az --version`. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Impostare la configurazione del backup

La decisione riguardo alla configurazione del server per il backup con ridondanza locale o il backup con ridondanza geografica viene presa al momento della creazione del server.

> [!NOTE]
> Dopo aver creato il server, il tipo di ridondanza (locale o geografica) non può essere modificato.
>

Durante la creazione di un server tramite il comando `az mariadb server create`, il parametro `--geo-redundant-backup` definisce l'opzione di ridondanza del backup. Se il valore è `Enabled`, vengono eseguiti backup con ridondanza geografica. Se invece il valore è `Disabled`, vengono eseguiti backup con ridondanza locale.

Il periodo di conservazione dei backup è specificato dal parametro `--backup-retention`.

Per altre informazioni sull'impostazione di questi valori durante la creazione, vedere la [guida introduttiva sull'interfaccia della riga di comando del server Database di Azure per MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

È possibile modificare il periodo di conservazione dei backup di un server nel modo seguente:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

L'esempio precedente modifica il periodo di conservazione dei backup di mydemoserver impostandolo su 10 giorni.

Il periodo di conservazione dei backup determina quanto è possibile tornare indietro nel tempo con un ripristino temporizzato, essendo il ripristino basato sui backup disponibili. Il ripristino temporizzato è descritto in modo più dettagliato nella sezione seguente.

## <a name="server-point-in-time-restore"></a>Ripristino temporizzato del server

È possibile ripristinare il server in base a una temporizzazione precedente. I dati ripristinati vengono copiati in un nuovo server e il server esistente viene lasciato invariato. Se, ad esempio, una tabella è stata involontariamente eliminata a mezzogiorno di oggi, è possibile eseguire il ripristino a un qualsiasi momento prima di mezzogiorno. È possibile quindi recuperare la tabella e i dati mancanti dalla copia ripristinata del server.

Per ripristinare il server, usare il comando [az mariadb server restore](/cli/azure/mariadb/server#az-mariadb-server-restore) dell'interfaccia della riga di comando di Azure.

### <a name="run-the-restore-command"></a>Eseguire il comando di ripristino

Per ripristinare il server, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere il comando seguente:

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Il comando `az mariadb server restore` richiede i parametri seguenti:

| Impostazione | Valore consigliato | Descrizione  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Il gruppo di risorse in cui si trova il server di origine.  |
| name | mydemoserver-restored | Il nome del nuovo server creato con il comando di ripristino. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Selezionare un punto nel tempo per il ripristino. La data e l'ora devono trovarsi all'interno del periodo di memorizzazione dei backup del server di origine. Usare il formato ISO8601 per la data e l'ora. È possibile usare il proprio fuso orario locale, ad esempio `2018-03-13T05:59:00-08:00`. È anche possibile usare il formato UTC Zulu, ad esempio `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Il nome o l'ID del server di origine da cui eseguire il ripristino. |

SSE si ripristina un server a un punto precedente nel tempo, viene creato un nuovo server. Il server originale e i database dal punto nel punto specificato vengono copiati nel nuovo server.

I valori relativi al percorso e al piano tariffario per il server ripristinato sono gli stessi del server di origine. 

Al termine del ripristino, individuare il nuovo server creato per verificare che il ripristino dei dati sia avvenuto come previsto. Il nuovo server ha lo stesso nome di accesso dell'amministratore del server e la stessa password validi per il server esistente nel momento in cui è stato avviato il ripristino. È possibile modificare la password dalla pagina **Panoramica** del nuovo server.

Il nuovo server creato durante un ripristino non include gli endpoint servizio di rete virtuale presenti nel server originale. Per il nuovo server, queste regole devono essere impostate separatamente. Vengono ripristinate le regole del firewall del server originale.

## <a name="geo-restore"></a>Ripristino geografico

Se il server è stato configurato per backup con ridondanza geografica, è possibile creare un nuovo server dal backup di quel server esistente. Questo nuovo server può essere creato in qualsiasi area in cui è disponibile Database di Azure per MariaDB.  

Per creare un server tramite un backup con ridondanza geografica, usare il comando `az mariadb server georestore` dell'interfaccia della riga di comando di Azure.

> [!NOTE]
> Quando un server viene creato per la prima volta, potrebbe non essere subito disponibile per il ripristino geografico. Potrebbero essere necessarie alcune ore per popolare i metadati necessari.
>

Per eseguire un ripristino geografico del server, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere il comando seguente:

```azurecli-interactive
az mariadb server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8
```

Questo comando crea un nuovo server denominato *mydemoserver-georestored* negli Stati Uniti orientali che apparterrà a *myresourcegroup*. Si tratta di un server per utilizzo generico di quinta generazione con otto vCore. Il server viene creato dal backup con ridondanza geografica di *mydemoserver*, che si trova anch'esso nel gruppo di risorse *myresourcegroup*.

Se si vuole creare il nuovo server in un gruppo di risorse diverso dal server esistente, nel parametro `--source-server` è necessario specificare il nome del server come nell'esempio seguente:

```azurecli-interactive
az mariadb server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMariaDB/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

Il comando `az mariadb server georestore` richiede i parametri seguenti:

| Impostazione | Valore consigliato | Descrizione  |
| --- | --- | --- |
|resource-group| myresourcegroup | Nome del gruppo di risorse cui apparterrà il nuovo server.|
|name | mydemoserver-georestored | Nome del nuovo server. |
|source-server | mydemoserver | Nome del server esistente di cui vengono usati i backup con ridondanza geografica. |
|posizione | eastus | Posizione del nuovo server. |
|sku-name| GP_Gen5_8 | Questo parametro imposta il piano tariffario, la generazione delle risorse di calcolo e il numero di vCore del nuovo server. GP_Gen5_8 indica un server per utilizzo generico di quinta generazione con otto vCore.|

Quando si crea un nuovo server tramite un ripristino geografico, il server eredita le stesse dimensioni di archiviazione e lo stesso piano tariffario del server di origine. Questi valori non possono essere modificati durante la creazione. Dopo aver creato il nuovo server, le dimensioni di archiviazione possono essere aumentate.

Al termine del ripristino, individuare il nuovo server creato per verificare che il ripristino dei dati sia avvenuto come previsto. Il nuovo server ha lo stesso nome di accesso dell'amministratore del server e la stessa password validi per il server esistente nel momento in cui è stato avviato il ripristino. È possibile modificare la password dalla pagina **Panoramica** del nuovo server.

Il nuovo server creato durante un ripristino non include gli endpoint servizio di rete virtuale presenti nel server originale. Per il nuovo server, queste regole devono essere impostate separatamente. Vengono ripristinate le regole del firewall del server originale.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [backup](concepts-backup.md) del servizio
- Informazioni sulle [repliche](concepts-read-replicas.md)
- Altre informazioni sulle opzioni di [continuità aziendale](concepts-business-continuity.md)
