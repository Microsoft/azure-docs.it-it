---
title: 'Esempio: Identificare i visi nelle immagini - Viso'
titleSuffix: Azure Cognitive Services
description: Questa guida illustra come identificare visi sconosciuti tramite oggetti PersonGroup creati anticipatamente da persone note.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 07/02/2020
ms.author: sbowles
ms.openlocfilehash: 607f67258c5d069590f934891c09ccada780c977
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85918740"
---
# <a name="example-identify-faces-in-images"></a>Esempio: Identificare i visi nelle immagini

Questa guida illustra come identificare visi sconosciuti tramite oggetti PersonGroup creati anticipatamente da persone note. Gli esempi sono scritti in C# usando la libreria client di Viso di Servizi cognitivi di Azure.

Questo esempio dimostra:

- Come creare un oggetto PersonGroup. L'oggetto PersonGroup contiene un elenco di persone note.
- Come assegnare visi a ogni persona. I visi vengono usati come baseline per identificare le persone. È consigliabile usare viste frontali dei visi ben definite. Un esempio è la foto di un documento di identità. Un buon set di foto comprende visi della stessa persona in posizioni e con pettinature e vestiti diversi.

## <a name="prerequisites"></a>Prerequisiti
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="creare una risorsa Viso"  target="_blank">creare una risorsa Viso <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa** e copiare la chiave.
* Alcune foto con i visi di persone identificate. [Scaricare foto di esempio](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) per Anna, Bill e Clare.
* Una serie di foto di test. Le foto possono contenere o meno i visi delle persone identificate. Usare alcune immagini dal collegamento delle foto di esempio.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core nell'ambiente di sviluppo integrato o nell'editor preferito. 

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `face-identify`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```dotnetcli
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="step-1-authorize-the-api-call"></a>Passaggio 1: Autorizzare la chiamata API

Ogni chiamata all'API Viso richiede una chiave di sottoscrizione. Questa chiave può essere passata tramite un parametro di stringa di query o essere specificata nell'intestazione della richiesta. Quando si usa una libreria client, la chiave di sottoscrizione viene passata tramite il costruttore della classe **FaceClient**. Aggiungere il codice seguente al metodo **Main** del file *Program.cs*.
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<subscription key>"),
    new System.Net.Http.DelegatingHandler[] { });
```

## <a name="step-2-create-the-persongroup"></a>Passaggio 2: Creare il PersonGroup

In questo passaggio, un oggetto **PersonGroup** denominato "MyFriends" contiene Anna, Bill e Clare. Per ogni persona sono registrati più visi. I visi devono essere rilevati dalle immagini. Dopo tutti questi passaggi si ottiene un oggetto **PersonGroup** come nell'immagine seguente:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Passaggio 2.1: Definire le persone per l'oggetto PersonGroup
Una persona è un'unità di base di identificazione. Per una persona possono essere registrati uno o più visi. Un oggetto **PersonGroup** è una raccolta di persone. Ogni persona è definita all'interno di un oggetto **PersonGroup** specifico. L'identificazione viene eseguita in base a un oggetto **PersonGroup**. L'attività prevede la creazione di un oggetto **PersonGroup** e quindi la creazione di persone al suo interno, ad esempio Anna, Bill e Clare.

Creare innanzitutto un nuovo oggetto **PersonGroup** con l'API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione). L'API della libreria client corrispondente è il metodo **CreatePersonGroupAsync** per la classe **FaceClient**. L'ID del gruppo specificato per la creazione del gruppo è univoco per ogni sottoscrizione. È anche possibile ottenere, aggiornare o eliminare oggetti **PersonGroup** tramite altre API **PersonGroup**. 

Dopo aver definito un gruppo, è possibile definire le persone al suo interno usando l'API [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione). Il metodo della libreria client è **CreatePersonAsync**. È possibile aggiungere un viso a ogni persona, dopo averla creata.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a> Passaggio 2.2: Rilevare i visi e registrarli per la persona corretta
Il rilevamento viene eseguito inviando una richiesta Web "POST" all'API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento) con il file di immagine nel corpo della richiesta HTTP. Quando si usa la libreria client, il rilevamento dei visi viene eseguito tramite uno dei metodi Detect..Async per la classe FaceClient.

Per ogni viso rilevato è possibile chiamare [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person – Aggiunta viso) per aggiungerlo alla persona corretta.

Il codice seguente illustra il processo di rilevamento di un viso da un'immagine e l'aggiunta del viso per una persona:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Se l'immagine contiene più visi, viene aggiunto solo il viso più grande. È possibile aggiungere altri visi alla persona. Passare una stringa nel formato "targetFace = left, top, width, height" al parametro di query targetFace dell'API [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - Aggiunta viso). È anche possibile usare il parametro facoltativo targetFace per il metodo AddPersonFaceAsync per aggiungere altri visi. A ogni viso aggiunto alla persona viene assegnato un ID viso persistente univoco. L'ID può essere usato in [PersonGroup Person – Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) (PersonGroup Person – Eliminazione viso) e [Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso – Identificazione).

## <a name="step-3-train-the-persongroup"></a>Passaggio 3: Eseguire il training del PersonGroup

È necessario eseguire il training dell'oggetto **PersonGroup** deve essere prima di poterlo usare per eseguire un'identificazione. L'oggetto **PersonGroup** deve essere nuovamente sottoposto a training dopo l'aggiunta o la rimozione di una persona o se si modifica il viso registrato di una persona. Il training viene eseguito dall'API [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) (PersonGroup - Training). Quando si usa la libreria client, è richiesta una chiamata al metodo **TrainPersonGroupAsync**:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Il training è un processo asincrono, che potrebbe non essere terminato anche dopo la restituzione di risultati da parte del metodo **TrainPersonGroupAsync**. Può essere necessario inoltrare una query sullo stato del training. Usare l'API [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) (PersonGroup - Recupero stato training) o il metodo **GetPersonGroupTrainingStatusAsync** della libreria client. Il codice seguente illustra una semplice logica di attesa del completamento del training di **PersonGroup**:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Passaggio 4: Identificare un viso in base a un PersonGroup definito

Quando il servizio Viso esegue le identificazioni, calcola la somiglianza tra un viso di test e tutti i visi all'interno di un gruppo. Restituisce le persone più simili al viso di test. Questa operazione viene eseguita tramite l'API [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione) o il metodo IdentifyAsync della libreria client.

Il viso di test deve essere rilevato con i passaggi precedenti. L'ID viso viene quindi passato all'API di identificazione come secondo argomento. È possibile identificare più ID viso in una sola volta. Il risultato contiene tutti i risultati identificati. Per impostazione predefinita, il processo di identificazione restituisce solo una persona che rappresenta la corrispondenza migliore con il viso di test. Se si preferisce, è possibile specificare il parametro facoltativo _maxNumOfCandidatesReturned_ per consentire al processo di identificazione di restituire più candidati.

Il codice seguente illustra il processo di identificazione:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Dopo aver completato i passaggi, provare a identificare visi diversi. Vedere se i visi di Anna, Bill o Clare possono essere identificati in modo corretto in base alle immagini caricate per il rilevamento del viso. Vedere gli esempi seguenti:

![Identificare visi diversi](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Passaggio 5: Richiedere la funzionalità su larga scala

Un oggetto **PersonGroup** può contenere fino a 10.000 persone in base alla limitazione della progettazione precedente. Per altre informazioni sugli scenari con milioni di persone, vedere [Come usare la funzionalità su larga scala](how-to-use-large-scale.md).

## <a name="summary"></a>Summary

In questa guida si è appreso il processo di creazione di un oggetto **PersonGroup** e di identificazione di una persona. Sono state spiegate e illustrate le funzionalità seguenti:

- Rilevamento dei visi con l'API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) (Viso - Rilevamento).
- Creazione di oggetti PersonGroup con l'API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione).
- Creazione di persone con l'API [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione).
- Training di un oggetto PersonGroup con l'API [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) (PersonGroup - Training).
- Identificazione dei visi sconosciuti in base all'oggetto PersonGroup con l'API [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione).

## <a name="related-topics"></a>Argomenti correlati

- [Concetti relativi al riconoscimento del viso](../concepts/face-recognition.md)
- [Rilevare i visi in un'immagine](HowtoDetectFacesinImage.md)
- [Aggiungere visi](how-to-add-faces.md)
- [Usare la funzionalità su larga scala](how-to-use-large-scale.md)
