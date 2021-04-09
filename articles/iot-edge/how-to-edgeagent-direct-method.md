---
title: Metodi diretti edgeAgent predefiniti-Azure IoT Edge
description: Monitorare e gestire una distribuzione di IoT Edge usando metodi diretti predefiniti nel modulo di runtime di IoT Edge Agent
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 52cd7817594c5c2a1d4e3a4ca9c56891df594cd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201101"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Comunicare con edgeAgent usando metodi diretti predefiniti

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Monitorare e gestire le distribuzioni di IoT Edge usando i metodi diretti inclusi nel modulo IoT Edge Agent. I metodi diretti vengono implementati nel dispositivo e quindi possono essere richiamati dal cloud. L'agente IoT Edge include metodi diretti che consentono di monitorare e gestire i dispositivi di IoT Edge in modalità remota.

Per altre informazioni sui metodi diretti, su come usarli e su come implementarli nei moduli personali, vedere [comprendere e richiamare metodi diretti dall'hub](../iot-hub/iot-hub-devguide-direct-methods.md)Internet.

I nomi di questi metodi diretti vengono gestiti senza distinzione tra maiuscole e minuscole.

## <a name="ping"></a>Ping

Il metodo **ping** è utile per verificare se IOT Edge è in esecuzione in un dispositivo o se il dispositivo ha una connessione aperta all'hub Internet. Utilizzare questo metodo diretto per effettuare il ping dell'agente IoT Edge e ottenerne lo stato. Un Ping riuscito restituisce un payload vuoto e **"status": 200**.

Ad esempio:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Nel portale di Azure richiamare il metodo con il nome del metodo `ping` e un payload JSON vuoto `{}` .

![Richiama il metodo diretto ' ping ' in portale di Azure](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Riavvia modulo

Il metodo **RestartModule** consente la gestione remota dei moduli in esecuzione su un dispositivo IOT Edge. Se un modulo segnala uno stato di errore o un altro comportamento non integro, è possibile attivare l'agente di IoT Edge per riavviarlo. Un comando di riavvio riuscito restituisce un payload vuoto e **"status": 200**.

Il metodo RestartModule è disponibile nella versione IoT Edge 1.0.9 e versioni successive. 

È possibile usare il metodo diretto RestartModule su qualsiasi modulo eseguito in un dispositivo IoT Edge, incluso il modulo edgeAgent stesso. Tuttavia, se si usa questo metodo diretto per arrestare il edgeAgent, non si riceverà un risultato positivo perché la connessione viene interrotta durante il riavvio del modulo.

Ad esempio:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

Nel portale di Azure richiamare il metodo con il nome del metodo `RestartModule` e il payload JSON seguente:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Richiama il metodo diretto ' RestartModule ' in portale di Azure](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="diagnostic-direct-methods"></a>Metodi diretti di diagnostica

* [GetModuleLogs](how-to-retrieve-iot-edge-logs.md#retrieve-module-logs): recupera i registri dei moduli inline nella risposta del metodo diretto.
* [UploadModuleLogs](how-to-retrieve-iot-edge-logs.md#upload-module-logs): recuperare i registri dei moduli e caricarli nell'archivio BLOB di Azure.
* [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics): recuperare i log dei moduli usando un bundle di supporto e caricare un file zip nell'archivio BLOB di Azure.
* [GetTaskStatus](how-to-retrieve-iot-edge-logs.md#get-upload-request-status): controllare lo stato di una richiesta di caricamento dei log o del bundle di supporto.

Questi metodi di diagnostica diretta sono disponibili al momento della versione 1.0.10.

## <a name="next-steps"></a>Passaggi successivi

[Proprietà dei moduli gemelli "agente di IoT Edge" e "hub di IoT Edge"](module-edgeagent-edgehub.md)
