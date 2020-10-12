---
title: Sviluppare Funzioni di Azure con Visual Studio
description: Informazioni su come sviluppare e testare funzioni di Azure usando gli strumenti di funzioni di Azure per Visual Studio 2019.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 0ee5d270db2149be0cfbf6bf06f87a5d0133c6ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88612810"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Sviluppare Funzioni di Azure con Visual Studio  

Visual Studio consente di sviluppare, testare e distribuire funzioni libreria di classi C# in Azure. Se questa esperienza è la prima con funzioni di Azure, vedere [Introduzione a funzioni di Azure](functions-overview.md).

Quando si sviluppano le funzioni, Visual Studio offre i vantaggi seguenti: 

* Modifica, compilazione ed esecuzione di funzioni nel computer di sviluppo locale. 
* Pubblicare il progetto di funzioni di Azure direttamente in Azure e creare le risorse di Azure in base alle esigenze. 
* Usare gli attributi C# per dichiarare associazioni di funzioni direttamente nel codice C#.
* Sviluppo e distribuzione di funzioni C# precompilate. Le funzioni precompilate offrono migliori prestazioni nell'avvio a freddo rispetto alle funzioni basate su script C#. 
* Possibilità di scrivere il codice delle funzioni in C# sfruttando al contempo tutti i vantaggi dello sviluppo in Visual Studio. 

Questo articolo fornisce informazioni dettagliate su come usare Visual Studio per sviluppare funzioni della libreria di classi C# e pubblicarle in Azure. Prima di leggere questo articolo, provare a completare la [Guida introduttiva alle funzioni di Visual Studio](functions-create-your-first-function-visual-studio.md). 

Se non specificato diversamente, le procedure e gli esempi indicati sono per Visual Studio 2019. 

## <a name="prerequisites"></a>Prerequisiti

- Strumenti di funzioni di Azure. Per aggiungere gli strumenti per le funzioni di Azure, includere il carico di lavoro **sviluppo di Azure** nell'installazione di Visual Studio. Gli strumenti di funzioni di Azure sono disponibili nel carico di lavoro di sviluppo di Azure a partire da Visual Studio 2017.

- Altre risorse necessarie, ad esempio un account di archiviazione di Azure, vengono create nella sottoscrizione durante il processo di pubblicazione.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> In Visual Studio 2017, il carico di lavoro sviluppo di Azure installa gli strumenti di funzioni di Azure come estensione separata. Quando si aggiorna l'installazione di Visual Studio 2017, assicurarsi di usare la [versione più recente](#check-your-tools-version) degli strumenti di funzioni di Azure. Le sezioni seguenti illustrano come controllare e (se necessario) aggiornare l'estensione degli strumenti di funzioni di Azure in Visual Studio 2017. 
>
> Se si usa Visual Studio 2019, ignorare queste sezioni.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Controllare la versione degli strumenti in Visual Studio 2017

1. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**. Espandere **Installed**  >  **strumenti**installati, quindi scegliere **funzioni di Azure e strumenti per processi Web**.

    ![Verificare la versione degli strumenti di Funzioni](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Prendere nota della **versione** installata e confrontare questa versione con la versione più recente elencata nelle [note](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md)sulla versione. 

1. Se la versione è precedente, aggiornare gli strumenti in Visual Studio come illustrato nella sezione seguente.

### <a name="update-your-tools-in-visual-studio-2017"></a>Aggiornare gli strumenti in Visual Studio 2017

1. Nella finestra di dialogo **Estensioni e aggiornamenti** espandere **Aggiornamenti** > **Visual Studio Marketplace**, scegliere **Funzioni di Azure e strumenti per processi Web** e selezionare **Aggiorna**.

    ![Aggiornare la versione degli strumenti di Funzioni](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Dopo aver scaricato l'aggiornamento degli strumenti, selezionare **Chiudi**e quindi chiudere Visual Studio per attivare il programma di installazione degli strumenti di aggiornamento con VSIX.

1. Nel programma di installazione VSIX scegliere **modifica** per aggiornare gli strumenti. 

1. Al termine dell'aggiornamento, scegliere **Chiudi**, quindi riavviare Visual Studio.

> [!NOTE]  
> In Visual Studio 2019 e versioni successive l'estensione strumenti di funzioni di Azure viene aggiornata come parte di Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Creare un progetto di Funzioni di Azure

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Dopo aver creato un progetto di funzioni di Azure, il modello di progetto crea un progetto C#, installa il `Microsoft.NET.Sdk.Functions` pacchetto NuGet e imposta il Framework di destinazione. Il nuovo progetto contiene i file seguenti:

* **host.json**: consente di configurare l'host di Funzioni. Queste impostazioni si applicano sia durante l'esecuzione in locale che nell'esecuzione in Azure. Per altre informazioni, vedere il [riferimento su host.json](functions-host-json.md).

* **local.settings.json**: mantiene le impostazioni usate quando si esegue Funzioni localmente. Queste impostazioni non vengono usate durante l'esecuzione in Azure. Per ulteriori informazioni, vedere [file di impostazioni locali](#local-settings-file).

    >[!IMPORTANT]
    >Poiché il local.settings.jsnel file può contenere segreti, è necessario escluderlo dal controllo del codice sorgente del progetto. Verificare che l'impostazione **copia in directory di output** per questo file sia impostata su **copia se più recente**. 

Per altre informazioni, vedere [Progetto di libreria di classi per Funzioni](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Visual Studio non carica automaticamente le impostazioni in local.settings.jsquando si pubblica il progetto. Per assicurarsi che queste impostazioni esistano anche nell'app per le funzioni in Azure, caricarle dopo aver pubblicato il progetto. Per altre informazioni, vedere [impostazioni dell'app per le funzioni](#function-app-settings). I valori in una `ConnectionStrings` raccolta non vengono mai pubblicati.

Il codice può anche leggere i valori delle impostazioni dell'app per le funzioni come variabili di ambiente. Per altre informazioni, vedere [variabili di ambiente](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-the-project-for-local-development"></a>Configurare il progetto per lo sviluppo locale

Il runtime di Funzioni usa un account di archiviazione di Azure internamente. Per tutti i tipi di trigger diversi da HTTP e webhook, impostare la `Values.AzureWebJobsStorage` chiave su una stringa di connessione dell'account di archiviazione di Azure valida. L'app per le funzioni può usare anche l' [emulatore di archiviazione di Azure](../storage/common/storage-use-emulator.md) per l' `AzureWebJobsStorage` impostazione di connessione richiesta dal progetto. Per usare l'emulatore, impostare il valore di `AzureWebJobsStorage` su `UseDevelopmentStorage=true` . Modificare questa impostazione in una stringa di connessione dell'account di archiviazione effettiva prima della distribuzione.

Per impostare la stringa di connessione dell'account di archiviazione:

1. In Visual Studio selezionare **Visualizza**  >  **Cloud Explorer**.

2. In **Cloud Explorer**, espandere **account di archiviazione**e quindi selezionare l'account di archiviazione. Nella scheda **Proprietà** copiare il valore della **stringa di connessione primaria** .

2. Nel progetto aprire il local.settings.jsnel file e impostare il valore della `AzureWebJobsStorage` chiave sulla stringa di connessione copiata.

3. Ripetere il passaggio precedente per aggiungere chiavi univoche alla `Values` matrice per qualsiasi altra connessione richiesta dalle funzioni. 

## <a name="add-a-function-to-your-project"></a>Aggiungere una funzione al progetto

Nelle funzioni della libreria di classi C#, le associazioni usate dalla funzione sono definite applicando attributi nel codice. Quando si creano trigger di funzione dai modelli forniti, vengono applicati gli attributi del trigger. 

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Aggiungi**  >  **nuovo elemento**. 

2. Selezionare **funzione di Azure**, immettere un **nome** per la classe e quindi selezionare **Aggiungi**.

3. Scegliere il trigger, impostare le proprietà di binding e quindi fare clic su **OK**. Nell'esempio seguente vengono illustrate le impostazioni per la creazione di una funzione trigger di archiviazione code. 

    ![Creare una funzione trigger di archiviazione code](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Questo esempio di trigger usa una stringa di connessione con una chiave denominata `QueueStorage` . Definire questa impostazione della stringa di connessione nella [local.settings.jssu file](functions-run-local.md#local-settings-file).

4. Si esamini la classe appena aggiunta. Viene visualizzato un `Run()` metodo statico attribuito all' `FunctionName` attributo. Questo attributo indica che il metodo è il punto di ingresso per la funzione.

    La classe C# seguente, ad esempio, rappresenta una funzione trigger di archiviazione code di base:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

Un attributo specifico dell'associazione viene applicato a ogni parametro di associazione fornito al metodo del punto di ingresso. L'attributo accetta le informazioni di associazione come parametri. Nell'esempio precedente, al primo parametro è applicato un `QueueTrigger` attributo, che indica una funzione trigger di archiviazione di Accodamento. Il nome della coda e il nome dell'impostazione della stringa di connessione vengono passati come parametri all' `QueueTrigger` attributo. Per altre informazioni, vedere [Associazioni di archiviazione code in Funzioni di Azure](functions-bindings-storage-queue-trigger.md).

Usare la procedura precedente per aggiungere altre funzioni al progetto dell'app per le funzioni. Ogni funzione nel progetto può avere un trigger diverso, ma una funzione deve avere esattamente un trigger. Per altre informazioni, vedere [concetti relativi a trigger e associazioni di funzioni di Azure](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Aggiungere associazioni

Come nel caso dei trigger, le associazioni di input e output vengono aggiunte alla funzione come attributi di associazione. Per aggiungere associazioni a una funzione, procedere come segue:

1. Assicurarsi [di aver configurato il progetto per lo sviluppo locale](#configure-the-project-for-local-development).

2. Aggiungere il pacchetto di estensione NuGet appropriato per l'associazione specifica. 

   Per altre informazioni, vedere [libreria di classi C# con Visual Studio](./functions-bindings-register.md#local-csharp). Trovare i requisiti dei pacchetti NuGet specifici dell'associazione nell'articolo di riferimento per l'associazione. I requisiti del pacchetto per il trigger di Hub eventi sono ad esempio disponibili nell'[articolo di riferimento sulle associazioni di Hub eventi](functions-bindings-event-hubs.md).

3. Se sono presenti impostazioni dell'applicazione necessarie per l'associazione, aggiungerle alla `Values` raccolta nel [file delle impostazioni locali](functions-run-local.md#local-settings-file). 

   La funzione usa questi valori quando viene eseguita localmente. Quando la funzione viene eseguita nell'app per le funzioni in Azure, USA le [impostazioni dell'app](#function-app-settings)per le funzioni.

4. Aggiungere l'attributo di associazione appropriato per la firma del metodo. Nell'esempio seguente un messaggio della coda attiva la funzione e l'associazione di output crea un nuovo messaggio della coda con lo stesso testo in una coda diversa.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   La connessione all'archiviazione code viene ottenuta dall'impostazione `AzureWebJobsStorage`. Per altre informazioni, vedere l'articolo di riferimento per l'associazione specifica. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Test delle funzioni

Azure Functions Core Tools consente di eseguire il progetto Funzioni di Azure nel computer di sviluppo locale. Per ulteriori informazioni, vedere [utilizzare Azure Functions Core Tools](functions-run-local.md). Viene richiesto di installare questi strumenti la prima volta che si avvia una funzione da Visual Studio. 

Per testare la funzione in Visual Studio:

1. Premere F5. Se viene visualizzata, accettare la richiesta di Visual Studio di scaricare e installare gli strumenti dell'interfaccia della riga di comando Azure Functions Core Tools. Potrebbe essere necessario anche abilitare un'eccezione del firewall per consentire agli strumenti di gestire le richieste HTTP.

2. Con il progetto in esecuzione, testare il codice come si testa una funzione distribuita. 

   Per altre informazioni, vedere [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md). Quando si esegue Visual Studio in modalità di debug, i punti di interruzione vengono raggiunti come previsto.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Pubblicazione in Azure

Quando si esegue la pubblicazione da Visual Studio, viene usato uno dei due metodi di distribuzione seguenti:

* [Distribuzione Web](functions-deployment-technologies.md#web-deploy-msdeploy): esegue il pacchetto e distribuisce le app di Windows in qualsiasi server IIS.
* Distribuzione [zip con esecuzione da pacchetto abilitata](functions-deployment-technologies.md#zip-deploy): consigliato per le distribuzioni di funzioni di Azure.

Usare la procedura seguente per pubblicare il progetto in un'app per le funzioni in Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Impostazioni dell'app per le funzioni

Poiché Visual Studio non carica queste impostazioni automaticamente quando si pubblica il progetto, è necessario aggiungere anche all'app per le funzioni in Azure anche le impostazioni aggiunte nella local.settings.js.

Il modo più semplice per caricare le impostazioni necessarie nell'app per le funzioni in Azure consiste nel selezionare il collegamento **gestisci app Azure impostazioni servizio** visualizzato dopo la pubblicazione corretta del progetto.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="Impostazioni nella finestra di pubblicazione":::

Selezionando questo collegamento viene visualizzata la finestra di dialogo **Impostazioni applicazione** per l'app per le funzioni, in cui è possibile aggiungere nuove impostazioni dell'applicazione o modificare quelle esistenti.

![Impostazioni applicazione](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** Visualizza un valore di impostazione nel local.settings.jssu file e **Remote** Visualizza un valore di impostazione corrente nell'app per le funzioni in Azure. Scegliere **Aggiungi impostazione** per creare una nuova impostazione dell'app. Usare il collegamento **Inserisci valore da locale** per copiare un valore di impostazione nel campo **Remoto**. Le modifiche in sospeso vengono scritte nel file delle impostazioni locali e nell'app per le funzioni quando si seleziona **OK**.

> [!NOTE]
> Per impostazione predefinita, il local.settings.jsnel file non viene archiviato nel controllo del codice sorgente. Ciò significa che se si clona un progetto di funzioni locali dal controllo del codice sorgente, il progetto non dispone di un local.settings.jssu file. In questo caso, è necessario creare manualmente il local.settings.jsnel file nella radice del progetto, in modo che la finestra di dialogo **Impostazioni applicazione** funzioni come previsto. 

È anche possibile gestire le impostazioni dell'applicazione in questi altri modi:

* [Usare il portale di Azure](functions-how-to-use-azure-function-app-settings.md#settings).
* [Usare l' `--publish-local-settings` opzione pubblica nel Azure Functions Core Tools](functions-run-local.md#publish).
* [Usare l'interfaccia della riga di comando di Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Monitoraggio delle funzioni

È consigliabile monitorare l'esecuzione delle funzioni tramite l'integrazione dell'app per le funzioni con Azure Application Insights. Quando si crea un'app per le funzioni nel portale di Azure, questa integrazione avviene automaticamente per impostazione predefinita. Tuttavia, quando si crea l'app per le funzioni durante la pubblicazione di Visual Studio, l'integrazione nell'app per le funzioni in Azure non viene eseguita.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla Azure Functions Core Tools, vedere [utilizzare Azure Functions Core Tools](functions-run-local.md).

Per altre informazioni sullo sviluppo di funzioni come librerie di classi .NET, vedere [riferimento per sviluppatori C# in funzioni di Azure](functions-dotnet-class-library.md). Questo articolo include anche collegamenti a esempi d'uso degli attributi per dichiarare i vari tipi di associazioni supportati da Funzioni di Azure.    
