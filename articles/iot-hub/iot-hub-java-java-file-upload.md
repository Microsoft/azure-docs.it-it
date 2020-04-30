---
title: Caricare file dai dispositivi nell'hub IoT di Azure con Java | Microsoft Docs
description: Come caricare file da un dispositivo al cloud usando Azure IoT SDK per dispositivi per Java. I file caricati vengono salvati in un contenitore BLOB di archiviazione di Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f0753827fe5f7f2b866726683d4cb1f205da4599
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732472"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Caricare i file dal dispositivo al cloud con l'hub Internet (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Questa esercitazione si basa sul codice nell'esercitazione [inviare messaggi da cloud a dispositivo con l'hub](iot-hub-java-java-c2d.md) Internet per mostrare come usare le [funzionalità di caricamento dei file dell'hub](iot-hub-devguide-file-upload.md) Internet per caricare un file nell' [Archivio BLOB di Azure](../storage/index.yml). L'esercitazione illustra come:

* Specificare in modo sicuro un dispositivo con un URI del BLOB di Azure per il caricamento di un file.

* Usare le notifiche di caricamento di file dell'hub IoT per attivare l'elaborazione del file nel back-end dell'app.

L'esercitazione inviare i dati di [telemetria da un dispositivo a un hub](quickstart-send-telemetry-java.md) Internet e [inviare messaggi da cloud a dispositivo con l'hub](iot-hub-java-java-c2d.md) Internet illustra le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub Internet. L'esercitazione [Configurare il routing dei messaggi con l'IoT Hub](tutorial-routing.md) illustra come archiviare in modo affidabile i messaggi da dispositivo a cloud nell'archivio BLOB di Azure. Tuttavia in alcuni scenari non è possibile mappare facilmente i dati che i dispositivi inviano in messaggi relativamente ridotti da dispositivo a cloud, che l'hub IoT accetta. Ad esempio:

* File di grandi dimensioni che contengono immagini
* Video
* Dati di vibrazione campionati ad alta frequenza
* Qualche tipo di dati pre-elaborati.

Questi dati in genere vengono elaborati in batch nel cloud con strumenti come [Azure Data Factory](../data-factory/introduction.md) o lo stack [Hadoop](../hdinsight/index.yml). Quando è necessario caricare file da un dispositivo, è comunque possibile usare la sicurezza e l'affidabilità dell'hub IoT.

Al termine di questa esercitazione verranno eseguite due app console Java:

* **Simulated-Device**, una versione modificata dell'app creata nell'esercitazione [inviare messaggi da cloud a dispositivo con l'hub cose]. Ciò consente di caricare un file nell'archivio tramite un URI con firma di accesso condiviso fornito dall'hub IoT.

* **Read-file-upload-Notification**, che riceve le notifiche di caricamento file dall'hub Internet delle cose.

> [!NOTE]
> L'hub IoT supporta numerose piattaforme e linguaggi (inclusi C, .NET e Javascript) tramite gli Azure IoT SDK per dispositivi. Vedere il [Centro per sviluppatori di IoT di Azure](https://azure.microsoft.com/develop/iot) per istruzioni dettagliate su come connettere il dispositivo all'Hub IoT di Azure.

## <a name="prerequisites"></a>Prerequisiti

* [Java se Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Assicurarsi di selezionare **Java 8** in **Supporto a lungo termine** per passare ai download per JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo in questo articolo usa il protocollo MQTT, che comunica sulla porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Caricare un file da un'app per dispositivi

In questa sezione si modifica l'app per dispositivi creata in [inviare messaggi da cloud a dispositivo con l'hub](iot-hub-java-java-c2d.md) Internet per caricare un file nell'hub Internet.

1. Copiare un file di immagine nella cartella `simulated-device` e rinominarlo `myimage.png`.

2. Aprire il file `simulated-device\src\main\java\com\mycompany\app\App.java` in un editor di testo.

3. Aggiungere la dichiarazione di variabile alla classe **App**:

    ```java
    private static String fileName = "myimage.png";
    ```

4. Per elaborare i messaggi di richiamata dello stato di caricamento del file, aggiungere la classe annidata seguente alla classe **App**:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Per caricare le immagini nell'hub IoT, aggiungere il metodo seguente alla classe **App**:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. Modificare il metodo **main** per chiamare il metodo **uploadFile** come illustrato nel frammento di codice seguente:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. Usare il comando seguente per compilare l'app **simulated-device** e verificare la presenza di eventuali errori:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

In questo articolo viene creato un servizio back-end per ricevere i messaggi di notifica di caricamento file dall'hub di Internet delle cose creato in inviare dati di [telemetria da un dispositivo a un hub](quickstart-send-telemetry-java.md)Internet. Per ricevere i messaggi di notifica di caricamento file, il servizio richiede l'autorizzazione **Connect del servizio** . Per impostazione predefinita, ogni hub tutto viene creato con un criterio di accesso condiviso denominato **Service** che concede l'autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Ricevere la notifica di caricamento di un file

In questa sezione viene creata un'app console Java che riceve messaggi di notifica di caricamento file dall'hub IoT.

1. Creare un progetto Maven denominato **read-file-upload-notification** usando questo comando al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella `read-file-upload-notification`.

3. In un editor di testo aprire il file `pom.xml` nella cartella `read-file-upload-notification` e aggiungere la dipendenza seguente al nodo **dependencies**. L'aggiunta della dipendenza consente di usare il pacchetto **iothub-java-service-client** nell'applicazione per comunicare con il servizio hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-service-client** usando la [ricerca di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Salvare e chiudere il file `pom.xml`.

5. Aprire il file `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` in un editor di testo.

6. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire il `{Your IoT Hub connection string}` valore del segnaposto con la stringa di connessione dell'hub Internet che è stata copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string):

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Per stampare le informazioni relative al caricamento del file nella console, aggiungere la classe annidata seguente alla classe **App**:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. Per avviare il thread che rimane in ascolto delle notifiche di caricamento del file, aggiungere il codice seguente al metodo **main**:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. Salvare e chiudere il file `read-file-upload-notification\src\main\java\com\mycompany\app\App.java`.

11. Usare il comando seguente per compilare l'app **read-file-upload-notification** e verificare la presenza di eventuali errori:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

Al prompt dei comandi nella cartella `read-file-upload-notification` eseguire il comando seguente:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Al prompt dei comandi nella cartella `simulated-device` eseguire il comando seguente:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Lo screenshot seguente presenta l'output dell'app **simulated-device**:

![Output dell'app simulated-device](media/iot-hub-java-java-upload/simulated-device.png)

Lo screenshot seguente presenta l'output dell'app **read-file-upload-notification**:

![Output dell'app read-file-upload-notification](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Per visualizzare il file caricato nel contenitore di archiviazione configurato, è possibile usare il portale:

![File caricato](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT vedendo i seguenti articoli:

* [Creare un hub IoT a livello di codice](iot-hub-rm-template-powershell.md)

* [Introduzione a C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Simulazione di un dispositivo con IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
