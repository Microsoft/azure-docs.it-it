---
title: 'Avvio rapido: Libreria client di Visione artificiale per Java'
description: Questo argomento di avvio rapido illustra come usare la libreria client di Visione artificiale per Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: c8435d250f95f861f2a84e91d673eb59c3a6d44b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750724"
---
<a name="HOLTop"></a>

Usare la libreria client di Visione artificiale per:

* Analizzare un'immagine per trovare tag, descrizioni di testo, visi, contenuto per adulti e altro ancora.
* Eseguire la lettura del testo stampato e scritto a mano con l'API di lettura.

[Documentazione di riferimento](/java/api/overview/azure/cognitiveservices/client/computervision) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision) |[Artefatto (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [Esempi](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* La versione più recente di [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Lo [strumento di compilazione Gradle](https://gradle.org/install/) o un'altra utilità di gestione dipendenze.
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creare una risorsa Visione artificiale"  target="_blank">creare una risorsa Visione artificiale <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione al servizio Visione artificiale. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-gradle-project"></a>Creare un nuovo progetto Gradle

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `gradle init` dalla directory di lavoro. Questo comando creerà i file di compilazione essenziali per Gradle, tra cui *build.gradle.kts*, che viene usato in fase di esecuzione per creare e configurare l'applicazione.

```console
gradle init --type basic
```

Quando viene chiesto di scegliere un linguaggio **DSL**, selezionare **Kotlin**.

### <a name="install-the-client-library"></a>Installare la libreria client

Questo argomento di avvio rapido usa l'utilità di gestione dipendenze Gradle. La libreria client e le informazioni per altre utilità di gestione dipendenze sono disponibili in [Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision).

Individuare il file *build.gradle.kts* e aprirlo con un IDE o un editor di testo a scelta. Quindi copiare la configurazione di compilazione seguente. Questa configurazione definisce il progetto come applicazione Java il cui punto di ingresso è la classe **ComputerVisionQuickstarts**. Importa la libreria di Visione artificiale.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### <a name="create-a-java-file"></a>Creare un file Java

Dalla directory di lavoro eseguire il comando seguente per creare una cartella di origine del progetto:

```console
mkdir -p src/main/java
```

Passare alla nuova cartella e creare un file denominato *ComputerVisionQuickstarts.Java.* Aprirlo in un editor o un IDE a scelta e importare le istruzioni `import` seguenti:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java), che contiene gli esempi di codice di questo argomento.

Nella classe **ComputerVisionQuickstarts** dell'applicazione creare variabili per l'endpoint e la chiave della risorsa.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_creds)]


> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa Visione artificiale creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. La chiave e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. 
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](../../../cognitive-services-security.md) di Servizi cognitivi.

Nel metodo **main** dell'applicazione aggiungere le chiamate per i metodi usati in questa guida di avvio rapido. Verranno definiti in un secondo momento.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_maincalls)]

> [!div class="nextstepaction"]
> [Il client è stato configurato](?success=set-up-client#object-model) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client)

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità dell'SDK Visione artificiale per Java.

|Nome|Descrizione|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Questa classe è necessaria per tutte le funzionalità di Visione artificiale. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi.|
|[ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision)| Questa classe deriva dall'oggetto client e gestisce direttamente tutte le operazioni per le immagini, come l'analisi, il rilevamento del testo e la generazione di anteprime.|
|[VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Questa enumerazione definisce i diversi tipi di analisi delle immagini che è possibile eseguire in un'operazione di analisi standard. Specificare un set di valori di VisualFeatureTypes a seconda delle esigenze. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Visione artificiale per Java:

* [Autenticare il client](#authenticate-the-client)
* [Analizzare un'immagine](#analyze-an-image)
* [Leggere il testo stampato e scritto a mano](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticare il client

In un nuovo metodo creare un'istanza di un oggetto [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) con l'endpoint e la chiave.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Il client è stato autenticato](?success=authenticate-client#analyze-an-image) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analizzare un'immagine

Il codice seguente definisce un metodo, `AnalyzeLocalImage`, che usa l'oggetto client per analizzare un'immagine locale e stampare i risultati. Il metodo restituisce una descrizione di testo, una categorizzazione, un elenco di tag, i visi rilevati, i flag di contenuto per adulti, i colori principali e il tipo di immagine.

> [!TIP]
> È anche possibile analizzare un'immagine remota usando il relativo URL. Vedere i metodi [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision), ad esempio **AnalyzeImage**. In alternativa, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) per gli scenari che coinvolgono immagini remote.

### <a name="set-up-test-image"></a>Configurare l'immagine di test

Prima di tutto, creare una cartella **resources/** nella cartella **src/main/** del progetto e aggiungere un'immagine da analizzare. Aggiungere quindi la definizione di metodo seguente alla classe **ComputerVisionQuickstarts**. Cambiare il valore di `pathToLocalImage` in modo che corrisponda al file di immagine. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>Specificare le caratteristiche visive

Quindi, specificare le caratteristiche visive da estrarre nell'analisi. Per un elenco completo, vedere l'enumerazione [VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analisi
Questo metodo stampa i risultati dettagliati nella console per ogni ambito dell'analisi di immagini. È consigliabile racchiudere questa chiamata al metodo in un blocco try/catch. Il metodo **analyzeImageInStream** restituisce un oggetto **ImageAnalysis** che contiene tutte le informazioni estratte.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

Le sezioni seguenti illustrano come analizzare queste informazioni in modo dettagliato.

### <a name="get-image-description"></a>Ottenere la descrizione dell'immagine

Il codice seguente ottiene l'elenco di didascalie generate per l'immagine. Per altre informazioni, vedere [Descrivere le immagini](../../concept-describing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Ottenere la categoria dell'immagine

Il codice seguente ottiene la categoria rilevata dell'immagine. Per altre informazioni, vedere [Categorizzare le immagini](../../concept-categorizing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Ottenere i tag delle immagini

Il codice seguente ottiene il set di tag rilevati nell'immagine. Per altre informazioni, vedere [Tag del contenuto](../../concept-tagging-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Rilevare visi

Il codice seguente restituisce i visi rilevati nell'immagine con le rispettive coordinate del rettangolo e seleziona gli attributi del viso. Per altre informazioni, vedere [Rilevamento viso](../../concept-detecting-faces.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Rilevare contenuto per adulti, spinto o cruento

Il codice seguente stampa la presenza rilevata di contenuto per adulti nell'immagine. Per altre informazioni, vedere [Contenuto per adulti, spinto o cruento](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Ottenere la combinazione di colori dell'immagine

Il codice seguente stampa gli attributi di colore rilevati nell'immagine, ad esempio i colori dominanti e il colore principale. Per altre informazioni, vedere [Combinazioni di colori](../../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Ottenere contenuto specifico del dominio

Visione artificiale può usare un modello speciale per eseguire un'ulteriore analisi delle immagini. Per altre informazioni, vedere [Contenuto specifico del dominio](../../concept-detecting-domain-content.md). 

Il codice seguente analizza i dati sulle celebrità rilevate nell'immagine.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Il codice seguente analizza i dati sui luoghi di interesse rilevati nell'immagine.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Ottenere il tipo di immagine

Il codice seguente stampa informazioni sul tipo di immagine, sia che si tratti di ClipArt o di un disegno lineare.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

> [!div class="nextstepaction"]
> [L'immagine è stata analizzata](?success=analyze-image#read-printed-and-handwritten-text) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Leggere il testo stampato e scritto a mano

Visione artificiale può leggere il testo visibile di un'immagine e convertirlo in un flusso di caratteri. Questa sezione definisce un metodo, `ReadFromFile`, che accetta un percorso file locale e stampa il testo dell'immagine nella console.

> [!TIP]
> È anche possibile leggere il testo un'immagine remota a cui fa riferimento l'URL. Vedere i metodi [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision), ad esempio **read**. In alternativa, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) per gli scenari che coinvolgono immagini remote.

### <a name="set-up-test-image"></a>Configurare l'immagine di test

Creare una cartella **resources/** nella cartella **src/main/** del progetto e aggiungere un'immagine da cui si vuole leggere il testo. È possibile scaricare un'[immagine di esempio](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg) da usare qui.

Aggiungere quindi la definizione di metodo seguente alla classe **ComputerVisionQuickstarts**. Cambiare il valore di `localFilePath` in modo che corrisponda al file di immagine. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>Chiamare l'API di lettura

Aggiungere quindi il codice seguente per chiamare il metodo **readInStreamWithServiceResponseAsync** per l'immagine specificata.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

Il blocco di codice seguente estrae l'ID operazione dalla risposta della chiamata di lettura. Usa questo ID con un metodo helper per stampare i risultati di lettura del testo nella console. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

Chiudere il blocco try/catch e la definizione del metodo.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>Ottenere risultati della lettura

Quindi, aggiungere una definizione per il metodo helper. Questo metodo usa l'ID operazione del passaggio precedente per eseguire una query sull'operazione di lettura e ottenere i risultati del riconoscimento ottico dei caratteri (OCR) quando sono disponibili.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

La parte rimanente del metodo analizza i risultati del riconoscimento ottico dei caratteri (OCR) e li stampa sulla console.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

Infine, aggiungere l'altro metodo helper usato sopra, che estrae l'ID operazione dalla risposta iniziale.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

> [!div class="nextstepaction"]
> [Il testo è stato letto](?success=read-printed-handwritten-text#run-the-application) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Eseguire l'applicazione

È possibile compilare l'app con:

```console
gradle build
```

Eseguire l'applicazione con il comando `gradle run`:

```console
gradle run
```

> [!div class="nextstepaction"]
> [L'applicazione è stata eseguita](?success=run-the-application#clean-up-resources) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Le risorse sono state pulite](?success=clean-up-resources#next-steps) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come usare la libreria di Visione artificiale per Java per eseguire attività di base. Successivamente, esplorare la documentazione di riferimento per altre informazioni sulla libreria.

> [!div class="nextstepaction"]
>[Informazioni di riferimento su Visione artificiale (Java)](/java/api/overview/azure/cognitiveservices/client/computervision)


* [Informazioni su Visione artificiale](../../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).
