---
title: Connettere Raspberry Pi all'hub delle cose di Azure nel cloud (Node.js)
description: Informazioni su come configurare e connettere Raspberry Pi all'hub Azure per l'invio di dati alla piattaforma cloud di Azure in questa esercitazione.
author: wesmc7777
manager: eliotgra
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi invio dati al cloud, da raspberry pi al cloud
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: 1d6a51e2e9c052be0c59435b287c5fdde459f55d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334192"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Connettere Raspberry Pi ad Azure IoT Hub (Node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Questa esercitazione illustra le nozioni di base sull'uso di Raspberry Pi con il sistema operativo Raspbian. Viene poi illustrato come connettere i dispositivi al cloud usando l'[hub IoT di Azure](about-iot-hub.md). Per esempi di Windows 10 IoT Core, vedere [Windows Dev Center](https://www.windowsondevices.com/).

Se non si ha ancora un kit, Provare il [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). In alternativa, acquistare un nuovo kit [qui](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Operazioni da fare

* Creare un hub IoT.

* Registrare un dispositivo per Pi nel proprio hub IoT.

* Installare Raspberry Pi.

* Eseguire un'applicazione di esempio in Pi per inviare i dati del sensore all'hub IoT.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT di Azure e ottenere la stringa di connessione del nuovo dispositivo.

* Come connettere Pi con un sensore BME280.

* Come raccogliere i dati del sensore eseguendo un'applicazione di esempio in Pi.

* Come inviare i dati del sensore all'hub IoT.

## <a name="what-you-need"></a>Elementi necessari

![Elementi necessari](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Una scheda di Raspberry Pi 2 o Raspberry Pi 3.

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Un monitor, una tastiera USB e un mouse collegato a Pi.

* Un Mac o PC che esegue Windows o Linux.

* Una connessione Internet.

* Una scheda microSD da 16 GB o più.

* Una scheda microSD o un adattatore USB-SD con cui masterizzare l'immagine del sistema operativo nella scheda microSD.

* Un alimentatore da 5 V/2 A con cavo micro USB da 1,8 metri circa.

Gli elementi seguenti sono opzionali:

* Un sensore Adafruit BME280 assemblato per rilevare umidità, temperatura e pressione.

* Una basetta sperimentale.

* 6 cavi ponticello F/M.

* Un LED da 10 mm a luce diffusa.

> [!NOTE]
> Se non si dispone di elementi facoltativi, è possibile usare i dati dei sensori simulati.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Installare Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installare il sistema operativo Raspbian per Pi

Preparare la scheda microSD per l'installazione dell'immagine di Raspbian.

1. Scaricare Raspbian.

   a. [Raspbian Buster con desktop](https://www.raspberrypi.org/downloads/raspbian/) (file zip).

   b. Estrarre l'immagine di Raspbian in una cartella nel computer.

2. Installare Raspbian nella scheda microSD.

   a. [Scaricare e installare l'utilità di masterizzazione di schede SD per etcher](https://etcher.io/).

   b. Eseguire Etcher e selezionare l'immagine di Raspbian estratta nel passaggio 1.

   c. Selezionare l'unità della scheda microSD. Etcher potrebbe avere già selezionato l'unità corretta.

   d. Fare clic su Flash per installare Raspbian nella scheda microSD.

   e. Rimuovere la scheda microSD dal computer al termine dell'installazione. È possibile rimuovere direttamente la scheda microSD perché viene espulsa o smontata automaticamente da Etcher al termine dell'operazione.

   f. Inserire la scheda microSD in Pi.

### <a name="enable-ssh-and-i2c"></a>Abilitare SSH e I2C

1. Collegare Pi al monitor, alla tastiera e al mouse.

2. Avviare pi e accedere a Raspbian usando `pi` come nome utente e `raspberry` come password.

3. Fare clic sull'icona Raspberry > la configurazione **Preferenze**  >  **Raspberry Pi**.

   ![Il menu Preferenze di Raspbian](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. Nella scheda **Interfacce** impostare **I2C** e **SSH** su **Abilita**, quindi fare clic su **OK**. Se non si hanno sensori fisici e si vogliono usare i dati di sensori simulati, questo passaggio è facoltativo.

   ![Abilitare I2C e SSH su Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Per abilitare SSH e I2C è possibile trovare più documenti di riferimento su [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Connettere il sensore a Pi

Usare i cavi ponticello e la basetta sperimentale per connettere un LED e un sensore BME280 a Pi, come indicato di seguito. Se non si dispone del sensore, [ignorare questa sezione](#connect-pi-to-the-network).

![La connessione di Raspberry Pi e del sensore](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

Il sensore BME280 può raccogliere i dati relativi a temperatura e umidità. Il LED lampeggia quando il dispositivo invia un messaggio al cloud.

Per i pin dei sensori usare i collegamenti seguenti:

| Inizio (sensore e LED)     | Fine (scheda)            | Colore del cavo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5G)             | 3,3 V PWR (Pin 1)       | Cavo bianco   |
| GND (Pin 7G)             | GND (Pin 6)            | Cavo marrone   |
| SDI (Pin 10G)            | I2C1 SDA (Pin 3)       | Cavo rosso     |
| SCK (Pin 8G)             | I2C1 SCL (Pin 5)       | Cavo arancione  |
| LED VDD (Pin 18F)        | GPIO 24 (Pin 18)       | Cavo bianco   |
| LED GND (Pin 17F)        | GND (Pin 20)           | Cavo nero   |

Fare clic per visualizzare i [mapping pin di Raspberry Pi 2 & 3](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) come riferimento.

Dopo aver correttamente collegato BME280 a Raspberry Pi, dovrebbe apparire come mostrato nell'immagine di seguito.

![Pi e BME280 connessi](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Connettere Pi alla rete

Accendere Pi usando il cavo micro USB e l'alimentatore. Usare il cavo Ethernet per connettere Pi alla rete cablata oppure seguire le [istruzioni riportate nella Guida di Raspberry Pi](https://www.raspberrypi.org/documentation/configuration/wireless/) per connettere Pi alla rete wireless. Dopo aver connesso Pi alla rete, è necessario annotarne [l'indirizzo IP](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Connesso alla rete cablata](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Verificare che Pi sia connesso alla stessa rete del computer. Se il computer è connesso a una rete wireless mentre il dispositivo Pi è connesso a una rete cablata, ad esempio, l'indirizzo IP potrebbe non essere incluso nell'output di devdisco.

## <a name="run-a-sample-application-on-pi"></a>Eseguire un'applicazione di esempio in Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Clonare l'applicazione di esempio e installare i pacchetti di prerequisiti

1. Connettersi a Raspberry Pi usando uno dei client SSH seguenti dal computer host.

   **Utenti Windows**

   a. Scaricare e installare [PuTTY](https://www.putty.org/) per Windows.

   b. Copiare l'indirizzo IP di Pi nella sezione relativa a nome host o indirizzo IP e selezionare SSH come tipo di connessione.

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Utenti Mac e Ubuntu**

   Usare il client SSH predefinito in Ubuntu o macOS. Per connettere Pi tramite SSH potrebbe essere necessario eseguire `ssh pi@<ip address of pi>`.

   > [!NOTE]
   > Il nome utente predefinito è `pi` e la password è `raspberry`.

2. Installare Node.js e NPM in Pi.

   Controllare prima la versione di Node.js.

   ```bash
   node -v
   ```

   Se la versione è inferiore a 10. x o se non è presente alcun Node.js nel dispositivo pi, installare la versione più recente.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Clonare l'applicazione di esempio.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Installare tutti i pacchetti relativi all'esempio. L'installazione include Azure IoT SDK per dispositivi, la libreria del sensore BME280 e libreria di Pi per i collegamenti.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >A seconda della connessione di rete, il completamento del processo di installazione potrebbe richiedere alcuni minuti.

### <a name="configure-the-sample-application"></a>Configurare l'applicazione di esempio

1. Aprire il file di configurazione eseguendo i comandi seguenti:

   ```bash
   nano config.json
   ```

   ![File di configurazione](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   Questo file contiene due elementi configurabili. Il primo è `interval`, che definisce l'intervallo di tempo (in millisecondi) tra due messaggi inviati al cloud. Il secondo è `simulatedData`, ossia un valore booleano che indica se usare o no i dati del sensore simulato.

   Se **non si dispone del sensore**, impostare il valore `simulatedData` su `true` per permettere all'applicazione di esempio di creare e usare i dati del sensore simulati.

   *Nota: per impostazione predefinita, l'indirizzo I2C usato in questa esercitazione è 0x77. A seconda della configurazione, potrebbe anche essere 0x76: se si verifica un errore I2C, provare a modificare il valore in 118 e verificare se il funzionamento è migliore. Per individuare l'indirizzo usato dal sensore, eseguirlo `sudo i2cdetect -y 1` in una shell in Raspberry Pi*

2. Salvare e uscire premendo CTRL-O > INVIO > CTRL-X.

### <a name="run-the-sample-application"></a>Eseguire l'applicazione di esempio

Eseguire l'applicazione di esempio tramite il comando seguente:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Assicurarsi di copiare e incollare la stringa di connessione del dispositivo tra virgolette singole.

Dovrebbe essere visibile l'output seguente che mostra i dati del sensore e i messaggi inviati all'hub IoT.

![Output - dati del sensore inviati da Raspberry Pi all'hub IoT](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Leggere i messaggi ricevuti dall'hub

Un modo per monitorare i messaggi ricevuti dall'hub delle cose dal dispositivo consiste nell'usare gli strumenti di Azure per la Visual Studio Code. Per altre informazioni, vedere [usare gli strumenti di Azure per la Visual Studio Code per inviare e ricevere messaggi tra il dispositivo e l'hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)Internet.

Per altri modi per elaborare i dati inviati dal dispositivo, continuare con la sezione successiva.

## <a name="next-steps"></a>Passaggi successivi

È stata eseguita un'applicazione di esempio per raccogliere i dati del sensore e inviarli all'hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
