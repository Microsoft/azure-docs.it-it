---
title: 'Guida introduttiva: libreria client di analisi immagini per go'
titleSuffix: Azure Cognitive Services
description: Iniziare a usare la libreria client di analisi immagini per go con questa Guida introduttiva.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 6a0d13dbc0beb90e0e276c5f594ff09c721f06f8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073397"
---
<a name="HOLTop"></a>

Usare la libreria client di analisi immagini per analizzare un'immagine per tag, descrizione del testo, visi, contenuto per adulti e altro ancora.

[Documentazione di riferimento](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Pacchetto](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Versione più recente di [Go](https://golang.org/dl/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creare una risorsa Visione artificiale"  target="_blank">creare una risorsa Visione artificiale </a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione al servizio Visione artificiale. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-go-project-directory"></a>Creare una directory di progetto Go

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a un'area di lavoro per il progetto Go, denominato `my-app`.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

L'area di lavoro conterrà tre cartelle:

* **src**: questa directory conterrà il codice sorgente e i pacchetti. Tutti i pacchetti installati con il comando `go get` saranno disponibili in questa directory.
* **pkg**: questa directory conterrà gli oggetti pacchetto Go compilati. Tutti i file hanno estensione `.a`.
* **bin**: questa directory conterrà i file eseguibili binari creati durante l'esecuzione di `go install`.

> [!TIP]
> Per altre informazioni sulla struttura di un'area di lavoro Go, vedere la [documentazione del linguaggio Go](https://golang.org/doc/code.html#Workspaces). Questa guida include informazioni per l'impostazione di `$GOPATH` e `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Installare la libreria client per Go

Successivamente, installare la libreria client per Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

oppure, se si usa DEP, all'interno del repository eseguire:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Creare un'applicazione Go 

Successivamente, creare un file nella directory **src** denominato `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Aprire `sample-app.go` nell'IDE o nell'editor di testo preferito. Aggiungere quindi il nome del pacchetto e importare le librerie seguenti:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Dichiarare inoltre un contesto nella radice dello script. Questo oggetto sarà necessario per eseguire la maggior parte delle chiamate di funzione di analisi delle immagini:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Successivamente, si inizierà ad aggiungere il codice per eseguire diverse operazioni di Visione artificiale.

> [!div class="nextstepaction"]
> [Il client è stato configurato](?success=set-up-client#object-model) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Image Analysis go SDK.

|Nome|Descrizione|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Questa classe è necessaria per tutte le funzionalità di Visione artificiale, ad esempio l'analisi delle immagini e la lettura del testo. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per eseguire la maggior parte delle operazioni con le immagini.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Questo tipo contiene i risultati di una chiamata di funzione **AnalyzeImage**. Esistono tipi simili per ogni funzione specifica della categoria.|
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Questo tipo definisce le diverse tipologie di analisi delle immagini che è possibile eseguire in un'operazione di analisi standard. Specificare un set di valori di VisualFeatureTypes a seconda delle esigenze. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice illustrano come eseguire le attività seguenti con la libreria client di analisi immagini per go:

* [Autenticare il client](#authenticate-the-client)
* [Analizzare un'immagine](#analyze-an-image)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questo passaggio presuppone che siano state [create variabili di ambiente](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) per la chiave e l'endpoint del servizio Visione artificiale, denominate rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

Creare una funzione `main` e aggiungervi il codice seguente per creare un'istanza di un client con l'endpoint e la chiave.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Il client è stato autenticato](?success=authenticate-client#analyze-an-image) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analizzare un'immagine

Il codice seguente usa l'oggetto client per analizzare un'immagine remota e stampare i risultati nella console. È possibile ottenere una descrizione di testo, una categorizzazione, un elenco di tag, gli oggetti rilevati, i marchi rilevati, i visi rilegati, i flag di contenuto per adulti, i colori principali e il tipo di immagine.

### <a name="set-up-test-image"></a>Configurare l'immagine di test

Salvare prima un riferimento all'URL dell'immagine da analizzare. Includere il riferimento all'interno della funzione `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> È anche possibile analizzare un'immagine locale. Vedere i metodi [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient), ad esempio **DescribeImageInStream**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).

### <a name="specify-visual-features"></a>Specificare le caratteristiche visive

Le chiamate di funzione seguenti estraggono diverse funzionalità visive dall'immagine di esempio. Queste funzioni verranno definite nelle sezioni seguenti.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Ottenere la descrizione dell'immagine

La funzione seguente ottiene l'elenco delle didascalie generate per l'immagine. Per altre informazioni sulla descrizione dell'immagine, vedere [Descrivere le immagini](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Ottenere la categoria dell'immagine

La funzione seguente ottiene la categoria rilevata dell'immagine. Per altre informazioni, vedere [Categorizzare le immagini](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Ottenere i tag delle immagini

La funzione seguente ottiene il set di tag rilevati nell'immagine. Per altre informazioni, vedere [Tag del contenuto](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Rilevare oggetti

La funzione seguente rileva gli oggetti comuni nell'immagine e li stampa nella console. Per altre informazioni, vedere [Rilevamento di oggetti](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Rilevare marchi

Il codice seguente rileva i marchi e i logo aziendali nell'immagine e li stampa nella console. Per altre informazioni, vedere [Rilevamento di marchi](../../concept-brand-detection.md).

Dichiarare innanzitutto un riferimento a una nuova immagine all'interno della funzione `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Il codice seguente definisce la funzione di rilevamento dei marchi.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Rilevare visi

La funzione seguente restituisce i visi rilevati nell'immagine con le relative coordinate del rettangolo e determinati attributi del viso. Per altre informazioni, vedere [Rilevamento viso](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Rilevare contenuto per adulti, spinto o cruento

Il codice seguente stampa la presenza rilevata di contenuto per adulti nell'immagine. Per altre informazioni, vedere [Contenuto per adulti, spinto o cruento](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Ottenere la combinazione di colori dell'immagine

La funzione seguente stampa gli attributi di colore rilevati nell'immagine, ad esempio i colori dominanti e il colore principale. Per altre informazioni, vedere [Combinazioni di colori](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Ottenere contenuto specifico del dominio

L'analisi delle immagini può utilizzare modelli specializzati per eseguire ulteriori analisi sulle immagini. Per altre informazioni, vedere [Contenuto specifico del dominio](../../concept-detecting-domain-content.md). 

Il codice seguente analizza i dati sulle celebrità rilevate nell'immagine.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Il codice seguente analizza i dati sui luoghi di interesse rilevati nell'immagine.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Ottenere il tipo di immagine

La funzione seguente stampa informazioni sul tipo di immagine,&mdash;ad esempio se si tratta di ClipArt o di un disegno.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [L'immagine è stata analizzata](?success=analyze-image#run-the-application) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=analyze-image)


## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione dalla directory dell'applicazione con il comando `go run`.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [L'applicazione è stata eseguita](?success=run-the-application#clean-up-resources) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Le risorse sono state pulite](?success=clean-up-resources#next-steps) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come installare la libreria client di analisi immagini ed effettuare chiamate di base per l'analisi delle immagini. Quindi, vedere altre informazioni sulle funzionalità di analisi dell'API.

> [!div class="nextstepaction"]
>[Chiamare l'API di analisi](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Panoramica dell'analisi delle immagini](../../overview-image-analysis.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).

