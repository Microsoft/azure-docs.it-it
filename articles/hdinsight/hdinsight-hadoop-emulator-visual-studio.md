---
title: Data Lake tools for Visual Studio & Hortonworks - Azure HDInsight
description: Informazioni su come usare gli strumenti Azure Data Lake per VIsual Studio con Sandbox di Hortonworks (in esecuzione su una macchina virtuale locale). Grazie a questi strumenti è possibile creare ed eseguire i processi Hive e Pig in Sandbox, oltre a visualizzare l'output del processo e la cronologia.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: e128aaf6e1726b7a1341fefc6df3cdafd3beb880
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73500158"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Usare gli strumenti Azure Data Lake per Visual Studio con Sandbox di Hortonworks

Azure Data Lake include strumenti per l'uso di cluster Apache Hadoop generici. Questo documento illustra la procedura necessaria per l'uso degli strumenti Data Lake con Hortonworks Sandbox in esecuzione in una macchina virtuale locale.

Sandbox di Hortonworks consente di utilizzare Hadoop in locale nell'ambiente di sviluppo. Dopo aver sviluppato una soluzione da distribuire in modo scalabile, è possibile passare a un cluster HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Sandbox di Hortonworks in esecuzione su una macchina virtuale nell'ambiente di sviluppo. Questo documento è stato scritto e testato con l'ambiente sandbox in esecuzione in Oracle VirtualBox. Per informazioni sulla configurazione della sandbox, vedere l'[introduzione a Hortonworks Sandbox](hadoop/apache-hadoop-emulator-get-started.md) nel relativo documento.

* Visual Studio.

* [Azure SDK per .NET](https://azure.microsoft.com/downloads/) 2.7.1 o versioni successive.

* Gli [strumenti Azure Data Lake per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Configurare le password per Sandbox

Assicurarsi che l'ambiente Hortonworks Sandbox sia in esecuzione. Seguire la proceduta illustrata nel documento di [introduzione a Hortonworks Sandbox](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords). per configurare la password dell'account `root` SSH e dell'account `admin` Apache Ambari. Queste password vengono usate per la connessione a Sandbox da Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Connettere gli strumenti a Sandbox

1. Aprire Visual Studio, selezionare **Visualizza** e quindi **Esplora server**.

2. In **Esplora server** fare clic con il pulsante destro del mouse sulla voce **HDInsight** e quindi scegliere **Connect to HDInsight Emulator** (Connetti a HDInsight Emulator).

    ![Esplora server, con l'evidenziato all'emulatore HDInsight](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. Nella finestra di dialogo **Connect to HDInsight Emulator** (Connetti a HDInsight Emulator) immettere la password configurata per Ambari.

    ![Screenshot della finestra di dialogo con la casella di testo della password ambari evidenziata](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Selezionare **Avanti** per continuare.

4. Usare il campo **Password** per immettere la password configurata per l'account `root`. Mantenere i valori predefiniti per gli altri campi.

    ![Cattura della finestra di dialogo con la casella di testo della password radice evidenziata](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    Selezionare **Avanti** per continuare.

5. Attendere il completamento della convalida dei servizi. In alcuni casi, la convalida potrebbe non riuscire e verrebbe chiesto di aggiornare la configurazione. Se la convalida ha esito negativo, selezionare il pulsante **Aggiorna** e attendere il completamento della configurazione e della verifica del servizio.

    ![Schermata della finestra di dialogo, con il pulsante Aggiorna evidenziato](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > Il processo di aggiornamento usa Ambari per modificare la configurazione di Hortonworks Sandbox su quella prevista dagli strumenti Data Lake per Visual Studio.

6. Al termine della convalida, selezionare **Fine** per completare la configurazione.
    ![Schermata della finestra di dialogo, con il pulsante Fine evidenziato](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > A seconda della velocità dell'ambiente di sviluppo e della quantità di memoria allocata sulla macchina virtuale, la configurazione e la convalida dei servizi potrebbe richiedere alcuni minuti.

Al termine della procedura indicata, si dispone di una voce **Cluster locale HDInsight** in Esplora server nella sezione **HDInsight**.

## <a name="write-an-apache-hive-query"></a>Scrivere una query Apache Hive

Hive fornisce un linguaggio di query simile a SQL (HiveQL) per la gestione dei dati strutturati. Per informazioni su come eseguire query a richiesta nel cluster locale, seguire questa procedura.

1. In **Esplora server** fare clic con il pulsante destro del mouse sulla voce del cluster locale aggiunto in precedenza e quindi scegliere **Scrivi una query Hive**.

    ![Schermata di Esplora server, con Scrivi una query Hive evidenziato](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    Verrà visualizzata una nuova finestra di query, utilizzabile per scrivere e inviare una query al cluster locale.

2. Nella nuova finestra di query immettere il comando seguente:

        select count(*) from sample_08;

    Per eseguire la query, selezionare **Invia** nella parte superiore della finestra. Mantenere i valori predefiniti negli altri campi (**Batch** e nome del server).

    ![Schermata della finestra di query, con il pulsante Invia evidenziato](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    È anche possibile usare il menu a discesa accanto a **Invia** per selezionare **Avanzate**. In questo modo è possibile specificare opzioni aggiuntive durante l'invio del processo.

    ![Schermata dell'hive della finestra di dialogo Invia script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. Dopo aver inviato la query viene visualizzato lo stato del processo, che fornisce informazioni sul processo mentre viene elaborato da Hadoop. La voce **Stato processo** indica lo stato del processo. Anche se lo stato viene aggiornato periodicamente, è possibile usare l'icona di aggiornamento per eseguire l'operazione manualmente.

    ![Schermata della finestra di dialogo Vista processi con Stato processo evidenziato](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    Quando **Stato processo** diventa **Terminato**, viene visualizzato un grafo aciclico diretto (DAG). Questo diagramma descrive il percorso di esecuzione determinato da Tez durante l'elaborazione della query Hive. Tez è il motore di esecuzione predefinito per Hive nel cluster locale.

    > [!NOTE]  
    > Apache Tez è anche il motore predefinito quando si usano i cluster HDInsight basati su Linux. Non è il valore predefinito in HDInsight basato su Windows. Per usarlo su Windows è necessario aggiungere la riga `set hive.execution.engine = tez;` all'inizio della query Hive.

    Usare il collegamento **Job Output** (Output processo) per visualizzare l'output. In questo caso è 823, il numero di righe nella tabella sample_08. È possibile visualizzare le informazioni di diagnostica relative al processo usando i collegamenti **Job Log** (Log processo) e **Download YARN Log** (Scarica log YARN).

4. È anche possibile eseguire in modo interattivo i processi Hive impostando il campo **Batch** su **Interattivo**, quindi selezionando **Esegui**.

    ![Schermata dei pulsanti Interattivo ed Esecuzione evidenziati](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    Una query interattiva trasmette il log di output generato durante l'elaborazione alla finestra **Output HiveServer2**.

    > [!NOTE]  
    > Si tratta delle stesse informazioni accessibili dal collegamento **Log processo** dopo il completamento del processo.

    ![Screenshot dell'output di HiveServer2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Creare un progetto Hive

Inoltre, è possibile creare un progetto che contiene più script Hive. Usare un progetto quando si hanno script correlati o si vogliono archiviare gli script in un sistema di controllo della versione.

1. In Visual Studio selezionare **File**, **Nuovo** e quindi **Progetto**.

2. Nell'elenco dei progetti espandere **Modelli** e **Azure Data Lake**, quindi selezionare **HIVE (HDInsight)**. Nell'elenco dei modelli selezionare **Hive Sample** (Esempio Hive). Immettere un nome e un percorso e quindi selezionare **OK**.

    ![Nuova finestra progetto, con Azure Data Lake, Hive Sample e OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

Il progetto **Hive Sample** (Esempio Hive) contiene due script, **WebLogAnalysis.hql** e **SensorDataAnalysis.hql**. È possibile inviare gli script usando lo stesso pulsante **Invia** nella parte superiore della finestra.

## <a name="create-an-apache-pig-project"></a>Creare un progetto Apache Pig

Mentre Hive offre un linguaggio simile a SQL per la gestione dei dati strutturati, Pig esegue trasformazioni sui dati. Pig offre infatti un linguaggio (Pig Latin) che consente di sviluppare una pipeline di trasformazioni. Per usare Pig con il cluster locale, seguire questa procedura:

1. Aprire Visual Studio e selezionare **File**, **Nuovo**, quindi **Progetto**. Nell'elenco dei progetti espandere **Modelli** e **Azure Data Lake** e quindi selezionare **Pig (HDInsight)**. Nell'elenco dei modelli selezionare **Pig Application** (Applicazione Pig). Immettere un nome e un percorso e quindi selezionare **OK**.

    ![Screenshot della finestra Nuovo progetto, con Azure Data Lake, Pig, Pig Application e OK evidenziati](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Immettere il testo seguente come contenuto del file **script.pig** creato con questo progetto.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Mentre Pig usa una lingua diversa da Hive, la modalità di esecuzione dei processi è coerente tra entrambe le lingue, tramite il pulsante **Invia.** Selezionando l'elenco a discesa accanto a **Invia** viene visualizzata una finestra di dialogo di invio avanzato per Pig.

    ![Schermata della finestra di dialogo Invia script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. Anche lo stato del processo e l'output vengono visualizzati allo stesso modo di una query Hive.

    ![Screenshot di un processo Pig completato](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Visualizzare i processi

Gli strumenti Data Lake consentono anche di visualizzare facilmente le informazioni sui processi che sono stati eseguiti in Hadoop. Usare la procedura seguente per visualizzare i processi che sono stati eseguiti nel cluster locale.

1. In **Esplora server** fare clic con il pulsante destro del mouse sul cluster locale e quindi scegliere **Visualizza processi** per aprire l'elenco dei processi che sono stati inviati al cluster.

    ![Schermata di Esplora Server, con Visualizza processi evidenziato](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. Da questo elenco, selezionare un processo per visualizzarne i dettagli.

    ![Schermata di Browser processi, con uno dei processi evidenziati](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    Le informazioni visualizzate sono simili a quelle che appaiono dopo l'esecuzione di una query Hive o Pig, inclusi i collegamenti per visualizzare le informazioni relative al log e all'output.

3. Da qui si può anche modificare il processo e inviarlo di nuovo.

## <a name="view-hive-databases"></a>Visualizzare i database Hive

1. In **Esplora server** espandere la voce **HDInsight local cluster** (Cluster locale HDInsight) e quindi **Hive Databases** (Database Hive). Verranno visualizzati i database **Default** e **xademo** nel cluster locale. Attraverso l'espansione di un database è possibile visualizzare le tabelle al suo interno.

    ![Schermata di Esplora server, con i database espansi](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. L'espansione della consente di visualizzare le colonne presenti in essa. Per visualizzare rapidamente i dati fare clic con il pulsante destro del mouse su una tabella e scegliere **Visualizza prime 100 righe**.

    ![Esplora server, con la tabella espansa e l'opzione Visualizza prime 100 righe selezionata](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Proprietà del database e della tabella

È possibile visualizzare le proprietà di un database o di una tabella in modo da mostrare i dettagli per l'elemento selezionato nella finestra **Proprietà**. Vedere ad esempio le informazioni visualizzate nella schermata seguente:

![Schermata della finestra Proprietà](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Creare una tabella

Per creare una tabella, fare clic con il pulsante destro del mouse su un database e quindi scegliere **Crea tabella**.

![Schermata di Esplora Server, con Crea tabella evidenziato](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

È quindi possibile creare la tabella utilizzando un modulo. Nella parte inferiore della schermata seguente è possibile visualizzare il codice HiveQL non elaborato che verrà usato per creare la tabella.

![Schermata del modulo usato per creare una tabella](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>Passaggi successivi

* [Acquisire dimestichezza con Sandbox di Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Esercitazione di Apache Hadoop: introduzione a HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
