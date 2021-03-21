---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 30ea75a2df63fa935314fc103fe1e7e092f655b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104613020"
---
Questa guida di avvio rapido illustra come creare un'applicazione di dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare i dati di telemetria inviati. L'applicazione di esempio è scritta in C# ed è inclusa negli esempi di Azure IoT per C#. Un generatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Per completare questa guida di avvio rapido in Windows, è necessario che nel computer di sviluppo sia installato il software seguente:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>Scaricare il codice

In questa guida di avvio rapido viene preparato un ambiente di sviluppo che è possibile usare per clonare e compilare il repository degli esempi di Azure IoT per C#.

Aprire un prompt dei comandi in una cartella di propria scelta. Eseguire il comando seguente per clonare il repository GitHub degli [esempi di Microsoft Azure IoT per C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) in questo percorso:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>Compilare il codice

È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

1. Aprire il file di progetto *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* in Visual Studio 2019.

1. In Visual Studio passare a **Progetto > Thermostat Properties (Proprietà termostato) > Debug**. Aggiungere quindi al progetto le variabili di ambiente seguenti:

    | Nome | Valore |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Valore di cui si è preso nota quando è stato completato il passaggio [Configurare l'ambiente](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Valore di cui si è preso nota quando è stato completato il passaggio [Configurare l'ambiente](../articles/iot-pnp/set-up-environment.md) |

È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

## <a name="run-the-device-sample"></a>Eseguire l'esempio del dispositivo

Per verificare l'esecuzione del codice in Visual Studio in Windows, aggiungere un punto di interruzione alla funzione `main` nel file program.cs.

Il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà e ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un semplice dispositivo termostato Plug and Play IoT. Il modello implementato da questo esempio non usa [componenti](../articles/iot-pnp/concepts-modeling-guide.md) di Plug and Play IoT. Il [file di modello DTDL (Digital Twins Definition Language) per il dispositivo termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definisce i dati di telemetria, le proprietà e i comandi implementati dal dispositivo.

Il codice del dispositivo si connette all'hub IoT usando il metodo `CreateFromConnectionString` standard. Il dispositivo invia l'ID modello del modello DTDL implementato nella richiesta di connessione. Un dispositivo che invia un ID modello è un dispositivo Plug and Play IoT:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

L'ID modello viene archiviato nel codice come mostrato nel frammento di codice seguente:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Il codice che aggiorna le proprietà, gestisce i comandi e invia i dati di telemetria è identico al codice per un dispositivo che non usa le convenzioni di Plug and Play IoT.

L'esempio usa una libreria JSON per analizzare oggetti JSON nei payload inviati dall'hub IoT:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```
