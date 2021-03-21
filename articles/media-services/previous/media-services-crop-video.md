---
title: Questo articolo illustra come ritagliare video con Media Encoder Standard - Azure | Documentazione Microsoft
description: Il ritaglio è il processo di selezione di una finestra rettangolare all'interno del fotogramma video per codificare solo i pixel all'interno di quella finestra. Questo articolo illustra come ritagliare i video con Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 6a15e57884f85e2d56b77c3e5eec1267133fe96d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016730"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Ritagliare video con Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

È possibile usare Media Encoder Standard (MES) per ritagliare l'input video. Il ritaglio è il processo di selezione di una finestra rettangolare all'interno del fotogramma video per codificare solo i pixel all'interno di quella finestra. Il diagramma seguente illustra il processo.

![Ritagliare un video](./media/media-services-crop-video/media-services-crop-video01.png)

Si supponga di avere come input un video che ha una risoluzione di 1920x1080 pixel (proporzioni 16:9), ma presenta barre nere (formato 4:3) a sinistra e a destra, quindi solo una finestra in formato 4:3 o di 1440x1080 pixel contiene il video attivo. È possibile usare MES per ritagliare o modificare le barre nere e codificare l'area di 1440x1080 pixel.

Il ritaglio in MES è una fase di pre-elaborazione, quindi i parametri di ritaglio nel set di impostazioni di codifica si applicano al video di input originale. La codifica è una fase successiva e le impostazioni di larghezza/altezza si applicano al video *pre-elaborato* e non al video originale. Quando si progetta il set di impostazioni è necessario eseguire queste operazioni: (a) selezionare i parametri di ritaglio in base al video di input originale e (b) selezionare le impostazioni di codifica in base al video ritagliato. Se le impostazioni di codifica non corrispondono al video ritagliato, l'output non sarà quello previsto.

L'argomento [seguente](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) illustra come creare un processo di codifica con MES e come specificare un set di impostazioni personalizzato per l'attività di codifica. 

## <a name="creating-a-custom-preset"></a>Creazione di un set di impostazioni personalizzato
Nell'esempio illustrato nel diagramma:

1. L'input originale è 1920x1080
2. Deve essere ritagliato in un output di 1440x1080, centrato nel frame di input
3. Ciò equivale a un offset X (1920 - 1440)/2 = 240 e un offset Y pari a zero
4. La larghezza e l'altezza del rettangolo di ritaglio sono rispettivamente di 1440 e 1080
5. Nella fase di codifica, l'attività consiste nel produrre tre livelli con risoluzioni di 1440x1080, 960x720 e 480x360

### <a name="json-preset"></a>Set di impostazioni JSON

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Crop": {
          "X": 240,
          "Y": 0,
          "Width": 1440,
          "Height": 1080
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1440,
          "Height": 1080,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1250,
          "MaxBitrate": 1250,
          "BufferWindow": "00:00:05",
          "Width": 480,
          "Height": 360,
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

## <a name="restrictions-on-cropping"></a>Restrizioni relative al ritaglio
La funzionalità di ritaglio è concepita per essere manuale. È necessario caricare il video di input in uno strumento di modifica appropriato che consente di selezionare i frame di interesse, posizionare il cursore per determinare gli offset per il rettangolo di ritaglio, per determinare il set di impostazioni di codifica ottimizzato per quel particolare video e così via. Questa funzionalità non ha lo scopo di abilitare operazioni quali il rilevamento e la rimozione automatici dei bordi letterbox/pillarbox neri nel video di input.

Alla funzionalità di ritaglio si applicano le limitazioni seguenti. Se queste limitazioni non vengono osservate, l'attività di codifica produrrà errori o un output imprevisto.

1. Le coordinate e le dimensioni del rettangolo di ritaglio devono rientrare nel video di input
2. Come indicato in precedenza, la larghezza e l'altezza nelle impostazioni di codifica devono corrispondere al video ritagliato
3. Il ritaglio si applica a video acquisiti in modalità orizzontale, ovvero non è applicabile a video registrati con uno smartphone con orientamento verticale
4. Il ritaglio funziona in modo ottimale con video progressivo acquisito con pixel quadrati

## <a name="provide-feedback"></a>Fornire il feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Passaggio successivo
Vedere i percorsi di apprendimento di Servizi multimediali di Azure per informazioni sulle potenti funzionalità offerte da Servizi multimediali di Azure.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
