---
title: includere file
description: includere file
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ad4445cbea6553a7a96299e1276dbe8f3816e166
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994553"
---
1. In una nuova finestra del browser accedere al [portale di Azure](https://portal.azure.com/).

2. Nel menu a sinistra selezionare **Crea una risorsa**.
   
   ![Creare una risorsa nel portale di Azure](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Nella pagina **Nuovo** selezionare **Database** > **Azure Cosmos DB**.
   
   ![Riquadro Database nel portale di Azure](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. Nella pagina **Crea account Azure Cosmos DB** immettere le impostazioni per il nuovo account Azure Cosmos DB. 
 
    Impostazione|Valore|Descrizione
    ---|---|---
    Subscription|Sottoscrizione in uso|Selezionare la sottoscrizione di Azure da usare per l'account Azure Cosmos DB. 
    Gruppo di risorse|Creare un nuovo gruppo di risorse<br><br>Immettere quindi lo stesso nome specificato nome dell'account.|Selezionare **Crea nuovo**. Immettere quindi il nome di un nuovo gruppo di risorse per l'account. Per semplicità, usare lo stesso nome usato come nome dell'account Azure Cosmos DB. 
    Nome account|Immettere un nome univoco|Immettere un nome univoco per identificare l'account Azure Cosmos DB. L'URI dell'account sarà *mongo.cosmos.azure.com* e verrà aggiunto al nome dell'account univoco.<br><br>Il nome dell'account può contenere solo lettere minuscole, numeri e trattini e deve avere una lunghezza compresa tra 3 e 31 caratteri.
    API|API Azure Cosmos DB per MongoDB|L'API determina il tipo di account da creare. Azure Cosmos DB offre cinque API: Core (SQL) per i database di documenti, Gremlin per i database a grafo, API Azure Cosmos DB per MongoDB per i database di documenti, Tabella di Azure e Cassandra. Attualmente, è necessario creare un account separato per ogni API. <br><br>Selezionare l'**API Azure Cosmos DB per MongoDB** perché in questo argomento di avvio rapido si creerà una raccolta compatibile con MongoDB.<br><br>[Altre informazioni sull'API Azure Cosmos DB per MongoDB](../articles/cosmos-db/mongodb-introduction.md).|
    Location|Selezionare l'area più vicina agli utenti|Selezionare una posizione geografica in cui ospitare l'account Azure Cosmos DB. Usare la località più vicina agli utenti per offrire loro la massima velocità di accesso ai dati.|
    Modalità di capacità|Provisioning velocità effettiva o Serverless|Selezionare **Provisioning velocità effettiva** per creare un account in modalità [Provisioning velocità effettiva](../articles/cosmos-db/set-throughput.md). Selezionare **Serverless** per creare un account in modalità [Serverless](../articles/cosmos-db/serverless.md).<br><br>**Nota**: L'API MongoDB versione 3.6 è supportata da account serverless. Se si sceglie la versione 3.2, l'account verrà forzato in modalità Provisioning velocità effettiva.

    Selezionare **Rivedi+Crea**. È possibile ignorare le sezioni **Rete** e **Tag**. 

    ![Pagina del nuovo account per Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. La creazione dell'account richiede alcuni minuti. Attendere che nel portale venga visualizzata la pagina **Complimenti, l'account dell'API Azure Cosmos DB per MongoDB è pronto**.

    ![Riquadro delle notifiche nel portale di Azure](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
