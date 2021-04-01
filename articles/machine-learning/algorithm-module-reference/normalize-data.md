---
title: 'Normalizza dati: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Normalize data in Azure Machine Learning per trasformare un set di dati tramite la *normalizzazione*.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: de0a23ca9dea210d91fe259b06622226549ba3b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90890449"
---
# <a name="normalize-data-module"></a>Normalizza modulo dati

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per trasformare un set di dati tramite la *normalizzazione*.

La normalizzazione è una tecnica applicata spesso come parte della preparazione dei dati per Machine Learning. L'obiettivo della normalizzazione consiste nel modificare i valori delle colonne numeriche nel set di dati in modo da usare una scala comune, senza compromettere le differenze tra gli intervalli di valori o la perdita di informazioni. La normalizzazione è necessaria anche per alcuni algoritmi per modellare correttamente i dati.

Si supponga, ad esempio, che il set di dati di input contenga una colonna con valori compresi tra 0 e 1 e un'altra colonna con valori compresi tra 10.000 e 100.000. La grande differenza nella *scala* dei numeri può causare problemi quando si tenta di combinare i valori come funzionalità durante la modellazione.

La *normalizzazione* consente di evitare questi problemi creando nuovi valori che gestiscono la distribuzione e i rapporti generali nei dati di origine, mantenendo i valori all'interno di una scala applicata in tutte le colonne numeriche utilizzate nel modello.

Questo modulo offre diverse opzioni per la trasformazione dei dati numerici:

- È possibile modificare tutti i valori in una scala 0-1 o trasformare i valori rappresentando i valori come rango percentile anziché valori assoluti.
- È possibile applicare la normalizzazione a una singola colonna o a più colonne dello stesso set di dati.
- Se è necessario ripetere la pipeline o applicare gli stessi passaggi di normalizzazione ad altri dati, è possibile salvare i passaggi come trasformazione di normalizzazione e applicarla ad altri set di dati con lo stesso schema.

> [!WARNING]
> Alcuni algoritmi richiedono di normalizzare i dati prima di eseguire il training di un modello. Altri algoritmi scalano o normalizzano autonomamente i propri dati. Pertanto, quando si sceglie un algoritmo di machine learning da usare per la compilazione di un modello predittivo, assicurarsi di esaminare i requisiti dei dati dell'algoritmo prima di applicare la normalizzazione ai dati di training.

##  <a name="configure-normalize-data"></a>Configurare i dati normalizzati

Con questo modulo è possibile applicare un solo metodo di normalizzazione alla volta. Pertanto, lo stesso metodo di normalizzazione viene applicato a tutte le colonne selezionate. Per usare metodi di normalizzazione diversi, usare una seconda istanza di **normalizzare i dati**.

1. Aggiungere il modulo **Normalize data (normalizza dati** ) alla pipeline. È possibile trovare il modulo in Azure Machine Learning, in **trasformazione dati**, nella categoria **Ridimensiona e Riduci** .

2. Connettere un set di dati che contiene almeno una colonna di tutti i numeri.

3. Usare il selettore di colonna per scegliere le colonne numeriche da normalizzare. Se non si scelgono singole colonne, per impostazione predefinita vengono incluse **tutte** le colonne di tipo numerico nell'input e lo stesso processo di normalizzazione viene applicato a tutte le colonne selezionate. 

    Questo può causare risultati anomali se si includono colonne numeriche che non devono essere normalizzate. Controllare sempre attentamente le colonne.

    Se non vengono rilevate colonne numeriche, controllare i metadati della colonna per verificare che il tipo di dati della colonna sia un tipo numerico supportato.

    > [!TIP]
    > Per assicurarsi che le colonne di un tipo specifico vengano fornite come input, provare a usare il modulo [Select Columns in DataSet](./select-columns-in-dataset.md) prima di **normalizzare i dati**.

4. **USA 0 per le colonne costanti quando** questa opzione è selezionata: selezionare questa opzione quando una colonna numerica contiene un solo valore non modificabile. In questo modo si garantisce che tali colonne non vengano utilizzate nelle operazioni di normalizzazione.

5. Dall'elenco a discesa **metodo di trasformazione** scegliere una singola funzione matematica da applicare a tutte le colonne selezionate. 
  
    - **ZScore**: converte tutti i valori in un punteggio z.
    
      I valori nella colonna vengono trasformati usando la seguente formula:  
  
      ![normalizzazione con punteggi z&#45;](media/module/aml-normalization-z-score.png)
  
      La deviazione media e quella standard vengono calcolate separatamente per ogni colonna. Viene usata la deviazione standard della popolazione.
  
    - **MinMax**: il normalizzatore min-max ridimensiona in modo lineare ogni funzionalità con l'intervallo [0,1].
    
      Questa operazione viene eseguita scalando i valori di ogni funzione in modo che il valore minimo sia 0 e dividendo per il nuovo valore massimo, che è la differenza tra i valori massimo e minimo originali.
      
      I valori nella colonna vengono trasformati usando la seguente formula:  
  
      ![normalizzazione con la funzione min&#45;max](media/module/aml-normalization-minmax.png "AML_normalization-MinMax")  
  
    - **Logistica**: i valori nella colonna vengono trasformati usando la formula seguente:

      ![formula della normalizzazione per funzione logistica](media/module/aml-normalization-logistic.png "AML_normalization logistica")  
  
    - **Lognormale**: questa opzione converte tutti i valori in una scala lognormale.
  
      I valori nella colonna vengono trasformati usando la seguente formula:
  
      ![log delle formule&#45;distribuzione normale](media/module/aml-normalization-lognormal.png "AML_normalization-lognormale")
    
      Qui μ e σ sono i parametri della distribuzione, calcolati empiricamente dai dati come stime di probabilità massima, per ogni colonna separatamente.  
  
    - **Tanh**: tutti i valori vengono convertiti in una tangente iperbolica.
    
      I valori nella colonna vengono trasformati usando la seguente formula:
    
      ![normalizzazione con funzione tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Inviare la pipeline oppure fare doppio clic sul modulo **Normalize data** e selezionare **Esegui selezione**. 

## <a name="results"></a>Risultati

Il modulo **Normalize data** genera due output:

- Per visualizzare i valori trasformati, fare clic con il pulsante destro del mouse sul modulo e scegliere **Visualizza**.

    Per impostazione predefinita, i valori vengono trasformati sul posto. Se si desidera confrontare i valori trasformati con i valori originali, utilizzare il modulo [Add columns](./add-columns.md) per ricombinare i set di impostazioni e visualizzare le colonne side-by-side.

- Per salvare la trasformazione in modo che sia possibile applicare lo stesso metodo di normalizzazione a un altro set di dati, selezionare il modulo e selezionare **registra set di dati** nella scheda **output** nel riquadro di destra.

    È quindi possibile caricare le trasformazioni salvate dal gruppo **trasformazioni** del riquadro di spostamento a sinistra e applicarle a un set di dati con lo stesso schema utilizzando [Applica trasformazione](apply-transformation.md).  


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 