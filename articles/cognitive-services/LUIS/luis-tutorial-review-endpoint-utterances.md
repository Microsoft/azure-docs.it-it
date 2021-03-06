---
title: 'Esercitazione: Revisione delle espressioni di endpoint - LUIS'
description: Questa esercitazione illustra come migliorare le previsioni per le app verificando o correggendo le espressioni ricevute tramite l'endpoint HTTPS LUIS di cui LUIS non è sicuro. Potrebbe essere necessario verificare l'entità o la finalità di alcune espressioni.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/02/2020
ms.openlocfilehash: b8f8fa2cd3c9c22187bb95c55d9de2abb2e8caec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324638"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Esercitazione: Correggere le stime dubbie con la revisione delle espressioni di endpoint
Questa esercitazione illustra come migliorare le stime delle app tramite la verifica o la correzione delle espressioni ricevute tramite l'endpoint HTTPS LUIS di cui LUIS non è sicuro. È consigliabile includere l'analisi delle espressioni dell'endpoint come fase regolare della manutenzione pianificata di LUIS.

Questo processo di revisione consente a LUIS di acquisire informazioni sul dominio dell'app. LUIS seleziona le espressioni visualizzate nell'elenco per la revisione. L'elenco ha le caratteristiche seguenti:

* È specifico per l'app.
* È progettato per migliorare la precisione delle previsioni dell'app.
* Deve essere esaminato periodicamente.

La revisione delle espressioni endpoint consente di verificare o correggere la finalità prevista dell'espressione.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importare l'app di esempio
> * Esaminare le espressioni endpoint
> * Eseguire il training e pubblicare l'app
> * Eseguire query sull'endpoint dell'app per ottenere una risposta JSON di Language Understanding

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="download-json-file-for-app"></a>Scaricare il file JSON per l'app

Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

## <a name="import-json-file-for-app"></a>Importare il file JSON per l'app


1. Nella pagina **My apps** (App personali) del [portale LUIS](https://www.luis.ai) selezionare **+ New app for conversation** (Nuova app di conversazione) e quindi **Import as JSON** (Importa come JSON). Trovare il file JSON salvato nel passaggio precedente. Non è necessario modificare il nome dell'app. Selezionare **Operazione completata**

1. Selezionare **Build** (Compila) e quindi **Intents** (Finalità) per visualizzare le finalità, ovvero i blocchi principali di un'app LUIS.

    :::image type="content" source="media/luis-tutorial-review-endpoint-utterances/initial-intents-in-app.png" alt-text="Passare dalla pagina Versions (Versioni) alla pagina Intents (Finalità).":::

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Eseguire il training dell'app per applicare le modifiche alle entità all'app

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Pubblicare l'app per eseguire l'accesso dall'endpoint HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Aggiungere espressioni all'endpoint

In questa app sono presenti finalità ed entità, ma non è disponibile alcun utilizzo dell'endpoint. L'utilizzo dell'endpoint è necessario per migliorare l'app con la revisione delle espressioni dell'endpoint.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Passare alla fine dell'URL nella barra degli indirizzi e sostituire _YOUR_QUERY_HERE_ con le espressioni della tabella seguente. Inviare ogni espressione e recuperare il risultato. Sostituire quindi l'espressione alla fine con quella successiva.

    |Espressione dell'endpoint|Finalità allineata|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    È disponibile un singolo pool di espressioni da esaminare, indipendentemente dalla versione che si sta modificando attivamente o dalla versione dell'app pubblicata nell'endpoint.

## <a name="review-endpoint-utterances"></a>Esaminare le espressioni endpoint

Esaminare le espressioni dell'endpoint per assicurarsi che la finalità sia allineata correttamente. Anche se è presente un singolo pool di espressioni da rivedere tra tutte le versioni, il processo di allineamento corretto della finalità aggiunge l'espressione di esempio solo al _modello attivo_ corrente.

1. Nella sezione **Build** (Compila) del portale selezionare **Controlla le espressioni di endpoint** nel riquadro di spostamento sinistro. All'elenco viene applicato un filtro relativo alla finalità **ApplyForJob**.

    :::image type="content" source="./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png" alt-text="Passare dalla pagina Versions (Versioni) alla pagina Intents (Finalità).":::

    L'espressione `I'm looking for a job with Natural Language Processing` non ha la finalità corretta, _GetJobInformation_. È stata stimata in modo errato come _ApplyForJob_ a causa della somiglianza tra i nomi e i verbi dei processi nelle due finalità.

1.  Per allineare questa espressione, nella riga corrispondente selezionare la **Finalità allineata** corretta `GetJobInformation`. Aggiungere l'espressione cambiata all'app selezionando il segno di spunta.

    Esaminare le espressioni rimanenti in questa finalità, correggendo la finalità allineata in base alle esigenze. Usare la tabella di espressioni iniziali di questa esercitazione per visualizzare la finalità allineata.

    Nell'elenco **Controlla le espressioni di endpoint** non dovrebbero essere più presenti le espressioni corrette. Se vengono visualizzate altre espressioni, continuare a esaminare l'elenco, correggendo le finalità allineate, fino a svuotarlo.

    La correzione dell'etichettatura di entità viene eseguita dopo l'allineamento della finalità nella relativa pagina di dettagli.

1. Eseguire il training dell'app e ripubblicarla.

## <a name="get-intent-prediction-from-endpoint"></a>Ottenere una stima della finalità dall'endpoint

Per verificare che le espressioni di esempio correttamente allineate abbiano migliorato la previsione dell'app, provare a usare un'espressione simile a quella corretta.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Passare alla fine dell'URL nella barra degli indirizzi e sostituire _YOUR_QUERY_HERE_ con `Are there any natural language processing jobs in my department right now?`.

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.901367366
                },
                "ApplyForJob": {
                    "score": 0.0307973567
                },
                "EmployeeFeedback": {
                    "score": 0.0296942145
                },
                "MoveEmployee": {
                    "score": 0.00739785144
                },
                "FindForm": {
                    "score": 0.00449316856
                },
                "Utilities.Stop": {
                    "score": 0.00417657848
                },
                "Utilities.StartOver": {
                    "score": 0.00407167152
                },
                "Utilities.Help": {
                    "score": 0.003662492
                },
                "None": {
                    "score": 0.00335733569
                },
                "Utilities.Cancel": {
                    "score": 0.002225436
                },
                "Utilities.Confirm": {
                    "score": 0.00107437756
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2020-07-02 21:45:50"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
   ```

   Ora che le espressioni non sicure sono allineate correttamente, la finalità corretta è stata prevista con un **punteggio elevato**.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Possibilità di sostituire la revisione tramite l'aggiunta di altre espressioni
È possibile che ci si domandi se non sia possibile aggiungere altre espressioni di esempio e quale sia la finalità della revisione delle espressioni endpoint. In un'app LUIS concreta le espressioni endpoint vengono generate da utenti con scelte di parole e strutture di frase non ancora usate. Se fossero state usate le stesse scelte di parole e le stesse strutture, la previsione originale avrebbe avuto una percentuale maggiore.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Ragioni per la presenza della finalità principale nell'elenco di espressioni
Alcune espressioni endpoint presenteranno un punteggio di stima elevato nell'elenco di revisione. È comunque necessario esaminare e verificare tali espressioni. Sono incluse nell'elenco perché la finalità con punteggio più elevato successiva ha un punteggio troppo vicino a quello della finalità principale. La differenza tra le due finalità principali deve essere del 15%.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state esaminate espressioni inviate all'endpoint di cui LUIS non era sicuro. Una volta che queste espressioni sono state verificate e spostate nella finalità corretta come espressioni di esempio, LUIS migliorerà l'accuratezza della stima.

> [!div class="nextstepaction"]
> [Informazioni sull'uso dei criteri](luis-tutorial-pattern.md)
