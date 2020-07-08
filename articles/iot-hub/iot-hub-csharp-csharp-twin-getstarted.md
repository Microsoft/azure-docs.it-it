---
title: Introduzione ai dispositivi gemelli dell'hub IoT di Azure (.NET/.NET) | Microsoft Docs
description: Come usare i dispositivi gemelli dell'hub IoT di Azure per aggiungere tag e quindi usare una query dell'hub IoT. Usare Azure IoT SDK per dispositivi per Node.js per implementare l'app per dispositivo simulato e Azure IoT SDK per servizi per .NET per implementare un'app di servizio che aggiunge i tag ed esegue la query dell'hub IoT.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 620e0213733d278a28ec1bcad4b031f5764ccda9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733124"
---
# <a name="get-started-with-device-twins-net"></a>Introduzione ai dispositivi gemelli (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

In questa esercitazione vengono create le seguenti app console .NET:

* **CreateDeviceIdentity**. Questa app crea un'identità del dispositivo e la chiave di sicurezza associata per connettere l'app per dispositivo simulato.

* **AddTagsAndQuery**. Questa app back-end aggiunge tag ed esegue query sui dispositivi gemelli.

* **ReportConnectivity**. Questa app per dispositivi simula un dispositivo che si connette all'hub IoT con l'identità del dispositivo creata prima e segnala la condizione della connettività.

> [!NOTE]
> L'articolo [Azure IoT SDK](iot-hub-devguide-sdks.md) riporta informazioni sui componenti Azure IoT SDK che consentono di compilare le app back-end e per dispositivi.
>

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio.

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Creare l'app di servizio

In questa sezione si crea un'app console .NET usando C# e si aggiungono i metadati della posizione al dispositivo gemello associato a **myDeviceId**. Viene quindi eseguita una query nei dispositivi gemelli archiviati nell'hub IoT selezionando i dispositivi situati negli Stati Uniti e quindi quelli che segnalano una rete cellulare.

1. In Visual Studio selezionare **Crea un nuovo progetto**. In **Crea nuovo progetto**, selezionare **App console (.NET Framework)** , quindi selezionare **Avanti**.

1. In **Configura il nuovo progetto** assegnare al progetto il nome **AddTagsAndQuery**.

    ![Configurare il progetto AddTagsAndQuery](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AddTagsAndQuery** e quindi selezionare **Gestisci pacchetti NuGet**.

1. Selezionare **Sfoglia** e cercare e selezionare **Microsoft. Azure. Devices**. Selezionare **Installa**.

    ![Finestra Gestione pacchetti NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Questa procedura scarica, installa e aggiunge un riferimento al pacchetto NuGet [Azure IoT SDK per servizi](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e alle relative dipendenze.

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire `{iot hub connection string}` con la stringa di connessione dell'hub IoT copiata in [Ottenere la stringa di connessione dell'hub IoT](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Aggiungere il metodo seguente alla classe **Program** :

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    L'oggetto **RegistryManager** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal servizio. Il codice precedente inizializza prima di tutto l'oggetto **registryManager**, quindi recupera il dispositivo gemello per **myDeviceId** e infine ne aggiorna i tag con le informazioni sulla posizione desiderate.

    Dopo l'aggiornamento, il codice precedente esegue due query: la prima seleziona solo i dispositivi gemelli tra quelli situati nello stabilimento **Redmond43** e la seconda affina la query per selezionare solo i dispositivi che sono connessi anche tramite la rete cellulare.

    Il codice precedente, quando crea l'oggetto **query**, specifica un numero massimo di documenti restituiti. L'oggetto **query** contiene una proprietà booleana **HasMoreResults** che è possibile usare per richiamare i metodi **GetNextAsTwinAsync** più volte per recuperare tutti i risultati. Per i risultati che non sono dispositivi gemelli, ad esempio i risultati delle query di aggregazione, è disponibile un metodo chiamato **GetNextAsJson**.

1. Aggiungere infine le righe seguenti al metodo **Main** :

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Eseguire l'applicazione facendo clic con il pulsante destro del mouse sul progetto **AddTagsAndQuery** e selezionando **Debug**, seguito da **Avvia nuova istanza**. Nei risultati si noterà un dispositivo per la query che cerca tutti i dispositivi situati in **Redmond43** e nessuno per la query che limita i risultati ai dispositivi che usano una rete cellulare.

    ![Risultati della query nella finestra](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

Nella sezione successiva si crea un'app per dispositivo che segnala le informazioni sulla connettività e modifica il risultato della query nella sezione precedente.

## <a name="create-the-device-app"></a>Creare l'app per dispositivo

In questa sezione si crea un'app console .NET che si connette all'hub come **myDeviceId** e quindi aggiorna le proprietà segnalate per poter contenere le informazioni relative alla connessione usando una rete cellulare.

1. In Visual Studio selezionare **File** > **Nuovo** > **Progetto**. In **Crea nuovo progetto**, scegliere **App console (.NET Framework)** , quindi selezionare **Avanti**.

1. In **Configura il nuovo progetto** assegnare al progetto il nome **ReportConnectivity**. Per **Soluzione** scegliere **Aggiungi alla soluzione** e selezionare **Crea**.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ReportConnectivity**, quindi selezionare **Gestisci pacchetti NuGet**.

1. Selezionare **Sfoglia** e cercare e scegliere **Microsoft.Azure.Devices.Client**. Selezionare **Installa**.

   Questo passaggio scarica, installa e aggiunge un riferimento al pacchetto NuGet [Azure IoT SDK per dispositivi](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e alle relative dipendenze.

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire `{device connection string}` con la stringa di connessione del dispositivo indicata nella sezione [Registrare un nuovo dispositivo nell’hub IoT](#register-a-new-device-in-the-iot-hub).

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Aggiungere il metodo seguente alla classe **Program** :

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    L'oggetto **Client** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal dispositivo. Il codice riportato sopra inizializza l'oggetto **Client** e quindi recupera il dispositivo gemello per **myDeviceId**.

1. Aggiungere il metodo seguente alla classe **Program** :

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   Il codice sopra riportato aggiorna le proprietà segnalate di **myDeviceId** con le informazioni di connettività.

1. Aggiungere infine le righe seguenti al metodo **Main** :

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Imposta progetti di avvio**.

1. In **Proprietà comuni** > **Progetto di avvio**, selezionare **Progetti di avvio multipli**. Per **ReportConnectivity**, selezionare **Avvia** come **Azione**. Selezionare **OK** per salvare le modifiche.  

1. Eseguire l'app facendo clic con il pulsante destro del mouse sul progetto **ReportConnectivity** e selezionando **Debug**, seguito da **Avvia nuova istanza**. Si dovrebbe notare l'app che recupera le informazioni sui dispositivi gemelli e quindi l’invio della connettività come ***proprietà segnalata***.

    ![Eseguire l'app per dispositivi per segnalare la connettività](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Dopo che il dispositivo ha segnalato le informazioni sulla connettività, verrà visualizzato in entrambe le query.

1. Fare clic con il pulsante destro del mouse sul progetto **AddTagsAndQuery** e selezionare **Debug** > **Avvia nuova istanza** per eseguire di nuovo le query. Questa volta **myDeviceId** verrà visualizzato nei risultati di entrambe le query.

    ![Connettività del dispositivo segnalata correttamente](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. Sono stati aggiunti i metadati del dispositivo come tag da un'app back-end ed è stata scritta un'app per dispositivo simulato per segnalare le informazioni sulla connettività del dispositivo nel dispositivo gemello. Si è anche appreso come effettuare una query di queste informazioni usando il linguaggio di query simile a SQL dell'hub IoT.

Per altre informazioni, vedere le risorse riportate di seguito:

* Per scoprire come inviare dati di telemetria dai dispositivi, vedere l'esercitazione [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md).

* Per scoprire come configurare i dispositivi usando le proprietà desiderate del dispositivo gemello, vedere l'esercitazione [Usare le proprietà desiderate per configurare i dispositivi](tutorial-device-twins.md).

* Per scoprire come controllare i dispositivi in modo interattivo, ad esempio per attivare un ventilatore da un'app controllata dall'utente, vedere l'esercitazione [Usare i metodi diretti](quickstart-control-device-dotnet.md).
