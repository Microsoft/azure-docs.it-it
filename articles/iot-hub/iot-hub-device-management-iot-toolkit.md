---
title: Azure IoT device management with Azure IoT Tools for VSCode
description: Usare Azure IoT Tools per Visual Studio Code per la gestione dei dispositivi dell'hub IoT di Azure, con opzioni di gestione come i metodi diretti e le proprietà desiderate in dispositivi gemelli.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: d85e0e967dd802a77ccbc11b884d7a9f2891524d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688088"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Usare Azure IoT Tools per Visual Studio Code per la gestione dei dispositivi dell'hub IoT di Azure

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) è un'utile estensione di Visual Studio Code che semplifica la gestione dell'hub IoT e lo sviluppo di applicazioni IoT. Include opzioni di gestione che consentono di eseguire varie attività.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opzione di gestione          | Attività                    |
|----------------------------|--------------------------------|
| Metodi diretti             | Far eseguire un'attività al dispositivo, quale l'avvio o l'arresto dell'invio di messaggi o il riavvio del dispositivo.                                        |
| Lettura dispositivo gemello           | Ottenere lo stato restituito da un dispositivo. Ad esempio, il dispositivo segnala che il LED sta lampeggiando.                                    |
| Aggiornamento dispositivo gemello         | Impostare determinati stati di un dispositivo, ad esempio impostare la luce verde per un LED o impostare l'intervallo di invio dei dati di telemetria su 30 minuti.         |
| Messaggi da cloud a dispositivo   | Inviare notifiche a un dispositivo. Ad esempio "Oggi è molto probabile che piova. Meglio non dimenticare l'ombrello".              |

Per altre informazioni sulle differenze e sull'uso di queste opzioni, vedere [Device-to-cloud communication guidance](iot-hub-devguide-d2c-guidance.md) (Indicazioni sulla comunicazione da dispositivo a cloud) e [Cloud-to-device communication guidance](iot-hub-devguide-c2d-guidance.md) (Indicazioni sulla comunicazione da cloud a dispositivo).

I dispositivi gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi (metadati, configurazioni e condizioni). L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che si connette. Per altre informazioni sui dispositivi gemelli, vedere [Introduzione ai dispositivi gemelli](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Si apprende l'uso di Azure IoT Tools per Visual Studio Code con varie opzioni di gestione nel proprio computer di sviluppo.

## <a name="what-you-do"></a>Operazioni da fare

Eseguire Azure IoT Tools per Visual Studio Code con varie opzioni di gestione.

## <a name="what-you-need"></a>Elementi necessari

* Una sottoscrizione di Azure attiva.
* Un hub IoT di Azure nella sottoscrizione.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Strumenti IoT di Azure per il codice VS](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) o`vscode:extension/vsciot-vscode.azure-iot-tools`copiare l'URL e incollarlo in una finestra del browser: .

## <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

1. Nella visualizzazione **Explorer** del codice di Visual Studio, espandere la sezione **dispositivi Azure IoT Hub** nell'angolo in basso a sinistra.

2. Fare clic su **Seleziona hub IoT** nel menu di scelta rapida.

3. Verrà visualizzata una finestra popup nell'angolo in basso a destra che consentirà di accedere ad Azure per la prima volta.

4. Dopo l'accesso, verrà visualizzato l'elenco di sottoscrizioni di Azure; selezionare quindi la sottoscrizione di Azure e l'hub IoT.

5. Verrà visualizzato in pochi secondi l'elenco dei dispositivi nella scheda **Dispositivi di Azure IoT hub di Azure**.

   > [!Note]
   > È anche possibile completare la configurazione scegliendo **Set IoT Hub Connection String** (Configura la stringa di connessione dell'hub IoT). Immettere la stringa di connessione dei criteri **iothubowner** per l'hub IoT a cui si connette il dispositivo IoT nella finestra popup.

## <a name="direct-methods"></a>Metodi diretti

1. Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Invoke Direct Method** (Richiama metodo diretto). 

2. Immettere il nome e il payload del metodo nella casella di input.

3. I risultati verranno visualizzati nella visualizzazione**dell'hub OUTPUT Azure IoT.Results** will be shown in **OUTPUT** > Azure IoT Hub view.

## <a name="read-device-twin"></a>Lettura dispositivo gemello

1. Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Edit Device Twin** (Modifica dispositivo gemello). 

2. Verrà aperto un file **azure-iot-device-twin.json** con il contenuto del dispositivo gemello.

## <a name="update-device-twin"></a>Aggiornamento dispositivo gemello

1. Apportare alcune modifiche al campo **tags** o **properties.desired**.

2. Fare clic con il pulsante destro del mouse sul file **azure-iot-device-twin.json**.

3. Scegliere **Update Device Twin** (Aggiorna dispositivo gemello) per aggiornare il dispositivo gemello.

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Per inviare un messaggio dall'hub IoT al dispositivo, seguire questi passaggi:
 
1. Fare clic con il pulsante destro sul dispositivo e selezionare **Invia messaggio da cloud a dispositivo**. 

2. Immettere il messaggio nella casella di input.

3. I risultati verranno visualizzati nella visualizzazione**dell'hub OUTPUT Azure IoT.Results** will be shown in **OUTPUT** > Azure IoT Hub view.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come usare l'estensione Azure IoT Tools per Visual Studio Code con varie opzioni di gestione.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
