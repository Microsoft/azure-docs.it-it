---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: c60c0326018e615a0c84d56c98faee58560f1d87
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113153"
---
Introduzione alla libreria client di Visione personalizzata per Python. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per creare un modello di classificazione immagini. Si creerà un progetto, si aggiungeranno tag, si eseguirà il training del progetto e si userà l'URL dell'endpoint di stima del progetto per testarlo a livello di codice. Usare questo esempio come modello per la creazione di un'applicazione di riconoscimento immagini personalizzata.

> [!NOTE]
> Se si vuole creare un modello di classificazione ed eseguirne il training _senza_ scrivere codice, vedere invece le [istruzioni basate sul browser](../../getting-started-build-a-classifier.md).

Usare la libreria client di Visione personalizzata per Python per:

* Creare un nuovo progetto Visione personalizzata
* Aggiungere tag al progetto
* Caricare e contrassegnare le immagini
* Eseguire il training del progetto
* Pubblicare l'iterazione corrente
* Testare l'endpoint di stima

[Documentazione di riferimento](/python/api/overview/azure/cognitiveservices/customvision) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision) | [Pacchetto (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/) | [Esempi](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * L'installazione di Python deve includere [PIP](https://pip.pypa.io/en/stable/). È possibile verificare se PIP è stato installato eseguendo `pip --version` nella riga di comando. Ottenere PIP installando la versione più recente di Python.
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" creare una risorsa di visione personalizzata creare una risorsa "  target="_blank"> Visione personalizzata </a> nel portale di Azure per creare una risorsa di training e stima e ottenere le chiavi e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint delle risorse create sono necessari per connettere l'applicazione a Visione personalizzata. Le chiavi e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-library"></a>Installare la libreria client

Per scrivere un'app di analisi immagini con Visione personalizzata per Python, è necessaria la libreria client di Visione personalizzata. Dopo l'installazione di Python, eseguire il comando seguente in PowerShell o in una finestra della console:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare un nuovo file Python e importare le librerie seguenti.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py), che contiene gli esempi di codice di questo argomento.

Creare le variabili per l'endpoint e le chiavi della sottoscrizione di Azure della risorsa.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_creds)]

> [!IMPORTANT]
> Accedere al portale di Azure. Se le risorse Visione personalizzata create nella sezione **Prerequisiti** sono state distribuite correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. È possibile trovare le chiavi e l'endpoint nelle pagine **chiave ed endpoint** delle risorse. È necessario ottenere le chiavi per le risorse di training e di stima, insieme all'endpoint API per la risorsa di training.
>
> È possibile trovare il valore ID della risorsa di stima nella scheda **Proprietà** della risorsa, elencato come **ID sottoscrizione**.
>
> Al termine, ricordarsi di rimuovere le chiavi dal codice e di non renderle mai pubbliche. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](../../../cognitive-services-security.md) di Servizi cognitivi.

## <a name="object-model"></a>Modello a oggetti

|Nome|Description|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Questa classe gestisce la creazione, il training e la pubblicazione dei modelli. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Questa classe gestisce l'esecuzione di query sui modelli per le previsioni di classificazione immagini.|
|[ImagePrediction](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Questa classe definisce una singola previsione di oggetto in una singola immagine. Include le proprietà per l'ID e il nome dell'oggetto, la posizione del rettangolo di delimitazione dell'oggetto e un punteggio di attendibilità.|

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Visione personalizzata per Python:

* [Autenticare il client](#authenticate-the-client)
* [Creare un nuovo progetto Visione personalizzata](#create-a-new-custom-vision-project)
* [Aggiungere tag al progetto](#add-tags-to-the-project)
* [Caricare e contrassegnare le immagini](#upload-and-tag-images)
* [Eseguire il training del progetto](#train-the-project)
* [Pubblicare l'iterazione corrente](#publish-the-current-iteration)
* [Testare l'endpoint di stima](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza dei client di training e di previsione con l'endpoint e le chiavi. Creare oggetti **ApiKeyServiceClientCredentials** con le chiavi e usarli con l'endpoint per creare un oggetto [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) e [CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient).

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Creare un nuovo progetto Visione personalizzata

Per creare un nuovo progetto di Servizio visione artificiale personalizzato, aggiungere il codice seguente allo script. 

Per specificare altre opzioni quando si crea il progetto, come illustrato in [Creare un classificatore](../../getting-started-build-a-classifier.md) nella guida al portale Web, vedere il metodo [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Aggiungere tag al progetto

Per aggiungere tag di classificazione al progetto, aggiungere il codice seguente:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Scaricare prima di tutto le immagini di esempio per questo progetto. Salvare il contenuto della [cartella immagini di esempio](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) nel dispositivo locale.

> [!NOTE]
> Per completare la formazione è necessario un set più ampio di immagini? Trove, un progetto Microsoft Garage, consente di raccogliere e acquistare set di immagini a scopo di training. Una volta raccolte le immagini, è possibile scaricarle e quindi importarle nel progetto di Visione personalizzata nel modo consueto. Per altre informazioni, visitare la [pagina di Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3).

Per aggiungere le immagini di esempio al progetto, inserire il codice seguente dopo la creazione dei tag. Questo codice carica ogni immagine con il tag corrispondente. È possibile caricare fino a 64 immagini in un singolo batch.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Sarà necessario cambiare il percorso delle immagini in base a dove è stato scaricato il repository di esempi di Python SDK per Servizi cognitivi.

## <a name="train-the-project"></a>Eseguire il training del progetto

Questo codice crea la prima iterazione del modello di previsione. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Eseguire il training con i tag selezionati
>
> Facoltativamente, è possibile eseguire il training solo su un subset dei tag applicati. Questa operazione può essere eseguita se non è ancora stato applicato un numero sufficiente di determinati tag, ma si dispone di un numero sufficiente di altri. Nella chiamata **[train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** impostare il parametro opzionale *selected_tags* su un elenco di stringhe di ID dei tag da usare. Il modello eseguirà il training in modo da riconoscere solo i tag nell'elenco.

## <a name="publish-the-current-iteration"></a>Pubblicare l'iterazione corrente

L'iterazione è disponibile nell'endpoint di stima solo dopo che è stata pubblicata. Il codice seguente rende disponibile l'iterazione corrente del modello per l'esecuzione di query. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testare l'endpoint di stima

Per inviare un'immagine all'endpoint di stima e recuperare la stima, aggiungere il codice seguente alla fine del file:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_test)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire *CustomVisionQuickstart.py*.

```powershell
python CustomVisionQuickstart.py
```

L'output dell'applicazione dovrebbe essere simile al testo seguente:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Si può quindi verificare che all'immagine di test (disponibile in **<url_immagine_base>/Images/Test**) siano stati applicati i tag appropriati. È anche possibile tornare al [sito Web di Visione personalizzata](https://customvision.ai) e vedere lo stato corrente del progetto appena creato.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come eseguire ogni passaggio del processo di classificazione delle immagini nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](../../test-your-model.md)

* Informazioni su Visione personalizzata
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py)
* [Documentazione di riferimento sull'SDK](/python/api/overview/azure/cognitiveservices/customvision)
