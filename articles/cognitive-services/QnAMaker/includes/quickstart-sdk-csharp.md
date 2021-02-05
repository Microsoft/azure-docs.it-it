---
title: 'Avvio rapido: Libreria client di QnA Maker per .NET'
description: Questo argomento di avvio rapido illustra come usare la libreria client di QnA Maker per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.  QnA Maker consente di sviluppare un servizio di domande e risposte a partire dal contenuto semistrutturato, ad esempio documenti con domande frequenti, URL e manuali di prodotti.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: d853f4729d126590df711554f60efe6d9f2194cc
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569447"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Usare la libreria client di QnA Maker per .NET per:

 * Creare una knowledge base
 * Aggiornare una knowledge base
 * Pubblicare una knowledge base
 * Ottenere la chiave dell'endpoint di runtime di previsione
 * Attendere un'attività a esecuzione prolungata
 * Scaricare una knowledge base
 * Ottenere una risposta da una knowledge base
 * Eliminare una knowledge base

[Documentazione di riferimento](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/2.0.1) | [Esempi C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Usare la libreria client di QnA Maker per .NET per:

 * Creare una knowledge base
 * Aggiornare una knowledge base
 * Pubblicare una knowledge base
 * Attendere un'attività a esecuzione prolungata
 * Scaricare una knowledge base
 * Ottenere una risposta da una knowledge base
 * Eliminare una knowledge base

[Documentazione di riferimento](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1) | [Esempi C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisiti

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* [IDE di Visual Studio](https://visualstudio.microsoft.com/vs/) o la versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Dopo aver creato la sottoscrizione di Azure, creare una [risorsa di QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) nel portale di Azure per ottenere la chiave di creazione e il nome della risorsa. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e il nome della risorsa creata sono necessari per connettere l'applicazione all'API QnA Maker. La chiave e il nome della risorsa verranno incollati nel codice seguente più avanti in questo argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* [IDE di Visual Studio](https://visualstudio.microsoft.com/vs/) o la versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Dopo aver creato la sottoscrizione di Azure, creare una [risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) nel portale di Azure per ottenere la chiave di creazione e l'endpoint.
    * NOTA:  assicurarsi di selezionare la casella di controllo **Gestita**.
    * Dopo la distribuzione della risorsa QnA Maker, fare clic su **Vai alla risorsa**. La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API QnA Maker. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

---

## <a name="setting-up"></a>Configurazione

### <a name="visual-studio-ide"></a>IDE di Visual Studio

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Usando Visual Studio creare un'applicazione .NET Core e installare la libreria client facendo clic con il pulsante destro del mouse sulla soluzione in **Esplora soluzioni** e scegliendo **Gestisci pacchetti NuGet**. Nella finestra di dialogo Gestione pacchetti visualizzata selezionare **Sfoglia** e cercare `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`. Selezionare la versione `2.0.1`, quindi **Installa**.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Usando Visual Studio creare un'applicazione .NET Core e installare la libreria client facendo clic con il pulsante destro del mouse sulla soluzione in **Esplora soluzioni** e scegliendo **Gestisci pacchetti NuGet**. Nella finestra di dialogo Gestione pacchetti visualizzata selezionare **Sfoglia**, **Includi versione preliminare** e cercare `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`. Selezionare la versione `3.0.0-preview.1`, quindi **Installa**.

---

### <a name="cli"></a>CLI

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `qna-maker-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un unico file di origine: *program.cs*.

```console
dotnet new console -n qna-maker-quickstart
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

Nella directory dell'applicazione installare la libreria client di QnA Maker per .NET con il comando seguente:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.1
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), che contiene gli esempi di codice di questo argomento.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs), che contiene gli esempi di codice di questo argomento.

---

### <a name="using-directives"></a>Direttive using

Dalla directory del progetto aprire il file *program.cs* e aggiungere le direttive `using` seguenti:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

---

### <a name="subscription-key-and-resource-endpoints"></a>Endpoint risorsa e chiave della sottoscrizione

Nel metodo `Main` dell'applicazione aggiungere le variabili e il codice, come illustrato nella sezione seguente, per usare le attività comuni di questo argomento di avvio rapido.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

> [!IMPORTANT]
> Passare al portale di Azure e individuare la chiave e l'endpoint per la risorsa QnA Maker creata nei prerequisiti. Si trovano nella pagina **Chiave ed endpoint** della risorsa, in **Gestione risorse**. Si usano la chiave di sottoscrizione e la chiave di creazione e modifica. Per ulteriori informazioni sulla creazione di una chiave, seguire le [chiavi in QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/azure-resources?tabs=v1#keys-in-qna-maker).

- Creare variabili di ambiente denominate QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT e QNA_MAKER_RUNTIME_ENDPOINT per archiviare questi valori.
- Il formato del valore di QNA_MAKER_ENDPOINT è `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- Il formato del valore di QNA_MAKER_RUNTIME_ENDPOINT è `https://YOUR-RESOURCE-NAME.azurewebsites.net`.
- Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Ad esempio, [Azure Key Vault](../../../key-vault/general/overview.md) fornisce una risorsa di archiviazione sicura per le chiavi.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

> [!IMPORTANT]
> Passare al portale di Azure e individuare la chiave e l'endpoint per la risorsa QnA Maker creata nei prerequisiti. Si trovano nella pagina **Chiave ed endpoint** della risorsa, in **Gestione risorse**. Si usano la chiave di sottoscrizione e la chiave di creazione e modifica. Per ulteriori informazioni sulla creazione di una chiave, seguire le [chiavi in QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/azure-resources?tabs=v2#keys-in-qna-maker-managed-preview).

- Creare variabili di ambiente denominate QNA_MAKER_SUBSCRIPTION_KEY e QNA_MAKER_ENDPOINT per archiviare questi valori.
- Il formato del valore di QNA_MAKER_ENDPOINT è `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Ad esempio, [Azure Key Vault](../../../key-vault/general/overview.md) fornisce una risorsa di archiviazione sicura per le chiavi.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

---


## <a name="object-models"></a>Modelli a oggetti

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[QnA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) usa due modelli a oggetti diversi:
* **[QnAMakerClient](#qnamakerclient-object-model)** è l'oggetto per creare, gestire, pubblicare e scaricare la knowledge base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** è l'oggetto per eseguire una query sulla knowledge base con l'API GenerateAnswer e inviare nuove domande suggerite usando l'API Train (come parte dell'[apprendimento attivo](../concepts/active-learning-suggestions.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[QnA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) usa il modello a oggetti seguente:
* **[QnAMakerClient](#qnamakerclient-object-model)** è l'oggetto per creare, gestire, pubblicare, scaricare ed eseguire query sulla knowledge base.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modello a oggetti QnAMakerClient

Il client di creazione di QnA Maker è un oggetto [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) che esegue l'autenticazione ad Azure con Microsoft.Rest.ServiceClientCredentials, che contiene la chiave.

Dopo la creazione del client, usare la proprietà [Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) per creare, gestire e pubblicare la knowledge base.

Gestire la knowledge base inviando un oggetto JSON. Per le operazioni immediate, un metodo restituisce in genere un oggetto JSON che indica lo stato. Per le operazioni a esecuzione prolungata, la risposta è l'ID operazione. Chiamare il metodo [client.Operations.GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync) con l'ID operazione per determinare lo [stato della richiesta](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype).

### <a name="qnamakerruntimeclient-object-model"></a>Modello a oggetti QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Il client di previsione di QnA Maker è un oggetto [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) che esegue l'autenticazione ad Azure con Microsoft.Rest.ServiceClientCredentials, che contiene la chiave di runtime di previsione, restituita dalla chiamata del client di creazione `client.EndpointKeys.GetKeys` dopo la pubblicazione della knowledge base.

Usare il metodo [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) per ottenere una risposta dal runtime di query.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Con una risorsa gestita di QnA Maker non è necessario usare l'oggetto **QnAMakerRuntimeClient**. Chiamare invece il metodo [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync).

---

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di QnA Maker per .NET:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

* [Autenticare il client di creazione](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Creare una knowledge base](#create-a-knowledge-base)
* [Aggiornare una knowledge base](#update-a-knowledge-base)
* [Scaricare una knowledge base](#download-a-knowledge-base)
* [Pubblicare una knowledge base](#publish-a-knowledge-base)
* [Eliminare una knowledge base](#delete-a-knowledge-base)
* [Ottenere una chiave di runtime di query](#get-query-runtime-key)
* [Ottenere lo stato di un'operazione](#get-status-of-an-operation)
* [Autenticare il client di runtime di query](#authenticate-the-runtime-for-generating-an-answer)
* [Generare una risposta dalla knowledge base](#generate-an-answer-from-the-knowledge-base)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

* [Autenticare il client di creazione](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Creare una knowledge base](#create-a-knowledge-base)
* [Aggiornare una knowledge base](#update-a-knowledge-base)
* [Scaricare una knowledge base](#download-a-knowledge-base)
* [Pubblicare una knowledge base](#publish-a-knowledge-base)
* [Eliminare una knowledge base](#delete-a-knowledge-base)
* [Ottenere lo stato di un'operazione](#get-status-of-an-operation)
* [Generare una risposta dalla knowledge base](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticare il client per la creazione della knowledge base

Creare un'istanza di un oggetto client con la chiave e usarla con la risorsa per creare l'endpoint e quindi creare un oggetto [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) con l'endpoint e la chiave. Creare un oggetto [ServicePrincipalCredentials](/dotnet/api/microsoft.rest.serviceclientcredentials).

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Creare una knowledge base

In una knowledge base vengono archiviate le coppie di domande e risposte per l'oggetto [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) da tre origini:

* Per **contenuto editoriale**, usare l'oggetto [QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto).
    * Per usare i metadati e le richieste di completamento, usare il contesto editoriale, perché questi dati vengono aggiunti a livello di singola coppia di domande e risposte.
* Per i **file**, usare l' oggetto [FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto). FileDTO include il nome file e l'URL pubblico per raggiungere il file.
* Per **URL**, usare un elenco di stringhe per rappresentare gli URL disponibili pubblicamente.

Il passaggio di creazione include anche le proprietà della knowledge base:
* `defaultAnswerUsedForExtraction`: il risultato restituito quando non si trova nessuna risposta
* `enableHierarchicalExtraction`: crea automaticamente relazioni di richieste tra coppie di domande e risposte estratte
* `language`: quando si crea la prima knowledge base di una risorsa, imposta la lingua da usare nell'indice di Ricerca di Azure.

Chiamare il metodo [CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync), quindi passare l'ID operazione restituito al metodo [MonitorOperation](#get-status-of-an-operation) per eseguire il polling dello stato.

La riga finale del codice seguente restituisce l'ID knowledge base dalla risposta di MonitorOperation.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

---

Per creare correttamente una knowledge base, assicurarsi di includere la funzione [`MonitorOperation`](#get-status-of-an-operation), a cui si fa riferimento nel codice precedente.

## <a name="update-a-knowledge-base"></a>Aggiornare una knowledge base

È possibile aggiornare una knowledge base passando l'ID knowledge base e un [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) contenente gli oggetti DTO per l'[aggiunta](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), l'[aggiornamento](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate) e l'[eliminazione](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) al metodo [UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync). Usare il metodo [MonitorOperation](#get-status-of-an-operation) per determinare se l'aggiornamento è riuscito.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

---

Per aggiornare correttamente una knowledge base, assicurarsi di includere la funzione [`MonitorOperation`](#get-status-of-an-operation), a cui si fa riferimento nel codice precedente.

## <a name="download-a-knowledge-base"></a>Scaricare una knowledge base

Usare il metodo [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync) per scaricare il database come un elenco di [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto). Questa operazione _non_ equivale all'esportazione dalla pagina **Settings** (Impostazioni) del portale di QnA Maker, perché il risultato di questo metodo non è un file.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

Pubblicare la knowledge base con il metodo [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync). In questo modo, il modello corrente salvato e sottoposto a training a cui fa riferimento l'ID knowledge base viene pubblicato in un endpoint. Questo passaggio è necessario per eseguire query sulla knowledge base.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

## <a name="get-query-runtime-key"></a>Ottenere una chiave di runtime di query

Dopo la pubblicazione di una knowledge base, è necessaria la chiave di runtime di query per eseguire una query sul runtime. Non è la stessa chiave usata per creare l'oggetto client originale.

Usare il metodo [EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) per ottenere la classe [EndpointKeysDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto).

Usare una delle proprietà chiave restituite nell'oggetto per eseguire una query sulla knowledge base.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey)]

Una chiave di runtime è necessaria per eseguire query sulla knowledge base.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticare il runtime per la generazione di una risposta

Creare un oggetto [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) per eseguire una query sulla knowledge base e generare una risposta o eseguire il training da apprendimento attivo.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Usare QnAMakerRuntimeClient per:
* Ottenere una risposta dalla knowledge base
* Inviare nuove domande suggerite alla knowledge base per l'[apprendimento attivo](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generare una risposta dalla knowledge base

Generare una risposta da una knowledge base pubblicata usando il metodo [RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync). Questo metodo accetta l'ID knowledge base e [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Accedere ad altre proprietà di QueryDTO, ad esempio [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) e [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context) da usare nel chatbot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generare una risposta dalla knowledge base

Generare una risposta da una knowledge base pubblicata con il metodo [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync). Questo metodo accetta l'ID knowledge base e [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Accedere ad altre proprietà di QueryDTO, come a [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) e [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest) da usare nel chatbot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

---

Si tratta di un semplice esempio di esecuzione di query sulla knowledge base. Per informazioni sugli scenari di query avanzati, vedere [altri esempi di query](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Eliminare una knowledge base

Eliminare la knowledge base usando il metodo [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync) con un parametro dell'ID knowledge base.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Ottenere lo stato di un'operazione

Alcuni metodi, ad esempio per la creazione e l'aggiornamento, possono richiedere una quantità di tempo tale che invece di attendere il completamento del processo viene restituita un'[operazione](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation). Usare il relativo [ID operazione](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) per eseguire il polling (con logica di ripetizione dei tentativi) e determinare lo stato del metodo originale.

Il _ciclo_ e _Task.Delay_ nel blocco di codice seguente vengono usati per simulare la logica di ripetizione dei tentativi. Dovranno essere sostituiti con la propria logica di ripetizione dei tentativi.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `dotnet run` dalla directory dell'applicazione.

```dotnetcli
dotnet run
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart).

---
