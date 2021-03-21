---
title: Avvio rapido - Effettuare il provisioning di un dispositivo TPM simulato nell'hub IoT di Azure con C#
description: Questa guida introduttiva usa registrazioni singole. In questa guida di avvio rapido viene creato un dispositivo TPM simulato e ne viene effettuato il provisioning con l'SDK per dispositivi C per il servizio Device Provisioning in hub IoT di Azure.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom:
- mvc
- amqp
- mqtt
ms.openlocfilehash: e2930a3ca2ecb9d8217fdfea1cbcb0e669f61775
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960037"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Guida introduttiva: Effettuare il provisioning di un dispositivo simulato TPM tramite Azure IoT C SDK

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

In questa guida introduttiva si apprenderà come creare ed eseguire un simulatore di dispositivo TPM (Trusted Platform Module) in un computer di sviluppo Windows. Si connetterà il dispositivo simulato a un hub IoT usando un'istanza del servizio Device Provisioning. Verrà usato codice di esempio da [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) per facilitare la registrazione del dispositivo in un'istanza del servizio Device Provisioning e simulare una sequenza di avvio per il dispositivo.

Se non si ha familiarità con il processo di provisioning automatico, vedere la panoramica relativa al [provisioning](about-iot-dps.md#provisioning-process). Assicurarsi anche di avere completato la procedura descritta in [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md) prima di continuare con questa guida introduttiva. 

Il servizio Device Provisioning in Azure IoT supporta due tipi di registrazione:
- [Gruppi di registrazioni](concepts-service.md#enrollment-group): Usato per registrare più dispositivi correlati.
- [Registrazioni singole](concepts-service.md#individual-enrollment): Usato per registrare un singolo dispositivo.

Questo articolo descrive le registrazioni singole.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti seguenti si riferiscono a un ambiente di sviluppo Windows. Per Linux o macOS, vedere la sezione appropriata in [Preparare l'ambiente di sviluppo](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) nella documentazione dell'SDK.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) con il carico di lavoro ['Sviluppo di applicazioni desktop con C++'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) abilitato. Sono supportati anche Visual Studio 2015 e Visual Studio 2017.

* La versione più recente di [Git](https://git-scm.com/download/) installata.

<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Preparare un ambiente di sviluppo per Azure IoT C SDK

In questa sezione si preparerà un ambiente di sviluppo usato per compilare [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) e l'esempio del simulatore di dispositivo [TPM](/windows/device-security/tpm/trusted-platform-module-overview).

1. Scaricare il [sistema di compilazione CMake](https://cmake.org/download/).

    È importante che nel computer siano installati i prerequisiti di Visual Studio (Visual Studio e carico di lavoro 'Sviluppo di applicazioni desktop con C++') **prima** di avviare l'installazione di `CMake`. Quando i prerequisiti sono pronti e il download è stato verificato, installare il sistema di compilazione CMake.

2. Trovare il nome del tag per la [versione più recente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) dell'SDK.

3. Aprire un prompt dei comandi o la shell Git Bash. Eseguire i comandi seguenti per clonare la versione più recente del repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Usare il tag trovato nel passaggio precedente come valore per il parametro `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

4. Creare una sottodirectory `cmake` nella directory radice del repository Git e passare a tale cartella. Eseguire i comandi seguenti dalla directory `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>Compilare l'SDK ed eseguire il simulatore di dispositivo TPM

In questa sezione si compilerà Azure IoT C SDK, che include il codice di esempio del simulatore di dispositivo TPM. Questo esempio offre un [meccanismo di attestazione](concepts-service.md#attestation-mechanism) TPM tramite l'autenticazione di token di firma di accesso condiviso (SAS).

1. Dalla sottodirectory `cmake` creata nel repository Git azure-iot-sdk-c, eseguire il comando seguente per compilare l'esempio. Tramite questo comando di compilazione verrà generata anche una soluzione di Visual Studio per il dispositivo simulato.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Se `cmake` non trova il compilatore C++, si potrebbero verificare errori di compilazione durante l'esecuzione del comando precedente. In tal caso, provare a eseguire questo comando nel [prompt dei comandi di Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Al termine della compilazione, le ultime righe di output saranno simili all'output seguente:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Passare alla cartella radice del repository Git clonato ed eseguire il simulatore [TPM](/windows/device-security/tpm/trusted-platform-module-overview) usando il percorso indicato di seguito. Questo simulatore è in ascolto su un socket sulle porte 2321 e 2322. Non chiudere questa finestra di comando. Il simulatore dovrà restare in esecuzione fino alla fine di questa guida introduttiva. 

   Se si è nella cartella *cmake*, eseguire i comandi seguenti:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Non verrà visualizzato alcun output dal simulatore. Lasciare che continui l'esecuzione simulando un dispositivo TPM.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>Leggere le chiavi di crittografia dal dispositivo TPM

In questa sezione verrà compilato ed eseguito un esempio che leggerà la chiave di approvazione e l'ID di registrazione dal simulatore TPM lasciato in esecuzione e in ascolto sulle porte 2321 e 2322. Questi valori verranno usati per la registrazione del dispositivo nell'istanza del servizio Device Provisioning.

1. Avviare Visual Studio e aprire il nuovo file di soluzione denominato `azure_iot_sdks.sln`. Questo file della soluzione si trova nella cartella `cmake` creata in precedenza nella radice del repository Git azure-iot-sdk-c.

2. Nel menu di Visual Studio selezionare **Compila** > **Compila soluzione** per compilare tutti i progetti nella soluzione.

3. Nella finestra *Esplora soluzioni* di Visual Studio passare alla cartella **Provision\_Tools**. Fare clic con il pulsante destro del mouse sul progetto **tpm_device_provision** e scegliere **Imposta come progetto di avvio**. 

4. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. L'app legge e visualizza un **_ID registrazione_** e una **_Chiave di approvazione_**. Prendere nota di questi valori o copiarli. Questi valori verranno usati nella sezione successiva per la registrazione del dispositivo. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Creare una voce per la registrazione del dispositivo nel portale

1. Accedere al portale di Azure, selezionare il pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio Device Provisioning.

1. Selezionare **Gestisci registrazioni** e quindi fare clic sul pulsante **Aggiungi registrazione singola** nella parte superiore. 

1. Nel pannello **Aggiungi registrazione** immettere le informazioni seguenti:
   - Selezionare **TPM** come *meccanismo* di attestazione dell'identità.
   - Immettere l'*ID registrazione* e la *chiave di verifica dell'autenticità* per il dispositivo TPM usando i valori di cui si è preso nota in precedenza.
   - Selezionare un hub IoT collegato al servizio di provisioning.
   - Facoltativamente, è possibile specificare le informazioni seguenti:
       - Immettere un *ID dispositivo* univoco. È possibile usare quello suggerito **test-docs-device** o specificarne uno personalizzato. Assicurarsi di non usare dati sensibili quando si assegna un nome al dispositivo. Se si sceglie di non specificarne uno, per identificare il dispositivo verrà usato l'ID registrazione.
       - Aggiornare lo **stato iniziale del dispositivo gemello** con la configurazione iniziale desiderata per il dispositivo.
   - Al termine, fare clic sul pulsante **Salva**. 

      ![Immettere le informazioni di registrazione del dispositivo nel portale](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      Dopo la corretta registrazione, l'*ID registrazione* del dispositivo verrà visualizzato nell'elenco della scheda *Individual Enrollments* (Registrazioni singole). 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simulare la sequenza del primo avvio per il dispositivo

In questa sezione si configurerà il codice di esempio per l'uso di [AMQP (Advanced Message Queuing Protocol)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) per inviare la sequenza di avvio del dispositivo all'istanza del servizio Device Provisioning. Con questa sequenza di avvio il dispositivo verrà riconosciuto e assegnato a un hub IoT collegato all'istanza del servizio Device Provisioning.

1. Nel portale di Azure selezionare la scheda **Panoramica** per il servizio Device Provisioning e copiare il valore **_Ambito ID_**.

    ![Estrarre le informazioni dell'endpoint del servizio Device Provisioning dal portale](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. Nella finestra *Esplora soluzioni* di Visual Studio passare alla cartella **Provision\_Samples**. Espandere il progetto di esempio denominato **prov\_dev\_client\_sample**. Espandere **File di origine** e aprire **prov\_dev\_client\_sample.c**.

3. Nella parte superiore del file trovare le istruzioni `#define` per ogni protocollo di dispositivo come illustrato di seguito. Assicurarsi che solo `SAMPLE_AMQP` sia senza commento.

    Attualmente, il [protocollo MQTT non è supportato per la registrazione individuale TPM](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. Trovare la costante `id_scope` e sostituire il valore con il valore **Ambito ID** copiato in precedenza. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Trovare la definizione per la funzione `main()` nello stesso file. Assicurarsi che la variabile `hsm_type` sia impostata su `SECURE_DEVICE_TYPE_TPM` invece di `SECURE_DEVICE_TYPE_X509` come illustrato di seguito.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Fare clic con il pulsante destro del mouse sul progetto **prov\_dev\_client\_sample** e scegliere **Imposta come progetto di avvio**. 

7. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. Nella richiesta di ricompilare il progetto fare clic su **Sì** per ricompilare il progetto prima dell'esecuzione.

    L'output seguente è un esempio dell'avvio corretto del client del dispositivo di provisioning di esempio, che si connette all'istanza del servizio Device Provisioning per ottenere informazioni sull'hub IoT ed effettuare la registrazione:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Quando il servizio di provisioning completa il provisioning del dispositivo simulato nell'hub IoT, l'ID del dispositivo viene visualizzato tra i **Dispositivi IoT** dell'hub. 

    ![Il dispositivo viene registrato con l'hub IoT](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare ed esplorare l'esempio di client dispositivo, non eseguire la pulizia delle risorse create in questa guida di avvio rapido. Se non si intende continuare, seguire questa procedura per eliminare tutte le risorse create in questa guida.

1. Chiudere la finestra di output di esempio di client del dispositivo sul computer.
2. Chiudere la finestra del simulatore TPM sul computer.
3. Nel portale di Azure selezionare **Tutte le risorse** nel menu a sinistra e quindi il servizio Device Provisioning. Aprire **Gestisci registrazioni** per il servizio e quindi selezionare la scheda **Registrazioni singole**. Selezionare la casella di controllo accanto all'*ID registrazione* del dispositivo registrato in questa guida di avvio rapido e fare clic sul pulsante **Elimina** nella parte superiore del riquadro. 
4. Nel menu a sinistra nel portale di Azure selezionare **Tutte le risorse** e quindi selezionare l'hub IoT. Aprire il pannello **Dispositivi IoT** per l'hub, selezionare la casella di controllo accanto all'*ID dispositivo* del dispositivo registrato in questa guida di avvio rapido e quindi fare clic sul pulsante **Elimina** nella parte superiore del riquadro.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un dispositivo simulato TPM nel computer e ne è stato effettuato il provisioning nell'hub IoT usando il servizio Device Provisioning in hub IoT. Per informazioni su come registrare un dispositivo TPM a livello di codice, continuare con l'argomento di avvio rapido per la registrazione a livello di codice di un dispositivo TPM. 

> [!div class="nextstepaction"]
> [Avvio rapido di Azure: Registrare un dispositivo TPM nel servizio Device Provisioning in hub IoT di Azure](quick-enroll-device-tpm-java.md)