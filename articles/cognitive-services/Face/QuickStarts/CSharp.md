---
title: "Avvio rapido: Rilevare i visi in un'immagine con l'API REST di Azure e C#"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si userà l'API REST Viso di Azure con C# per rilevare i visi in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 050ff882e70d162749e1c4e913b523e79c81a7a2
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87834100"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Avvio rapido: Rilevare i visi in un'immagine con l'API REST Viso e C#

In questa guida di avvio rapido si userà l'API REST Viso di Azure con C# per rilevare i visi umani in un'immagine.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="creare una risorsa Viso"  target="_blank">creare una risorsa Viso <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Viso. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
- Qualsiasi edizione di [Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

1. In Visual Studio creare un nuovo progetto **App console (.NET Framework)** e assegnargli il nome **FaceDetection**.
1. Se la soluzione contiene anche altri progetti, selezionare questo come progetto di avvio singolo.

## <a name="add-face-detection-code"></a>Aggiungere codice di rilevamento volto

Aprire il nuovo file *Program.cs* del progetto. In questo caso, si aggiungerà il codice necessario per caricare immagini e rilevare i visi.

### <a name="include-namespaces"></a>Includere gli spazi dei nomi

Aggiungere le istruzioni `using` seguenti all'inizio del file *Program.cs*.

```csharp
using System;
using System.IO;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
```

### <a name="add-essential-fields"></a>Aggiungere campi fondamentali

Aggiungere la classe **Program** contenente i campi seguenti. Questi dati consentono di specificare come connettersi al servizio Viso e dove ottenere i dati di input. Sarà necessario aggiornare il campo `subscriptionKey` con il valore della chiave di sottoscrizione e potrebbe essere necessario modificare la stringa `uriBase` in modo che contenga la stringa dell'endpoint della risorsa.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
namespace DetectFace
{
    class Program
    {

        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // replace <myresourcename> with the string found in your endpoint URL
        const string uriBase =
            "https://<myresourcename>.cognitive.microsoft.com/face/v1.0/detect";
```

### <a name="receive-image-input"></a>Ricevere l'input dell'immagine

Aggiungere il codice seguente al metodo **Main** della classe **Program**. Questo codice scrive un prompt nella console che richiede all'utente di immettere l'URL di un'immagine. Viene quindi chiamato un altro metodo, **MakeAnalysisRequest**, per elaborare l'immagine nel percorso specificato.

```csharp
        static void Main(string[] args)
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Detect faces:");
            Console.Write(
                "Enter the path to an image with faces that you wish to analyze: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                try
                {
                    MakeAnalysisRequest(imageFilePath);
                    Console.WriteLine("\nWait a moment for the results to appear.\n");
                }
                catch (Exception e)
                {
                    Console.WriteLine("\n" + e.Message + "\nPress Enter to exit...\n");
                }
            }
            else
            {
                Console.WriteLine("\nInvalid file path.\nPress Enter to exit...\n");
            }
            Console.ReadLine();
        }
```

### <a name="call-the-face-detection-rest-api"></a>Chiamare l'API REST di rilevamento volto

Aggiungere il metodo seguente alla classe **Program**. Il metodo costruisce una chiamata REST all'API Viso per rilevare informazioni sul viso nell'immagine remota (la stringa `requestParameters` specifica quali attributi del viso recuperare), quindi scrive i dati di output in una stringa JSON.

Nei passaggi seguenti verranno definiti i metodi helper.

```csharp
        // Gets the analysis of the specified image by using the Face REST API.
        static async void MakeAnalysisRequest(string imageFilePath)
        {
            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add(
                "Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false" +
                "&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses," +
                "emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = uriBase + "?" + requestParameters;

            HttpResponseMessage response;

            // Request body. Posts a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json"
                // and "multipart/form-data".
                content.Headers.ContentType =
                    new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                response = await client.PostAsync(uri, content);

                // Get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n");
                Console.WriteLine(JsonPrettyPrint(contentString));
                Console.WriteLine("\nPress Enter to exit...");
            }
        }
```

### <a name="process-the-input-image-data"></a>Elaborare i dati di immagine di input

Aggiungere il metodo seguente alla classe **Program**. Questo metodo converte l'immagine all'URL specificato in una matrice di byte.

```csharp
        // Returns the contents of the specified file as a byte array.
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
```

### <a name="parse-the-json-response"></a>Analizzare la risposta JSON.

Aggiungere il metodo seguente alla classe **Program**. Questo metodo formatta l'input JSON in modo da renderlo più facilmente leggibile. L'app scriverà i dati stringa nella console. È quindi possibile chiudere la classe e lo spazio dei nomi.

```csharp
        // Formats the given JSON string by adding line breaks and indents.
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}
```

## <a name="run-the-app"></a>Eseguire l'app

Una risposta con esito positivo visualizzerà i dati sui volti in formato JSON facilmente leggibile. Ad esempio:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
            "neutral": 0.986,
            "sadness": 0.009,
            "surprise": 0.005
         },
         "blur": {
            "blurLevel": "low",
            "value": 0.06
         },
         "exposure": {
            "exposureLevel": "goodExposure",
            "value": 0.67
         },
         "noise": {
            "noiseLevel": "low",
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
               },
               {
                  "color": "black",
                  "confidence": 0.87
               },
               {
                  "color": "other",
                  "confidence": 0.51
               },
               {
                  "color": "blond",
                  "confidence": 0.08
               },
               {
                  "color": "red",
                  "confidence": 0.08
               },
               {
                  "color": "gray",
                  "confidence": 0.02
               }
            ]
         }
      }
   }
]
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata una semplice applicazione console .NET che usa le chiamate REST con il servizio Viso di Azure per rilevare i visi in un'immagine e restituire i relativi attributi. Successivamente, esplorare la documentazione di riferimento dell'API Viso per altre informazioni sugli scenari supportati.

> [!div class="nextstepaction"]
> [API Viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
