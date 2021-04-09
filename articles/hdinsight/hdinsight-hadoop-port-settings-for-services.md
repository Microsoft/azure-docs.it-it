---
title: Porte usate dai servizi Hadoop su HDInsight - Azure
description: Questo articolo fornisce un elenco di porte usate dai servizi Apache Hadoop in esecuzione in Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: b5dadb886078f701bb01447efd606c862ee73073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945530"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Porte usate dai servizi Apache Hadoop su HDInsight

Questo documento fornisce un elenco delle porte usate dai servizi Apache Hadoop in esecuzione nei cluster HDInsight. Fornisce anche informazioni sulle porte usate per connettersi al cluster tramite SSH.

## <a name="public-ports-vs-non-public-ports"></a>Porte pubbliche e porte non pubbliche

I cluster HDInsight basati su Linux espongono pubblicamente solo tre porte su Internet: 22, 23 e 443. Queste porte proteggono l'accesso al cluster tramite SSH e i servizi esposti tramite il protocollo HTTPS protetto.

HDInsight viene implementato da diverse macchine virtuali di Azure (nodi cluster) in esecuzione in una rete virtuale di Azure. Dall'interno della rete virtuale è possibile accedere alle porte non esposte a Internet. Se ci si connette tramite SSH al nodo Head, è possibile accedere direttamente ai servizi in esecuzione nei nodi del cluster.

> [!IMPORTANT]  
> Se non si specifica una rete virtuale di Azure come opzione di configurazione per HDInsight, se ne crea automaticamente una. Tuttavia, non è possibile aggiungere a questa rete virtuale altri computer (ad esempio altre macchine virtuali di Azure o il computer di sviluppo client).

Per aggiungere altre macchine alla rete virtuale, creare innanzitutto la rete virtuale e specificarla durante la creazione del cluster HDInsight. Per altre informazioni, vedere [pianificare una rete virtuale per HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Porte pubbliche

Tutti i nodi in un cluster HDInsight si trovano in una rete virtuale di Azure. Non è possibile accedere direttamente ai nodi da Internet. Un gateway pubblico fornisce accesso a Internet per le porte seguenti, comuni a tutti i tipi di cluster HDInsight.

| Servizio | Porta | Protocollo | Descrizione |
| --- | --- | --- | --- |
| sshd |22 |SSH |Connette i client a SSHD sul nodo head primario. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Connette i client a sshd sul nodo perimetrale. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Connette i client a SSHD sul nodo head secondario. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Interfaccia utente Web Ambari Vedere [Gestire i cluster HDInsight mediante l'uso dell'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |API REST Ambari Vedere [Gestire i cluster HDInsight mediante l'uso dell'API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |API REST HCatalog Vedere  [usare MapReduce con curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Esegue la connessione ad Hive tramite ODBC. Vedere [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Esegue la connessione ad Apache Hive tramite JDBC. Vedere [Connettersi ad Apache Hive in Azure HDInsight con il driver Hive JDBC](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Gli elementi seguenti sono disponibili per tipi di cluster specifici:

| Servizio | Porta | Protocollo | Tipo di cluster | Descrizione |
| --- | --- | --- | --- | --- |
| `Stargate` |443 |HTTPS |hbase |API REST HBase Vedere [Introduzione all'uso di Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |API REST Spark Vedere [Inviare processi Apache Spark in remoto con Apache LIVY](spark/apache-spark-livy-rest-interface.md) |
| Server Spark Thrift |443 |HTTPS |Spark |Server Spark Thrift usato per inviare query Hive. Vedere [Usare Beeline con Apache Hive in HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Interfaccia utente Web di Storm Vedere [Distribuzione e gestione di topologie Apache Storm in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |
| Proxy Rest Kafka |443 |HTTPS |Kafka |API REST Kafka. Vedere [interagire con cluster di Apache Kafka in Azure HDInsight usando un proxy Rest](kafka/rest-proxy.md) |

### <a name="authentication"></a>Authentication

Tutti i servizi esposti pubblicamente su Internet devono essere autenticati:

| Porta | Credenziali |
| --- | --- |
| 22 o 23 |Le credenziali utente SSH specificate durante la creazione del cluster |
| 443 |Il nome di accesso (impostazione predefinita: admin) e la password impostati durante la creazione del cluster |

## <a name="non-public-ports"></a>Porte non pubbliche

> [!NOTE]  
> Alcuni servizi sono disponibili solo su tipi di cluster specifici. Ad esempio, HBase è disponibile solo su tipi di cluster HBase.

> [!IMPORTANT]  
> Alcuni servizi vengono eseguiti in un solo nodo head alla volta. Se quando si tenta di connettersi al servizio nel nodo head primario viene visualizzato un errore, riprovare usando il nodo head secondario.

### <a name="ambari"></a>Ambari

| Servizio | Nodi | Porta | Percorso URL | Protocollo |
| --- | --- | --- | --- | --- |
| Interfaccia utente Web Ambari | Nodi head | 8080 | / | HTTP |
| API REST Ambari | Nodi head | 8080 | /api/v1 | HTTP |

Esempi:

* API REST Ambari: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Porte HDFS

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| --- | --- | --- | --- | --- |
| Interfaccia utente Web NameNode |Nodi head |30070 |HTTPS |Interfaccia utente Web per visualizzare lo stato |
| Servizio metadati NameNode |Nodi head |8020 |IPC |Metadati del file system |
| DataNode |Tutti i nodi di lavoro |30075 |HTTPS |Interfaccia utente Web per visualizzare lo stato, i log e così via. |
| DataNode |Tutti i nodi di lavoro |30010 |&nbsp; |Trasferimento dati |
| DataNode |Tutti i nodi di lavoro |30020 |IPC |Operazioni sui metadati |
| NameNode secondario |Nodi head |50090 |HTTP |Checkpoint per i metadati NameNode |

### <a name="yarn-ports"></a>Porte YARN

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| --- | --- | --- | --- | --- |
| Interfaccia utente Web di Resource Manager |Nodi head |8088 |HTTP |Interfaccia utente Web per Resource Manager |
| Interfaccia utente Web di Resource Manager |Nodi head |8090 |HTTPS |Interfaccia utente Web per Resource Manager |
| Interfaccia di amministrazione di Resource Manager |Nodi head |8141 |IPC |Per gli invii di applicazioni (hive, server hive, Pig e così via) |
| Utilità di pianificazione di Resource Manager |Nodi head |8030 |HTTP |Interfaccia di amministrazione |
| Interfaccia dell'applicazione Resource Manager |Nodi head |8050 |HTTP |Indirizzo dell'interfaccia di gestione delle applicazioni |
| NodeManager |Tutti i nodi di lavoro |30050 |&nbsp; |L'indirizzo del gestore di contenitore |
| Interfaccia utente Web di NodeManager |Tutti i nodi di lavoro |30060 |HTTP |Interfaccia Gestione risorse |
| Indirizzo di Timeline |Nodi head |10200 |RPC |Il servizio RPC del servizio Timeline. |
| Interfaccia utente Web di Timeline |Nodi head |8188 |HTTP |L'interfaccia utente Web del servizio Timeline |

### <a name="hive-ports"></a>Porte Hive

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nodi head |10001 |Thrift |Servizio per la connessione ad Hive (Thrift/JDBC) |
| Metastore Hive |Nodi head |9083 |Thrift |Servizio per la connessione ai metadati Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Porte WebHCat

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| --- | --- | --- | --- | --- |
| Server WebHCat |Nodi head |30111 |HTTP |API Web su HCatalog e su altri servizi Hadoop |

### <a name="mapreduce-ports"></a>Porte MapReduce

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| --- | --- | --- | --- | --- |
| JobHistory |Nodi head |19888 |HTTP |Interfaccia utente Web di MapReduce JobHistory |
| JobHistory |Nodi head |10020 |&nbsp; |Server di MapReduce JobHistory |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Trasferisce output intermedi di Map ai reducer che eseguono la richiesta |

### <a name="oozie"></a>Oozie

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| --- | --- | --- | --- | --- |
| Server di Oozie |Nodi head |11000 |HTTP |URL per il servizio Oozie |
| Server di Oozie |Nodi head |11001 |HTTP |Porta per l'amministrazione di Oozie |

### <a name="ambari-metrics"></a>Metriche di Ambari

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| --- | --- | --- | --- | --- |
| TimeLine (cronologia delle applicazioni) |Nodi head |6188 |HTTP |L'interfaccia utente Web del servizio Timeline |
| TimeLine (cronologia delle applicazioni) |Nodi head |30200 |RPC |L'interfaccia utente Web del servizio Timeline |

### <a name="hbase-ports"></a>Porte HBase

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| --- | --- | --- | --- | --- |
| HMaster |Nodi head |16000 |&nbsp; |&nbsp; |
| Interfaccia utente Web informativa di HMaster |Nodi head |16010 |HTTP |La porta per l'interfaccia utente Web Master HBase |
| Server dell'area |Tutti i nodi di lavoro |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |La porta usata dai client per connettersi a ZooKeeper |

### <a name="kafka-ports"></a>Porte Kafka

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| --- | --- | --- | --- | --- |
| Gestore |Nodi di lavoro |9092 |Protocollo di trasmissione Kafka |Usato per la comunicazione di client |
| &nbsp; |Nodi Zookeeper |2181 |&nbsp; |La porta usata dai client per connettersi a ZooKeeper |
| Proxy REST | Nodi di gestione Kafka |9400 |HTTPS |[Specifica REST Kafka](/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Porte Spark

| Servizio | Nodi | Porta | Protocollo | Percorso URL | Descrizione |
| --- | --- | --- | --- | --- | --- |
| Server Spark Thrift |Nodi head |10002 |Thrift | &nbsp; | Servizio per la connessione a Spark SQL (Thrift/JDBC) |
| Server Livy | Nodi head | 8998 | HTTP | &nbsp; | Servizio per l'esecuzione di istruzioni, processi e applicazioni |
| Jupyter Notebook | Nodi head | 8001 | HTTP | &nbsp; | Sito Web Jupyter Notebook |

Esempi:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. In questo esempio, `10.0.0.11` è l'indirizzo IP del nodo head che ospita il servizio Livy.