---
title: Risolvere i problemi di YARN in Azure HDInsight
description: Risposte alle domande frequenti sull'utilizzo di Apache Hadoop YARN e Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 0cd2571276992812327e286ba9b935fcbf6fbbaf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871810"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Risolvere i problemi di YARN di Apache Hadoop usando Azure HDInsight

Informazioni sui problemi principali che possono verificarsi quando si usano i payload di Apache Hadoop YARN in Apache Ambari unitamente alle risoluzioni.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Come si crea una nuova coda YARN in un cluster?

### <a name="resolution-steps"></a>Passaggi per la risoluzione del problema

Seguire questa procedura in Ambari per creare una nuova coda YARN e bilanciare l'allocazione delle capacità tra tutte le code.

In questo esempio è stata modificata la capacità dal 50% al 25% per due code esistenti (**predefinita** e **thriftsvr**), in modo da consentire alla nuova coda (Spark) di avere una capacità del 50%.

| Coda | Capacità | Capacità massima |
| --- | --- | --- |
| default | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Selezionare l'icona **Visualizzazioni di Ambari** e scegliere il motivo di griglia. Selezionare quindi **YARN Queue Manager** (Gestore code YARN).

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png" alt-text="Gestore code YARN di Apache Ambari dashboard" border="false":::
2. Selezionare la coda **predefinita**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png" alt-text="Apache Ambari YARN selezionare coda predefinita" border="false":::
3. Per la coda **predefinita**, modificare la **capacità** dal 50% al 25%. Per la coda **thriftsvr**, impostare la **capacità** sul 25%.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png" alt-text="Impostare la capacità sul 25% per la coda predefinita e la coda thriftsvr" border="false":::
4. Per creare una nuova coda, fare clic su **Aggiungi coda**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png" alt-text="Coda di aggiunta del dashboard di Apache Ambari YARN" border="false":::

5. Assegnare un nome alla nuova coda.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png" alt-text="Coda nome dashboard di Apache Ambari YARN" border="false":::  

6. Lasciare i valori di **Capacità** al 50% e selezionare il pulsante **Azioni**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png" alt-text="Azione di selezione di Apache Ambari YARN" border="false":::  
7. Selezionare **Save and Refresh Queues** (Salva e aggiorna code).

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png" alt-text="Selezionare Salva e aggiorna code" border="false":::  

Queste modifiche saranno immediatamente visibili nell'interfaccia utente dell'utilità di pianificazione YARN.

### <a name="additional-reading"></a>Altre letture

- [Utilità di pianificazione della capacità di YARN di Apache Hadoop](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Come si scaricano i log di YARN da un cluster?

### <a name="resolution-steps"></a>Passaggi per la risoluzione del problema

1. Connettersi al cluster HDInsight con un client Secure Shell (SSH). Per altre informazioni, vedere [Informazioni aggiuntive](#additional-reading-2).

1. Elencare tutti gli ID applicazione delle applicazioni YARN attualmente in esecuzione con il comando seguente:

    ```apache
    yarn top
    ```

    Gli ID sono elencati nella colonna **APPLICATIONID**, di cui è necessario scaricare i log **APPLICATIONID**.

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Per scaricare i log dei contenitori YARN per tutti gli schemi dell'applicazione, usare il comando seguente:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Verrà creato un file di log denominato amlogs.txt.

1. Per scaricare i log dei contenitori YARN solo per gli schemi dell'applicazione più recenti, usare il comando seguente:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Verrà creato un file di log denominato latestamlogs.txt.

1. Per scaricare i log dei contenitori YARN per i primi due schemi dell'applicazione, usare il comando seguente:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Verrà creato un file di log denominato first2amlogs.txt.

1. Per scaricare tutti i log dei contenitori YARN, usare il comando seguente:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Verrà creato un file di log denominato logs.txt.

1. Per scaricare il log dei contenitori YARN per un determinato contenitore, usare il comando seguente:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Verrà creato un file di log denominato containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Altre letture

- [Connettersi a HDInsight (Apache Hadoop) con SSH](./hdinsight-hadoop-linux-use-ssh-unix.md)
- [Concetti e applicazioni di Apache Hadoop YARN](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]