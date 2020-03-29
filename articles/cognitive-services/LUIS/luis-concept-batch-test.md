---
title: Test batch - LUIS
titleSuffix: Azure Cognitive Services
description: Usare test in batch per lavorare continuamente all'applicazione allo scopo di perfezionarla e di migliorarne la comprensione del linguaggio.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: e9ad7c52af20762633c710b39a64fbebf0cf6213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220050"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Test in batch con 1000 espressioni nel portale di LUIS

I test in batch convalidano la versione con training attiva per misurarne l'accuratezza della stima. Un test batch consente di visualizzare l'accuratezza di ogni finalità ed entità nella versione attiva, visualizzando i risultati con un grafico. Esaminare i risultati del test batch per intraprendere l'azione appropriata per migliorare l'accuratezza, ad esempio aggiungendo altre espressioni di esempio a una finalità se l'app spesso non riesce a identificare la finalità corretta o etichettare le entità all'interno dell'espressione.

## <a name="group-data-for-batch-test"></a>Raggruppare i dati per il test in batch

È importante che le espressioni usate per i test in batch siano nuove per Language Understanding. Se si dispone di un set di dati di espressioni, dividere le espressioni in tre set: espressioni di esempio aggiunte a una finalità, espressioni ricevute dall'endpoint pubblicato e espressioni usate per eseguire il test in batch di LUIS dopo il training. 

## <a name="a-data-set-of-utterances"></a>Un set di dati di espressioni

Inviare un file batch di espressioni, noto come set di *dati,* per il test in batch. Il set di dati è un file in formato JSON contenente un massimo di 1.000 espressioni non **duplicate** con etichetta. Puoi testare fino a 10 set di dati in un'app. Se è necessario testarne altri, eliminare un set di dati e quindi aggiungerne uno nuovo.

|**Regole**|
|--|
|*Nessuna espressione duplicata|
|1000 espressioni o meno|

*I duplicati sono considerati corrispondenze di stringa esatte, non corrispondenze che vengono prima tokenizzate. 

## <a name="entities-allowed-in-batch-tests"></a>Entità consentite nel test in batch

Tutte le entità personalizzate nel modello vengono visualizzate nel filtro del test in batch anche se nei dati del file batch non sono presenti entità corrispondente.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Formato file batch

Il file batch è costituito da espressioni. Ogni espressione deve avere una previsione di finalità e le [entità di Machine Learning](luis-concept-entity-types.md#types-of-entities) che si prevede vengano individuate. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Modello di sintassi di batch per finalità con entità

Usare il modello seguente per avviare il file batch:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Il file batch usare le proprietà **startPos** e **endPos** per notare l'inizio e la fine di un'entità. I valori sono basati su zero e non devono iniziare o terminare in uno spazio. Questo è diverso dai log di query, che usano proprietà startIndex ed endIndex. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Modello di sintassi di batch per finalità senza entità

Usare il modello seguente per avviare il file batch senza entità:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Se non si intende testare le entità, includere la proprietà `entities` e impostare il valore come una matrice vuota `[]`.


## <a name="common-errors-importing-a-batch"></a>Errori comuni nell'importazione di un batch

Di seguito sono riportati gli errori più comuni. 

> * Più di 1.000 espressioni
> * Un oggetto JSON di espressione che non ha una proprietà di entità. La proprietà può essere una matrice vuota.
> * Parole etichettata in più entità
> * Etichetta dell'entità che inizia o termina in uno spazio.

## <a name="batch-test-state"></a>Stato del test in batch

LUIS tiene traccia dello stato dell'ultimo test di ogni set di dati. con le dimensioni (numero di espressioni nel batch), la data dell'ultima esecuzione e l'ultimo risultato (numero di espressioni previste correttamente).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Risultati del test in batch

Il risultato del test in batch è un grafico a dispersione, noto come matrice di errore. Questo grafico è un confronto a quattro delle espressioni nel file batch e della finalità e delle entità previste dal modello corrente. 

I punti dati nelle sezioni **False Positive** (Falso positivo) e **False Negative** (Falso negativo) indicano errori che devono essere esaminati. Se tutti i punti dati si trovano nelle sezioni **True Positive** e **True Negative,** la precisione dell'app è perfetta in questo set di dati.

![Quattro sezioni del grafico](./media/luis-concept-batch-test/chart-sections.png)

Questo grafico consente di trovare le espressioni che Language Understanding prevede in modo errato in base al training attuale. I risultati vengono visualizzati per ogni area del grafico. Selezionare i singoli punti del grafico per esaminare le informazioni sulle espressioni oppure selezionare il nome dell'area per esaminare i risultati delle espressioni in quell'area.

![Test in batch](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Errori nei risultati

Gli errori nel test in batch indicano finalità che non vengono previste come indicato nel file batch. Gli errori sono indicati nelle due sezioni rosse del grafico. 

La sezione dei falsi positivi indica che un'espressione corrispondeva a una finalità o a un'entità quando non avrebbe dovuto. La sezione dei falsi negativi indica che un'espressione non corrispondeva a una finalità o a un'entità quando avrebbe dovuto. 

## <a name="fixing-batch-errors"></a>Correzione di errori batch

In presenza di errori nei test in batch, è possibile aggiungere più espressioni a una finalità e/o etichettare più espressioni con l'entità per consentire a Language Understanding di distinguere tra le finalità. Se sono state aggiunte espressioni che sono state etichettate e si continuano a riscontrare errori di previsione nei test in batch, è possibile aggiungere un [elenco di frasi](luis-concept-feature.md) con un vocabolario settoriale specifico per consentire a Language Understanding di apprendere più rapidamente. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [testare un batch](luis-how-to-batch-test.md)
