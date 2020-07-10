---
title: Generare raccomandazioni con Apache Mahout in Azure HDInsight
description: Informazioni su come usare la libreria di Machine Learning Apache Mahout per generare raccomandazioni di film con HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: a4e4a45519526dd0eeb938a3b83e737d82589c1e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207674"
---
# <a name="generate-recommendations-using-apache-mahout-in-azure-hdinsight"></a>Generare raccomandazioni con Apache Mahout in Azure HDInsight

Informazioni su come usare la libreria di Machine Learning [Apache Mahout](https://mahout.apache.org) con Azure HDInsight per generare raccomandazioni di film.

Mahout è una libreria di [Machine Learning](https://en.wikipedia.org/wiki/Machine_learning) per Apache Hadoop. Mahout contiene gli algoritmi per l'elaborazione dei dati, ad esempio applicazione di filtri, classificazione e clustering. In questo articolo si userà un motore di raccomandazione per generare consigli cinematografici in base ai film visti dai propri amici.

Per altre informazioni sulla versione di Mahout in HDInsight, vedere l'articolo relativo a [versioni di HDInsight e componenti di Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="prerequisites"></a>Prerequisiti

Un cluster Apache Hadoop in HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="understanding-recommendations"></a>Informazioni sulle raccomandazioni

Una delle funzioni fornite da Mahout è un motore di raccomandazione. Questo motore accetta i dati nei formati `userID`, `itemId` e `prefValue` (la preferenza per l'elemento). Mahout può quindi eseguire l'analisi delle co-occorrenze per determinare che gli *utenti con una preferenza per un elemento hanno anche una preferenza per altri elementi*. Mahout determina quindi gli utenti con preferenze di elementi simili, che possono essere usate per le raccomandazioni.

Il flusso di lavoro seguente costituisce un esempio semplificato che usa dati relativi ai film:

* **Co-occorrenza**: a Joe, Alice e Bob piacciono *Guerre stellari*, *L'Impero colpisce ancora* e *Il ritorno dello Jedi*. Mahout determina che agli utenti a cui piace uno di questi film piacciono anche gli altri due.

* **Co-occorrenza**: a Bob e Alice piacciono anche *La minaccia fantasma*, *L'attacco dei cloni* e *La vendetta dei Sith*. Mahout determina che agli utenti a cui piacciono i tre film precedenti piacciono anche questi tre.

* **Raccomandazione per somiglianza**: poiché a Joe piacciono i primi tre film, Mahout cerca i film che piacciono ad altri utenti con preferenze simili, ma che Joe non ha guardato o per i quali non ha ancora espresso una preferenza o una valutazione. In questo caso, Mahout raccomanda *La minaccia fantasma*, *L'attacco dei cloni* e *La vendetta dei Sith*.

### <a name="understanding-the-data"></a>Informazioni sui dati

[GroupLens Research](https://grouplens.org/datasets/movielens/) fornisce i dati di classificazione dei film in un formato compatibile con Mahout. Questi dati sono disponibili nello spazio di archiviazione predefinito del cluster in `/HdiSamples/HdiSamples/MahoutMovieData`.

Sono disponibili due file: `moviedb.txt` e `user-ratings.txt`. Il file `user-ratings.txt` viene usato durante l'analisi. `moviedb.txt` viene usato per presentare informazioni in testo descrittivo quando si visualizzano i risultati.

I dati contenuti in `user-ratings.txt` presentano una struttura di `userID`, `movieID`, `userRating` e `timestamp` che indica la valutazione di un film effettuata da ogni utente. Di seguito è riportato un esempio dei dati:

```output
    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596
```

## <a name="run-the-analysis"></a>Eseguire l'analisi

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Per eseguire il processo di raccomandazione, usare il comando seguente:

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> Il completamento del processo potrebbe richiedere alcuni minuti ed è possibile eseguire più processi MapReduce.

## <a name="view-the-output"></a>Visualizzare l'output

1. Una volta completato il processo, usare il seguente comando per visualizzare l'output generato:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    L'output viene visualizzato come segue:

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    La prima colonna rappresenta il valore `userID`. I valori racchiusi tra "[" e "]" sono `movieId`:`recommendationScore`.

2. È possibile usare l'output, insieme a moviedb.txt, per fornire altre informazioni sulle raccomandazioni. È prima necessario copiare i file in locale con i comandi seguenti:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    I dati di output vengono copiati da questo comando in un file denominato **recommendations.txt** nella directory corrente insieme ai file dei dati relativi ai film.

3. Usare il comando seguente per creare uno script Python che cerca i nomi dei film per i dati presenti nell'output delle raccomandazioni:

    ```bash
    nano show_recommendations.py
    ```

    All'apertura dell'editor usare il testo seguente come contenuto del file:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Premere **CTRL-X**, **Y** e infine **INVIO** per salvare i dati.

4. Eseguire lo script Python. Il comando seguente presuppone che l'utente si trovi nella directory in cui sono stati scaricati tutti i file:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Questo comando esamina le raccomandazioni generate per l'ID utente 4.

   * Il file **user-ratings.txt** viene usato per recuperare i film che sono stati classificati.

   * Il file **moviedb.txt** viene usato per recuperare i nomi dei film.

   * Il file **recommendations.txt** viene usato per recuperare le raccomandazioni di film per questo utente.

     L'output di questo comando è simile al testo seguente:

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>Eliminare i dati temporanei

I processi Mahout non rimuovono i dati temporanei creati durante l'elaborazione del processo. Nel processo di esempio è specificato il parametro `--tempDir` per isolare i file temporanei in un percorso specifico per semplificarne l'eliminazione. Per rimuovere i file temporanei, usare il comando seguente:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Se si vuole eseguire nuovamente il comando, è inoltre necessario eliminare la directory di output. Per eliminare la directory, usare il codice seguente:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come usare Mahout, è possibile trovare altri modi per usare i dati in HDInsight:

* [Apache Hive con HDInsight](hdinsight-use-hive.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)
