---
title: includere file
description: includere file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: fd47d5df053931c343fd811fe4d93b66f080f225
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947350"
---
Usare le librerie client di LUIS (Language Understanding) per .NET per:
* Creare un'app
* Aggiungere una finalità, un'entità basata Machine Learning, con un'espressione di esempio
* Eseguire il training e pubblicare l'app
* Eseguire query sul runtime di previsione

[Documentazione di riferimento](/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding) | [Codice sorgente della libreria di creazione](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) e [previsione](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [NuGet di creazione](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) e [previsione](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [Esempio in C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/sdk-3x//Program.cs)

## <a name="prerequisites"></a>Prerequisiti

* La versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) e dell'[interfaccia della riga di comando di .NET Core](/dotnet/core/tools/).
* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Dopo aver creato la sottoscrizione di Azure, [creare una risorsa di creazione Language Understanding](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa [creata](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) sono necessari per connettere l'applicazione alla creazione di Language Understanding. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido. Per provare il servizio, è possibile usare il piano tariffario gratuito (`F0`).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core nell'ambiente di sviluppo integrato o nell'editor preferito.

1. Nella finestra di una console (ad esempio cmd, PowerShell o Bash) usare il comando dotnet `new` per creare una nuova app console con il nome `language-understanding-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Spostarsi nella cartella dell'app appena creata.

    ```dotnetcli
    cd language-understanding-quickstart
    ```

1. È possibile compilare l'applicazione con il comando seguente:

    ```dotnetcli
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

### <a name="install-the-nuget-libraries"></a>Installare le librerie NuGet

Nella directory dell'applicazione installare le librerie client di LUIS (Language Understanding) per .NET con i comandi seguenti:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.2.0-preview.3
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.1.0-preview.1
```

## <a name="authoring-object-model"></a>Modello a oggetti di creazione

Il client di creazione di Language Understanding (LUIS) è un oggetto [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient) contenente la chiave di creazione che esegue l'autenticazione in Azure.

## <a name="code-examples-for-authoring"></a>Esempi di codice per la creazione

Al termine della creazione del client, usare il client per accedere alle funzionalità, tra cui:

* App: [creazione](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync), [eliminazione](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync), [pubblicazione](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync)
* Espressioni di esempio: [aggiunta](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions), [eliminazione in base a ID](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync)
* Funzionalità: gestione di [elenchi di frasi](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync)
* Modello: gestione di [finalità](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions) ed entità
* Criterio: gestione di [criteri](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions)
* Training: [training](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync) dell'app e polling per lo [stato del training](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync)
* [Versioni](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions): gestione con clonazione, esportazione ed eliminazione

## <a name="prediction-object-model"></a>Modello a oggetti di previsione

Il client del runtime di previsioni di LUIS (Language Understanding) è un oggetto [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient) che esegue l'autenticazione in Azure e contiene la chiave della risorsa.

## <a name="code-examples-for-prediction-runtime"></a>Esempi di codice per il runtime di previsione

Al termine della creazione del client, usare il client per accedere alle funzionalità, tra cui:

* Previsioni per [slot di staging o produzione](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync)
* Previsioni per [versione](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync)


[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Dalla directory del progetto aprire il file *Program.cs* nell'ambiente di sviluppo integrato o nell'editor preferito. Sostituire il codice `using` esistente con le direttive `using` seguenti:

[!code-csharp[Add NuGet libraries to code file](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Aggiungere il codice boilerplate

1. Cambiare la firma del metodo `Main` per consentire chiamate asincrone:

    ```csharp
    public static async Task Main()
    ```

1. Aggiungere il resto del codice nel metodo `Main` della classe `Program` se non diversamente specificato.

## <a name="create-variables-for-the-app"></a>Creare variabili per l'app

Creare due set di variabili: il primo set modificato, il secondo set viene lasciato come visualizzato nell'esempio di codice. 

1. Creare le variabili in cui contenere la chiave di creazione e i nomi delle risorse.

    [!code-csharp[Variables you need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouChange)]

1. Creare le variabili in cui contenere gli endpoint, il nome dell'app, la versione e il nome della finalità.

    [!code-csharp[Variables you don't need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un oggetto [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials) con la chiave e usarlo con l'endpoint per creare un oggetto [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient).

[!code-csharp[Authenticate the client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Creare un'app LUIS

Un'app LUIS contiene il modello di elaborazione del linguaggio naturale (NLP) che include finalità, entità ed espressioni di esempio.

Creare un oggetto [ApplicationCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject). Il nome e le impostazioni cultura della lingua sono proprietà obbligatorie. Chiamare il metodo [Apps.AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync). La risposta corrisponde all'ID app.

[!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Creare la finalità per l'app
L'oggetto primario in un modello di app LUIS è la finalità. La finalità è allineata a un raggruppamento di _intenzioni_ delle espressioni dell'utente. Un utente può porre una domanda o fare un'affermazione per una risposta _con finalità_ specifica da un bot o da un'altra applicazione client. Alcuni esempi di intenzioni sono la prenotazione di un volo, la richiesta di informazioni meteo per una città di destinazione e la richiesta di informazioni di contatto per il servizio clienti.

Creare un oggetto [ModelCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject) con il nome della finalità univoca, quindi passare l'ID app, l'ID versione e l'oggetto ModelCreateObject al metodo [Model.AddIntentAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync). La risposta corrisponde all'ID finalità.

Il valore `intentName` è hardcoded per `OrderPizzaIntent` come parte delle variabili descritte nella sezione [Creare variabili per l'app](#create-variables-for-the-app).

[!code-csharp[Create intent for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Creare le entità per l'app

Sebbene le entità non siano obbligatorie, sono in genere presenti nella maggior parte delle app. L'entità estrae le informazioni necessarie per soddisfare l'intenzione dell'utente dall'espressione dell'utente. Sono disponibili numerosi tipi di entità [predefinite](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync) e personalizzate, ognuna con i propri modelli DTO (Data Transformation Object).  Le entità predefinite comuni da aggiungere all'app includono [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

È importante tenere presente che le entità non sono contrassegnate con una finalità. Possono in genere essere applicate a molte finalità. Solo le espressioni utente di esempio sono contrassegnate per una singola finalità specifica.

I metodi di creazione per le entità sono parte della classe [Model](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions). Ogni tipo di entità dispone di un modello DTO (Data Transformation Object) specifico, in genere contenente la parola `model` nello spazio dei nomi [Models](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models).

Il codice di creazione entità crea un'entità basata su Machine Learning con le sottoentità e le funzionalità applicate alle sottoentità `Quantity`.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Screenshot parziale del portale che mostra l'entità creata, un'entità di Machine Learning con le sottoentità e le funzionalità applicate alle sottoentità 'Quantity'.":::

[!code-csharp[Create entities for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddEntities)]

Usare il metodo seguente per la classe per individuare l'ID della sottoentità Quantity, in modo da assegnare le funzionalità a tale sottoentità.

[!code-csharp[Find subentity id](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Aggiungere un'espressione di esempio alla finalità

Per determinare l'intenzione di un'espressione ed estrarre le entità, l'app necessita di esempi di espressioni. Gli esempi devono fare riferimento a un'unica finalità specifica e contrassegnare tutte le entità personalizzate. Non è necessario contrassegnare le entità predefinite.

Aggiungere le espressioni di esempio creando un elenco di oggetti [ExampleLabelObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject), un oggetto per ogni espressione di esempio. Ogni esempio deve contrassegnare tutte le entità con un dizionario di coppie nome/valore con nome dell'entità e valore dell'entità. Il valore dell'entità deve corrispondere esattamente a quanto visualizzato nel testo dell'espressione di esempio.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Screenshot parziale che mostra l'espressione di esempio etichettata nel portale. ":::

Chiamare [Examples.AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions) con l'ID app, l'ID versione e l'esempio.

[!code-csharp[Add example utterance to intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Eseguire il training dell'app

Dopo aver creato il modello, è necessario eseguire il training dell'app LUIS per questa versione del modello. Un modello sottoposto a training può essere usatoin un [contenitore](../luis-container-howto.md) o [pubblicato](../luis-how-to-publish-app.md) negli slot di staging o di produzione.

Il metodo [Train.TrainVersionAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions) richiede l'ID app e l'ID versione.

Il training di un modello di dimensioni ridotte, come quello mostrato in questa esercitazione di avvio rapido, verrà eseguito molto rapidamente. Per le applicazioni a livello di produzione, il training dell'app deve includere una chiamata di polling al metodo [GetStatusAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) per determinare quando o se il training ha avuto esito positivo. La risposta è un elenco di oggetti [ModelTrainingInfo](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo) con uno stato separato per ogni oggetto. Affinché il training venga considerato completato, è necessario che tutti gli oggetti abbiano esito positivo.

[!code-csharp[Train the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Pubblicare l'app nello slot di produzione

Pubblicare l'app LUIS con il metodo [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync). Questo metodo consente di pubblicare la versione con training corrente nello slot specificato nell'endpoint. L'applicazione client usa questo endpoint per inviare espressioni utente per la previsione della finalità e l'estrazione di entità.

[!code-csharp[Publish app to production slot](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>Autenticare il client del runtime di previsione

Usare un oggetto [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials) con la chiave e con l'endpoint per creare un oggetto [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient).

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-csharp[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PredictionCreateClient)]


## <a name="get-prediction-from-runtime"></a>Ottenere previsioni dal runtime

Aggiungere il codice seguente per creare la richiesta al runtime di previsione.

L'espressione utente fa parte dell'oggetto [PredictionRequest](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest).

Per soddisfare la richiesta, il metodo **GetSlotPredictionAsync** richiede diversi parametri, ad esempio l'ID app, il nome dello slot e l'oggetto richiesta di previsioni. Le altre opzioni, ad esempio per l'output dettagliato, la visualizzazione di tutte le finalità e il log, sono facoltative.

[!code-csharp[Get prediction from runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `dotnet run` dalla directory dell'applicazione.

```dotnetcli
dotnet run
```
