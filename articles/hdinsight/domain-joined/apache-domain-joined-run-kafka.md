---
title: Esercitazione - Apache Kafka ed Enterprise Security - Azure HDInsight
description: Esercitazione - Informazioni su come configurare i criteri di Apache Ranger per Kafka in Azure HDInsight con Enterprise Security Package.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 552998de23bebd98f56ba28eb6ad581689e52e41
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933691"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Esercitazione: Configurare i criteri di Apache Kafka in HDInsight con Enterprise Security Package (anteprima)

Informazioni su come configurare i criteri di Apache Ranger per i cluster Apache Kafka Enterprise Security Package (ESP). Gli ESP cluster sono connessi a un dominio che consente agli utenti di eseguire l'autenticazione con le credenziali di dominio. In questa esercitazione vengono creati due criteri di Ranger per limitare l'accesso a `sales` e `marketingspend`.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare utenti del dominio
> * Creazione dei criteri di Ranger
> * Creare argomenti in un cluster Kafka
> * Testare i criteri di Ranger

## <a name="prerequisite"></a>Prerequisito

Un [cluster HDInsight Kafka con Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Connettersi all'interfaccia utente di amministrazione di Apache Ranger

1. Da un browser, connettersi all'interfaccia utente di amministrazione di Ranger usando l'URL `https://ClusterName.azurehdinsight.net/Ranger/`. Ricordarsi di modificare `ClusterName` con il nome del cluster Kafka. Le credenziali di Ranger non sono le stesse usate del cluster Hadoop. Per evitare che i browser usino credenziali memorizzate nella cache di Hadoop, usare una nuova finestra del browser InPrivate per connettersi all'interfaccia utente di Ranger Admin.

2. Accedere usando le credenziali di amministratore di Azure Active Directory (AD). Le credenziali di amministratore di Azure AD non sono le stesse del cluster HDInsight o del nodo SSH di Linux HDInsight.

   ![Interfaccia utente di amministrazione di Apache Ranger di HDInsight](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Creare utenti del dominio

Visitare [Creare un cluster HDInsight con Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md) per informazioni su come creare gli utenti del dominio **sales_user** e **marketing_user**. In uno scenario di produzione, gli utenti del dominio provengono dal tenant di Active Directory.

## <a name="create-ranger-policy"></a>Creare un criterio di Ranger

Creare un criterio di Ranger per **sales_user** e **marketing_user**.

1. Aprire l’**interfaccia utente di amministrazione di Ranger**.

2. Selezionare **\<ClusterName>_kafka** in **Kafka**. Potrebbe essere presente un criterio preconfigurato.

3. Selezionare **Aggiungi nuovo criterio** e immettere i valori seguenti:

   |Impostazione  |Valore consigliato  |
   |---------|---------|
   |Nome criterio  |  hdi sales* policy   |
   |Argomento   |  sales* |
   |Seleziona utente  |  sales_user1 |
   |Autorizzazioni  | publish, consume, create |

   I seguenti caratteri jolly possono essere inclusi nel nome dell'argomento:

   * "*" indica zero o più occorrenze del carattere.
   * "?" indica un carattere singolo.

   ![Creazione di un criterio 1 nell'interfaccia utente di amministrazione di Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Attendere alcuni istanti per eseguire la sincronizzazione con Azure AD, se un utente di dominio non viene popolato automaticamente per **Seleziona utente**.

4. Selezionare **Aggiungi** per salvare il criterio.

5. Selezionare **Aggiungi nuovo criterio** e quindi immettere i valori seguenti:

   |Impostazione  |Valore consigliato  |
   |---------|---------|
   |Nome criterio  |  hdi marketing policy   |
   |Argomento   |  marketingspend |
   |Seleziona utente  |  marketing_user1 |
   |Autorizzazioni  | publish, consume, create |

   ![Creazione di un criterio 2 nell'interfaccia utente di amministrazione di Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Selezionare **Aggiungi** per salvare il criterio.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Creare argomenti in un cluster Kafka con ESP

Per creare due argomenti `salesevents` e `marketingspend`:

1. Per aprire una connessione SSH al cluster, usare il comando seguente:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Sostituire `DOMAINADMIN` con l'utente amministratore per il cluster configurato durante la [creazione del cluster](./apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp) e sostituire `CLUSTERNAME` con il nome del cluster. Quando richiesto, immettere la password per l'account utente amministratore. Per altre informazioni sull'uso di `SSH` con HDInsight, vedere [Use SSH with HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) (Uso di SSH con HDInsight).

2. Per salvare il nome del cluster in una variabile e installare un'utilità di analisi JSON `jq`, usare i comandi seguenti. Quando richiesto, immettere il nome del cluster Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Per ottenere gli host del broker Kafka, usare i comandi seguenti. Quando richiesto, immettere la password dell'account amministratore del cluster.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Prima di continuare, può essere necessario configurare l'ambiente di sviluppo se non è stato ancora fatto. Saranno necessari componenti quali Java JDK, Apache Maven e un client SSH con SCP. Per altre informazioni, vedere le [istruzioni di configurazione](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Scaricare gli [esempi di producer e consumer Apache Kafka aggiunti a dominio](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Seguire i passaggi 2 e 3 sotto **Compilare e distribuire l'esempio** in [Esercitazione: Usare le API Apache Kafka Producer e Consumer](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)

1. Eseguire i comandi seguenti:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testare i criteri di Ranger

In base ai criteri di Ranger configurati, **sales_user** può creare o utilizzare risorse per l'argomento `salesevents` ma non per l'argomento `marketingspend`. Al contrario, **marketing_user** può creare o utilizzare risorse per l'argomento `marketingspend` ma non per l'argomento `salesevents`.

1. Aprire una nuova connessione SSH al cluster. Eseguire questo comando per accedere come  **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Usare i nomi dei broker della sezione precedente per impostare le variabili di ambiente seguenti:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Esempio: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

3. Seguire il passaggio 3 in **Compilare e distribuire l'esempio** dell'[Esercitazione: Usare le API Apache Kafka Producer e Consumer](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) per assicurarsi che `kafka-producer-consumer.jar` sia disponibile anche per **sales_user**.

   > [!NOTE]  
   > Per questa esercitazione, usare il file kafka-producer-consumer.jar nel progetto "DomainJoined-Producer-Consumer" (non quello nel progetto Producer-Consumer, che serve per gli scenari non aggiunti a un dominio).

4. Verificare che **sales_user1** possa creare risorse nell'argomento `salesevents` eseguendo il comando seguente:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

5. Eseguire il comando seguente per utilizzare risorse dall'argomento `salesevents`:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Verificare che sia possibile leggere i messaggi.

6. Verificare che **sales_user1** possa creare risorse nell'argomento `marketingspend` eseguendo il comando seguente nella stessa finestra ssh:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Si verifica un errore di autorizzazione che può essere ignorato.

7. Si noti che **marketing_user1** non può utilizzare risorse dall'argomento `salesevents`.

   Ripetere i passaggi da 1 a 3 di cui sopra, ma questa volta come **marketing_user1**.

   Eseguire il comando seguente per utilizzare risorse dall'argomento `salesevents`:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   I messaggi precedenti non possono essere visualizzati.

8. Visualizzare gli eventi di accesso di controllo dall'interfaccia utente di Ranger.

   ![Eventi di accesso di controllo dei criteri dell'interfaccia utente di Ranger ](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)
   
## <a name="produce-and-consume-topics-in-esp-kafka-by-using-the-console"></a>Creare e utilizzare argomenti in ESP Kafka con la console

> [!NOTE]
> Non è possibile usare i comandi della console per creare argomenti. È invece necessario usare il codice Java illustrato nella sezione precedente. Per altre informazioni, vedere [Creare argomenti in un cluster Kafka con ESP](#create-topics-in-a-kafka-cluster-with-esp).

Per creare e utilizzare argomenti in ESP Kafka con la console:

1. Usare `kinit` specificando il nome utente dell'utente. Immettere la password quando richiesto.

   ```bash
   kinit sales_user1
   ```

2. Impostare le variabili di ambiente:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf"
   export KAFKABROKERS=<brokerlist>:9092
   ```

3. Creare messaggi nell'argomento `salesevents`:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --topic salesevents --broker-list $KAFKABROKERS --security-protocol SASL_PLAINTEXT
   ```

4. Utilizzare i messaggi dell'argomento `salesevents`:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --topic salesevents --from-beginning --bootstrap-server $KAFKABROKERS --security-protocol SASL_PLAINTEXT
   ```

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eseguire questa procedura per eliminare il cluster Kafka creato:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Nella casella **Ricerca** in alto digitare **HDInsight**.
1. Selezionare **Cluster HDInsight** in **Servizi**.
1. Nell'elenco di cluster HDInsight visualizzato, fare clic su **...** accanto al cluster creato per questa esercitazione. 
1. Fare clic su **Elimina**. Fare clic su **Sì**.

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se kafka-producer-consumer.jar non funziona in un cluster aggiunto a dominio, assicurarsi di usare il file kafka-producer-consumer.jar nel progetto "DomainJoined-Producer-Consumer" (non quello nel progetto Producer-Consumer, che serve per gli scenari non aggiunti a un dominio).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Crittografia dischi con chiavi gestite dal cliente](../disk-encryption.md)
