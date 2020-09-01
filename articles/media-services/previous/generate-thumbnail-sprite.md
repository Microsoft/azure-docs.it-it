---
title: Generare uno sprite di anteprima con Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento illustra come generare uno sprite di anteprima con Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: juliako
ms.openlocfilehash: 1d05e73bc9aa51bf20121790de94b1b6d0e26615
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268571"
---
# <a name="generate-a-thumbnail-sprite"></a>Generare uno sprite di anteprima

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

È possibile usare Media Encoder Standard per generare uno sprite di anteprima, ovvero un file JPEG contenente più piccole anteprime di risoluzione raccolte in un'unica immagine (grande), insieme a un file VTT. Il file VTT specifica l'intervallo di tempo occupato da ogni anteprima nel video di input, insieme alle dimensioni e alle coordinate dell'anteprima nel file JPEG di grandi dimensioni. Il file VTT e l'immagine sprite consentono ai lettori video di mostrare una barra di scorrimento "visiva" e offrire così all'utente un feedback visivo mentre esegue lo scrubbing avanti e indietro nella sequenza temporale del video.

Per poter usare Media Encoder Standard allo scopo di generare un'anteprima sprite, il set di impostazioni:

1. Deve usare il formato di immagine di anteprima JPG
2. Deve specificare i valori Start/Step/Range come timestamp o valori % (e non come numero di frame) 
    
    1. È possibile combinare timestamp e valori %

3. Deve contenere il valore SpriteColumn come numero non negativo maggiore o uguale a 1

    1. Se SpriteColumn è impostato su M >= 1, l'immagine di output è un rettangolo con M colonne. Se il numero di immagini di anteprima generate tramite #2 non è un multiplo esatto di M, l'ultima riga sarà incompleta e rimarranno pixel neri.  

Ecco un esempio:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>Problemi noti

1.  Non è possibile generare un'immagine sprite con un'unica riga di immagini (SpriteColumn = 1 restituisce un'immagine con una singola colonna).
2.  Non è ancora supportata la suddivisione delle immagini sprite in immagini JPEG di dimensioni limitate. È quindi necessario prestare attenzione a limitare il numero di anteprime e le relative dimensioni, in modo che lo sprite di anteprima risultante non sia superiore a 8M pixel.
3.  Azure Media Player supporta immagini sprite nei browser Microsoft Edge, Chrome e Firefox. L'analisi del file VTT non è supportata in Internet Explorer 11.

## <a name="next-steps"></a>Passaggi successivi

[Codificare contenuti](media-services-encode-asset.md)
