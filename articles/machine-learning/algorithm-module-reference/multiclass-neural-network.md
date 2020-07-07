---
title: 'Rete neurale multiclasse: informazioni di riferimento sui moduli'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di rete neurale multiclasse in Azure Machine Learning per creare un modello di rete neurale che può essere usato per stimare una destinazione con più valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: c7fb02681936f575153f23ac60db43cc75541075
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82137757"
---
# <a name="multiclass-neural-network-module"></a>Modulo di rete neurale multiclasse

Questo articolo descrive un modulo disponibile nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un modello di rete neurale da usare per stimare una destinazione contenente più valori. 

Ad esempio, le reti neurali di questo tipo possono essere utilizzate in attività complesse di visione artificiale, ad esempio il riconoscimento di cifre o lettere, la classificazione dei documenti e il riconoscimento del modello.

La classificazione con reti neurali è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con tag* che include una colonna di etichetta.

Per eseguire il training del modello, è possibile fornire il modello e il set di dati con tag come input per il [training del modello](./train-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi esempi di input.  

## <a name="about-neural-networks"></a>Informazioni sulle reti neurali

Una rete neurale è un set di livelli interconnessi. Gli input sono il primo livello e sono connessi a un livello di output da un grafico aciclici costituito da bordi ponderati e nodi.

Tra i livelli di input e di output è possibile inserire più livelli nascosti. Con uno solo o più livelli nascosti, è possibile eseguire facilmente la maggior parte delle attività di stima. Tuttavia, la ricerca recente ha dimostrato che le reti neurali profonde (DNN) con molti livelli possono essere efficaci in attività complesse, ad esempio il riconoscimento vocale o di immagine. I livelli successivi vengono usati per modellare livelli crescenti di profondità semantica.

La relazione tra input e output viene appresa dal training della rete neurale sui dati di input. La direzione del grafico prosegue dagli input attraverso il livello nascosto e il livello di output. Tutti i nodi di un livello sono connessi con i bordi ponderati ai nodi nel livello successivo.

Per calcolare l'output della rete per un input specifico, viene calcolato un valore in ogni nodo nei livelli nascosti e nel livello di output. Il valore viene impostato calcolando la somma ponderata dei valori dei nodi del livello precedente. Una funzione di attivazione viene quindi applicata a tale somma ponderata.

## <a name="configure-multiclass-neural-network"></a>Configurare la rete neurale multiclasse

1. Aggiungere il modulo di **rete neurale multiclasse** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo in **Machine Learning**, **Initialize**, nella categoria **classificazione** .

2. **Crea modalità trainer**: usare questa opzione per specificare come si desidera eseguire il training del modello:

    - **Singolo parametro**: scegliere questa opzione se si conosce già il modo in cui si vuole configurare il modello.

    - **Intervallo parametri**: selezionare questa opzione se non si è certi dei parametri migliori e si vuole eseguire uno sweep di parametri. Selezionare un intervallo di valori di cui eseguire l'iterazione e l' [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) esegue l'iterazione su tutte le combinazioni possibili delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.  

3. **Specifica livello nascosto**: selezionare il tipo di architettura di rete da creare.

    - **Caso di connessione completa**: selezionare questa opzione per creare un modello usando l'architettura di rete neurale predefinita. Per i modelli di rete neurale multiclasse, i valori predefiniti sono i seguenti:

        - Un livello nascosto
        - Il livello di output è completamente connesso al livello nascosto.
        - Il livello nascosto è completamente connesso al livello di input.
        - Il numero di nodi nel livello di input è determinato dal numero di funzioni nei dati di training.
        - Il numero di nodi nel livello nascosto può essere impostato dall'utente. Il valore predefinito è 100.
        - Il numero di nodi nel livello di output dipende dal numero di classi.
  
   

5. **Numero di nodi nascosti**: questa opzione consente di personalizzare il numero di nodi nascosti nell'architettura predefinita. Digitare il numero di nodi nascosti. Il valore predefinito è un livello nascosto con nodi 100.

6. **Velocità di apprendimento**: definire le dimensioni del passaggio effettuato a ogni iterazione, prima della correzione. Un valore più grande per la velocità di apprendimento può comportare una maggiore velocità di convergenza del modello, ma è possibile che venga superata la minima locale.

7. **Numero di iterazioni di apprendimento**: specificare il numero massimo di volte in cui l'algoritmo deve elaborare i case di training.

8. **Il diametro iniziale dei pesi di apprendimento**: specificare i pesi del nodo all'inizio del processo di apprendimento.

9. **Momentum**: specificare un peso da applicare durante l'apprendimento ai nodi dalle iterazioni precedenti.
  
11. **Esempi di riproduzione casuale**: selezionare questa opzione per riprodurre in modo casuale i casi tra le iterazioni.

    Se si deseleziona questa opzione, i case vengono elaborati esattamente nello stesso ordine ogni volta che si esegue la pipeline.

12. Valore di **inizializzazione numero casuale**: digitare un valore da usare come valore di inizializzazione, se si vuole garantire la ripetibilità tra le esecuzioni della stessa pipeline.

14. Eseguire il training del modello:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, connettere un set di dati con tag e il modulo [Train Model](train-model.md) .  
  
    + Se si imposta la **modalità di creazione dell'allenatore** sull'intervallo di **parametri**, connettere un set di dati con tag ed eseguire il training del modello usando gli [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se si passa un intervallo di parametri a [Training Model](train-model.md), viene utilizzato solo il valore predefinito nell'elenco di parametri singoli.  
    > 
    > Se si passa un singolo set di valori di parametro al modulo [Tune Model iperparameters](tune-model-hyperparameters.md) , quando si prevede un intervallo di impostazioni per ogni parametro, i valori vengono ignorati e vengono usati i valori predefiniti per lo Learner.  
    > 
    > Se si seleziona l'opzione **Parameter range** e si immette un solo valore per qualsiasi parametro, il singolo valore specificato viene usato durante lo sweep, anche se altri parametri cambiano in un intervallo di valori.  
  

## <a name="results"></a>Risultati

Al termine del training:

- Per salvare uno snapshot del modello sottoposto a training, selezionare la scheda **output** nel riquadro di destra del modulo **Train Model** . Selezionare l'icona **registra set di dati** per salvare il modello come modulo riutilizzabile.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 