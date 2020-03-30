---
title: Uso di Apache Hive come strumento ETL - Azure HDInsight
description: Usare Apache Hive per estrarre, trasformare e caricare i dati in Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be331f36a6305b05ce83a2b2d5fdfb73a154ce3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623109"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Usare Apache Hive come strumento per estrazione, trasformazione e caricamento (ETL, Extract, Transform, and Load)

In genere è necessario pulire e trasformare i dati in ingresso prima di caricarli in una destinazione adatta all'analisi. Le operazioni di estrazione, trasformazione e caricamento consentono di preparare i dati e caricarli in una destinazione dati.  Apache Hive in HDInsight è in grado di leggere i dati non strutturati, elaborare i dati in base alle esigenze e quindi caricarli in un data warehouse relazionale per sistemi di supporto decisionale. In questo approccio i dati vengono estratti dall'origine e archiviati in un servizio di archiviazione scalabile, ad esempio nei BLOB del servizio di archiviazione di Azure o in Azure Data Lake Storage. I dati vengono quindi trasformati mediante una sequenza di query Hive e infine vengono gestiti all'interno di Hive in preparazione al caricamento bulk nell'archivio dati di destinazione.

## <a name="use-case-and-model-overview"></a>Panoramica del caso d'uso e del modello

La figura seguente mostra una panoramica del caso d'uso e del modello per l'automazione ETL. I dati di input vengono trasformati per generare l'output appropriato.  Durante tale trasformazione è possibile che si modifichino la forma, il tipo e persino il linguaggio dei dati.  I processi ETL possono convertire unità imperiali in unità di misura decimali, modificare i fusi orari e migliorare la precisione per un corretto allineamento con i dati esistenti nella destinazione.  I processi ETL possono anche combinare nuovi dati con i dati esistenti per mantenere aggiornati i report o per fornire ulteriori informazioni sui dati esistenti.  Le applicazioni, ad esempio i servizi e gli strumenti per la creazione di report, possono quindi utilizzare questi dati nel formato desiderato.

![Apache Hive: architettura ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop in genere viene usato nei processi ETL in cui viene importato un numero considerevole di file di testo, ad esempio file CSV, e/o un numero inferiore ma variabile di file di testo.  Hive è un ottimo strumento per preparare i dati prima di caricarli nella destinazione dati.  Hive consente di creare uno schema tramite il file CSV e di usare un linguaggio simile a SQL per generare programmi di MapReduce che interagiscono con i dati.

Di seguito sono indicati i passaggi tipici per l'uso di Hive per eseguire operazioni ETL:

1. Caricare dati in Azure Data Lake Storage o nell'archivio BLOB di Azure.
2. Creare un database dell'archivio di metadati tramite il database SQL di Azure per la memorizzazione degli schemi da parte di Hive.
3. Creare un cluster HDInsight e connettere l'archivio dati.
4. Definire lo schema da applicare in fase di lettura dei dati nell'archivio dati:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Trasformare i dati e caricarli nella destinazione.  È possibile usare Hive in diversi modi durante la trasformazione e il caricamento:

    * Eseguire query e preparare i dati usando Hive e salvarli come file CSV in Azure Data Lake Storage o nell'archivio BLOB di Azure.  Usare quindi uno strumento come SQL Server Integration Services (SSIS) per acquisire i file CSV e caricare i dati in un database relazionale di destinazione, ad esempio SQL Server.
    * Eseguire query sui dati direttamente da Excel o C# tramite il driver ODBC di Hive.
    * Usare [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) per leggere i file CSV flat preparati e caricarli nel database relazionale di destinazione.

## <a name="data-sources"></a>Origini dati

Le origini dati in genere sono rappresentate da dati esterni corrispondenti a dati esistenti nell'archivio dati, ad esempio:

* Dati relativi ai social media, file di log, sensori e applicazioni che generano file di dati.
* Set di dati ottenuti dai provider di dati, ad esempio statistiche sul meteo o cifre di vendita.
* Dati in streaming acquisiti, filtrati ed elaborati tramite un framework o uno strumento appropriato.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Destinazioni di output

È possibile usare Hive per eseguire l'output dei dati a diverse destinazioni, tra cui:

* Un database relazionale, ad esempio SQL Server o un database SQL di Azure.
* Un data warehouse, ad esempio Azure SQL Data Warehouse.
* Excel.
* Una tabella e un archivio BLOB di Azure.
* Applicazioni o servizi che richiedono l'elaborazione dei dati in formati specifici, oppure file che contengono tipi specifici di struttura delle informazioni.
* Un archivio documenti JSON come [Database cosmo di Azure](https://azure.microsoft.com/services/cosmos-db/).

## <a name="considerations"></a>Considerazioni

Il modello ETL in genere viene usato per:

* Caricare il flusso di dati o grandi quantità di dati semistrutturati o non strutturati da origini esterne in un sistema di informazioni o in database esistente.
* Pulire, trasformare e convalidare i dati prima di caricarli, possibilmente con più di un passaggio di trasformazione nel cluster.
* Generare report e visualizzazioni che vengono regolarmente aggiornati. Ad esempio, se la generazione di un report richiede troppo tempo durante il giorno, è possibile pianificarne l'esecuzione durante la notte. Per eseguire automaticamente una query Hive, è possibile usare App per la logica di Azure e PowerShell.To automatically run a Hive [query,](../../logic-apps/logic-apps-overview.md) you can use Azure Logic Apps and PowerShell.

Se la destinazione dei dati non è un database, è possibile generare un file nel formato appropriato all'interno della query, ad esempio un file CSV. Questo file può essere quindi importato in Excel o in Power BI.

Se è necessario eseguire diverse operazioni sui dati come parte del processo ETL, valutare la relativa modalità di gestione. Se le operazioni sono controllate da un programma esterno, anziché essere gestite come flusso di lavoro all'interno della soluzione, è necessario decidere se alcune di esse possono essere eseguite in parallelo e rilevare quando viene completato ogni processo. Potrebbe essere più semplice usare un meccanismo di flusso di lavoro, ad esempio Oozie in Hadoop, che tentare di orchestrare una sequenza di operazioni mediante script esterni o programmi personalizzati. Per altre informazioni su Oozie, vedere [Workflow and job orchestration](https://msdn.microsoft.com/library/dn749829.aspx) (Orchestrazione di processi e flussi di lavoro).

## <a name="next-steps"></a>Passaggi successivi

* [Estrarre, trasformare e caricare (ETL) su larga scala](apache-hadoop-etl-at-scale.md)
* [Rendere operativa una pipeline di analisi dei dati](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
