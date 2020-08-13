---
title: Avvio rapido - Effettuare il provisioning di un dispositivo TPM simulato nell'hub IoT di Azure con Python
description: "Avvio rapido: creare un dispositivo TPM simulato ed effettuarne il provisioning con l'SDK per dispositivi Java per il servizio Device Provisioning in hub IoT. Questa guida introduttiva usa registrazioni singole."
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 53590344a45990187b5ca8aba4eca18da526f308
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87872617"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Guida introduttiva: Creare ed effettuare il provisioning di un dispositivo simulato TPM usando l'SDK per dispositivi Python per il servizio Device Provisioning in hub IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Questa guida di avvio rapido descrive come creare un dispositivo IoT simulato in un computer Windows. Il dispositivo simulato include un simulatore TPM come modulo di protezione hardware. Verrà usato il codice Python di esempio del dispositivo per connettere il dispositivo simulato all'hub IoT usando una registrazione singola con il servizio Device provisioning.

## <a name="prerequisites"></a>Prerequisiti

- Revisione dei [Concetti relativi al provisioning automatico](concepts-auto-provisioning.md).
- Completamento dell'esercitazione [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md).
- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2015+](https://visualstudio.microsoft.com/vs/) con il carico di lavoro Sviluppo di applicazioni desktop con C++.
- [Sistema di compilazione CMake](https://cmake.org/download/).
- [Git](https://git-scm.com/download/).

> [!IMPORTANT]
> Questo articolo si applica solo alla versione 1 dell'SDK per Python, ora deprecata. I client dispositivo e servizio per il servizio Device Provisioning in hub IoT non sono ancora disponibili nella versione 2. Il team si sta attualmente impegnando per offrire la parità delle funzionalità nella versione 2.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparare l'ambiente 

1. Assicurarsi di aver installato [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 o versione successiva, con il carico di lavoro Sviluppo di applicazioni desktop con C++ abilitato per l'installazione di Visual Studio.

1. Scaricare e installare il [sistema di compilazione CMake](https://cmake.org/download/).

1. Verificare che `git` sia installato nel computer e venga aggiunto alle variabili di ambiente accessibili alla finestra di comando. Vedere gli [strumenti client Git di Software Freedom Conservancy](https://git-scm.com/download/) per la versione più recente degli strumenti `git` da installare, tra cui **Git Bash**, l'app da riga di comando che è possibile usare per interagire con il repository Git locale. 

1. Aprire un prompt dei comandi o Git Bash. Clonare il repository GitHub per l'esempio di codice di simulazione del dispositivo:
    
    ```cmd/sh
    git clone --single-branch --branch v1-deprecated https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

1. Creare una cartella nella copia locale di questo repository GitHub per il processo di compilazione CMake. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

1. L'esempio di codice usa un simulatore Windows TPM. Usare il comando seguente per abilitare l'autenticazione con token di firma di accesso condiviso. Viene anche generata una soluzione di Visual Studio per il dispositivo simulato.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

1. In un prompt dei comandi separato passare alla cartella del simulatore TPM ed eseguire il simulatore [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) affinché sia il [Modulo di protezione hardware](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) per il dispositivo simulato. Fare clic su **Consenti accesso**. che è in ascolto di un socket sulle porte 2321 e 2322. Non chiudere questa finestra di comando. Il simulatore dovrà restare in esecuzione fino alla fine di questa guida di avvio rapido. 

    ```cmd/sh
    .\azure-iot-sdk-python\c\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    ![Simulatore TPM](./media/python-quick-create-simulated-device/tpm-simulator.png)


## <a name="create-a-device-enrollment-entry"></a>Creare una voce per la registrazione dei dispositivi

Il servizio Device Provisioning in Azure IoT supporta due tipi di registrazione:

- [Gruppi di registrazioni](concepts-service.md#enrollment-group): Usato per registrare più dispositivi correlati.
- [Registrazioni singole](concepts-service.md#individual-enrollment): Usato per registrare un singolo dispositivo.

Questo articolo descrive le registrazioni singole.

1. Aprire la soluzione generata nella cartella *cmake* denominata `azure_iot_sdks.sln` e compilarla in Visual Studio.

1. Fare clic con il pulsante destro del mouse sul progetto **tpm_device_provision** e scegliere **Imposta come progetto di avvio**. Eseguire la soluzione. Nella finestra di output vengono visualizzati la **_chiave di verifica dell'autenticità_** e l' **_ID registrazione_** necessari per la registrazione del dispositivo. Annotare questi valori. 

    ![Configurazione TPM](./media/python-quick-create-simulated-device/tpm-setup.png)

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

     ![Immettere le informazioni di registrazione del dispositivo nel pannello del portale](./media/python-quick-create-simulated-device/enterdevice-enrollment.png)  

   Dopo la corretta registrazione, l'*ID registrazione* del dispositivo verrà visualizzato nell'elenco della scheda *Individual Enrollments* (Registrazioni singole). 


## <a name="simulate-the-device"></a>Simulare il dispositivo

1. Scaricare e installare [Python 2.x o 3.x](https://www.python.org/downloads/). Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alle variabili di ambiente specifiche per la piattaforma.
    - Se si usa il sistema operativo Windows, usare il [pacchetto ridistribuibile di Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) per consentire l'uso di DLL native da Python.

1. Seguire [queste istruzioni](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) per compilare i pacchetti Python.

   > [!NOTE]
   > Se si esegue `build_client.cmd`, assicurarsi di usare il flag `--use-tpm-simulator`.
   > 
   > [!NOTE]
   > Se si usa `pip`, assicurarsi di installare anche il pacchetto `azure-iot-provisioning-device-client`. Si noti che i pacchetti PIP rilasciati usano il vero modulo TPM, non il simulatore. Per usare il simulatore, è necessario eseguire la compilazione dall'origine usando il flag `--use-tpm-simulator`.

1. Passare alla cartella degli esempi.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

1. Usando l'IDE Python, modificare lo script Python denominato **provisioning\_device\_client\_sample.py**. Modificare le variabili *GLOBAL\_PROV\_URI* e *ID\_SCOPE* specificando i valori annotati in precedenza. Assicurarsi anche che la variabile *SECURITY\_DEVICE\_TYPE* sia impostata su `ProvisioningSecurityDeviceType.TPM`

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.TPM
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

    ![Informazioni sul servizio](./media/python-quick-create-simulated-device/extract-dps-endpoints.png)

1. Eseguire l'esempio. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

1. Si notino i messaggi che simulano l'avvio e la connessione del dispositivo al servizio Device Provisioning per ottenere le informazioni dell'hub IoT. 

    ![Registrazione completata](./media/python-quick-create-simulated-device/registration-success.png)

1. Al termine del provisioning del dispositivo simulato nell'hub IoT collegato al servizio di provisioning, l'ID dispositivo verrà visualizzato nel pannello **Dispositivi IoT** dell'hub.

    ![Il dispositivo viene registrato con l'hub IoT](./media/python-quick-create-simulated-device/hubregistration.png) 

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
> [Avvio rapido di Azure: Registrare un dispositivo TPM nel servizio Device Provisioning in hub IoT di Azure](quick-enroll-device-tpm-python.md)
