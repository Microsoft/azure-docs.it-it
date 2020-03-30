---
title: Preparare il modello per la distribuzione
titleSuffix: ML Studio (classic) - Azure
description: Come preparare il modello sottoposto a training per la distribuzione come servizio Web convertendo l'esperimento di formazione di Machine Learning Studio (classico) in un esperimento predittivo.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/28/2017
ms.openlocfilehash: 061c340f8c4952d5a0f2a3873f7475e4f733c290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204512"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Come preparare il modello per la distribuzione in Azure Machine Learning Studio (classico)How to prepare your model for deployment in Azure Machine Learning Studio (classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (classico) offre gli strumenti necessari per sviluppare un modello di analisi predittiva e quindi renderlo operativo distribuendolo come servizio Web di Azure.Azure Machine Learning Studio (classic) gives you the tools you need to develop a predictive analytics model and then operationalize it by deploying it as an Azure web service.

A tale scopo, si usa Studio (classico) per creare un esperimento, denominato esperimento di *training,* in cui eseguire il training, il punteggio e la modifica del modello. Quando si è soddisfatti del risultato, è possibile procedere alla distribuzione del modello convertendo l'esperimento di training in un *esperimento predittivo* configurato in modo da assegnare un punteggio ai dati utente.

È possibile visualizzare un esempio di questo processo [nell'Esercitazione 1: Prevedere il rischio](tutorial-part1-credit-risk.md)di credito .

Questo articolo contiene informazioni approfondite su come convertire un esperimento di training in un esperimento predittivo e successivamente distribuirlo. La comprensione di questi dettagli è fondamentale per imparare a configurare il modello distribuito in modo da renderlo più efficace.



## <a name="overview"></a>Panoramica 

Il processo di conversione di un esperimento di training in un esperimento predittivo prevede tre passaggi:

1. Sostituire i moduli di algoritmi di Machine Learning con il modello di training.
2. Organizzare l'esperimento solo per i moduli necessari alla valutazione. Un esperimento di training include alcuni moduli che sono necessari per il training, ma che non lo saranno più al termine del training.
3. Definire in che modo il modello accetterà i dati dall'utente del servizio Web e quali dati restituirà.

> [!TIP]
> Durante l'esperimento di training, è stato eseguito il training ed è stato assegnato un punteggio al modello utilizzando i propri dati. In seguito alla distribuzione, tuttavia, gli utenti invieranno nuovi dati al modello che, a sua volta, restituirà i risultati della previsione. Quando dunque si converte l'esperimento di training in un esperimento predittivo per prepararlo per la distribuzione, è importante considerare in che modo il modello verrà utilizzato da altri utenti.
> 
> 

## <a name="set-up-web-service-button"></a>Pulsante Set Up Web Service
Dopo aver eseguito l'esperimento (fare clic su **RUN** (ESEGUI) nella parte inferiore dell'area di disegno dell'esperimento), fare clic sul pulsante **Set Up Web Service** (Configura servizio Web) scegliendo l'opzione **Predictive Web Service** (Servizio Web predittivo). **Set Up Web Service** (Configura servizio Web) esegue automaticamente i tre passaggi necessari per convertire l'esperimento di training in un esperimento predittivo:

1. Salva il modello di training nella sezione dei **modelli di training** della tavolozza del modulo (a sinistra dell'area di disegno dell'esperimento). Sostituisce quindi l'algoritmo di Machine Learning e i moduli [Train Model][train-model] (Modello di training) con il modello di training salvato.
2. Analizza l'esperimento e rimuove i moduli chiaramente usati solo per il training e non più necessari.
3. Inserisce moduli di _input_ e di _output del servizio Web_ in posizioni predefinite nell'esperimento (questi moduli accettano e restituiscono i dati utente).

Ad esempio, il seguente esperimento esegue il training di un modello di albero delle decisioni incrementato a due classi utilizzando dati di classificazione di esempio:

![esperimento di training](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

I moduli nell'esperimento eseguono fondamentalmente quattro diverse funzioni:

![Funzioni del modulo](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Quando si converte l'esperimento di training in un esperimento predittivo, alcuni di questi moduli non sono più necessari o hanno a questo punto uno scopo diverso:

* **Data** (Dati): i dati in questo set di dati di esempio non vengono usati durante l'assegnazione di un punteggio. L'utente del servizio Web fornisce i dati da classificare. Tuttavia, i metadati di questo set di dati, ad esempio i tipi di dati, vengono usati dal modello sottoposto a training. È quindi necessario mantenere il set di dati nell'esperimento predittivo perché possa fornire questi metadati.

* **Prep**: in base ai dati utente che verranno inviati per la classificazione, i moduli potrebbero non essere necessari per elaborare i dati in ingresso. Il pulsante **Set Up Web Service** (Configura servizio Web) non agisce su questi moduli, pertanto è necessario decidere come gestirli.
  
    In questo esempio alcuni valori nel set di dati di esempio potrebbero essere mancanti, pertanto è stato aggiunto un modulo [Clean Missing Data][clean-missing-data] (Pulisci dati mancanti) per gestirli. Il set di dati di esempio include, inoltre, colonne che non sono necessarie per il training del modello. È stato pertanto incluso un modulo [Select columns in Dataset][select-columns] (Seleziona colonne nel set di dati) per escludere le colonne aggiuntive dal flusso di dati. Se si è certi che i dati inviati per la classificazione tramite il servizio Web non presentino valori mancanti, è possibile rimuovere il modulo [Clean Missing Data][clean-missing-data]. Poiché tuttavia il modulo [Select Columns in Dataset][select-columns] (Seleziona colonne nel set di dati) consente di definire le colonne di dati previste dal modello di training, è necessario mantenerlo.

* **Train** (Training): questi moduli vengono usati per eseguire il training del modello. Quando si fa clic su **Set Up Web Service** (Configura servizio Web), questi moduli vengono sostituiti con un singolo modulo contenente il modello sottoposto a training. Il nuovo modulo viene salvato nella sezione **Trained Models** (Modelli sottoposti a training) della tavolozza dei moduli.

* **Score** (Punteggio): in questo esempio il modulo [Split Data][split] (Dividi dati) viene usato per suddividere il flusso di dati in dati di test e dati di training. Poiché nell'esperimento predittivo non si esegue più il training, è possibile rimuovere [Split Data][split] (Dividi dati). In modo analogo, il secondo modulo [Score Model][score-model] (Assegna punteggio al modello) e il modulo [Evaluate Model][evaluate-model] (Valuta modello) vengono usati per confrontare i risultati dei dati di test, pertanto questi moduli non sono necessari nell'esperimento predittivo. Il modulo rimanente [Score Model][score-model] è però necessario per restituire un risultato relativo all'assegnazione del punteggio tramite il servizio Web.

Ecco come appare l'esempio dopo aver fatto clic su **Set Up Web Service**:

![Esperimento predittivo convertito](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Le operazioni eseguite da **Set Up Web Service** (Configura sevizio Web) potrebbero essere sufficienti per preparare l'esperimento per la distribuzione come servizio Web. Tuttavia, potrebbe essere necessario eseguire ulteriori operazioni specifiche per l'esperimento.

### <a name="adjust-input-and-output-modules"></a>Regolazione dei moduli di input e output
Nell'esperimento di training è stato utilizzato un set di dati di training e quindi sono state eseguite delle operazioni di elaborazione per ottenere i dati in un formato utile all'algoritmo Machine Learning necessario. Se i dati che si prevede di ricevere tramite il servizio Web non richiedono questo tipo di elaborazione, è possibile ignorarli. A questo scopo, connettere l'output del modulo **Web service input** (Input servizio Web) a un altro modulo nell'esperimento. I dati dell'utente verranno inviati in questa posizione nel modello.

Per impostazione predefinita, ad esempio, **Set Up Web Service** (Configura servizio Web) inserisce il modulo **Web service input** (Input del servizio Web) all'inizio del flusso di dati, come illustrato nella figura precedente. È tuttavia possibile posizionare manualmente **Web service input** (Input servizio Web) dopo i moduli di elaborazione dei dati:

![Spostamento del modulo web service input](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

I dati di input forniti tramite il servizio Web ora passano direttamente al modulo Score Model senza pre-elaborazione.

In modo analogo, per impostazione predefinita, **Set Up Web Service** inserisce il modulo di output del servizio Web in fondo al flusso di dati. In questo esempio il servizio Web restituisce all'utente l'output del modulo [Score Model][score-model] (Assegna punteggio al modello) che include il vettore dei dati di input completo, oltre ai risultati dell'assegnazione del punteggio.
Se però si preferisce restituire un valore diverso, è possibile aggiungere moduli aggiuntivi prima del modulo **Web service output** (Output servizio Web). 

Per restituire, ad esempio, solo i risultati dell'assegnazione del punteggio e non l'intero vettore dei dati di input, aggiungere un modulo [Select Columns in Dataset][select-columns] (Seleziona colonne nel set di dati) per escludere tutte le colonne ad eccezione dei risultati dell'assegnazione del punteggio. Spostare quindi il modulo **Web service output** (Output servizio Web) nell'output del modulo [Select Columns in Dataset][select-columns] (Seleziona colonne nel set di dati). L'esperimento è simile al seguente:

![Spostamento del modulo web service output](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Aggiungere o rimuovere i moduli di elaborazione dati aggiuntivi
Se presenti nell'esperimento, è possibile rimuove i moduli di cui si è certi che non saranno utili per la classificazione. Ad esempio, poiché il modulo **Web service input** (Input servizio Web) è stato spostato in un punto successivo ai moduli di elaborazione dei dati, è possibile rimuovere il modulo [Clean Missing Data][clean-missing-data] (Pulisci dati mancanti) dall'esperimento predittivo.

L'esperimento predittivo ora appare come illustrato di seguito:

![Rimozione del modulo aggiuntivo](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Aggiungere parametri facoltativi al servizio Web
In alcuni casi, è possibile consentire all'utente del servizio Web di modificare il comportamento dei moduli quando si accede al servizio. *I parametri del servizio Web* consentono di eseguire questa operazione.

Un esempio comune è la configurazione di un modulo [Import Data][import-data] (Importa dati) per consentire all'utente del servizio Web distribuito di specificare un'origine dati diversa quando si accede al servizio Web oppure la configurazione di un modulo [Export Data][export-data] (Esporta dati) in modo che sia possibile specificare una destinazione differente.

È possibile definire i parametri del servizio Web e associarli a uno o più parametri di modulo e specificare se sono obbligatori o facoltativi. Effettuando l'accesso al servizio e modificando adeguatamente le azioni del modulo, l'utente del servizio Web fornisce valori per tali parametri.

Per ulteriori informazioni sui parametri del servizio Web e su come usarli, vedere [Usare i parametri del servizio Web di Azure Machine Learning][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Distribuire l'esperimento predittivo come servizio Web
Ora che l'esperimento predittivo è stato sufficientemente preparato, è possibile distribuirlo come servizio Web di Azure. Usando il servizio Web, gli utenti possono inviare dati al modello che poi restituirà le stime.

Per altre informazioni sul processo di distribuzione completo, vedere [Distribuire un servizio Web di Azure Machine Learning][deploy].

[deploy]: deploy-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
