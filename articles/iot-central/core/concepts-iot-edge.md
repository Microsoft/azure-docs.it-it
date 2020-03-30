---
title: Azure IoT Edge e Azure IoT Central Documenti Microsoft
description: Informazioni su come usare Azure IoT Edge con un'applicazione IoT Central.Understand how to use Azure IoT Edge with an IoT Central application.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 69660152458de26e9dbcbf1f50db6ce6824351d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027069"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Connettere i dispositivi Azure IoT Edge a un'applicazione Azure IoT CentralConnect Azure IoT Edge devices to an Azure IoT Central application

IoT Edge è costituito da tra componenti:

* I **moduli di IoT Edge** sono contenitori che eseguono servizi di Azure, servizi partner o il codice dell'utente. Vengono distribuiti nei dispositivi IoT Edge ed eseguiti in locale in tali dispositivi.
* Il **runtime IoT Edge** viene eseguito in ogni dispositivo IoT Edge e gestisce i moduli distribuiti in ogni dispositivo.
* Un'**interfaccia basata sul cloud** consente di monitorare e gestire in remoto i dispositivi IoT Edge. IoT Central è l'interfaccia cloud.

Un dispositivo **Azure IoT Edge** può essere un dispositivo gateway con dispositivi downstream che si connettono al dispositivo IoT Edge. In questo articolo vengono necessarie ulteriori informazioni sui modelli di connettività dei dispositivi downstream.

Un **modello di dispositivo** definisce le funzionalità del dispositivo e dei moduli IoT Edge. Le funzionalità includono i dati di telemetria inviati dal modulo, le proprietà del modulo e i comandi a cui un modulo risponde.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relazioni dei dispositivi downstream con il gateway e i moduli

I dispositivi downstream possono connettersi al dispositivo gateway IoT Edge tramite il modulo `$edgeHub`. Questo dispositivo IoT Edge diventa un gateway trasparente in questo scenario.

![Diagramma del gateway trasparente](./media/concepts-iot-edge/gateway-transparent.png)

I dispositivi downstream possono anche connettersi al dispositivo gateway IoT Edge tramite un modulo personalizzato. Nello scenario seguente i dispositivi downstream si connettono tramite un modulo personalizzato Modbus.

![Diagramma della connessione del modulo personalizzato](./media/concepts-iot-edge/gateway-module.png)

Il diagramma seguente mostra la connessione a un dispositivo gateway IoT Edge tramite entrambi i tipi di moduli (personalizzato e `$edgeHub`).  

![Diagramma della connessione tramite entrambi i moduli di connessione](./media/concepts-iot-edge/gateway-module-transparent.png)

Infine, i dispositivi downstream possono connettersi al dispositivo gateway IoT Edge tramite più moduli personalizzati. Il diagramma seguente mostra i dispositivi downstream che si connettono tramite un modulo personalizzato Modbus, un modulo personalizzato BLE (Bluetooth a basso consumo) e il modulo `$edgeHub`. 

![Diagramma della connessione tramite più moduli personalizzati](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Manifesti di distribuzione e modelli di dispositivo

IoT Edge consente di distribuire e gestire la logica di business sotto forma di moduli. I moduli di IoT Edge sono l'unità di calcolo più piccola gestita da IoT Edge e possono contenere i servizi di Azure, ad esempio Analisi di flusso di Azure, o il codice specifico della soluzione. Per comprendere come vengono sviluppati, distribuiti e gestiti i moduli, vedere [Moduli di IoT Edge](../../iot-edge/iot-edge-modules.md).

A livello generale, un manifesto della distribuzione è un elenco di moduli gemelli configurati con le relative proprietà desiderate. Un manifesto della distribuzione indica a un dispositivo IoT Edge (o a un gruppo di dispositivi) quali moduli installare e come configurarli. I manifesti della distribuzione includono le proprietà desiderate per ogni modulo gemello. I dispositivi IoT Edge riportano le proprietà segnalate per ogni modulo.

Usare Visual Studio Code per creare un manifesto della distribuzione. Per altre informazioni, vedere [Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

In Azure IoT Central è possibile importare un manifesto di distribuzione per creare un modello di dispositivo. Il diagramma di flusso seguente mostra il ciclo di vita del manifesto della distribuzione in IoT Central.

![Diagramma di flusso del ciclo di vita del manifesto della distribuzione](./media/concepts-iot-edge/dmflow.png)

IoT Plug and Play (anteprima) modella un dispositivo IoT Edge come segue:

* Ogni modello di dispositivo IoT Edge ha un modello di funzionalità di dispositivo.
* Per ogni modulo personalizzato elencato nel manifesto della distribuzione viene generato un modello di funzionalità del modulo.
* Viene stabilita una relazione tra ogni modello di funzionalità del modulo e un modello di funzionalità di dispositivo.
* Il modello di funzionalità del modulo implementa le interfacce del modulo.
* Ogni interfaccia del modulo contiene i dati di telemetria, le proprietà e i comandi.

![Diagramma della creazione del modello IoT Edge](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>Dispositivi gateway IoT Edge

Se il dispositivo IoT Edge è stato selezionato per fungere da dispositivo gateway, è possibile aggiungere relazioni downstream ai modelli di funzionalità di dispositivo per i dispositivi da connettere al dispositivo gateway.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce i modelli di applicazione IoT Central, iniziare [a creare un'applicazione centrale IoT](quick-deploy-iot-central.md).