---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 08/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ba09deda155ac446693a7e1037390a3f1fd2700f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919379"
---
Questo articolo fornisce informazioni e codice di esempio per iniziare a usare la libreria client di Visione personalizzata con C# per creare un modello di classificazione immagini. Dopo la creazione, è possibile aggiungere tag, caricare immagini, eseguire il training del progetto, ottenere l'URL dell'endpoint predefinito per la stima del progetto e usare l'endpoint per un test a livello di codice dell'immagine. Usare questo esempio come modello per la creazione di un'applicazione .NET personalizzata. Se si preferisce eseguire la procedura di creazione e utilizzo di un modello di classificazione _senza_ codice, vedere le [indicazioni basate su browser](../../getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prerequisites

- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Ottenere la libreria client di Visione personalizzata e il codice di esempio

Per scrivere un'app .NET che usa Visione personalizzata saranno necessari i pacchetti NuGet di Visione personalizzata. Questi pacchetti sono inclusi nel progetto di esempio che si scaricherà, ma è possibile accedervi singolarmente qui.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clonare o scaricare il progetto [Cognitive Services .NET Samples](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Passare alla cartella **CustomVision/ImageClassification** e aprire _ImageClassification.csproj_ in Visual Studio.

Questo progetto di Visual Studio crea un nuovo progetto di Visione personalizzata denominato __My New Project__, accessibile tramite il [sito Web di Visione personalizzata](https://customvision.ai/). Quindi carica le immagini per eseguire il training e testare un classificatore. In questo progetto il classificatore ha lo scopo di determinare se un albero è un __abete canadese__ o un __ciliegio giapponese__.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="understand-the-code"></a>Informazioni sul codice

Aprire il file _Program.cs_ ed esaminare il codice. [Creare variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per le chiavi di training e di stima denominate rispettivamente `CUSTOM_VISION_TRAINING_KEY` e `CUSTOM_VISION_PREDICTION_KEY`. Lo script cercherà queste variabili.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Inoltre, ottenere l'URL dell'endpoint dalla pagina Impostazioni del sito Visione personalizzata. Salvarlo in una variabile di ambiente denominata `CUSTOM_VISION_ENDPOINT`. Lo script salva un riferimento nella radice della classe.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Le righe di codice seguenti eseguono la funzionalità principale del progetto.

### <a name="create-a-new-custom-vision-service-project"></a>Creare un nuovo progetto di Servizio visione artificiale personalizzato

Il progetto creato verrà visualizzato nel [sito Web di Visione personalizzata](https://customvision.ai/) visitato in precedenza. Per specificare altre opzioni quando si crea il progetto, come illustrato in [Creare un classificatore](../../getting-started-build-a-classifier.md) nella guida al portale Web, vedere il metodo [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Creare tag nel progetto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Le immagini per questo progetto sono incluse. Sono referenziate nel metodo **LoadImagesFromDisk** in _Program.cs_. È possibile caricare fino a 64 immagini in un singolo batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Training del classificatore e pubblicazione

Questo codice crea la prima iterazione del modello di previsione e quindi la pubblica nell'endpoint di previsione. Per inviare richieste di previsione, è possibile usare il nome dell'iterazione. L'iterazione è disponibile nell'endpoint di stima solo dopo che è stata pubblicata.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Impostare l'endpoint di stima

L'endpoint di stima è il riferimento che è possibile usare per inviare un'immagine al modello corrente e ottenere una stima di classificazione.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Inviare un'immagine all'endpoint di stima predefinito

Questo script, l'immagine di test viene caricata nel metodo **LoadImagesFromDisk** e l'output delle stime del modello deve essere visualizzato nella console. Il valore della variabile `publishedModelName` deve corrispondere al valore di "Pubblicata come" disponibile nella scheda **Prestazioni** del portale di Visione personalizzata. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Quando l'applicazione viene eseguita, dovrebbe aprire una finestra della console e scrivere l'output seguente:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Si può quindi verificare che all'immagine di test (disponibile in **Images/Test/** ) siano stati applicati i tag appropriati. Premere un tasto qualsiasi per uscire dall'applicazione. È anche possibile tornare al [sito Web di Visione personalizzata](https://customvision.ai) e vedere lo stato corrente del progetto appena creato.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è appreso come eseguire ogni passaggio del processo di classificazione delle immagini nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](../../test-your-model.md)
