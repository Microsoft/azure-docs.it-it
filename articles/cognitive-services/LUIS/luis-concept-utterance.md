---
title: Espressioni di esempio valide-LUIS
description: Le espressioni sono gli input dell'utente che l'app deve interpretare. Raccogliere le frasi che si ritiene verranno immesse dagli utenti. Includere espressioni con lo stesso significato ma con una costruzione diversa in termini di lunghezza e posizione delle parole.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 4c3b5674fe039a89eb97b915d13b5d472a6a0568
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316393"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Comprendere quali sono le espressioni ottimali per l'app LUIS

Le **espressioni** sono gli input dell'utente che l'app ha bisogno di interpretare. Per insegnare a LUIS a estrarre le finalità e le entità, è importante acquisire una varietà di espressioni di esempio diverse per ogni finalità. L'apprendimento attivo, o il processo di continuare a eseguire il training su nuovi enunciati, è essenziale per l'intelligence di Machine Learning fornita da LUIS.

Raccogliere le espressioni che si ritiene verranno immesse dagli utenti. Includere espressioni che hanno lo stesso significato ma vengono costruite in modi diversi:

* Lunghezza dell'espressione - breve, media e lunga per l'applicazione client
* Lunghezza della parola e della frase
* Posizione della parola - entità all'inizio, nel mezzo e alla fine dell'espressione
* Grammatica
* Pluralizzazione
* Stemming
* Scelta di sostantivo e verbo
* [Punteggiatura](luis-reference-application-settings.md#punctuation-normalization) : una buona varietà con la grammatica corretta, errata e nessuna

## <a name="how-to-choose-varied-utterances"></a>Come scegliere espressioni varie

Di seguito sono descritti alcuni principi da tenere a mente quando si inizia [aggiungendo espressioni di esempio](luis-how-to-add-example-utterances.md) al modello LUIS.

### <a name="utterances-arent-always-well-formed"></a>Le espressioni non sono sempre ben formate

Un'espressione potrebbe essere una frase come "Prenotami un biglietto per Parigi" oppure un frammento di frase, come "Prenotazione" o "Volo Parigi".  Spesso gli utenti fanno errori di ortografia. Quando si pianifica l'app, valutare se eseguire il [Controllo ortografico Bing](luis-tutorial-bing-spellcheck.md) o meno sull'input dell'utente prima di passarlo a LUIS.

Se non si esegue il controllo ortografico sulle espressioni dell'utente, è necessario formare LUIS sulle espressioni che includono errori di digitazione e di ortografia.

### <a name="use-the-representative-language-of-the-user"></a>Usare il linguaggio rappresentativo dell'utente

Quando si scelgono le espressioni, tenere presente che quello che si ritiene un termine o una frase comune potrebbe non essere corretto per l'utente tipico dell'applicazione client. L'utente potrebbe non avere esperienza nel dominio. Fare attenzione quando si usano termini o frasi che l'utente pronuncerebbe solo se fosse esperto.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Scegliere terminologia e frasi alternative

Si noterà che, anche se si fanno sforzi per creare una modelli di frase alternativi, alcuni termini verranno comunque ripetuti.

Considerare queste espressioni di esempio:

|Espressioni di esempio|
|--|
|come ottenere un computer?|
|Dove ottenere un computer?|
|Voglio ottenere un computer, come posso fare?|
|Quando posso ottenere un computer?|

Il termine principale qui, "computer", non è vario. Usare alternative quali computer desktop, portatile, workstation o anche solo macchina. LUIS può dedurre in modo intelligente i sinonimi dal contesto, ma quando crei espressioni per il training, è sempre meglio variare.

## <a name="example-utterances-in-each-intent"></a>Espressioni di esempio in ogni finalità

Ogni finalità deve avere almeno 15 espressioni di esempio. Se la finalità non prevede espressioni di esempio, non sarà possibile formare LUIS. Se si ha uno scopo con una o pochissime espressioni di esempio, LUIS potrebbe non prevedere accuratamente l'intento.

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Aggiungere piccoli gruppi di 15 espressioni per ogni iterazione di creazione

In ogni iterazione del modello, non aggiungere un'ampia quantità di espressioni. Aggiungere espressioni in gruppi di 15. Ripetere [training](luis-how-to-train.md), [pubblicazione](luis-how-to-publish-app.md) e [test](luis-interactive-test.md).

LUIS compila modelli efficaci con espressioni selezionate con attenzione dall'autore del modello LUIS. L'aggiunta di un numero eccessivo di espressioni non è utile perché introduce confusione.

È consigliabile iniziare con poche espressioni, poi [esaminare le espressioni dell'endpoint](luis-how-to-review-endpoint-utterances.md) per prevederne correttamente la finalità e per estrarre l'entità.

## <a name="utterance-normalization"></a>Normalizzazione dell'espressione

La normalizzazione delle espressioni è il processo di ignorare gli effetti dei tipi di testo, ad esempio la punteggiatura e i segni diacritici, durante il training e la stima.

Per impostazione predefinita, le impostazioni di normalizzazione dell'espressione sono disattivate. Le impostazioni includono:

* Moduli di Word
* Diacritici
* Punteggiatura

Se si attiva un'impostazione di normalizzazione, i punteggi nel riquadro di **test** , i test batch e le query di endpoint cambiano per tutte le espressioni per l'impostazione di normalizzazione.

Quando si clona una versione nel portale LUIS, le impostazioni della versione continuano con la nuova versione clonata.

Impostare le impostazioni della versione tramite il portale LUIS, nella sezione **Gestisci** nella pagina **Impostazioni applicazione** o nell' [API Aggiorna impostazioni versione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings). Per ulteriori informazioni su queste modifiche di normalizzazione, vedere il [riferimento](luis-reference-application-settings.md).

### <a name="word-forms"></a>Moduli di Word

La normalizzazione di **Word form** ignora le differenze nelle parole che si espandono oltre la radice.

<a name="utterance-normalization-for-diacritics-and-punctuation"></a>

### <a name="diacritics"></a>Diacritici

I segni diacritici sono contrassegni o segni all'interno del testo, ad esempio:

```
İ ı Ş Ğ ş ğ ö ü
```

### <a name="punctuation-marks"></a>Segni di punteggiatura
Normalizzare la **punteggiatura** significa che prima che i modelli vengano sottoposti a training e prima che le query dell'endpoint vengano stimate, la punteggiatura verrà rimossa dalle espressioni.

Punteggiatura è un token separato in LUIS. Un enunciato contenente un punto alla fine rispetto a un enunciato che non contiene un punto alla fine è costituito da due espressioni separate e può ottenere due stime diverse.

Se la punteggiatura non viene normalizzata, LUIS non ignora i segni di punteggiatura, per impostazione predefinita, perché alcune applicazioni client possono avere importanza su questi contrassegni. Assicurarsi che le espressioni di esempio usino sia lo stile con punteggiatura sia quello senza punteggiatura, in modo che entrambi gli stili restituiscano gli stessi punteggi relativi.

Verificare che il modello gestisca la punteggiatura nelle espressioni di esempio (con e senza punteggiatura) o nei [modelli](luis-concept-patterns.md) dove risulta più semplice ignorare la punteggiatura con la sintassi speciale: `I am applying for the {Job} position[.]`

Se la punteggiatura non ha un significato specifico nell'applicazione client, considerare la possibilità di [ignorare la punteggiatura](#utterance-normalization) normalizzando la punteggiatura.

### <a name="ignoring-words-and-punctuation"></a>Ignorare parole e punteggiatura

Se si desidera ignorare parole specifiche o segni di punteggiatura nei modelli, utilizzare un [modello](luis-concept-patterns.md#pattern-syntax) con la sintassi _Ignore_ delle parentesi quadre, `[]` .

<a name="training-utterances"></a>

## <a name="training-with-all-utterances"></a>Formazione con tutti gli enunciati

In genere, il training non è determinante, in quanto la previsione delle espressioni potrebbe variare leggermente a seconda delle versioni o delle app.
È possibile rimuovere il training non deterministico aggiornando l'[API delle impostazioni della versione ](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) con la coppia nome/valore `UseAllTrainingData` in modo da usare tutti i dati di training.

## <a name="testing-utterances"></a>Eseguire il test delle espressioni

Gli sviluppatori devono iniziare il test dell'applicazione LUIS con traffico reale inviando espressioni all'URL dell'[endpoint di stima](luis-how-to-azure-subscription.md). Queste espressioni vengono usate per migliorare le prestazioni di finalità ed entità con l'[esame delle espressioni](luis-how-to-review-endpoint-utterances.md). I test inviati attraverso il riquadro di test del sito Web LUIS non vengono mandati attraverso l'endpoint e pertanto non contribuiscono all'apprendimento attivo.

## <a name="review-utterances"></a>Esaminare le espressioni

Dopo che il modello è stato formato e pubblicato e dopo aver ricevuto le query dell'[endpoint](luis-glossary.md#endpoint), [esaminare le espressioni](luis-how-to-review-endpoint-utterances.md) inviate da LUIS. LUIS seleziona le espressioni dell'endpoint con punteggi bassi in termini di finalità o entità.

## <a name="best-practices"></a>Procedure consigliate

Rivedere le [procedure consigliate](luis-concept-best-practices.md) e applicarle come parte del normale ciclo di creazione e modifica.

## <a name="label-for-word-meaning"></a>Assegnare etichette per il significato delle parole

Se la scelta o la disposizione della parola è la stessa ma il significato è diverso, non etichettarla con le entità.

Nelle espressioni seguenti, la parola `fair` è un omografo. L'ortografia è la stessa ma il significato è diverso:

|Espressione|
|--|
|Quali sono le fiere in programma nell'area di Seattle la prossima estate?|
|L'attuale classificazione per la recensione di Seattle è valida?|

Se si desidera che un'entità di evento trovi tutti i dati dell'evento, etichettare la parola `fair` nella prima espressione, ma non nella seconda.


## <a name="next-steps"></a>Passaggi successivi
Consultare [Add example utterances](luis-how-to-add-example-utterances.md) (Aggiungere espressioni di esempio) per informazioni su come insegnare a un'app LUIS a capire le espressioni degli utenti.

