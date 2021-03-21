---
title: 'Esercitazione: Analizzare dati di Apache Spark per Azure HDInsight con Power BI'
description: 'Esercitazione: Usare Microsoft Power BI per visualizzare i dati Apache Spark archiviati nei cluster HDInsight'
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 285c6c05a1a216303ee9d8019093c963cad60aa0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946481"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Esercitazione: Analizzare dati di Apache Spark usando Power BI in HDInsight

In questa esercitazione si apprenderà come usare Microsoft Power BI per visualizzare i dati nel cluster Apache Spark in Azure HDInsight.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Visualizzare i dati di Spark usando Power BI

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Completare l'articolo [Esercitazione: Caricare i dati ed eseguire query in un cluster Apache Spark in Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Facoltativo: [Sottoscrizione di valutazione di Power BI](https://app.powerbi.com/signupredirect?pbi_source=web)

## <a name="verify-the-data"></a>Verificare i dati

Il [notebook Jupyter](https://jupyter.org/) creato nell'[esercitazione precedente](apache-spark-load-data-run-query.md) include il codice per creare una tabella `hvac`. Questa tabella si basa sul file CSV disponibile in tutti i cluster HDInsight Spark all'indirizzo `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Usare la procedura seguente per verificare i dati.

1. Da Jupyter Notebook incollare il codice seguente e quindi premere **MAIUSC + INVIO**. Il codice verifica l'esistenza delle tabelle.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    L'output è simile al seguente:

    ![Mostra tabelle in Spark](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Se il notebook è stato chiuso prima di iniziare questa esercitazione, `hvactemptable` è stato eliminato e non è stato quindi incluso nell'output.  Dagli strumenti BI è possibile accedere solo alle tabelle Hive archiviate nel metastore (indicato da **False** nella colonna **isTemporary**). In questa esercitazione si esegue la connessione alla tabella **hvac** creata.

2. Incollare il codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**. Il codice verifica i dati nella tabella.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    L'output è simile al seguente:

    ![Visualizzazione delle righe dalla tabella hvac in Spark](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. Nel menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi). Arrestare il notebook per rilasciare le risorse.

## <a name="visualize-the-data"></a>Visualizzare i dati

In questa sezione si usa Power BI per creare visualizzazioni, report e dashboard dai dati del cluster Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Creare un report in Power BI Desktop

I primi passaggi nell'uso di Spark sono la connessione al cluster in Power BI Desktop, il caricamento dei dati dal cluster e la creazione di una visualizzazione basata su tali dati.

1. Aprire Power BI Desktop. Se si apre la schermata iniziale, chiuderla.

2. Nella scheda **Home** passare a **Recupera dati** > **Altro**.

    ![Ottenere dati in Power BI Desktop da Apache Spark per HDInsight](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Ottenere dati in Power BI da Apache Spark BI")

3. Immettere `Spark` nella casella di ricerca, selezionare **Azure HDInsight Spark** e quindi selezionare **Connetti**.

    ![Ottenere dati in Power BI da Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Ottenere dati in Power BI da Apache Spark BI")

4. Immettere l'URL del cluster (nel formato `mysparkcluster.azurehdinsight.net`) nella casella di testo **Server**.

5. In **Modalità Connettività dati** selezionare **DirectQuery**. Selezionare **OK**.

    Con Spark è possibile usare entrambe le modalità di connettività dati. Se si usa DirectQuery, le modifiche si riflettono nei report senza l'aggiornamento dell'intero set di dati. Se si importano i dati, per visualizzare le modifiche è necessario aggiornare il set di dati. Per altre informazioni su come e quando usare DirectQuery, vedere [Uso di DirectQuery in Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Immettere le informazioni dell'account di accesso di HDInsight, quindi selezionare **Connetti**. Il nome account predefinito è *admin*.

7. Selezionare la tabella `hvac`, attendere la visualizzazione dell'anteprima dei dati e quindi selezionare **Carica**.

    ![Nome utente e password del cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nome utente e password del cluster Spark")

    Power BI Desktop ha le informazioni necessarie per connettersi al cluster Spark e caricare dati dalla tabella `hvac`. La tabella e le relative colonne sono visualizzate nel riquadro **Campi**.

8. Visualizzare la variazione tra temperatura di destinazione e temperatura effettiva per ogni edificio:

    1. Nel riquadro **VISUALIZZAZIONI** selezionare **Grafico ad aree**.

    2. Trascinare il campo **BuildingID** in **Asse** e trascinare i campi **ActualTemp** e **TargetTemp** in **Valore**.

        ![aggiungere colonne di valori](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "aggiungere colonne di valori")

        Il diagramma è simile al seguente:

        ![somma grafico area](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "somma grafico area")

        Per impostazione predefinita, la visualizzazione mostra la somma di **ActualTemp** e **TargetTemp**. Selezionare la freccia verso il basso accanto ad **ActualTemp** e a **TragetTemp** nel riquadro Visualizzazioni per osservare che è selezionato **Somma**.

    3. Selezionare le frecce verso il basso accanto ad **ActualTemp** e a **TragetTemp** nel riquadro Visualizzazioni e quindi selezionare **Media** per ottenere una media della temperatura effettiva e di quella di destinazione per ogni edificio.

        ![media dei valori](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "media dei valori")

        La visualizzazione dei dati sarà simile a quella nello screenshot. Spostare il cursore sopra la visualizzazione per ottenere suggerimenti con i dati rilevanti.

        ![grafico area](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "grafico area")

9. Passare a **File** > **Salva**, immettere il nome `BuildingTemperature` per il file e quindi selezionare **Salva**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Pubblicare il report nel servizio Power BI (facoltativo)

Il servizio Power BI consente di condividere report e dashboard nell'organizzazione. In questa sezione prima si pubblicano i set di dati e il report, quindi si aggiunge il report a un dashboard. I dashboard vengono in genere usati per concentrarsi su un subset di dati in un report. Nel report è presente una sola visualizzazione, ma è comunque utile eseguire la procedura.

1. Aprire Power BI Desktop.

1. Nella scheda **Home** fare clic su **Pubblica**.

    ![Pubblicare in Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Pubblicare in Power BI Desktop")

1. Selezionare un'area di lavoro in cui pubblicare il set di dati e il report e quindi fare clic su **Seleziona**. Nella figura seguente è selezionato il valore predefinito **Area di lavoro personale**.

    ![Selezionare l'area di lavoro in cui pubblicare il set di dati e il report](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Selezionare l'area di lavoro in cui pubblicare il set di dati e il report")

1. Al termine della pubblicazione, selezionare **Apri 'BuildingTemperature.pbix' in Power BI**.

    ![Pubblicazione completata, fare clic per immettere le credenziali](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Pubblicazione completata, fare clic per immettere le credenziali")

1. Nel servizio Power BI selezionare **Immettere le credenziali**.

    ![Immettere le credenziali nel servizio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Immettere le credenziali nel servizio Power BI")

1. Selezionare **Modifica credenziali**.

    ![Modificare le credenziali nel servizio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Modificare le credenziali nel servizio Power BI")

1. Immettere le informazioni dell'account di accesso di HDInsight e quindi fare clic su **Accedi**. Il nome account predefinito è *admin*.

    ![Accedere al cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Accedere al cluster Spark")

1. Nel riquadro a sinistra passare ad **Aree di lavoro** > **Area di lavoro personale** > **REPORT** e quindi selezionare **BuildingTemperature**.

    ![Report elencato nei report nel riquadro sinistro](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Report elencato nei report nel riquadro sinistro")

    Il report **BuildingTemperature** è visualizzato anche in **SET DI DATI** nel riquadro a sinistra.

    L'oggetto visivo creato in Power BI Desktop è ora disponibile nel servizio Power BI.

1. Passare il puntatore del mouse sopra la visualizzazione e quindi fare clic sull'icona a forma di puntina nell'angolo in alto a destra.

    ![Report nel servizio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Report nel servizio Power BI")

1. Selezionare "Nuovo dashboard", immettere il nome `Building temperature` e quindi fare clic su **Aggiungi**.

    ![Aggiungere al nuovo dashboard](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Aggiungere al nuovo dashboard")

1. All'interno del report selezionare **Vai al dashboard**.

L'oggetto visivo è stato aggiunto al dashboard. È possibile aggiungere altri oggetti visivi al report e aggiungerli poi allo stesso dashboard. Per altre informazioni su report e dashboard, vedere [Report in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) e [Dashboard in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non è in uso. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, eliminare i cluster quando non vengono usati è una scelta economicamente conveniente.

Per eliminare un cluster, vedere [Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Microsoft Power BI per visualizzare i dati nel cluster Apache Spark in Azure HDInsight. Passare al prossimo articolo per vedere che è possibile creare un'applicazione di apprendimento automatico.

> [!div class="nextstepaction"]
> [Creare un'applicazione di apprendimento automatico](./apache-spark-ipython-notebook-machine-learning.md)