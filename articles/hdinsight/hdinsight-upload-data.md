---
title: Caricare dati per processi Apache Hadoop in HDInsight
description: Informazioni su come caricare i dati per processi Apache Hadoop e accedervi in HDInsight con Interfaccia della riga di comando classica di Azure, Azure Storage Explorer, Azure PowerShell, la riga di comando di Hadoop o Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 7eb1f7e1ce02a30f84cb520438f60fcbcfa3a965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100141"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Caricare dati per processi Apache Hadoop in HDInsight

Azure HDInsight include un file system HDFS (Hadoop Distributed File System) completo su Archiviazione di Azure e Azure Data Lake Storage (Gen1 e Gen2). Archiviazione di Azure e Data Lake Storage Gen1 e Gen2 sono stati progettati come estensioni Hadoop Distributed File System (HDFS) per offrire un'esperienza lineare ai clienti. Abilitano il set completo di componenti nell'ecosistema Hadoop, con possibilità di agire direttamente sui dati gestiti da Hadoop stesso. Archiviazione di Azure e Data Lake Storage Gen1 e Gen2 sono file system distinti, ottimizzati per l'archiviazione di dati e per l'esecuzione di calcoli su di essi. Per informazioni sui vantaggi dell'uso di Archiviazione di Azure, vedere [Usare Archiviazione di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md), Usare Data Lake Storage [Gen1 con HDInsight](hdinsight-hadoop-use-data-lake-store.md)e [Usare Data Lake Storage Gen2 con HDInsight.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, tenere presenti i requisiti seguenti:

* Disporre di un cluster HDInsight di Azure. Per istruzioni vedere [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) o [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Conoscenza degli articoli seguenti:
    * [Usare Archiviazione di Azure con HDInsightUse Azure Storage with HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Usare Data Lake Store con cluster Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Usare l'anteprima di Archiviazione di Azure Data Lake Gen2 con cluster Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Caricare i dati in Archiviazione di Azure

## <a name="utilities"></a>Servizi pubblici

Microsoft fornisce le utilità seguenti da usare con Archiviazione di Azure:

| Strumento | Linux | OS X | WINDOWS |
| --- |:---:|:---:|:---:|
| [Portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interfaccia della riga di comando di AzureAzure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [Copia Azzurra](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Comando Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> Il comando Hadoop è disponibile solo nel cluster HDInsight. Il comando consente solo il caricamento dei dati dal file system locale in Archiviazione di Azure.  

## <a name="hadoop-command-line"></a><a id="commandline"></a>Riga di comando di Hadoop

La riga di comando di Hadoop è utile solo per archiviare i dati nel BLOB di archiviazione di Azure quando i dati sono già presenti nel nodo head del cluster.

Per utilizzare il comando Hadoop , è necessario prima connettersi al nodo head utilizzando [SSH o PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Dopo essersi connessi, è possibile usare la sintassi seguente per caricare un file nell'archiviazione.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Ad esempio, usare `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Poiché il file system predefinito per HDInsight si trova in Archiviazione di Azure, /example/data/data.txt si trova effettivamente in Archiviazione di Azure.Because the default file system for HDInsight is in Azure Storage, /example/data/data.txt is actually in Azure Storage. È inoltre possibile fare riferimento al file come segue:

    wasbs:///example/data/data.txt

o

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Per un elenco di altri comandi Hadoop che funzionano con i file, vedere[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Nei cluster Apache HBase la dimensione di blocco predefinita usata per la scrittura dei dati è 256 KB. Questa impostazione non costituisce un problema quando si usano API HBase o REST, ma l'uso dei comandi `hadoop` o `hdfs dfs` per scrivere dati di dimensioni superiori a ~12 GB provoca un errore. Per altre informazioni, vedere la sezione [Eccezione di archiviazione per la scrittura nel BLOB](#storageexception) più avanti in questo articolo.

## <a name="graphical-clients"></a>Client con interfaccia grafica

Esistono diverse applicazioni che forniscono un'interfaccia grafica per usare Archiviazione di Azure. Nella tabella seguente è riportato un elenco di alcune di queste applicazioni:

| Client | Linux | OS X | WINDOWS |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools per HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Esplora archivi di AzureAzure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer per Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Montare Archiviazione di Azure come unità locale

Vedere [Montare Archiviazione di Azure come unità locale](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Caricamento tramite servizi

### <a name="azure-data-factory"></a>Data factory di Azure

Data factory di Azure è un servizio completamente gestito per la composizione di servizi di archiviazione, elaborazione e spostamento dei dati in pipeline di produzione dei dati ottimizzate, scalabili e affidabili.

|Tipo di archiviazione|Documentazione|
|----|----|
|Archiviazione BLOB di Azure|[Copiare dati in o dall'archiviazione BLOB di Azure usando Azure Data FactoryCopy data to or from Azure Blob storage by using Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Copiare dati da e in Azure Data Lake Storage di 1° generazione usando Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Caricare dati in Azure Data Lake Storage Gen2 con Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a><a id="sqoop"></a>Apache Sqoop

Sqoop è uno strumento progettato per il trasferimento di dati tra Hadoop e i database relazionali. Può essere usato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Hive e quindi esportarli nuovamente in un sistema RDBMS.

Per altre informazioni, vedere [Usare Sqoop con HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>SDK di sviluppo

È possibile accedere ad Archiviazione di Microsoft Azure anche tramite un SDK di Azure dai linguaggi di programmazione seguenti:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Per altre informazioni sull'installazione di SDK di Azure, vedere [Download di Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="storage-exception-for-write-on-blob"></a><a id="storageexception"></a>Eccezione di archiviazione per la scrittura nel BLOB

**Sintomi**: quando si usa il comando `hadoop` o `hdfs dfs` per scrivere file di dimensioni pari o superiori a ~12 GB in un cluster HBase, è possibile che si verifichi l'errore seguente:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Causa:** HBase nei cluster HDInsight ha una dimensione di blocco di 256 KB durante la scrittura nell'archiviazione di Azure.For : HBase on HDInsight clusters default to a block size of 256 KB when writing to Azure storage. Benché questa impostazione sia ottimale per le API HBase o REST, genera un errore se si usano le utilità da riga di comando `hadoop` o `hdfs dfs`.

**Risoluzione**: usare `fs.azure.write.request.size` per specificare dimensioni maggiori per i blocchi. È possibile eseguire questa operazione per i singoli utenti usando il parametro `-D`. Il comando seguente è un esempio di utilizzo di questo parametro con il comando `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

È anche possibile aumentare globalmente il valore di `fs.azure.write.request.size` usando Apache Ambari. La procedura seguente consente di cambiare il valore nell'interfaccia utente Web di Ambari:

1. Nel browser passare all'interfaccia utente Web di Ambari per il cluster, Si `https://CLUSTERNAME.azurehdinsight.net`tratta `CLUSTERNAME` di , dove è il nome del cluster.

    Quando richiesto, immettere il nome dell'amministratore e la password per il cluster.
2. Sul lato sinistro dello schermo selezionale **HDFS** e quindi fare clic sulla scheda **Configs** (Configurazioni).
3. Nel campo **Filter** (Filtro) immettere `fs.azure.write.request.size`. Il campo e il valore corrente vengono visualizzati al centro della pagina.
4. Modificare il valore da 262144 (256 KB) al nuovo valore. Ad esempio, 4194304 (4 MB).

    ![Immagine della modifica del valore tramite l'interfaccia utente Web di Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Per altre informazioni sull'uso di Ambari, vedere [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come importare dati in HDInsight, leggere gli articoli seguenti per informazioni sull'esecuzione di analisi:

* [Introduzione ad Azure HDInsightGet started with Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Inviare processi Apache Hadoop a livello di codice](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hadoop/hdinsight-use-pig.md)
