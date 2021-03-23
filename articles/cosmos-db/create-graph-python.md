---
title: 'Avvio rapido: API Gremlin con Python - Azure Cosmos DB'
description: Questa guida introduttiva illustra come usare l'API Gremlin di Azure Cosmos DB per creare un'applicazione console con il portale di Azure e Python
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: chrande
ms.custom: devx-track-python
ms.openlocfilehash: 342b9c9aae0a523ac770ba78f298c4ba91c434e7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798749"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Avvio rapido: Creare un database a grafo in Azure Cosmos DB usando Python e il portale di Azure
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Console Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

In questa guida di avvio rapido si apprenderà a creare e gestire un account API Gremlin di Azure Cosmos DB (grafo) dal portale di Azure e aggiungere dati usando un'app Python clonata da GitHub. Azure Cosmos DB è un servizio di database modello che consente di creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, con funzionalità di scalabilità orizzontale e distribuzione globale.

## <a name="prerequisites"></a>Prerequisiti
- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). In alternativa, è possibile [provare gratuitamente Azure DB Cosmos](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure.
- [Python 3.6 +](https://www.python.org/downloads/) incluso il programma di installazione del pacchetto [PIP](https://pip.pypa.io/en/stable/installing/) .
- [Driver Python per Gremlin](https://github.com/apache/tinkerpop/tree/master/gremlin-python).
- [Git](https://git-scm.com/downloads).

> [!NOTE]
> Questa guida introduttiva richiede un account del database a grafo creato dopo il 20 dicembre 2017. Gli account esistenti supporteranno Python dopo la migrazione alla disponibilità generale.

## <a name="create-a-database-account"></a>Creare un account di database

Prima di potere creare un database a grafo, è necessario creare un account database Gremlin (grafo) con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Aggiungere un grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Si può ora passare a usare il codice. Per clonare un'app API Gremlin da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice.  

1. Aprire un prompt dei comandi, creare una nuova cartella denominata git-samples e quindi chiudere il prompt dei comandi.

    ```bash
    md "C:\git-samples"
    ```

2. Aprire una finestra del terminale Git, ad esempio git bash, ed eseguire il comando `cd` per passare a una cartella in cui installare l'app di esempio.  

    ```bash
    cd "C:\git-samples"
    ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. I frammenti di codice provengono tutti dal file *connect.py*, che si trova nella cartella *C:\git-samples\azure-cosmos-db-graph-python-getting-started\\* . In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-information). 

* Il `client` Gremlin viene inizializzato nella riga 104 in *connect.py*:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* All'inizio del file *connect.py* viene dichiarata una serie di passaggi di Gremlin, che vengono quindi eseguiti usando il metodo `client.submitAsync()`:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare la stringa di connessione e copiarla nell'app. Queste impostazioni consentono all'app di comunicare con il database ospitato.

1. Nell'account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com/) selezionare **Chiavi**. 

    Copiare la prima parte del valore dell'URI.

    :::image type="content" source="./media/create-graph-python/keys.png" alt-text="Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure":::

2. Aprire il file *connect.py* e alla riga 104 incollare il valore di URI su `<YOUR_ENDPOINT>` qui:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    La parte URI dell'oggetto client sarà ora simile a questo codice:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. Modificare il secondo parametro dell'oggetto `client` per sostituire le stringhe `<YOUR_DATABASE>` e `<YOUR_COLLECTION_OR_GRAPH>`. Se si sono usati i valori suggeriti, il parametro sarà simile a questo codice:

    `username="/dbs/sample-database/colls/sample-graph"`

    L'intero oggetto `client` sarà ora simile a questo codice:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. Nella pagina **Chiavi** usare il pulsante Copia per copiare la CHIAVE PRIMARIA e incollarla su `<YOUR_PASSWORD>` nel parametro `password=<YOUR_PASSWORD>`.

    La definizione dell'intero oggetto `client` sarà ora simile a questo codice:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Salvare il file *connect.py*.

## <a name="run-the-console-app"></a>Eseguire l'app console

1. Nella finestra del terminale Git eseguire il comando `cd` per passare alla cartella azure-cosmos-db-graph-python-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. Nella finestra del terminale Git usare il comando seguente per installare i pacchetti Python necessari.

   ```
   pip install -r requirements.txt
   ```

3. Nella finestra del terminale Git usare il comando seguente per avviare l'applicazione Python.
    
    ```
    python connect.py
    ```

    La finestra del terminale mostra l'aggiunta dei vertici e degli archi al grafo. 
    
    Se si verificano errori di timeout, controllare di avere aggiornato correttamente le informazioni di connessione in [Aggiornare le informazioni di connessione](#update-your-connection-information) e provare a eseguire di nuovo l'ultimo comando. 
    
    All'arresto del programma premere Invio per tornare al portale di Azure nel browser Internet.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Verificare e aggiungere dati di esempio

Dopo avere inserito i vertici e gli archi, è ora possibile tornare a Esplora dati e visualizzare i vertici aggiunti al grafo, quindi aggiungere altri punti dati.

1. Nell'account Azure Cosmos DB nel portale di Azure, selezionare **Esplora dati**, espandere **sample-graph**, selezionare **Grafo** e quindi **Applica filtro**. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png" alt-text="Screenshot che mostra la sezione API con la voce Grafo selezionata con l'opzione Applica filtro.":::

2. Nell'elenco **Risultati** verificare i tre nuovi utenti aggiunti al grafo. È possibile spostare i vertici intorno mediante trascinamento della selezione, applicare lo zoom mediante lo scorrimento della rotellina del mouse ed espandere le dimensioni del grafo usando la doppia freccia. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png" alt-text="Nuovi vertici nel grafo in Esplora dati nel portale di Azure":::

3. Vengono ora aggiunti alcuni nuovi utenti. Selezionare il pulsante **New Vertex** (Nuovo vertice) per aggiungere dati al grafo.

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Screenshot che mostra il riquadro Nuovo vertice in cui è possibile immettere i valori.":::

4. Immettere un'etichetta di *persona*.

5. Fare clic su **Aggiungi proprietà** per aggiungere ognuna delle proprietà seguenti. Si noti che è possibile creare proprietà univoche per ogni persona del grafo. È necessaria solo la chiave id.

    Key|Valore|Note
    ----|----|----
    pk|/pk| 
    id|ashley|Identificatore univoco per il vertice. Se non si specifica alcun ID, ne verrà generato automaticamente uno.
    gender|female| 
    tech | java | 

    > [!NOTE]
    > In questa esercitazione introduttiva creare una raccolta non partizionata. Se tuttavia si crea una raccolta partizionata specificando una chiave di partizione durante la creazione della raccolta, sarà necessario includere la chiave di partizione come chiave in ogni nuovo vertice. 

6. Selezionare **OK**. Potrebbe essere necessario espandere la schermata per vedere il pulsante **OK** nella parte inferiore dello schermo.

7. Selezionare nuovamente **Nuovo vertice** e aggiungere un altro nuovo utente. 

8. Immettere un'etichetta di *persona*.

9. Fare clic su **Aggiungi proprietà** per aggiungere ognuna delle proprietà seguenti:

    Key|Valore|Note
    ----|----|----
    pk|/pk| 
    id|rakesh|Identificatore univoco per il vertice. Se non si specifica alcun ID, ne verrà generato automaticamente uno.
    gender|male| 
    school|MIT| 

10. Selezionare **OK**. 

11. Selezionare il pulsante **Applica filtro** con il filtro `g.V()` predefinito per visualizzare tutti i valori nel grafo. Tutti gli utenti sono ora visualizzati nell'elenco **Risultati**. 

    Quando si aggiungono altri dati, è possibile usare i filtri per limitare i risultati visualizzati. Per impostazione predefinita, Esplora dati usa `g.V()` per recuperare tutti i vertici di un grafo. È possibile modificarlo in un'altra [query di grafo](tutorial-query-graph.md), ad esempio `g.V().count()`, per restituire un conteggio di tutti i vertici del grafo in formato JSON. Se è stato modificato il filtro, reimpostarlo su `g.V()` e selezionare **Applica filtro** per visualizzare di nuovo tutti i risultati.

12. È ora possibile connettere rakesh e ashley. Assicurarsi che il valore **ashley** sia selezionato nell'elenco **Risultati**, quindi selezionare il pulsante di modifica accanto a **Destinazioni** in basso a destra. Potrebbe essere necessario allargare la finestra per visualizzare l'area **Proprietà**.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Cambiare la destinazione di un vertice in un grafo":::

13. Nella casella **Destinazione** digitare *rakesh* e nella casella **Edge label** (Etichetta arco) digitare *knows*, quindi selezionare la casella di controllo.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png" alt-text="Aggiungere una connessione tra ashley e rakesh in Esplora dati":::

14. Selezionare ora **rakesh** dall'elenco Risultati. Come si può notare ashley e rakesh sono connessi. 

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer.png" alt-text="Due vertici connessi in Esplora dati":::

È stata completata la parte relativa alla creazione delle risorse di questa esercitazione. È possibile continuare ad aggiungere vertici al grafo, modificare quelli esistenti o modificare le query. Vengono ora esaminate le metriche di Azure Cosmos DB e quindi pulite le risorse. 

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare un grafo con Esplora dati e come eseguire un'app Python per aggiungere dati al grafo. È ora possibile creare query più complesse e implementare la potente logica di attraversamento dei grafi usando Gremlin. 

> [!div class="nextstepaction"]
> [Eseguire query con Gremlin](tutorial-query-graph.md)

