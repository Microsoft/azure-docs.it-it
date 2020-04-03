---
title: Limiti - LUISLimits - LUIS
description: Questo articolo illustra i limiti di LUIS (Language Understanding) dei Servizi cognitivi di Azure. LUIS dispone di diverse aree di limiti. Il limite modello controlla finalità, entità e funzionalità in LUIS. I limiti di quota si basano sul tipo di chiave. La combinazione di tasti controlla il sito Web di LUIS.
ms.topic: reference
ms.date: 04/02/2020
ms.openlocfilehash: 4aa69cb0fd36fe5bf4ea2928022aea602b8830d6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618857"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Limiti per il modello LUIS e le chiavi
LUIS dispone di diverse aree di limiti. La prima è il [limite modello](#model-boundaries), che controlla finalità, entità e funzionalità in LUIS. La seconda area è [limiti di quota](#key-limits) basata sul tipo di chiave. Una terza area di limiti è rappresentata dalla [combinazione di tasti](#keyboard-controls) per il controllo del sito Web LUIS. Una quarta area è data dal [mapping dell'area globale](luis-reference-regions.md) tra il sito Web di creazione LUIS e le API dell'[endpoint LUIS](luis-glossary.md#endpoint).


## <a name="model-boundaries"></a>Limiti di modello

Se l'app supera i limiti del modello LUIS, è consigliabile usare un'app [dispatch LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) o un [contenitore LUIS](luis-container-howto.md).

|Area|Limite|
|--|:--|
| [Nome dell'app][luis-get-started-create-app] | *Numero max predefinito di caratteri |
| APPLICAZIONI| 500 applicazioni per risorsa di creazione di Azure50 applications per Azure authoring resource |
| [Test in batch][batch-testing]| 10 set di dati, 1000 espressioni per ogni set di dati|
| Elenco esplicito | 50 per applicazione|
| Entità esterne | senza limiti |
| [Finalità][intents]|500 per applicazione: 499 finalità personalizzate e la finalità _None_ richiesta.<br>[L'applicazione basata](https://aka.ms/dispatch-tool) sulla spedizione ha le corrispondenti 500 fonti di spedizione.|
| [Elencare le entità](./luis-concept-entity-types.md) | Padre: 50, figlio: 20.000 elementi. Il nome canonico è il *numero max predefinito di caratteri. I sinonimi non hanno restrizioni di lunghezza. |
| [Entità apprese automaticamente - ruoli](./luis-concept-entity-types.md):<br> Composito<br>Semplice<br>ruolo dell'entità|Un limite di 100 entità padre o 330 entità, a seconda di quale limite l'utente raggiunge per primo. Un ruolo viene conteggiato come entità ai fini di questo limite. Un esempio è un composito con un'entità semplice, che ha 2 ruoli, è: 1 composito , 1 semplice , 2 ruoli e 4 delle 330 entità.<br>I sottocomponenti possono essere nidificati fino a 5 livelli.|
|Modello come feature| Numero massimo di modelli che possono essere utilizzati come descrittore (feature) per un modello specifico come 10 modelli. Il numero massimo di elenchi di frasi utilizzate come descrittore (caratteristica) per un modello specifico siano 10 elenchi di frasi.|
| [Anteprima - Entità elenco dinamico](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 elenchi di richiesta dell'endpoint di previsione di 1k per query|
| [Modelli](luis-concept-patterns.md)|500 criteri per ogni applicazione.<br>Il criterio può contenere al massimo 400 caratteri.<br>3 entità pattern.any per criterio<br>Il criterio può contenere al massimo 2 testi facoltativi annidati|
| [Pattern.any](./luis-concept-entity-types.md)|100 per applicazione, 3 entità pattern.any per criterio |
| [Elenco di frasi][phrase-list]|500 elenchi di frasi. 10 elenchi di frasi globali a causa del modello come limite di entità geografiche. L'elenco di frasi non intercambiabili ha un massimo di 5.000 frasi. L'elenco di frasi intercambiabili ha un massimo di 50.000 frasi. Numero massimo di frasi totali per applicazione di 500.000 frasi.|
| [Entità predefinite](./luis-prebuilt-entities.md) | nessun limite|
| [Entità di espressione regolare](./luis-concept-entity-types.md)|20 entità<br>È consentito un numero massimo di 500 caratteri. per ogni criterio di entità di espressione regolare|
| [Ruoli](luis-concept-roles.md)|300 ruoli per ogni applicazione. 10 per entità|
| [Espressione][utterances] | 500 caratteri|
| [Espressioni][utterances] | 15.000 per applicazione: non esiste alcun limite al numero di espressioni per finalità|
| [Versioni](luis-concept-version.md)| 100 versioni per applicazione |
| [Nome della versione][luis-how-to-manage-versions] | 10 caratteri limitati a caratteri alfanumerici e punto (.) |

*Il numero max predefinito di caratteri è 50.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Univocità del nome

Utilizzare le seguenti regole di univocità dei nomi.

Gli elementi seguenti devono essere univoci all'interno di un'app LUIS:The following must be unique within a LUIS app:

* nome della versione
* intent
* Entità
* roles

I seguenti elementi devono essere univoci nell'ambito applicato:

* elenco di frasi

## <a name="object-naming"></a>Denominazione degli oggetti

Non utilizzare i seguenti caratteri nei nomi seguenti.

|Oggetto|Escludi caratteri|
|--|--|
|Nomi di finalità, entità e ruoli|`:`<br>`$` <br> `&`|
|Nome della versione|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Utilizzo e limiti delle risorse

Language Understand include risorse separate, un tipo per la creazione e un tipo per l'esecuzione di query sull'endpoint di stima. Per altre informazioni sulle differenze tra i tipi di chiave, vedere [Chiavi di creazione e di endpoint per query di stima in LUIS](luis-concept-keys.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Limiti delle risorse di creazione

Usare il `LUIS.Authoring`tipo , , quando si filtrano le risorse nel portale di Azure.Use the _kind_, , when filtering resources in the Azure portal. LUIS limita 500 applicazioni per ogni risorsa di creazione di Azure.LUIS limits 500 applications per Azure authoring resource.

|Risorsa di creazione|Creazione di TPS|
|--|--|
|Starter|1 milione/mese, 5/secondo|
|F0 - Livello gratuito |1 milione/mese, 5/secondo|

* TPS - Transazioni al secondo

[Ulteriori informazioni sui prezzi.][pricing]

### <a name="query-prediction-resource-limits"></a>Limiti delle risorse di stima delle queryQuery prediction resource limits

Usare il `LUIS`tipo , , quando si filtrano le risorse nel portale di Azure.Use the _kind_, , when filtering resources in the Azure portal. La risorsa endpoint di stima delle query LUIS, usata nel runtime, è valida solo per le query endpoint.

|Risorsa Previsione query|Query TPS|
|--|--|
|F0 - Livello gratuito |10.000/mese, 5/secondo|
|S0 - Livello standard|50/secondo|

### <a name="sentiment-analysis"></a>Analisi del sentiment

[L'integrazione dell'analisi del sentiment,](luis-how-to-publish-app.md#enable-sentiment-analysis)che fornisce informazioni sul sentiment, viene fornita senza richiedere un'altra risorsa di Azure.Sentiment analysis integration , which provides sentiment information, is provided without requiring another Azure resource.

### <a name="speech-integration"></a>Integrazione riconoscimento vocale

[L'integrazione vocale](../speech-service/how-to-recognize-intents-from-speech-csharp.md) fornisce 1 000 richieste di endpoint per costo unitario.

[Ulteriori informazioni sui prezzi.][pricing]

## <a name="keyboard-controls"></a>Controlli tastiera

|Input tastiera | Descrizione |
|--|--|
|Controllo + E|passa dai token alle entità nell'elenco delle espressioni|

## <a name="website-sign-in-time-period"></a>Periodo di tempo per l'accesso al sito Web

Il periodo di tempo di accesso è di **60 minuti**. Trascorso questo tempo, si verificherà il seguente errore. Sarà necessario eseguire nuovamente l'accesso.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
