---
title: Gestire le regole del firewall-interfaccia della riga di comando di Azure-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come creare e gestire le regole del firewall in database di Azure per PostgreSQL-server singolo usando la riga di comando dell'interfaccia della riga di comando di Azure.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a559b8c65ab57b0144b807a3b4cc1faa912d430
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422740"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Creare e gestire regole del firewall in database di Azure per PostgreSQL-server singolo con l'interfaccia della riga di comando di Azure
Le regole del firewall a livello di server possono essere usate per gestire l'accesso a un database di Azure per il server PostgreSQL da un indirizzo IP o un intervallo di indirizzi IP specifico. Usando pratici comandi dell'interfaccia della riga di comando di Azure è possibile creare, aggiornare, eliminare, elencare e visualizzare le regole del firewall per gestire il server. Per una panoramica delle regole del firewall di database di Azure per PostgreSQL, vedere [regole del firewall del server database di Azure per PostgreSQL](concepts-firewall-rules.md).

Le regole della rete virtuale (VNet) possono essere usate anche per proteggere l'accesso al server. Altre informazioni sulla [creazione e la gestione di endpoint e regole del servizio rete virtuale con l'interfaccia della](howto-manage-vnet-using-cli.md)riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Installare l'utilità della riga di comando [Azure CLI](/cli/azure/install-azure-cli) o usare Azure Cloud Shell nel browser.
- [Server e database di Database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configurare le regole del firewall di Database di Azure per PostgreSQL
I comandi [az postgres server firewall-rule-](/cli/azure/postgres/server/firewall-rule) vengono usati per configurare le regole del firewall.

## <a name="list-firewall-rules"></a>Elencare le regole del firewall 
Per elencare le regole firewall del server esistenti, eseguire il comando [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule).
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
L'output elenca le eventuali regole firewall presenti, per impostazione predefinita in formato JSON. È possibile usare l'opzione `--output table` per ottenere un formato di tabella più leggibile come output.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Creare la regola del firewall
Per creare una nuova regola del firewall sul server, eseguire il comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). 


Per consentire l'accesso a un singolo indirizzo IP, specificare lo stesso indirizzo in `--start-ip-address` e `--end-ip-address` , come in questo esempio, sostituendo l'IP illustrato qui con l'IP specifico.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Per consentire alle applicazioni da indirizzi IP di Azure di connettersi al server di Database di Azure per PostgreSQL, specificare l'indirizzo IP 0.0.0.0 come indirizzo IP iniziale e indirizzo IP finale, come in questo esempio.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Questa opzione configura il firewall in modo da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, verificare che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall creata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizza invece un messaggio di errore.

## <a name="update-firewall-rule"></a>Aggiornare la regola del firewall 
Aggiornare una regola del firewall esistente sul server usando il comando [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule). Specificare il nome della regola del firewall esistente come input e gli attributi dell'indirizzo IP iniziale e finale per l'aggiornamento.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall aggiornata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizza invece un messaggio di errore.
> [!NOTE]
> Se la regola del firewall non esiste, viene creata dal comando di aggiornamento.

## <a name="show-firewall-rule-details"></a>Visualizzare i dettagli di una regola del firewall
Si può anche visualizzare i dettagli di una regola firewall esistente a livello di server eseguendo il comando [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule).
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall specificata, per impostazione predefinita in formato JSON. Se si verifica un errore, l'output visualizza invece un messaggio di errore.

## <a name="delete-firewall-rule"></a>Eliminare la regola del firewall
Per revocare l'accesso al server per un intervallo IP, eliminare una regola firewall esistente eseguendo il comando [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule). Specificare il nome della regola del firewall esistente.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Al completamento dell'operazione non verrà visualizzato alcun output. In caso di errore, viene restituito il testo di un messaggio di errore.

## <a name="next-steps"></a>Passaggi successivi
- Analogamente, è possibile usare un Web browser per [creare e gestire le regole firewall di Database di Azure per PostgreSQL usando il portale di Azure](howto-manage-firewall-using-portal.md).
- Altre informazioni sulle [regole firewall di Database di Azure per il server PostgreSQL](concepts-firewall-rules.md).
- Proteggere ulteriormente l'accesso al server [creando e gestendo gli endpoint e le regole del servizio rete virtuale usando l'interfaccia della](howto-manage-vnet-using-cli.md)riga di comando di Azure.
- Per informazioni sulla connessione a un database di Azure per il server PostgreSQL, vedere [raccolte di connessioni per database di Azure per PostgreSQL](concepts-connection-libraries.md).
