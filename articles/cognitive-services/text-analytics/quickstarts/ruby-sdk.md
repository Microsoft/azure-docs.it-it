---
title: 'Guida introduttiva: Libreria client di Analisi del testo per Ruby | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si rileva la lingua usando la libreria client di Analisi del testo per Ruby di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: 8afceb19af0d177415d0b68b5d38f19d18835af5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291772"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Guida introduttiva: Usare la libreria client di Analisi del testo per Ruby

Introduzione alla libreria client di Analisi del testo. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

Usare la libreria client di Analisi del testo per eseguire queste operazioni:

* Analisi del sentiment
* Rilevamento della lingua
* Riconoscimento delle entità
* Estrazione di frasi chiave

> [!NOTE]
> Questo argomento di avvio rapido si applica solo alla versione 2.1 di Analisi del testo. Attualmente non è disponibile una libreria client v3 per Ruby.

[Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [Pacchetto (RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [Esempi](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Versione corrente di [Ruby](https://www.ruby-lang.org/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="creare una risorsa di Analisi del testo"  target="_blank">creare una risorsa di Analisi del testo <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. 
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Analisi del testo. Questa operazione verrà eseguita più avanti nell'avvio rapido.
    * È possibile usare il piano tariffario gratuito per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-ruby-application"></a>Creare una nuova applicazione Ruby

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. Creare quindi un file denominato `GemFile` e un file Ruby per il codice.

```console
mkdir myapp && cd myapp
```

Nel file `GemFile` aggiungere le righe seguenti per aggiungere la libreria client come dipendenza.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

Importare i pacchetti seguenti nel file Ruby.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Creare le variabili per l'endpoint e la chiave di Azure della risorsa. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Modello a oggetti 

Il client di Analisi del testo esegue l'autenticazione in Azure tramite la chiave. Il client fornisce diversi metodi per l'analisi del testo, come singola stringa o batch. 

Il testo viene inviato all'API come elenco di `documents`, che sono oggetti `dictionary` contenenti una combinazione di attributi `id`, `text` e `language`, a seconda del metodo usato. L'attributo `text` archivia il testo da analizzare in base all'attributo `language` di origine, mentre `id` può essere un valore qualsiasi. 

L'oggetto risposta è un elenco contenente le informazioni di analisi per ogni documento. 

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Analisi del testo per Ruby:

* [Autenticare il client](#authenticate-the-client)
* [Analisi del sentiment](#sentiment-analysis)
* [Rilevamento della lingua](#language-detection)
* [Riconoscimento delle entità](#entity-recognition)
* [Estrazione delle frasi chiave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare una classe denominata `TextAnalyticsClient`. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

In questa classe creare una funzione denominata `initialize` per eseguire l'autenticazione del client tramite la chiave e l'endpoint. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

All'esterno della classe usare la funzione `new()` del client per crearne un'istanza.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Analisi del sentiment

Nell'oggetto client creare una funzione denominata `AnalyzeSentiment()` che accetti un elenco di documenti di input che verranno creati in un secondo momento. Chiamare la funzione `sentiment()` del client e ottenere il risultato. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e il punteggio del sentiment. I punteggi più vicini a 0 indicano un sentiment negativo, mentre quelli più vicini a 1 indicano un sentiment positivo.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

All'esterno della funzione creare una nuova funzione denominata `SentimentAnalysisExample()` che accetti l'oggetto `TextAnalyticsClient` creato in precedenza. Creare un elenco di oggetti `MultiLanguageInput` contenenti i documenti da analizzare. Ogni oggetto conterrà un `id`, `Language` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi. Chiamare quindi la funzione `AnalyzeSentiment()` del client.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Chiamare la funzione `SentimentAnalysisExample()`.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Output

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Rilevamento della lingua

Nell'oggetto client creare una funzione denominata `DetectLanguage()` che accetti un elenco di documenti di input che verranno creati in un secondo momento. Chiamare la funzione `detect_language()` del client e ottenere il risultato. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e la lingua rilevata.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

All'esterno della funzione creare una nuova funzione denominata `DetectLanguageExample()` che accetti l'oggetto `TextAnalyticsClient` creato in precedenza. Creare un elenco di oggetti `LanguageInput` contenenti i documenti da analizzare. Ogni oggetto conterrà un `id` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre l'`id` può essere un valore qualsiasi. Chiamare quindi la funzione `DetectLanguage()` del client.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Chiamare la funzione `DetectLanguageExample()`.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Riconoscimento delle entità

Nell'oggetto client creare una funzione denominata `RecognizeEntities()` che accetti un elenco di documenti di input che verranno creati in un secondo momento. Chiamare la funzione `entities()` del client e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le entità riconosciute.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

All'esterno della funzione creare una nuova funzione denominata `RecognizeEntitiesExample()` che accetti l'oggetto `TextAnalyticsClient` creato in precedenza. Creare un elenco di oggetti `MultiLanguageInput` contenenti i documenti da analizzare. Ogni oggetto conterrà un `id`, un elemento `language` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi. Chiamare quindi la funzione `RecognizeEntities()` del client.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Chiamare la funzione `RecognizeEntitiesExample()`.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Output

```console
===== ENTITY RECOGNITION =====
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

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Nell'oggetto client creare una funzione denominata `ExtractKeyPhrases()` che accetti un elenco di documenti di input che verranno creati in un secondo momento. Chiamare la funzione `key_phrases()` del client e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le frasi chiave estratte.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

All'esterno della funzione creare una nuova funzione denominata `KeyPhraseExtractionExample()` che accetti l'oggetto `TextAnalyticsClient` creato in precedenza. Creare un elenco di oggetti `MultiLanguageInput` contenenti i documenti da analizzare. Ogni oggetto conterrà un `id`, un elemento `language` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi. Chiamare quindi la funzione `ExtractKeyPhrases()` del client.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Chiamare la funzione `KeyPhraseExtractionExample()`.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

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
                rock
Document ID: 4
         Key phrases:
                fútbol
```
