---
title: Usare Azure IoT Tools per VSCode per la gestione della messaggistica dell'hub IT
description: Informazioni su come usare Azure IoT Tools per Visual Studio Code per monitorare dispositivi per i messaggi al cloud e inviare messaggi al dispositivo nell'hub IoT di Azure cloud.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 1c4a840233e576c528e9c58d57eca0b3d524bf4d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566929"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Usare Azure IoT Tools per Visual Studio Code per inviare e ricevere messaggi tra il dispositivo e l'hub IoT

![Diagramma end-to-end](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

Questo articolo illustra come usare Azure IoT Tools per Visual Studio Code per monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo. I messaggi da dispositivo a cloud possono includere dati di sensori raccolti dal dispositivo e inviati all'hub IoT. I messaggi da cloud a dispositivo possono includere comandi inviati dall'hub IoT al dispositivo per far lampeggiare un LED connesso a quest'ultimo.

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) è un'utile estensione di Visual Studio Code che semplifica la gestione dell'hub IoT e lo sviluppo di applicazioni IoT. Questo articolo descrive come usare Azure IoT Tools per Visual Studio Code e inviare e ricevere messaggi tra il dispositivo e l'hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure attiva.

* Un hub IoT di Azure nella sottoscrizione.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Azure IoT Tools per VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) copiare e incollare questo URL in una finestra del browser: `vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

1. Nella visualizzazione **Explorer** del codice di Visual Studio, espandere la sezione **dispositivi Azure IoT Hub** nell'angolo in basso a sinistra.

2. Fare clic su **Seleziona hub IoT** nel menu di scelta rapida.

3. Verrà visualizzata una finestra popup nell'angolo in basso a destra che consentirà di accedere ad Azure per la prima volta.

4. Dopo l'accesso, verrà visualizzato l'elenco di sottoscrizioni di Azure; selezionare quindi la sottoscrizione di Azure e l'hub IoT.

5. Verrà visualizzato in pochi secondi l'elenco dei dispositivi nella scheda **Dispositivi di Azure IoT hub di Azure**.

   > [!Note]
   > È anche possibile completare la configurazione scegliendo **Set IoT Hub Connection String** (Configura la stringa di connessione dell'hub IoT). Immettere la stringa di connessione del criterio **iothubowner** per l'hub IoT a cui si connette il dispositivo IoT nella finestra popup.

## <a name="monitor-device-to-cloud-messages"></a>Monitorare i messaggi da dispositivo a cloud

Per monitorare i messaggi inviati dal dispositivo all'hub IoT, seguire questi passaggi:

1. Fare clic con il pulsante destro del mouse sul dispositivo **e scegliere Start Monitoring Built-in Event Endpoint (Avvia monitoraggio endpoint eventi predefinito).**

2. I messaggi monitorati verranno visualizzati in **OUTPUT**  >  **hub IoT di Azure** visualizzazione.

3. Per arrestare il monitoraggio, fare clic con il pulsante destro del mouse sulla visualizzazione **OUTPUT** e scegliere **Arresta monitoraggio endpoint eventi predefinito.**

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Per inviare un messaggio dall'hub IoT al dispositivo, seguire questi passaggi:

1. Fare clic con il pulsante destro sul dispositivo e selezionare **Invia messaggio da cloud a dispositivo**.

2. Immettere il messaggio nella casella di input.

3. I risultati verranno visualizzati in **OUTPUT**  >  **hub IoT di Azure** visualizzazione.

## <a name="next-steps"></a>Passaggi successivi

È stato appreso come monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo tra il dispositivo IoT e l'hub IoT di Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]