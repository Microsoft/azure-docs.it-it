---
title: Usare i parametri della pipeline per ripetere il training dei modelli nella finestra di progettazione
titleSuffix: Azure Machine Learning
description: Ripetere il training dei modelli con pipeline pubblicate e parametri della pipeline in Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 6efb0f095f8a157f723a3b7c0c2b229546ebb36b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97708467"
---
# <a name="use-pipeline-parameters-to-retrain-models-in-the-designer"></a>Usare i parametri della pipeline per ripetere il training dei modelli nella finestra di progettazione


Questo articolo illustra come usare Azure Machine Learning Designer per ripetere il training di un modello di apprendimento automatico usando i parametri della pipeline. Verranno usate le pipeline pubblicate per automatizzare il flusso di lavoro e impostare i parametri per eseguire il training del modello con i nuovi dati. I parametri della pipeline consentono di riutilizzare le pipeline esistenti per diversi processi.  

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Eseguire il training di un modello di Machine Learning.
> * Creare un parametro della pipeline.
> * Pubblicare la pipeline di training.
> * Ripetere il training del modello con nuovi parametri.

## <a name="prerequisites"></a>Prerequisiti

* Area di lavoro di Azure Machine Learning
* Completare la parte 1 di questa serie di procedure, [trasformare i dati nella finestra di progettazione](how-to-designer-transform-data.md)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

Questo articolo presuppone inoltre che l'utente abbia una certa conoscenza della creazione di pipeline nella finestra di progettazione. Per un'introduzione guidata, eseguire l'[esercitazione](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Pipeline di esempio

La pipeline usata in questo articolo è una versione modificata di una stima del [reddito](samples-designer.md#classification) della pipeline di esempio nella Home page della finestra di progettazione. La pipeline usa il modulo [Import Data](algorithm-module-reference/import-data.md) (Importare dati) anziché il set di dati di esempio per illustrare come eseguire il training dei modelli con i propri dati.

![Screenshot che mostra la pipeline di esempio modificata con un riquadro che evidenzia il modulo Import Data (Importare dati)](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Creare un parametro della pipeline

I parametri della pipeline vengono usati per creare pipeline versatili che possono essere inviate nuovamente in un secondo momento con valori di parametro variabili. Alcuni scenari comuni sono l'aggiornamento di set di impostazioni o alcuni parametri ipertestuali per la ripetizione del training. Creare i parametri della pipeline per impostare dinamicamente le variabili durante il runtime. 

I parametri della pipeline possono essere aggiunti ai parametri delle origini dati o dei moduli in una pipeline. Quando la pipeline viene inviata nuovamente, è possibile specificare i valori di questi parametri.

Per questo esempio, il percorso dei dati di training verrà modificato da un valore fisso a un parametro in modo che sia possibile ripetere il training del modello con dati diversi. È anche possibile aggiungere altri parametri del modulo come parametri della pipeline in base al caso d'uso.

1. Selezionare il modulo **Import Data** (Importare dati).

    > [!NOTE]
    > Questo esempio usa il modulo Import Data (Importare Dati) per accedere ai dati in un archivio dati registrato. Tuttavia, se si usano modelli di accesso ai dati alternativi, è possibile seguire una procedura simile.

1. Nel riquadro dei dettagli del modulo a destra del canvas selezionare l'origine dati.

1. Immettere il percorso dei dati. È anche possibile selezionare **Browse path** (Sfoglia percorso) per esplorare l'albero dei file. 

1. Passare il mouse sul campo **Path** (Percorso) e selezionare i tre punti visualizzati sopra il campo **Path** (Percorso).

1. Selezionare **Add to pipeline parameter** (Aggiungi a parametro pipeline).

1. Specificare un nome di parametro e un valore predefinito.

   ![Screenshot che illustra come creare un parametro della pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Selezionare **Salva**.

   > [!NOTE]
   > È anche possibile scollegare un parametro module dal parametro pipeline nel riquadro dei dettagli del modulo, in modo analogo all'aggiunta di parametri della pipeline.
   >
   > È possibile esaminare e modificare i parametri della pipeline selezionando l'icona a forma di ingranaggio **Settings** (Impostazioni) accanto al titolo della bozza della pipeline. 
   >    - Dopo lo scollegamento, è possibile eliminare il parametro della pipeline nel riquadro **setings** .
   >    - È anche possibile aggiungere un parametro della pipeline nel riquadro **Impostazioni** e quindi applicarlo a un parametro del modulo.

1. Avviare l'esecuzione della pipeline.

## <a name="publish-a-training-pipeline"></a>Pubblicare una pipeline di training

Pubblicare una pipeline in un endpoint della pipeline per riutilizzare facilmente le pipeline in futuro. Un endpoint della pipeline crea un endpoint REST per richiamare la pipeline in futuro. In questo esempio, l'endpoint della pipeline consente di riutilizzare la pipeline per ripetere il training di un modello con dati diversi.

1. Selezionare **Publish** (Pubblica) sopra il canvas della finestra di progettazione.
1. Selezionare o creare un endpoint della pipeline.

   > [!NOTE]
   > È possibile pubblicare più pipeline in un singolo endpoint. A ogni pipeline di un determinato endpoint viene assegnato un numero di versione che è possibile specificare quando si chiama l'endpoint della pipeline.

1. Selezionare **Pubblica**.

## <a name="retrain-your-model"></a>Ripetere il training del modello

Ora che è disponibile una pipeline di training pubblicata, è possibile usarla per ripetere il training del modello con i nuovi dati. È possibile avviare le esecuzioni da un endpoint della pipeline dall'area di lavoro di Studio o a livello di codice.

### <a name="submit-runs-by-using-the-studio-portal"></a>Inviare le esecuzioni tramite il portale di studio

Usare la procedura seguente per inviare un endpoint della pipeline con parametri eseguito dal portale di studio:

1. Passare alla pagina **Endpoints** (Endpoint) nell'area di lavoro di Studio.
1. Selezionare la scheda **Pipeline endpoints** (Endpoint pipeline). Quindi selezionare l'endpoint della pipeline.
1. Selezionare la scheda **Published pipelines** (Pipeline pubblicate). Quindi selezionare la versione della pipeline che si vuole eseguire.
1. Selezionare **Submit** (Invia).
1. Nella finestra di dialogo di impostazione è possibile specificare i valori dei parametri per l'esecuzione. Per questo esempio, aggiornare il percorso dei dati per eseguire il training del modello usando un set di dati diverso da US.

![Screenshot che illustra come configurare l'esecuzione di una pipeline con parametri nella finestra di progettazione](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Avviare le esecuzioni usando il codice

L'endpoint REST di una pipeline pubblicata è disponibile nel pannello di panoramica. Chiamando l'endpoint è possibile ripetere il training della pipeline pubblicata.

Per eseguire una chiamata REST è necessaria un'intestazione di autenticazione di tipo bearer token OAuth 2.0. Per informazioni sulla configurazione dell'autenticazione nell'area di lavoro e sull'esecuzione di una chiamata REST con parametri, vedere [Creare una pipeline di Azure Machine Learning per l'assegnazione di punteggi batch](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare un endpoint della pipeline di training con parametri usando la finestra di progettazione.

Per la procedura dettagliata completa per distribuire un modello per eseguire stime, vedere l'[esercitazione con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md) per eseguire il training e la distribuzione di un modello di regressione.
