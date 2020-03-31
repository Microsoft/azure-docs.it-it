---
title: "Guida introduttiva: Controllare l'ortografia con l'SDK Controllo ortografico Bing per C#"
titleSuffix: Azure Cognitive Services
description: Introduzione all'uso dell'API REST Controllo ortografico Bing per controllare l'ortografia e la grammatica.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 1cda7032d5bfe58e9f8bcbdb8b18dd597a691441
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78273537"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Guida introduttiva: Controllare l'ortografia con l'SDK Controllo ortografico Bing per C#

Usare questa guida introduttiva per iniziare a eseguire il controllo ortografico con l'SDK Controllo ortografico Bing per C#. Mentre Controllo ortografico Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK fornisce un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Dipendenze dell'applicazione

* Qualsiasi edizione di [Visual Studio 2017 o versioni successive](https://visualstudio.microsoft.com/downloads/).
* [Pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) di Controllo ortografico Bing

Per aggiungere l'SDK di Controllo ortografico Bing a un progetto, selezionare **Gestisci pacchetti NuGet** da **Esplora soluzioni** in Visual Studio. Aggiungere il pacchetto `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. Il pacchetto installa anche le dipendenze seguenti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare una nuova soluzione di console C# in Visual Studio. Aggiungere quindi l'istruzione `using` seguente.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Creare una nuova classe. Creare quindi una funzione asincrona denominata `SpellCheckCorrection()` che accetta una chiave di sottoscrizione e invia la richiesta di controllo ortografico.

3. Creare un'istanza del client creando un nuovo oggetto `ApiKeyServiceClientCredentials`. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Inviare la richiesta e leggere la risposta

1. Nella funzione creata sopra eseguire i passaggi seguenti. Inviare la richiesta di controllo ortografico con il client. Aggiungere il testo da controllare al parametro `text` e impostare la modalità su `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Ottenere il primo risultato del controllo ortografico, se presente. Stampare la prima parola con errore di ortografia (token) restituita, il tipo di token e il numero di suggerimenti.

    ```csharp
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Ottenere la prima correzione suggerita, se presente. Stampare il punteggio del suggerimento e la parola suggerita. 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>Eseguire l'applicazione

Compilare ed eseguire il progetto. Se si usa Visual Studio, premere **F5** per eseguire il debug del file.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](tutorials/spellcheck.md)

- [Informazioni sull'API Controllo ortografico Bing](overview.md)
- [Guida di riferimento per l'SDK Controllo ortografico Bing in C#](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)
