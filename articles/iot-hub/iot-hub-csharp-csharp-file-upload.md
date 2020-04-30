---
title: Caricare file dai dispositivi nell'hub IoT con .NET | Microsoft Docs
description: Come caricare file da un dispositivo al cloud usando Azure IoT SDK per dispositivi per .NET. I file caricati vengono salvati in un contenitore BLOB di archiviazione di Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 14472e84d425bf03a3c6a0c2dc558d4b8225caec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733407"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Caricare i file dal dispositivo al cloud con l'hub Internet (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Questa esercitazione si basa sul codice nell'esercitazione [inviare messaggi da cloud a dispositivo con l'hub](iot-hub-csharp-csharp-c2d.md) Internet per mostrare come usare le funzionalità di caricamento dei file dell'hub Internet. Illustra le operazioni seguenti:

* Specificare in modo sicuro un dispositivo con un URI del BLOB di Azure per il caricamento di un file.

* Usare le notifiche di caricamento di file dell'hub IoT per attivare l'elaborazione del file nel back-end dell'app.

L'esercitazione inviare i dati di [telemetria da un dispositivo a un hub](quickstart-send-telemetry-dotnet.md) Internet e [inviare messaggi da cloud a dispositivo con l'hub](iot-hub-csharp-csharp-c2d.md) Internet illustra le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub Internet. L'esercitazione configurare il routing dei messaggi [con l'hub](tutorial-routing.md) Internet viene descritto come archiviare in modo affidabile i messaggi da dispositivo a cloud nell'archivio BLOB Microsoft Azure. Tuttavia, in alcuni scenari non è possibile mappare facilmente i dati che i dispositivi inviano ai messaggi relativamente piccoli da dispositivo a cloud accettati dall'hub. Ad esempio:

* File di grandi dimensioni che contengono immagini

* Video

* Dati di vibrazione campionati ad alta frequenza

* Qualche tipo di dati pre-elaborati

Questi dati in genere vengono elaborati in batch nel cloud con strumenti come [Azure Data Factory](../data-factory/introduction.md) o lo stack [Hadoop](../hdinsight/index.yml). Quando è necessario caricare i file da un dispositivo, è comunque possibile usare la sicurezza e affidabilità dell'hub IoT.

Al termine di questa esercitazione vengono eseguite due app console .NET:

* **SimulatedDevice**. Ciò consente di caricare un file nell'archivio tramite un URI con firma di accesso condiviso fornito dall'hub IoT. Si tratta di una versione modificata dell'app creata nell'esercitazione [inviare messaggi da cloud a dispositivo con l'hub](iot-hub-csharp-csharp-c2d.md) Internet.

* **ReadFileUploadNotification**. Questa app riceve le notifiche di caricamento file dall'hub Internet delle cose.

> [!NOTE]
> L'hub Internet delle cose supporta molte piattaforme e linguaggi di dispositivi, tra cui C, Java, Python e JavaScript, tramite gli SDK per dispositivi Azure. Vedere il [Centro per sviluppatori di IoT di Azure](https://azure.microsoft.com/develop/iot) per istruzioni dettagliate su come connettere il dispositivo all'Hub IoT di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio

* Un account Azure attivo. Se non si dispone di un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo in questo articolo usa il protocollo MQTT, che comunica sulla porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Caricare un file da un'app per dispositivi

In questa sezione si modifica l'app per dispositivi creata in [inviare messaggi da cloud a dispositivo con l'hub](iot-hub-csharp-csharp-c2d.md) Internet per ricevere i messaggi da cloud a dispositivo dall'hub Internet delle cose.

1. In Visual Studio Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto **SimulatedDevice** e scegliere **Aggiungi** > **elemento esistente**. Trovare un file di immagine e includerlo nel progetto. Nell'esercitazione si presuppone che l'immagine sia denominata `image.jpg`.

1. Fare clic con il pulsante destro del mouse sull'immagine, quindi scegliere **Proprietà**. Controllare che **Copia nella directory di output** sia impostato su **Copia sempre**.

    ![Mostra la posizione in cui aggiornare le proprietà dell'immagine per la copia nella directory di output](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. Nel file **Program.cs** aggiungere le istruzioni seguenti all’inizio del file:

    ```csharp
    using System.IO;
    ```

1. Aggiungere il seguente metodo alla classe **Program**:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    Il metodo `UploadToBlobAsync` accetta il nome del file e l'origine del flusso del file da caricare e gestisce il caricamento nell'archiviazione. Nell'app console viene visualizzato il tempo necessario per caricare il file.

1. Aggiungere la riga seguente nel metodo **Main** , immediatamente prima `Console.ReadLine()`di:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio backoff esponenziali, come suggerito nella [gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

In questo articolo viene creato un servizio back-end per ricevere i messaggi di notifica di caricamento file dall'hub di Internet delle cose creato in inviare dati di [telemetria da un dispositivo a un hub](quickstart-send-telemetry-dotnet.md)Internet. Per ricevere i messaggi di notifica di caricamento file, il servizio richiede l'autorizzazione **Connect del servizio** . Per impostazione predefinita, ogni hub tutto viene creato con un criterio di accesso condiviso denominato **Service** che concede l'autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Ricevere la notifica di caricamento di un file

In questa sezione verrà scritta un'app console di .NET che riceve messaggi di notifica di caricamento dall'hub IoT.

1. Nella soluzione di Visual Studio corrente selezionare **file** > **nuovo** > **progetto**. In **Crea un nuovo progetto**selezionare **App Console (.NET Framework)** e quindi fare clic su **Avanti**.

1. Denominare il progetto *ReadFileUploadNotification*. In **soluzione**selezionare **Aggiungi a soluzione**. Selezionare **Crea** per creare il progetto.

    ![Configurare il progetto ReadFileUploadNotification in Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ReadFileUploadNotification** e scegliere **Gestisci pacchetti NuGet**.

1. In **Gestione pacchetti NuGet**selezionare **Sfoglia**. Cercare e selezionare **Microsoft. Azure. Devices**, quindi selezionare **Install (installa**).

    Questo passaggio consente di scaricare, installare e aggiungere un riferimento al [pacchetto NuGet del servizio Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) per gli SDK nel progetto **ReadFileUploadNotification** .

1. Nel file **Program.cs** per questo progetto, aggiungere l'istruzione seguente all'inizio del file:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire il `{iot hub connection string}` valore del segnaposto con la stringa di connessione dell'hub Internet che è stata copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string):

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Aggiungere il seguente metodo alla classe **Program**:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Si noti che il modello di ricezione è identico a quello usato per ricevere messaggi da cloud a dispositivo dall'app per dispositivo.

1. Aggiungere infine le righe seguenti al metodo **Main** :

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Imposta progetti di avvio**.

1. In > **progetto di avvio** **Proprietà comuni**selezionare **progetti di avvio multipli**, quindi selezionare l'azione di **avvio** per **ReadFileUploadNotification** e **SimulatedDevice**. Selezionare **OK** per salvare le modifiche.

1. Premere **F5**. Si avviano entrambe le applicazioni. Verrà visualizzato il messaggio di completamento del caricamento in un'applicazione console e il messaggio di notifica di caricamento ricevuto da altre applicazioni console. È possibile usare il [portale di Azure](https://portal.azure.com/) o Esplora server di Visual Studio per verificare la presenza del file caricato nell'account di archiviazione di Azure.

    ![Schermata che mostra le opzioni di output](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub Internet con gli articoli seguenti:

* [Creare un hub IoT a livello di codice](iot-hub-rm-template-powershell.md)

* [Introduzione a C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
