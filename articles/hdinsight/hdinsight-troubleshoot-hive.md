---
title: Risolvere i problemi di Hive tramite Azure HDInsight
description: Risposte alle domande frequenti sull'uso di Apache Hive e Azure HDInsight.
keywords: Azure HDInsight, Hive, domande frequenti, guida alla risoluzione dei problemi, domande comuni
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 02247adb9852a72b386feb2ef0924b0f1b3d6277
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895226"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Risolvere i problemi di Apache Hive tramite Azure HDInsight

Informazioni sui problemi principali che possono verificarsi quando si usano i payload di Apache Hive in Apache Ambari unitamente alle risoluzioni.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Come si esporta un metastore Hive e lo si importa in un altro cluster?

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Connettersi al cluster HDInsight con un client Secure Shell (SSH). Per altre informazioni, vedere [Informazioni aggiuntive](#additional-reading-end).

2. Eseguire il comando seguente nel cluster HDInsight da cui si vuole esportare il metastore:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Questo comando genera un file denominato alltables.sql.

3. Copiare il file alltables.sql nel nuovo cluster HDInsight e quindi eseguire il comando seguente:

    ```apache
    hive -f alltables.sql
    ```

Il codice nella procedura di risoluzione presuppone che i percorsi di dati nel nuovo cluster siano uguali ai percorsi di dati nel cluster precedente. Se i percorsi dei dati sono diversi, è possibile modificare manualmente il `alltables.sql` file generato in modo da riflettere le modifiche.

### <a name="additional-reading"></a>Informazioni aggiuntive

- [Connettersi a un cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Come si individuano i log Hive in un cluster?

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Connettersi al cluster HDInsight usando SSH. Per altre informazioni, vedere **Informazioni aggiuntive**.

2. Per visualizzare i log del client Hive, usare il comando seguente:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Per visualizzare i log del metastore Hive, usare il comando seguente:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Per visualizzare i log del server hive, usare il comando seguente:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Informazioni aggiuntive

- [Connettersi a un cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Come si avvia la shell di Hive con configurazioni specifiche in un cluster?

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Specificare una coppia chiave-valore di configurazione all'avvio della shell di Hive. Per altre informazioni, vedere [Informazioni aggiuntive](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Per elencare tutte le configurazioni valide nella shell di Hive, usare il comando seguente:

   ```apache
   hive> set;
   ```

   Ad esempio, usare il comando seguente per avviare la shell di Hive con la registrazione debug abilitata nella console:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Informazioni aggiuntive

- [Hive configuration properties](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties) (Proprietà di configurazione di Hive)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Come si analizzano i dati di un grafo aciclico diretto di Apache Tez in un percorso critico del cluster?

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Per analizzare i dati di un grafo aciclico diretto di Apache Tez in un grafo critico del cluster, connettersi al cluster HDInsight usando SSH. Per altre informazioni, vedere [Informazioni aggiuntive](#additional-reading-end).

2. Al prompt dei comandi, eseguire il seguente comando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Per elencare altri analizzatori che possono essere usati per analizzare i dati DAG di Tez, usare il comando seguente:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   È necessario fornire un programma di esempio come primo argomento.

   I nomi di programma validi includono:
    - **ContainerReuseAnalyzer**: stampare i dettagli di riutilizzo dei contenitori in un grafo aciclico diretto
    - **CriticalPath**: trovare il percorso critico di un grafo aciclico diretto
    - **LocalityAnalyzer**: stampare i dettagli della località in un grafo aciclico diretto
    - **ShuffleTimeAnalyzer**: analizzare i dettagli degli orari di riproduzione casuale in un grafo aciclico diretto
    - **SkewAnalyzer**: analizzare i dettagli dell'asimmetria in un grafo aciclico diretto
    - **SlowNodeAnalyzer**: stampare i dettagli del nodo in un grafo aciclico diretto
    - **SlowTaskIdentifier**: stampare i dettagli sulle attività lente in un grafo aciclico diretto
    - **SlowestVertexAnalyzer**: stampare i dettagli relativi ai vertici più lenti in un grafo aciclico diretto
    - **SpillAnalyzer**: stampare i dettagli relativi all'espansione in un grafo aciclico diretto
    - **TaskConcurrencyAnalyzer**: stampare i dettagli relativi alla concorrenza delle attività in un grafo aciclico diretto
    - **VertexLevelCriticalPathAnalyzer**: trovare il percorso critico a livello di vertice in un grafo aciclico diretto

### <a name="additional-reading"></a>Informazioni aggiuntive

- [Connettersi a un cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Come si scaricano i dati di un grafo aciclico diretto di Tez da un cluster?

#### <a name="resolution-steps"></a>Procedura per la risoluzione

Esistono due modi per raccogliere i dati di un grafo aciclico diretto di Tez:

- Dalla riga di comando:

    Connettersi al cluster HDInsight usando SSH. Al prompt dei comandi, eseguire il comando seguente:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Usare la visualizzazione Tez di Ambari:

  1. Passare ad Ambari.
  2. Passare alla visualizzazione Tez, disponibile sotto l'icona dei riquadri, nell'angolo superiore destro.
  3. Selezionare il grafo aciclico diretto da visualizzare.
  4. Selezionare **Download data** (Scarica dati).

### <a name="additional-reading"></a><a name="additional-reading-end"></a>Letture aggiuntive

[Connettersi a un cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

- Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.

- Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
