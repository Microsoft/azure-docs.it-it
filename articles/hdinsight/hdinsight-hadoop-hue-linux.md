---
title: Hue con Hadoop nei cluster HDInsight basati su Linux - Azure
description: Informazioni su come installare Hue nei cluster HDInsight e usare il tunneling per instradare le richieste a Hue. Usare Hue per sfogliare le risorse di archiviazione ed eseguire Hive o Pig.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: 8d4663aac6af4abb8d9855d2f972965e997d9c92
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945690"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installare e usare Hue nei cluster Hadoop di HDInsight

Informazioni su come installare Hue nei cluster HDInsight e usare il tunneling per instradare le richieste a Hue.

## <a name="what-is-hue"></a>Informazioni su Hue

Hue è un insieme di applicazioni Web che consente di interagire con un cluster Apache Hadoop. È possibile usare Hue per esplorare lo spazio di archiviazione associato a un cluster Hadoop (WASB nel caso di cluster HDInsight), eseguire processi Hive e script Pig e così via. I componenti seguenti sono disponibili con l'installazione di Hue in un cluster Hadoop di HDInsight.

* Editor Hive Beeswax
* Apache Pig
* Metastore Manager
* Apache Oozie
* FileBrowser (che interagisce con il contenitore predefinito di WASB)
* Job Browser

> [!WARNING]  
> I componenti forniti con il cluster HDInsight sono supportati in modo completo e il Supporto Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. Per esempio, è possibile ricorrere a molti siti di community, come: [Pagina delle domande di Domande e risposte Microsoft per HDInsight](/answers/topics/azure-hdinsight.html), [https://stackoverflow.com](https://stackoverflow.com). Anche per i progetti Apache sono disponibili siti specifici in [https://apache.org](https://apache.org), per esempio: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Installare Hue mediante azioni script

Usare le informazioni nella tabella seguente per l'azione di script. Per istruzioni specifiche su come utilizzare le azioni di script, vedere [Personalizzare cluster HDInsight con azioni di script](hdinsight-hadoop-customize-cluster-linux.md).

> [!NOTE]  
> Per installare Hue nei cluster HDInsight, la dimensione consigliata del nodo head è minimo A4 (8 core, 14 GB di memoria).

|Proprietà |valore |
|---|---|
|Tipo di script:|- Personalizzato|
|Nome|Installare Hue|
|URI script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Tipi di nodo:|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Usare Hue con i cluster HDInsight

È possibile avere un solo account utente con Hue nei cluster regolari. Per l'accesso multiutente, abilitare [Enterprise Security Package](./domain-joined/hdinsight-security-overview.md) nel cluster. Il tunneling SSH è il solo modo di accedere a Hue nel cluster una volta che è in esecuzione. Il tunneling tramite SSH consente al traffico di raggiungere direttamente il nodo head del cluster in cui viene eseguito Hue. Al termine del provisioning del cluster, seguire questa procedura per usare Hue in un cluster HDInsight.

> [!NOTE]  
> È consigliabile utilizzare il browser web di Firefox per seguire le istruzioni seguenti.

1. Utilizzare le informazioni contenute in [Utilizzare SSH Tunneling per accedere all'interfaccia utente Web Apache Ambari, a ResourceManager, JobHistory, NameNode, Oozie e ad altre interfacce utente Web](hdinsight-linux-ambari-ssh-tunnel.md) per creare un tunnel SSH dal sistema client al cluster HDInsight e quindi configurare il browser Web per usare il tunnel SSH come proxy.

1. Usare il [comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Una volta connessi, utilizzare il comando seguente per ottenere il nome di dominio completo del nodo head primario:

    ```bash
    hostname -f
    ```

    Verrà restituito un nome simile al seguente:

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    Questo è il nome host del nodo head primario in cui si trova il sito Web Hue.

1. Usare il browser per aprire il portale di Hue all'indirizzo `http://HOSTNAME:8888`. Sostituire HOSTNAME con il nome ottenuto nel passaggio precedente.

   > [!NOTE]  
   > Quando si accede per la prima volta, viene richiesto di creare un account per l'accesso al portale Hue. Le credenziali specificate saranno limitate al portale e non sono correlate alle credenziali amministratore o utente SSH specificate durante il provisioning del cluster.

    ![Finestra di accesso del portale di Hue HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Specificare le credenziali per il portale di Hue")

### <a name="run-a-hive-query"></a>Eseguire una query Hive

1. Nel portale di Hue selezionare **Query Editors** (Editor di query) e quindi selezionare **Hive** per aprire l'editor Hive.

    ![Editor Hive per l'uso del portale di Hue in HDInsight ](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Usare Hive")

2. Nella scheda **Assist** (Assistenza) in **Database** dovrebbe essere visibile **hivesampletable**. Si tratta di una tabella di esempio inclusa in tutti i cluster Hadoop in HDInsight. Immettere una query di esempio nel riquadro destro e visualizzare l'output nella scheda **Risultati** nel riquadro sottostante, come illustrato nella schermata.

    ![Query Hive del portale di Hue HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Eseguire query Hive")

    È anche possibile usare la scheda **Grafico** per vedere una rappresentazione visiva dei risultati.

### <a name="browse-the-cluster-storage"></a>Esplorare l'archiviazione cluster

1. Nel portale di Hue selezionare **Browser file** nell'angolo superiore destro della barra dei menu.
2. Per impostazione predefinita, il browser file viene aperto in corrispondenza della directory **/user/myuser** . Selezionare la barra subito prima della directory user nel percorso per passare alla radice del contenitore di archiviazione di Azure associato al cluster.

    ![Browser file del portale di Hue in HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Usare il browser file")

3. Fare clic son il pulsante destro del mouse su un file o una cartella per visualizzare le operazioni disponibili. Usare il pulsante **Carica** nell'angolo destro per caricare i file nella directory corrente. Usare il pulsante **Nuovo** per creare nuovi file o directory.

> [!NOTE]  
> Il browser file Hue può mostrare solo il contenuto del contenitore predefinito associato al cluster HDInsight. Eventuali account di archiviazione o contenitori aggiuntivi associati al cluster non saranno accessibili tramite il browser file. I contenitori aggiuntivi associati al cluster saranno comunque sempre accessibili per i processi Hive. Ad esempio, se si immette il comando `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` nell'editor Hive, è possibile vedere il contenuto anche dei contenitori aggiuntivi. In questo comando **newcontainer** non è il contenitore predefinito associato a un cluster.

## <a name="important-considerations"></a>Considerazioni importanti

1. Lo script usato per installare Hue ne consente l'installazione solo nel nodo head del cluster.

1. Durante l'installazione vengono riavviati più servizi Hadoop (HDFS, YARN, MR2, Oozie) per l'aggiornamento della configurazione. Al termine dell'installazione di Hue tramite lo script, è possibile che l'avvio di altri servizi Hadoop richieda qualche istante. Ciò potrebbe influire inizialmente sulle prestazioni di Hue. Una volta avviati tutti i servizi, Hue sarà completamente funzionale.

1. Hue non riconosce i processi di Apache Tez, che attualmente corrisponde all'importazione predefinita per Hive. Se si vuole usare MapReduce come motore di esecuzione di Hive, aggiornare lo script per l'uso dei comandi seguenti:

   `set hive.execution.engine=mr;`

1. Con i cluster Linux è possibile avere uno scenario in cui i servizi vengono eseguiti sul nodo head primario mentre Resource Manager potrebbe essere in esecuzione su quello secondario. Questo scenario potrebbe causare errori (illustrati di seguito) quando si usa Hue per visualizzare i dettagli dei processi IN ESECUZIONE nel cluster. I dettagli del processo possono tuttavia essere visualizzati dopo il completamento del processo.

   ![Messaggio di esempio di errore nel portale di Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Errore nel portale di Hue")

   Questo è causato da un problema noto. Come soluzione alternativa, modificare Ambari in modo che anche l'istanza di Resource Manager attiva venga eseguita sul nodo head primario.

1. Hue riconosce WebHDFS mentre i cluster HDInsight usano Archiviazione di Azure Storage tramite `wasbs://`. Lo script personalizzato usato con l'azione script installa WebWasb, un servizio compatibile con WebHDFS-per comunicare con WASB. Quindi, anche se in alcuni punti nel portale di Hue è indicato HDFS (come quando si sposta il mouse su **File Browser**), dovrà essere interpretato come WASB.

## <a name="next-steps"></a>Passaggi successivi

[Installare R nei cluster HDInsight](./r-server/r-server-overview.md). Usare la personalizzazione dei cluster per installare R nei cluster Hadoop di HDInsight. R è un linguaggio open source e un ambiente per l'elaborazione statistica. Fornisce centinaia di funzioni statistiche predefinite e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica.