---
title: Competenza cognitiva Shaper
titleSuffix: Azure Cognitive Search
description: Estrarre i metadati e le informazioni strutturate dai dati non strutturati e informarli come tipi complessi in una pipeline di arricchimento AI in Azure ricerca cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 81eb0e60befc544a6c3bee8f04e901b6a5e472bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85560826"
---
# <a name="shaper-cognitive-skill"></a>Competenza cognitiva Shaper

L'abilità **shaper** consolida diversi input in un [tipo complesso](search-howto-complex-data-types.md) a cui è possibile fare riferimento in un secondo momento nella pipeline di arricchimento. L'abilità **shaper** consente di creare essenzialmente una struttura, definire il nome dei membri di tale struttura e assegnare valori a ogni membro. Esempi di campi consolidati utili negli scenari di ricerca includono la combinazione di un nome e un cognome in una singola struttura, città e stato in una singola struttura oppure nome e data di nascita in una singola struttura per stabilire un'identità univoca.

Inoltre, l'abilità **shaper** illustrata nello [scenario 3](#nested-complex-types) aggiunge una proprietà facoltativa *sourceContext* all'input. Le proprietà *source* e *sourceContext* si escludono a vicenda. Se l'input è nel contesto della competenza, usare semplicemente *source*. Se l'input si trova in un contesto *diverso* rispetto al contesto skill, usare *sourceContext*. Per *sourceContext* è necessario definire un input annidato con l'elemento specifico da risolvere come origine. 

Il nome dell'output è sempre "output". Internamente, la pipeline può eseguire il mapping di un nome diverso, ad esempio "analyzedText", come illustrato negli esempi riportati di seguito, ma la capacità di **shaper** restituisce "output" nella risposta. Questo potrebbe essere importante se si esegue il debug di documenti approfonditi e si nota la discrepanza nella denominazione oppure se si compila una competenza personalizzata e si esegue la struttura della risposta manualmente.

> [!NOTE]
> L'abilità dell' **shaper** non è associata a un'API servizi cognitivi e non viene addebitata l'utilizzo. È tuttavia necessario [collegare una risorsa di Servizi cognitivi](cognitive-search-attach-cognitive-services.md) per eseguire l'override dell'opzione di risorsa **Gratuito**, che consente solo un numero ridotto di arricchimenti al giorno.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scenario 1: tipi complessi

Si consideri uno scenario in cui si desidera creare una struttura denominata *analyzedText* dotata di due membri: rispettivamente *testo* e *valutazione*. In un indice un campo ricercabile multiparte è denominato *tipo complesso* e viene spesso creato quando i dati di origine hanno una struttura complessa corrispondente a cui viene eseguito il mapping.

Tuttavia, un altro approccio per la creazione di tipi complessi è costituito dall'abilità **shaper** . Grazie all'inclusione di questa competenza in un insieme di competenze, le operazioni in memoria durante l'elaborazione delle competenze possono restituire forme di dati con strutture annidate, che possono quindi essere mappate a un tipo complesso nell'indice. 

Nell'esempio di definizione di competenze seguente vengono forniti i nomi dei membri come input. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Indice di esempio

Un indicizzatore viene richiamato da un indicizzatore e un indicizzatore richiede un indice. Una rappresentazione di campo complessa nell'indice potrebbe essere simile all'esempio seguente. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Input competenze

Un documento JSON in arrivo che fornisce un input utilizzabile per questa skill di **shaper** può essere:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Output delle competenze

L'abilità **shaper** genera un nuovo elemento denominato *analyzedText* con gli elementi combinati di *testo* e *sentimenti*. Questo output è conforme allo schema dell'indice. Che verrà importato e indicizzato in un indice ricerca cognitiva di Azure.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Scenario 2: consolidamento dell'input

In un altro esempio, si supponga che in diverse fasi di elaborazione della pipeline siano stati estratti il titolo di un libro e i titoli dei capitoli in diverse pagine del libro. È ora possibile creare una singola struttura composta da questi input diversi.

La definizione delle competenze di **shaper** per questo scenario potrebbe essere simile all'esempio seguente:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Output delle competenze
In questo caso, lo **shaper** rende Flat tutti i titoli dei capitoli per creare una singola matrice. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scenario 3: consolidamento dell'input da contesti annidati

Si supponga di avere il titolo, i capitoli e il contenuto di un libro e di avere eseguito il riconoscimento delle entità e le frasi chiave sul contenuto e ora è necessario aggregare i risultati delle diverse competenze in un'unica forma con il nome del capitolo, le entità e le frasi chiave.

La definizione delle competenze di **shaper** per questo scenario potrebbe essere simile all'esempio seguente:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Output delle competenze
In questo caso, lo **shaper** crea un tipo complesso. Questa struttura esiste in memoria. Se si desidera salvare il file in un [Archivio informazioni](knowledge-store-concept-intro.md), è necessario creare una proiezione nel proprio Skills che definisce le caratteristiche di archiviazione.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Come usare i tipi complessi](search-howto-complex-data-types.md)
+ [Knowledge store](knowledge-store-concept-intro.md)
+ [Creare un archivio conoscenze in REST](knowledge-store-create-rest.md)