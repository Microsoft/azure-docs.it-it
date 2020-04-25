---
title: 'Modello di valutazione: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Evaluate Model in Azure Machine Learning per misurare l'accuratezza di un modello sottoposto a training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/24/2020
ms.openlocfilehash: cf9597f4a722ff9cda68e87b31db77c989afcb0b
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82129839"
---
# <a name="evaluate-model-module"></a>Modulo Evaluate Model

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Utilizzare questo modulo per misurare l'accuratezza di un modello sottoposto a training. Si fornisce un set di dati contenente i punteggi generati da un modello e il modulo **Evaluate Model** calcola un set di metriche di valutazione standard del settore.
  
 Le metriche restituite dal **modello di valutazione** dipendono dal tipo di modello che si sta valutando:  
  
-   **Modelli di classificazione**    
-   **Modelli di regressione**  
-   **Modelli di clustering**  


> [!TIP]
> Se non si ha familiarità con la valutazione del modello, si consiglia la serie di video di Dr. Stephen Elston come parte del [corso di apprendimento automatico](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) di EDX. 


## <a name="how-to-use-evaluate-model"></a>Come usare Evaluate Model
1. Connettere l'output del **set di dati con punteggio** del modello di [Punteggio](./score-model.md) alla porta di input sinistra di **Evaluate Model**. 

2. Opzionale Connettere l'output del **set di dati con punteggio** del modello di [Punteggio](./score-model.md) per il secondo modello all'input di **destra** di **Evaluate Model**. È possibile confrontare facilmente i risultati di due modelli diversi sugli stessi dati. I due algoritmi di input devono avere lo stesso tipo di algoritmo. In alternativa, è possibile confrontare i punteggi di due esecuzioni diverse sugli stessi dati con parametri diversi.

    > [!NOTE]
    > Il tipo di algoritmo fa riferimento a' classificazione a due classi ',' classificazione multiclasse ',' regressione ',' clustering ' in ' Machine Learning algoritmi '. 

3. Inviare la pipeline per generare i punteggi di valutazione.

## <a name="results"></a>Risultati

Dopo aver eseguito **Evaluate Model**, fare clic con il pulsante destro del mouse sul modulo e selezionare **Visualizza risultati valutazione** per visualizzare i risultati.

Se si connettono i set di dati a entrambi gli input di **Evaluate Model**, i risultati conterranno le metriche per entrambi i set di dati o per entrambi i modelli.
Il modello o i dati collegati alla porta sinistra vengono presentati per primi nel report, seguiti dalle metriche per il set di dati o dal modello collegato sulla porta destra.  

Ad esempio, l'immagine seguente rappresenta un confronto dei risultati di due modelli di clustering basati sugli stessi dati, ma con parametri diversi.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Poiché si tratta di un modello di clustering, i risultati della valutazione sono diversi rispetto a quelli di due modelli di regressione oppure a confrontati due modelli di classificazione. Tuttavia, la presentazione complessiva è la stessa. 

## <a name="metrics"></a>Metriche

In questa sezione vengono descritte le metriche restituite per i tipi specifici di modelli supportati per l'utilizzo con **Evaluate Model**:

+ [modelli di classificazione](#metrics-for-classification-models)
+ [modelli di regressione](#metrics-for-regression-models)
+ [modelli di clustering](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Metriche per i modelli di classificazione

Quando si valutano i modelli di classificazione, vengono restituite le metriche seguenti.
  
-   L' **accuratezza** misura la bontà di un modello di classificazione come la percentuale di risultati reali al numero totale di case.  
  
-   La **precisione** è la percentuale di risultati reali rispetto a tutti i risultati positivi.  
  
-   **Richiama** è la frazione di tutti i risultati corretti restituiti dal modello.  
  
-   Il **Punteggio f** viene calcolato come media ponderata della precisione e richiama tra 0 e 1, dove il valore f-Score ideale è 1.  
  
-   **AUC** misura l'area sotto la curva tracciata con i veri positivi sull'asse y e i falsi positivi sull'asse x. Questa metrica è utile perché fornisce un singolo numero che consente di confrontare modelli di tipi diversi.  
  
- La **perdita di log media** è un singolo punteggio usato per esprimere la penalità per i risultati errati. Viene calcolata come differenza tra due distribuzioni di probabilità, quella vera e quella nel modello.  
  
- La **perdita dei log di training** è un singolo punteggio che rappresenta il vantaggio del classificatore su una stima casuale. La perdita di log misura l'incertezza del modello confrontando le probabilità che restituisce ai valori noti (verità) nelle etichette. Si desidera ridurre al minimo la perdita di log per il modello nel suo complesso.

### <a name="metrics-for-regression-models"></a>Metriche per i modelli di regressione
 
Le metriche restituite per i modelli di regressione sono progettate per stimare la quantità di errori.  Un modello viene considerato adatto ai dati anche se la differenza tra i valori osservati e quelli stimati è ridotta. Tuttavia, l'analisi del modello dei residui (la differenza tra un punto stimato e il valore effettivo corrispondente) può indicare una grande quantità di potenziali distorsioni nel modello.  
  
 Per la valutazione dei modelli di regressione vengono restituite le metriche seguenti.
  
- **Errore assoluto medio (Mae)** consente di misurare la distanza tra le stime e i risultati effettivi; un punteggio più basso è quindi migliore.  
  
- **Radice errore quadratico medio (valori RMSE)** crea un singolo valore che riepiloga l'errore nel modello. Grazie alla quadratura della differenza, la metrica ignora la differenza tra la stima e la stima.  
  
- **Errore assoluto relativo (Rae)** è la differenza assoluta relativa tra i valori previsti ed effettivi; relativa perché la differenza media è divisa per la media aritmetica.  
  
- L'errore **quadratico relativo (RSE)** normalizza in modo analogo l'errore quadrato totale dei valori stimati dividendo per l'errore quadrato totale dei valori effettivi.  
  

  
- Il **coefficiente di determinazione**, spesso definito R<sup>2</sup>, rappresenta la potenza predittiva del modello come valore compreso tra 0 e 1. Zero indica che il modello è casuale (non spiega niente); 1 indica una soluzione perfetta. Tuttavia, è consigliabile usare l'attenzione per interpretare i valori di R<sup>2</sup> , in quanto i valori bassi possono essere completamente normali e i valori elevati possono essere sospetti.

###  <a name="metrics-for-clustering-models"></a>Metriche per i modelli di clustering

Poiché i modelli di clustering differiscono significativamente dai modelli di classificazione e regressione in molti aspetti, [Evaluate Model](evaluate-model.md) restituisce anche un set di statistiche diverso per i modelli di clustering.  
  
 Le statistiche restituite per un modello di clustering descrivono il numero di punti dati assegnati a ogni cluster, la quantità di separazione tra i cluster e il modo in cui i punti dati vengono raggruppati all'interno di ogni cluster.  
  
 Le statistiche per il modello di clustering vengono calcolate in base all'intero set di dati, con righe aggiuntive contenenti le statistiche per ogni cluster.  
  
Per la valutazione dei modelli di clustering vengono restituite le metriche seguenti.
    
-   I punteggi nella colonna, la **distanza media da un altro centro**, rappresentano la chiusura, in media, di ogni punto del cluster verso il centroidi di tutti gli altri cluster.   

-   I punteggi della colonna, ovvero la **distanza media dal centro cluster**, rappresentano la chiusura di tutti i punti di un cluster al centro del cluster.  
  
-   Nella colonna **numero di punti** è indicato il numero di punti dati assegnati a ogni cluster, insieme al numero complessivo totale di punti dati in qualsiasi cluster.  
  
     Se il numero di punti dati assegnati ai cluster è inferiore al numero totale di punti dati disponibili, significa che non è stato possibile assegnare i punti dati a un cluster.  
  
-   I punteggi nella colonna, **distanza massima da cluster Center**, rappresentano la somma delle distanze tra ogni punto e il baricentro del cluster del punto.  
  
     Se questo numero è elevato, può indicare che il cluster è ampiamente distribuito. È consigliabile esaminare questa statistica insieme alla **distanza media da cluster Center** per determinare la distribuzione del cluster.   

-   Il Punteggio di **valutazione combinato** nella parte inferiore di ogni sezione dei risultati elenca i punteggi medi per i cluster creati in quel particolare modello.  
  

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 