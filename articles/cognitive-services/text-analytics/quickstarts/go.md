---
title: "Avvio rapido: Uso di Go per chiamare l'API Analisi del testo"
titleSuffix: Azure Cognitive Services
description: Informazioni ed esempi di codice per iniziare a usare l'API Analisi del testo in Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: aahi
ms.openlocfilehash: 3b922dca46b575abc428aa6e76fb920ffc13d6b0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "86028004"
---
# <a name="quickstart-using-go-to-call-the-text-analytics-cognitive-service"></a>Avvio rapido: Uso di Go per chiamare il servizio cognitivo Analisi del testo 
<a name="HOLTop"></a>

Questo articolo illustra come [rilevare la lingua](#Detect), [analizzare il sentiment](#SentimentAnalysis), [estrarre frasi chiave](#KeyPhraseExtraction) e [identificare le entità collegate](#Entities) usando le  [API Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759711)  con Go.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]


<a name="Detect"></a>

## <a name="detect-language"></a>Rileva lingua

L'API Rilevamento lingua rileva la lingua di un documento di testo usando il [metodo per il rilevamento della lingua](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Creare un nuovo progetto Go nell'editor di codice preferito.
1. Aggiungere il codice riportato di seguito.
1. Copiare la chiave e l'endpoint dell'API Analisi del testo nel codice.
1. Salvare il file con estensione "go".
1. Aprire un prompt dei comandi dalla cartella radice in un computer in cui è installato Go.
1. Compilare il file, ad esempio: `go build detect.go`.
1. Eseguire il file, ad esempio: `go run detect.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    
    var subscriptionKey string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"
    const uriPath = "/text/analytics/v3.0/languages"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "text": "This is a document written in English."},
        {"id": "2", "text": "Este es un document escrito en Español."},
        {"id": "3", "text": "这是一个用中文写的文件"},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="detect-language-response"></a>Risposta alla richiesta di rilevamento della lingua

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente:

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analizzare la valutazione

L'API Analisi del sentiment rileva il sentiment di un set di record di testo usando il [metodo Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). L'esempio seguente assegna un punteggio a due documenti, uno in inglese e un altro in spagnolo.

1. Creare un nuovo progetto Go nell'editor di codice preferito.
1. Aggiungere il codice riportato di seguito.
1. Copiare la chiave e l'endpoint di Analisi del testo nel codice.
1. Salvare il file con estensione "go".
1. Aprire un prompt dei comandi dalla cartella radice in un computer in cui è installato Go.
1. Compilare il file, ad esempio: `go build sentiment.go`.
1. Eseguire il file, ad esempio: `go run sentiment.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKey string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    const uriPath = "/text/analytics/v3.0/sentiment"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="analyze-sentiment-response"></a>Risposta alla richiesta di analisi del sentiment

Il risultato viene considerato positivo se il punteggio assegnato è più vicino a 1.0 e negativo se il punteggio assegnato è più vicino a 0.0.
Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente:

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Estrarre le frasi chiave

L'API Estrazione frasi chiave consente di estrarre le frasi chiave da un documento di testo usando il [metodo per le frasi chiave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). L'esempio seguente estrae le frasi chiave per i documenti in inglese e in spagnolo.

1. Creare un nuovo progetto Go nell'editor di codice preferito.
1. Aggiungere il codice riportato di seguito.
1. Copiare la chiave e l'endpoint di Analisi del testo nel codice.
1. Salvare il file con estensione "go".
1. Aprire un prompt dei comandi in un computer in cui è installato Go.
1. Compilare il file, ad esempio: `go build key-phrases.go`.
1. Eseguire il file, ad esempio: `go run key-phrases.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKey string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"
    
    const uriPath = "/text/analytics/v3.0/keyPhrases"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
        {"id": "3", "language": "en", "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="extract-key-phrases-response"></a>Risposta alla richiesta di estrazione delle frasi chiave

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente:

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identificare le entità

L'API Entità identifica le entità note in un documento di testo usando il metodo [Entities](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634). Le [entità](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) estraggono parole dal testo, ad esempio "Stati Uniti" e quindi forniscono il tipo e/o il collegamento a Wikipedia relativo alla parola. Il tipo di "Stati Uniti" è `location`, mentre il collegamento a Wikipedia è `https://en.wikipedia.org/wiki/United_States`.  L'esempio seguente identifica le entità per i documenti in inglese.

1. Creare un nuovo progetto Go nell'editor di codice preferito.
1. Aggiungere il codice riportato di seguito.
1. Salvare il file con estensione "go".
1. Aprire un prompt dei comandi in un computer in cui è installato Go.
1. Compilare il file, ad esempio: `go build entities.go`.
1. Eseguire il file, ad esempio: `go run entities.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {

    var subscriptionKey string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"
    
    const uriPath = "/text/analytics/v3.0/entities/recognition/general"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "Microsoft is an It company."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="entity-extraction-response"></a>Risposta di estrazione di entità

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Vedere anche

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti](../text-analytics-resource-faq.md)
