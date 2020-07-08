---
title: Estrarre testo dalle immagini
titleSuffix: Azure Cognitive Search
description: Elaborare ed estrarre testo e altre informazioni dalle immagini in pipeline di ricerca cognitiva di Azure.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98054060210f55803d6e2811e1f494fd3ff00e48
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76838259"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>Come elaborare ed estrarre informazioni dalle immagini negli scenari di arricchimento di intelligenza artificiale

Azure ricerca cognitiva offre diverse funzionalità per l'uso di immagini e file di immagine. Durante l'individuazione dei documenti, è possibile usare il parametro *imageAction* per estrarre testo da foto o immagini contenenti testo alfanumerico, come la parola "STOP" in un segnale di arresto. Altri scenari includono la generazione della rappresentazione testuale di un'immagine, ad esempio "tarassaco" per una foto di un tarassaco o del colore "giallo". È possibile anche estrarre i metadati relativi all'immagine, ad esempio le dimensioni.

Questo articolo illustra l'elaborazione di immagini in modo più dettagliato e fornisce indicazioni per l'uso di immagini in una pipeline di arricchimento di intelligenza artificiale.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Ottenere immagini normalizzate

Nell'ambito del processo di individuazione dei documenti, è disponibile un nuovo set di parametri di configurazione dell'indicizzatore per la gestione di file di immagine o di immagini incorporate nei file. Questi parametri consentono di normalizzare le immagini in modo da poterle elaborare ulteriormente. La normalizzazione delle immagini le rende più uniformi. Le immagini di grandi dimensioni vengono ridimensionate a un'altezza e una larghezza tali da poter essere usate. Per le immagini che forniscono metadati sull'orientamento, la rotazione dell'immagine viene impostata per il caricamento verticale. Le modifiche a livello di metadati vengono acquisite in un tipo complesso creato per ogni immagine. 

Non è possibile disattivare la normalizzazione delle immagini. Le competenze che prevedono l'iterazione sulle immagini richiedono immagini normalizzate. Per abilitare la normalizzazione delle immagini in un indicizzatore, è necessario associare un skillt a tale indicizzatore.

| Parametro di configurazione | Descrizione |
|--------------------|-------------|
| imageAction   | Impostare su "none" se non deve essere eseguita alcuna operazione quando vengono rilevate immagini incorporate o file di immagine. <br/>Impostare su "generateNormalizedImages" per generare una matrice di immagini durante l'individuazione dei documenti.<br/>Impostare su "generateNormalizedImagePerPage" per generare una matrice di immagini normalizzate dove, per i file PDF nell'origine dati, viene eseguito il rendering di ogni pagina in un'unica immagine di output.  Per i tipi di file diversi da PDF, la funzionalità è la stessa di "generateNormalizedImages".<br/>Per qualsiasi opzione diversa da "none", le immagini verranno esposte nel campo *normalized_images*. <br/>Il valore predefinito è "none". Questa configurazione è pertinente solo alle origini dati BLOB, quando "dataToExtract" è impostato su "contentAndMetadata". <br/>Verrà estratto un massimo di 1000 immagini da un documento specifico. Se sono presenti più di 1000 immagini in un documento, il primo 1000 verrà estratto e verrà generato un avviso. |
|  normalizedImageMaxWidth | La larghezza massima (in pixel) per le immagini normalizzate generate. Il valore predefinito è 2000. Il valore massimo consentito è 10000. | 
|  normalizedImageMaxHeight | L'altezza massima (in pixel) per le immagini normalizzate generate. Il valore predefinito è 2000. Il valore massimo consentito è 10000.|

> [!NOTE]
> Se si imposta la proprietà *imageAction* su un valore diverso da "None", non sarà possibile impostare la proprietà *parsingMode* su un valore diverso da "default".  Nella configurazione dell'indicizzatore è possibile impostare solo una di queste due proprietà su un valore non predefinito.

Impostare il parametro **parsingMode** su `json`, per indicizzare ogni BLOB come un singolo documento, oppure su `jsonArray`, se i BLOB contengono matrici JSON ed è necessario trattare ogni elemento di una matrice come un documento separato.

Il valore predefinito di 2000 pixel per i valori massimi di altezza e larghezza delle immagini normalizzate è basato sulle dimensioni massime supportate dalla [competenza OCR](cognitive-search-skill-ocr.md) e dalla [competenza di analisi delle immagini](cognitive-search-skill-image-analysis.md). La [skill OCR](cognitive-search-skill-ocr.md) supporta una larghezza e un'altezza massime di 4200 per le lingue diverse dall'inglese e 10000 per la lingua inglese.  Se si aumentano i limiti massimi, l'elaborazione potrebbe avere esito negativo sulle immagini più grandi a seconda della definizione del suo competenze e della lingua dei documenti. 

Specificare imageAction nella [definizione dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer) come indicato di seguito:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

Se *imageAction* è impostato su un valore diverso da "none", il nuovo campo *normalized_images* conterrà una matrice di immagini. Ogni immagine è un tipo complesso che contiene i membri seguenti:

| Membro immagine       | Descrizione                             |
|--------------------|-----------------------------------------|
| data               | Stringa con codifica Base64 dell'immagine normalizzata in formato JPEG.   |
| width              | Larghezza dell'immagine normalizzata in pixel. |
| height             | Altezza dell'immagine normalizzata in pixel. |
| originalWidth      | Larghezza originale dell'immagine prima della normalizzazione. |
| originalHeight      | Altezza originale dell'immagine prima della normalizzazione. |
| rotationFromOriginal |  Rotazione in senso antiorario, espressa in gradi, effettuata per creare l'immagine normalizzata. Un valore compreso tra 0 e 360 gradi. Questo passaggio legge i metadati dall'immagine generata da una fotocamera o da uno scanner. In genere, è un multiplo di 90 gradi. |
| contentOffset | L'offset di carattere all'interno del campo di contenuto da cui è stata estratta l'immagine. Questo campo è applicabile solo ai file con immagini incorporate. |
| pageNumber | Se l'immagine è stata estratta o sottoposta a rendering da un file PDF, questo campo contiene il numero di pagina nel file PDF da cui è stato estratto o sottoposto a rendering, a partire da 1.  Se l'immagine non è stata da un file PDF, questo campo sarà 0.  |

 Valore di esempio di *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Competenze relative alle immagini

Le immagini vengono accettate come input da due competenze cognitive predefinite: [OCR](cognitive-search-skill-ocr.md) e [Analisi delle immagini](cognitive-search-skill-image-analysis.md). 

Attualmente, queste competenze possono essere usate solo con immagini generate dalla procedura di individuazione dei documenti. L'unico input supportato, quindi, è `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Competenza di analisi delle immagini

La [skill Analysis image](cognitive-search-skill-image-analysis.md) estrae un set completo di funzionalità visive basate sul contenuto di un'immagine. È possibile, ad esempio, generare una didascalia da un'immagine, generare tag o identificare personaggi famosi e luoghi di interesse.

### <a name="ocr-skill"></a>Competenza OCR

La [competenza OCR](cognitive-search-skill-ocr.md) estrae il testo da file di immagine in formato JPG, PNG e bitmap. È possibile estrarre sia il testo sia informazioni di layout. Le informazioni di layout presentano rettangoli di selezione per ogni stringa identificata.

## <a name="embedded-image-scenario"></a>Scenario di immagine incorporata

Uno scenario comune prevede la creazione di una singola stringa contenente tutti i contenuti del file, sia il testo che il testo ricavato dalle immagini, attenendosi alla procedura seguente:  

1. [Estrarre normalized_images](#get-normalized-images)
1. Eseguire la competenza OCR usando `"/document/normalized_images"` come input
1. Unire la rappresentazione testuale di queste immagini con il testo non elaborato estratto dal file. È possibile usare la competenza di [unione del testo](cognitive-search-skill-textmerger.md) per consolidare i due blocchi di testo in un'unica stringa di grandi dimensioni.

L'insieme di competenze di esempio illustrato di seguito crea un campo *merged_text* contenente il contenuto testuale del documento. Contiene anche il testo OCR di ognuna delle immagini incorporate. 

#### <a name="request-body-syntax"></a>Sintassi del corpo della richiesta
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```

È possibile ora eseguire il mapping del campo merged_text come campo ricercabile nella definizione dell'indicizzatore. In questo modo sarà possibile eseguire ricerche su tutti i contenuti dei file, incluso il testo delle immagini.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualizzare i rettangoli di selezione del testo estratto

Un altro scenario comune è la visualizzazione delle informazioni di layout nei risultati della ricerca. All'interno dei risultati della ricerca, ad esempio, è possibile che si voglia evidenziare la posizione in cui è stata trovata una porzione di testo in un'immagine.

Poiché il passaggio OCR viene eseguito sulle immagini normalizzate, le coordinate di layout si trovano nello spazio delle immagini normalizzate. Quando si visualizza l'immagine normalizzata, la presenza di coordinate in genere non costituisce un problema, ma in alcune situazioni si preferisce visualizzare l'immagine originale. In questo caso, è possibile convertire ogni punto delle coordinate presenti nel layout nel sistema di coordinate dell'immagine originale. 

Se si preferisce trasformare le coordinate normalizzate nello spazio delle coordinate originale, è possibile usare l'algoritmo seguente:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>Vedere anche
+ [Crea indicizzatore (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Competenza di analisi delle immagini](cognitive-search-skill-image-analysis.md)
+ [Competenza OCR](cognitive-search-skill-ocr.md)
+ [Text merge skill](cognitive-search-skill-textmerger.md) (Competenza di unione del testo)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
