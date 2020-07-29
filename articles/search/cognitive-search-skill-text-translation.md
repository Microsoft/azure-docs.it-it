---
title: Competenze cognitive per la traduzione del testo
titleSuffix: Azure Cognitive Search
description: Valuta il testo e, per ogni record, restituisce il testo convertito nel linguaggio di destinazione specificato in una pipeline di arricchimento AI in Azure ricerca cognitiva.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b35af58141dc46e0cc36efe009023c1bf52850e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080047"
---
#   <a name="text-translation-cognitive-skill"></a>Competenze cognitive per la traduzione del testo

L'abilità di **traduzione del testo** valuta il testo e, per ogni record, restituisce il testo convertito nel linguaggio di destinazione specificato. Questa competenza usa il [API traduzione testuale v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) disponibile in Servizi cognitivi.

Questa funzionalità è utile se si prevede che i documenti non siano tutti in una lingua, nel qual caso è possibile normalizzare il testo in una sola lingua prima dell'indicizzazione per la ricerca attraverso la traduzione.  È utile anche per i casi d'uso della localizzazione, in cui è possibile che si desideri avere copie dello stesso testo disponibili in più lingue.

Il [API traduzione testuale v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) è un servizio cognitivo non regionale, ovvero non è garantito che i dati si trovino nella stessa area della ricerca cognitiva di Azure o della risorsa Servizi cognitivi collegati.

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di cracking dei documenti in Ricerca cognitiva di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione di immagini sono descritti nella [pagina dei prezzi di Ricerca cognitiva di Azure](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>Limiti dei dati
Le dimensioni massime di un record devono essere di 50.000 caratteri in base alla misurazione di [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se è necessario suddividere i dati prima di inviarli all'abilità di traduzione del testo, provare a usare la [capacità di suddivisione del testo](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Input                | Descrizione |
|---------------------|-------------|
| defaultToLanguageCode | Necessaria Codice della lingua in cui tradurre i documenti per i documenti che non specificano in modo esplicito la lingua a. <br/> Vedere l'[elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| defaultFromLanguageCode | Opzionale Codice della lingua da cui tradurre i documenti per i documenti che non specificano in modo esplicito il linguaggio from.  Se defaultFromLanguageCode non è specificato, verrà utilizzato il rilevamento automatico della lingua fornito dal API Traduzione testuale per determinare la lingua da. <br/> Vedere l'[elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| suggestedFrom | Opzionale Il codice della lingua per tradurre i documenti da quando non vengono specificati né l'input fromLanguageCode né il parametro defaultFromLanguageCode e il rilevamento automatico della lingua ha esito negativo.  Se la lingua suggestedFrom non è specificata, l'inglese (en) verrà usato come lingua suggestedFrom. <br/> Vedere l'[elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Input competenze

| Nome input     | Descrizione |
|--------------------|-------------|
| text | Testo da tradurre.|
| toLanguageCode    | Stringa che indica la lingua in cui deve essere convertito il testo. Se questo input non è specificato, verrà usato defaultToLanguageCode per tradurre il testo. <br/>Vedere l'[elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | Stringa che indica la lingua corrente del testo. Se questo parametro non viene specificato, il defaultFromLanguageCode (o il rilevamento automatico della lingua se il defaultFromLanguageCode non viene fornito) verrà usato per tradurre il testo. <br/>Vedere l'[elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Output competenze

| Nome output    | Descrizione |
|--------------------|-------------|
| translatedText | Risultato stringa della traduzione del testo da translatedFromLanguageCode a translatedToLanguageCode.|
| translatedToLanguageCode  | Stringa che indica il codice della lingua in cui è stato convertito il testo. Utile se si esegue la conversione in più lingue e si desidera essere in grado di tenere traccia del testo che è il linguaggio.|
| translatedFromLanguageCode    | Stringa che indica il codice della lingua da cui è stato convertito il testo. Utile se è stata scelta l'opzione di rilevamento automatico della lingua perché questo output fornirà il risultato del rilevamento.|

##  <a name="sample-definition"></a>Definizione di esempio

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
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
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Errori e avvisi
Se si fornisce un codice lingua non supportato per la lingua da o a, viene generato un errore e il testo non viene convertito.
Se il testo è vuoto, verrà generato un avviso.
Se il testo è più grande di 50.000 caratteri, verranno tradotti solo i primi 50.000 caratteri e verrà emesso un avviso.

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
