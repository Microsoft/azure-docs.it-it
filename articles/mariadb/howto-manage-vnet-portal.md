---
title: Gestire endpoint VNet-portale di Azure-database di Azure per MariaDB
description: Creare e gestire gli endpoint e le regole di servizio di rete virtuale per Database di Azure per MariaDB tramite il portale di Azure
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5eaa7821c61010b322d8f9032c439df28c297f3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665022"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Creare e gestire gli endpoint e le regole di servizio di rete virtuale per Database di Azure per MariaDB tramite il portale di Azure

Gli endpoint e le regole dei servizi di rete virtuale estendono lo spazio di indirizzi privato di una rete virtuale a un server di Database di Azure per MariaDB. Per una panoramica degli endpoint di servizio di rete virtuale per Database di Azure per MariaDB, incluse le limitazioni, vedere [Endpoint di servizio di rete virtuale per server di Database di Azure per MariaDB](concepts-data-access-security-vnet.md). Gli endpoint di servizio di rete virtuale sono disponibili in tutte le aree supportate per Database di Azure per MariaDB.

> [!NOTE]
> Gli endpoint di servizio di rete virtuale sono supportati solo per i server per utilizzo generico e ottimizzati per la memoria.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Creare una regola di rete virtuale e abilitare gli endpoint di servizio

1. Nella pagina del server MariaDB, in Impostazioni, fare clic su **Sicurezza connessione** per aprire la pagina Sicurezza connessione per Database di Azure per MariaDB.

2. Verificare che il controllo Consenti l'accesso a servizi di Azure sia impostato su **disattivato**.

> [!Important]
> Se lo si imposta su ON, il server di database MariaDB di Azure accetta la comunicazione da qualsiasi subnet. Lasciando il controllo impostato su SÌ, il numero di accessi potrebbe essere eccessivo dal punto di vista della sicurezza. La funzionalità dell'endpoint del servizio Rete virtuale di Microsoft Azure, in coordinamento con la funzionalità delle regole della rete virtuale del database di Azure per MariaDB, può ridurre la superficie di attacco della sicurezza.

3. Fare quindi clic su **+ Aggiunta di una rete virtuale esistente**. Se non si dispone di una rete virtuale, è possibile fare clic su **+ Crea nuova rete virtuale** per crearne una. Vedere [Guida introduttiva: Creare una rete virtuale con il portale di Azure](../virtual-network/quick-create-portal.md).

   ![Portale di Azure: fare clic su Sicurezza connessione](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Immettere un nome di regola di rete virtuale, selezionare la sottoscrizione, specificare il nome della rete virtuale e quello della subnet e quindi fare clic su **Abilita**. In questo modo, gli endpoint di servizio di rete virtuale nella subnet vengono abilitati automaticamente tramite il tag di servizio **Microsoft.SQL**.

   ![Portale di Azure: configurare la rete virtuale](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   L'account deve avere le autorizzazioni necessarie per la creazione di una rete virtuale e di un endpoint di servizio.

   Gli endpoint di servizio possono essere configurati sulle reti virtuali, in modo indipendente, da un utente con accesso in scrittura alla rete virtuale.
    
   Per associare le risorse dei servizi di Azure a una rete virtuale, l'utente deve avere l'autorizzazione "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" per le subnet da aggiungere. Per impostazione predefinita, questa autorizzazione è inclusa nei ruoli di amministratore del servizio predefiniti e può essere modificata creando ruoli personalizzati.
    
   Altre informazioni sui [ruoli predefiniti](../role-based-access-control/built-in-roles.md) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](../role-based-access-control/custom-roles.md).
    
   Le reti virtuali e le risorse dei servizi di Azure possono essere nella stessa sottoscrizione o in sottoscrizioni diverse. Se la rete virtuale e le risorse dei servizi di Azure si trovano in sottoscrizioni diverse, le risorse devono trovarsi nello stesso tenant di Active Directory (AD). Verificare che in entrambe le sottoscrizioni sia registrato il provider di risorse **Microsoft.Sql**. Per altre informazioni, fare riferimento a [resource-manager-registration][resource-manager-portal].

   > [!IMPORTANT]
   > Prima configurare gli endpoint di servizio è consigliabile leggere questo articolo in cui sono riportate considerazioni e istruzioni di configurazione per gli endpoint di servizio. **Endpoint servizio di rete virtuale:** un [endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) è una subnet in cui i valori delle proprietà includono uno o più nomi formali di tipi di servizi di Azure. Gli endpoint dei servizi di rete virtuale usano il nome del tipo di servizio **Microsoft.Sql**, che fa riferimento al servizio di Azure denominato Database SQL. Questo tag di servizio si applica al database SQL di Azure e ai servizi di Database di Azure per MariaDB, PostgreSQL e MySQL. È importante tenere presente che, quando si applica il tag di servizio **Microsoft.Sql** a un endpoint di servizio di rete virtuale, viene configurato il traffico dell'endpoint per tutti i servizi di Database di Azure, tra cui i server Database SQL di Azure, Database di Azure per PostgreSQL, Database di Azure per MariaDB e Database di Azure per MySQL nella subnet.
   > 

5. Dopo aver completato la procedura di abilitazione, fare clic su **OK**. Si noterà che gli endpoint di servizio di rete virtuale sono abilitati insieme a una regola di rete virtuale.

   ![Endpoint di servizio di rete virtuale abilitati e regola di rete virtuale creata](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [Configurazione di SSL nel Database di Azure per MariaDB](howto-configure-ssl.md)
- Con una procedura analoga, è possibile creare script per [abilitare gli endpoint di servizio di rete virtuale e creare una regola di rete virtuale per Database di Azure per MariaDB tramite l'interfaccia della riga di comando di Azure](howto-manage-vnet-cli.md).

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md