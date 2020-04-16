---
title: Moderazione video con revisione umana - Content Moderator
titleSuffix: Azure Cognitive Services
description: Utilizzare la moderazione video assistita da computer e lo strumento Di revisione per moderare i contenuti inappropriati
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0c031a890efc7fad7e5d9caefce3b0e66c515d90
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404245"
---
# <a name="video-moderation-with-human-review"></a>Moderazione video con revisione umana

Utilizza [lo strumento](Review-Tool-User-Guide/human-in-the-loop.md) di [moderazione video](video-moderation-api.md) e revisione assistita dal moderatore dei contenuti per moderare video e trascrizioni per adulti (espliciti) e (suggestivi) per ottenere i migliori risultati per la tua attività.

## <a name="video-trained-classifier-preview"></a>Classificatore con riconoscimento di video (anteprima)

La classificazione video con supporto di computer può essere eseguita con modelli per il riconoscimento di immagini o di video. A differenza dei classificatori video con riconoscimento di immagini, il classificatore di video con contenuti spinti e per adulti di Microsoft è sottoposto a un processo di riconoscimento video. Questo metodo comporta una migliore qualità della corrispondenza.

## <a name="shot-detection"></a>Rilevamento inquadratura

Quando si producono i dettagli di classificazione, ulteriori funzionalità di video intelligence assicurano una maggiore flessibilità per l'analisi dei video. Invece di produrre solo i fotogrammi, il servizio di moderazione video di Microsoft fornisce anche informazioni a livello di inquadratura. È pertanto possibile analizzare i video a livello di inquadratura e di fotogramma.

## <a name="key-frame-detection"></a>Rilevamento di fotogrammi chiave

Invece di produrre fotogrammi a intervalli regolari, il servizio di moderazione video identifica e restituisce solo i fotogrammi potenzialmente completi (validi). Questa funzionalità assicura una maggiore efficienza nella generazione dei fotogrammi per l'analisi dei contenuti spinti e per adulti a livello di fotogramma.

L'estratto seguente mostra una risposta parziale con potenziali inquadrature, fotogrammi chiave e punteggi di contenuti spinti e per adulti:

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>Visualizzazione per le revisioni umane

Per i casi meno netti, è indispensabile una soluzione di revisione umana per il rendering dei video, dei relativi fotogrammi e dei tag assegnati automaticamente. I moderatori umani che esaminano video e fotogrammi ottengono una visione completa delle informazioni, modificano i tag e inoltrano le loro decisioni.

![Visualizzazione predefinita dello strumento di revisione di video](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Visualizzazione lettore per la revisione a livello video

Le decisioni binarie a livello video sono rese possibili grazie a una visualizzazione lettore video che mostra i potenziali fotogrammi con contenuto spinto e per adulti. I revisori umani scorrono il video con diverse opzioni di velocità per esaminare le scene e confermano le loro decisioni attivando e disattivando i tag.

![Visualizzazione lettore dello strumento di revisione di video](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Visualizzazione fotogrammi per revisioni dettagliate

La revisione dettagliata di un video per un'analisi fotogramma per fotogramma è resa possibile grazie a una visualizzazione basata su fotogrammi. I revisori umani esaminano e selezionano uno o più fotogrammi e attivano o disattivano i tag per confermare le loro decisioni. Un passaggio successivo facoltativo è l'offuscamento dei fotogrammi o dei contenuti offensivi.

![Visualizzazione fotogrammi dello strumento di revisione di video](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderazione delle trascrizioni

In genere, i video contengono anche una voce fuori campo che necessita di moderazione in caso di commenti potenzialmente offensivi. È possibile usare il servizio Azure Media Indexer per il riconoscimento vocale e l'API di revisione di Content Moderator per inviare la trascrizione per la moderazione del testo all'interno dello strumento di revisione.

![Visualizzazione trascrizione dello strumento di revisione di video](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Passaggi successivi

- Apprendere le nozioni fondamentali con la [guida introduttiva alla moderazione video](video-moderation-api.md).
- Imparare a generare [revisioni di video](video-reviews-quickstart-dotnet.md) dall'output moderato per i revisori umani.
- Aggiungere [revisioni di trascrizioni video](video-transcript-reviews-quickstart-dotnet.md) alle revisioni di video.
- Fare riferimento all'esercitazione dettagliata su come sviluppare una [soluzione completa di moderazione video](video-transcript-moderation-review-tutorial-dotnet.md).