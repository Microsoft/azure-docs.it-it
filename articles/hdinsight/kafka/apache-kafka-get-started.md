---
title: 'Guida introduttiva: Configurare Apache Kafka in HDInsight usando il portale di Azure'
description: Questa guida di avvio rapido illustra come creare un cluster Apache Kafka in Azure HDInsight tramite il portale di Azure. Illustra inoltre gli argomenti, i sottoscrittori e i consumer di Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/24/2020
ms.openlocfilehash: 90f7010970f70379c8adecc4214c44d896a1beaf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130214"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Guida introduttiva: Creare cluster Apache Kafka in HDInsight di Azure usando il portale di Azure

[Apache Kafka](./apache-kafka-introduction.md) è una piattaforma di streaming open source distribuita. Viene spesso usata come broker di messaggi perché offre funzionalità simili a una coda messaggi di pubblicazione/sottoscrizione.

Questo argomento di avvio rapido illustra come creare un cluster Apache Kafka tramite il portale di Azure. Illustra anche come usare le utilità incluse per inviare e ricevere messaggi tramite Apache Kafka. Per una spiegazione approfondita delle configurazioni disponibili, vedere [Configurare i cluster in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Per altre informazioni sull'uso del portale per la creazione di cluster, vedere [Creare cluster nel portale](../hdinsight-hadoop-create-linux-clusters-portal.md).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Possono accedere all'API Apache Kafka solo risorse interne alla stessa rete virtuale. In questa guida di avvio rapido si accede al cluster direttamente usando SSH. Per connettere altri servizi, reti o macchine virtuali ad Apache Kafka, è necessario prima di tutto creare una rete virtuale e quindi creare le risorse all'interno della rete. Per altre informazioni, vedere il documento [Connettersi ad Apache Kafka da una rete locale](apache-kafka-connect-vpn-gateway.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Creare un cluster Apache Kafka

Per creare un cluster Apache Kafka in HDInsight seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu in alto selezionare **+ Crea una risorsa**.

    ![Creazione di una risorsa HDInsight nel portale di Azure](./media/apache-kafka-get-started/azure-portal-create-resource.png)

1. Selezionare **Analytics** > **Azure HDInsight** per passare alla pagina **Crea cluster HDInsight**.

1. Nella scheda **Nozioni di base** specificare le informazioni seguenti:

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Subscription    |  Nell'elenco a discesa selezionare la sottoscrizione di Azure che viene usata per il cluster. |
    |Resource group     | Creare un gruppo di risorse o selezionarne uno esistente.  Un gruppo di risorse è un contenitore di componenti di Azure.  In questo caso, il gruppo di risorse contiene il cluster HDInsight e l'account di Archiviazione di Azure dipendente. |
    |Nome cluster   | Immettere un nome univoco globale. Il nome può includere al massimo 59 caratteri, tra cui lettere, numeri e trattini. Si noti che il primo e l'ultimo carattere del nome non possono essere trattini. |
    |Region    | Nell'elenco a discesa selezionare un'area in cui viene creato il cluster.  Scegliere un'area vicina all'utente per ottenere prestazioni migliori. |
    |Tipo di cluster| Scegliere **Selezionare il tipo di cluster** per aprire un elenco. Nell'elenco selezionare **Kafka** come tipo di cluster.|
    |Versione|Verrà specificata la versione predefinita per il tipo di cluster. Selezionare dall'elenco a discesa se si vuole specificare una versione diversa.|
    |Nome utente e password di accesso del cluster    | Il nome di accesso predefinito è **admin**. La password deve avere una lunghezza minima di 10 caratteri e deve contenere almeno una cifra, una lettera maiuscola, una lettera minuscola e un carattere non alfanumerico, ad eccezione di ' " ` \). Assicurarsi di **non fornire** password comuni, ad esempio "Pass@word1".|
    |Nome utente Secure Shell (SSH) | Il nome utente predefinito è **sshuser**.  È possibile fornire un altro nome come nome utente SSH. |
    |Usare la password di accesso del cluster per SSH| Selezionare questa casella di controllo se si vuole usare per l'utente SSH la stessa password fornita per l'utente di accesso del cluster.|

   ![Informazioni di base per la creazione del cluster nel portale di Azure](./media/apache-kafka-get-started/azure-portal-cluster-basics.png)

    Ogni area (posizione) di Azure fornisce _domini di errore_. Un dominio di errore è un raggruppamento logico dell'hardware sottostante in un data center di Azure. Ogni dominio di errore condivide una fonte di alimentazione e un commutatore di rete comuni. Le macchine virtuali e i dischi gestiti che implementano i nodi in un cluster HDInsight sono distribuiti tra i domini di errore. Questa architettura limita il potenziale impatto dei guasti dell'hardware fisico.

    Per la disponibilità elevata dei dati, selezionare una posizione (area) contenente __tre domini di errore__. Per informazioni sul numero di domini di errore in un'area, vedere il documento [Disponibilità delle macchine virtuali Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

    Fare clic sul pulsante **Avanti: Archiviazione>>** per passare alle impostazioni di archiviazione.

1. Nella scheda **Archiviazione** specificare i valori seguenti:

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Tipo di archiviazione primario|Usare il valore predefinito **Archiviazione di Azure**.|
    |Metodo di selezione|Usare il valore predefinito **Selezionare dall'elenco**.|
    |Account di archiviazione primario|Usare l'elenco a discesa per scegliere un account di archiviazione esistente oppure selezionare **Crea nuovo**. Se si crea un nuovo account, il nome deve avere una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole|
    |Contenitore|Usare il valore inserito automaticamente.|

    ![Introduzione alla creazione di un cluster HDInsight Linux - Specifica dei valori di archiviazione](./media/apache-kafka-get-started/azure-portal-cluster-storage.png "Specifica dei valori di archiviazione per la creazione di un cluster HDInsight")

    Selezionare la scheda **Sicurezza + rete**.

1. Per questa guida introduttiva non modificare le impostazioni di sicurezza predefinite. Per altre informazioni su Enterprise Security Package, vedere [Configurare un cluster HDInsight con Enterprise Security Package usando Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Per informazioni su come usare una chiave personalizzata per la crittografia dei dischi per Apache Kafka, vedere [Crittografia dischi con chiavi gestite dal cliente](../disk-encryption.md)

   Se si vuole connettere il cluster a una rete virtuale, selezionare una rete virtuale nell'elenco a discesa **Rete virtuale**.

   ![Aggiungere il cluster a una rete virtuale](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png)

    Scegliere la scheda **Configurazione + prezzi**.

1. Per garantire la disponibilità di Apache Kafka in HDInsight, la voce relativa al __numero di nodi__ per il **nodo di lavoro** deve essere impostata almeno su 3. Il valore predefinito è 4.

    La voce relativa ai **dischi standard per nodo di lavoro** consente di configurare la scalabilità di Apache Kafka in HDInsight. Per archiviare i dati, Apache Kafka in HDInsight usa il disco locale delle macchine virtuali nel cluster. Dal momento che in Apache Kafka i processi I/O sono intensivi, viene usato il servizio [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) per assicurare una velocità effettiva elevata e fornire maggiore spazio di archiviazione per ogni nodo. Il tipo di disco gestito può essere __Standard__ (HDD) o __Premium__ (SSD). Il tipo di disco dipende dalle dimensioni della macchina virtuale usate dai nodi del ruolo di lavoro (broker Apache Kafka). I dischi Premium vengono usati con le macchine virtuali serie DS e GS. Tutti gli altri tipi di macchine virtuali usano dischi Standard.

   ![Impostare le dimensioni del cluster Apache Kafka](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Selezionare la scheda **Rivedi e crea**.

1. Verificare la configurazione del cluster. Modificare le impostazioni non corrette. Infine, scegliere **Crea** per creare il cluster.

    ![Riepilogo della configurazione del cluster Kafka](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    La creazione del cluster può richiedere fino a 20 minuti.

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Quando richiesto, immettere la password per l'utente SSH.

    Dopo avere eseguito la connessione, vengono visualizzate informazioni simili al testo seguente:

    ```output
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    83 packages can be updated.
    37 updates are security updates.


    Welcome to Apache Kafka on HDInsight.

    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Ottenere le informazioni sugli host Apache Zookeeper e broker

Quando si usa Kafka, è necessario conoscere gli host *Apache Zookeeper* e *broker*. Questi host vengono usati con l'API Apache Kafka e molte delle utilità offerte con Kafka.

In questa sezione si ottengono le informazioni sull'host dall'API REST Apache Ambari nel cluster.

1. Installare [jq](https://stedolan.github.io/jq/), un processore JSON da riga di comando. Questa utilità consente di analizzare i documenti JSON ed è utile nell'analisi delle informazioni sull'host. Dalla connessione SSH aperta, immettere il comando seguente per installare `jq`:

    ```bash
    sudo apt -y install jq
    ```

1. Configurare la variabile di password. Sostituire `PASSWORD` con la password di accesso al cluster e quindi immettere il comando:

    ```bash
    export password='PASSWORD'
    ```

1. Estrarre il nome del cluster con l'uso corretto di maiuscole e minuscole. L'uso effettivo di maiuscole e minuscole nel nome del cluster può differire dal previsto, a seconda della modalità di creazione del cluster. Questo comando otterrà la combinazione di maiuscole e minuscole effettiva e quindi la archivierà in una variabile. Immettere il comando seguente:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Se si esegue questo processo dall'esterno del cluster, è disponibile una procedura diversa per l'archiviazione del nome del cluster. Recuperare il nome del cluster in lettere minuscole dal portale di Azure. Sostituire quindi `<clustername>` con il nome del cluster nel comando seguente ed eseguire il comando: `export clusterName='<clustername>'`.


1. Per impostare una variabile di ambiente con le informazioni degli host Zookeeper, usare il comando seguente. Il comando recupera tutti gli host Zookeeper, quindi restituisce solo le prime due voci. per mantenere un certo livello di ridondanza nel caso in cui un host fosse irraggiungibile.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Questo comando richiede l'accesso ad Ambari. Se il cluster è protetto da un gruppo NSG, eseguire questo comando da un computer in grado di accedere ad Ambari. 

1. Usare il comando seguente per verificare che la variabile di ambiente sia impostata correttamente:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    Questo comando restituisce informazioni simili al testo seguente:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. Per impostare una variabile di ambiente con le informazioni degli host broker Apache Kafka, usare il comando seguente:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Questo comando richiede l'accesso ad Ambari. Se il cluster è protetto da un gruppo NSG, eseguire questo comando da un computer in grado di accedere ad Ambari. 

1. Usare il comando seguente per verificare che la variabile di ambiente sia impostata correttamente:

    ```bash
    echo $KAFKABROKERS
    ```

    Questo comando restituisce informazioni simili al testo seguente:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Gestire gli argomenti di Apache Kafka

Kafka archivia i flussi di dati in *argomenti*. Per gestire gli argomenti è possibile usare l'utilità `kafka-topics.sh`.

* **Per creare un argomento** usare il comando seguente nella connessione SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Questo comando si connette a Zookeeper usando le informazioni sull'host archiviate in `$KAFKAZKHOSTS` e quindi crea un argomento di Apache Kafka denominato **test**.

    * I dati archiviati in questo argomento vengono divisi in otto partizioni.

    * Ogni partizione è replicata in tre nodi di ruolo di lavoro nel cluster.

        Se il cluster è stato creato in un'area di Azure con tre domini di errore, usare il fattore di replica 3. In caso contrario usare il fattore di replica 4.
        
        Nelle aree con tre domini di errore, il fattore di replica 3 consente di distribuire le repliche tra i domini di errore. Nelle aree con due domini di errore, il fattore di replica 4 distribuisce le repliche uniformemente tra i domini.
        
        Per informazioni sul numero di domini di errore in un'area, vedere il documento [Disponibilità delle macchine virtuali Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        Apache Kafka non rileva i domini di errore di Azure. Quando si creano le repliche di partizione per gli argomenti, è possibile che le repliche non vengano distribuite in modo corretto per la disponibilità elevata.

        Per garantire la disponibilità elevata, usare lo [strumento per il ribilanciamento delle partizioni Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Questo strumento deve essere eseguito da una connessione SSH al nodo head del cluster Apache Kafka.

        Per garantire la massima disponibilità dei dati Apache Kafka, è consigliabile ribilanciare le repliche di partizione per l'argomento nelle situazioni seguenti:

        * Quando si crea un nuovo argomento o una nuova partizione

        * Quando si aumentano le prestazioni di un cluster

* **Per elencare gli argomenti** usare il comando seguente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Questo comando consente di elencare gli argomenti disponibili nel cluster Apache Kafka.

* **Per eliminare un argomento** usare il comando seguente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Questo comando elimina l'argomento denominato `topicname`.

    > [!WARNING]  
    > Se si elimina l'argomento `test` creato in precedenza, è necessario crearlo di nuovo. Questo argomento si userà in passaggi indicati più avanti in questo documento.

Per altre informazioni sui comandi disponibili con l'utilità `kafka-topics.sh`, usare il comando seguente:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Produrre e utilizzare record

Kafka archivia i *record* negli argomenti. I record vengono prodotti da *producer* e usati da *consumer*. I producer e i consumer comunicano con il servizio *broker Kafka*. Ogni nodo del ruolo di lavoro nel cluster HDInsight è un host del broker Apache Kafka.

Seguire questa procedura per archiviare i record nell'argomento test creato in precedenza e quindi leggerli usando un consumer:

1. Per scrivere i record nell'argomento usare l'utilità `kafka-console-producer.sh` dalla connessione SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Dopo questo comando viene visualizzata una riga vuota.

2. Digitare un messaggio di testo nella riga vuota e premere INVIO. Digitare invece alcuni messaggi in questo modo e quindi usare **Ctrl + C** per tornare al prompt normale. Ogni riga viene inviata come record distinto all'argomento Apache Kafka.

3. Per scrivere i record dall'argomento usare l'utilità `kafka-console-consumer.sh` dalla connessione SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Questo comando recupera i record dall'argomento e li visualizza. L'uso di `--from-beginning` indica al consumer di partire dall'inizio del flusso, quindi verranno recuperati tutti i record.

    Se si usa una versione meno recente di Kafka, sostituire `--bootstrap-server $KAFKABROKERS` con `--zookeeper $KAFKAZKHOSTS`.

4. Usare __Ctrl + C__ per arrestare il consumer.

È possibile creare producer e consumer anche a livello di codice. Per un esempio dell'uso di questa API, vedere il documento relativo alle [API Producer e Consumer Apache Kafka con HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create da questa guida introduttiva, è possibile eliminare il gruppo di risorse. Se si elimina il gruppo di risorse, vengono eliminati anche il cluster HDInsight associato e tutte le altre risorse correlate al gruppo di risorse.

Per rimuovere il gruppo di risorse usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e quindi scegliere __Gruppi di risorse__ per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse da eliminare e quindi fare clic con il pulsante destro del mouse su __Altro__ (...) a destra dell'elenco.
3. Scegliere __Elimina gruppo di risorse__ e quindi confermare.

> [!WARNING]  
> Se si elimina un cluster Apache Kafka in HDInsight, vengono eliminati anche eventuali dati archiviati in Kafka.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare Apache Spark con Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
