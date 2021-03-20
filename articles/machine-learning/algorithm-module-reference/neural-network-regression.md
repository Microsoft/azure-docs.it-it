---
title: 'Regressione della rete neurale: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione della rete neurale in Azure Machine Learning per creare un modello di regressione usando un algoritmo di rete neurale personalizzabile.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 403576454615effeb53651b51679681422b08e9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90890361"
---
# <a name="neural-network-regression-module"></a>Modulo di regressione della rete neurale

*Crea un modello di regressione usando un algoritmo di rete neurale*  
  
 Categoria: Machine Learning/inizializzare il modello/regressione
  
## <a name="module-overview"></a>Panoramica del modulo  

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per creare un modello di regressione usando un algoritmo di rete neurale personalizzabile.
  
 Anche se le reti neurali sono ampiamente note per l'uso di problemi complessi di formazione e modellazione, ad esempio riconoscimento di immagini, sono facilmente soggetti a problemi di regressione. Qualsiasi classe di modelli statistici può definirsi rete neurale se usa pesi adattivi e può definire in maniera approssimativa funzioni non lineari dei relativi input. Pertanto la regressione della rete neurale è adatta a problemi in cui un modello di regressione più tradizionale non può contenere una soluzione.
  
 La regressione della rete neurale è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con tag*, che include una colonna di etichetta. Poiché un modello di regressione effettua stime su un valore numerico, la colonna di etichetta deve essere un tipo di dati numerico.  
  
 Per eseguire il training del modello, è possibile fornire il modello e il set di dati con tag come input per il [training del modello](./train-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi esempi di input.  
  
## <a name="configure-neural-network-regression"></a>Configurare la regressione della rete neurale 

È possibile personalizzare ampiamente le reti neurali. Questa sezione descrive come creare un modello usando due metodi:
  
+ [Creare un modello di rete neurale usando l'architettura predefinita](#bkmk_DefaultArchitecture)  
  
    Se si accetta l'architettura di rete neurale predefinita, utilizzare il riquadro **Proprietà** per impostare i parametri che controllano il comportamento della rete neurale, ad esempio il numero di nodi nel livello nascosto, la velocità di apprendimento e la normalizzazione.

    Se non si ha familiarità con le reti neurali, iniziare da qui. Il modulo supporta molte personalizzazioni, nonché l'ottimizzazione del modello, senza una conoscenza approfondita delle reti neurali. 

+ Definire un'architettura personalizzata per una rete neurale 

    Utilizzare questa opzione se si desidera aggiungere ulteriori livelli nascosti oppure personalizzare completamente l'architettura di rete, le relative connessioni e le funzioni di attivazione.
    
    Questa opzione è consigliata se si ha già una certa familiarità con le reti neurali. Usare il linguaggio NET # per definire l'architettura di rete.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a> Creare un modello di rete neurale usando l'architettura predefinita

1.  Aggiungere il modulo di **regressione della rete neurale** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo in **Machine Learning**, **Initialize**, nella categoria **regressione** . 
  
2. Indicare come si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: scegliere questa opzione se si conosce già il modo in cui si vuole configurare il modello.

    -   **Intervallo parametri**: selezionare questa opzione se non si è certi dei parametri migliori e si vuole eseguire uno sweep di parametri. Selezionare un intervallo di valori di cui eseguire l'iterazione e l' [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) esegue l'iterazione su tutte le combinazioni possibili delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.   

3.  In **specifica livello nascosto** selezionare **case con connessione completa**. Questa opzione consente di creare un modello utilizzando l'architettura di rete neurale predefinita, per un modello di regressione della rete neurale, con questi attributi:  
  
    + La rete ha esattamente un livello nascosto.
    + Il livello di output è completamente connesso al livello nascosto e il livello nascosto è completamente connesso al livello di input.
    + Il numero di nodi nel livello nascosto può essere impostato dall'utente (il valore predefinito è 100).  
  
    Poiché il numero di nodi nel livello di input è determinato dal numero di funzioni nei dati di training, in un modello di regressione può essere presente un solo nodo nel livello di output.  
  
4. Per **numero di nodi nascosti**, digitare il numero di nodi nascosti. Il valore predefinito è un livello nascosto con nodi 100. Questa opzione non è disponibile se si definisce un'architettura personalizzata usando NET #.
  
5.  Per la **velocità di apprendimento**, digitare un valore che definisce il passaggio effettuato a ogni iterazione, prima della correzione. Un valore superiore per la velocità di apprendimento può provocare una convergenza più rapida del modello, ma può eccedere i valori minimi locali.

6.  Per **numero di iterazioni di apprendimento** specificare il numero massimo di volte in cui l'algoritmo elabora i case di training.


8.  Per **il momento**, digitare un valore da applicare durante l'apprendimento come peso nei nodi delle iterazioni precedenti.

10. Selezionare l'opzione, **esempi shuffle**, per modificare l'ordine dei case tra le iterazioni. Se si deseleziona questa opzione, i case vengono elaborati esattamente nello stesso ordine ogni volta che si esegue la pipeline.
  
11. Per il valore di **inizializzazione del numero casuale**, è possibile digitare facoltativamente un valore da usare come valore di inizializzazione. La specifica di un valore di inizializzazione è utile quando si desidera garantire la ripetibilità tra le esecuzioni della stessa pipeline.
  
13. Connettere un set di dati di training ed eseguire il training del modello:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, connettere un set di dati con tag e il modulo [Train Model](train-model.md) .  
  
    + Se si imposta la **modalità di creazione dell'allenatore** sull'intervallo di **parametri**, connettere un set di dati con tag ed eseguire il training del modello usando gli [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se si passa un intervallo di parametri a [Training Model](train-model.md), viene utilizzato solo il valore predefinito nell'elenco di parametri singoli.  
    > 
    > Se si passa un singolo set di valori di parametro al modulo [Tune Model iperparameters](tune-model-hyperparameters.md) , quando si prevede un intervallo di impostazioni per ogni parametro, i valori vengono ignorati e vengono usati i valori predefiniti per lo Learner.  
    > 
    > Se si seleziona l'opzione **Parameter range** e si immette un solo valore per qualsiasi parametro, il singolo valore specificato viene usato durante lo sweep, anche se altri parametri cambiano in un intervallo di valori.  
  
   
14. Inviare la pipeline.  

## <a name="results"></a>Risultati

Al termine del training:

- Per salvare uno snapshot del modello sottoposto a training, selezionare la scheda **output** nel riquadro di destra del modulo **Train Model** . Selezionare l'icona **registra set di dati** per salvare il modello come modulo riutilizzabile.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 