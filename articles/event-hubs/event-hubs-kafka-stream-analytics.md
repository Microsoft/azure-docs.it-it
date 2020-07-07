---
title: Hub eventi di Azure - Elaborare eventi di Apache Kafka
description: 'Esercitazione: Questo articolo illustra come elaborare gli eventi di Kafka che vengono inseriti tramite hub eventi usando Analisi di flusso di Azure'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: 8fbc1ae326cc75603f5a86361e4bc79ecc461fd6
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85313266"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Esercitazione: Elaborare gli eventi di Apache Kafka per Hub eventi mediante Analisi di flusso 
Questo articolo illustra come eseguire lo streaming dei dati in Hub eventi ed elaborarli con Analisi di flusso di Azure. L'articolo include le istruzioni dettagliate per le operazioni seguenti: 

1. Creare uno spazio dei nomi di Hub eventi.
2. Creare un client Kafka che invia messaggi all'hub eventi.
3. Creare un processo di Analisi di flusso di Azure che copia i dati dall'hub eventi in un archivio BLOB di Azure. 

Non è necessario modificare i client di protocollo o eseguire i propri cluster quando si usa l'endpoint di Kafka esposto da un hub eventi. Hub eventi di Azure supporta [Apache Kafka versione 1.0.](https://kafka.apache.org/10/documentation.html) Supporta anche versioni successive. 


## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di soddisfare i requisiti seguenti:

* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Scaricare](https://maven.apache.org/download.cgi) e [installare](https://maven.apache.org/install.html) un archivio binario Maven.
* [Git](https://www.git-scm.com/)
* Un **account di archiviazione di Azure**. Se non è disponibile, [crearne uno](../storage/common/storage-account-create.md) prima di procedere. Il processo di Analisi di flusso in questa procedura archivia i dati di output in un archivio BLOB di Azure. 


## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi
Quando si crea uno spazio dei nomi di Hub eventi di livello **Standard**, l'endpoint Kafka per lo spazio dei nomi viene abilitato automaticamente. È possibile trasmettere eventi dalle applicazioni che usano il protocollo Kafka in Hub eventi di livello Standard. Seguire le istruzioni dettagliate illustrate in [Creare un hub eventi usando il portale di Azure](event-hubs-create.md) per creare uno spazio dei nomi di Hub eventi di livello **Standard**. 

> [!NOTE]
> Hub eventi per Kafka è disponibile solo nei livelli **Standard** e **Dedicato**. Il livello **Basic** non supporta Kafka in Hub eventi.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Inviare e ricevere messaggi con Kafka in Hub eventi

1. Clonare il [repository di Hub eventi di Azure per Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) nel proprio computer.
2. Passare alla cartella `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Aggiornare i dettagli di configurazione per il producer in `src/main/resources/producer.config`. Specificare il **nome** e la **stringa di connessione** per lo **spazio dei nomi dell'hub eventi**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Passare a `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/` e aprire il file **TestDataReporter.java** in un editor di propria scelta. 
6. Impostare un commento per la riga di codice seguente:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Aggiungere la riga di codice seguente al posto del codice commentato: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Questo codice invia i dati dell'evento in formato **JSON**. Quando si configura l'input per un processo di Analisi di flusso, si specifica il formato JSON per i dati di input. 
7. **Eseguire il producer** ed eseguire lo streaming in Hub eventi. In un computer Windows, quando si usa un **prompt dei comandi di Node.js**, passare alla cartella `azure-event-hubs-for-kafka/quickstart/java/producer` prima di eseguire questi comandi. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Verificare che l'hub eventi riceva i dati

1. Selezionare **Hub eventi** in **ENTITÀ**. Verificare che venga visualizzato un hub eventi denominato **test**. 

    ![Hub eventi - test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Verificare che siano visualizzati i messaggi in arrivo nell'hub eventi. 

    ![Hub eventi - messaggi](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Elaborare i dati dell'evento tramite un processo di Analisi di flusso di Azure
In questa sezione si crea un processo di Analisi di flusso di Azure. Il client Kafka invia eventi all'hub eventi. Si crea un processo di Analisi di flusso di Azure che accetta i dati degli eventi come input e li invia a un archivio BLOB di Azure. Se non si dispone di un **account di archiviazione di Azure**, [crearne uno](../storage/common/storage-account-create.md).

La query nel processo di Analisi di flusso di Azure attraversa i dati senza eseguire alcuna analisi. È possibile creare una query che trasforma i dati di input per produrre dati di output in un formato diverso o con informazioni approfondite ottenute.  

### <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso. 

1. Selezionare **+ Crea una risorsa** nel [portale di Azure](https://portal.azure.com).
2. Selezionare **Analytics** nel menu **Azure Marketplace** e selezionare **Processo di Analisi di Flusso**. 
3. Nella pagina **Nuovo processo di Analisi di flusso** eseguire queste azioni: 
    1. Immettere un **nome** per il processo. 
    2. Selezionare la propria **sottoscrizione**.
    3. Selezionare **Crea nuovo** e immettere un nome per il **gruppo di risorse**. È anche possibile **usare un gruppo di risorse esistente**. 
    4. Selezionare una **posizione** per il processo.
    5. Fare clic su **Crea** per creare il processo. 

        ![Nuovo processo di Analisi di flusso](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Configurare l'input del processo

1. Nel messaggio di notifica selezionare **Vai alla risorsa** per visualizzare la pagina **Processo di Analisi di flusso**. 
2. Selezionare **Input** nella sezione **TOPOLOGIA PROCESSO** nel menu di sinistra.
3. Selezionare **Aggiungi input del flusso** e selezionare **Hub eventi**. 

    ![Aggiungere un hub eventi come input](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Nella pagina di configurazione **Event Hub input** (Input hub eventi) eseguire queste azioni: 

    1. Specificare un **alias** per l'input. 
    2. Selezionare la **sottoscrizione di Azure**.
    3. Selezionare lo **spazio dei nomi dell'hub eventi** creato in precedenza. 
    4. Selezionare **test** per l'**hub eventi**. 
    5. Selezionare **Salva**. 

        ![Configurazione dell'input dell'hub eventi](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Configurare l'output del processo 

1. Selezionare **Output** nella sezione **TOPOLOGIA PROCESSO** nel menu. 
2. Selezionare **+ Aggiungi** nella barra degli strumenti e selezionare **Archiviazione BLOB**.
3. Nella pagina delle impostazioni dell'output dell'archivio BLOB eseguire queste azioni: 
    1. Specificare un **alias** per l'output. 
    2. Selezionare la **sottoscrizione**di Azure. 
    3. Selezionare il proprio **account di archiviazione di Azure**. 
    4. Immettere un **nome per il contenitore** che archivia i dati di output dalla query di Analisi di flusso.
    5. Selezionare **Salva**.

        ![Configurazione dell'output dell'archivio BLOB](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definire una query
Dopo aver configurato un processo di Analisi di flusso per leggere un flusso di dati in ingresso, il passaggio successivo consiste nel creare una trasformazione che analizza i dati in tempo reale. Per definire la query di trasformazione, si usa il [linguaggio di query di Analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). In questa procedura dettagliata si definisce una query che attraversa i dati senza apportare alcuna trasformazione.

1. Selezionare **Query**.
2. Nella finestra della query sostituire `[YourOutputAlias]` con l'alias di output creato in precedenza.
3. Sostituire `[YourInputAlias]` con l'alias di input creato in precedenza. 
4. Sulla barra degli strumenti selezionare **Salva**. 

    ![Query](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Eseguire il processo di Analisi di flusso

1. Fare clic su **Panoramica** nel menu di sinistra. 
2. Selezionare **Start**. 

    ![Menu Start](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Nella pagina **Avvia processo** selezionare **Avvia**. 

    ![Pagina Avvia processo](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Attendere che lo stato del processo cambi da **Starting** (In avvio) a **running** (in esecuzione). 

    ![Stato del processo: in esecuzione](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testare lo scenario
1. Eseguire di nuovo il **producer Kafka** per inviare gli eventi all'hub eventi. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Assicurarsi di visualizzare i **dati di output** generati nell'**archivio BLOB di Azure**. Viene visualizzato un file JSON nel contenitore con 100 righe simili alle righe di esempio seguenti: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    In questo scenario il processo di Analisi di flusso di Azure ha ricevuto i dati di input dall'hub eventi e li ha archiviati nell'archivio BLOB di Azure. 



## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come eseguire lo streaming in Hub eventi senza modificare i client di protocollo o eseguire cluster personalizzati. Per altre informazioni su Hub eventi per Apache Kafka, vedere [Guida per sviluppatori Apache Kafka per Hub eventi di Azure](apache-kafka-developer-guide.md). 