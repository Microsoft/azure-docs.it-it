---
title: Tune Model Hyperparameters
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Tune Model iperparametris in Azure Machine Learning per eseguire uno sweep di parametri in un modello per determinare le impostazioni ottimali dei parametri.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/17/2020
ms.openlocfilehash: b88a9477c9d63becb32bc95ca78b4a8117168a47
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907761"
---
# <a name="tune-model-hyperparameters"></a>Tune Model Hyperparameters

Questo articolo descrive come usare il modulo Tune Model iperparametris in Azure Machine Learning Designer. L'obiettivo è determinare gli iperparametri ottimali per un modello di machine learning. Il modulo compila e testa più modelli usando diverse combinazioni di impostazioni. Viene confrontata la metrica su tutti i modelli per ottenere le combinazioni di impostazioni. 

Il *parametro* dei termini e l' *iperparametro* possono essere confusi. I *parametri* del modello sono quelli impostati nel riquadro destro del modulo. Fondamentalmente, questo modulo esegue lo *sweep dei parametri* sulle impostazioni del parametro specificate. Viene illustrato un set ottimale di _iperparametri_, che potrebbe essere diverso per ogni particolare albero delle decisioni, set di dati o metodo di regressione. Il processo di individuazione della configurazione ottimale viene talvolta definito *ottimizzazione*. 

Il modulo supporta il metodo seguente per trovare le impostazioni ottimali per un modello: *training integrato e ottimizzazione.* In questo metodo è possibile configurare un set di parametri da usare. Quindi si consente al modulo di scorrere più combinazioni. Il modulo misura l'accuratezza fino a trovare un modello "migliore". Con la maggior parte dei moduli di apprendimento è possibile scegliere quali parametri devono essere modificati durante il processo di training e quali devono rimanere corretti.

A seconda del periodo di tempo in cui si desidera eseguire il processo di ottimizzazione, è possibile decidere di testare in modo esaustivo tutte le combinazioni. In alternativa, è possibile abbreviare il processo stabilendo una griglia di combinazioni di parametri e testando un subset casuale della griglia dei parametri.

Questo metodo genera un modello sottoposto a training che è possibile salvare per il riutilizzo.  

> [!TIP] 
> È possibile eseguire un'attività correlata. Prima di iniziare l'ottimizzazione, applicare la selezione delle caratteristiche per determinare le colonne o le variabili con il valore più elevato per le informazioni.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Come configurare gli iperparametri del modello di ottimizzazione  

L'apprendimento degli iperparametri ottimali per un modello di apprendimento automatico richiede un notevole utilizzo delle pipeline.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Eseguire il training di un modello usando uno sweep di parametri  

Questa sezione descrive come eseguire uno sweep di parametri di base, che esegue il training di un modello usando il modulo Tune Model iperparametri.

1.  Aggiungere il modulo Tune Model iperparametris alla pipeline nella finestra di progettazione.

2.  Connettere un modello non sottoposto a training all'input più a sinistra. 

    > [!NOTE] 
    > L' **ottimizzazione degli iperparametri del modello** può essere connessa solo ai moduli di algoritmi di Machine Learning predefiniti e non può supportare il modello personalizzato creato in creare un **modello Python**.


3.  Aggiungere il set di dati che si vuole usare per il training e connetterlo all'input centrale degli iperparametri del modello di ottimizzazione.  

    Facoltativamente, se si dispone di un set di dati con tag, è possibile connetterlo alla porta di input più a destra (**set di dati di convalida facoltativo**). In questo modo è possibile misurare l'accuratezza durante il training e l'ottimizzazione.

4.  Nel riquadro di destra di ottimizzare gli iperparametri del modello, scegliere un valore per la **modalità di sweep dei parametri**. Questa opzione consente di controllare la modalità di selezione dei parametri.

    - **Griglia intera**: quando si seleziona questa opzione, il modulo esegue il ciclo su una griglia predefinita dal sistema, per provare combinazioni diverse e identificare il migliore discente. Questa opzione è utile quando non si conoscono le impostazioni dei parametri migliori e si desidera provare tutte le possibili combinazioni di valori.

    - **Sweep casuale**: quando si seleziona questa opzione, il modulo selezionerà i valori dei parametri in modo casuale su un intervallo definito dal sistema. È necessario specificare il numero massimo di esecuzioni che si desidera venga eseguito dal modulo. Questa opzione è utile quando si desidera migliorare le prestazioni del modello usando le metriche desiderate, conservando comunque le risorse di calcolo.    

5.  Per **colonna etichetta**, aprire il selettore di colonna per scegliere una singola colonna etichetta.

6.  Scegliere il numero di esecuzioni:

    - **Numero massimo di esecuzioni nello Sweep casuale**: se si sceglie una sweep casuale, è possibile specificare il numero di volte in cui il modello deve essere sottoposto a training, usando una combinazione casuale di valori di parametro.

7.  Per la **classificazione**, scegliere una singola metrica da usare per classificare i modelli.

    Quando si esegue uno sweep di parametri, il modulo calcola tutte le metriche applicabili per il tipo di modello e le restituisce nel rapporto **risultati Sweep** . Il modulo usa metriche separate per i modelli di regressione e classificazione.

    Tuttavia, la metrica scelta determina il modo in cui vengono classificati i modelli. Solo il modello superiore, classificato in base alla metrica scelta, viene restituito come modello sottoposto a training da usare per l'assegnazione dei punteggi.

8.  Per il valore di **inizializzazione casuale**, immettere un numero da usare per avviare lo sweep dei parametri. 

9. Inviare la pipeline.

## <a name="results-of-hyperparameter-tuning"></a>Risultati dell'ottimizzazione degli iperparametri

Al termine del training:

+ Per visualizzare i risultati dello sweep, fare clic con il pulsante destro del mouse sul modulo, quindi scegliere **Visualizza**.

    L'output include tutte le metriche relative a sweep e accuratezza dei parametri applicabili al tipo di modello, ma la metrica selezionata per la classificazione determina quale modello viene considerato "migliore".

+ Per salvare uno snapshot del modello sottoposto a training, selezionare la scheda **output + log** nel riquadro di destra del modulo **Train Model** . Selezionare l'icona **registra set di dati** per salvare il modello come modulo riutilizzabile.


## <a name="technical-notes"></a>Note tecniche

Questa sezione contiene i suggerimenti e i dettagli di implementazione.

### <a name="how-a-parameter-sweep-works"></a>Funzionamento di uno sweep di parametri

Quando si configura uno sweep di parametri, si definisce l'ambito della ricerca. La ricerca potrebbe usare un numero finito di parametri selezionati in modo casuale. Oppure potrebbe trattarsi di una ricerca completa su uno spazio dei parametri definito dall'utente.

+ **Sweep casuale**: questa opzione consente di il training di un modello usando un numero impostato di iterazioni. 

  È possibile specificare un intervallo di valori di cui eseguire l'iterazione e il modulo usa un subset di tali valori scelto in modo casuale. I valori vengono scelti con la sostituzione, ovvero i numeri scelti in modo casuale non vengono rimossi dal pool di numeri disponibili. Quindi, la probabilità che qualsiasi valore selezionato rimanga invariata in tutti i passaggi.  

+ **Griglia intera**: l'opzione per l'utilizzo dell'intera griglia indica che ogni combinazione viene testata. Questa opzione è la più completa, ma richiede la maggior parte del tempo. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Controllo della lunghezza e della complessità del training

L'iterazione su molte combinazioni di impostazioni può richiedere molto tempo, in modo che il modulo fornisca diversi modi per vincolare il processo:

+ Limitare il numero di iterazioni utilizzate per il test di un modello.
+ Limitare lo spazio dei parametri.
+ Limitare sia il numero di iterazioni che lo spazio dei parametri.

È consigliabile eseguire la pipeline con le impostazioni per determinare il metodo più efficiente di training su un set di dati e un modello specifici.

### <a name="choosing-an-evaluation-metric"></a>Scelta di una metrica di valutazione

Al termine del test, il modello presenta un report che contiene l'accuratezza per ogni modello, in modo da poter esaminare i risultati della metrica:

- Per tutti i modelli di classificazione binaria viene usato un set uniforme di metriche.
- L'accuratezza viene utilizzata per tutti i modelli di classificazione multiclasse.
- Per i modelli di regressione viene usato un set di metriche diverso. 

Tuttavia, durante il training, è necessario scegliere una *singola* metrica da usare per classificare i modelli generati durante il processo di ottimizzazione. Si potrebbe notare che la metrica migliore varia a seconda del problema aziendale e del costo di falsi positivi e falsi negativi.

#### <a name="metrics-used-for-binary-classification"></a>Metriche usate per la classificazione binaria

-   L' **accuratezza** è la percentuale di risultati reali al numero totale di case.  

-   La **precisione** è la proporzione dei risultati reali ai risultati positivi.  

-   Il **richiamo** è la frazione di tutti i risultati corretti rispetto a tutti i risultati.  

-   Il **Punteggio F** è una misura che bilancia la precisione e il richiamo.  

-   **AUC** è un valore che rappresenta l'area sotto la curva quando vengono tracciati falsi positivi sull'asse x e i veri positivi sono tracciati sull'asse y.  

-   La **perdita di log media** è la differenza tra due distribuzioni di probabilità: la vera e l'altra nel modello.  

#### <a name="metrics-used-for-regression"></a>Metriche usate per la regressione

-   **Mean Absolute Error** media tutti gli errori nel modello, in cui *Error* indica la distanza del valore stimato rispetto al valore true. Viene spesso abbreviato come *Mae*.  

-   La **radice dell'errore quadratico medio** misura la media dei quadrati degli errori e quindi prende la radice di tale valore. Viene spesso abbreviato in *valori RMSE*.  

-   **Errore assoluto relativo** rappresenta l'errore come percentuale del valore true.  

-   L' **errore quadratico relativo** normalizza l'errore quadrato totale dividendo per l'errore quadrato totale dei valori stimati.  

-   Il **coefficiente di determinazione** è un numero singolo che indica il modo in cui i dati si adattano a un modello. Il valore 1 indica che il modello corrisponde esattamente ai dati. Un valore pari a zero indica che i dati sono casuali, altrimenti non possono essere adattati al modello. Spesso è chiamato *r<sup>2</sup>*, *r<sup>2</sup>* o *r-squared*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Moduli che non supportano lo sweep di parametri

Quasi tutti gli studenti in Azure Machine Learning supportano la convalida incrociata con uno sweep di parametri integrato, che consente di scegliere i parametri con cui eseguire la pipeline. Se lo Learner non supporta l'impostazione di un intervallo di valori, è comunque possibile utilizzarlo nella convalida incrociata. In questo caso, per lo sweep viene selezionato un intervallo di valori consentiti. 


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

