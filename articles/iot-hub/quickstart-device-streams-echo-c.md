---
title: "Guida introduttiva: comunicare con l'app per dispositivi in C con i flussi di dispositivi dell'hub Azure."
description: In questo argomento di avvio rapido viene eseguita un'applicazione sul lato dispositivo in C che comunica con un dispositivo IoT tramite un flusso del dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: fd179bf17f481ae84f768d619c6ddc0b9afab280
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202553"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Guida introduttiva: Comunicare con un'applicazione del dispositivo in C tramite i flussi dispositivo dell'hub IoT (anteprima)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

L'hub IoT di Azure supporta attualmente i flussi del dispositivo come [funzionalità in anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I [flussi dispositivo dell'hub IoT](iot-hub-device-streams-overview.md) consentono alle applicazioni del servizio e del dispositivo di comunicare in modo sicuro e di facile integrazione con i firewall. Durante l'anteprima pubblica, l'SDK C supporta solo i flussi del dispositivo sul lato dispositivo. Di conseguenza, questo argomento di avvio rapido include le istruzioni per eseguire solo l'applicazione sul lato dispositivo. Per eseguire un'applicazione sul lato servizio corrispondente, vedere questi articoli:

* [Comunicare con le applicazioni del dispositivo in C# tramite i flussi dispositivo dell'hub IoT](./quickstart-device-streams-echo-csharp.md)

* [Comunicare con app del dispositivo in Node.js tramite i flussi del dispositivo dell'hub IoT](./quickstart-device-streams-echo-nodejs.md)

L'applicazione C sul lato dispositivo in questa guida introduttiva presenta le funzionalità seguenti:

* Stabilire un flusso dispositivo in un dispositivo IoT.

* Ricevere i dati inviati dall'applicazione sul lato servizio e rinviarli.

Il codice illustrerà il processo di avvio di un flusso del dispositivo, oltre a come usarlo per inviare e ricevere dati.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

È necessario soddisfare i prerequisiti seguenti:

* Installare [Visual Studio 2019](https://www.visualstudio.com/vs/) con il carico di lavoro **Sviluppo di applicazioni desktop con C++** abilitato.

* Installare la versione più recente di [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

L'anteprima dei flussi del dispositivo è attualmente supportata solo per gli hub IoT creati nelle aree seguenti:

  * Stati Uniti centrali
  * Stati Uniti centrali EUAP
  * Europa settentrionale
  * Asia sud-orientale

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

Per questo argomento di avvio rapido si userà [Azure IoT SDK per dispositivi per C](iot-hub-device-sdk-c-intro.md). Si preparerà un ambiente di sviluppo usato per clonare e creare [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) da GitHub. L'SDK in GitHub include il codice di esempio usato in questo argomento.

   > [!NOTE]
   > Prima di iniziare questa procedura, assicurarsi che Visual Studio sia installato con il carico di lavoro **Sviluppo di applicazioni desktop con C++**.

1. Installare il [sistema di compilazione CMake](https://cmake.org/download/) come descritto nella pagina di download.

1. Aprire un prompt dei comandi o la shell Git Bash. Eseguire i comandi seguenti per clonare il repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Questa operazione dovrebbe richiedere qualche minuto.

1. Creare una sottodirectory *cmake* nella directory radice del repository Git e passare a tale cartella. Eseguire i comandi seguenti dalla directory *azure-iot-sdk-c*:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti dalla directory *cmake* per creare una versione dell'SDK specifica per la piattaforma client di sviluppo.

   * Linux:

      ```bash
      cmake ..
      make -j
      ```

   * In Windows aprire un [prompt dei comandi per gli sviluppatori per Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). Eseguire il comando appropriato per la versione in uso di Visual Studio. In questo argomento di avvio rapido si usa Visual Studio 2019. Questi comandi creano una soluzione di Visual Studio per il dispositivo simulato nella directory *cmake*.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

Per poter connettere un dispositivo, è necessario registrarlo con l'hub IoT. In questa sezione si usa Azure Cloud Shell con l'[estensione IoT](/cli/azure/ext/azure-iot/iot) per registrare un dispositivo simulato.

1. Per creare l'identità del dispositivo, eseguire il comando seguente in Cloud Shell:

   > [!NOTE]
   > * Sostituire il segnaposto *YourIoTHubName* con il nome scelto per l'hub IoT.
   > * Per il nome del dispositivo che si sta registrando, è consigliabile usare *MyDevice*, come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usarlo nell'intero articolo e aggiornarlo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Per ottenere la *stringa di connessione* per il dispositivo appena registrato, eseguire il comando seguente in Cloud Shell:

   > [!NOTE]
   > Sostituire il segnaposto *YourIoTHubName* con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Prendere nota della stringa di connessione del dispositivo restituita per usarla in seguito in questo argomento di avvio rapido. Sarà simile a quanto indicato nell'esempio seguente:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Stabilire la comunicazione tra il dispositivo e il servizio tramite i flussi del dispositivo

In questa sezione verranno eseguite sia l'applicazione sul lato dispositivo sia l'applicazione sul lato servizio con le relative comunicazioni.

### <a name="run-the-device-side-application"></a>Eseguire l'applicazione sul lato dispositivo

Per eseguire l'applicazione sul lato dispositivo, procedere come segue:

1. Fornire le credenziali del dispositivo modificando il file di origine **iothub_client_c2d_streaming_sample.c** nella cartella `iothub_client/samples/iothub_client_c2d_streaming_sample` e aggiungendo la stringa di connessione del dispositivo.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Compilare il codice con i comandi seguenti:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Eseguire il programma compilato:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Eseguire l'applicazione sul lato servizio

Come indicato in precedenza, l'SDK dell'hub IoT per C supporta solo i flussi del dispositivo sul lato dispositivo. Per compilare ed eseguire l'applicazione sul lato servizio associata, seguire le istruzioni disponibili in uno degli argomenti di avvio rapido seguenti:

* [Comunicare con un'applicazione del dispositivo in C# tramite i flussi dispositivo dell'hub IoT](./quickstart-device-streams-echo-csharp.md)

* [Comunicare con un'app del dispositivo in Node.js tramite i flussi del dispositivo dell'hub IoT](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato configurato un hub IoT, è stato registrato un dispositivo ed è stato stabilito un flusso del dispositivo tra un'applicazione C sul dispositivo e un'altra sul lato servizio, quindi tale flusso è stato usato per inviare e ricevere dati tra le applicazioni.

Per altre informazioni sui flussi del dispositivo, vedere:

> [!div class="nextstepaction"]
> [Panoramica dei flussi dispositivo](./iot-hub-device-streams-overview.md)
