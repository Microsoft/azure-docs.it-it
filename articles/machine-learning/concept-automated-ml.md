---
title: Che cos'è il Machine Learning automatico/AutoML
titleSuffix: Azure Machine Learning
description: Informazioni su come Azure Machine Learning possibile selezionare automaticamente un algoritmo e generare un modello da esso per risparmiare tempo usando i parametri e i criteri forniti per selezionare l'algoritmo migliore per il modello.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: ce51a1b25453a5bbacbd268b37f2bd21cfe37fea
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983466"
---
# <a name="what-is-automated-machine-learning-automl"></a>Che cos'è automatizzato Machine Learning (AutoML)?

Il Machine Learning automatizzato, detto anche ML o AutoML automatizzato, è il processo che consente di automatizzare le attività iterative che richiedono molto tempo per lo sviluppo di modelli di machine learning. Consente a data scientist, analisti e sviluppatori di creare modelli ML con scalabilità, efficienza e produttività elevate, garantendo al tempo stesso la qualità del modello. Il Machine Learning automatico si basa su un'innovazione della [divisione Microsoft Research](https://arxiv.org/abs/1705.05355).

Lo sviluppo tradizionale di modelli di Machine Learning prevede un uso intensivo delle risorse, che richiede una conoscenza significativa del dominio e tempi per produrre e confrontare dozzine di modelli. Grazie alla funzionalità di Machine Learning automatizzata, potrai accelerare il tempo necessario per ottenere modelli di ML pronti per la produzione con grande facilità ed efficienza.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Quando usare AutoML: classifica, regressione, & previsione

Applicare il Machine Learning automatico quando si desidera Azure Machine Learning per eseguire il training e l'ottimizzazione di un modello utilizzando la metrica di destinazione specificata. Automatizzato ML democratizza il processo di sviluppo di modelli di machine learning e consente agli utenti, indipendentemente dalla loro esperienza di data science, di identificare una pipeline di Machine Learning end-to-end per eventuali problemi.

I data scientist, gli analisti e gli sviluppatori di tutti i settori possono usare il Machine Learning automatico per:
+ Implementare soluzioni ML senza una conoscenza approfondita della programmazione
+ Risparmiare tempo e risorse
+ Utilizzare data science procedure consigliate
+ Fornire risoluzione dei problemi agile

### <a name="classification"></a>Classificazione

La classificazione è un'attività di Machine Learning comune. Classificazione è un tipo di apprendimento supervisionato in cui i modelli imparano a usare i dati di training e applicano tali informazioni ai nuovi dati. Azure Machine Learning offre featurizations in modo specifico per queste attività, ad esempio featurizers del testo Deep Neural Network per la classificazione. Altre informazioni sulle [Opzioni di conteggi](how-to-use-automated-ml-for-ml-models.md#featurization). 

L'obiettivo principale dei modelli di classificazione è quello di stimare le categorie a cui rientreranno i nuovi dati in base alle informazioni sui dati di training. Esempi di classificazione comuni includono rilevamento delle frodi, riconoscimento della grafia e rilevamento degli oggetti.  Scopri di più e Vedi un esempio di [classificazione con Machine Learning automatizzato](tutorial-train-models-with-aml.md).

Vedere esempi di classificazione e automazione di Machine Learning in questi notebook Python: [rilevamento delle frodi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [previsione del marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)e [classificazione dei dati dei newsgroup](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)

### <a name="regression"></a>Regressione
Analogamente alla classificazione, le attività di regressione sono anche un'attività di apprendimento supervisionata comune. Azure Machine Learning offre [featurizations in modo specifico per queste attività](how-to-use-automated-ml-for-ml-models.md#featurization).

Diversa dalla classificazione in cui i valori di output stimati sono categorici, i modelli di regressione stimano i valori di output numerici in base a predittori indipendenti. Nella regressione l'obiettivo è di stabilire la relazione tra le variabili del predittore indipendenti stimando il modo in cui una variabile influisca sulle altre. Ad esempio, il prezzo dell'automobile in base a funzionalità come, chilometraggio dei gas, classificazione di sicurezza e così via. Scopri di più e Vedi un esempio di [regressione con Machine Learning automatizzato](tutorial-auto-train-models.md).

Vedere esempi di regressione e Machine Learning automatizzati per le stime in questi notebook Python: [stima delle prestazioni della CPU](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Previsione delle serie temporali

La compilazione di previsioni è parte integrante di qualsiasi azienda, sia che si tratti di ricavi, scorte, vendite o richieste dei clienti. È possibile utilizzare Machine Learning Machine Learning per combinare tecniche e approcci e ottenere una previsione della serie temporale consigliata e di alta qualità. Scopri di più con questa procedura: [automatizzare Machine Learning per la previsione delle serie temporali](how-to-auto-train-forecast.md). 

Un esperimento di serie temporali automatizzato viene considerato un problema di regressione MultiVariante. I valori delle serie temporali precedenti sono "trasformati tramite pivot" per diventare dimensioni aggiuntive per il regressore insieme ad altri predittori. Questo approccio, a differenza dei metodi classici della serie temporale, ha il vantaggio di incorporare naturalmente più variabili contestuali e la relazione tra loro durante il training. Machine Learning Machine Learning apprende un singolo modello, ma spesso con rami internamente, per tutti gli elementi del set di dati e gli orizzonti di stima. Sono pertanto disponibili più dati per stimare i parametri del modello e la generalizzazione per la serie non visibile diventa possibile.

La configurazione della previsione avanzata include:
* rilevamento festività e conteggi
* serie temporali e DNN Learner (ARIMA automatico, profeta, ForecastTCN)
* molti modelli supportano il raggruppamento
* convalida incrociata di origine in sequenza
* ritardi configurabili
* funzionalità di aggregazione della finestra in sequenza


Vedere esempi di regressione e Machine Learning automatizzati per le stime in questi notebook Python: [previsioni di vendita](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [previsione della domanda](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)e [previsioni di produzione di bevande](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automl-works"></a>Funzionamento di AutoML

Durante il training, Azure Machine Learning crea una serie di pipeline in parallelo che provano diversi algoritmi e parametri. Il servizio esegue l'iterazione degli algoritmi ML abbinati alle selezioni di funzionalità, in cui ogni iterazione produce un modello con un punteggio di training. Maggiore è il punteggio, maggiore è il modello considerato "adatta" ai dati.  Si arresterà quando si raggiungeranno i criteri di uscita definiti nell'esperimento. 

Con **Azure Machine Learning**è possibile progettare ed eseguire gli esperimenti di training di Machine Learning automatici con i passaggi seguenti:

1. **Identificare il problema di ml** da risolvere: classificazione, previsione o regressione

1. **Scegliere se si vuole usare Python SDK o l'esperienza Web di studio**: informazioni sulla parità tra [Python SDK e l'esperienza Web di studio](#parity).

   * Per un'esperienza limitata o senza codice, provare l'esperienza Web di Azure Machine Learning Studio all'indirizzo[https://ml.azure.com](https://ml.azure.com/)  
   * Per gli sviluppatori Python, vedere [Azure Machine Learning Python SDK](how-to-configure-auto-train.md) 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **Specificare l'origine e il formato dei dati di training con etichetta**: matrici numpy o Dataframe Pandas

1. **Configurare la destinazione di calcolo per il training del modello**, ad esempio il [computer locale, Azure Machine Learning calcoli, le macchine virtuali remote o Azure Databricks](how-to-set-up-training-targets.md).  Informazioni sulla formazione automatica [su una risorsa remota](how-to-auto-train-remote.md).

1. **Configurare i parametri automatici di Machine Learning** che determinano il numero di iterazioni su modelli diversi, le impostazioni iperparametri, la pre-elaborazione avanzata/conteggi e le metriche da esaminare quando si determina il modello migliore.  
1. **Inviare l'esecuzione del training.**

1. **Esaminare i risultati** 

Nella seguente figura viene illustrato questo processo. 
![Machine Learning automatizzato](./media/concept-automated-ml/automl-concept-diagram2.png)


È anche possibile esaminare le informazioni sull'esecuzione registrata, che [contengono le metriche](how-to-understand-automated-ml.md) raccolte durante l'esecuzione. L'esecuzione del training produce un oggetto (`.pkl` file) serializzato Python che contiene il modello e la pre-elaborazione dei dati.

Sebbene la creazione di modelli sia automatizzata, è anche possibile [scoprire quanto siano importanti o rilevanti le funzionalità](how-to-configure-auto-train.md#explain) dei modelli generati.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pre-elaborazione

In ogni esperimento di Machine Learning automatizzato, i dati vengono pre-elaborati usando i metodi predefiniti e, facoltativamente, tramite la pre-elaborazione avanzata.

> [!NOTE]
> I passaggi di pre-elaborazione di Machine Learning automatizzati (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione di valori di testo nel formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di pre-elaborazione applicati durante il training vengono automaticamente applicati ai dati di input.

### <a name="automatic-preprocessing-standard"></a>Pre-elaborazione automatica (standard)

In ogni esperimento di Machine Learning automatizzato, i dati vengono ridimensionati o normalizzati automaticamente per consentire agli algoritmi di ottenere risultati ottimali.  Durante il training del modello, viene applicata una delle tecniche di ridimensionamento o di normalizzazione seguenti a ogni modello.

|Normalizzazione scalabile&nbsp;&&nbsp;| Descrizione |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizzare le funzionalità rimuovendo la media e il ridimensionamento in varianza unità  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Trasforma le funzionalità ridimensionando ogni funzionalità in base al valore minimo e massimo della colonna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Ridimensionare ogni funzionalità in base al valore assoluto massimo |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Questa funzionalità scaler in base al rispettivo intervallo di quantile |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Riduzione della dimensionalità lineare mediante scomposizione di valori singolari dei dati per proiettarla in uno spazio di dimensioni inferiori |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Questo trasformatore esegue la riduzione della dimensionalità lineare per mezzo della scomposizione del valore singolare troncato (SVD). Contrariamente a PCA, questo estimatore non centra i dati prima di calcolare la scomposizione di valori singolari, il che significa che può funzionare con le matrici SciPy. sparse in modo efficiente |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Ogni esempio, ovvero ogni riga della matrice di dati, con almeno un componente diverso da zero, viene ridimensionato in modo indipendente dagli altri esempi, in modo che la relativa norma (L1 o L2) sia uguale a uno |

### <a name="advanced-preprocessing--featurization"></a>Pre-elaborazione avanzata & conteggi

Sono disponibili anche funzionalità avanzate di pre-elaborazione e conteggi, ad esempio Guardrails dati, codifica e trasformazioni. [Scopri di più su cosa è incluso conteggi](how-to-use-automated-ml-for-ml-models.md#featurization). Abilitare questa impostazione con:

+ Azure Machine Learning Studio: abilitare **conteggi automatici** nella sezione **Visualizza configurazione aggiuntiva** [con questi passaggi](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ Python SDK: specifica `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` per la [ `AutoMLConfig` classe](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 



## <a name="ensemble-models"></a><a name="ensemble"></a>Modelli di ensemble

Machine Learning automatizzato supporta i modelli di Ensemble, che sono abilitati per impostazione predefinita. Ensemble Learning migliora i risultati di machine learning e le prestazioni predittive combinando più modelli anziché l'uso di singoli modelli. Le iterazioni insieme vengono visualizzate come le iterazioni finali dell'esecuzione. Il Machine Learning automatico usa i metodi Ensemble di voto e di stacking per combinare i modelli:

* **Voto**: stima in base alla media ponderata delle probabilità della classe stimata (per le attività di classificazione) o alle destinazioni di regressione stimate (per le attività di regressione).
* **Stacking**: lo stacking combina i modelli eterogeneo e addestra un metamodello in base all'output dei singoli modelli. I metadati predefiniti correnti sono LogisticRegression per le attività di classificazione e ElasticNet per le attività di regressione/previsione.

L' [algoritmo di selezione dell'ensemble di Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) con inizializzazione dell'insieme ordinato viene usato per decidere quali modelli usare nell'insieme. A un livello elevato, questo algoritmo Inizializza l'insieme con un massimo di cinque modelli con i punteggi individuali migliori e verifica che questi modelli siano entro il 5% di soglia del punteggio migliore per evitare un insieme iniziale scadente. Per ogni iterazione di ensemble viene quindi aggiunto un nuovo modello all'insieme esistente e il punteggio risultante viene calcolato. Se un nuovo modello ha migliorato il Punteggio di ensemble esistente, l'insieme viene aggiornato in modo da includere il nuovo modello.

Vedere le [procedure](how-to-configure-auto-train.md#ensemble) per modificare le impostazioni di ensemble predefinite in Machine Learning automatico.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Linee guida per le destinazioni di calcolo locali e remote gestite da ML

L'interfaccia Web per Machine Learning machine learning usa sempre una [destinazione di calcolo](concept-compute-target.md)remota.  Tuttavia, quando si usa Python SDK, scegliere una destinazione di calcolo locale o remota per il training di Machine Learning automatico.

* **Calcolo locale**: il training viene eseguito sul computer portatile locale o sul calcolo della macchina virtuale. 
* **Calcolo remoto**: il training si verifica nei cluster di calcolo Machine Learning.  

### <a name="choose-compute-target"></a>Scegliere la destinazione di calcolo
Prendere in considerazione questi fattori quando si sceglie la destinazione di calcolo:

 * **Scegliere un calcolo locale**: se lo scenario riguarda le esplorazioni iniziali o le demo che usano piccoli dati e treni brevi, ovvero secondi o un paio di minuti per ogni esecuzione, il training sul computer locale potrebbe essere la scelta migliore.  Non sono disponibili tempi di installazione, le risorse dell'infrastruttura (PC o VM) sono direttamente disponibili.
 * **Scegliere un cluster di elaborazione ml remoto**: se si esegue il training con set di impostazioni di dimensioni maggiori, ad esempio nel training di produzione, creazione di modelli che richiedono treni più lunghi, il calcolo remoto fornirà `AutoML` prestazioni ottimali per l'ora end-to-end perché parallelizzare i treni tra i nodi del cluster. In un calcolo remoto, il tempo di avvio per l'infrastruttura interna aggiungerà circa 1,5 minuti per ogni esecuzione, oltre a minuti aggiuntivi per l'infrastruttura cluster se le macchine virtuali non sono ancora in esecuzione.

### <a name="pros-and-cons"></a>Vantaggi e svantaggi
Considerare questi vantaggi e svantaggi quando si sceglie di usare local e remote.

|  | Vantaggi (vantaggi)  |Svantaggi (handicap)  |
|---------|---------|---------|---------|
|**Destinazione di calcolo locale** |  <li> Nessun orario di avvio dell'ambiente   | <li>  Subset di funzionalità<li>  Non è possibile eseguire parallelizzare <li> Peggiore per i dati di grandi dimensioni. <li>Nessun flusso di dati durante il training <li>  Nessun conteggi basato su DNN <li> Solo Python SDK |
|**Cluster di calcolo di ML remoti**|  <li> Set completo di funzionalità <li> Esecuzioni figlio parallelizzare <li>   Supporto dati di grandi dimensioni<li>  Conteggi basato su DNN <li>  Scalabilità dinamica del cluster di elaborazione su richiesta <li> È disponibile anche l'esperienza senza codice (interfaccia utente Web)  |  <li> Tempo di avvio per i nodi del cluster <li> Tempo di avvio per ogni esecuzione figlio    |

### <a name="feature-availability"></a>Disponibilità delle funzionalità 

 Quando si usa il calcolo remoto, sono disponibili altre funzionalità, come illustrato nella tabella seguente. Alcune di queste funzionalità sono disponibili solo in un'area di lavoro aziendale.

| Funzionalità                                                    | Remoto | Locale | Richiede <br>Area di lavoro aziendale |
|------------------------------------------------------------|--------|-------|-------------------------------|
| Streaming dei dati (supporto per dati di grandi dimensioni, fino a 100 GB)          | ✓      |       | ✓                             |
| DNN-conteggi del testo basato su BERT e formazione             | ✓      |       | ✓                             |
| Supporto GPU predefinito (formazione e inferenza)        | ✓      |       | ✓                             |
| Supporto per la classificazione delle immagini e l'assegnazione di etichette                  | ✓      |       | ✓                             |
| Modelli ARIMA, Prophet e ForecastTCN automatici per la previsione | ✓      |       | ✓                             |
| Più esecuzioni/iterazioni in parallelo                       | ✓      |       | ✓                             |
| Creare modelli con l'interpretazione nell'interfaccia utente di AutoML Studio Web Experience      | ✓      |       | ✓                             |
| Personalizzazione della progettazione delle funzionalità nell'interfaccia utente dell'esperienza Web di studio                        | ✓      |       | ✓                              |
| Ottimizzazione di iperparametri di Azure ML                             | ✓      |       |                               |
| Supporto del flusso di lavoro della pipeline di Azure ML                         | ✓      |       |                               |
| Continua un'esecuzione                                             | ✓      |       |                               |
| Previsione                                                | ✓      | ✓     | ✓                             |
| Creazione ed esecuzione di esperimenti nei notebook                    | ✓      | ✓     |                               |
| Registrare e visualizzare le informazioni e le metriche dell'esperimento nell'interfaccia utente | ✓      | ✓     |                               |
| Guardrails dati                                            | ✓      | ✓     |                               |


## <a name="automated-ml-in-azure-machine-learning"></a>ML automatizzato in Azure Machine Learning

Azure Machine Learning offre due esperienze per l'uso di ML automatizzato

* Per i clienti con esperienza di codice, [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* Per i clienti con esperienza limitata o senza codice, Azure Machine Learning Studio alle[https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>Impostazioni esperimento 

Le impostazioni seguenti consentono di configurare l'esperimento di ML automatizzato. 

| |Python SDK|Esperienza Web di studio|
----|:----:|:----:
Suddividere i dati in set di Training/convalida| ✓|✓
Supporta le attività ML: classificazione, regressione e previsione| ✓| ✓
Ottimizza in base alla metrica primaria| ✓| ✓
Supporta il calcolo di AML come destinazione di calcolo | ✓|✓
Configurare l'orizzonte di previsione, i ritardi di destinazione & finestra in sequenza|✓|✓
Imposta criteri di uscita |✓|✓ 
Imposta iterazioni simultanee| ✓|✓
Rilascia colonne| ✓|✓
Algoritmi di blocco|✓|✓
Convalida incrociata |✓|✓
Supporta la formazione su cluster Azure Databricks| ✓|
Visualizzare i nomi delle funzionalità progettate|✓|
Riepilogo di conteggi| ✓|
Conteggi per le festività|✓|
Livelli di dettaglio del file di log| ✓|

### <a name="model-settings"></a>Impostazioni modello

Queste impostazioni possono essere applicate al modello migliore in seguito all'esperimento di Machine Learning automatizzato.

| |Python SDK|Esperienza Web di studio|
|----|:----:|:----:|
|Registrazione ottimale del modello, distribuzione, spiegazione| ✓|✓|
|Abilitare i modelli di ensemble di voto & stack Ensemble| ✓|✓|
|Mostra il modello migliore in base a una metrica non primaria|✓||
|Abilita/Disabilita la compatibilità del modello ONNX|✓||
|Testare un modello | ✓| |

### <a name="run-control-settings"></a>Impostazioni di controllo esecuzione

Queste impostazioni consentono di esaminare e controllare le esecuzioni dell'esperimento e le esecuzioni figlio. 

| |Python SDK|Esperienza Web di studio|
|----|:----:|:----:|
|Tabella di riepilogo esecuzione| ✓|✓|
|Annulla esecuzioni & esecuzioni figlio| ✓|✓|
|Ottenere Guardrails| ✓|✓|
|Sospendere & riprendere le esecuzioni| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Con Azure Machine Learning, è possibile usare il Machine Learning automatico per compilare un modello Python e convertirlo nel formato ONNX. Quando i modelli sono nel formato ONNX, possono essere eseguiti in un'ampia gamma di piattaforme e dispositivi. Altre informazioni sull' [accelerazione dei modelli ml con ONNX](concept-onnx.md).

Vedere come convertire il formato ONNX [in questo esempio di Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Informazioni sugli [algoritmi supportati in ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

Il runtime di ONNX supporta anche C#, quindi è possibile usare il modello creato automaticamente nelle app C# senza dover ricodificare o una delle latenze di rete introdotte dagli endpoint REST. Altre informazioni sull' [inferenza dei modelli ONNX con l'API C# di ONNX Runtime](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 

## <a name="next-steps"></a>Passaggi successivi

Vedere gli esempi e informazioni su come creare modelli usando Machine Learning automatico:

+ Configurare le impostazioni per l'esperimento di training automatico:
  + In Azure Machine Learning Studio [usare questa procedura](how-to-use-automated-ml-for-ml-models.md).
  + Con Python SDK, [seguire questa procedura](how-to-configure-auto-train.md).

+ Informazioni su come usare una [destinazione di calcolo remota](how-to-auto-train-remote.md)

+ Seguire l' [esercitazione: eseguire automaticamente il training di un modello di regressione con Azure Machine Learning](tutorial-auto-train-models.md) 

+ Per informazioni su come eseguire il training automatico usando i dati delle serie temporali, [seguire questa procedura](how-to-auto-train-forecast.md).

+ Prova [Jupyter notebook esempi per Machine Learning automatizzato](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
* Il Machine Learning automatico è disponibile anche in altre soluzioni Microsoft, ad esempio [ml.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-automated) e [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
