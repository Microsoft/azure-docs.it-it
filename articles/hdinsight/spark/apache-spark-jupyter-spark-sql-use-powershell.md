---
title: 'Guida introduttiva: Creare un cluster Apache Spark in Azure HDInsight con Azure PowerShell'
description: Questa guida di avvio rapido illustra come usare Azure PowerShell per creare un cluster Apache Spark in HDInsight di Azure ed eseguire una query Spark SQL semplice.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc
ms.openlocfilehash: 7416c25128da8dcaf803a9f03144110941200ab2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77049133"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>Guida introduttiva: Creare cluster Apache Spark in Azure HDInsight usando PowerShell

In questa guida di avvio rapido si usa Azure PowerShell per creare un cluster Apache Spark in Azure HDInsight. Creare quindi un notebook Jupyter e usarlo per eseguire query Spark SQL su tabelle Apache Hive. Azure HDInsight è un servizio di analisi open source, gestito e ad ampio spettro per le aziende. Il framework Apache Spark per Azure HDInsight consente di velocizzare cluster computing e analisi dei dati grazie all'elaborazione in memoria. Il notebook Jupyter consente di interagire con i dati, combinare codice e testo Markdown ed eseguire visualizzazioni semplici.

[Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter Notebook](https://jupyter.org/)

## <a name="prerequisite"></a>Prerequisito

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Il [modulo Az di PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) installato.

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Creare un cluster Apache Spark in HDInsight

> [!IMPORTANT]  
> La fatturazione dei cluster HDInsight viene calcolata al minuto, indipendentemente dal fatto che siano in uso o meno. Assicurarsi di eliminare il cluster dopo aver finito di usarlo. Per altre informazioni, vedere la sezione [Pulire le risorse](#clean-up-resources) di questo articolo.

La creazione di un cluster HDInsight include la creazione dei seguenti oggetti e risorse di Azure:

- Un gruppo di risorse di Azure. Un gruppo di risorse di Azure è un contenitore per le risorse di Azure.
- Un account di archiviazione di Azure o Azure Data Lake Storage.  Ogni cluster HDInsight richiede una risorsa di archiviazione per i dati dipendenti. In questa guida di avvio rapido viene creato un cluster che usa BLOB del servizio di archiviazione di Azure come risorsa di archiviazione del cluster. Per altre informazioni sull'uso di Data Lake Storage Gen2, vedi [Avvio rapido: Impostazione dei cluster in HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
- Un cluster di tipi di cluster diversi in HDInsight.  In questa guida introduttiva verrà creato un cluster Spark 2.3.

Per creare le risorse si usa uno script di PowerShell. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Quando si esegue lo script PowerShell, viene chiesto di immettere i valori seguenti:

|Parametro|valore|
|------|------|
|Nome del gruppo di risorse di Azure | Immettere un nome univoco per il gruppo di risorse.|
|Location| Specificare l'area di Azure, ad esempio "Stati Uniti centrali". |
|Nome dell'account di archiviazione predefinito | Fornire un nome univoco per l'account di archiviazione. |
|Nome cluster | Specificare un nome univoco per il cluster HDInsight.|
|Credenziali di accesso del cluster | Questo account viene usato più avanti in questa guida di avvio rapido per connettersi al dashboard del cluster.|
|Credenziali dell'utente SSH | I client SSH possono essere usati per creare una sessione remota della riga di comando con i cluster in HDInsight.|

1. Selezionare **Prova** nell'angolo superiore destro per il blocco di codice seguente per aprire [Azure Cloud Shell](../../cloud-shell/overview.md) e seguire le istruzioni per la connessione ad Azure.

2. Copiare e incollare lo script PowerShell seguente in Cloud Shell.

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value

    $defaultStorageContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the cluster in HDInsight
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   La creazione del cluster richiede circa 20 minuti. Prima di procedere con la sessione successiva, è necessario creare il cluster.

Se si verifica un problema durante la creazione di cluster HDInsight, è possibile che non si abbiano le autorizzazioni necessarie per eseguire questa operazione. Per altre informazioni, vedere [Requisiti di controllo di accesso](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Creare un notebook Jupyter

[Jupyter Notebook](https://jupyter.org/) è un ambiente notebook interattivo che supporta diversi linguaggi di programmazione. Il notebook consente di interagire con i dati, combinare codice e testo Markdown ed eseguire visualizzazioni semplici.

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **cluster HDInsight**.
   
   ![Aprire il cluster HDInsight nel portale di Azure](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-search-hdinsight-cluster.png)
   
1. Nell'elenco selezionare il cluster creato.
   
   ![Aprire il cluster HDInsight nel portale di Azure](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-open-hdinsight-cluster.png)
   
1. Nella pagina **Panoramica** del cluster selezionare **Dashboard cluster** e quindi selezionare **Jupyter Notebook**. Se richiesto, immettere le credenziali di accesso del cluster.

   ![Aprire Jupyter Notebook per eseguire una query Spark SQL interattiva](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Aprire Jupyter Notebook per eseguire una query Spark SQL interattiva")

1. Per creare un notebook selezionare **Nuovo** > **PySpark**.

   ![Creare un Jupyter Notebook per eseguire una query Spark SQL interattiva](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Creare un Jupyter Notebook per eseguire una query Spark SQL interattiva")

   Un nuovo notebook verrà creato e aperto con il nome Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Eseguire le istruzioni Apache Spark SQL

SQL (Structured Query Language) è il linguaggio più diffuso e più usato per l'esecuzione di query e la definizione dei dati. Spark SQL funziona come estensione di Apache Spark per l'elaborazione dei dati strutturati, usando la nota sintassi SQL.

1. Verificare che il kernel sia pronto. Il kernel è pronto quando accanto al relativo nome nel notebook viene visualizzato un cerchio vuoto. Un cerchio pieno indica che il kernel è occupato.

    ![stato del kernel](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "stato del kernel")

    Quando si avvia il notebook per la prima volta, il kernel esegue alcune attività in background. Attendere che il kernel sia pronto. 
1. Incollare il codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO** per eseguire il codice. Il comando elenca le tabelle Hive sul cluster:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Quando si usa un notebook Jupyter con il cluster Spark in HDInsight, si ottiene un elemento `sqlContext` predefinito che può essere usato per eseguire query Hive con Spark SQL. `%%sql` indica a Jupyter Notebook di usare l'elemento `sqlContext` predefinito per eseguire la query Hive. La query recupera le prime 10 righe di una tabella Hive (**hivesampletable**) disponibile per impostazione predefinita in tutti i cluster HDInsight. Per ottenere i risultati sono necessari circa 30 secondi. L'output è simile al seguente:

    ![Query Apache Hive in Spark in HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query.png "Query Hive in HDInsight Spark")

    Ogni volta che si esegue una query in Jupyter, il titolo della finestra del Web browser visualizza lo stato **(Occupato)** accanto al titolo del notebook. È anche visibile un cerchio pieno accanto al testo **PySpark** nell'angolo in alto a destra.

1. Eseguire un'altra query per visualizzare i dati in `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    La schermata si aggiornerà per visualizzare l'output della query.

    ![Output di query Hive in HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query-output.png "Output di query Hive in HDInsight")

1. Nel menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi). Quando il notebook viene arrestato, le risorse del cluster vengono rilasciate.

## <a name="clean-up-resources"></a>Pulire le risorse

HDInsight salva i dati in Archiviazione di Azure o in Azure Data Lake Storage in modo che sia possibile eliminare senza problemi un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. Se si prevede di svolgere subito l'esercitazione elencata nei [passaggi successivi](#next-steps), si può mantenere il cluster.

Tornare al portale di Azure e selezionare **Elimina**.

![Eliminazione di un cluster HDInsight nel portale di Azure](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-azure-portal-delete-cluster.png "Eliminare un cluster HDInsight")

È anche possibile selezionare il nome del gruppo di risorse per aprire la pagina del gruppo di risorse e quindi selezionare **Elimina gruppo di risorse**. Eliminando il gruppo di risorse, si elimina sia il cluster HDInsight che l'account di archiviazione predefinito.

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>Pulire a fasi con il modulo Az di PowerShell

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un cluster Apache Spark in HDInsight ed eseguire una query Spark SQL di base. Passare all'esercitazione successiva per imparare come usare un cluster HDInsight per eseguire query interattive su dati di esempio.

> [!div class="nextstepaction"]
> [Eseguire query interattive su Apache Spark](./apache-spark-load-data-run-query.md)
