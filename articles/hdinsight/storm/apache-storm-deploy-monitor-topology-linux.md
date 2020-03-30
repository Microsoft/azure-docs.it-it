---
title: Distribuire e gestire le topologie Apache Storm su Azure HDInsight
description: Informazioni su come distribuire, monitorare e gestire le topologie Apache Storm utilizzando Storm Dashboard in HDInsight basato su Linux. Utilizzare gli strumenti Hadoop per Visual Studio
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: e890289230b3215bd102d8c5a78dca4f1b7b90f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271902"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Distribuire e gestire le topologie Apache Storm su Azure HDInsight

In questo documento sono illustrati i concetti di gestione e monitoraggio delle topologie [Apache Storm](https://storm.apache.org/) in esecuzione su Storm in cluster HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Storm in HDInsight. Consultare [Creare cluster di Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selezionare **Storm** per **Tipo di cluster**.

* (Facoltativo) Familiarità con Secure Shell (SSH) e Secure Copy (SCP). Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Facoltativo) Visual Studio, Azure SDK 2.5.1 o versione versione più recente e Data Lake Tools per Visual Studio. Per ulteriori informazioni, vedere [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Inviare una topologia utilizzando Visual Studio

È possibile utilizzare Data Lake Tools per Visual Studio per l'invio di topologie ibride o in C, al cluster Storm. Nei seguenti passaggi viene usata un'applicazione di esempio. Per informazioni sulla creazione della topologia mediante gli strumenti Data Lake, vedere Topologie di [Apache Storm con Visual Studio e C.](apache-storm-develop-csharp-visual-studio-topology.md)

1. Se non è già stata installata la versione più recente degli strumenti Data Lake per Visual Studio, vedere [Usare Data Lake Tools per Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > The Azure Data Lake and Stream Analytics Tools were formerly called the HDInsight Tools for Visual Studio.
    >
    > Gli strumenti di Analisi dei dati di Azure e di analisi di flusso per Visual Studio sono inclusi nel carico di lavoro di sviluppo di Azure per Visual Studio 2019.Azure Data Lake and Stream Analytics Tools for Visual Studio are included in the **Azure development** workload for Visual Studio 2019.

1. Avviare Visual Studio.

1. Nella finestra **iniziale** selezionare **Crea un nuovo progetto**.

1. Nella finestra **Crea un nuovo progetto** selezionare `Storm`la casella di ricerca e immettere . Quindi scegliere **Storm Sample** dall'elenco dei risultati e selezionare **Avanti**.

1. Nella finestra **Configura il nuovo progetto** immettere un nome di **progetto**e passare a o creare un **percorso** in cui salvare il nuovo progetto. Quindi selezionare **Crea**.

    ![Configurare la finestra del nuovo progetto, Visual StudioConfigure your new project window, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. In **Esplora server**fare clic con il pulsante destro del mouse su **Azure** e scegliere Connetti a sottoscrizione di **Microsoft Azure...** e completare il processo di accesso.

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e **scegliere Invia a storma in HDInsight**.

    > [!NOTE]  
    > Se richiesto, immettere le credenziali di accesso per la sottoscrizione di Azure. Se si dispone di più di una sottoscrizione, accedere a quella che contiene il cluster Storm in HDInsight.

1. Nell'elenco a discesa Storm **Cluster** della finestra di dialogo **Invia topologia** scegliere Storm nel cluster HDInsight e quindi selezionare **Submit**. È possibile monitorare se l'invio ha esito positivo visualizzando il riquadro **Output.You** can monitor whether the submission is successful by viewing the Output pane.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Inviare una topologia utilizzando SSH e il comando Storm

1. Usare [il comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster, quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Dalla sessione ssh, utilizzare il comando seguente per avviare la topologia di esempio **WordCount:**

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Questo comando consente di avviare la topologia di esempio WordCount nel cluster. Questa topologia genera in modo casuale le frasi e quindi conta le occorrenze di ogni parola nelle frasi.

    > [!NOTE]  
    > Quando si invia la topologia al cluster, è necessario prima copiare `storm` il file JAR contenente il cluster prima di utilizzare il comando. Usare il comando `scp` per copiare il file nel cluster. Immettere ad esempio `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > L'esempio *WordCount* e altri esempi di avviamento `/usr/hdp/current/storm-client/contrib/storm-starter/`di tempeste sono già inclusi nel cluster in .

## <a name="submit-a-topology-programmatically"></a>Inviare una topologia a livello di codiceSubmit a topology programmatically

È possibile distribuire a livello di codice una topologia usando il servizio Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)fornisce un'applicazione Java di esempio che illustra come distribuire e avviare una topologia tramite il servizio Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitorare e gestire una topologia in Visual StudioMonitor and manage a topology in Visual Studio

Quando si invia una topologia utilizzando Visual Studio, viene visualizzata la finestra **Visualizzazione topologie Storm.** Per visualizzare informazioni sulla topologia in esecuzione, selezionarla dall'elenco.

![Monitorare la topologia, finestra Visualizzazione tempestose, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> È inoltre possibile visualizzare le **topologie Storm ** da **Esplora Server**. Espandere **Azure** > **HDInsight**, fare clic con il pulsante destro del mouse su Storm nel cluster HDInsight e quindi scegliere **Visualizza topologie Storm**.

Selezionare la forma degli spout o dei bolt per visualizzare informazioni su questi componenti. Viene visualizzata una descrizione comando con le informazioni sul componente per l'elemento selezionato.

### <a name="deactivate-and-reactivate-a-topology"></a>Disattivare e riattivare una topologia

La disattivazione di una topologia la sospende fino a quando la topologia non viene interrotta o riattivata. Per eseguire queste operazioni, utilizzare i **pulsanti Disattiva** e **Riattiva** nell'area **Azioni** nella parte superiore della finestra **Vista topologie tempestose.**

### <a name="rebalance-a-topology"></a>Ribilanciare una topologia

Il ribilanciamento di una topologia consente al sistema di analizzare il parallelismo della topologia. Ad esempio, se il cluster è stato ridimensionato per aggiungere altre note, il ribilanciamento consente a una topologia di visualizzare i nuovi nodi.

Per ribilanciare una topologia, utilizzare il pulsante **Ribilancia** nell'area **Azioni** della finestra **Visualizzazione Topologie tempestose.**

> [!WARNING]  
> Il ribilanciamento di una topologia disattiva la topologia, ridistribuisce i lavoratori in modo uniforme nel cluster e quindi restituisce la topologia allo stato in cui si trovava prima che si verificasse il ribilanciamento. Se la topologia era attiva, diventa nuovamente attiva. Se la topologia è stata disattivata, rimane disattivata.

### <a name="kill-a-running-topology"></a>Terminare una topologia in esecuzione

Le topologie Storm continuano a essere eseguite fino all'arresto o all'eliminazione del cluster. Per arrestare una topologia, utilizzare il pulsante **Elimina** nell'area **Azioni.**

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitorare e gestire una topologia utilizzando SSH e il comando Tempesta

L'utilità `storm` consente di usare le topologie in esecuzione dalla riga di comando. Per visualizzare l'elenco completo dei comandi, usare `storm -h` .

### <a name="list-topologies"></a>Visualizzare l'elenco delle topologie

Usare il comando seguente per ottenere un elenco delle topologie in esecuzione:

```shell
storm list
```

Questo comando restituisce informazioni simili al testo seguente:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Disattivare e riattivare una topologia

La disattivazione di una topologia la sospende fino a quando la topologia non viene interrotta o riattivata. Utilizzare i seguenti comandi per disattivare o riattivare:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Terminare una topologia in esecuzione

Una volta avviate, le topologie Storm continueranno a rimanere in esecuzione finché non vengono arrestate. Per arrestare una topologia, usare il comando seguente:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Ribilanciare una topologia

Il ribilanciamento di una topologia consente al sistema di analizzare il parallelismo della topologia. Ad esempio, se il cluster è stato ridimensionato per aggiungere altre note, il ribilanciamento consente a una topologia di visualizzare i nuovi nodi.

> [!WARNING]  
> Il ribilanciamento di una topologia disattiva la topologia, ridistribuisce i lavoratori in modo uniforme nel cluster e quindi restituisce la topologia allo stato in cui si trovava prima che si verificasse il ribilanciamento. Se la topologia era attiva, diventa nuovamente attiva. Se invece era disattivata, rimarrà disattivata.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitorare e gestire una topologia utilizzando l'interfaccia utente di Storm

L'interfaccia utente di Storm fornisce un'interfaccia Web per l'utilizzo delle topologie in esecuzione ed è inclusa nel cluster HDInsight.The Storm UI provides a web interface for working with running topologies, and it is included on your HDInsight cluster. Per visualizzare l'interfaccia utente di Storm, usare un Web browser per aprire, `https://CLUSTERNAME.azurehdinsight.net/stormui`, dove *CLUSTERNAME* corrisponde al nome del cluster.

> [!NOTE]  
> Se viene chiesto di fornire un nome utente e una password, immettere il nome utente e la password dell'amministratore del cluster utilizzati durante la creazione del cluster.

### <a name="storm-ui-main-page"></a>Pagina principale dell'interfaccia utente di Storm

Nella pagina principale dell'interfaccia utente di Storm sono disponibili le informazioni seguenti.

| Sezione | Descrizione |
| --- | --- |
| Riepilogo del cluster| informazioni di base sul cluster Storm. |
| Riepilogo Nimbus | Elenco di informazioni di base su Nimbus. |
| Topology summary | elenco delle topologie in esecuzione. Per visualizzare ulteriori informazioni su una topologia specifica, selezionare il relativo collegamento nella colonna **Nome.** |
| Riepilogo supervisore | informazioni su Storm Supervisor. Per visualizzare le risorse del lavoratore associate a un supervisore specifico, selezionare il relativo collegamento nella colonna **Host** o **Id.** |
| Configurazione Nimbus | configurazione Nimbus per il cluster. |

La pagina principale dell'interfaccia utente di Storm è simile a questa pagina Web:

![Pagina principale, interfaccia utente Storm, topologie Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Topology summary

Se si seleziona un collegamento nella sezione **Topology summary** , verranno visualizzate le informazioni seguenti sulla topologia.

| Sezione | Descrizione |
| --- | --- |
| Topology summary | informazioni di base sulla topologia. |
| Azioni topologia| Azioni di gestione che è possibile eseguire per la topologia. Le azioni disponibili sono descritte più avanti in questa sezione. |
| Topology stats | statistiche sulla topologia. Per impostare l'intervallo di tempo per una voce in questa sezione, selezionare il relativo collegamento nella colonna **Finestra.** |
| *Spouts (intervallo di tempo)* | spout usati dalla topologia. Per visualizzare ulteriori informazioni su uno spout specifico, selezionare il relativo collegamento nella colonna **Id.** |
| Bulloni *(intervallo di tempo)* | bolt usati nella topologia. Per visualizzare ulteriori informazioni su un bullone specifico, selezionare il relativo collegamento nella colonna **Id.** |
| Risorse lavoratore | Elenco di risorse lavoratore. Per visualizzare ulteriori informazioni su una risorsa di lavoro specifica, selezionare il relativo collegamento nella colonna **Host.** |
| Visualizzazione topologia | Pulsante **Mostra visualizzazione** che visualizza una visualizzazione della topologia. |
| Topology configuration | configurazione della topologia selezionata. |

La pagina di riepilogo della topologia Storm è simile a questa pagina Web:

![Pagina di riepilogo della topologia, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

Nella sezione **Azioni topologia** è possibile selezionare i pulsanti seguenti per eseguire un'azione:

| Pulsante | Descrizione |
| --- | --- |
| Activate | riprende l'elaborazione di una topologia disattivata. |
| Disattivare | sospende una topologia in esecuzione. |
| Ribilanciare | regola il parallelismo della topologia. È necessario ribilanciare le topologie in esecuzione dopo aver modificato il numero di nodi nel cluster. Questa operazione consente alla topologia di regolare il parallelismo per compensare il numero aggiuntivo o ridotto di nodi nel cluster.<br/><br/>Per altre informazioni, vedere <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of an Apache Storm topology</a> (Informazioni sul parallelismo di una topologia Apache Storm).
| Kill | arresta una topologia Storm dopo il timeout specificato. |
| Debug | Avvia una sessione di debug per la topologia in esecuzione. |
| Interrompi debug | Termina la sessione di debug per la topologia in esecuzione. |
| Livello di registrazione delle modifiche | Modifica il livello di log di debug. |

##### <a name="spout-and-bolt-summary"></a>Spout e risone

Se si seleziona un elemento nella sezione **Spouts** o **Bolts**, verranno visualizzate le informazioni seguenti:

| Sezione | Descrizione |
| --- | --- |
| Riepilogo componenti | informazioni di base sullo spout o sul bolt. |
| Azioni dei componenti | **Pulsanti Debug** e **Interrompi debug.** |
| Statistiche spout o statistiche bullone | statistiche relative allo spout o al bolt. Per impostare l'intervallo di tempo per una voce in questa sezione, selezionare il relativo collegamento nella colonna **Finestra.** |
| (Solo bullone)<br/>Statistiche di*input (intervallo di tempo)* | informazioni sui flussi di input usati dal bolt. |
| Statistiche di *output (intervallo di tempo)* | informazioni sui flussi generati dallo spout o dal bolt. |
| Profilatura e debug | Controlli per la profilatura e il debug dei componenti in questa pagina. È possibile impostare il valore **Stato/Timeout (Minuti)** ed è possibile selezionare i pulsanti per **JStack**, **Riavvia lavoro**e **Heap**. |
| Esecutori *(intervallo di tempo)* | informazioni sulle istanze dello spout o del bolt. Per visualizzare un registro delle informazioni di diagnostica prodotte per questa istanza, selezionare la voce **Porta** per un esecutore specifico. È inoltre possibile visualizzare le risorse di lavoro associate a un esecutore specifico selezionando il relativo collegamento nella colonna **Host.** |
| Errors | informazioni su eventuali errori dello spout o del bolt. |

La pagina di riepilogo del bullone Storm è simile a questa pagina Web:

![Pagina di riepilogo bullone, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitorare e gestire la topologia tramite l'API REST

L'interfaccia utente di Storm si basa sull'API REST, pertanto è possibile eseguire attività di gestione e monitoraggio simili usando l'API REST. L'API REST consente di creare strumenti personalizzati per la gestione e il monitoraggio di topologie Storm.

Per altre informazioni, vedere l'articolo relativo all'[API REST dell'interfaccia utente di Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Le seguenti informazioni sono specifiche per l'uso dell'API REST con Apache Storm in HDInsight.

> [!IMPORTANT]  
> L'API REST di Storm non è disponibile pubblicamente su Internet. È necessario accedervi utilizzando un tunnel SSH per il nodo head del cluster HDInsight. Per informazioni sulla creazione e l'uso di un tunnel SSH, vedere [Usare il tunneling SSH per accedere ad Azure HDInsight.](../hdinsight-linux-ambari-ssh-tunnel.md)

### <a name="base-uri"></a>URI di base

L'URI di base per l'API REST nei cluster `https://HEADNODEFQDN:8744/api/v1/`HDInsight basati su Linux è disponibile all'indirizzo URL , dove si sostituisce *HEADNODEFQDN* con il nodo head. Il nome di dominio del nodo head viene generato durante la creazione del cluster e non è statico.

È possibile trovare il nome di dominio completo (FQDN) per il nodo head del cluster in diversi modi:You can find the fully qualified domain name (FQDN) for the cluster head node in several ways:

| Metodo di individuazione FQDN | Descrizione |
| --- | --- |
| Sessione SSH | usare il comando `headnode -f` da una sessione SSH al cluster. |
| Web Ambari | Nella pagina Web del cluster`https://CLUSTERNAME.azurehdinsight.net`Ambari ( ), selezionare **Servizi** nella parte superiore della pagina, quindi selezionare **Tempesta**. Dalla scheda **Riepilogo** selezionare **Storm UI Server** (Server dell'interfaccia utente di Storm). Il nome FQDN del nodo che ospita l'API REST e l'interfaccia utente di Storm è visualizzato nella parte superiore della pagina. |
| API REST Ambari | usare il comando `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` per recuperare informazioni sul nodo in cui sono in esecuzione l'interfaccia utente di Storm e l'API REST. Sostituire le due istanze di *CLUSTERNAME* con il nome del cluster. Quando richiesto, immettere la password per l'account utente (amministratore). Nella risposta, la voce "host_name" dell'output JSON contiene il nome di dominio completo del nodo. |

### <a name="authentication"></a>Authentication

Le richieste all'API REST devono utilizzare *l'autenticazione di base,* pertanto è necessario usare il nome e la password dell'amministratore per il cluster HDInsight.

> [!NOTE]  
> Poiché l'autenticazione di base viene inviata in testo non crittografato, è necessario usare *sempre* HTTPS per proteggere le comunicazioni con il cluster.

### <a name="return-values"></a>Valori restituiti

Le informazioni restituite dall'API REST possono essere usate solo nel cluster. Ad esempio, il nome di dominio completo (FQDN) restituito per i server [Apache](https://zookeeper.apache.org/) non è accessibile da Internet.

## <a name="next-steps"></a>Passaggi successivi

Vedere altre informazioni su come [sviluppare topologie basate su Java con Apache Maven](apache-storm-develop-java-topology.md).

Per un elenco di altre topologie di esempio, vedere Esempio di topologie Apache Storm in Azure HDInsight.For a list of more example [topologies, see Example Apache Storm topologies in Azure HDInsight](apache-storm-example-topology.md).
