---
title: 'Esercitazione: Distribuire un modello di Machine Learning con la finestra di progettazione'
titleSuffix: Azure Machine Learning
description: Questa esercitazione mostra come creare una soluzione di analisi predittiva nella finestra di progettazione di Azure Machine Learning (anteprima). Eseguire il training, assegnare punteggi e distribuire un modello di Machine Learning usando moduli con trascinamento della selezione.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: c3ca37fd47b6551a95f9a491053ec7863acd1eeb
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389393"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Esercitazione: Distribuire un modello di Machine Learning con la finestra di progettazione (anteprima)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

È possibile distribuire il modello predittivo sviluppato nella [prima parte dell'esercitazione](tutorial-designer-automobile-price-train-score.md) per offrire ad altri la possibilità di usarlo. Nella prima parte è stato eseguito il training del modello. Ora è il momento di generare nuove stime in base all'input dell'utente. In questa parte dell'esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una pipeline di inferenza in tempo reale.
> * Creare un cluster di inferenza.
> * Distribuire l'endpoint in tempo reale.
> * Testare l'endpoint in tempo reale.

## <a name="prerequisites"></a>Prerequisiti

Completare [la prima parte dell'esercitazione](tutorial-designer-automobile-price-train-score.md) per apprendere come eseguire il training e assegnare un punteggio a un modello di Machine Learning nella finestra di progettazione.

## <a name="create-a-real-time-inference-pipeline"></a>Creare una pipeline di inferenza in tempo reale

Per distribuire la pipeline, è necessario prima convertire la pipeline di training in una pipeline di inferenza in tempo reale. Questo processo rimuove i moduli di training e aggiunge input e output del servizio Web per gestire le richieste.

### <a name="create-a-real-time-inference-pipeline"></a>Creare una pipeline di inferenza in tempo reale

1. Sopra il canvas della pipeline selezionare **Create inference pipeline** > **Real-time inference pipeline** (Crea pipeline di inferenza > Pipeline di inferenza in tempo reale).

    ![Screenshot che mostra dove trovare il pulsante per creare la pipeline](./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png)

    La pipeline dovrebbe ora avere un aspetto simile al seguente: 

   ![Screenshot della configurazione prevista della pipeline dopo la preparazione per la distribuzione](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Quando si seleziona **Create inference pipeline** accadono diverse cose:
    
    * Il modello sottoposto a training viene archiviato come modulo **Dataset** (Set di dati) nel riquadro dei moduli. È possibile trovarlo in **My Datasets** (Set di dati personali).
    * I moduli di training, come **Train Model** (Training modello) e **Split Data** (Divisione dati), vengono rimossi.
    * Il modello con training salvato viene aggiunto nuovamente alla pipeline.
    * Vengono aggiunti i moduli **Web Service Input** (Input servizio Web) e **Web Service Output** (Output servizio Web). Questi moduli mostrano il punto in cui i dati dell'utente vengono immessi nella pipeline e il punto in cui vengono restituiti.

    > [!NOTE]
    > Per impostazione predefinita, **Input servizio Web** prevede lo stesso schema di dati dei dati di training usati per creare la pipeline predittiva. In questo scenario il prezzo è incluso nello schema. Tuttavia, non viene usato come fattore durante la previsione.
    >

1. Selezionare **Submit** (Invia) e usare la stessa destinazione di calcolo e lo stesso esperimento usati nella prima parte.

    La prima volta, l'esecuzione della pipeline potrebbe richiedere fino a 20 minuti. Le impostazioni di calcolo predefinite prevedono una dimensione minima del nodo pari a 0, il che significa che la finestra di progettazione deve allocare risorse dopo l'inattività. Le esecuzioni ripetute della pipeline richiederanno meno tempo, perché le risorse di calcolo sono già allocate. Inoltre, la finestra di progettazione usa i risultati memorizzati nella cache per ogni modulo per migliorare ulteriormente l'efficienza.

1. Selezionare **Distribuisci**.

## <a name="create-an-inferencing-cluster"></a>Creare un cluster di inferenza

Nella finestra di dialogo visualizzata è possibile selezionare uno dei cluster del servizio Azure Kubernetes esistenti in cui distribuire il modello. Se non si ha un cluster del servizio Azure Kubernetes, seguire questa procedura per crearne uno.

1. Selezionare **Compute** (Calcolo) nella finestra di dialogo visualizzata per passare alla pagina **Compute**.

1. Sulla barra multifunzione di spostamento selezionare **Inference Clusters** >  **+ New** (Cluster di inferenza > Nuovo).

    ![Screenshot che mostra come passare al riquadro del nuovo cluster di inferenza](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. Nel riquadro del cluster di inferenza configurare un nuovo servizio Kubernetes.

1. Immettere *aks-compute* in **Compute name** (Nome del calcolo).
    
1. Selezionare un'area vicina disponibile per **Region** (Area).

1. Selezionare **Create** (Crea).

    > [!NOTE]
    > La creazione di un nuovo servizio Azure Kubernetes richiede circa 15 minuti. È possibile controllare lo stato del provisioning nella pagina **Inference Clusters** (Cluster di inferenza)
    >

## <a name="deploy-the-real-time-endpoint"></a>Distribuire l'endpoint in tempo reale

Al termine del provisioning del servizio Azure Kubernetes, tornare alla pipeline di inferenza in tempo reale per completare la distribuzione.

1. Selezionare **Deploy** (Distribuisci) sopra il canvas.

1. Selezionare **Deploy new real-time endpoint** (Distribuisci nuovo endpoint in tempo reale). 

1. Selezionare il cluster del servizio Azure Kubernetes creato.

1. Selezionare **Distribuisci**.

    ![Screenshot che mostra come configurare un nuovo endpoint in tempo reale](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    Al termine della distribuzione verrà visualizzata una notifica di esito positivo sopra il canvas. L'operazione potrebbe richiedere qualche minuto.

## <a name="test-the-real-time-endpoint"></a>Testare l'endpoint in tempo reale

Una volta completata la distribuzione, è possibile testare l'endpoint in tempo reale passando alla pagina **Endpoints**.

1. Nella pagina **Endpoints** selezionare l'endpoint appena distribuito.

    ![Screenshot che mostra la scheda degli endpoint in tempo reale con l'endpoint appena creato evidenziato](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Selezionare **Test**.

1. È possibile immettere manualmente i dati di test oppure usare i dati di esempio compilati automaticamente e selezionare **Test**.

    Il portale invia una richiesta di test all'endpoint e mostra i risultati. Anche se generato, il valore del prezzo per i dati di input non viene usato per generare il valore della stima.

    ![Screenshot che mostra come testare l'endpoint in tempo reale con l'etichetta con punteggio del prezzo evidenziata](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati i principali passaggi per creare, distribuire e usare un modello di Machine Learning nella finestra di progettazione. Per altre informazioni su come usare la finestra di progettazione per risolvere altri tipi di problemi, vedere le altre pipeline di esempio.

> [!div class="nextstepaction"]
> [Esempi della finestra di progettazione](samples-designer.md)
