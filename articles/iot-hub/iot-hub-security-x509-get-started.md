---
title: Esercitazione per la sicurezza X.509 nell'hub IoT di Azure | Microsoft Docs
description: Introduzione alla sicurezza basata su X.509 nell'hub IoT di Azure in un ambiente simulato.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: a3e328418a0f111cd0b985310ea6dc497999772d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92909795"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Configurare la sicurezza X.509 nell'hub IoT di Azure

Questa esercitazione illustra i passaggi necessari per proteggere l'hub Azure Internet con l' *autenticazione del certificato X. 509*. A scopo illustrativo, viene usato lo strumento open source OpenSSL per creare certificati localmente nel computer Windows. È consigliabile usare questa esercitazione solo a scopo di test. Per un ambiente di produzione, è necessario acquistare i certificati da un' *autorità di certificazione (CA) radice*. Inoltre, nell'ambiente di produzione, assicurarsi di disporre di una strategia per gestire il rollover del certificato quando un certificato del dispositivo o un certificato della CA scade.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione è necessario disporre delle risorse seguenti pronte:

* Un hub IoT creato con la propria sottoscrizione di Azure. Vedere [Creare un hub IoT usando il portale di Azure](iot-hub-create-through-portal.md) per istruzioni dettagliate.

* [Visual studio 2017 o Visual studio 2019](https://www.visualstudio.com/vs/) è installato.

## <a name="get-x509-ca-certificates"></a>Ottenere certificati della CA X.509

La sicurezza basata su certificati X.509 nell'hub IoT richiede prima di tutto una [catena di certificati X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), che include il certificato radice e i certificati intermedi fino al certificato foglia.

Per ottenere i certificati, è possibile scegliere uno dei modi seguenti:

* Acquistare i certificati X.509 da un'*autorità di certificazione (CA) radice*. Questo metodo è consigliato per gli ambienti di produzione.

* Creare certificati X. 509 personalizzati usando uno strumento di terze parti, ad esempio [openssl](https://www.openssl.org/). Questa tecnica è ottimale a scopo di test e sviluppo. Vedere [Gestione dei certificati CA di prova per esempi e certificazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) per informazioni sulla generazione di certificati CA di prova tramite PowerShell o Bash. Nella parte restante di questa esercitazione vengono usati i certificati CA di prova generati seguendo le istruzioni riportate in [Gestione dei certificati CA di prova per esempi e certificazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

* Generare un [certificato della CA intermedia X. 509](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) firmato da un certificato CA radice esistente e caricarlo nell'hub. Una volta caricato e verificato il certificato intermedio, come indicato di seguito, è possibile usarlo al posto di un certificato CA radice indicato di seguito. È possibile usare strumenti come OpenSSL ([openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) e [openssl ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) per generare e firmare un certificato CA intermedio.

> [!NOTE]
> Non caricare la radice di terze parti, se non è univoca per l'utente, perché ciò consentirebbe ad altri clienti della terza parte di connettere i propri dispositivi all'hub Internet.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrare i certificati della CA X.509 nell'hub IoT

Questi passaggi illustrano come aggiungere una nuova autorità di certificazione all'hub IoT tramite il portale. Quando si usa l'autenticazione della CA del certificato X. 509, assicurarsi di registrare il nuovo certificato prima della scadenza di quella esistente come parte della strategia di rollover del certificato.

> [!NOTE]
> Il numero massimo di certificati della CA X. 509 che è possibile registrare per un hub Internet è 25. Per altre informazioni, vedere [quote e limitazioni dell'hub Azure](iot-hub-devguide-quotas-throttling.md).

1. Nella portale di Azure passare all'hub Internet e selezionare **Impostazioni**  >  **certificati** per l'hub.

1. Selezionare **Aggiungi** per aggiungere un nuovo certificato.

1. In **nome certificato** immettere un nome visualizzato descrittivo e selezionare il file del certificato creato nella sezione precedente del computer.

1. Quando si riceve una notifica che indica che il certificato è stato caricato correttamente, selezionare **Salva**.

    ![Caricamento del certificato](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Il certificato viene visualizzato nell'elenco dei certificati con lo stato non **verificato**.

1. Selezionare il certificato appena aggiunto per visualizzare **i dettagli del certificato** e quindi selezionare **genera codice di verifica**.

   ![Verifica il certificato](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Copiare il **codice di verifica** negli Appunti. Viene usato per convalidare la proprietà del certificato.

1. Seguire il passaggio 3 nella sezione [relativa alla gestione dei certificati della CA di test per esempi ed esercitazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).  Questo processo firma il codice di verifica con la chiave privata associata al certificato della CA X. 509, che genera una firma. Sono disponibili vari strumenti per eseguire questo processo di firma, ad esempio OpenSSL. Questo processo è noto come [prova di possesso](https://tools.ietf.org/html/rfc5280#section-3.1).

1. In **Dettagli certificato**, in **file di certificato di verifica con estensione PEM o CER**, trovare e aprire il file della firma. Quindi selezionare **Verifica**.

   Lo stato del certificato diventa **verificato**. Selezionare **Aggiorna** se il certificato non viene aggiornato automaticamente.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Creare un dispositivo X.509 per l'hub IoT

1. Nella portale di Azure passare all'hub Internet e quindi selezionare **esploratori**  >  **dispositivi**.

1. Selezionare **nuovo** per aggiungere un nuovo dispositivo.

1. In **ID dispositivo** immettere un nome visualizzato descrittivo. Per **tipo di autenticazione** scegliere **CA X. 509 firmata** e quindi selezionare **Salva**.

   ![Creare il dispositivo X.509 nel portale](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Autenticare il dispositivo X.509 con i certificati X.509

Per autenticare il dispositivo X.509, è prima di tutto necessario firmare il dispositivo con il certificato della CA. La firma dei dispositivi foglia viene in genere eseguita nell'impianto di produzione, con strumenti di produzione abilitati di conseguenza. Quando il dispositivo passa da un produttore a un altro, l'azione di firma di ogni produttore viene acquisita come un certificato intermedio nella catena. Il risultato è una catena di certificati del certificato della CA per il certificato foglia del dispositivo. Passaggio 4 [Gestione dei certificati CA di prova per esempi e certificazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) genera un certificato del dispositivo.

In seguito verrà illustrato come creare un'applicazione C# per simulare il dispositivo X.509 registrato per l'hub IoT. Verranno inviati valori di temperatura e umidità dal dispositivo simulato all'hub. In questa esercitazione verrà creata solo l'applicazione del dispositivo. Rimarrà come esercizio per i lettori la creazione dell'applicazione di servizio dell'hub IoT per inviare risposta agli eventi inviati da questo dispositivo simulato. L'applicazione C# presuppone che siano stati seguiti i passaggi descritti in [Gestione dei certificati CA di prova per esempi e certificazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. Aprire Visual Studio, selezionare **Crea nuovo progetto**, quindi scegliere il modello di progetto **App Console (.NET Framework)** . Selezionare **Avanti**.

1. In **Configura il nuovo progetto** assegnare al progetto il nome *SimulateX509Device* e quindi selezionare **Crea**.

   ![Creare il progetto di dispositivo X.509 in Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **SimulateX509Device** e quindi scegliere **Gestisci pacchetti NuGet**.

1. In **Gestione pacchetti NuGet** selezionare **Sfoglia** e cercare e scegliere **Microsoft. Azure. Devices. client**. Selezionare **Installa**.

   ![Aggiungere il pacchetto NuGet SDK per dispositivi in Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Questo passaggio scarica, installa e aggiunge un riferimento al pacchetto NuGet Azure IoT SDK per dispositivi e alle relative dipendenze.

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Aggiungere i campi seguenti alla classe **Program** :

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Usare il nome descrittivo del dispositivo usato nella sezione precedente al posto di _<your_device_id>_.

1. Aggiungere la funzione seguente per creare numeri casuali per temperatura e umidità e inviare tali valori all'hub:

    ```csharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. Aggiungere infine le righe di codice seguenti alla funzione **Main** , sostituendo i segnaposto _Device-ID_, _il nome---_----------------------------------------- 

    ```csharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   Questo codice si connette all'hub IoT creando la stringa di connessione per il dispositivo X.509. Una volta stabilita la connessione, il codice invia quindi gli eventi di temperatura e umidità all'hub e attende la risposta.

1. Eseguire l'app. Poiché questa applicazione accede a un file con *estensione pfx* , potrebbe essere necessario eseguire questa app come amministratore.

   1. Compilare la soluzione di Visual Studio.

   1. Aprire una nuova finestra del prompt dei comandi usando **Esegui come amministratore**.  

   1. Passare alla cartella che contiene la soluzione, quindi passare al percorso *bin/debug* nella cartella della soluzione.

   1. Eseguire l'applicazione **SimulateX509Device.exe** dal prompt dei comandi.

   Il dispositivo dovrebbe connettersi correttamente all'hub e inviare eventi.

   ![Eseguire l'app per dispositivi](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla protezione della soluzione IoT, vedere:

* [Procedure consigliate per la sicurezza](../iot-fundamentals/iot-security-best-practices.md)

* [Architettura della sicurezza IoT](../iot-fundamentals/iot-security-architecture.md)

* [Proteggere la distribuzione di IoT](../iot-fundamentals/iot-security-deployment.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/quickstart-linux.md)