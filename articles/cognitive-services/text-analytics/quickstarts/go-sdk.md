---
title: 'Guida introduttiva: Libreria client di Analisi del testo per Go | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: In questo argomento avvio rapido si rileva la lingua usando la libreria client di Analisi del testo per Go di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0b4495616c750b2b3e8431e011d71ae8671af1ef
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77912642"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Guida introduttiva: Usare la libreria client di Analisi del testo per Go

[Documentazione di riferimento](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Pacchetto (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Esempi](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> Questo argomento di avvio rapido si applica solo alla versione 2.1 di Analisi del testo. Attualmente non è disponibile una libreria client v3 per Go.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/)
* Versione più recente di [Go](https://golang.org/dl/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="creare una risorsa di Analisi del testo"  target="_blank">creare una risorsa di Analisi del testo <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. 
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Analisi del testo. Questa operazione verrà eseguita più avanti nell'avvio rapido.
    * È possibile usare il piano tariffario gratuito per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-go-project"></a>Creare un nuovo progetto Go

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a un'area di lavoro per il progetto Go. L'area di lavoro conterrà tre cartelle: 

* **src**: questa directory contiene il codice sorgente e i pacchetti. Tutti i pacchetti installati con il comando `go get` saranno inclusi qui.
* **pkg**: questa directory contiene gli oggetti pacchetto Go compilati. Tutti i file hanno estensione `.a`.
* **bin**: questa directory contiene i file eseguibili binari creati durante l'esecuzione di `go install`.

> [!TIP]
> Acquisire familiarità con la struttura di un'[area di lavoro Go](https://golang.org/doc/code.html#Workspaces). Questa guida include informazioni per l'impostazione di `$GOPATH` e `$GOROOT`.

Creare un'area di lavoro denominata `my-app` e le sottodirectory necessarie per `src`, `pkg` e `bin`:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Installare la libreria client di Analisi del testo per Go

Installare la libreria client per Go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

oppure, se si usa DEP, all'interno del repository eseguire:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Creare l'applicazione Go

Creare quindi un file denominato `src/quickstart.go`:

```bash
$ cd src
$ touch quickstart.go
```

Aprire `quickstart.go` nell'IDE o nell'editor di testo preferito. Aggiungere quindi il nome del pacchetto e importare le librerie seguenti:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Modello a oggetti 

Il client di Analisi del testo è un oggetto [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) che esegue l'autenticazione in Azure tramite la chiave. Il client fornisce diversi metodi per l'analisi del testo, come singola stringa o batch. 

Il testo viene inviato all'API come elenco di `documents`, che sono oggetti `dictionary` contenenti una combinazione di attributi `id`, `text` e `language`, a seconda del metodo usato. L'attributo `text` archivia il testo da analizzare in base all'attributo `language` di origine, mentre `id` può essere un valore qualsiasi. 

L'oggetto risposta è un elenco contenente le informazioni di analisi per ogni documento. 

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Analisi del testo per Python:

* [Autenticare il client](#authenticate-the-client)
* [Analisi del sentiment](#sentiment-analysis)
* [Rilevamento della lingua](#language-detection)
* [Riconoscimento delle entità](#entity-recognition)
* [Estrazione delle frasi chiave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticare il client


In una nuova funzione creare le variabili per l'endpoint e la chiave della sottoscrizione di Azure della risorsa.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Creare un nuovo oggetto [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New). Passare la chiave alla funzione [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer), che verrà quindi passata alla proprietà `authorizer` del client.

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>Analisi del sentiment

Creare una nuova funzione denominata `SentimentAnalysis()` e creare un client usando il metodo `GetTextAnalyticsClient()` creato in precedenza. Creare un elenco di oggetti [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contenente i documenti da analizzare. Ogni oggetto conterrà un `id`, `Language` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi. 

Chiamare la funzione [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) del client e ottenere il risultato. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e il punteggio del sentiment. I punteggi più vicini a 0 indicano un sentiment negativo, mentre quelli più vicini a 1 indicano un sentiment positivo.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

Chiamare `SentimentAnalysis()` nel progetto.

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Rilevamento della lingua

Creare una nuova funzione denominata `LanguageDetection()` e creare un client usando il metodo `GetTextAnalyticsClient()` creato in precedenza. Creare un elenco di oggetti [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) contenente i documenti da analizzare. Ogni oggetto conterrà un `id` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre l'`id` può essere un valore qualsiasi. 

Chiamare la funzione [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) del client e ottenere il risultato. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e la lingua rilevata.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Chiamare `LanguageDetection()` nel progetto.

### <a name="output"></a>Output

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Riconoscimento delle entità

Creare una nuova funzione denominata `ExtractEntities()` e creare un client usando il metodo `GetTextAnalyticsClient()` creato in precedenza. Creare un elenco di oggetti [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contenente i documenti da analizzare. Ogni oggetto conterrà un `id`, `language` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi. 

Chiamare la funzione [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) del client e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati e stampare l'ID di ogni documento e il punteggio delle entità estratte.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

Chiamare `ExtractEntities()` nel progetto.

### <a name="output"></a>Output

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Creare una nuova funzione denominata `ExtractKeyPhrases()` e creare un client usando il metodo `GetTextAnalyticsClient()` creato in precedenza. Creare un elenco di oggetti [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contenente i documenti da analizzare. Ogni oggetto conterrà un `id`, `language` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi.

Chiamare la funzione [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) del client e ottenere il risultato. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le frasi chiave estratte.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Chiamare `ExtractKeyPhrases()` nel progetto.

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
