---
title: Creare un database a grafo con Java in Azure Cosmos DB
description: Presenta un esempio di codice Java che permette di connettersi ai dati di un grafo ed eseguire query su di essi in Azure Cosmos DB tramite Gremlin.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: jasonh
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: ad00fcc0c7b871210b29400821808b6729d953f6
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409409"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-gremlin-api"></a>Avvio rapido: Creare un database a grafo con Java SDK e l'API Gremlin di Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Console Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

In questo argomento di avvio rapido si apprenderà a creare e gestire un account API Gremlin di Azure Cosmos DB (grafo) dal portale di Azure e aggiungere dati usando un'app Java clonata da GitHub. Azure Cosmos DB è un servizio di database modello che consente di creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, con funzionalità di scalabilità orizzontale e distribuzione globale.

## <a name="prerequisites"></a>Prerequisiti
- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Indirizzare la variabile di ambiente `JAVA_HOME` alla cartella di installazione di JDK.
- Un [archivio binario Maven](https://maven.apache.org/download.cgi). 
- [Git](https://www.git-scm.com/downloads). 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-information).

I frammenti seguenti provengono tutti dal file *C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted\Program.java*.

Questa app console Java usa un database [API Gremlin](graph-introduction.md) con il driver [Apache TinkerPop](https://tinkerpop.apache.org/) di OSS. 

- Il `Client` Gremlin viene inizializzato dalla configurazione contenuta nel file *C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\remote.yaml*.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

- Viene eseguita una serie di passaggi di Gremlin tramite il metodo `client.submit`.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare la stringa di connessione e copiarla nell'app. Queste impostazioni consentono all'app di comunicare con il database ospitato.

1. Nell'account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com/) selezionare **Chiavi**. 

    Copiare la prima parte del valore dell'URI.

    :::image type="content" source="./media/create-graph-java/copy-access-key-azure-portal.png" alt-text="Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure":::

2. Aprire il file *src/remote.yaml* e incollare il valore di ID univoco sopra `$name$` in `hosts: [$name$.graphs.azure.com]`.

    La riga 1 del file *remote.yaml* si presenta ora nel modo seguente: 

    `hosts: [test-graph.graphs.azure.com]`

3. Sostituire `graphs` con `gremlin.cosmosdb` nel valore di `endpoint`. (Se l'account di database Graph è stato creato prima del 20 dicembre 2017, non apportare modifiche al valore dell'endpoint e continuare con il passaggio successivo.)

    Il valore dell'endpoint dovrebbe ora essere simile all'output seguente:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. Nel portale di Azure usare il pulsante Copia per copiare la CHIAVE PRIMARIA e incollarla su `$masterKey$` in `password: $masterKey$`.

    La riga 4 del file *remote.yaml* si presenta ora nel modo seguente: 

    `password: 2Ggkr662ifxz2Mg==`

5. Modificare la riga 3 di *remote.yaml* da

    `username: /dbs/$database$/colls/$collection$`

    to 

    `username: /dbs/sample-database/colls/sample-graph`

    Se per il grafo o il database di esempio è stato usato un nome univoco, aggiornare i valori nel modo appropriato.

6. Salvare il file *remote.yaml*.

## <a name="run-the-console-app"></a>Eseguire l'app console

1. Nella finestra del terminale git eseguire il comando `cd` per passare alla cartella azure-cosmos-db-graph-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. Nella finestra del terminale Git usare il comando seguente per installare i pacchetti Java necessari.

   ```git
   mvn package
   ```

3. Nella finestra del terminale Git usare il comando seguente per avviare l'applicazione Java.
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    La finestra del terminale mostra l'aggiunta dei vertici al grafo. 
    
    Se si verificano errori di timeout, controllare di avere aggiornato correttamente le informazioni di connessione in [Aggiornare le informazioni di connessione](#update-your-connection-information) e provare a eseguire di nuovo l'ultimo comando. 
    
    All'arresto del programma premere INVIO per tornare al portale di Azure nel browser Internet. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Verificare e aggiungere dati di esempio

È ora possibile tornare a Esplora dati e visualizzare i vertici aggiunti al grafo, quindi aggiungere altri punti dati.

1. Nell'account Azure Cosmos DB nel portale di Azure, selezionare **Esplora dati**, espandere **sample-graph**, selezionare **Grafo** e quindi **Applica filtro**. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png" alt-text="Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure":::

2. Nell'elenco **Risultati** verificare i nuovi utenti aggiunti al grafo. Selezionare **ben**. Come si può notare, l'utente è connesso a robin. È possibile spostare i vertici intorno mediante trascinamento della selezione, applicare lo zoom mediante lo scorrimento della rotellina del mouse ed espandere le dimensioni del grafo usando la doppia freccia. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png" alt-text="Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure":::

3. Vengono ora aggiunti alcuni nuovi utenti. Selezionare **Nuovo vertice** per aggiungere dati al grafo.

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure":::

4. Nella casella dell'etichetta immettere *person*.

5. Fare clic su **Aggiungi proprietà** per aggiungere ognuna delle proprietà seguenti. Si noti che è possibile creare proprietà univoche per ogni persona del grafo. È necessaria solo la chiave id.

    Key|Valore|Note
    ----|----|----
    id|ashley|Identificatore univoco per il vertice. Se non si specifica alcun ID, ne verrà generato automaticamente uno.
    gender|female| 
    tech | java | 

    > [!NOTE]
    > In questa guida di avvio rapido viene creata una raccolta non partizionata. Se tuttavia si crea una raccolta partizionata specificando una chiave di partizione durante la creazione della raccolta, sarà necessario includere la chiave di partizione come chiave in ogni nuovo vertice. 

6. Selezionare **OK**. Potrebbe essere necessario espandere la schermata per vedere il pulsante **OK** nella parte inferiore dello schermo.

7. Selezionare nuovamente **Nuovo vertice** e aggiungere un altro nuovo utente. 

8. Immettere un'etichetta di *persona*.

9. Fare clic su **Aggiungi proprietà** per aggiungere ognuna delle proprietà seguenti:

    Key|Valore|Note
    ----|----|----
    id|rakesh|Identificatore univoco per il vertice. Se non si specifica alcun ID, ne verrà generato automaticamente uno.
    gender|male| 
    school|MIT| 

10. Selezionare **OK**. 

11. Selezionare il pulsante **Applica filtro** con il filtro `g.V()` predefinito per visualizzare tutti i valori nel grafo. Tutti gli utenti sono ora visualizzati nell'elenco **Risultati**. 

    Quando si aggiungono altri dati, è possibile usare i filtri per limitare i risultati visualizzati. Per impostazione predefinita, Esplora dati usa `g.V()` per recuperare tutti i vertici di un grafo. È possibile modificarlo in un'altra [query di grafo](tutorial-query-graph.md), ad esempio `g.V().count()`, per restituire un conteggio di tutti i vertici del grafo in formato JSON. Se è stato modificato il filtro, reimpostarlo su `g.V()` e selezionare **Applica filtro** per visualizzare di nuovo tutti i risultati.

12. È ora possibile connettere rakesh e ashley. Assicurarsi che nell'elenco **Risultati** sia selezionato il valore **ashley** e quindi selezionare :::image type="content" source="./media/create-graph-java/edit-pencil-button.png" alt-text="Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure"::: accanto a **Destinazioni** in basso a destra. Per visualizzare il pulsante potrebbe essere necessario allargare la finestra.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure":::

13. Nella casella **Destinazione** immettere *rakesh* e nella casella **Etichetta arco** immettere *knows* e quindi selezionare la casella di controllo.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png" alt-text="Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure":::

14. Selezionare ora **rakesh** dall'elenco Risultati. Come si può notare ashley e rakesh sono connessi. 

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer.png" alt-text="Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure":::

È stata completata la parte relativa alla creazione delle risorse di questa esercitazione. È possibile continuare ad aggiungere vertici al grafo, modificare quelli esistenti o modificare le query. Vengono ora esaminate le metriche di Azure Cosmos DB e quindi pulite le risorse. 

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare un grafo con Esplora dati e come eseguire un'app Java che aggiunge dati al grafo. È ora possibile creare query più complesse e implementare la potente logica di attraversamento dei grafi usando Gremlin. 

> [!div class="nextstepaction"]
> [Eseguire query con Gremlin](tutorial-query-graph.md)

