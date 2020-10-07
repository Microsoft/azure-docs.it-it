---
title: "Avvio rapido: Effettuare il provisioning di un dispositivo TPM simulato nell'hub IoT di Azure con Node.js"
description: "Avvio rapido: creare un dispositivo TPM simulato ed effettuarne il provisioning con l'SDK per dispositivi Node.js per il servizio Device Provisioning in hub IoT di Azure. Questa guida introduttiva usa registrazioni singole."
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: f8b7e55d95c08a31a20503e329e64b9e65e3b723
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323924"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Guida introduttiva: Creare ed effettuare il provisioning di un dispositivo simulato TPM usando Node.js Device SDK per il servizio Device Provisioning in hub IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Questa guida di avvio rapido descrive come creare un dispositivo IoT simulato in un computer Windows. Il dispositivo simulato include un simulatore TPM come modulo di protezione hardware. Verrà usato il codice Node.js di esempio del dispositivo per connettere il dispositivo simulato all'hub IoT usando una registrazione singola con il servizio Device provisioning.

## <a name="prerequisites"></a>Prerequisiti

- Conoscenza dei concetti relativi al [provisioning](about-iot-dps.md#provisioning-process).
- Completamento dell'esercitazione [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md).
- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v4.0 o versioni successive](https://nodejs.org).
- [Git](https://git-scm.com/download/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparare l'ambiente 

1. Verificare che [Node.js v4.0 o versione successiva](https://nodejs.org) sia installato nel computer.

1. Verificare che `git` sia installato nel computer e venga aggiunto alle variabili di ambiente accessibili alla finestra di comando. Vedere gli [strumenti client Git di Software Freedom Conservancy](https://git-scm.com/download/) per la versione più recente degli strumenti `git` da installare, tra cui **Git Bash**, l'app da riga di comando che è possibile usare per interagire con il repository Git locale. 


## <a name="simulate-a-tpm-device"></a>Simulare un dispositivo TPM

1. Aprire un prompt dei comandi o Git Bash. Clonare il repository GitHub `azure-utpm-c`:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. Passare alla cartella radice GitHub ed eseguire il simulatore [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) affinché sia il [Modulo di protezione hardware](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) per il dispositivo simulato. che è in ascolto di un socket sulle porte 2321 e 2322. Non chiudere questa finestra di comando. Il simulatore dovrà restare in esecuzione fino alla fine di questa guida di avvio rapido: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Creare una nuova cartella vuota denominata **registerdevice**. Nella cartella **registerdevice** creare un file package.json eseguendo questo comando al prompt dei comandi. Rispondere a tutte le domande poste da `npm` o accettare i valori predefiniti, se idonei:
   
    ```cmd/sh
    npm init
    ```

1. Installare i pacchetti precursori seguenti:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > Sono presenti alcuni problemi noti per l'installazione dei pacchetti indicati in precedenza. Per risolvere questi problemi, eseguire `npm install --global --production windows-build-tools` usando un prompt dei comandi in modalità **Esegui come amministratore**, eseguire `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` dopo aver sostituito il percorso con quello della versione installata e quindi eseguire di nuovo i comandi di installazione precedenti.
    >

1. Installare i pacchetti seguenti che contengono i componenti usati durante la registrazione:

   - un client di protezione che funziona con TPM: `azure-iot-security-tpm`
   - un trasporto per consentire al dispositivo di connettersi al servizio Device Provisioning: `azure-iot-provisioning-device-http` oppure `azure-iot-provisioning-device-amqp`
   - un client per usare il traporto e il client di protezione: `azure-iot-provisioning-device`

     Dopo aver registrato il dispositivo, è possibile usare i normali pacchetti dei client per dispositivi dell'hub IoT per connettere il dispositivo con le credenziali fornite durante la registrazione. Prerequisiti:

   - il client per dispositivi: `azure-iot-device`
   - un trasporto: `azure-iot-device-amqp`, `azure-iot-device-mqtt`, o `azure-iot-device-http`
   - il client di protezione già installato: `azure-iot-security-tpm`

     > [!NOTE]
     > Gli esempi seguenti usano i trasporti `azure-iot-provisioning-device-http` e `azure-iot-device-mqtt`.
     > 

     È possibile installare tutti questi pacchetti contemporaneamente eseguendo questo comando al prompt dei comandi nella cartella **registerdevice**:

       ```cmd/sh
       npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
       ```

1. Con un editor di testo creare un nuovo file **ExtractDevice.js** nella cartella **registerdevice**.

1. Aggiungere le istruzioni `require` seguenti all'inizio del file **ExtractDevice.js**:
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Aggiungere la funzione seguente per implementare il metodo:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Salvare e chiudere il file **ExtractDevice.js**. Eseguire l'esempio:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. Nella finestra di output vengono visualizzati la **_chiave di verifica dell'autenticità_** e l' **_ID registrazione_** necessari per la registrazione del dispositivo. Annotare questi valori. 


## <a name="create-a-device-entry"></a>Creare una voce del dispositivo

Il servizio Device Provisioning in Azure IoT supporta due tipi di registrazione:

- [Gruppi di registrazioni](concepts-service.md#enrollment-group): Usato per registrare più dispositivi correlati.
- [Registrazioni singole](concepts-service.md#individual-enrollment): Usato per registrare un singolo dispositivo.

Questo articolo descrive le registrazioni singole.

1. Accedere al portale di Azure, selezionare il pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio Device Provisioning.

1. Nel menu del servizio Device Provisioning selezionare **Gestisci registrazioni**. Selezionare la scheda **Registrazioni singole** e quindi il pulsante **Aggiungi registrazione singola** in alto. 

1. Nel pannello **Aggiungi registrazione** immettere le informazioni seguenti:
   - Selezionare **TPM** come *meccanismo* di attestazione dell'identità.
   - Immettere l'*ID registrazione* e la *chiave di verifica dell'autenticità* per il dispositivo TPM usando i valori di cui si è preso nota in precedenza.
   - Selezionare un hub IoT collegato al servizio di provisioning.
   - Facoltativamente, è possibile specificare le informazioni seguenti:
       - Immettere un *ID dispositivo* univoco. Assicurarsi di non usare dati sensibili quando si assegna un nome al dispositivo. Se si sceglie di non specificarne uno, per identificare il dispositivo verrà usato l'ID registrazione.
       - Aggiornare lo **stato iniziale del dispositivo gemello** con la configurazione iniziale desiderata per il dispositivo.
   - Al termine, fare clic sul pulsante **Salva**. 

     ![Immettere le informazioni di registrazione del dispositivo nel pannello del portale](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Dopo la corretta registrazione, l'*ID registrazione* del dispositivo viene visualizzato nell'elenco della scheda *Registrazioni singole*. 


## <a name="register-the-device"></a>Registrare il dispositivo

1. Nel portale di Azure selezionare il pannello **Panoramica** per il servizio Device Provisioning e prendere nota dei valori **_Endpoint dispositivo globale_** e **_Ambito ID_** .

    ![Estrarre le informazioni dell'endpoint del servizio Device Provisioning dal pannello del portale](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Con un editor di testo creare un nuovo file **RegisterDevice.js** nella cartella **registerdevice**.

1. Aggiungere le istruzioni `require` seguenti all'inizio del file **RegisterDevice.js**:
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > **Azure IoT SDK per Node.js** supporta altri protocolli come _AMQP_, _AMQP WS_ e _MQTT WS_.  Per altri esempi vedere [Esempi di Device Provisioning Service SDK per Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).
    > 

1. Aggiungere le variabili **globalDeviceEndpoint** e **idScope** e usarle per creare un'istanza di **ProvisioningDeviceClient**. Sostituire **{globalDeviceEndpoint}** e **{idScope}** con i valori di **_endpoint dispositivo globale_** e **_ID ambito_** del **passaggio 1**:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Aggiungere la funzione seguente per implementare il metodo nel dispositivo:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Salvare e chiudere il file **RegisterDevice.js**. Eseguire l'esempio:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. Si notino i messaggi che simulano l'avvio e la connessione del dispositivo al servizio Device Provisioning per ottenere le informazioni dell'hub IoT. Al termine del provisioning del dispositivo simulato nell'hub IoT collegato al servizio di provisioning, l'ID dispositivo verrà visualizzato nel pannello **Dispositivi IoT** dell'hub. 

    ![Il dispositivo viene registrato con l'hub IoT](./media/quick-create-simulated-device/hub-registration.png) 

    Se si è modificato lo *stato iniziale del dispositivo gemello* rispetto al valore predefinito della voce di registrazione del dispositivo, è possibile eseguire il pull dello stato del dispositivo desiderato dall'hub e agire di conseguenza. Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare ed esplorare l'esempio di client dispositivo, non eseguire la pulizia delle risorse create in questa guida di avvio rapido. Se non si intende continuare, seguire questa procedura per eliminare tutte le risorse create in questa guida.

1. Chiudere la finestra di output di esempio di client del dispositivo sul computer.
1. Chiudere la finestra del simulatore TPM sul computer.
1. Nel portale di Azure selezionare **Tutte le risorse** nel menu a sinistra e quindi il servizio Device Provisioning. Aprire il pannello **Gestisci registrazioni** per il servizio e quindi selezionare la scheda **Registrazioni singole**. Selezionare la casella di controllo accanto all'*ID registrazione* del dispositivo registrato in questa guida di avvio rapido e fare clic sul pulsante **Elimina** nella parte superiore del riquadro. 
1. Nel menu a sinistra nel portale di Azure selezionare **Tutte le risorse** e quindi selezionare l'hub IoT. Aprire il pannello **Dispositivi IoT** per l'hub, selezionare la casella di controllo accanto all'*ID dispositivo* del dispositivo registrato in questa guida di avvio rapido e quindi fare clic sul pulsante **Elimina** nella parte superiore del riquadro.


## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un dispositivo simulato TPM nel computer e ne è stato effettuato il provisioning nell'hub IoT usando il servizio Device Provisioning in hub IoT. Per informazioni su come registrare un dispositivo TPM a livello di codice, continuare con l'argomento di avvio rapido per la registrazione a livello di codice di un dispositivo TPM. 

> [!div class="nextstepaction"]
> [Avvio rapido di Azure: Registrare un dispositivo TPM nel servizio Device Provisioning in hub IoT di Azure](quick-enroll-device-tpm-node.md)
