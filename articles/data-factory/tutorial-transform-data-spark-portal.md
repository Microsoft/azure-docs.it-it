---
title: 'Trasformare i dati con Spark in Azure Data Factory '
description: Questa esercitazione offre istruzioni dettagliate per la trasformazione dei dati tramite l'attività Spark in Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 5b0bcdd66e17fb93a560b6073c13e3170e3ab37b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409253"
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Trasformare dati nel cloud usando un'attività Spark in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questa esercitazione si usa il portale di Azure per creare una pipeline di Azure Data Factory. Questa pipeline trasforma i dati usando un'attività Spark e un servizio collegato su richiesta di Azure HDInsight. 

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory. 
> * Creare una pipeline che usa un'attività Spark.
> * Attivare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Account di archiviazione di Azure**. Creare uno script Python e un file di input, quindi caricarli in Archiviazione di Azure. L'output del programma Spark viene archiviato in questo account di archiviazione. Il cluster Spark su richiesta usa lo stesso account di archiviazione come risorsa di archiviazione primaria.  

> [!NOTE]
> HDInsight supporta solo gli account di archiviazione per utilizzo generico di livello Standard. Assicurarsi che l'account non sia un account di archiviazione Premium o solo BLOB.

* **Azure PowerShell**. Seguire le istruzioni in [Come installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps).


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Caricare lo script Python nell'account di archiviazione BLOB
1. Creare un file Python denominato **WordCount_Spark.py** con il contenuto seguente: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
1. Sostituire *&lt;storageAccountName&gt;* con il nome del proprio account di archiviazione di Azure. Salvare quindi il file. 
1. Nell'Archivio BLOB di Azure creare un contenitore denominato **adftutorial**, se non esiste. 
1. Creare una cartella denominata **spark**.
1. Creare una sottocartella denominata **script** nella cartella **spark**. 
1. Caricare il file **WordCount_Spark.py** nella sottocartella **script**. 


### <a name="upload-the-input-file"></a>Caricare il file di input
1. Creare un file denominato **minecraftstory.txt** con del testo. Il programma Spark conta il numero di parole in questo testo. 
1. Creare una sottocartella denominata **inputfiles** nella cartella **spark**. 
1. Caricare il file **minecraftstory.txt** nella sottocartella **inputfiles**. 

## <a name="create-a-data-factory"></a>Creare una data factory

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Scegliere **Nuovo** dal menu a sinistra, selezionare **Dati e analisi** e quindi selezionare **Data Factory**. 
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
1. Nel riquadro **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 
      
   ![Riquadro "Nuova data factory"](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato l'errore seguente, cambiare il nome della data factory. Usare, ad esempio **&lt;nomeutente&gt;ADFTutorialDataFactory**. Per le regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
   ![Errore quando un nome non è disponibile](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
1. Per **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
1. In **Gruppo di risorse** eseguire una di queste operazioni:
     
   - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
   - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
   Alcuni dei passaggi di questa guida introduttiva presuppongono l'uso del nome **ADFTutorialResourceGroup** per il gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/management/overview.md).  
1. Per **Versione** selezionare **V2**.
1. Per **Località** selezionare la località per la data factory. 

   Per un elenco di aree di Azure in cui Data Factory è attualmente disponibile, selezionare le aree di interesse nella pagina seguente, quindi espandere **Analytics** per individuare **Data Factory**: [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL di Azure) e le risorse di calcolo (ad esempio HDInsight) usati da Data Factory possono trovarsi in altre aree.

1. Selezionare **Create** (Crea).

1. Al termine della creazione verrà visualizzata la pagina **Data factory**. Selezionare il riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Data Factory in una scheda separata.

    ![Home page della data factory, con il riquadro "Crea e monitora"](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>Creare servizi collegati
In questa sezione vengono creati due servizi collegati: 
    
- Un **servizio collegato Archiviazione di Azure** che collega un account di archiviazione di Azure alla data factory. Questo archivio viene usato dal cluster HDInsight su richiesta. Contiene anche lo script Spark da eseguire. 
- Un **servizio collegato HDInsight su richiesta**. Azure Data Factory crea automaticamente un cluster HDInsight ed esegue il programma Spark. Elimina quindi il cluster HDInsight dopo un tempo di inattività preconfigurato. 

### <a name="create-an-azure-storage-linked-service"></a>Creare un servizio collegato Archiviazione di Azure

1. Nella pagina **Attività iniziali** passare alla scheda **Modifica** nel pannello a sinistra. 

   ![Pagina "Attività iniziali"](./media/tutorial-transform-data-spark-portal/get-started-page.png)

1. Selezionare **Connessioni** nella parte inferiore della finestra e quindi **+ Nuovo**. 

   ![Pulsanti per la creazione di una nuova connessione](./media/tutorial-transform-data-spark-portal/new-connection.png)
1. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Archivio dati** > **Archiviazione BLOB di Azure** e quindi selezionare **Continua**. 

   ![Selezione del riquadro "Archiviazione BLOB di Azure"](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
1. Per **Nome account di archiviazione** selezionare il nome dell'elenco e quindi selezionare **Salva**. 

   ![Casella per specificare il nome dell'account di archiviazione](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Creare un servizio collegato HDInsight su richiesta

1. Selezionare il pulsante **+ Nuovo** per creare un altro servizio collegato. 
1. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Calcolo** > **Azure HDInsight** e quindi selezionare **Continua**. 

   ![Selezione del riquadro "Azure HDInsight"](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
1. Nella finestra **New Linked Service** (Nuovo servizio collegato) completare questa procedura: 

   a. Per **Nome** immettere **AzureHDInsightLinkedService**.
   
   b. Per **Tipo** verificare che sia selezionata l'opzione **On-demand HDInsight** (HDInsight su richiesta).
   
   c. Per **Azure Storage Linked Service** (Servizio collegato Archiviazione di Azure) selezionare **AzureBlobStorage1**. Questo servizio collegato è stato creato in precedenza. Se è stato usato un nome diverso, specificare il nome corretto qui. 
   
   d. Per **Tipo di cluster** selezionare **spark**.
   
   e. Per **ID entità servizio** immettere l'ID dell'entità servizio autorizzata a creare un cluster HDInsight. 
   
      Questa entità servizio deve essere un membro del ruolo Collaboratore della sottoscrizione o del gruppo di risorse in cui viene creato il cluster. Per altre informazioni, vedere [Creare un'applicazione e un'entità servizio di Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md). L'**ID entità servizio** equivale all'*ID applicazione* e una **chiave entità servizio** equivale al valore di un *segreto client*.
   
   f. Per **Chiave dell'entità servizio** immettere la chiave. 
   
   g. Per **Gruppo di risorse** selezionare lo stesso gruppo di risorse usato durante la creazione della data factory. Il cluster Spark viene creato in questo gruppo di risorse. 
   
   h. Espandere **Tipo di sistema operativo**.
   
   i. Immettere un nome in **Nome dell'utente del cluster**. 
   
   j. Immettere la **password del cluster** per l'utente. 
   
   k. Selezionare **Fine**. 

   ![Impostazioni servizio collegato HDInsight](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight limita il numero totale di core che è possibile usare in ogni area di Azure supportata. Per il servizio collegato HDInsight su richiesta, il cluster HDInsight viene creato nella stessa posizione dell'archivio di Azure usato come risorsa di archiviazione primaria. Assicurarsi che siano disponibili sufficienti quote di core per la creazione del cluster. Per altre informazioni, vedere [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Creare una pipeline

1. Selezionare il pulsante **+** (segno più) e quindi selezionare **Pipeline** dal menu.

   ![Pulsanti per la creazione di una nuova pipeline](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
1. Nella casella degli strumenti **Attività** espandere **HDInsight**. Trascinare l'attività **Spark** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline. 

   ![Trascinamento dell'attività Spark](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
1. Nelle proprietà della finestra dell'attività **Spark** in basso completare questa procedura: 

   a. Passare alla scheda **HDI Cluster** (Cluster HDInsight).
   
   b. Selezionare il servizio **AzureHDInsightLinkedService** creato nella procedura precedente. 
        
   ![Specificare il servizio collegato HDInsight](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
1. Passare alla scheda **Script/Jar** e completare questa procedura: 

   a. Per **Job Linked Service** (Servizio collegato a processo) selezionare **AzureBlobStorage1**.
   
   b. Selezionare **Sfoglia risorsa di archiviazione**.

   ![Specificare lo script Spark nella scheda "Script/Jar"](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   c. Passare alla cartella **adftutorial/spark/script**, selezionare **WordCount_Spark.py** e quindi **Fine**.      

1. Per convalidare la pipeline, selezionare il pulsante **Convalida** sulla barra degli strumenti. Selezionare il pulsante **>>** (freccia destra) per chiudere la finestra di convalida. 
    
   ![Pulsante "Convalida"](./media/tutorial-transform-data-spark-portal/validate-button.png)
1. Selezionare **Pubblica tutti**. L'interfaccia utente di Data Factory pubblicherà le entità (servizi collegati e pipeline) nel servizio Azure Data Factory. 
    
   ![Pulsante "Pubblica tutti"](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline
Selezionare **Aggiungi trigger** nella barra degli strumenti, quindi selezionare **Attiva adesso**. 

![Pulsanti "Trigger" e "Trigger Now" (Attiva adesso)](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Passare alla scheda **Monitoraggio**. Verificare che venga visualizzata un'esecuzione della pipeline. La creazione di un cluster Spark richiede circa 20 minuti. 
   
1. Selezionare periodicamente **Aggiorna** per controllare lo stato dell'esecuzione della pipeline. 

   ![Scheda per il monitoraggio delle esecuzioni della pipeline, con il pulsante "Aggiorna"](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**.

   ![Stato dell'esecuzione della pipeline](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   È possibile tornare alla visualizzazione delle sessioni della pipeline selezionando il collegamento **Tutte le sessioni della pipeline**  nella parte superiore.

   ![Visualizzazione "Esecuzioni attività"](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verificare l'output
Verificare che il file di output sia stato creato nella cartella spark/otuputfiles/wordcount del contenitore adftutorial. 

![Percorso del file di output](./media/tutorial-transform-data-spark-portal/verity-output.png)

Il file dovrebbe contenere ogni parola del file di testo di input e il numero di occorrenze della parola nel file. Ad esempio: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio trasforma i dati usando un'attività Spark e un servizio collegato HDInsight su richiesta. Si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory. 
> * Creare una pipeline che usa un'attività Spark.
> * Attivare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

Per informazioni su come trasformare i dati tramite l'esecuzione di uno script Hive in un cluster Azure HDInsight che si trova in una rete virtuale, passare all'esercitazione successiva: 

> [!div class="nextstepaction"]
> [Esercitazione: Trasformare dati usando Hive nella rete virtuale di Azure](tutorial-transform-data-hive-virtual-network-portal.md).





