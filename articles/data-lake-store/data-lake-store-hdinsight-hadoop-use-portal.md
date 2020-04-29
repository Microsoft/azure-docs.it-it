---
title: Creare cluster HDInsight di Azure con Data Lake Storage Gen1-Portal
description: Usare il portale di Azure per creare e usare cluster HDInsight con Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265571"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Creare cluster HDInsight con Azure Data Lake Storage Gen1 tramite il portale di Azure

> [!div class="op_single_selector"]
> * [Usare il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usare PowerShell (per l'archiviazione predefinita)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usare PowerShell (per l'archiviazione aggiuntiva)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usare Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Informazioni su come usare il portale di Azure per creare un cluster HDInsight con un account Azure Data Lake Storage Gen1 come risorsa di archiviazione predefinita o risorsa di archiviazione aggiuntiva. Anche se l'archiviazione aggiuntiva è facoltativa per un cluster HDInsight, è consigliabile archiviare i dati aziendali negli account di archiviazione aggiuntivi.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di aver soddisfatto i requisiti seguenti:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account data Lake storage Gen1**. Seguire le istruzioni fornite in [Introduzione all'uso di Azure Data Lake Storage Gen1 tramite il portale di Azure](data-lake-store-get-started-portal.md). È anche necessario creare una cartella radice nell'account.  In questo articolo viene usata una cartella radice denominata __/Clusters__ .
* **Un'entità servizio di Azure Active Directory**. Questa guida alle procedure fornisce istruzioni su come creare un'entità servizio in Azure Active Directory (Azure AD). Tuttavia, per creare un'entità servizio è necessario essere un amministratore di Azure AD. Se si è un amministratore, è possibile ignorare questo prerequisito e continuare.

>[!NOTE]
>È possibile creare un'entità servizio solo se si è un amministratore Azure AD. Prima di poter creare un cluster HDInsight con Data Lake Storage Gen1, un amministratore di Azure AD deve creare un'entità servizio. Inoltre, l'entità servizio deve essere creata usando un certificato, come descritto in [Creare un'entità servizio con certificato](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Creazione di un cluster HDInsight

In questa sezione viene creato un cluster HDInsight con account Data Lake Storage Gen1 come risorsa di archiviazione predefinita o aggiuntiva. Questo articolo è incentrato solo sulla configurazione di account Data Lake Storage Gen1. Per informazioni generali sulla creazione di cluster e le relative procedure, vedere [Creare cluster Hadoop basati su Linux in HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Creare un cluster con Data Lake Storage Gen1 come risorsa di archiviazione predefinita

Per creare un cluster HDInsight con un account Data Lake Storage Gen1 come account di archiviazione predefinito:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Seguire [Creare i cluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) per informazioni generali sulla creazione di cluster HDInsight.
3. Nel pannello **Archiviazione**, in **Tipo di archiviazione primario** selezionare **Azure Data Lake Storage Gen1** e quindi immettere le informazioni seguenti:

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Aggiungere entità servizio a cluster HDInsight")

    * **Seleziona account Data Lake Store**: selezionare un account Azure Data Lake Storage Gen1 esistente. È necessario un account Azure Data Lake Storage Gen1 esistente.  Vedere [Prerequisiti](#prerequisites).
    * **Percorso radice**: immettere un percorso in cui archiviare i file specifici del cluster. Nello screenshot è __/clusters/myhdiadlcluster/__, in cui la cartella __/clusters__ deve esistere e il portale crea la cartella *myhdicluster*.  *myhdicluster* è il nome del cluster.
    * **Accesso a Data Lake Store**: configurare l'accesso tra l'account Data Lake Storage Gen1 e il cluster HDInsight. Per istruzioni, vedere [Configurare l'accesso a Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).
    * **Account**di archiviazione aggiuntivi: aggiungere account di archiviazione di Azure come account di archiviazione aggiuntivi per il cluster. Per aggiungere altri account Data Lake Storage Gen1, assegnare al cluster le autorizzazioni per i dati in più account Data Lake Storage Gen1 durante la configurazione di un account Data Lake Storage Gen1 come tipo di archiviazione primario. Vedere [Configurare l'accesso a Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

4. In **Accesso a Data Lake Store** fare clic su **Seleziona** e continuare con la creazione del cluster come descritto in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Creare un cluster con Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva

Le istruzioni seguenti illustrano come creare un cluster HDInsight con un account di archiviazione di Azure come risorsa di archiviazione predefinita e un account Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva.

Per creare un cluster HDInsight con un account Data Lake Storage Gen1 come account di archiviazione aggiuntivo:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Seguire [Creare i cluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) per informazioni generali sulla creazione di cluster HDInsight.
3. Nel pannello **Archiviazione**, in **Tipo di archiviazione primario** selezionare **Archiviazione di Azure** e quindi immettere le informazioni seguenti:

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Aggiungere entità servizio a cluster HDInsight")

    * **Metodo di selezione** : per specificare un account di archiviazione che fa parte della sottoscrizione di Azure, selezionare **sottoscrizioni personali**e quindi selezionare l'account di archiviazione. Per specificare un account di archiviazione esterno alla sottoscrizione di Azure, selezionare **Chiave di accesso**, quindi immettere le informazioni per l'account di archiviazione esterno.

    * **Contenitore predefinito** : usare il valore predefinito o specificare un nome personalizzato.
    * **Account di archiviazione aggiuntivi** : aggiungere altri account di archiviazione di Azure come risorsa di archiviazione aggiuntiva.
    * Accesso **Data Lake Store** : configurare l'accesso tra l'account data Lake storage Gen1 e il cluster HDInsight. Per istruzioni, vedere [configurare l'accesso data Lake storage Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Configurare l'accesso a Data Lake Storage Gen1

In questa sezione è possibile configurare l'accesso a Data Lake Storage Gen1 dai cluster HDInsight usando un'entità servizio di Azure Active Directory.

### <a name="specify-a-service-principal"></a>Specificare un'entità servizio

Dal portale di Azure è possibile usare un'entità servizio esistente o crearne una nuova.

Per creare un'entità servizio dal portale di Azure:

1. Selezionare **accesso data Lake Store** dal pannello archiviazione.
1. Nel pannello **accesso data Lake storage Gen1** selezionare **Crea nuovo**.
1. Selezionare **entità servizio**, quindi seguire le istruzioni per creare un'entità servizio.
1. Scaricare il certificato se si decide di usarlo ancora in futuro. Il download del certificato è un'operazione utile se in futuro si vorrà usare la stessa entità servizio per creare altri cluster HDInsight.

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Aggiungere entità servizio a cluster HDInsight")

1. Selezionare **accesso** per configurare l'accesso alla cartella.  Vedere [Configurare le autorizzazioni file](#configure-file-permissions).

Per usare un'entità servizio esistente dal portale di Azure:

1. Selezionare **Data Lake Store accesso**.
1. Nel pannello **accesso data Lake storage Gen1** selezionare **Usa esistente**.
1. Selezionare **entità servizio**, quindi selezionare un'entità servizio.
1. Caricare il certificato (file PFX) associato all'entità servizio selezionata e quindi immettere la password del certificato.

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Aggiungere entità servizio a cluster HDInsight")

1. Selezionare **accesso** per configurare l'accesso alla cartella.  Vedere [Configurare le autorizzazioni file](#configure-file-permissions).

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Configurare le autorizzazioni file

La configurazione varia a seconda che l'account venga usato come risorsa di archiviazione predefinita o come account di archiviazione aggiuntivo:

* Uso come risorsa di archiviazione predefinita

  * Autorizzazione a livello di radice dell'account Data Lake Storage Gen1
  * Autorizzazione a livello di radice dell'archiviazione cluster HDInsight. Ad esempio, la cartella __/clusters__ usata prima nell'esercitazione.

* Uso come risorsa di archiviazione aggiuntiva

  * Autorizzazione a livello delle cartelle in cui è necessario l'accesso ai file.

Per assegnare l'autorizzazione a livello di radice dell'account Data Lake Storage Gen1:

1. Nel pannello **accesso data Lake storage Gen1** selezionare **accesso**. Viene aperto il pannello per la **Selezionare le autorizzazioni file**. Questo pannello elenca tutti gli account Data Lake Storage Gen1 nella sottoscrizione.
1. Passare il mouse (non fare clic) sul nome dell'account Data Lake Storage Gen1 per rendere visibile la casella di controllo e selezionarla.

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Aggiungere entità servizio a cluster HDInsight")

   Per impostazione predefinita, le impostazioni di __lettura__, __scrittura__ed __esecuzione__ sono tutte selezionate.

1. Fare clic su **Seleziona** nella parte inferiore della pagina.
1. Selezionare **Esegui** per assegnare l'autorizzazione.
1. Seleziona **Chiudi**.

Per assegnare l'autorizzazione a livello di radice del cluster HDInsight:

1. Nel pannello **accesso data Lake storage Gen1** selezionare **accesso**. Viene aperto il pannello per la **Selezionare le autorizzazioni file**. Questo pannello elenca tutti gli account Data Lake Storage Gen1 nella sottoscrizione.
1. Nel pannello **selezionare le autorizzazioni** per i file selezionare il nome dell'account data Lake storage Gen1 per visualizzarne il contenuto.
1. Selezionare la radice di archiviazione cluster HDInsight selezionando la casella di controllo a sinistra della cartella. In base allo screenshot precedente, la radice di archiviazione del cluster è la cartella __/clusters__ specificata durante la selezione di Data Lake Storage Gen1 come risorsa di archiviazione predefinita.
1. Impostare le autorizzazioni per la cartella.  Per impostazione predefinita, sono selezionate lettura, scrittura ed esecuzione.
1. Fare clic su **Seleziona** nella parte inferiore della pagina.
1. Selezionare **Run** (Esegui).
1. Seleziona **Chiudi**.

Se si usa Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva, è necessario assegnare autorizzazioni solo per le cartella a cui si vuole accedere dal cluster HDInsight. Ad esempio, nella schermata seguente si garantisce l'accesso solo alla cartella **mynewfolder** in un account Data Lake Storage Gen1.

![Assegnare le autorizzazioni dell'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Assegnare le autorizzazioni dell'entità servizio al cluster HDInsight")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Verificare la configurazione del cluster

Al termine della configurazione del cluster, nel pannello del cluster verificare i risultati eseguendo uno o entrambi i passaggi seguenti:

* Per verificare che la risorsa di archiviazione associata per il cluster sia l'account Data Lake Storage Gen1 specificato, selezionare **account di archiviazione** nel riquadro sinistro.

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Aggiungere entità servizio a cluster HDInsight")

* Per verificare che l'entità servizio sia associata correttamente al cluster HDInsight, selezionare **Data Lake storage Gen1 accesso** nel riquadro sinistro.

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Aggiungere entità servizio a cluster HDInsight")

## <a name="examples"></a>Esempi

Dopo aver configurato il cluster con Data Lake Storage Gen1 come risorsa di archiviazione, vedere questi esempi di come usare il cluster HDInsight per analizzare i dati archiviati nel Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Eseguire una query Hive sui dati archiviati in un account Data Lake Storage Gen1 (come risorsa di archiviazione primaria)

Per eseguire una query Hive, usare l'interfaccia delle visualizzazioni Hive disponibile nel portale di Ambari. Per istruzioni su come usare le visualizzazioni Hive di Ambari, vedere [Usare la visualizzazione Hive con Hadoop in HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Quando si usano dati in un account Data Lake Storage Gen1, è necessario modificare alcune stringhe.

Ad esempio, se si usa il cluster creato con Data Lake Storage Gen1 come risorsa di archiviazione primaria, il percorso dei dati è: *adl://<nome_account_data_lake_storage_gen1>/azuredatalakestore.net/path/to/file*. Una query Hive per creare una tabella dai dati di esempio archiviati nell'account Data Lake Storage Gen1 avrà un aspetto simile all'istruzione seguente:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descrizioni:

* `adl://hdiadlsg1storage.azuredatalakestore.net/` è la radice dell'account Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` è la radice dei dati del cluster specificata durante la creazione del cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` è il percorso del file di esempio usato nella query.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Eseguire una query Hive sui dati archiviati in un account Data Lake Storage Gen1 (come risorsa di archiviazione aggiuntiva)

Se il cluster creato usa l'archiviazione BLOB come risorsa di archiviazione predefinita, i dati di esempio non si troveranno nell'account Data Lake Storage Gen1 usato come risorsa di archiviazione aggiuntiva. In questi casi, trasferire innanzitutto i dati dall'archiviazione BLOB all'account Data Lake Storage Gen1 e quindi eseguire le query come mostrato nell'esempio precedente.

Per informazioni su come copiare dati dall'archiviazione BLOB a un account Data Lake Storage Gen1, vedere gli articoli seguenti:

* [Usare Distcp per copiare dati tra i BLOB di archiviazione di Azure e Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Usare lo strumento AdlCopy per copiare i dati da BLOB di archiviazione di Azure a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Usare Data Lake Storage Gen1 con un cluster Spark

È possibile usare un cluster Spark per eseguire processi Spark sui dati archiviati in un account Data Lake Storage Gen1. Per altre informazioni, vedere [usare il cluster HDInsight Spark per analizzare i dati in data Lake storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Usare Data Lake Storage Gen1 in una topologia Storm

È possibile usare l'account Data Lake Storage Gen1 per scrivere dati da una topologia Storm. Per istruzioni su come ottenere questo scenario, vedere [Usare Azure Data Lake Storage Gen1 con Apache Storm in HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Vedere anche

* [Usare Data Lake Storage Gen1 con i cluster Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Creare un cluster HDInsight per usare Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
