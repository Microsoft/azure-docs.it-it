---
title: "Avvio rapido: Effettuare il provisioning di un dispositivo TPM simulato nell'hub IoT di Azure con Java"
description: "Avvio rapido: creare un dispositivo TPM simulato ed effettuarne il provisioning con l'SDK per dispositivi Java per il servizio Device Provisioning in hub IoT di Azure. Questa guida introduttiva usa registrazioni singole."
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 31832c13ddee848864dcfe0d796deb7fcdcd8359
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526545"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-java-device-sdk-for-azure-iot-hub-device-provisioning-service"></a>Avvio rapido: Creare ed effettuare il provisioning di un dispositivo simulato TPM usando l'SDK per dispositivi Java per il servizio Device Provisioning in hub IoT di Azure.

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Questa guida di avvio rapido descrive come creare un dispositivo IoT simulato in un computer Windows. Il dispositivo simulato include un simulatore TPM come modulo di protezione hardware. Verrà usato il codice Java di esempio del dispositivo per connettere il dispositivo simulato all'hub IoT usando una registrazione singola con il servizio Device provisioning.

## <a name="prerequisites"></a>Prerequisiti 

- Conoscenza dei concetti relativi al [provisioning](about-iot-dps.md#provisioning-process).
- Completamento dell'esercitazione [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md).
- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Development Kit 8](https://aka.ms/azure-jdks).
- [Maven](https://maven.apache.org/install.html).
- [Git](https://git-scm.com/download/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparare l'ambiente 

1. Verificare che [Java SE Development Kit 8](https://aka.ms/azure-jdks) sia installato nel computer.

1. Scaricare e installare [Maven](https://maven.apache.org/install.html).

1. Verificare che `git` sia installato nel computer e venga aggiunto alle variabili di ambiente accessibili alla finestra di comando. Vedere gli [strumenti client Git di Software Freedom Conservancy](https://git-scm.com/download/) per la versione più recente degli strumenti `git` da installare, tra cui **Git Bash**, l'app da riga di comando che è possibile usare per interagire con il repository Git locale. 

1. Aprire un prompt dei comandi. Clonare il repository GitHub per l'esempio di codice di simulazione del dispositivo.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Eseguire il simulatore [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) affinché sia il [Modulo di protezione hardware](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) per il dispositivo simulato. Fare clic su **Consenti l'accesso** per consentire le modifiche alle impostazioni _Windows Firewall_. che è in ascolto di un socket sulle porte 2321 e 2322. Non chiudere questa finestra. Il simulatore deve restare in esecuzione fino alla fine di questa guida di avvio rapido. 

    ```cmd/sh
    .\azure-iot-sdk-java\provisioning\provisioning-tools\tpm-simulator\Simulator.exe
    ```

    ![Simulatore TPM](./media/java-quick-create-simulated-device/simulator.png)

1. In un prompt dei comandi separato passare alla cartella radice e compilare le dipendenze dell'esempio.

    ```cmd/sh
    cd azure-iot-sdk-java
    mvn install -DskipTests=true
    ```

1. Passare alla cartella dell'esempio.

    ```cmd/sh
    cd provisioning/provisioning-samples/provisioning-tpm-sample
    ```

1. Accedere al portale di Azure, selezionare il pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio Device Provisioning. Prendere nota dell'_ambito ID_ e dell'_endpoint globale del servizio di provisioning_.

    ![Informazioni per il servizio Device Provisioning](./media/java-quick-create-simulated-device/extract-dps-endpoints.png)

1. Modificare `src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningTpmSample.java` per poter includere l'_ambito ID_ e l'_endpoint globale del servizio di provisioning_ annotati prima.  

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    ```
    Salvare il file.

1. Usare i comandi seguenti per compilare il progetto, passare alla cartella di destinazione ed eseguire il file con estensione jar creato. Sostituire il segnaposto `version` con la versione Java.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-tpm-sample-{version}-with-deps.jar
    ```

1. Inizia l'esecuzione del programma. Prendere nota della _chiave di verifica dell'autenticità_ e dell'_ID registrazione_ per la sezione successiva e lasciare il programma in esecuzione.

    ![Programma del dispositivo TPM Java](./media/java-quick-create-simulated-device/program.png)
    

## <a name="create-a-device-enrollment-entry"></a>Creare una voce per la registrazione dei dispositivi

Il servizio Device Provisioning in Azure IoT supporta due tipi di registrazione:

- [Gruppi di registrazioni](concepts-service.md#enrollment-group): usato per registrare più dispositivi correlati.
- [Registrazioni singole](concepts-service.md#individual-enrollment): usato per registrare un singolo dispositivo.

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

     ![Immettere le informazioni di registrazione del dispositivo nel pannello del portale](./media/java-quick-create-simulated-device/enterdevice-enrollment.png)  

   Dopo la corretta registrazione, l'*ID registrazione* del dispositivo viene visualizzato nell'elenco della scheda *Registrazioni singole*. 


## <a name="simulate-the-device"></a>Simulare il dispositivo

1. Nella finestra di comando che esegue il codice di esempio Java sul computer premere *INVIO* per continuare l'esecuzione dell'applicazione. Si notino i messaggi che simulano l'avvio e la connessione del dispositivo al servizio Device Provisioning per ottenere le informazioni dell'hub IoT.  

    ![Programma finale del dispositivo TPM Java](./media/java-quick-create-simulated-device/program-final.png)

1. Al termine del provisioning del dispositivo simulato nell'hub IoT collegato al servizio di provisioning, l'ID dispositivo verrà visualizzato nel pannello **Dispositivi IoT** dell'hub.

    ![Il dispositivo viene registrato con l'hub IoT](./media/java-quick-create-simulated-device/hubregistration.png) 

    Se si è modificato lo *stato iniziale del dispositivo gemello* rispetto al valore predefinito della voce di registrazione del dispositivo, è possibile eseguire il pull dello stato del dispositivo desiderato dall'hub e agire di conseguenza. Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare ed esplorare l'esempio di client dispositivo, non eseguire la pulizia delle risorse create in questa guida di avvio rapido. Se non si intende continuare, seguire questa procedura per eliminare tutte le risorse create in questa guida.

1. Chiudere la finestra di output di esempio di client del dispositivo sul computer.
1. Chiudere la finestra del simulatore TPM sul computer.
1. Nel portale di Azure selezionare **Tutte le risorse** nel menu a sinistra e quindi il servizio Device Provisioning. Aprire il pannello **Gestisci registrazioni** per il servizio e quindi selezionare la scheda **Registrazioni singole**. Selezionare la casella di controllo accanto all'*ID registrazione* del dispositivo registrato in questa guida di avvio rapido e fare clic sul pulsante **Elimina** nella parte superiore del riquadro. 
1. Nel menu a sinistra nel portale di Azure selezionare **Tutte le risorse** e quindi selezionare l'hub IoT. Aprire il pannello **Dispositivi IoT** per l'hub, selezionare la casella di controllo accanto all'*ID dispositivo* del dispositivo registrato in questa guida di avvio rapido e quindi fare clic sul pulsante **Elimina** nella parte superiore del riquadro.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un dispositivo simulato TPM nel computer e ne è stato effettuato il provisioning nell'hub IoT usando il servizio Device Provisioning in hub IoT. Per informazioni su come registrare un dispositivo TPM a livello di codice, continuare con l'argomento di avvio rapido per la registrazione a livello di codice di un dispositivo TPM. 

> [!div class="nextstepaction"]
> [Avvio rapido di Azure: Registrare un dispositivo TPM nel servizio Device Provisioning in hub IoT di Azure](quick-enroll-device-tpm-java.md)
