---
title: Usare le regole di rete virtuale - Interfaccia della riga di comando di Azure - Database di Azure per PostgreSQL - Server singolo
description: Questo articolo descrive come creare e gestire gli endpoint e le regole del servizio di rete virtuale per Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07e0f7cf2834b3984d9207fa18f3b0e32340e216
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660883"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Creare e gestire gli endpoint del servizio di rete virtuale per Database di Azure per PostgreSQL - Server singolo tramite l'interfaccia della riga di comando di Azure
Gli endpoint e le regole dei servizi di rete virtuale estendono lo spazio di indirizzi privato di una rete virtuale a un server di Database di Azure per PostgreSQL. Usando pratici comandi dell'interfaccia della riga di comando di Azure è possibile creare, aggiornare, eliminare, elencare e visualizzare gli endpoint e le regole di servizio rete virtuale per gestire il server. Per una panoramica degli endpoint di servizio di rete virtuale per Database di Azure per PostgreSQL, incluse le limitazioni, vedere [Usare gli endpoint e le regole di servizio di rete virtuale per Database di Azure per PostgreSQL](concepts-data-access-and-security-vnet.md). Gli endpoint di servizio di rete virtuale sono disponibili in tutte le aree supportate per Database di Azure per PostgreSQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa guida dettagliata:
- Installazione dell'[interfaccia della riga di comando di Azure ](/cli/azure/install-azure-cli). In alternativa, l'uso di Azure Cloud Shell nel browser.
- Creare un database [e un server di database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md).

    > [!NOTE]
    > Gli endpoint di servizio di rete virtuale sono supportati solo per i server per utilizzo generico e ottimizzati per la memoria.
    > In caso di peering di reti virtuali, se il traffico scorre attraverso un gateway di rete virtuale comune con endpoint di servizio e deve raggiungere il peer, creare una regola di elenco di controllo di accesso o di rete virtuale per consentire a Macchine virtuali di Azure all'interno della rete virtuale del gateway di accedere al server di Database di Azure per PostgreSQL.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Questo articolo richiede la versione 2.0 dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Configurare gli endpoint di servizio di rete virtuale per Database di Azure per PostgreSQL
Per configurare le reti virtuali vengono usati i comandi [az network vnet](/cli/azure/network/vnet).

Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Selezionare l'ID sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account#az-account-set). Sostituire la proprietà **id** dell'output **az login** per la sottoscrizione nel segnaposto dell'ID sottoscrizione.

- L'account deve avere le autorizzazioni necessarie per la creazione di una rete virtuale e di un endpoint di servizio.

Gli endpoint di servizio possono essere configurati sulle reti virtuali, in modo indipendente, da un utente con accesso in scrittura alla rete virtuale.

Per associare le risorse dei servizi di Azure a una rete virtuale, l'utente deve avere l'autorizzazione "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" per le subnet da aggiungere. Per impostazione predefinita, questa autorizzazione è inclusa nei ruoli di amministratore del servizio predefiniti e può essere modificata creando ruoli personalizzati.

Altre informazioni sui [ruoli predefiniti](../role-based-access-control/built-in-roles.md) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](../role-based-access-control/custom-roles.md).

Le reti virtuali e le risorse dei servizi di Azure possono essere nella stessa sottoscrizione o in sottoscrizioni diverse. Se la rete virtuale e le risorse dei servizi di Azure si trovano in sottoscrizioni diverse, le risorse devono trovarsi nello stesso tenant di Active Directory (AD). Verificare che in entrambe le sottoscrizioni sia registrato il provider di risorse **Microsoft.Sql**. Per altre informazioni, vedere [Resource-Manager-Registration][resource-manager-portal].

> [!IMPORTANT]
> Prima di eseguire lo script di esempio riportato di seguito o configurare gli endpoint di servizio è consigliabile leggere questo articolo in cui sono riportate considerazioni e istruzioni di configurazione per gli endpoint di servizio. **Endpoint servizio di rete virtuale:** un [endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) è una subnet in cui i valori delle proprietà includono uno o più nomi formali di tipi di servizi di Azure. Gli endpoint dei servizi di rete virtuale usano il nome del tipo di servizio **Microsoft.Sql**, che fa riferimento al servizio di Azure denominato Database SQL. Questo tag di servizio si applica al database SQL di Azure e ai servizi di Database di Azure per PostgreSQL e MySQL. È importante tenere presente che, quando si applica il tag di servizio **Microsoft.Sql** a un endpoint di servizio di rete virtuale, viene configurato il traffico dell'endpoint per tutti i server di Database SQL di Azure, Database di Azure per PostgreSQL e Database di Azure per MySQL nella subnet. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Script di esempio per creare un'istanza di Database di Azure per PostgreSQL, una rete virtuale e un endpoint di servizio di rete virtuale e per proteggere il server nella subnet con una regola di rete virtuale
In questo script di esempio modificare le righe evidenziate per personalizzare nome utente e password amministratore. Sostituire l'ID sottoscrizione usato nel comando `az account set --subscription` con il proprio ID sottoscrizione.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
