---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 1f07cda0afa7213ca9c803754950c86a3f246469
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625435"
---
Questa guida fornisce istruzioni e codice di esempio per iniziare a usare la libreria client di Visione personalizzata per Go per creare un modello di rilevamento oggetti. Si creerà un progetto, si aggiungeranno tag, si eseguirà il training del progetto e si userà l'URL dell'endpoint di stima del progetto per testarlo a livello di codice. Usare questo esempio come modello per la creazione di un'applicazione di riconoscimento immagini personalizzata.

> [!NOTE]
> Se si vuole creare un modello di rilevamento oggetti ed eseguirne il training _senza_ scrivere codice, vedere invece le [istruzioni basate sul browser](../../get-started-build-detector.md).

## <a name="prerequisites"></a>Prerequisiti 

- [Go 1.8+](https://golang.org/doc/install)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Installare la libreria client di Visione personalizzata

Per scrivere un'app di analisi immagini con Visione personalizzata per Go, è necessaria la libreria client di Visione personalizzata. In PowerShell eseguire questo comando:

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

o se si usa `dep` nell'esecuzione del repository:
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>Aggiungere il codice

Creare un nuovo file denominato *sample.go* nella directory del progetto preferita.

## <a name="create-the-custom-vision-project"></a>Creare il progetto di Visione personalizzata

Per creare un nuovo progetto di Servizio visione artificiale personalizzato, aggiungere il codice seguente allo script. Inserire le chiavi di sottoscrizione nelle definizioni appropriate. Inoltre, ottenere l'URL dell'endpoint dalla pagina Impostazioni del sito Visione personalizzata.

Per specificare altre opzioni quando si crea il progetto, come illustrato in [Creare un rilevatore](../../get-started-build-detector.md) nella guida al portale Web, vedere il metodo [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_).

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "<your endpoint URL>"
    project_name string = "Go Sample OD Project"
    iteration_publish_name = "detectModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    var objectDetectDomain training.Domain
    domains, _ := trainer.GetDomains(ctx)

    for _, domain := range *domains.Value {
        fmt.Println(domain, domain.Type)
        if domain.Type == "ObjectDetection" && *domain.Name == "General" {
            objectDetectDomain = domain
            break
        }
    }
    fmt.Println("Creating project...")
    project, _ := trainer.CreateProject(ctx, project_name, "", objectDetectDomain.ID, "")
```

## <a name="create-tags-in-the-project"></a>Creare tag nel progetto

Per creare i tag di classificazione per il progetto, aggiungere il codice seguente alla fine del file *sample.go*:

```Go
# Make two tags in the new project
forkTag, _ := trainer.CreateTag(ctx, *project.ID, "fork", "A fork", string(training.Regular))
scissorsTag, _ := trainer.CreateTag(ctx, *project.ID, "scissors", "Pair of scissors", string(training.Regular))
```

## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Quando si aggiungono tag alle immagini nei progetti di rilevamento degli oggetti, è necessario specificare l'area di ogni oggetto contrassegnato usando coordinate normalizzate.

> [!NOTE]
> Se non si ha un'utilità di clic e trascinamento per contrassegnare le coordinate delle aree, è possibile usare l'interfaccia utente Web in [Customvision.ai](https://www.customvision.ai/). In questo esempio le coordinate sono già disponibili.

Per aggiungere le immagini, i tag e le aree al progetto, inserire il codice seguente dopo la creazione dei tag. Si noti che in questa esercitazione le aree sono hardcoded inline. Le aree specificano il rettangolo delimitatore in coordinate normalizzate e le coordinate sono specificate in questo ordine: sinistra, alto, larghezza, altezza.

```Go
forkImageRegions := map[string][4]float64{
    "fork_1.jpg": [4]float64{ 0.145833328, 0.3509314, 0.5894608, 0.238562092 },
    "fork_2.jpg": [4]float64{ 0.294117659, 0.216944471, 0.534313738, 0.5980392 },
    "fork_3.jpg": [4]float64{ 0.09191177, 0.0682516545, 0.757352948, 0.6143791 },
    "fork_4.jpg": [4]float64{ 0.254901975, 0.185898721, 0.5232843, 0.594771266 },
    "fork_5.jpg": [4]float64{ 0.2365196, 0.128709182, 0.5845588, 0.71405226 },
    "fork_6.jpg": [4]float64{ 0.115196079, 0.133611143, 0.676470637, 0.6993464 },
    "fork_7.jpg": [4]float64{ 0.164215669, 0.31008172, 0.767156839, 0.410130739 },
    "fork_8.jpg": [4]float64{ 0.118872553, 0.318251669, 0.817401946, 0.225490168 },
    "fork_9.jpg": [4]float64{ 0.18259804, 0.2136765, 0.6335784, 0.643790841 },
    "fork_10.jpg": [4]float64{ 0.05269608, 0.282303959, 0.8088235, 0.452614367 },
    "fork_11.jpg": [4]float64{ 0.05759804, 0.0894935, 0.9007353, 0.3251634 },
    "fork_12.jpg": [4]float64{ 0.3345588, 0.07315363, 0.375, 0.9150327 },
    "fork_13.jpg": [4]float64{ 0.269607842, 0.194068655, 0.4093137, 0.6732026 },
    "fork_14.jpg": [4]float64{ 0.143382356, 0.218578458, 0.7977941, 0.295751631 },
    "fork_15.jpg": [4]float64{ 0.19240196, 0.0633497, 0.5710784, 0.8398692 },
    "fork_16.jpg": [4]float64{ 0.140931368, 0.480016381, 0.6838235, 0.240196079 },
    "fork_17.jpg": [4]float64{ 0.305147052, 0.2512582, 0.4791667, 0.5408496 },
    "fork_18.jpg": [4]float64{ 0.234068632, 0.445702642, 0.6127451, 0.344771236 },
    "fork_19.jpg": [4]float64{ 0.219362751, 0.141781077, 0.5919118, 0.6683006 },
    "fork_20.jpg": [4]float64{ 0.180147052, 0.239820287, 0.6887255, 0.235294119 },
}

scissorsImageRegions := map[string][4]float64{
    "scissors_1.jpg": [4]float64{ 0.4007353, 0.194068655, 0.259803921, 0.6617647 },
    "scissors_2.jpg": [4]float64{ 0.426470578, 0.185898721, 0.172794119, 0.5539216 },
    "scissors_3.jpg": [4]float64{ 0.289215684, 0.259428144, 0.403186262, 0.421568632 },
    "scissors_4.jpg": [4]float64{ 0.343137264, 0.105833367, 0.332107842, 0.8055556 },
    "scissors_5.jpg": [4]float64{ 0.3125, 0.09766343, 0.435049027, 0.71405226 },
    "scissors_6.jpg": [4]float64{ 0.379901975, 0.24308826, 0.32107842, 0.5718954 },
    "scissors_7.jpg": [4]float64{ 0.341911763, 0.20714055, 0.3137255, 0.6356209 },
    "scissors_8.jpg": [4]float64{ 0.231617644, 0.08459154, 0.504901946, 0.8480392 },
    "scissors_9.jpg": [4]float64{ 0.170343131, 0.332957536, 0.767156839, 0.403594762 },
    "scissors_10.jpg": [4]float64{ 0.204656869, 0.120539248, 0.5245098, 0.743464053 },
    "scissors_11.jpg": [4]float64{ 0.05514706, 0.159754932, 0.799019635, 0.730392158 },
    "scissors_12.jpg": [4]float64{ 0.265931368, 0.169558853, 0.5061275, 0.606209159 },
    "scissors_13.jpg": [4]float64{ 0.241421565, 0.184264734, 0.448529422, 0.6830065 },
    "scissors_14.jpg": [4]float64{ 0.05759804, 0.05027781, 0.75, 0.882352948 },
    "scissors_15.jpg": [4]float64{ 0.191176474, 0.169558853, 0.6936275, 0.6748366 },
    "scissors_16.jpg": [4]float64{ 0.1004902, 0.279036, 0.6911765, 0.477124184 },
    "scissors_17.jpg": [4]float64{ 0.2720588, 0.131977156, 0.4987745, 0.6911765 },
    "scissors_18.jpg": [4]float64{ 0.180147052, 0.112369314, 0.6262255, 0.6666667 },
    "scissors_19.jpg": [4]float64{ 0.333333343, 0.0274019931, 0.443627447, 0.852941155 },
    "scissors_20.jpg": [4]float64{ 0.158088237, 0.04047389, 0.6691176, 0.843137264 },
}
```
Usare quindi questa mappa di associazioni per caricare ogni immagine di esempio con le relative coordinate di area. È possibile caricare fino a 64 immagini in un singolo batch. Aggiungere il codice seguente.

> [!NOTE]
> Occorrerà anche modificare il percorso delle immagini in base alla posizione in cui è stato scaricato il progetto Cognitive Services Go SDK Samples.

```Go
// Go through the data table above and create the images
fmt.Println("Adding images...")
var fork_images []training.ImageFileCreateEntry
for file, region := range forkImageRegions {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "fork", file))

    imageRegion := training.Region { 
        TagID:forkTag.ID,
        Left:&region[0],
        Top:&region[1],
        Width:&region[2],
        Height:&region[3],
    }

    fork_images = append(fork_images, training.ImageFileCreateEntry {
        Name: &file,
        Contents: &imageFile,
        Regions: &[]training.Region{ imageRegion },
    })
}
    
fork_batch, _ := trainer.CreateImagesFromFiles(ctx, *project.ID, training.ImageFileCreateBatch{ 
    Images: &fork_images,
})

if (!*fork_batch.IsBatchSuccessful) {
    fmt.Println("Batch upload failed.")
}

var scissor_images []training.ImageFileCreateEntry
for file, region := range scissorsImageRegions {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "scissors", file))

    imageRegion := training.Region { 
        TagID:scissorsTag.ID,
        Left:&region[0],
        Top:&region[1],
        Width:&region[2],
        Height:&region[3],
    }

    scissor_images = append(scissor_images, training.ImageFileCreateEntry {
        Name: &file,
        Contents: &imageFile,
        Regions: &[]training.Region{ imageRegion },
    })
}
    
scissor_batch, _ := trainer.CreateImagesFromFiles(ctx, *project.ID, training.ImageFileCreateBatch{ 
    Images: &scissor_images,
})
    
if (!*scissor_batch.IsBatchSuccessful) {
    fmt.Println("Batch upload failed.")
}     
```

## <a name="train-and-publish-the-project"></a>Eseguire il training del progetto e pubblicarlo

Questo codice crea la prima iterazione del modello di previsione e quindi la pubblica nell'endpoint di previsione. Il nome assegnato all'iterazione pubblicata può essere usato per inviare le richieste di stima. L'iterazione è disponibile nell'endpoint di stima solo dopo che è stata pubblicata.

```go
iteration, _ := trainer.TrainProject(ctx, *project.ID)
fmt.Println("Training status:", *iteration.Status)
for {
    if *iteration.Status != "Training" {
        break
    }
    time.Sleep(5 * time.Second)
    iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
    fmt.Println("Training status:", *iteration.Status)
}

trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

## <a name="use-the-prediction-endpoint"></a>Usare l'endpoint di stima

Per inviare un'immagine all'endpoint di stima e recuperare la stima, aggiungere il codice seguente alla fine del file:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_od_image.jpg"))
    results, _ := predictor.DetectImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions    {
        boundingBox := *prediction.BoundingBox

        fmt.Printf("\t%s: %.2f%% (%.2f, %.2f, %.2f, %.2f)", 
            *prediction.TagName,
            *prediction.Probability * 100,
            *boundingBox.Left,
            *boundingBox.Top,
            *boundingBox.Width,
            *boundingBox.Height)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire *sample.go*.

```shell
go run sample.go
```

L'output dell'applicazione dovrebbe essere visualizzato nella console. È quindi possibile verificare che l'immagine di test (disponibile in **samples/vision/images/Test**) sia contrassegnata in modo appropriato e che l'area di rilevamento sia corretta.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stato eseguito ogni passaggio del processo di rilevamento degli oggetti nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione. La guida seguente è incentrata sulla classificazione delle immagini, ma i principi sono simili a quelli del rilevamento di oggetti.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](../../test-your-model.md)

* Informazioni su Visione personalizzata
* [Documentazione di riferimento sull'SDK (esercitazione)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/customvision/training)
* [Documentazione di riferimento sull'SDK (previsione)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction)