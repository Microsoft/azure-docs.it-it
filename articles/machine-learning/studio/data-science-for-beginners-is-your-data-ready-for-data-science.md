---
title: Valutazione dei dati
titleSuffix: ML Studio (classic) - Azure
description: Quattro criteri che i dati devono soddisfare per essere pronti per l'analisi scientifica. Questo video illustra esempi concreti per la valutazione dei dati di base.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 8eb9c5a182c17815d4efcf171cc99b1e9f5bc89e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82930166"
---
# <a name="is-your-data-ready-for-data-science"></a>Sono pronti i dati per l'analisi scientifica?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: Analisi scientifica dei dati per principianti
Informazioni su come valutare i dati per assicurarsi che questo processo soddisfi i criteri di base per la preparazione per l'analisi scientifica dei dati.

Per trarre il meglio dalla serie è consigliabile guardare tutti i video. [L'elenco dei video è disponibile qui](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Altri video della serie
*Analisi scientifica dei dati per principianti* è una rapida introduzione all'analisi scientifica dei dati in cinque brevi video.

* Video 1: [5 domande a cui può rispondere l'analisi scientifica dei dati](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min e 14 sec)*
* Video 2: Verifica della preparazione dei dati per l'analisi scientifica dei dati
* Video 3: [Porre una domanda a cui è possibile rispondere con i dati](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min e 17 sec)*
* Video 4: [Prevedere una risposta con un modello semplice](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min e 42 sec)*
* Video 5: [Copiare il lavoro di altre persone per l'analisi scientifica dei dati](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min e 18 sec)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Trascrizione: Sono pronti i dati per l'analisi scientifica?
Questo è il video "I tuoi dati sono pronti per l'analisi scientifica?", il secondo della serie *Data Science for Beginners* (Analisi scientifica dei dati per principianti).  

Per ottenere le risposte desiderate dall'analisi scientifica dei dati, è necessario fornire materiale non elaborato di alta qualità. Proprio come preparare una pizza: più buoni sono gli ingredienti usati, migliore sarà il risultato finale. 

## <a name="criteria-for-data"></a>Criteri per i dati
In data science esistono alcuni componenti di cui deve essere eseguito il pull contemporaneamente. Tra questi:

* Rilevanti
* Connesso
* Accurati
* In quantità sufficiente

## <a name="is-your-data-relevant"></a>Sono rilevanti i dati?
Per prima cosa, i dati devono essere rilevanti.

![Dati rilevanti vs. dati irrilevanti, valutare i dati](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

La tabella a sinistra presenta il livello di alcol nel sangue di sette persone testato fuori da un bar di Boston, la media battuta dei Red Sox nell'ultimo match e il prezzo del latte nel negozio all'angolo.

Si tratta di dati del tutto legittimi. L'unico inconveniente è che non sono rilevanti. Non esiste alcuna relazione ovvia tra questi numeri. Infatti, dati il prezzo attuale del latte e la media battuta dei Red Sox, non vi è alcun modo per risalire al contenuto di alcol nel sangue.

Osservando la tabella a destra si apprende invece che è stata misurata la massa corporea di ciascun individuo ed è stato contato il numero di bevande ingerite.  I numeri presenti su ogni riga sono adesso rilevanti l'uno per l'altro. Data la massa corporea di un individuo e il numero di margarita bevuti, è possibile stimare la quantità di alcol presente nel sangue.

## <a name="do-you-have-connected-data"></a>I dati a disposizione sono connessi?
L'ingrediente successivo è rappresentato dai dati connessi.

![Dati connessi vs. dati disconnessi: criteri dei dati, preparazione dei dati](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Ecco alcuni dati rilevanti sulla qualità degli hamburger: temperatura della griglia, peso della carne e classificazione nella rivista sul cibo locale. Si presti però attenzione agli spazi vuoti nella tabella a sinistra.

Alcuni valori non sono presenti nella maggior parte dei set di dati. Accade spesso di avere buchi del genere ed esistono delle soluzioni per colmarli. Tuttavia, se mancano diversi valori, i dati iniziano a somigliare a un formaggio svizzero.

Dalla tabella a sinistra si evince che la quantità di dati mancanti è talmente elevata da poter difficilmente ipotizzare qualsiasi tipo di relazione tra la temperatura della griglia e il peso della carne. Questo esempio mostra dati disconnessi.

La tabella a destra, invece, è interamente completa e rappresenta un esempio di dati connessi.

## <a name="is-your-data-accurate"></a>Sono accurati i dati?
Il successivo componente necessario è l'accuratezza. Ecco quattro bersagli da colpire.

![Dati accurati vs. dati non accurati - criteri di dati](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Si osservi il bersaglio in alto a destra. Attorno al punto centrale vi è un raggruppamento stretto. Quello è naturalmente accurato. Stranamente, nel linguaggio di data science, anche le prestazioni poco al di sotto del centro del bersaglio sono considerate accurate.

Se si tratteggiasse il centro di queste frecce, si vedrebbe che è molto vicino al centro del bersaglio. Le frecce sono distribuite attorno al bersaglio, quindi considerate imprecise, ma sono centrate attorno al centro del bersaglio, quindi considerate accurate.

Osservando adesso il bersaglio in alto a sinistra, le frecce sono molto vicine l'una all'altra, a formare un raggruppamento stretto. Sono precise, ma non accurate perché il centro è fuori dal centro del bersaglio. Le frecce nel bersaglio in fondo a sinistra sono sia inaccurate sia imprecise. Questo arciere deve fare più pratica.

## <a name="do-you-have-enough-data-to-work-with"></a>La quantità dei dati a disposizione con cui lavorare è sufficiente?
Infine, il quarto elemento è rappresentato da dati sufficienti.

![La quantità dei dati a disposizione è sufficiente per l'analisi? Valutazione dei dati](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Si immagini che ciascun punto di dati nella tabella sia una pennellata in un dipinto. Se le pennellate sono poche, il dipinto può apparire confuso ed è difficile stabilire quale sia il soggetto.

Se si danno altre pennellate, il dipinto inizia quindi a essere più nitido.

Quando i tratti sono appena sufficienti, è possibile vedere soltanto quanto basta per prendere alcune decisioni approssimative. Si tratta di un posto che mi piacerebbe visitare? C'è molta luce, l'acqua sembra limpida: sì, è il posto in cui andrò in vacanza.

Aggiungendo sempre più dati, l'immagine diventa più chiara ed è possibile prendere decisioni più dettagliate. Adesso, guardando i tre hotel sulla riva sinistra, è possibile ammirare le straordinarie caratteristiche architettoniche di quello in primo piano. È anche possibile scegliere di restare al terzo piano per la vista mozzafiato.

Dati rilevanti, connessi, accurati e in quantità sufficiente rappresentano tutti gli ingredienti necessari per analisi di data science di alta qualità.

Assicurarsi di consultare gli altri quattro video di *Data Science per principianti* da Microsoft Azure Machine Learning Studio (classico).

## <a name="next-steps"></a>Passaggi successivi
* [Provare un primo esperimento di data science con Machine Learning Studio (classico)](create-experiment.md)
* [Leggere l'Introduzione all'apprendimento automatico in Microsoft Azure](/azure/machine-learning/overview-what-is-azure-ml)
