---
title: Competenze cognitive per l'estrazione di documenti (anteprima)
titleSuffix: Azure Cognitive Search
description: Estrae il contenuto da un file all'interno della pipeline di arricchimento. Questa competenza è attualmente disponibile in anteprima pubblica.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: chalton
ms.openlocfilehash: f209be383e445e3b0c011e0bfb4266a191a8d931
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080874"
---
# <a name="document-extraction-cognitive-skill"></a>Abilità cognitiva di estrazione documenti

> [!IMPORTANT] 
> Questa competenza è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Attualmente non è disponibile alcun portale o supporto per .NET SDK.

L'abilità di **estrazione dei documenti** estrae il contenuto da un file all'interno della pipeline di arricchimento. In questo modo è possibile sfruttare i vantaggi del passaggio di estrazione del documento che in genere si verifica prima dell'esecuzione delle competenze con i file che possono essere generati da altre competenze.

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti aumentano quando si chiamano le API nei servizi cognitivi e per l'estrazione di immagini come parte della fase di indicizzazione del documento. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione delle immagini sono descritti nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Input            | Valori consentiti | Descrizione |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Impostare su `default` per l'estrazione dei documenti da file che non sono testo puro o JSON. Impostare su `text` per migliorare le prestazioni in file di testo normale. Impostare su `json` per estrarre il contenuto strutturato dai file JSON. Se `parsingMode` non è definito in modo esplicito, verrà impostato su `default` . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Impostare su `contentAndMetadata` per estrarre tutti i metadati e il contenuto testuale da ogni file. Impostare su `allMetadata` per estrarre solo i [metadati specifici del tipo di contenuto](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) , ad esempio i metadati univoci solo per i file con estensione png. Se `dataToExtract` non è definito in modo esplicito, verrà impostato su `contentAndMetadata` . |
| `configuration` | Vedere di seguito. | Dizionario di parametri facoltativi che regolano il modo in cui viene eseguita l'estrazione del documento. Vedere la tabella seguente per le descrizioni delle proprietà di configurazione supportate. |

| Parametro di configurazione   | Valori consentiti | Descrizione |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Impostare su `none` per ignorare le immagini incorporate o i file di immagine nel set di dati. Questo è il valore predefinito. <br/>Per l' [analisi delle immagini con competenze cognitive](cognitive-search-concept-image-scenarios.md), impostare su `generateNormalizedImages` per fare in modo che l'abilità crei una matrice di immagini normalizzate come parte del cracking del documento. Per questa azione è necessario che `parsingMode` sia impostato su `default` e che `dataToExtract` sia impostato su `contentAndMetadata` . Per immagine normalizzata si intende un'elaborazione aggiuntiva che produce un output dell'immagine uniforme, ridimensionato e ruotato per favorire un rendering coerente quando si includono immagini nei risultati della ricerca visiva (ad esempio, fotografie delle stesse dimensioni in un controllo grafico, come illustrato nella [demo su JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Queste informazioni vengono generate per ogni immagine quando si usa questa opzione.  <br/>Se si imposta su `generateNormalizedImagePerPage` , i file PDF verranno trattati in modo diverso, anziché estrarre le immagini incorporate, verrà eseguito il rendering di ogni pagina come immagine e normalizzata di conseguenza.  I tipi di file non PDF verranno trattati come se `generateNormalizedImages` fosse stato impostato.
| `normalizedImageMaxWidth` | Qualsiasi numero intero compreso tra 50-10000 | La larghezza massima (in pixel) per le immagini normalizzate generate. Il valore predefinito è 2000. | 
| `normalizedImageMaxHeight` | Qualsiasi numero intero compreso tra 50-10000 | L'altezza massima (in pixel) per le immagini normalizzate generate. Il valore predefinito è 2000. |

> [!NOTE]
> Il valore predefinito di 2000 pixel per i valori massimi di altezza e larghezza delle immagini normalizzate è basato sulle dimensioni massime supportate dalla [competenza OCR](cognitive-search-skill-ocr.md) e dalla [competenza di analisi delle immagini](cognitive-search-skill-image-analysis.md). La [skill OCR](cognitive-search-skill-ocr.md) supporta una larghezza e un'altezza massime di 4200 per le lingue diverse dall'inglese e 10000 per la lingua inglese.  Se si aumentano i limiti massimi, l'elaborazione potrebbe avere esito negativo sulle immagini più grandi a seconda della definizione del suo competenze e della lingua dei documenti. 
## <a name="skill-inputs"></a>Input competenze

| Nome input     | Descrizione |
|--------------------|-------------|
| `file_data` | File da cui deve essere estratto il contenuto. |

L'input "file_data" deve essere un oggetto definito come segue:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Questo oggetto di riferimento file può essere generato in uno dei tre modi seguenti:

 - Impostazione del `allowSkillsetToReadFileData` parametro nella definizione dell'indicizzatore su "true".  Verrà creato un percorso `/document/file_data` che rappresenta un oggetto che rappresenta i dati del file originale scaricati dall'origine dati BLOB. Questo parametro si applica solo ai dati nell'archivio BLOB.

 - Impostazione del `imageAction` parametro nella definizione dell'indicizzatore su un valore diverso da `none` .  In questo modo viene creata una matrice di immagini che segue la convenzione necessaria per l'input di questa competenza se viene passato singolarmente (ad esempio `/document/normalized_images/*` ).

 - La presenza di un'abilità personalizzata restituisce un oggetto JSON definito esattamente come sopra.  Il `$type` parametro deve essere impostato su exactly `file` e il `data` parametro deve essere costituito dai dati della matrice di byte codificati 64 di base del contenuto del file.

## <a name="skill-outputs"></a>Output competenze

| Nome output    | Descrizione |
|--------------|-------------|
| `content` | Contenuto testuale del documento. |
| `normalized_images`   | Quando `imageAction` è impostato su un valore diverso da `none` , il nuovo campo *normalized_images* conterrà una matrice di immagini. Per informazioni dettagliate sul formato di output di ogni immagine, vedere [la documentazione per l'estrazione delle immagini](cognitive-search-concept-image-scenarios.md) . |

##  <a name="sample-definition"></a>Definizione di esempio

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Input di esempio

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Output di esempio

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Come elaborare ed estrarre informazioni da immagini in scenari di ricerca cognitiva](cognitive-search-concept-image-scenarios.md)