---
title: Sviluppo di hub Internet degli Azure per i dispositivi vincolati con l'SDK C dell'hub
description: 'Guida per sviluppatori: materiale sussidiario sulle modalità di sviluppo tramite Azure IoT SDK per i dispositivi vincolati.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 9010ff582f05e81e17e280e20f180ceccf0e746f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733195"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Sviluppare per dispositivi vincolati tramite Azure IoT C SDK

L'SDK C dell'hub IoT di Azure è scritto in ANSI C (C99), che lo rende particolarmente adatto per il funzionamento su una vasta gamma di piattaforme con footprint di memoria e disco ridotto. La quantità di RAM consigliata è almeno 64 KB, ma il footprint di memoria esatto varia in base al protocollo usato, al numero di connessioni aperte e alla piattaforma di destinazione.
> [!NOTE]
> * Azure le informazioni sul consumo di risorse di Azure sono regolarmente pubblicate per semplificare lo sviluppo.  Visitare il [repository GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) ed esaminare il benchmark più recente.
>

L'SDK C è disponibile in forma di pacchetto da apt-get, NuGet e MBED. Per fare riferimento ai dispositivi vincolati, è possibile compilare l'SDK in locale per la piattaforma di destinazione. Questa documentazione illustra come rimuovere alcune funzionalità per ridurre il footprint dell'SDK C usando [cmake](https://cmake.org/). Inoltre, questa documentazione presenta i modelli di programmazione consigliati per l'uso con i dispositivi vincolati.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Compilazione dell'SDK C per i dispositivi vincolati

Compilare l'SDK C per i dispositivi vincolati.

### <a name="prerequisites"></a>Prerequisiti

Seguire questa [guida all'installazione dell'SDK C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) per preparare l'ambiente di sviluppo per la compilazione dell'SDK C. Prima di arrivare al passaggio per la compilazione con cmake, è possibile richiamare flag cmake per rimuovere le funzionalità non usate.

### <a name="remove-additional-protocol-libraries"></a>Rimuovere le librerie di protocollo aggiuntive

L'SDK C supporta attualmente cinque protocolli: MQTT, MQTT su WebSocket, AMQPs, AMQP su WebSocket e HTTPS. La maggior parte degli scenari richiede uno o due protocolli in esecuzione in un client, pertanto è possibile rimuovere dall'SDK la libreria di protocollo non usata. Altre informazioni sulla scelta del protocollo di comunicazione appropriato per uno specifico scenario sono disponibili in [Scegliere un protocollo di comunicazione](iot-hub-devguide-protocols.md). Ad esempio, MQTT è un protocollo leggero che spesso è più indicato per i dispositivi vincolati.

È possibile rimuovere le librerie AMQP e HTTP usando il comando cmake seguente:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Rimuovere la funzionalità di registrazione dall'SDK

L'SDK C assicura una registrazione completa per supportare il debug. È possibile rimuovere la funzionalità di registrazione per i dispositivi di produzione usando il comando cmake seguente:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Rimuovere la funzionalità di caricamento nel BLOB

È possibile caricare file di grandi dimensioni in Archiviazione di Azure tramite la funzionalità predefinita dell'SDK. L'hub IoT di Azure funge da dispatcher per un account di Archiviazione di Azure associato. È possibile usare questa funzionalità per inviare file multimediali, batch di dati di telemetria di grandi dimensioni e log. È possibile ottenere altre informazioni in [Caricare file con l'hub IoT](iot-hub-devguide-file-upload.md). Se l'applicazione non necessita di questa funzionalità, è possibile rimuoverla con il comando cmake seguente:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Esecuzione di strip nell'ambiente Linux

Se i file binari sono eseguiti in un sistema Linux, è possibile usare il [comando strip](https://en.wikipedia.org/wiki/Strip_(Unix)) per ridurre le dimensioni dell'applicazione finale dopo la compilazione.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Modelli di programmazione per i dispositivi vincolati

Di seguito verranno illustrati i modelli di programmazione per i dispositivi vincolati.

### <a name="avoid-using-the-serializer"></a>Evitare l'uso del serializzatore

L'SDK C dispone di un [serializzatore SDK C](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer) facoltativo che consente di usare tabelle di mapping dichiarative per definire i metodi e le proprietà dei dispositivi gemelli. Il serializzatore è progettato per semplificare lo sviluppo, ma aggiunge un sovraccarico, non indicato per i dispositivi vincolati. In questo caso, provare a usare le API client primitive e analizzare JSON tramite un parser leggero come [parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-_ll_"></a>Usare il livello inferiore (_LL_)

L'SDK C supporta due modelli di programmazione. Un set dispone di API con un infisso _LL_, che indica il livello inferiore. Questo set di API è più leggero e non avvia thread di lavoro, il che significa che l'utente deve controllare manualmente la pianificazione. Ad esempio, per il client del dispositivo, le API _LL_ sono disponibili in questo [file di intestazione](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Un altro set di API senza l'indice _LL_ viene definito livello dedicato, in cui un thread di lavoro viene avviato automaticamente. Ad esempio, le API del livello dedicato per il client del dispositivo sono disponibili in questo [file di intestazione del client del dispositivo IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). Per i dispositivi vincolati in cui ogni thread aggiuntivo può richiedere una percentuale notevole delle risorse di sistema, prendere in considerazione l'uso delle API _LL_.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'architettura dell'SDK C per l'hub IoT di Azure:
-    [Codice sorgente dell'SDK C per l'hub IoT di Azure](https://github.com/Azure/azure-iot-sdk-c/)
-    [Introduzione ad Azure IoT SDK per dispositivi per C](iot-hub-device-sdk-c-intro.md)
