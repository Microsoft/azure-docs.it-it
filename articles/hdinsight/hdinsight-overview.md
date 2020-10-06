---
title: Che cos'è Azure HDInsight
description: Introduzione a HDInsight e allo stack di tecnologie Apache Hadoop e Apache Spark con i relativi componenti, tra cui Kafka, Hive, Storm e HBase, per l'analisi dei Big Data.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: contperfq1
ms.date: 08/21/2020
ms.openlocfilehash: f66eaaa3be0b300f6a0aa68bd43a2c336f4b4d23
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526630"
---
# <a name="what-is-azure-hdinsight"></a>Che cos'è Azure HDInsight?

Azure HDInsight è un servizio di analisi open source, gestito e ad ampio spettro nel cloud per le aziende. È possibile usare framework open source, ad esempio Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, Apache Storm, R e altri.

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Informazioni su Azure HDInsight e sullo stack di tecnologie Hadoop

Azure HDInsight è una distribuzione cloud dei componenti di Hadoop. Azure HDInsight rende semplice, rapida ed economicamente conveniente l'elaborazione di grandi quantità di dati. È possibile usare i framework open source più diffusi, ad esempio Hadoop, Spark, Hive, LLAP, Kafka, Storm, R e altri. Questi framework consentono di abilitare una vasta gamma di scenari, ad esempio l'estrazione, la trasformazione e il caricamento, il data warehousing, Machine Learning e Internet delle cose.

Per i componenti dello stack di tecnologie Hadoop disponibili in HDInsight, vedere [Componenti e versioni disponibili con HDInsight](./hdinsight-component-versioning.md). Per altre informazioni su Hadoop in HDInsight, vedere la pagina delle [funzionalità di Azure per HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-big-data"></a>Informazioni sui Big Data

I Big Data vengono raccolti in volumi sempre più elevati, a velocità crescenti e nella gamma di formati più ampia mai resa disponibile. I Big Data possono essere cronologici (dati archiviati) oppure in tempo reale (trasmessi dall'origine). Per altre informazioni sui caso d'uso più comuni per i Big Data, vedere [Scenari per l'uso di HDInsight](#scenarios-for-using-hdinsight).

## <a name="why-should-i-use-azure-hdinsight"></a>Perché usare Azure HDInsight?

Questa sezione elenca le funzionalità di Azure HDInsight.

|Funzionalità  |Descrizione  |
|---------|---------|
|Cloud nativo     |     Azure HDInsight permette di creare cluster ottimizzati per [Hadoop](./hadoop/apache-hadoop-linux-tutorial-get-started.md), [Spark](./spark/apache-spark-jupyter-spark-sql.md), [Interactive Query (LLAP)](./interactive-query/apache-interactive-query-get-started.md), [Kafka](./kafka/apache-kafka-get-started.md), [Storm](./storm/apache-storm-tutorial-get-started-linux.md), [HBase](./hbase/apache-hbase-tutorial-get-started-linux.md) e  [ML Services](./r-server/r-server-overview.md) in Azure. HDInsight fornisce anche un contratto di servizio end-to-end per tutti i carichi di lavoro di produzione.  |
|Costi contenuti e scalabilità     | HDInsight consente di [ridimensionare](./hdinsight-administer-use-portal-linux.md#scale-clusters) i carichi di lavoro aumentando o riducendo le prestazioni.È possibile ridurre il costo  [creando cluster su richiesta](./hdinsight-hadoop-create-linux-clusters-adf.md)  e pagando solo per le risorse usate. È anche possibile compilare pipeline di dati per rendere operativi i processi. Calcolo e archiviazione disaccoppiati offrono migliori prestazioni e flessibilità. |
|Sicurezza e conformità    | HDInsight consente di proteggere i dati aziendali usando [Rete virtuale di Azure](./hdinsight-plan-virtual-network-deployment.md), la [crittografia](./hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) e l'integrazione con [Azure Active Directory](./domain-joined/hdinsight-security-overview.md). HDInsight soddisfa anche i più diffusi [standard di conformità](https://azure.microsoft.com/overview/trusted-cloud) del settore e governativi.        |
|Monitoraggio    | Azure HDInsight si integra con i [log di Monitoraggio di Azure](./hdinsight-hadoop-oms-log-analytics-tutorial.md) per fornire una singola interfaccia che consente di monitorare tutti i cluster.        |
|Disponibilità globale | HDInsight è disponibile in più  [aree](https://azure.microsoft.com/regions/services/)  di qualsiasi altra offerta di analisi di Big Data. Azure HDInsight è anche disponibile in Azure per enti pubblici, Cina e Germania per soddisfare le esigenze aziendali nelle principali aree sovrane. |  
|Produttività     |  Azure HDInsight consente di usare strumenti di produttività avanzati per Hadoop e Spark con gli ambienti di sviluppo più diffusi. Questi ambienti di sviluppo includono [Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md), [VSCode](./hdinsight-for-vscode.md), [Eclipse](./spark/apache-spark-eclipse-tool-plugin.md) e [IntelliJ](./spark/apache-spark-intellij-tool-plugin.md) per supportare Scala, Python, R, Java e .NET. I data scientist possono anche collaborare usando notebook diffusi, ad esempio [Jupyter](./spark/apache-spark-jupyter-notebook-kernels.md) e [Zeppelin](./spark/apache-spark-zeppelin-notebook.md).    |
|Estendibilità     |  È possibile estendere i cluster HDInsight con componenti installati (Hue, Presto e così via) con [azioni script](./hdinsight-hadoop-customize-cluster-linux.md), l'[aggiunta di nodi perimetrali](./hdinsight-apps-use-edge-node.md) o l'[integrazione con altre applicazioni Big Data certificate](./hdinsight-apps-install-applications.md). HDInsight consente un'integrazione senza problemi con le più diffuse soluzioni Big Data tramite una distribuzione [con un clic](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Scenari per l'uso di HDInsight

Azure HDInsight può essere usato per un'ampia gamma di scenari per l'elaborazione di Big Data. Possono essere dati cronologici (dati già raccolti e archiviati) o dati in tempo reale (dati trasmessi direttamente dall'origine). Gli scenari per l'elaborazione di tali dati possono essere riepilogati nelle categorie seguenti:

### <a name="batch-processing-etl"></a>Elaborazione batch (ETL)

L'estrazione, trasformazione e caricamento (ETL) è un processo in cui dati strutturati o non strutturati vengono estratti da origini dati eterogenei. I dati vengono quindi trasformati in un formato strutturato e caricati in un archivio dati. È possibile usare i dati trasformati per data science o data warehousing.

### <a name="data-warehousing"></a>Data warehousing

È possibile usare HDInsight per eseguire query interattive nell'ordine di grandezza di petabyte su dati strutturati o non strutturati in qualsiasi formato. È anche possibile compilare modelli che le connettono a strumenti di business intelligence. Per altre informazioni, [vedere la storia di questo cliente](https://customers.microsoft.com/story/milliman).

Architettura di HDInsight: data warehouse

### <a name="internet-of-things-iot"></a>Internet delle cose

È possibile usare HDInsight per elaborare dati di streaming ricevuti in tempo reale da diversi tipi di dispositivi. Per altre informazioni, [leggere questo post di blog di Azure che annuncia l'anteprima pubblica di Apache Kafka su HDInsight con Azure Managed Disks](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

Architettura di HDInsight: Internet delle cose

### <a name="data-science"></a>Data science

HDInsight consente di compilare applicazioni che estraggono informazioni critiche dai dati. È anche possibile usare Azure Machine Learning per prevedere le tendenze future per l'azienda. Per altre informazioni, [vedere la storia di questo cliente](https://customers.microsoft.com/story/pros).

Architettura di HDInsight: data science

### <a name="hybrid"></a>Ibrido

È possibile usare HDInsight per estendere l'infrastruttura per Big Data locale esistente ad Azure per sfruttare le funzionalità di analisi avanzata del cloud.

Architettura di HDInsight: ibrida

## <a name="cluster-types-in-hdinsight"></a>Tipi di cluster in HDInsight

HDInsight include tipi di cluster specifici e funzionalità di personalizzazione dei cluster, ad esempio la possibilità di aggiungere componenti, utilità e linguaggi. HDInsight offre i seguenti tipi di cluster:

|Tipo di cluster | Descrizione |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|un framework che usa HDFS, la gestione risorse YARN e un semplice modello di programmazione MapReduce per elaborare e analizzare i dati batch in parallelo.|
|[Apache Spark](./spark/apache-spark-overview.md)|è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data. Vedere [Informazioni su Apache Spark in HDInsight](./spark/apache-spark-overview.md).|
|[Apache HBase](./hbase/apache-hbase-overview.md)|un database NoSQL basato su Hadoop che fornisce accesso casuale e coerenza assoluta per quantità elevate di dati non strutturati e semistrutturati. Può gestire potenzialmente milioni di righe e colonne. Vedere [Informazioni su HBase in HDInsight](./hbase/apache-hbase-overview.md)|
|[ML Services](./r-server/r-server-overview.md)|un server che ospita e gestisce processi R paralleli e distribuiti. Offre ai data scientist, agli statistici e ai programmatori R l'accesso su richiesta a metodi di analisi scalabili e distribuiti su HDInsight. Vedere [Panoramica di ML Services su HDInsight](./r-server/r-server-overview.md).|
|[Apache Storm](./storm/apache-storm-overview.md)|un sistema di calcolo distribuito e in tempo reale per l'elaborazione rapida di grandi flussi di dati. Storm viene offerto come cluster gestito in HDInsight. Vedere [Analizzare i dati del sensore in tempo reale con Storm e Hadoop](./storm/apache-storm-overview.md).|
|[Apache Interactive Query](./interactive-query/apache-interactive-query-get-started.md)|Caching in memoria per query Hive interattive e più rapide. Vedere [Usare Interactive Query in HDInsight](./interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|una piattaforma open source usata per creare applicazioni e pipeline di dati di streaming. Kafka fornisce inoltre funzionalità di code di messaggi che consentono di pubblicare e sottoscrivere i flussi di dati. Vedere [Introduction to Apache Kafka on HDInsight](./kafka/apache-kafka-introduction.md) (Introduzione ad Apache Kafka in HDInsight).|

## <a name="open-source-components-in-hdinsight"></a>Componenti open source in HDInsight

Azure HDInsight consente di creare cluster con framework open source, ad esempio Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase e R. Per impostazione predefinita, questi cluster vengono forniti con altri componenti open source inclusi nel cluster, ad esempio [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](https://avro.apache.org/docs/current/spec.html), [Apache Hive](https://hive.apache.org), [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Apache Mahout](https://mahout.apache.org/), [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Phoenix](https://phoenix.apache.org/), [Apache Pig](https://pig.apache.org/), [Apache Sqoop](https://sqoop.apache.org/), [Apache Tez](https://tez.apache.org/), [Apache Oozie](https://oozie.apache.org/), [Apache ZooKeeper](https://zookeeper.apache.org/).  

## <a name="programming-languages-in-hdinsight"></a>Linguaggi di programmazione in HDInsight

I cluster di HDInsight, tra cui Spark, HBase, Kafka, Hadoop e altri, supportano molti linguaggi di programmazione. Alcuni linguaggi di programmazione non sono installati per impostazione predefinita. Per le librerie, i moduli o i pacchetti non installati per impostazione predefinita, [usare un'azione script per installarli](./hdinsight-hadoop-script-actions-linux.md).

|Linguaggio di programmazione  |Informazioni  |
|---------|---------|
|Supporto per i linguaggi di programmazione predefiniti     | Per impostazione predefinita, i cluster HDInsight supportano:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Go</li></ul>  |
|Linguaggi per macchine virtuali Java     | Molti linguaggi diversi da Java possono essere eseguiti in una macchina virtuale Java. Se tuttavia si eseguono alcuni di questi linguaggi, potrebbe essere necessario installare componenti aggiuntivi sui cluster. I linguaggi seguenti basati su JVM sono supportati nei cluster HDInsight: <ul><li>Clojure</li><li>Jython (Python per Java)</li><li>Scala</li></ul>     |
|Linguaggi specifici di Hadoop     | I cluster HDInsight supportano i linguaggi seguenti, specifici dello stack di tecnologie Hadoop: <ul><li>Pig Latin per processi Pig</li><li>HiveQL per processi Hive e SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>Strumenti di sviluppo per HDInsight

È possibile usare gli strumenti di sviluppo di HDInsight, inclusi IntelliJ, Eclipse, Visual Studio Code e Visual Studio, per creare e inviare processi e query sui dati di HDInsight con una semplice integrazione con Azure.

* [Toolkit di Azure per IntelliJ](./spark/apache-spark-intellij-tool-plugin.md)

* [Toolkit di Azure per Eclipse](./spark/apache-spark-eclipse-tool-plugin.md)

* [Strumenti di Azure HDInsight per Visual Studio Code](./hdinsight-for-vscode.md)

* [Strumenti di Azure Data Lake per Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="business-intelligence-on-hdinsight"></a>Business intelligence in HDInsight

Gli strumenti di business intelligence (BI) noti consentono di recuperare, analizzare e creare report di dati integrati con HDInsight usando il componente aggiuntivo Power Query o Microsoft Hive ODBC Driver:

* [Apache Spark BI usando gli strumenti di visualizzazione di dati con Azure HDInsight](./spark/apache-spark-use-bi-tools.md)

* [Visualizzare i dati Apache Hive con Microsoft Power BI in Azure HDInsight](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [Visualize Interactive Query Hive data with Power BI in Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Power BI in Azure HDInsight)

* [Connettere Excel a Apache Hadoop mediante Power Query](./hadoop/apache-hadoop-connect-excel-power-query.md) (richiede Windows)

* [Connettere Excel ad Apache Hadoop con Microsoft Hive ODBC Driver](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (richiede Windows)


## <a name="in-region-data-residency"></a>Residenza dei dati nell'area geografica 

Spark, Hadoop, LLAP, Storm e MLService non archiviano i dati dei clienti, quindi questi servizi soddisfano automaticamente i requisiti di residenza dei dati nell'area geografica, inclusi quelli specificati in [Centro protezione](https://azuredatacentermap.azurewebsites.net/). 

Kafka e HBase non archiviano i dati dei clienti. Questi dati vengono archiviati automaticamente da Kafka e HBase in una singola area geografica, quindi questo servizio soddisfa i requisiti di residenza dei dati nell'area geografica, inclusi quelli specificati in [Centro protezione](https://azuredatacentermap.azurewebsites.net/). 


Gli strumenti di business intelligence (BI) noti consentono di recuperare, analizzare e creare report di dati integrati con HDInsight usando il componente aggiuntivo Power Query o Microsoft Hive ODBC Driver.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un cluster Apache Hadoop in HDInsight](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* Creare un cluster Apache Spark - Portale
* [Pianificare una rete virtuale per Azure HDInsight](hdinsight-plan-virtual-network-deployment.md)
* [Sicurezza aziendale in Azure HDInsight](./domain-joined/hdinsight-security-overview.md)
