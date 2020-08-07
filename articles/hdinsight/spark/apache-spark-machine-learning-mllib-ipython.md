---
title: Esempio di Machine Learning con MLlib Spark in HDInsight di Azure
description: Informazioni su come usare MLlib Spark per creare un'app di Machine Learning che analizza un set di dati usando una classificazione tramite la regressione logistica.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020, devx-track-python
ms.date: 04/27/2020
ms.openlocfilehash: 2ab996c3f3310656e7b85dded8e57a129b901660
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873807"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Usare MLlib Apache Spark per compilare un'applicazione di Machine Learning e analizzare un set di dati

Informazioni su come usare Apache Spark MLlib per creare un'applicazione di machine learning. L'applicazione eseguirà l'analisi predittiva su un set di dati aperto. Dalle librerie di Machine Learning integrate di Spark, questo esempio usa la *classificazione* tramite la regressione logistica.

MLlib è una libreria Spark di base che offre molte utilità utili per le attività di Machine Learning, ad esempio:

* Classificazione
* Regressione
* Clustering
* Modellazione
* Scomposizione di valori singolari e analisi in componenti principali
* Testing e calcolo ipotetici di statistiche di esempio

## <a name="understand-classification-and-logistic-regression"></a>Informazioni sulla classificazione e la regressione logistica

La *classificazione*, un'attività comune di apprendimento automatico, è il processo di ordinamento dei dati in categorie. È compito di un algoritmo di classificazione determinare come assegnare "etichette" ai dati di input forniti dall'utente. Ad esempio, è possibile pensare a un algoritmo di Machine Learning che accetta informazioni sulle azioni come input. Quindi divide le scorte in due categorie: le scorte da vendere e le scorte che è opportuno tenere.

La regressione logistica è l'algoritmo usato per la classificazione. L'API di regressione logistica di Spark è utile per la *classificazione binaria*, ovvero la classificazione di dati di input in uno di due gruppi. Per altre informazioni sulle regressioni logistiche, vedere [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

In sintesi, il processo di regressione logistica produce una *funzione logistica*. Usare la funzione per stimare la probabilità che un vettore di input appartenga a un gruppo o all'altro.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Esempio di analisi predittiva dei dati di controllo degli alimenti

In questo esempio si usa Spark per eseguire un'analisi predittiva dei dati di ispezione degli alimenti (**Food_Inspections1.csv**). Dati acquisiti tramite il [portale dati della città di Chicago](https://data.cityofchicago.org/). Questo set di dati contiene informazioni sui controlli degli alimenti condotti a Chicago. Sono incluse informazioni su ogni stabilimento, sulle eventuali violazioni trovate e sui risultati del controllo. Il file di dati in formato CSV è già disponibile nell'account di archiviazione associato al cluster in **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Nei passaggi seguenti, si svilupperà un modello per sapere che cosa serve per superare o non superare un controllo sugli alimenti.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Creare una pipeline di apprendimento automatico MLlib Apache Spark

1. Creare un notebook di Jupyter usando il kernel PySpark. Per le istruzioni, vedere [Creare un notebook Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importare i tipi richiesti per l'applicazione. Copiare e incollare il codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```

    A causa del kernel PySpark, non è necessario creare contesti in modo esplicito. I contesti Spark e hive vengono creati automaticamente quando si esegue la prima cella di codice.

## <a name="construct-the-input-dataframe"></a>Creare il frame di dati di input

Usare il contesto Spark per eseguire il pull dei dati CSV non elaborati in memoria come testo non strutturato. Usare quindi la libreria CSV di Python per analizzare ogni riga dei dati.

1. Eseguire le righe seguenti per creare un set di dati RDD (Resilient Distributed Dataset) tramite importazione e analisi dei dati di input.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value

    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Eseguire il codice seguente per recuperare una riga da RDD, in modo da poter esaminare lo schema dei dati:

    ```PySpark
    inspections.take(1)
    ```

    L'output è il seguente:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    L'output dà un'idea dello schema del file di input. Include il nome di ogni stabilimento e il tipo di stabilimento. Inoltre, l'indirizzo, i dati delle ispezioni e la posizione, tra le altre cose.

3. Eseguire il codice seguente per creare un frame di dati (*df*) e una tabella temporanea (*CountResults*) con alcune colonne che sono utili per l'analisi predittiva. `sqlContext`viene usato per eseguire trasformazioni sui dati strutturati.

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])

    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Le quattro colonne di interesse nel frame di dati sono **ID**, **Name**, **results**e **Violations**.

4. Eseguire il codice seguente per ottenere un piccolo campione dei dati:

    ```PySpark
    df.show(5)
    ```

    L'output è il seguente:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Informazioni sui dati

Ora si determinerà il contenuto del set di dati. 

1. Eseguire il codice seguente per visualizzare i valori distinti nella colonna **results**:

    ```PySpark
    df.select('results').distinct().show()
    ```

    L'output è il seguente:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Eseguire il codice seguente per visualizzare la distribuzione di questi risultati:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    Il comando speciale `%%sql` seguito da `-o countResultsdf` assicura che l'output della query venga mantenuto in locale nel server Jupyter, di solito il nodo head del cluster. L'output viene conservato come frame di dati [Pandas](https://pandas.pydata.org/) con il nome specificato **countResultsdf**. Per altre informazioni sul comando Magic `%%sql` e sugli altri comandi Magic disponibili con il kernel PySpark, vedere [Kernel disponibili per i notebook di Jupyter con cluster Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    L'output è il seguente:

    ![Output della query SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Output della query SQL")

3. È anche possibile creare un tracciato tramite Matplotlib, una libreria che consente di creare visualizzazioni di dati. Poiché il tracciato deve essere creato dal frame di dati **countResultsdf** conservato in locale, il frammento di codice deve iniziare con `%%local`. Questa azione garantisce che il codice venga eseguito localmente nel server Jupyter.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Per stimare il risultato di un controllo di prodotti alimentari, è necessario sviluppare un modello basato sulle violazioni. Dato che la regressione logistica è un metodo di classificazione binaria, è consigliabile raggruppare i dati dei risultati in due categorie: **Fail** e **Pass**:

   - Pass
       - Pass
       - Pass w/ conditions
   - Esito negativo
       - Esito negativo
   - Discard
       - Business not located
       - Out of Business

     I dati con gli altri risultati ("business not located" o "out of business") non sono utili e costituiscono comunque una piccola percentuale dei risultati.

4. Eseguire il codice seguente per convertire il frame di dati esistente (`df`) in un nuovo frame di dati in cui ogni controllo è rappresentato come coppia etichetta-violazioni. In questo caso, un'etichetta di `0.0` rappresenta un errore, un'etichetta di `1.0` rappresenta un esito positivo e un'etichetta `-1.0` rappresenta alcuni risultati oltre a questi due risultati.

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Eseguire il codice seguente per visualizzare una riga dei dati con etichetta:

    ```PySpark
    labeledData.take(1)
    ```

    L'output è il seguente:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Creare un modello di regressione logistica dal frame di dati di input

L'attività finale consiste nel convertire i dati con etichetta. Convertire i dati in un formato che possa essere analizzato dalla regressione logistica. L'input per un algoritmo di regressione logistica necessita di un set di *coppie di vettori di funzionalità di etichetta*. Dove il "vettore di funzionalità" è un vettore di numeri che rappresentano il punto di input. Quindi, è necessario convertire la colonna "violazioni", che è semistrutturata e contiene numerosi commenti in testo libero. Convertire la colonna in una matrice di numeri reali che possono essere facilmente comprensibili per un computer.

Un approccio di apprendimento automatico standard per l'elaborazione del linguaggio naturale consiste nell'assegnare a ogni parola distinta un "indice". Passare quindi un vettore all'algoritmo di machine learning. In modo che il valore di ogni indice contenga la frequenza relativa di tale parola nella stringa di testo.

MLlib fornisce un modo semplice per eseguire questa operazione. In primo luogo, suddividere in token ogni stringa di violazione per ottenere le parole singole in ogni stringa. Quindi, usare un `HashingTF` per convertire ogni set di token in un vettore di funzione da passare successivamente all'algoritmo di regressione logistica per creare un modello. Tutti questi passaggi vengono eseguiti in sequenza usando una "pipeline".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Valutare il modello usando un altro set di dati

È possibile usare il modello creato in precedenza per *stimare* quali saranno i risultati dei nuovi controlli. Le stime si basano sulle violazioni osservate. Il training di questo modello è stato eseguito sul set di dati **Food_Inspections1.csv**. È possibile usare un secondo set di dati, **Food_Inspections2.csv**, per *valutare* l'efficacia di questo modello sui nuovi dati. Questo secondo set di dati (**Food_Inspections2.csv**) si trova nel contenitore di archiviazione predefinito associato al cluster.

1. Eseguire il codice seguente per creare un nuovo frame di dati, **predictionsDf** contenente la stima generata dal modello. Il frammento di codice crea anche la tabella temporanea **Predictions** basata sul dataframe.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Verrà visualizzato un output simile al testo seguente:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Esaminare una delle stime. Eseguire questo frammento di codice:

    ```PySpark
    predictionsDf.take(1)
    ```

   Esiste una stima per la prima voce nel set di dati di test.

1. Il metodo `model.transform()` applicherà la stessa trasformazione ai nuovi dati con lo stesso schema e formulerà una stima per la classificazione dei dati. È possibile eseguire alcune statistiche per avere un'idea del modo in cui sono state eseguite le stime:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    L'output ha un aspetto simile al testo seguente:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    L'uso della regressione logistica con Spark offre un modello della relazione tra le descrizioni delle violazioni in inglese. E se una determinata azienda passa o non supera un controllo di cibo.

## <a name="create-a-visual-representation-of-the-prediction"></a>Creare una rappresentazione visiva della stima

È ora possibile creare una visualizzazione finale per comprendere meglio i risultati di questo test.

1. Iniziare dall'estrazione delle diverse stime e dei vari risultati dalla tabella temporanea **Predictions** creata in precedenza. Le query seguenti separano l'output in *true_positive*, *false_positive*, *true_negative* e *false_negative*. Nelle query seguenti disattivare la visualizzazione usando `-q` e, tramite `-o`, salvare l'output come frame di dati utilizzabili con il comando speciale `%%local`.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Usare infine il frammento di codice seguente per generare il tracciato con **Matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Verrà visualizzato l'output seguente:

    ![Output dell'applicazione di Machine Learning Spark: percentuali dei grafici a torta di ispezioni non riuscite.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Output del risultato di Machine Learning Spark")

    In questo grafico un risultato positivo indica il controllo degli alimenti non superato, mentre un risultato negativo indica un controllo superato.

## <a name="shut-down-the-notebook"></a>Arrestare il notebook

Al termine dell'esecuzione dell'applicazione, è necessario arrestare il notebook per rilasciare le risorse. Per fare ciò, dal menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi). Questa azione spegne e chiude il notebook.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Microsoft Cognitive Toolkit modello di apprendimento avanzato con Azure HDInsight](apache-spark-microsoft-cognitive-toolkit.md)
