---
title: Personalizzazione dei set di impostazioni di Media Encoder Standard | Documentazione Microsoft
description: Questo argomento illustra come eseguire la codifica avanzata personalizzando i set di impostazioni delle attività di Media Encoder Standard. Questo argomento illustra come usare Media Services .NET SDK per creare un processo e un'attività di codifica. Illustra anche come specificare set di impostazioni personalizzati per il processo di codifica.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 6c0abeedb1253c2561f2d435aa3fe5a428418a50
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019471"
---
# <a name="customizing-media-encoder-standard-presets"></a>Personalizzazione dei set di impostazioni di Media Encoder Standard  

## <a name="overview"></a>Panoramica

Questo articolo illustra come eseguire le attività di codifica avanzata con Media Encoder Standard usando un set di impostazioni personalizzato. In questo articolo viene usato .NET per creare un'attività di codifica e un processo che la esegue.  

L'articolo illustra come personalizzare un set di impostazioni partendo dal set di impostazioni [Codec video H.264 a bitrate multiplo con risoluzione 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) e riducendo il numero di livelli. L'articolo [Personalizzazione dei set di impostazioni di Media Encoder Standard](media-services-advanced-encoding-with-mes.md) illustra i set di impostazioni personalizzati che è possibile usare per eseguire attività di codifica avanzata.

> [!NOTE]
> I set di impostazioni personalizzati descritti in questo articolo non possono essere usati nelle trasformazioni di [servizi multimediali V3](../latest/index.yml) o nei comandi dell'interfaccia della riga di comando. Per ulteriori informazioni, vedere le [linee guida per la migrazione da V2 a V3](../latest/migrate-from-v2-to-v3.md) .

## <a name="customizing-a-mes-preset"></a><a id="customizing_presets"></a> Personalizzazione di un set di impostazioni di Media Encoder Standard

### <a name="original-preset"></a>Set di impostazioni originale

Salvare il codice JSON definito nell'articolo [Codec video H.264 a bitrate multiplo con risoluzione 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) in un file con estensione json. Ad esempio, **CustomPreset_JSON.json**.

### <a name="customized-preset"></a>Set di impostazioni personalizzato

Aprire il file **CustomPreset_JSON.json** e rimuovere i primi tre livelli da **H264Layers** in modo che il file sia simile al seguente.

```json 
    {  
      "Version": 1.0,  
      "Codecs": [  
        {  
          "KeyFrameInterval": "00:00:02",  
          "H264Layers": [  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 1000,  
              "MaxBitrate": 1000,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 650,  
              "MaxBitrate": 650,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 400,  
              "MaxBitrate": 400,  
              "BufferWindow": "00:00:05",  
              "Width": 320,  
              "Height": 180,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            }  
          ],  
          "Type": "H264Video"  
        },  
        {  
          "Profile": "AACLC",  
          "Channels": 2,  
          "SamplingRate": 48000,  
          "Bitrate": 128,  
          "Type": "AACAudio"  
        }  
      ],  
      "Outputs": [  
        {  
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
          "Format": {  
            "Type": "MP4Format"  
          }  
        }  
      ]  
    }  
```

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Codifica con l’SDK .NET dei servizi multimediali

Il seguente codice usa l'SDK .NET di Servizi multimediali per eseguire le seguenti attività: 

- Creare un processo di codifica.
- Ottenere un riferimento al codificatore Media Encoder Standard.
- Caricare il set di impostazioni personalizzato JSON creato nella sezione precedente. 

    ```csharp
    // Load the JSON from the local file.
    string configuration = File.ReadAllText(fileName);  
    ```

- Aggiungere un'attività di codifica al processo. 
- Specificare l’asset di input da codificare.
- Creare un asset di output che contenga l'asset codificato.
- Aggiungere un gestore eventi per controllare l'avanzamento del processo.
- Inviare il processo.
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio

Configurare l'ambiente di sviluppo e popolare il file di app.config con le informazioni di connessione, come descritto in [sviluppo di servizi multimediali con .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Esempio   

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace CustomizeMESPresests
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using custom presets.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }

    }
}
```

## <a name="see-also"></a>Vedere anche

- [Come codificare con una trasformazione personalizzata usando l'interfaccia della riga di comando](../latest/custom-preset-cli-howto.md)
- [Codifica con Servizi multimediali v3](../latest/encoding-concept.md)

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
