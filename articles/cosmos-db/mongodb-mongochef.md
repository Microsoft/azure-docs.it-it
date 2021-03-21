---
title: Usare Studio 3T per connettersi all'API di Azure Cosmos DB per MongoDB
description: Informazioni su come connettersi all'API di un Azure Cosmos DB per MongoDB usando Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: a02aaadf8c774557eb182acf041b6f19337a0de8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096513"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Connettersi a un account Azure Cosmos usando Studio 3T
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Per connettersi all'API di un Azure Cosmos DB per MongoDB usando Studio 3T, è necessario:

* Scaricare e installare [Studio 3T](https://studio3t.com/).
* Ottenere le informazioni sulla stringa di [connessione](connect-mongodb-account.md) dell'account Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Creare la connessione in Studio 3T

Per aggiungere l'account Azure Cosmos alla gestione connessione di studio 3T, seguire questa procedura:

1. Recuperare le informazioni di connessione per l'account dell'API Azure Cosmos DB per MongoDB usando le istruzioni nell'articolo [connettere un'applicazione MongoDB a Azure Cosmos DB](connect-mongodb-account.md) .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Screenshot della pagina della stringa di connessione":::

2. Fare clic su **Connect** (Connetti) per aprire la gestione connessioni, quindi fare clic su **New Connection** (Nuova connessione)

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Screenshot della gestione connessione di studio 3T che evidenzia il pulsante nuova connessione.":::
3. Nella finestra **nuova connessione** , nella scheda **Server** , immettere l'host (FQDN) dell'account Azure Cosmos e la porta.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="Screenshot della scheda server di gestione connessione di studio 3T":::
4. Nella finestra **New Connection** (Nuova connessione) della scheda **Authentication** (Autenticazione) scegliere la modalità di autenticazione **Basic (MONGODB-CR or SCARM-SHA-1)** (Base - MONGODB-CR o SCARM-SHA-1) e immettere il nome utente e la password.  Accettare il database di autenticazione predefinito (admin) o specificare un valore personalizzato.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="Screenshot della scheda autenticazione della gestione connessione di studio 3T":::
5. Nella finestra **New Connection** (Nuova connessione) nella scheda **SSL** selezionare la casella di controllo **Use SSL protocol to connect** (Usa protocollo SSL per la connessione) e quindi selezionare il pulsante di opzione **Accept server self-signed SSL certificates** (Accetta certificati SSL autofirmati server).

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Screenshot della scheda SSL di gestione connessione di studio 3T":::
6. Fare clic sul pulsante **Test Connection** (Test connessione) per convalidare le informazioni di connessione, quindi fare clic su **OK** per tornare alla finestra New Connection (Nuova connessione) e infine su **Save** (Salva).

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Screenshot della finestra di connessione di test di studio 3T":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Usare Studio 3T per creare un database, una raccolta e documenti
Per creare un database, una raccolta e documenti usando Studio 3T, seguire la procedura seguente:

1. In **Connection Manager** (Gestione connessioni) evidenziare la connessione e fare clic su **Connect** (Connetti).

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="Screenshot della gestione connessione di studio 3T":::
2. Fare clic con il pulsante destro del mouse sull'host, quindi scegliere **Add Database** (Aggiungi database).  Specificare un nome per il database e fare clic su **OK**.

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Screenshot dell'opzione Add database di studio 3T":::
3. Fare clic con il pulsante destro del mouse sul database, quindi scegliere **Add Collection** (Aggiungi raccolta).  Specificare un nome per la raccolta, quindi fare clic su **Create**(Crea).

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Screenshot dell'opzione di aggiunta della raccolta di studio 3T":::
4. Selezionare la voce di menu **Collection** (Raccolta), quindi fare clic su **Add Document** (Aggiungi documento).

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Screenshot della voce di menu Aggiungi documento di studio 3T":::
5. Nella finestra di dialogo Add Document (Aggiungi documento) incollare quanto segue, quindi fare clic su **Add Document**(Aggiungi documento).

    ```json
    {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
            { "firstName": "Thomas" },
            { "firstName": "Mary Kay"}
        ],
        "children": [
            {
                "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
                "pets": [{ "givenName": "Fluffy" }]
            }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }
    ```
    
6. Aggiungere un altro documento, questa volta con il contenuto seguente:

    ```json
    {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                "givenName": "Lisa",
                "gender": "female",
                "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    }
    ```

7. Eseguire una query di esempio. Cercare ad esempio famiglie con cognome 'Andersen' e restituire i campi relativi a genitori e stato.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Screenshot dei risultati delle query di Mongo chef":::

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.
