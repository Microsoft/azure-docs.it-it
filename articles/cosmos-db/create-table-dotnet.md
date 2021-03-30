---
title: 'Avvio rapido: API Tabella con .NET - Azure Cosmos DB'
description: Questa guida introduttiva illustra come usare l'API Tabelle di Azure Cosmos DB per creare un'applicazione con il portale di Azure e .NET
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 513af71384fd1fa5d38ee3ec367a42892a662444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096938"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Avvio rapido: Creare un'app dell'API Tabella con .NET SDK e Azure Cosmos DB 
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
>  

Questa guida introduttiva descrive come usare .NET e l'[API Tabelle](table-introduction.md) di Azure Cosmos DB per creare un'app clonando un esempio di GitHub. La guida introduttiva illustra anche come creare un account Azure Cosmos DB e come usare Esplora dati per creare tabelle e entità nel portale di Azure basato sul Web.

## <a name="prerequisites"></a>Prerequisiti

Se Visual Studio 2019 non è ancora installato, è possibile scaricare e usare la **versione gratuita** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Aggiungere una tabella

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Aggiungere dati di esempio

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

A questo punto è possibile clonare un'app Table da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire un prompt dei comandi, creare una nuova cartella denominata git-samples e quindi chiudere il prompt dei comandi.

   ```bash
   md "C:\git-samples"
   ```

2. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una nuova cartella in cui installare l'app di esempio.

   ```bash
   cd "C:\git-samples"
   ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

> [!TIP]
> Per una procedura dettagliata più completa con codice simile, vedere l'articolo relativo all'[esempio di API Tabella di Cosmos DB](./tutorial-develop-table-dotnet.md).

## <a name="open-the-sample-application-in-visual-studio"></a>Aprire l'applicazione di esempio in Visual Studio

1. In Visual Studio scegliere **Apri** dal menu **File**, quindi scegliere **Progetto/Soluzione**. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-open-solution.png" alt-text="Aprire la soluzione"::: 

2. Passare alla cartella in cui è stata clonata l'applicazione di esempio e aprire il file TableStorage.sln.

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. In alternativa, è possibile passare alla sezione [Aggiornare la stringa di connessione](#update-your-connection-string) di questo documento.

* Il codice seguente mostra come creare una tabella in Archiviazione di Azure:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

* Il codice seguente mostra come inserire i dati nella tabella:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

* Il codice seguente mostra come eseguire query sui dati dalla tabella:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

* Il codice seguente mostra come eliminare i dati dalla tabella:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app. Questo consente all'app di comunicare con il database ospitato. 

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Stringa di connessione**. Usare il pulsante di copia sul lato destro della finestra per copiare la **STRINGA DI CONNESSIONE PRIMARIA**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Visualizzare e copiare la STRINGA DI CONNESSIONE PRIMARIA nel riquadro Stringa di connessione":::

2. Aprire il file **Settings.json** in Visual Studio. 

3. Incollare il valore del campo **STRINGA DI CONNESSIONE PRIMARIA** dal portale nel valore StorageConnectionString. Incollare la stringa tra virgolette.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. Premere CTRL+S per salvare il file **Settings.json**.

L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Compilare e distribuire l'app

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **CosmosTableSamples** in **Esplora soluzioni** e quindi scegliere **Gestisci pacchetti NuGet**. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-manage-nuget.png" alt-text="Gestisci pacchetti NuGet":::

2. Nella casella **Sfoglia** di NuGet digitare Microsoft.Azure.Cosmos.Table. Verrà trovata la libreria client per l'API Tabelle di Cosmos DB. Si noti che questa libreria è attualmente disponibile per .NET Framework e .NET Standard. 
   
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-nuget-browse.png" alt-text="Scheda Sfoglia di NuGet":::

3. Fare clic su **Installa** per installare la libreria **Microsoft.Azure.Cosmos.Table**. Vengono installati il pacchetto dell'API Tabelle di Azure Cosmos DB e tutte le dipendenze.

4. Quando si esegue l'intera app, i dati di esempio vengono inseriti nell'entità tabella ed eliminati al termine, in modo da non visualizzare i dati inseriti se si esegue l'intero esempio. È tuttavia possibile inserire alcuni punti di interruzione per visualizzare i dati. Aprire il file BasicSamples.cs e fare clic con il pulsante destro del mouse sulla riga 52, scegliere **Punto di interruzione**, quindi selezionare **Inserisci punto di interruzione**. Inserire un altro punto di interruzione alla riga 55.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-breakpoint.png" alt-text="Aggiungere un punto di interruzione"::: 

5. Premere F5 per eseguire l'applicazione. La finestra della console mostra il nome del nuovo database della tabella (in questo caso, demoa13b1) in Azure Cosmos DB. 
    
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-console.png" alt-text="Output della console":::

   Quando si raggiunge il primo punto di interruzione, tornare a Esplora dati nel portale di Azure. Fare clic sul pulsante **Aggiorna**, espandere la tabella demo* e fare clic su **Entità**. La scheda **Entità** a destra illustra la nuova entità che è stata aggiunta per Walter Harp. Si noti che il numero di telefono per la nuova entità è 425-555-0101.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-entity.png" alt-text="Nuova entità":::
    
   Se viene visualizzato un errore che indica che non è stato trovato il file Settings.json durante l'esecuzione del progetto, è possibile risolvere il problema aggiungendo la voce XML seguente alle impostazioni del progetto. Fare clic con il pulsante destro del mouse su CosmosTableSamples, scegliere il comando di modifica di CosmosTableSamples.csproj e aggiungere l'elemento itemGroup seguente: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Chiudere la scheda **Entità** in Esplora dati.
    
7. Premere F5 per eseguire l'app fino al punto di interruzione successivo. 

    Quando si raggiunge il punto di interruzione, passare al portale di Azure, fare clic di nuovo su **Entità** per aprire la scheda **Entità** e notare che il numero di telefono è stato aggiornato a 425-555-0105.

8. Premere F5 per eseguire l'app. 
 
   L'app aggiunge le entità da usare in un'app di esempio avanzata attualmente non supportata dall'API Tabelle. L'app elimina quindi la tabella creata dall'app di esempio.

9. Nella finestra della console premere INVIO per terminare l'esecuzione dell'app. 
  

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare una tabella con Esplora dati e come eseguire un'app.  È ora possibile eseguire query sui dati tramite l'API di tabella.  

> [!div class="nextstepaction"]
> [Importare i dati delle tabelle nell'API Tabelle](table-import.md)