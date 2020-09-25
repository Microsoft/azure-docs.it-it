---
title: Punteggio di confidenza-QnA Maker
titleSuffix: Azure Cognitive Services
description: Quando una query dell'utente viene confrontata con una knowledge base, QnA Maker restituisce le risposte pertinenti insieme a un punteggio di attendibilità.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.custom: seodec18
ms.openlocfilehash: abae83cee106feb553e8ced404d23ba5619ba416
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327154"
---
# <a name="the-confidence-score-of-an-answer"></a>Punteggio di attendibilità di una risposta
Quando una query dell'utente viene confrontata con una knowledge base, QnA Maker restituisce le risposte pertinenti insieme a un punteggio di attendibilità. Questo punteggio indica la probabilità che la risposta corrisponda perfettamente alla query dell'utente specificata.

Il punteggio di attendibilità è un numero compreso tra 0 e 100. Un punteggio pari a 100 indica probabilmente una corrispondenza esatta, mentre un punteggio pari a 0 indica che non è stata trovata alcuna risposta corrispondente. Maggiore sarà il punteggio, maggiore sarà l'attendibilità della risposta. Per una determinata query potrebbero essere restituite più risposte. In questo caso le risposte sono restituite in ordine decrescente in base al punteggio di attendibilità.

Nell'esempio seguente è possibile vedere una sola entità domanda/risposta con 2 domande.


![Esempio di coppia domanda/risposta](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Per l'esempio precedente è possibile aspettarsi punteggi come quelli dell'intervallo di esempio riportato di seguito, per diversi tipi di query utente:


![Intervallo di punteggi](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


La tabella seguente indica l'attendibilità tipica associata a un determinato punteggio.

|Valore del punteggio|Significato del punteggio|Query di esempio|
|--|--|--|
|90-100|Corrispondenza quasi esatta tra query dell'utente e domanda della knowledge base|"Le modifiche non vengono aggiornate nella knowledge base dopo la pubblicazione"|
|> 70|Attendibilità elevata: in genere una risposta appropriata che risponde completamente alla query dell'utente|"Ho pubblicato la mia knowledge base ma non è aggiornata"|
|50-70|Attendibilità media: in genere una risposta piuttosto appropriata che dovrebbe rispondere alla finalità principale della query dell'utente|"Devo salvare gli aggiornamenti personali prima di pubblicare la mia knowledge base?"|
|30-50|Attendibilità bassa: in genere una risposta correlata che risponde parzialmente alla finalità dell'utente|"Cosa fa la funzione Save and train (Salva ed esegui training)?"|
|< 30|Attendibilità molto bassa: in genere non risponde alla query dell'utente, ma include alcune parole o frasi corrispondenti |"Dove posso aggiungere sinonimi alla mia knowledge base?"|
|0|Nessuna corrispondenza, perciò non viene restituita alcuna risposta.|"Quanto costa il servizio?"|

## <a name="choose-a-score-threshold"></a>Scegliere un punteggio soglia
La tabella precedente illustra i punteggi previsti nella maggior parte delle knowledge base. Tuttavia, poiché ogni KB è diverso e presenta tipi diversi di parole, finalità e obiettivi, è consigliabile testare e scegliere la soglia più adatta alle proprie esigenze. Per impostazione predefinita, la soglia è impostata su 0, in modo che vengano restituite tutte le risposte possibili. La soglia consigliata che dovrebbe funzionare per la maggior parte delle KB è **50**.

Quando si sceglie la soglia, tenere presente il bilanciamento tra Accuracy (Precisione) e Coverage (Attinenza) e modificare la soglia in base alle esigenze.

- Quando **Accuracy** (Precisione) è più importante, aumentare la soglia. In questo modo, ogni volta che una risposta viene restituita, essa sarà molto più ATTENDIBILE e avrà una probabilità molto maggiore di essere quella che l'utente sta cercando. In questo modo molte domande potrebbero rimanere senza risposta. *Ad esempio:* se si imposta la soglia su **70**, si potrebbe escludere alcuni esempi ambigui come "Cosa fa la funzione Save and train (Salva ed esegui training)?".

- Se **Coverage** (Attinenza) è più importante e si desidera rispondere al massimo numero di domande possibile, anche se la risposta ha una relazione solo parziale con la domanda dell'utente, ABBASSARE la soglia. In questo modo potrebbero esserci più casi in cui la risposta non risponde all'effettiva query dell'utente ma offre informazioni attinenti alla domanda. *Ad esempio* , se si esegue la soglia **30**, è possibile fornire risposte per le query quali "dove è possibile modificare la mia KB?"

> [!NOTE]
> Le versioni più recenti di QnA Maker includono miglioramenti della logica di assegnazione dei punteggi e potrebbero influire sulla soglia. Ogni volta che si aggiorna il servizio, assicurarsi di testare e modificare la soglia, se necessario. È possibile controllare la versione del servizio QnA [qui](https://www.qnamaker.ai/UserSettings) e scoprire come ottenere gli aggiornamenti più recenti [qui](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Imposta soglia

Impostare il punteggio soglia come proprietà del [corpo JSON dell'API GenerateAnswer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Ciò significa che è possibile impostarlo per ogni chiamata a GenerateAnswer.

Dal Framework bot, impostare il punteggio come parte dell'oggetto Options con [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) o [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Migliorare i punteggi di attendibilità
Per ottimizzare il punteggio di attendibilità di una particolare risposta a una query dell'utente, è possibile aggiungere la query alla Knowledge Base come domanda alternativa per tale risposta. È anche possibile usare [variazioni delle parole](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) senza distinzione tra maiuscole e minuscole per aggiungere i sinonimi alle parole chiave nella Knowledge Base.


## <a name="similar-confidence-scores"></a>Punteggi di attendibilità simili
Quando più risposte hanno un punteggio di attendibilità simile, è probabile che la query fosse troppo generica e quindi associata con pari probabilità a più risposte. Provare a strutturare meglio le domande e le risposte in modo che ogni entità di domanda/risposta abbia una finalità distinta.


## <a name="confidence-score-differences-between-test-and-production"></a>Differenze di Punteggio di confidenza tra test e produzione
Il punteggio di attendibilità di una risposta può variare in modo trascurabile tra la versione di test e la versione pubblicata della Knowledge Base, anche se il contenuto è lo stesso. Ciò è dovuto al fatto che il contenuto del test e della Knowledge base pubblicata si trova in diversi indici di Azure ricerca cognitiva.

L'indice di test include tutte le coppie di QnA delle Knowledge base. Quando si esegue una query sull'indice di test, la query viene applicata all'intero indice, quindi i risultati sono limitati alla partizione per la Knowledge base specifica. Se i risultati della query di test influiscono negativamente sulla capacità di convalidare la Knowledge base, è possibile:
* organizzare la Knowledge base utilizzando una delle seguenti opzioni:
    * 1 risorsa limitata a 1 KB: limitare la singola risorsa QnA (e l'indice di test di Azure ricerca cognitiva risultante) a una singola Knowledge base.
    * 2 risorse-1 per test, 1 per la produzione: hanno due QnA Maker risorse, usando una per il testing (con gli indici di test e produzione) e una per il prodotto (anche con gli indici di test e produzione)
* e utilizzano sempre gli stessi parametri, ad esempio **[Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** , quando si eseguono query sulla Knowledge base di test e di produzione

Quando si pubblica una knowledge base, il contenuto di domande e risposte della knowledge base passa dall'indice di test a un indice di produzione in Ricerca di Azure. Vedere come funziona l'operazione di [pubblicazione](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

Se si dispone di una Knowledge base in aree diverse, ogni area usa il proprio indice ricerca cognitiva di Azure. Poiché vengono usati indici diversi, i punteggi non saranno esattamente gli stessi.


## <a name="no-match-found"></a>Nessuna corrispondenza trovata
Se lo strumento di classificazione non trova corrispondenze soddisfacenti, viene restituito il punteggio di attendibilità di 0.0 oppure "None" (Nessuno) e la risposta predefinita è "No good match found in the KB" (Nessuna buona corrispondenza trovata nella Knowledge Base). È possibile eseguire l'override di questa [risposta predefinita](../How-To/metadata-generateanswer-usage.md) nel bot o nel codice dell'applicazione che chiama l'endpoint. In alternativa è anche possibile impostare la risposta sostitutiva in Azure, modificando così la risposta predefinita per tutte le Knowledge Base distribuite in un particolare servizio QnA Maker.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Procedure consigliate](./best-practices.md)

