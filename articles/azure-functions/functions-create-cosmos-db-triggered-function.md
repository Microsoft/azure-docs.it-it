---
title: Creare una funzione attivata da Azure Cosmos DB
description: Usare Funzioni di Azure per creare una funzione senza server che viene richiamata quando vengono aggiunti i dati al database in Azure Cosmos DB.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: quickstart
ms.date: 10/02/2018
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 830c7cdee247118ed24fc9b3a2a9efe8609c75d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75863285"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Creare una funzione attivata da Azure Cosmos DB

Informazioni su come creare una funzione attivata quando i dati vengono aggiunti o modificati in Azure Cosmos DB. Per altre informazioni su Azure Cosmos DB, vedere [Azure Cosmos DB: elaborazione di database senza server con Funzioni di Azure](../cosmos-db/serverless-computing-database.md).

![Visualizzare il messaggio nei log.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Prerequisites

Per completare questa esercitazione:

+ Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Prima di creare il trigger è necessario un account Azure Cosmos DB che usa l'API SQL.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Creare un'app per le funzioni di Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Si creerà ora una funzione nella nuova app per le funzioni.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Creare i trigger di Azure Cosmos DB

1. Espandere l'app per le funzioni e fare clic sul pulsante **+** accanto a **Funzioni**. Se questa è la prima funzione nell'app per le funzioni, selezionare **Nel portale** e quindi **Continua**. In caso contrario, andare direttamente al passaggio 3.

   ![Pagina della guida introduttiva di Funzioni nel portale di Azure](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. Scegliere **Altri modelli** e quindi **Termina e visualizza i modelli**.

    ![Selezione di altri modelli nella guida introduttiva di Funzioni](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. Nel campo di ricerca digitare `cosmos` e quindi scegliere il modello del **trigger Azure Cosmos DB**.

1. Se richiesto, selezionare **Installa** per installare l'estensione Azure Cosmos DB nell'app per le funzioni. Al termine dell'installazione, selezionare **Continua**.

    ![Installare le estensioni di binding](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. Configurare il nuovo trigger con le impostazioni come indicato nella tabella sotto l'immagine.

    ![Creare la funzione attivata da Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | Impostazione      | Valore consigliato  | Descrizione                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nome** | Predefinito | Usare il nome della funzione predefinito suggerito dal modello.|
    | **Connessione all'account Azure Cosmos DB** | Nuova impostazione | Selezionare **Nuova**, scegliere **Sottoscrizione**, l'**account di database** creato in precedenza e fare clic su **Seleziona**. Verrà creata un'impostazione applicazione per la connessione all'account. Questa impostazione viene usata dal binding per la connessione al database. |
    | **Nome contenitore** | Items | Il nome del contenitore da monitorare. |
    | **Crea raccolta di lease se non esiste** | Selezionato | Il contenitore non esiste ancora, quindi è necessario crearlo. |
    | **Nome database** | Attività | Il nome del database con il contenitore da monitorare. |

1. Fare clic su **Crea** per creare la funzione attivata da Azure Cosmos DB. Dopo aver creato la funzione, viene visualizzato il codice della funzione basato sul modello.  

    ![Modello di funzione di Cosmos DB in C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Questo modello di funzione scrive il numero di documenti e l'ID del primo documento nei log.

Successivamente, connettersi al proprio account Azure Cosmos DB e creare il contenitore `Items` nel database `Tasks`.

## <a name="create-the-items-container"></a>Creare il contenitore Items

1. Aprire una seconda istanza del [portale di Azure](https://portal.azure.com) in una nuova scheda del browser.

1. Sul lato sinistro del portale espandere la barra delle icone, digitare `cosmos` nel campo di ricerca e selezionare **Azure Cosmos DB**.

    ![Cercare il servizio Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Scegliere l'account di Azure Cosmos DB, quindi selezionare **Esplora dati**. 

1. In **API SQL** scegliere il database **Tasks** e selezionare **Nuovo contenitore**.

    ![Creare un contenitore](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. In **Aggiungi contenitore** usare le impostazioni visualizzate nella tabella riportata sotto l'immagine. 

    ![Definire il contenitore Tasks](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Impostazione|Valore consigliato|Descrizione |
    | ---|---|--- |
    | **ID database** | Attività |Nome del nuovo database. Deve corrispondere al nome definito nell'associazione della funzione. |
    | **ID contenitore** | Items | Il nome del nuovo contenitore. Deve corrispondere al nome definito nell'associazione della funzione.  |
    | **[Chiave di partizione](../cosmos-db/partition-data.md)** | /category|Chiave di partizione che distribuisce i dati in modo uniforme a ogni partizione. Quando si crea un contenitore a elevate prestazioni, è importante selezionare la chiave di partizione corretta. | 
    | **Velocità effettiva** |400 UR| Usare il valore predefinito. Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento. |    

1. Fare clic su **OK** per creare il contenitore Items. La creazione del contenitore potrebbe richiedere del tempo.

Dopo aver creato il contenitore specificato nel binding della funzione, è possibile testare la funzione aggiungendo elementi a questo nuovo contenitore.

## <a name="test-the-function"></a>Testare la funzione

1. In Esplora dati espandere il nuovo contenitore **Items**, scegliere **Items**, quindi selezionare **Nuovo elemento**.

    ![Creare un elemento nel contenitore Items](./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png)

1. Sostituire il contenuto del nuovo elemento con il contenuto seguente, quindi scegliere **Salva**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Passare alla prima scheda del browser che contiene la funzione nel portale. Espandere i log della funzione e verificare che il nuovo documento abbia attivato la funzione. Verificare che il valore dell'ID del documento `task1` sia scritto nel log. 

    ![Visualizzare il messaggio nei log.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Facoltativo) Tornare al documento, apportare una modifica e fare clic su **Aggiorna**. Quindi, tornare ai log della funzione e verificare che l'aggiornamento abbia attivato anche la funzione.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

È stata creata una funzione che viene eseguita quando un documento viene aggiunto o modificato nel database di Azure Cosmos DB. Per altre informazioni sui trigger di Azure Cosmos DB, vedere [Binding di Azure Cosmos DB per Funzioni di Azure](functions-bindings-cosmosdb.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
