---
title: 'Avvio rapido: Creare un server - az postgres up - Database di Azure per PostgreSQL - Server singolo'
description: Guida di avvio rapido per creare un server singolo Database di Azure per PostgreSQL con un comando dell'interfaccia della riga di comando di Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: c11a98c04f81e7c111faf4aa449ab2bf9a4890bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92485223"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Avvio rapido: Usare un comando dell'interfaccia della riga di comando di Azure, az postgres up (anteprima), per creare un'istanza di server singolo Database di Azure per PostgreSQL

> [!IMPORTANT]
> Il comando [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) dell'interfaccia della riga di comando di Azure è disponibile in anteprima.

Il database di Azure per PostgreSQL è un servizio gestito che consente di eseguire, gestire e ridimensionare database PostgreSQL a disponibilità elevata nel cloud. L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida di avvio rapido illustra come usare il comando [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) per creare un server Database di Azure per PostgreSQL con l'interfaccia della riga di comando di Azure. Oltre a creare il server, il comando `az postgres up` crea un database di esempio e un utente root nel database, apre il firewall per i servizi di Azure e crea regole del firewall predefinite per il computer client. Con queste impostazione predefinite il processo di sviluppo risulta più rapido.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Per questo articolo è necessario eseguire in locale l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

È necessario accedere all'account con il comando [az login](/cli/azure/authenticate-azure-cli). Si noti la proprietà **ID** dell'output del comando per il nome della sottoscrizione corrispondente.

```azurecli
az login
```

Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Selezionare l'ID sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account). Sostituire il segnaposto "subscription ID" con la proprietà **subscription ID** dell'output di **az login** per la sottoscrizione.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Per usare i comandi, installare l'estensione [db-up](/cli/azure/ext/db-up). Se viene restituito un errore, verificare di aver installato l'ultima versione dell'interfaccia della riga di comando di Azure. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Creare un server Database di Azure per PostgreSQL con il comando seguente:

```azurecli
az postgres up
```

Il server viene creato con i valori predefiniti seguenti, a meno che non si sostituiscano manualmente:

**Impostazione** | **Valore predefinito** | **Descrizione**
---|---|---
server-name | Generata dal sistema | Nome univoco per identificare il database di Azure per il server PostgreSQL.
resource-group | Generata dal sistema | Nuovo gruppo di risorse di Azure.
sku-name | GP_Gen5_2 | Il nome dello SKU. Segue la convenzione {piano tariffario}\_{generazione di calcolo}\_{Vcore} in sintassi abbreviata. L'impostazione predefinita è un server per utilizzo generico di quinta generazione con 2 vCore. Per altre informazioni sui livelli, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/postgresql/).
backup-retention | 7 | Per quanto tempo viene conservata una copia di backup. L'unità è giorni.
geo-redundant-backup | Disabled | Indica se abilitare i backup con ridondanza geografica per questo server.
posizione | westus2 | Località di Azure per il server.
ssl-enforcement | Disabled | Indica se TLS/SSL deve essere abilitato o meno per questo server.
storage-size | 5120 | Capacità di archiviazione del server (l'unità è MB).
version | 10 | La versione principale di PostgreSQL.
admin-user | Generata dal sistema | Nome utente per l'amministratore.
admin-password | Generata dal sistema | Password dell'utente amministratore.

> [!NOTE]
> Per altre informazioni sul comando `az postgres up` e i relativi parametri aggiuntivi, vedere la [documentazione dell'interfaccia della riga di comando di Azure](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

Dopo la creazione del server, vengono applicate le impostazioni seguenti:

- Viene creata una regola del firewall denominata "devbox". L'interfaccia della riga di comando di Azure prova a rilevare l'indirizzo IP del computer da cui viene eseguito il comando `az postgres up` e consente tale indirizzo IP.
- Viene attivata l'opzione "Consenti l'accesso a Servizi di Azure". Questa impostazione configura il firewall del server in modo da accettare connessioni da tutte le risorse di Azure, incluse le risorse non incluse nella sottoscrizione dell'utente.
- Viene creato un database vuoto denominato "sampledb".
- Viene creato un nuovo utente denominato "root" con privilegi per "sampledb".

> [!NOTE]
> Database di Azure per PostgreSQL comunica sulla porta 5432. Quando si esegue la connessione da una rete aziendale, il traffico in uscita sulla porta 5432 potrebbe non essere consentito dal firewall della rete. Richiedere al reparto IT di aprire la porta 5432 per la connessione al server.

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Al termine del comando `az postgres up`, viene restituito un elenco di stringhe di connessione per i linguaggi di programmazione più diffusi. Queste stringhe di connessione sono preconfigurate con gli attributi specifici del server Database di Azure per PostgreSQL appena creato.

È possibile visualizzare di nuovo l'elenco di queste stringhe di connessione usando il comando [az postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string).

## <a name="clean-up-resources"></a>Pulire le risorse

Eseguire la pulizia di tutte le risorse create nell'avvio rapido con il comando seguente. Questo comando elimina il server Database di Azure per PostgreSQL e il gruppo di risorse.

```azurecli
az postgres down --delete-group
```

Se si vuole eliminare solo il server appena creato, è possibile eseguire [az postgres down](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down).

```azurecli
az postgres down
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md)
