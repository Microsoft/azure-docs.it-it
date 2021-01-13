---
title: 'Avvio rapido: Libreria client di Visione artificiale per .NET'
description: Questo argomento di avvio rapido illustra come usare la libreria client di Visione artificiale per .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 1826edb31c442fb10b8053433ec01ee5abad0eb2
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109460"
---
<a name="HOLTop"></a>

Usare la libreria client di Visione artificiale per:

* Analizzare un'immagine per trovare tag, descrizioni di testo, visi, contenuto per adulti e altro ancora.
* Eseguire la lettura del testo stampato e scritto a mano con l'API di lettura.

[Documentazione di riferimento](/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Esempi](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [IDE di Visual Studio](https://visualstudio.microsoft.com/vs/) o la versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creare una risorsa Visione artificiale"  target="_blank">creare una risorsa Visione artificiale <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione al servizio Visione artificiale. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

#### <a name="visual-studio-ide"></a>[IDE di Visual Studio](#tab/visual-studio)

Creare un'applicazione .NET Core con Visual Studio. 

### <a name="install-the-client-library"></a>Installare la libreria client 

Dopo aver creato un nuovo progetto, installare la libreria client facendo clic con il pulsante destro del mouse sulla soluzione del progetto in **Esplora soluzioni** e scegliendo **Gestisci pacchetti NuGet**. Nella finestra di dialogo Gestione pacchetti visualizzata selezionare **Sfoglia**, **Includi versione preliminare** e cercare `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`. Selezionare la versione `6.0.0-preview.1`, quindi **Installa**. 

#### <a name="cli"></a>[CLI](#tab/cli)

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `computer-vision-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```console
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installare la libreria client

Nella directory dell'applicazione installare la libreria client di Visione artificiale per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), che contiene gli esempi di codice di questo argomento.

Dalla directory del progetto aprire il file *ComputerVisionQuickstart.cs* nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere le direttive `using` seguenti:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

Nella classe **Program** dell'applicazione creare le variabili per l'endpoint e per la chiave di Azure della risorsa.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa Visione artificiale creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. La chiave e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. 
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](../../../cognitive-services-security.md) di Servizi cognitivi.

Nel metodo `Main` dell'applicazione aggiungere le chiamate per i metodi usati in questa guida di avvio rapido. Verranno create in seguito.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Il client è stato configurato](?success=set-up-client#object-model) [Si è verificato un problema](https://www.research.net/r/7QYZKHL?issue=set-up-client)

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità dell'SDK Visione artificiale per .NET.

|Nome|Descrizione|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Questa classe è necessaria per tutte le funzionalità di Visione artificiale. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per eseguire la maggior parte delle operazioni con le immagini.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Questa classe contiene metodi aggiuntivi per **ComputerVisionClient.**|
|[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Questa enumerazione definisce i diversi tipi di analisi delle immagini che è possibile eseguire in un'operazione di analisi standard. Specificare un set di valori di VisualFeatureTypes a seconda delle esigenze. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Visione artificiale per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Analizzare un'immagine](#analyze-an-image)
* [Leggere il testo stampato e scritto a mano](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questa guida di avvio rapido presuppone che siano state [create variabili di ambiente](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) per la chiave e l'endpoint del servizio Visione artificiale, denominate rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

In un nuovo metodo creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** con la chiave e usarlo con l'endpoint per creare un oggetto **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Il client è stato autenticato](?success=authenticate-client#analyze-an-image) [Si è verificato un problema](https://www.research.net/r/7QYZKHL?issue=authenticate-client)

## <a name="analyze-an-image"></a>Analizzare un'immagine

Il codice seguente definisce un metodo, `AnalyzeImageUrl`, che usa l'oggetto client per analizzare un'immagine remota e stampare i risultati. Il metodo restituisce una descrizione di testo, una categorizzazione, un elenco di tag, i visi rilevati, i flag di contenuto per adulti, i colori principali e il tipo di immagine.

> [!TIP]
> È anche possibile analizzare un'immagine locale. Vedere i metodi [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet), ad esempio **AnalyzeImageInStreamAsync**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).

### <a name="set-up-test-image"></a>Configurare l'immagine di test

Nella classe **Program** salvare un riferimento all'URL dell'immagine da analizzare.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>Specificare le caratteristiche visive

Definire il nuovo metodo per l'analisi delle immagini. Aggiungere il codice seguente, che specifica le caratteristiche visive da estrarre nell'analisi. Per un elenco completo, vedere l'enumerazione **[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

Inserire uno dei blocchi di codice seguenti nel metodo **AnalyzeImageUrl** per implementarne le funzionalità. Ricordarsi di aggiungere una parentesi quadra di chiusura alla fine.

```csharp
}
```

### <a name="analyze"></a>Analisi

Il metodo **AnalyzeImageAsync** restituisce un oggetto **ImageAnalysis** che contiene tutte le informazioni estratte.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

Le sezioni seguenti illustrano come analizzare queste informazioni in modo dettagliato.

### <a name="get-image-description"></a>Ottenere la descrizione dell'immagine

Il codice seguente ottiene l'elenco di didascalie generate per l'immagine. Per altre informazioni, vedere [Descrivere le immagini](../../concept-describing-images.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Ottenere la categoria dell'immagine

Il codice seguente ottiene la categoria rilevata dell'immagine. Per altre informazioni, vedere [Categorizzare le immagini](../../concept-categorizing-images.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Ottenere i tag delle immagini

Il codice seguente ottiene il set di tag rilevati nell'immagine. Per altre informazioni, vedere [Tag di contenuto](../../concept-tagging-images.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Rilevare oggetti

Il codice seguente rileva gli oggetti comuni nell'immagine e li stampa nella console. Per altre informazioni, vedere [Rilevamento di oggetti](../../concept-object-detection.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Rilevare marchi

Il codice seguente rileva i marchi e i logo aziendali nell'immagine e li stampa nella console. Per altre informazioni, vedere [Rilevamento di marchi](../../concept-brand-detection.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Rilevare visi

Il codice seguente restituisce i visi rilevati nell'immagine con le rispettive coordinate del rettangolo e determinati attributi del viso. Per altre informazioni, vedere [Rilevamento viso](../../concept-detecting-faces.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Rilevare contenuto per adulti, spinto o cruento

Il codice seguente stampa la presenza rilevata di contenuto per adulti nell'immagine. Per altre informazioni, vedere [Contenuto per adulti, spinto o cruento](../../concept-detecting-adult-content.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Ottenere la combinazione di colori dell'immagine

Il codice seguente stampa gli attributi di colore rilevati nell'immagine, ad esempio i colori dominanti e il colore principale. Per altre informazioni, vedere [Combinazioni di colori](../../concept-detecting-color-schemes.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Ottenere contenuto specifico del dominio

Visione artificiale può usare modelli speciali per eseguire un'ulteriore analisi delle immagini. Per altre informazioni, vedere [Contenuto specifico del dominio](../../concept-detecting-domain-content.md). 

Il codice seguente analizza i dati sulle celebrità rilevate nell'immagine.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

Il codice seguente analizza i dati sui luoghi di interesse rilevati nell'immagine.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Ottenere il tipo di immagine

Il codice seguente stampa informazioni sul tipo di immagine,&mdash;ad esempio se si tratta di ClipArt o di un disegno.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

> [!div class="nextstepaction"]
> [L'immagine è stata analizzata](?success=analyze-image#read-printed-and-handwritten-text) [Si è verificato un problema](https://www.research.net/r/7QYZKHL?issue=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Leggere il testo stampato e scritto a mano

Visione artificiale può leggere il testo visibile di un'immagine e convertirlo in un flusso di caratteri. Per altre informazioni sul riconoscimento del testo, vedere il documento concettuale [Riconoscimento ottico dei caratteri (OCR)](../../concept-recognizing-text.md#read-api). Il codice di questa sezione usa l'ultima versione dell'[SDK di Visione artificiale per Read 3.0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) e definisce un metodo, `BatchReadFileUrl`, che usa l'oggetto client per rilevare ed estrarre il testo nell'immagine.

> [!TIP]
> È anche possibile estrarre il testo da un'immagine locale. Vedere i metodi [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet), ad esempio **ReadInStreamAsync**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).

### <a name="set-up-test-image"></a>Configurare l'immagine di test

Nella classe **Program** salvare un riferimento all'URL dell'immagine da cui si vuole estrarre il testo. Questo frammento di codice include immagini di esempio per testo sia stampato che scritto a mano.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Chiamare l'API di lettura

Definire il nuovo metodo per la lettura del testo. Aggiungere il codice seguente, che chiama il metodo **ReadAsync** per l'immagine specificata. Viene restituito un ID operazione e viene avviato un processo asincrono per leggere il contenuto dell'immagine.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_url)]

### <a name="get-read-results"></a>Ottenere risultati della lettura

Ottenere quindi l'ID operazione restituito dalla chiamata a **ReadAsync** e usarlo per eseguire una query sul servizio per ottenere i risultati dell'operazione. Il codice seguente controlla l'operazione fino a quando non vengono restituiti i risultati. Stampa quindi i dati di testo estratti nella console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_response)]

### <a name="display-read-results"></a>Visualizzare i risultati della lettura

Aggiungere il codice seguente per analizzare e visualizzare i dati di testo recuperati e terminare la definizione del metodo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Il testo è stato letto](?success=read-printed-handwritten-text#run-the-application) [Si è verificato un problema](https://www.research.net/r/7QYZKHL?issue=read-printed-handwritten-text)

## <a name="run-the-application"></a>Eseguire l'applicazione

#### <a name="visual-studio-ide"></a>[IDE di Visual Studio](#tab/visual-studio)

Eseguire l'applicazione facendo clic sul pulsante **Debug** nella parte superiore della finestra dell'ambiente di sviluppo integrato.

#### <a name="cli"></a>[CLI](#tab/cli)

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```dotnet
dotnet run
```

---
> [!div class="nextstepaction"]
> [L'applicazione è stata eseguita](?success=run-the-application#clean-up-resources) [Si è verificato un problema](https://www.research.net/r/7QYZKHL?issue=run-the-application)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Le risorse sono state pulite](?success=clean-up-resources#next-steps) [Si è verificato un problema](https://www.research.net/r/7QYZKHL?issue=clean-up-resources) 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Informazioni di riferimento per l'API Visione artificiale (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)


* [Informazioni su Visione artificiale](../../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).