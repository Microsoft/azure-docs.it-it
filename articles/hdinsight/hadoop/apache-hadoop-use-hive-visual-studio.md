---
title: Apache Hive & Data Lake Tools per Visual Studio-Azure HDInsight
description: Informazioni su come usare gli strumenti Data Lake per Visual Studio per eseguire query Apache Hive con Apache Hadoop in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74687794"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Eseguire query Apache Hive usando gli strumenti Data Lake per Visual Studio

Informazioni su come usare gli strumenti Data Lake per Visual Studio per eseguire query su Apache Hive. Gli strumenti Data Lake consentono di creare, inviare e monitorare facilmente query Hive in Apache Hadoop in Azure HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Hadoop in HDInsight. Per informazioni sulla creazione di questo elemento, vedere [creare Apache Hadoop cluster in Azure HDInsight usando Gestione risorse modello](./apache-hadoop-linux-tutorial-get-started.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/). I passaggi descritti in questo articolo usano Visual Studio 2019.

* Strumenti HDInsight per Visual Studio o Azure Data Lake Tools per Visual Studio. Per informazioni sull'installazione e la configurazione degli strumenti, vedere [Install data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a> Eseguire query Apache Hive usando Visual Studio

Per la creazione e l'esecuzione di query Hive sono disponibili due opzioni:

* Creare query ad hoc.
* Creare un'applicazione hive.

### <a name="create-an-ad-hoc-hive-query"></a>Creare una query hive ad hoc

Le query ad hoc possono essere eseguite in modalità **batch** o **interattiva** .

1. Avviare **Visual Studio** e selezionare **continua senza codice**.

2. In **Esplora server**fare clic con il pulsante destro del mouse su **Azure**, scegliere **Connetti a Microsoft Azure sottoscrizione...** e completare il processo di accesso.

3. Espandere **HDInsight**, fare clic con il pulsante destro del mouse sul cluster in cui si vuole eseguire la query e quindi scegliere **Scrivi una query hive**.

4. Immettere la query hive seguente:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selezionare **Esegui**. Per impostazione predefinita, la modalità di esecuzione è **interattiva**.

    ![Eseguire query hive interattive, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Per eseguire la stessa query in modalità **batch** , impostare l'elenco a discesa da **interattivo** a **batch**. Il pulsante di esecuzione passa da **Execute** a **Submit**.

    ![Inviare query hive batch, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    L'editor Hive supporta IntelliSense. Strumenti Data Lake per Visual Studio supporta il caricamento di metadati remoti quando si modifica lo script Hive. Se, ad esempio, si `SELECT * FROM`digita, IntelliSense elenca tutti i nomi di tabella suggeriti. Quando si specifica un nome di tabella, IntelliSense elenca i nomi delle colonne. Gli strumenti supportano la maggior parte delle funzioni definite dall'utente predefinite, delle sottoquery e delle istruzioni DML Hive. IntelliSense suggerisce solo i metadati del cluster selezionato nella barra degli strumenti HDInsight.

7. Nella barra degli strumenti query (l'area sotto la scheda query e sopra il testo della query) selezionare **Invia**oppure selezionare la freccia a discesa accanto a **Invia** e scegliere **Avanzate** dall'elenco a discesa. Se si seleziona la seconda opzione,

8. Se è stata selezionata l'opzione di invio avanzato, configurare **nome processo**, **argomenti**, **configurazioni aggiuntive**e **directory stato** nella finestra di dialogo **Invia script** . Selezionare **Invia**.

    ![Finestra di dialogo Invia script, query hive Hadoop di HDInsight](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Creare un'applicazione Hive

Per eseguire una query hive creando un'applicazione hive, seguire questa procedura:

1. Aprire **Visual Studio**.

2. Nella finestra **iniziale** selezionare **Crea un nuovo progetto**.

3. Nella finestra **Crea un nuovo progetto** , nella casella **Cerca modelli** , immettere *hive*. Quindi scegliere **applicazione hive** e fare clic su **Avanti**.

4. Nella finestra **Configura nuovo progetto** immettere un **nome di progetto**, selezionare o creare un **percorso** per il nuovo progetto e quindi selezionare **Crea**.

5. Aprire il file **Script.hql** creato con il progetto e incollarvi le seguenti istruzioni HiveQL:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Queste istruzioni eseguono le azioni seguenti:

    * `DROP TABLE`: Elimina la tabella se esiste.

    * `CREATE EXTERNAL TABLE`: crea una nuova tabella "esterna" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati vengono lasciati nella posizione originale.

        > [!NOTE]  
        > È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo MapReduce o un servizio di Azure.
        >
        > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

    * `ROW FORMAT`: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

    * `STORED AS TEXTFILE LOCATION`: Indica a hive che i dati sono archiviati nella directory *example/data* e che sono archiviati come testo.

    * `SELECT`: Seleziona un conteggio di tutte le righe in `t4` cui la colonna `[ERROR]`contiene il valore. Questa istruzione restituisce un valore di `3`, perché tre righe contengono questo valore.

    * `INPUT__FILE__NAME LIKE '%.log'`: Indica a hive di restituire solo i dati da file che terminano con. log. Questa clausola limita la ricerca al file *Sample. log* che contiene i dati.

6. Dalla barra degli strumenti file di query, che ha un aspetto simile alla barra degli strumenti query ad hoc, selezionare il cluster HDInsight che si desidera utilizzare per la query. Modificare quindi **Interactive** in **batch** (se necessario) e selezionare **Submit (Invia** ) per eseguire le istruzioni come processo hive.

   Verrà visualizzata una finestra di **riepilogo del processo Hive** con informazioni relative al processo in esecuzione. Usare il collegamento **Aggiorna** per aggiornare le informazioni del processo finché il campo **Stato processo** non viene impostato su **Completato**.

   ![Riepilogo del processo hive completato, applicazione hive, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Selezionare **output processo** per visualizzare l'output del processo. Mostra `[ERROR] 3`, ovvero il valore restituito dalla query.

### <a name="additional-example"></a>Altro esempio

L'esempio seguente si basa sulla `log4jLogs` tabella creata nella procedura precedente, [creare un'applicazione hive](#create-a-hive-application).

1. Da **Esplora server**, fare clic con il pulsante destro del mouse sul cluster e selezionare **Scrivi una query hive**.

2. Immettere la query hive seguente:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Queste istruzioni eseguono le azioni seguenti:

    * `CREATE TABLE IF NOT EXISTS`: Crea una tabella, se non esiste già. Poiché la `EXTERNAL` parola chiave non viene usata, questa istruzione crea una tabella interna. Le tabelle interne vengono archiviate nel data warehouse di Hive e sono gestite da Hive.

        > [!NOTE]  
        > A differenza delle tabelle `EXTERNAL`, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

    * `STORED AS ORC`: Archivia i dati nel formato ORC ( *optimized Row Column* ). ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.

    * `INSERT OVERWRITE ... SELECT`: seleziona le righe della tabella `log4jLogs` contenenti `[ERROR]`, quindi inserisce i dati nella tabella `errorLogs`.

3. Se necessario, modificare **interattivo** in **batch** , quindi selezionare **Invia**.

4. Per verificare che il processo abbia creato la tabella, passare a **Esplora server** ed espandere **Azure** > **HDInsight**. Espandere il cluster HDInsight e quindi espandere **database** > hive**predefinito**. Vengono elencate la tabella **errorLogs** e la tabella **log4jLogs**.

## <a name="next-steps"></a>Passaggi successivi

Come si può notare, gli strumenti HDInsight per Visual Studio forniscono un modo semplice per lavorare con le query Hive in HDInsight.

* Per informazioni generali su hive in HDInsight, vedere [che cos'è Apache hive e HiveQL in Azure HDInsight?](hdinsight-use-hive.md)

* Per informazioni su altri modi per lavorare con Hadoop in HDInsight, vedere [usare MapReduce in Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)

* Per altre informazioni su HDInsight Tools per Visual Studio, vedere[usare Data Lake Tools per Visual Studio per connettersi ad Azure HDInsight ed eseguire query Apache hive](apache-hadoop-visual-studio-tools-get-started.md)
