---
title: Usare Livy Spark per inviare processi al cluster Spark in Azure HDInsight
description: Informazioni su come usare l'API REST di Apache Spark per inviare i processi in modalità remota a un cluster Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: ac3904284ebf20fa1d5e75f9249732be3963f677
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78206283"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Usare l'API REST di Apache Spark per inviare i processi remoti a un cluster HDInsight Spark

Informazioni su come usare [Apache Livy](https://livy.incubator.apache.org/), l'API REST di Apache Spark per inviare processi remoti a un cluster Azure HDInsight Spark. Per la documentazione dettagliata, vedere [Apache Livio](https://livy.incubator.apache.org/docs/latest/rest-api.html).

È possibile usare Livy per l'esecuzione interattiva di shell Spark o per inviare processi batch da eseguire su Spark. Questo articolo parla dell'uso di Livy per inviare processi batch. I frammenti di codice in questo articolo usano cURL per eseguire chiamate API REST all'endpoint Livy Spark.

## <a name="prerequisites"></a>Prerequisiti

Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="submit-an-apache-livy-spark-batch-job"></a>Inviare un processo batch Apache Livy Spark

Prima di inviare un processo batch, è necessario caricare il file con estensione jar dell'applicazione nell'archivio del cluster associato al cluster. A tale scopo è possibile usare [AzCopy](../../storage/common/storage-use-azcopy.md), un'utilità della riga di comando. Sono disponibili molti altri client da usare per caricare i dati. Altre informazioni in merito sono disponibili in [Caricare dati per processi Apache Hadoop in HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Esempi

* Se il file jar si trova nell'archivio cluster (WASBS)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Se si vuole trasferire il nome del file con estensione JAR e il nome della classe come parte di un file di input, in questo esempio input.txt

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Ottenere informazioni sui batch Livy Spark in esecuzione nel cluster

Sintassi:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Esempi

* Per recuperare tutti i batch Livy Spark in esecuzione nel cluster:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Se si desidera recuperare un batch specifico con un ID batch specificato

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Eliminare un processo batch Livy Spark

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Esempio

Eliminazione di un processo batch con ID `5`batch.

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark e disponibilità elevata

Livy fornisce disponibilità elevata per i processi Spark in esecuzione nel cluster. Ecco alcuni esempi.

* Se il servizio Livio si interrompe dopo l'invio di un processo in modalità remota a un cluster Spark, l'esecuzione del processo continua in background. Quando Livy ritorna attivo, ripristina lo stato del processo e crea un report.
* I notebook di Jupyter per HDInsight sono basati su Livy in back-end. Se un notebook è in esecuzione in un processo Spark e il servizio Livy viene riavviato, il notebook continuerà a eseguire le celle del codice.

## <a name="show-me-an-example"></a>Mostra un esempio

Questa sezione esamina alcuni esempi di come usare Livy Spark per inviare un processo batch, monitorare il progresso del processo e quindi eliminarlo. L'applicazione usata in questo esempio è quella sviluppata nell'articolo [Creare un'applicazione Scala autonoma da eseguire nel cluster HDInsight Spark](apache-spark-create-standalone-application.md). I passaggi seguenti presuppongono:

* Il file jar dell'applicazione è già stato copiato nell'account di archiviazione associato al cluster.
* CuRL è installato nel computer in cui si sta provando a eseguire questi passaggi.

Eseguire la procedura seguente:

1. Per semplicità d'uso, impostare le variabili di ambiente. Questo esempio è basato su un ambiente Windows, modificare le variabili in base alle esigenze dell'ambiente in uso. Sostituire `CLUSTERNAME`e `PASSWORD` con i valori appropriati.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Verificare che Livio Spark sia in esecuzione nel cluster. È possibile eseguire questa operazione ottenendo un elenco di batch in esecuzione. Se si sta eseguendo un processo usando Livio per la prima volta, l'output deve restituire zero.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    L'output dovrebbe essere simile al seguente frammento di codice:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Si noti che l'ultima riga nell'output corrisponde a **total:0**, che indica che non sono presenti batch in esecuzione.

1. Inviare ora un processo batch. Il frammento di codice seguente usa un file di input (input.txt) per trasferire il nome del file con estensione JAR e il nome della classe come parametri. Se si eseguono questi passaggi da un computer Windows, l'approccio consigliato è l'uso di un file di input.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    I parametri nel file **input.txt** sono definiti come segue:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    L'output visualizzato dovrebbe essere simile al frammento di codice seguente:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Si noti che l'ultima riga dell'output indica **state:starting**. Indica anche **id:0**. **0** è l'ID del batch.

1. È ora possibile recuperare lo stato di questo batch specifico usando l'ID del batch.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    L'output visualizzato dovrebbe essere simile al frammento di codice seguente:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    L'output ora indica **state:success**, il che suggerisce che il processo è stato completato.

1. Se si vuole, è ora possibile eliminare il batch.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    L'output visualizzato dovrebbe essere simile al frammento di codice seguente:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    L'ultima riga dell'output indica che il batch è stato correttamente eliminato. L'eliminazione di un processo, mentre è in esecuzione, interrompe anche il processo. Se si elimina un processo completato correttamente o non correttamente, le informazioni sul processo vengono eliminate completamente.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Aggiornamenti alla configurazione di Livy a partire dalla versione 3.5 di HDInsight

I cluster HDInsight 3.5 e superiori, per impostazione predefinita, disabilitano l'uso di percorsi di file locali per accedere ai file di dati di esempio o a file JAR. Si consiglia di usare invece il percorso `wasbs://` per accedere a file JAR o a file di dati di esempio dal cluster.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Invio di processi Livy per un cluster all'interno di una rete virtuale di Azure

Se ci si connette a un cluster HDInsight Spark all'interno di una rete virtuale di Azure, è possibile connettersi direttamente a Livy nel cluster. In tal caso, l'URL per l'endpoint di Livy è `http://<IP address of the headnode>:8998/batches`. In questo caso, **8998** è la porta su cui viene eseguito Livy sul nodo head del cluster. Per altre informazioni sull'accesso ai servizi sulle porte non pubbliche, vedere [Porte usate dai servizi Apache Hadoop su HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione dell'API REST Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
