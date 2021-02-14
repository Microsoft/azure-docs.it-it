---
title: 'ML Studio (versione classica) - Esercitazione 2: Eseguire il training di modelli di rischio di credito - Azure'
description: Esercitazione dettagliata che mostra come creare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning Studio (versione classica). Questa esercitazione è la seconda di una serie in tre parti. Illustra come eseguire il training e la valutazione dei modelli.
keywords: rischio di credito, soluzione di analisi predittiva, valutazione del rischio
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 677c5b791f475468fbcf15c81fbabdcdbb1972de
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517451"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Esercitazione 2: Eseguire il training di modelli di rischio di credito - Azure Machine Learning Studio (versione classica)

**SI APPLICA A:**  ![Questo è un segno di spunta, che significa che l'articolo si applica a Machine Learning Studio (versione classica).](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (versione classica) ![Questa è una X, che significa che l'articolo si applica ad Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

In questa esercitazione si esamina il processo di sviluppo di una soluzione di analisi predittiva. Si svilupperà un modello semplice in Machine Learning Studio (versione classica).  Il modello verrà quindi distribuito come servizio Web di Azure Machine Learning.  Questo modello distribuito può creare previsioni usando nuovi dati. Questa esercitazione è la **seconda di una serie in tre parti**.

Si supponga di dover prevedere il rischio di credito di un soggetto in base alle informazioni fornite in una richiesta di credito.  

La valutazione del rischio di credito è un problema complesso che verrà tuttavia semplificato con questa esercitazione. Verrà usata come esempio di come è possibile creare una soluzione di analisi predittiva con Microsoft Azure Machine Learning Studio (versione classica). Per questa soluzione si useranno Azure Machine Learning Studio (versione classica) e un servizio Web di Machine Learning.  

In questa esercitazione in tre parti si inizia con dati sul rischio di credito disponibili pubblicamente.  Verrà quindi sviluppato e sottoposto a training un modello predittivo.  Il modello verrà infine distribuito come servizio Web.

Nella [prima parte dell'esercitazione](tutorial-part1-credit-risk.md) è stata creata un'area di lavoro di Machine Learning Studio (versione classica), sono stati caricati i dati ed è stato creato un esperimento.

In questa parte dell'esercitazione verranno eseguite queste operazioni:
 
> [!div class="checklist"]
> * Eseguire il training di più modelli
> * Classificare e valutare i modelli


Nella [terza parte dell'esercitazione](tutorial-part3-credit-risk-deploy.md) si distribuirà il modello come servizio Web.

## <a name="prerequisites"></a>Prerequisiti

Completare la [prima parte dell'esercitazione](tutorial-part1-credit-risk.md).

## <a name="train-multiple-models"></a><a name="train"></a>Eseguire il training di più modelli

Uno dei vantaggi che offre Azure Machine Learning Studio (versione classica) per la creazione di modelli di Machine Learning è la possibilità di valutare più tipi di modelli contemporaneamente in un singolo esperimento e confrontare i risultati. Questo tipo di esperimento consente di trovare la soluzione migliore per il problema che si desidera risolvere.

Nell'esperimento sviluppato in questa esercitazione verranno creati due tipi diversi di modello, quindi ne verranno confrontati i punteggi risultanti per decidere quale algoritmo usare nell'esperimento finale.  

Sono disponibili diversi modelli tra cui scegliere. Per visualizzare i modelli disponibili, espandere il nodo **Machine Learning** nella tavolozza dei moduli, espandere **Initialize Model** (Inizializza modello) e quindi i nodi al suo interno. Ai fini di questo esperimento, verranno selezionati i moduli [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi), o SVM, e [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi).

> [!TIP]
> Per ottenere informazioni sul tipo di algoritmo di Machine Learning più adatto per risolvere un problema specifico, vedere [Come scegliere gli algoritmi di Microsoft Azure Machine Learning Studio (versione classica)](../how-to-select-algorithms.md).
> 
> 

In questo esperimento verranno aggiunti il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi) e il modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi).

### <a name="two-class-boosted-decision-tree"></a>Two-Class Boosted Decision Tree

Prima di tutto verrà configurato il modello di albero delle decisioni con boosting.

1. Trovare il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi) nel pannello dei moduli e trascinarlo nel canvas.

1. Trovare il modulo [Train Model][train-model] (Training modello), trascinarlo nel canvas, quindi connettere l'output del modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi) alla porta di input sinistra del modulo [Train Model][train-model] (Training modello).
   
   Il modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi) inizializza il modello generico e [Train Model][train-model] (Training modello) usa i dati di training per il training del modello. 

1. Connettere l'output sinistro del modulo [Execute R Script][execute-r-script] sinistro alla porta di input destra del modulo [Train Model][train-model] (Training modello). In questa esercitazione [sono stati usati i dati provenienti dal lato sinistro](#train) del modulo Split Data (Divisione dati) per il training.
   
   > [!TIP]
   > Due degli input e uno degli output del modulo [Execute R Script][execute-r-script] (Esegui script R) non sono necessari per questo esperimento, pertanto potranno rimanere scollegati. 
   > 
   > 

Questa parte dell'esperimento avrà ora un aspetto analogo al seguente:  

![Training a model](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

A questo punto è necessario indicare al modulo [Train Model][train-model] (Training modello) che il modello dovrà stimare il valore del rischio di credito.

1. Selezionare il modulo [Train Model][train-model]. Nel riquadro **Proprietà**, fare clic su **Launch column selector** (Avvia selettore colonne).

1. Nella finestra di dialogo **Select a single column** (Selezionare una singola colonna), digitare "rischio di credito" nel campo di ricerca in **Colonne disponibili**, selezionare "Rischio di credito" di seguito e fare clic sul pulsante freccia destra ( **>** ) per spostare "Rischio di credito" su **Colonne selezionate**. 

    ![Selezionare la colonna Rischio di credito per il modulo "Train Model" (Training modello)](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Fare clic sul segno di spunta **OK**.

### <a name="two-class-support-vector-machine"></a>Two-Class Support Vector Machine

Successivamente si configura il modello SVM.  

Innanzitutto, una breve spiegazione di SVM. Gli alberi delle decisioni con boosting funzionano bene con qualsiasi tipo di funzionalità. Poiché tuttavia il modulo di macchina a vettori di supporto genera un classificatore lineare, il modello che genera ha l'errore di test migliore quando gli elementi numerici hanno la stessa scala. Per convertire tutte le funzioni numeriche nella stessa scala, usare una trasformazione "Tanh" con il modulo [Normalize Data][normalize-data] (Normalizza dati). In questo modo, vengono trasformati i numeri nell'intervallo [0,1]. I modulo SVM converte gli elementi stringa in elementi di categoria e quindi in elementi 0/1 binari, quindi non è necessario trasformare manualmente gli elementi stringa. Non si deve inoltre trasformare la colonna Credit Risk (colonna 21): si tratta di un valore numerico, ma è il valore per il quale il modulo deve eseguire la previsione dopo il training e non dovrà essere quindi modificato.  

Per configurare il modello SVM, procedere come segue:

1. Trovare il modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi) nel pannello dei moduli e trascinarlo nel canvas.

1. Fare clic con il pulsante destro del mouse sul modulo [Train Model][train-model] (Training modello), scegliere **Copia**, quindi fare clic con il pulsante destro del mouse sul canvas e scegliere **Incolla**. La copia del modulo [Train Model][train-model] (Training modello) presenta la stessa selezione di colonne dell'originale.

1. Connettere l'output del modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi) alla porta di input sinistra del secondo modulo [Train Model][train-model] (Training modello).

1. Trovare il modulo [Normalize Data][normalize-data] (Normalizza dati) e trascinarlo nel canvas.

1. Connettere l'output sinistro del modulo [Execute R Script][execute-r-script] (Esegui script R) sinistro all'input di questo modulo (si noti che la porta di output di un modulo potrebbe essere connessa a più di un modulo).

1. Connettere la porta di output sinistra del modulo [Normalize Data][normalize-data] (Normalizza dati) alla porta di input destra del secondo modulo [Train Model][train-model] (Training modello).

Questa parte dell'esperimento avrà ora un aspetto analogo al seguente:  

![Training the second model](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Ora configurare il modulo [Normalize Data][normalize-data] (Normalizza dati):

1. Fare clic per selezionare il modulo [Normalize Data][normalize-data] (Normalizza dati). Nel riquadro **Proprietà** relativo al modulo di trasformazione scegliere **Tanh** per il parametro **Transformation method** (Metodo di trasformazione).

1. Fare clic su **Launch column selector** (Avvia selettore di colonna), selezionare "No columns" (Nessuna colonna) per **Begin With** (Inizia con), selezionare **Include** (Includi) nel primo elenco a discesa, selezionare **column type** (tipo di colonna) nel secondo elenco a discesa e infine selezionare **Numeric** (Numerico) nel terzo elenco a discesa. Questo specifica che verranno trasformate tutte le colonne numeriche, ma solo quelle di questo tipo.

1. Fare clic sul segno più (+) a destra di questa riga per creare una nuova riga di elenchi a discesa. Selezionare **Escludi** nel primo elenco a discesa, selezionare **column names** (nomi colonna) nel secondo elenco a discesa e immettere "Rischio di credito" nel campo di testo. Questa impostazione specifica che la colonna Credit Risk deve essere ignorata. Ciò è necessario perché la colonna è numerica e verrebbe quindi trasformata se non venisse esclusa.

1. Fare clic sul segno di spunta **OK**.  

    ![Selezionare le colonne per il modulo Normalize Data (Normalizza dati)](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


Il modulo [Normalize Data][normalize-data] (Normalizza dati) è ora impostato per eseguire una trasformazione Tanh su tutte le colonne numeriche, a eccezione della colonna del rischio di credito.  

## <a name="score-and-evaluate-the-models"></a>Classificare e valutare i modelli

Si useranno i dati di test che sono stati separati dal modulo [Split Data][split] (Divisione dati) per assegnare il punteggio ai moduli sottoposti a training. Sarà quindi possibile confrontare i risultati dei due modelli per stabilire quale ha generato i risultati migliori.  

### <a name="add-the-score-model-modules"></a>Aggiungere i moduli Score Model (Punteggio modello)

1. Trovare il modulo [Score Model][score-model] (Punteggio modello) e trascinarlo nel canvas.

1. Connettere il modulo [Train Model][train-model] (Training modello) connesso al modulo [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Albero delle decisioni con boosting a due classi) alla porta di input sinistra del modulo [Score Model][score-model] (Punteggio modello).

1. Connettere il modulo [Execute R Script][execute-r-script] (Esecuzione script R) destro alla porta di input destra del modulo [Score Model][score-model] (Punteggio modello).

    ![Modulo Score Model (Punteggio modello) connesso](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   Il modulo [Score Model][score-model] (Punteggio modello) può ora prelevare le informazioni sul credito dai dati di test, eseguirle tramite il modello e confrontare le previsioni generate dal modello con la colonna relativa al rischio di credito dei dati di test.

1. Copiare e incollare il modulo [Score Model][score-model] (Punteggio modello) per creare una seconda copia.

1. Connettere l'output del modello SVM, vale a dire la porta di output del modulo [Train Model][train-model] (Training modello) connessa al modulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Macchina a vettori di supporto a due classi), alla porta di input del secondo modulo [Score Model][score-model] (Punteggio modello).

1. Per il modello SVM è necessario eseguire la stessa trasformazione sui dati di test eseguita in precedenza sui dati di training. Pertanto, copiare e incollare il modulo [Normalize Data][normalize-data] (Normalizza dati) per creare una seconda copia e connetterla al modulo [Execute R Script][execute-r-script] (Esecuzione script R) destro.

1. Connettere l'output sinistro del secondo modulo [Normalize Data][normalize-data] (Normalizza dati) alla porta di input destra del secondo modulo [Score Model][score-model] (Punteggio modello).

    ![Entrambi i moduli Score Model (Punteggio modello) connessi](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Aggiungere il modulo Evaluate Model (Modello di valutazione)

Per valutare i due risultati di punteggio e confrontarli, viene usato il modulo [Evaluate Model][evaluate-model] (Valutazione modello).  

1. Trovare il modulo [Evaluate Model][evaluate-model] (Valutazione modello) e trascinarlo nel canvas.

1. Connettere la porta di output del modulo [Score Model][score-model] (Punteggio modello) associata al modello di albero delle decisioni con boosting alla porta di input sinistra del modulo [Evaluate Model][evaluate-model] (Valutazione modello).

1. Connettere l'altro modulo [Score Model][score-model] (Punteggio modello) alla porta di input destra.  

    ![Modulo Evaluate Model (Modello di valutazione) connesso](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Eseguire l'esperimento e controllare i risultati

Per eseguire l'esperimento, fare clic sul pulsante **RUN** (ESEGUI) sotto l'area di disegno. L'operazione potrebbe richiedere alcuni minuti. Su ogni modulo verrà visualizzato un indicatore rotante per indicare che il modulo è in esecuzione, quindi apparirà un segno di spunta verde al termine dell'esecuzione. Quando tutti i moduli presentano il segno di spunta, l'esecuzione dell'esperimento sarà completa.

L'esperimento avrà ora un aspetto analogo al seguente:  

![Valutazione di entrambi i modelli](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Per verificare i risultati, fare clic sulla porta di output del modulo [Evaluate Model][evaluate-model] (Valutazione modello) e selezionare **Visualize** (Visualizza).  

Il modulo [Evaluate Model][evaluate-model] (Valutazione modello) produce una coppia di curve e metriche che consentono di confrontare i risultati dei due modelli classificati. È possibile visualizzare i risultati come curve ROC (Receiver Operator Characteristic), curve precisione/recupero o curve di accuratezza. Altri dati visualizzati includono una matrice di confusione, valori cumulativi per l'area nella curva (AUC) e altra metrica. È possibile modificare il valore soglia spostando il dispositivo di scorrimento a sinistra o a destra e vedere come ciò influisce sul set della metrica.  

A destra del grafico fare clic su **Scored dataset** (Set di dati con punteggio) o **Scored dataset to compare** (Set di dati con punteggio da confrontare) per evidenziare la curva associata e visualizzare le metriche associate più sotto. Nella legenda per le curve, "Scored dataset" (Set di dati con punteggio) corrisponde alla porta di input sinistra del modulo [Evaluate Model][evaluate-model] (Modello di valutazione). In questo caso si tratta del modello di albero delle decisioni con boosting. "Scored dataset to compare" corrisponde alla porta di input destra, in questo caso il modello di macchina a vettori di supporto. Quando si fa clic su una di queste etichette, viene evidenziata la curva per il modello e viene visualizzata la metrica corrispondente, come mostrato dalla grafica seguente.  

![ROC curves for models](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Esaminando questi valori, è possibile decidere quale sia il modello che più si avvicina ai risultati previsti. È possibile tornare indietro ed eseguire l'iterazione dell'esperimento modificando i valori di parametro dei vari modelli. 

La scienza e l'arte di interpretare questi risultati e di ottimizzare le prestazioni del modello non rientrano nell'ambito di questa esercitazione. Per ulteriori informazioni, è possibile leggere gli articoli seguenti:
- [Come valutare le prestazioni del modello in Azure Machine Learning Studio (versione classica)](evaluate-model-performance.md)
- [Scegliere i parametri per ottimizzare gli algoritmi in Azure Machine Learning Studio (versione classica)](algorithm-parameters-optimize.md)
- [Interpretare i risultati dei modelli in Azure Machine Learning Studio (versione classica)](interpret-model-results.md)

> [!TIP]
> Ogni volta che si esegue l'esperimento, viene conservato un record dell'iterazione nella cronologia di esecuzione. È possibile visualizzare le iterazioni e tornare a una qualsiasi di esse facendo clic su **VISUALIZZA CRONOLOGIA ESECUZIONI** sotto l'area di disegno. È anche possibile fare clic su **Prior Run** (Esecuzione precedente) nel riquadro **Properties** (Proprietà) per tornare all'iterazione immediatamente precedente a quella aperta.
> 
> È possibile eseguire una copia di qualsiasi interazione dell'esperimento facendo clic su **SALVA CON NOME** sotto l'area di disegno. 
> Usare le proprietà **Summary** (Riepilogo) e **Description** (Descrizione) dell'esperimento per tenere traccia dei tentativi eseguiti nelle iterazioni dell'esperimento.
> 
> Per altre informazioni, vedere [Gestire iterazioni dell'esperimento in Azure Machine Learning Studio (versione classica)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati completati i passaggi seguenti: 
 
> [!div class="checklist"]
> * Creare un esperimento
> * Eseguire il training di più modelli
> * Classificare e valutare i modelli

A questo punto si è pronti per distribuire i modelli per i dati.

> [!div class="nextstepaction"]
> [Esercitazione 3 - Distribuire modelli](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/