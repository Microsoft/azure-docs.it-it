---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 4573224a77b080e0ba6cefe5069164466e7df5b9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586524"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Creare un progetto .NET Core

Aprire un nuovo prompt dei comandi (o una nuova sessione del terminale) ed eseguire i comandi seguenti:

```console
dotnet new console -o transliterate-sample
cd transliterate-sample
```

Il primo comando esegue due operazioni. Consente di creare una nuova applicazione console .NET e di creare una directory denominata `transliterate-sample`. Il secondo comando consente di passare alla directory del progetto.

In seguito sarà necessario installare Json.NET. Dalla directory del progetto eseguire:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Selezionare la versione del linguaggio C#

Questa guida introduttiva richiede C# 7.1 o versione successiva. Esistono alcuni modi per modificare la versione C# per il progetto. In questa Guida, vi mostreremo come regolare il file `transliterate-sample.csproj`. Per tutte le opzioni disponibili, come ad esempio cambiare lingua in Visual Studio, vedere [Selezionare la versione del linguaggio C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Aprire il progetto, quindi aprire `transliterate-sample.csproj`. Assicurarsi che `LangVersion` sia impostato su 7.1 o versione successiva. Se non esiste un gruppo di proprietà per la versione del linguaggio, aggiungere le righe seguenti:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Aggiungere gli spazi dei nomi obbligatori al progetto

Il comando `dotnet new console`, eseguito in precedenza, ha consentito di creare un progetto, incluso il file `Program.cs`. Si tratta del file in cui verrà inserito il codice dell'applicazione. Aprire il file `Program.cs` e sostituire le istruzioni using seguenti. Queste istruzioni assicurano l'accesso a tutti i tipi necessari per compilare ed eseguire l'app di esempio.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Creare le classi per la risposta JSON

Successivamente, si creerà una classe usata per la deserializzazione della risposta JSON restituita da Translator.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator.
/// </summary>
public class TransliterationResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}
```

## <a name="get-subscription-information-from-environment-variables"></a>Ottenere informazioni sulla sottoscrizione dalle variabili di ambiente

Aggiungere le righe seguenti alla classe `Program`. Queste righe leggono la chiave e l'endpoint della sottoscrizione dalle variabili di ambiente e generano un errore se si verificano problemi.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-transliterate-text"></a>Creare una funzione per traslitterare il testo

All'interno della classe `Program` creare una funzione asincrona denominata `TransliterateTextRequest()`. Questa funzione accetta quattro argomenti: `subscriptionKey`, `endpoint`, `route` e `inputText`.

```csharp
static public async Task TransliterateTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>Serializzare la richiesta di traduzione

In seguito, è necessario creare e serializzare l'oggetto JSON che include il testo da tradurre. Tenere presente che è possibile passare più di un oggetto nel `body`.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Creare un'istanza del client ed effettuare una richiesta

Queste righe consentono di creare un'istanza di `HttpClient` e `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Costruire una richiesta e stampare la risposta

All'interno di `HttpRequestMessage` vengono eseguite le operazioni seguenti:

* Dichiarare il metodo HTTP
* Costruire l'URI della richiesta
* Inserire il corpo della richiesta (oggetto JSON serializzato)
* Aggiungere le intestazioni obbligatorie
* Effettuare una richiesta asincrona
* Stampare la risposta

Aggiungere questo codice a `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to Post.
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(endpoint + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
TransliterationResult[] deserializedOutput = JsonConvert.DeserializeObject<TransliterationResult[]>(result);
// Iterate over the deserialized results.
foreach (TransliterationResult o in deserializedOutput)
{
    Console.WriteLine("Transliterated to {0} script: {1}", o.Script, o.Text);
}
```

Se si usa una sottoscrizione multiservizio di Servizi cognitivi, è necessario includere anche `Ocp-Apim-Subscription-Region` nei parametri della richiesta. [Informazioni sull'autenticazione con la sottoscrizione multiservizio](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

L'ultimo passaggio consiste nel chiamare `TransliterateTextRequest()` nella funzione `Main`. In questo esempio viene traslitterato un testo dal giapponese al latino. Individuare `static void Main(string[] args)` e sostituirlo con il codice seguente:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate
    string route = "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
    string textToTransliterate = @"こんにちは";
    await TransliterateTextRequest(subscriptionKey, endpoint, route, textToTransliterate);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

Si noterà che in `Main` si sta dichiarando `subscriptionKey`, `endpoint`, `route` e lo script da traslitterare `textToTransliterate`.

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

A questo punto è possibile eseguire l'app di esempio. Dalla riga di comando, o dalla sessione di terminale, passare alla directory del progetto ed eseguire:

```console
dotnet run
```

## <a name="sample-response"></a>Risposta di esempio

Dopo aver eseguito l'esempio, nel terminale si dovrebbe visualizzare quanto segue:

```bash
Transliterated to latn script: Kon\'nichiwa
```

Questo messaggio viene compilato dal file JSON non elaborato, che avrà un aspetto simile al seguente:

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Pulire le risorse

Assicurarsi di rimuovere eventuali informazioni riservate dal codice sorgente dell'app di esempio, ad esempio le chiavi di sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le informazioni di riferimento sulle API per sapere quali operazioni è possibile eseguire con Translator.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
