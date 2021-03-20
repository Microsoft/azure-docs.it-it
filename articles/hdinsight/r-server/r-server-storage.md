---
title: Soluzioni di archiviazione di Azure per i servizi ML in HDInsight-Azure
description: Informazioni sulle diverse opzioni di archiviazione disponibili per gli utenti con ML Services in HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/02/2020
ms.openlocfilehash: ddc48025de164ff68fb539a293e06bae09171742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943914"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Soluzioni di archiviazione di Azure per i servizi ML in Azure HDInsight

ML Services in HDInsight può usare soluzioni di archiviazione diverse per salvare in modo permanente i dati, il codice o gli oggetti che contengono risultati dall'analisi. Queste soluzioni includono le opzioni seguenti:

- [Archivio BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Archiviazione file di Azure](https://azure.microsoft.com/services/storage/files/)

È possibile anche accedere a più account di archiviazione o contenitori di Azure con il cluster HDInsight. Archiviazione file di Azure è un'opzione di archiviazione dati comoda da usare nel nodo perimetrale che consente di montare una condivisione file di archiviazione di Azure in, ad esempio, il file system Linux. Tuttavia, le condivisioni File di Azure possono essere montate e usate in qualsiasi sistema dotato di un sistema operativo supportato, ad esempio Windows o Linux.

Quando si crea un cluster Apache Hadoop in HDInsight, si specifica un account di **archiviazione BLOB di Azure** o **Data Lake storage Gen1**. Un contenitore di archiviazione specifico dell'account include il file system del cluster creato, ad esempio Hadoop Distributed File System. Per altre informazioni e istruzioni, vedere:

- [Usare l'archivio BLOB di Azure con HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Usare Data Lake Storage Gen1 con i cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Usare gli account di archiviazione BLOB di Azure con il cluster ML Services

Se durante la creazione del cluster ML Services è stato specificato più di un account di archiviazione, le istruzioni seguenti illustrano come usare un account secondario per l'accesso ai dati e le operazioni nel cluster ML Services. Si suppongano il contenitore e gli account di archiviazione seguenti: **storage1**, un contenitore predefinito denominato **container1** e **storage2** con **container2**.

> [!WARNING]  
> Per motivi di prestazioni, il cluster HDInsight viene creato nello stesso data center dell'account di archiviazione primario specificato. L'uso di un account di archiviazione in una località diversa rispetto al cluster HDInsight non è supportato.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Usare l'archiviazione predefinita con ML Services in HDInsight

1. Usando un client SSH, connettersi al nodo perimetrale del cluster. Per altre informazioni sull'uso di SSH con cluster HDInsight, vedere l'articolo [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Copiare un file di esempio, mysamplefile.csv, nella directory /share.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Passare a R Studio o un'altra console R e scrivere il codice R per impostare il nodo dei nomi su **default** e il percorso del file a cui si vuole accedere.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

Tutti i riferimenti a file e directory puntano all'account di archiviazione `wasbs://container1@storage1.blob.core.windows.net`. Si tratta dell'**account di archiviazione predefinito** associato al cluster HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Usare l'archiviazione aggiuntiva con ML Services in HDInsight

Si supponga ora di voler elaborare un file denominato mysamplefile1.csv, che si trova nella directory /private di **container2** in **storage2**.

Nel codice R puntare il nome del riferimento al nodo sull'account di archiviazione **storage2** .

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Tutti i riferimenti a file e directory ora puntano all'account di archiviazione `wasbs://container2@storage2.blob.core.windows.net`. Questo è il **nodo del nome** specificato.

Configurare la `/user/RevoShare/<SSH username>` directory in **Storage2** nel modo seguente:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-gen1-with-ml-services-cluster"></a>Usare Azure Data Lake Storage Gen1 con il cluster di servizi ML

Per usare Data Lake Storage Gen1 con il cluster HDInsight, è necessario concedere al cluster l'accesso a ogni Azure Data Lake Storage Gen1 che si vuole usare. Per istruzioni su come usare la portale di Azure per creare un cluster HDInsight con un Azure Data Lake Storage Gen1 come risorsa di archiviazione predefinita o come risorsa di archiviazione aggiuntiva, vedere [creare un cluster HDInsight con data Lake storage Gen1 usando portale di Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Usare quindi la risorsa di archiviazione nello script R come un account di archiviazione Azure secondario, come descritto nella procedura precedente.

### <a name="add-cluster-access-to-your-azure-data-lake-storage-gen1"></a>Aggiunta dell'accesso al cluster all'Azure Data Lake Storage Gen1

È possibile accedere a Data Lake Storage Gen1 usando un'entità servizio Azure Active Directory (Azure AD) associata al cluster HDInsight.

1. Quando si crea il cluster HDInsight, selezionare **cluster Azure ad Identity** dalla scheda **origine dati** .

2. Nella finestra di dialogo **identità Azure AD cluster** selezionare **Crea nuovo** in **selezionare un'entità servizio ad**.

Dopo aver assegnato un nome all'entità servizio, creare una password per tale entità e fare clic su **Gestisci l'accesso ad Archivio Azure Data Lake** per associare l'entità servizio a Data Lake Storage.

È anche possibile aggiungere l'accesso al cluster a uno o più account Data Lake storage Gen1 in seguito alla creazione del cluster. Aprire la voce portale di Azure per un Data Lake Storage Gen1 e passare a **Esplora dati > Access > Aggiungi**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Come accedere a Data Lake Storage Gen1 da ML Services in HDInsight

Una volta ottenuto l'accesso alla Data Lake Storage Gen1, è possibile usare l'archiviazione nel cluster di servizi ML in HDInsight come si farebbe con un account di archiviazione di Azure secondario. L'unica differenza è che il prefisso **wasbs://** viene modificato in **ADL://** come indicato di seguito:

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

I comandi seguenti vengono usati per configurare la Data Lake Storage Gen1 con la directory RevoShare e aggiungere il file CSV di esempio dall'esempio precedente:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Usare Archiviazione file di Azure con ML Services in HDInsight

È anche disponibile un'opzione di archiviazione dati comoda da usare nel nodo perimetrale denominato [file di Azure](https://azure.microsoft.com/services/storage/files/). Consente di montare una condivisione file di Archiviazione di Azure nel file system Linux. Questa opzione può essere utile per l'archiviazione di file di dati, script R e oggetti risultato che potrebbero essere necessari in seguito, soprattutto quando sarà opportuno usare il file system nativo nel nodo perimetrale invece di HDFS.

Un vantaggio importante di File di Azure riguarda la possibilità di montare e usare le condivisioni file in qualsiasi sistema dotato di un sistema operativo supportato, ad esempio Windows o Linux. Ad esempio, può essere usato da un altro cluster HDInsight disponibile all'utente o a un membro del team, da una macchina virtuale di Azure o anche da un sistema locale. Per altre informazioni, vedere:

- [Come usare archiviazione file di Azure con Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Come usare Archiviazione file di Azure su Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica del cluster ML Services in HDInsight](r-server-overview.md)
- [Opzioni del contesto di calcolo per un cluster ML Services su HDInsight](r-server-compute-contexts.md)
- [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
