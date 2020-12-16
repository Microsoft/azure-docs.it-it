---
title: 'Avvio rapido: Libreria client di Content Moderator per .NET'
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come iniziare a usare la libreria client di Azure Content Moderator per .NET. È possibile integrare un software di filtro dei contenuti nell'app per garantire la conformità con le normative o mantenere l'ambiente previsto per gli utenti.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: 8a715c23900ac0d5a16ff4a0a8ade5ea9458cfed
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561495"
---
Introduzione alla libreria client di Azure Content Moderator per .NET. Seguire questi passaggi per installare il pacchetto NuGet e provare il codice di esempio per le attività di base. 

Content Moderator è un servizio di intelligenza artificiale che consente di gestire i contenuti potenzialmente offensivi, rischiosi o in altro modo indesiderati. Usare il servizio di moderazione del contenuto basato sull'intelligenza artificiale per analizzare testo, immagini e video e applicare automaticamente i flag di contenuto. Integrare quindi l'app con lo strumento di revisione, un ambiente di moderazione online per un team di revisori umani. È possibile integrare un software di filtro dei contenuti nell'app per garantire la conformità con le normative o mantenere l'ambiente previsto per gli utenti.

Usare la libreria client di Content Moderator per .NET per:

* Moderare il testo
* Moderare le immagini
* Creare una revisione

[Documentazione di riferimento](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [Esempi](../../samples-dotnet.md)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [IDE di Visual Studio](https://visualstudio.microsoft.com/vs/) o la versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="creare una risorsa Content Moderator"  target="_blank">creare una risorsa Content Moderator <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione a Content Moderator. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

#### <a name="visual-studio-ide"></a>[IDE di Visual Studio](#tab/visual-studio)

Creare un'applicazione .NET Core con Visual Studio. 

### <a name="install-the-client-library"></a>Installare la libreria client 

Dopo aver creato un nuovo progetto, installare la libreria client facendo clic con il pulsante destro del mouse sulla soluzione del progetto in **Esplora soluzioni** e scegliendo **Gestisci pacchetti NuGet**. Nella finestra di dialogo Gestione pacchetti visualizzata selezionare **Sfoglia**, **Includi versione preliminare** e cercare `Microsoft.Azure.CognitiveServices.ContentModerator`. Selezionare la versione `2.0.0`, quindi **Installa**. 

#### <a name="cli"></a>[CLI](#tab/cli)

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `content-moderator-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
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

All'interno della directory dell'applicazione, installare la libreria client di Content Moderator per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs), che contiene gli esempi di codice di questo argomento.

Dalla directory del progetto aprire il file *Program.cs* nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere le istruzioni `using` seguenti:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

Nella classe **Program** creare le variabili per l'endpoint e la chiave della risorsa.

> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa Content Moderator creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. La chiave e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. 
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](../../../cognitive-services-security.md) di Servizi cognitivi.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


Nel metodo `main()` dell'applicazione aggiungere le chiamate per i metodi usati in questa guida di avvio rapido. Verranno create in seguito.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>Modello a oggetti

Le classi seguenti gestiscono alcune delle principali funzionalità della libreria client di Content Moderator per .NET.

|Nome|Descrizione|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Questa classe è necessaria per tutte le funzionalità di Content Moderator. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi.|
|[ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Questa classe fornisce la funzionalità per analizzare le immagini e individuare contenuti per adulti, informazioni personali o visi umani.|
|[TextModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Questa classe fornisce la funzionalità per analizzare il testo e individuare lingua, volgarità, errori e informazioni personali.|
|[Revisioni](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Questa classe fornisce la funzionalità delle API di revisione, inclusi i metodi per la creazione di processi, flussi di lavoro personalizzati e revisioni umane.|

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Content Moderator per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Moderare il testo](#moderate-text)
* [Moderare le immagini](#moderate-images)
* [Creare una revisione](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticare il client

In un nuovo metodo creare un'istanza di oggetti client con l'endpoint e la chiave.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>Moderare il testo

Il codice seguente usa un client di Content Moderator per analizzare un corpo di testo e stampare i risultati nella console. Nella radice della classe **Program** definire i file di input e di output:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Nella radice del progetto aggiungere un file *TextFile.txt*. Aggiungere un testo personalizzato in questo file oppure usare il testo di esempio seguente:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


Definire quindi il metodo di moderazione del testo in un punto qualsiasi della classe **Program**:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Moderare le immagini

Il codice seguente usa un client di Content Moderator, insieme a un oggetto [ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet), per analizzare le immagini remote e verificare la presenza di contenuti per adulti e spinti.

> [!NOTE]
> È anche possibile analizzare il contenuto di un'immagine locale. Vedere la [documentazione di riferimento](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) per i metodi e le operazioni che funzionano con le immagini locali.

### <a name="get-sample-images"></a>Recupera immagini di esempio

Nella radice della classe **Program** definire i file di input e di output:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Creare quindi il file di input, *ImageFiles.txt* nella radice del progetto. In questo file aggiungere gli URL delle immagini da analizzare, inserendo un URL in ogni riga. È possibile usare le immagini di esempio seguenti:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definire la classe helper

Aggiungere la definizione di classe seguente all'interno della classe **Program**. Questa classe interna gestirà i risultati della moderazione delle immagini.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definire il metodo di moderazione delle immagini

Il metodo seguente consente di scorrere gli URL delle immagini in un file di testo, di creare un'istanza di **EvaluationData** e di analizzare l'immagine per rilevare la presenza di contenuto per adulti o spinti, testo e visi umani. Aggiunge quindi l'istanza finale di **EvaluationData** a un elenco e scrive l'elenco completo dei dati restituiti nella console.

#### <a name="iterate-through-images"></a>Eseguire l'iterazione tra le immagini

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analizzare il contenuto

Per altre informazioni sugli attributi dell'immagine verificati da Content Moderator, vedere la [guida concettuale alla moderazione di immagini](../../image-moderation-api.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Scrivere i risultati della moderazione in un file

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Creare una revisione

È possibile usare la libreria client di Content Moderator per .NET per inserire contenuti nello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), in modo che i moderatori possano esaminarlo. Per altre informazioni sullo strumento di revisione, vedere la [Guida concettuale dello strumento di revisione](../../review-tool-user-guide/human-in-the-loop.md).

Il metodo di questa sezione usa la classe [Reviews](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) per creare una revisione, recuperare il relativo ID e controllarne i dettagli dopo aver ricevuto input degli utenti tramite il portale Web dello strumento di revisione. Registra tutte queste informazioni in un file di testo di output. 

### <a name="get-sample-images"></a>Recupera immagini di esempio

Dichiarare la matrice seguente nella radice della classe **Program**. Questa variabile fa riferimento a un'immagine di esempio da usare per creare la revisione.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Ottenere le credenziali per la revisione

Accedere allo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) e recuperare il nome del proprio team. Quindi assegnarlo alla variabile appropriata nella classe **Program**. Facoltativamente, è possibile configurare un endpoint di callback per ricevere aggiornamenti sull'attività della revisione.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definire la classe helper

Aggiungere la definizione di classe seguente all'interno della classe **Program**. Questa classe verrà usata per rappresentare una singola istanza di revisione inviata allo strumento di revisione.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definire il metodo helper

Aggiungere il metodo seguente alla classe **Program**. Questo metodo scriverà i risultati delle query di revisione nel file di testo di output.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definire il metodo di creazione della revisione

A questo punto si è pronti per definire il metodo che gestirà la creazione della revisione e l'esecuzione di query. Aggiungere un nuovo metodo, **CreateReviews** e definire le variabili locali seguenti.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Inviare le revisioni allo strumento di revisione

Aggiungere quindi il codice seguente per scorrere le immagini di esempio specificate, aggiungere metadati e inviarle allo strumento di revisione in un singolo batch. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

L'oggetto restituito dalla chiamata API conterrà valori ID univoci per ogni immagine caricata. Il codice seguente analizza questi ID e li usa per eseguire query in Content Moderator per lo stato di ogni immagine nel batch.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Ottenere i dettagli della revisione

Il codice seguente fa in modo che il programma attenda l'input dell'utente. Quando si arriva a questo passaggio in fase di esecuzione, è possibile passare allo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) manualmente, verificare che l'immagine di esempio sia stata caricata e interagire con essa. Per informazioni su come interagire con una revisione, vedere la [Guida pratica per le revisioni](../../review-tool-user-guide/review-moderated-images.md). Al termine, è possibile premere un tasto qualsiasi per continuare con il programma e recuperare i risultati del processo di revisione.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Se in questo scenario è stato usato un endpoint di callback, si dovrebbe ricevere un evento nel formato seguente:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

#### <a name="visual-studio-ide"></a>[IDE di Visual Studio](#tab/visual-studio)

Eseguire l'applicazione facendo clic sul pulsante **Debug** nella parte superiore della finestra dell'ambiente di sviluppo integrato.

#### <a name="cli"></a>[CLI](#tab/cli)

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare la libreria .NET di Content Moderator per eseguire attività di moderazione. Per altre informazioni sulla moderazione di immagini o di altri contenuti multimediali, è possibile leggere una guida concettuale.

> [!div class="nextstepaction"]
> [Concetti di moderazione delle immagini](../../image-moderation-api.md)

* [Che cos'è Azure Content Moderator?](../../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs).