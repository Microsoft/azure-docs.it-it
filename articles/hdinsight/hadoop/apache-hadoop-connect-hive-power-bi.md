---
title: Visualizzare i dati Apache Hive con Power BI - Azure HDInsight
description: Informazioni su come usare Microsoft Power BI per visualizzare i dati Hive elaborati da Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 973b8a49c4a849f5a50fb3ab72321746a5ca06a7
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86083475"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualizzare i dati Apache Hive con Microsoft Power BI tramite ODBC in Azure HDInsight

Informazioni su come connettere Microsoft Power BI Desktop ad Azure HDInsight usando ODBC e visualizzare i dati Apache Hive.

> [!IMPORTANT]
> È possibile sfruttare il driver ODBC Hive per eseguire l'importazione tramite il connettore ODBC generico in Power BI Desktop. Tuttavia, non è consigliato per i carichi di lavoro di BI considerata la natura non interattiva del motore di query Hive. Il [connettore HDInsight Interactive Query](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) e il [connettore HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) rappresentano una scelta migliore per le prestazioni.

In questo articolo vengono caricati i dati da una `hivesampletable` tabella hive a Power bi. La tabella Hive contiene alcuni dati relativi all'utilizzo dei cellulari. Si tracciano quindi i dati sull'utilizzo in una mappa del mondo:

![Report mappa di Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Le informazioni si applicano anche al nuovo tipo di cluster [Interactive Query](../interactive-query/apache-interactive-query-get-started.md). Per informazioni su come connettersi a HDInsight Interactive Query tramite query dirette, vedere [Visualize Interactive Query Hive data with Microsoft Power BI using direct query in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Microsoft Power BI tramite query dirette in Azure HDInsight).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* Cluster HDInsight. Il cluster può essere un cluster HDInsight con Hive o un cluster Interactive Query, rilasciato di recente. Per la creazione dei cluster, vedere [Creare cluster](apache-hadoop-linux-tutorial-get-started.md).

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/). È possibile scaricarne una copia dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Creare un'origine dati Hive ODBC

Vedere [Creare un'origine dati Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Caricare i dati da HDInsight

La tabella hive **hivesampletable** viene fornita con tutti i cluster HDInsight.

1. Avviare Power BI Desktop.

1. Dal menu in alto passare a **Home**  >  **Ottieni dati**  >  **altro...**.

    ![HDInsight Excel Power BI aprire i dati](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. Nella finestra di dialogo **Ottieni dati** selezionare **altro** a sinistra, selezionare **ODBC** a destra e quindi selezionare **Connetti** in basso.

1. Nella finestra **di dialogo da ODBC** selezionare il nome dell'origine dati creato nell'ultima sezione dall'elenco a discesa. Selezionare **OK**.

1. Per il primo utilizzo, viene visualizzata una finestra di dialogo del **driver ODBC** . Selezionare **predefinito o personalizzato** dal menu a sinistra. Selezionare quindi **Connetti** per aprire lo **strumento di navigazione**.

1. Nella finestra di dialogo **strumento di navigazione** espandere **ODBC > hive > default**, selezionare **hivesampletable**e quindi selezionare **Load**.

## <a name="visualize-data"></a>Visualizzare i dati

Continuare dall'ultima procedura.

1. Dal riquadro Visualizzazioni selezionare **mappa**, ovvero un'icona a globo.

    ![Personalizzazione del report in Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. Dal riquadro **campi** selezionare **Country** e **devicemake**. È possibile visualizzare i dati tracciati sulla mappa.

1. Espandere la mappa.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come visualizzare i dati di HDInsight usando Power BI.  Per altre informazioni, vedere gli articoli seguenti:

* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettere Excel a Apache Hadoop tramite Power query](apache-hadoop-connect-excel-power-query.md).
* [Visualizzare i dati di Apache Hive Interactive query con Microsoft Power BI tramite query diretta](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
