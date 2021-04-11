---
title: Approfondimento - Analisi avanzata - Azure HDInsight
description: Informazioni su come Advanced Analytics usa gli algoritmi per elaborare Big Data in Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 0780f66c981f0cebebc1ab327d783954753db965
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866727"
---
# <a name="deep-dive---advanced-analytics"></a>Approfondimento - Analisi avanzata

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Cos'è l'analisi avanzata per HDInsight?

HDInsight offre la possibilità di ottenere informazioni dettagliate importanti da grandi quantità di dati strutturati, non strutturati e in rapido movimento. L'analisi avanzata prevede l'uso di architetture altamente scalabili, modelli di apprendimento statistici e di Machine Learning e dashboard intelligenti per offrire informazioni dettagliate significative. Il Machine Learning, o *analisi predittiva*, si serve di algoritmi che identificano le relazioni tra i dati e apprendono da tali relazioni per generare previsioni e aiutare nei processi decisionali.

## <a name="advanced-analytics-process"></a>Processo di analisi avanzata

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png" alt-text="Flusso del processo di analisi avanzata" border="false":::

Dopo aver identificato il problema aziendale e aver avviato la raccolta e l'elaborazione dei dati, è necessario creare un modello che rappresenti la domanda per cui generare una previsione. Il modello usa uno o più algoritmi di Machine Learning per creare il tipo di previsione più adatto alle esigenze aziendali specifiche.  La maggior parte dei dati deve essere usata per eseguire il training del modello, mentre i dati rimanenti vengono usati per testare o valutare il modello.

Dopo aver creato, caricato, testato e valutato il modello, il passaggio successivo è costituito dalla distribuzione del modello per iniziare a rispondere alle domande. L'ultimo passaggio consiste nel monitorare le prestazioni del modello e procedere all'ottimizzazione in base alle esigenze.

## <a name="common-types-of-algorithms"></a>Tipi di algoritmi comuni

Le soluzioni di analisi avanzata offrono un set di algoritmi di Machine Learning. Di seguito è riportato un riepilogo delle categorie di algoritmi e dei casi d'uso aziendali comuni associati.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png" alt-text="Riepiloghi della categoria Machine Learning" border="false":::

Insieme alla selezione degli algoritmi più adatti, è necessario considerare se devono essere forniti dati per il training. Gli algoritmi di Machine Learning sono suddivisi in categorie come indicato di seguito:

* Supervisionati: per poter ottenere risultati, è necessario eseguire il training dell'algoritmo su un set di dati etichettati
* Semi supervisionati: l'algoritmo può essere ampliato da destinazioni aggiuntive tramite query interattive da un trainer, che non era disponibile durante la fase iniziale di formazione
* Senza supervisione: l'algoritmo non richiede dati di training
* Rinforzo: l'algoritmo usa agenti software per determinare il comportamento ideale in un contesto specifico (tecnica spesso usata in robotica)

| Categoria algoritmo| Uso | Tipo di apprendimento | Algoritmi |
| --- | --- | --- | -- |
| Classificazione | Classificare oggetti o persone in gruppi | Sotto la supervisione | Alberi delle decisioni, regressione logistica, reti neurali |
| Clustering | Dividere un set di esempi in gruppi omogenei | Non supervisionato | Clustering K-Means |
| Rilevamento schemi | Identificare associazioni frequenti nei dati | Non supervisionato | Regole di associazione |
| Regressione | Prevedere risultati numerici | Sotto la supervisione | Regressione lineare, reti neurali |
| Rinforzo | Determinare il comportamento ottimale dei robot | Rinforzo | Simulazioni Monte Carlo, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Machine Learning in HDInsight

HDInsight offre diverse opzioni di Machine Learning per un flusso di lavoro di analisi avanzata:

* Apprendimento automatico e Apache Spark
* R Services e ML Services
* Azure Machine Learning e Apache Hive
* Apache Spark e Deep Learning

### <a name="machine-learning-and-apache-spark"></a>Apprendimento automatico e Apache Spark

[HDInsight Spark](../spark/apache-spark-overview.md) è una soluzione ospitata in Azure di [Apache Spark](https://spark.apache.org/), un framework di elaborazione parallela dei dati unificato e open source che usa l'elaborazione in memoria per ottimizzare l'analisi dei Big Data. Il motore di elaborazione Spark è costruito per la velocità, la semplicità d'uso e le analisi sofisticate. Le funzionalità di calcolo distribuite in memoria rendono Spark uno strumento valido per l'esecuzione di algoritmi iterativi utilizzati in calcoli grafici e di Machine Learning.

Tre librerie di Machine Learning scalabili introducono funzionalità di modellazione algoritmica nell'ambiente distribuito:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html): MLlib contiene l'API originale basata su RDD di Spark.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html): SparkML è un pacchetto più recente che contiene un'API di livello superiore basata su Spark DataFrames per la costruzione di pipeline ML.
* [**MMLSpark**](https://github.com/Azure/mmlspark): la libreria di Microsoft Machine Learning per Apache Spark (MMLSpark) è stata progettata per incrementare la produttività dei data scientist in Spark, aumentare la velocità di sperimentazione e sfruttare le più innovative tecniche di Machine Learning, tra cui l'apprendimento avanzato, su set di dati molto ampi. La libreria MMLSpark semplifica le attività di modellazione comuni per la creazione di modelli in PySpark.

### <a name="r-and-ml-services"></a>R Services e ML Services

Nell'ambito di HDInsight è possibile creare un cluster HDInsight con [ML Services](../r-server/r-server-overview.md) pronto per essere usato con set di dati e modelli di grandi dimensioni. Questa nuova funzionalità offre a data scientist ed esperti di statistica un'interfaccia R familiare e scalabile su richiesta tramite HDInsight, senza dover eseguire attività aggiuntive di configurazione e manutenzione del cluster.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning e Apache Hive

[Azure Machine Learning Studio (classico)](https://studio.azureml.net/) fornisce strumenti per modellare l'analisi predittiva e un servizio completamente gestito che è possibile usare per distribuire i modelli predittivi come servizi Web pronti all'uso. Grazie agli strumenti per la creazione di soluzioni di analisi predittiva complete nel cloud, Azure Machine Learning consente di creare, testare, usare e gestire modelli predittivi in modo rapido. È possibile selezionare gli algoritmi da una libreria di grandi dimensioni, usare un ambiente basato sul Web per creare modelli e distribuire con facilità il modello come servizio Web.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark e Deep Learning

L'[apprendimento avanzato](https://www.microsoft.com/research/group/dltc/) è un ambito del Machine Learning che usa *reti neurali profonde* (DNN) ispirate dai processi biologici del cervello umano. Molti ricercatori considerano l'approccio adottato per l'apprendimento avanzato come promettente per l'intelligenza artificiale. Alcuni esempi di apprendimento avanzato sono i sistemi di traduzione del parlato, i sistemi di riconoscimento delle immagini e i sistemi di ragionamento automatici. A sostegno del proprio impegno nel campo dell'apprendimento avanzato, Microsoft ha sviluppato [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), uno strumento open source gratuito e facile da usare. Il toolkit è ampiamente usato da numerosi prodotti Microsoft, da aziende di tutto il mondo che hanno la necessità di distribuire l'apprendimento avanzato su vasta scala e da studenti interessati agli algoritmi e alle tecniche più recenti.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenario - Riconoscere le immagini per identificare gli schemi di sviluppo urbano

Di seguito è riportato un esempio di pipeline di Machine Learning di analisi avanzata che usa HDInsight.

In questo scenario si vedrà come DNN prodotto in un Framework di apprendimento avanzato, il Cognitive Toolkit Microsoft (CNTK), possa essere reso operativo per il Punteggio di grandi raccolte di immagini archiviate in un account di archiviazione BLOB di Azure usando PySpark in un cluster HDInsight Spark. Questo approccio viene applicato a un caso d'uso DNN comune, alla classificazione di immagini aeree e può essere usato per identificare modelli recenti di sviluppo urbano.  Si userà un modello di classificazione delle immagini con training preliminare. Il training preliminare è stato eseguito in base al [set di dati CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) e il modello è stato applicato a 10.000 immagini trattenute.

Questo scenario di analisi avanzata prevede tre principali attività:

1. Creare un cluster Hadoop di Azure HDInsight con una distribuzione di Apache Spark 2.1.0.
2. Eseguire uno script personalizzato per installare Microsoft Cognitive Toolkit in un cluster Spark di Azure HDInsight.
3. Caricare un Jupyter Notebook predefinito nel cluster HDInsight Spark per applicare un modello di apprendimento avanzato Microsoft Cognitive Toolkit con training ai file in un account di archiviazione BLOB di Azure tramite l'API Python Spark (PySpark).

Questo esempio è basato sul set di immagini CIFAR-10 compilato e distribuito da Alex Krizhevsky, Vinod Nair e Geoffrey Hinton. Il set di dati 10 CIFAR contiene 60.000 immagini a colori 32x32 appartenenti a 10 classi che si escludono a vicenda:

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png" alt-text="Immagini di esempio Machine Learning" border="false":::

Per altre informazioni sul set di dati, vedere la pagina relativa alla [formazione su più livelli di funzionalità di Alex Krizhevsky da immagini di piccole dimensioni](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Il set di dati è stato suddiviso in un set di training costituito da 50.000 immagini e un set di test costituito da 10.000 immagini. Il primo set è stato usato per il training di un modello di rete residua (ResNet) convoluzionale costituito da venti livelli tramite Microsoft Cognitive Toolkit seguendo [questa esercitazione](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) disponibile nel repository GitHub di Cognitive Toolkit. Le restanti immagini 10.000 sono state usate per testare l'accuratezza del modello. A questo punto entra in gioco l'elaborazione distribuita. L'attività di pre-elaborazione e riconoscimento delle immagini è altamente parallelizzabile. Con il modello sottoposto a training salvato, si è usato:

* PySpark per distribuire le immagini e il modello sottoposto a training nei nodi di lavoro del cluster.
* Python per elaborare in via preliminare le immagini in ogni nodo del cluster Spark HDInsight.
* Cognitive Toolkit per caricare il modello e riconoscere le immagini pre-elaborate in ogni nodo.
* Notebook di Jupyter per eseguire lo script PySpark, aggregare i risultati e usare [Matplotlib](https://matplotlib.org/) per visualizzare le prestazioni del modello.

L'intero processo di pre-elaborazione/riconoscimento delle 10.000 immagini dura meno di un minuto in un cluster con quattro nodi di lavoro. Il modello prevede con accuratezza le etichette di circa 9.100 immagini (91%). Una matrice di confusione illustra gli errori di classificazione più comuni, ad esempio l'inversione di etichettatura di cani e gatti che si verifica più spesso rispetto ad altre coppie di etichette.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png" alt-text="Grafico risultati Machine Learning" border="false":::

### <a name="try-it-out"></a>Procedura

Seguire [questa esercitazione](../spark/apache-spark-microsoft-cognitive-toolkit.md) per implementare la soluzione end-to-end: configurare un cluster HDInsight Spark, installare cognitive Toolkit ed eseguire il Jupyter notebook che classifica le immagini del cifar 10.000.

## <a name="next-steps"></a>Passaggi successivi

Apache Hive e Azure Machine Learning

* [Apache Hive e Azure Machine Learning end-to-end](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Uso di un cluster Azure HDInsight Hadoop in un set di dati da 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark e MLLib

* [Apprendimento automatico con Apache Spark in HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark con Machine Learning: usare Apache Spark in HDInsight per l'analisi della temperatura di compilazione usando i dati HVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: usare Apache Spark in HDInsight per stimare i risultati dell'ispezione degli alimenti](../spark/apache-spark-machine-learning-mllib-ipython.md)

Apprendimento avanzato, Cognitive Toolkit e altri strumenti

* [Macchina virtuale data science di Azure](../../machine-learning/data-science-virtual-machine/overview.md)
* [Introducing H2O.ai on Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/) (Introduzione a H2O.ai in Azure HDInsight)
