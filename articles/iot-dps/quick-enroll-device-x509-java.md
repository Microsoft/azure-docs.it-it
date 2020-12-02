---
title: 'Avvio rapido: Registrare dispositivi X.509 nel servizio Device Provisioning di Azure con Java'
description: Questa guida introduttiva usa sia registrazioni di gruppo che registrazioni singole. In questa guida di avvio rapido verrà eseguita la registrazione dei dispositivi X.509 nel servizio Device Provisioning in hub IoT di Azure con Java.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 8591220eb80d9c60ed7873be546007449ab46f89
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463038"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-java"></a>Guida introduttiva: Registrare i dispositivi X.509 nel servizio Device Provisioning con Java

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Questa guida di avvio rapido descrive come usare Java per registrare a livello di codice un gruppo di dispositivi simulati X.509 nel servizio Device Provisioning in hub IoT di Azure. I dispositivi vengono registrati in un'istanza del servizio di provisioning creando un gruppo di registrazione o una registrazione singola. Questa guida di avvio rapido descrive come creare entrambi i tipi di registrazioni usando Java Service SDK e un'applicazione Java di esempio.

## <a name="prerequisites"></a>Prerequisiti

- Completamento dell'esercitazione [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md).
- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support). Questa guida di avvio rapido installa [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/). Funziona sia in Windows che in Linux. Questa guida di avvio rapido usa Windows.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Scaricare e modificare il codice di esempio Java

In questa sezione viene usato un certificato X.509 autofirmato. È importante tenere presente quanto segue:

* I certificati autofirmati sono destinati solo alle operazioni di testing e non dovrebbero essere usati nell'ambiente di produzione.
* La data di scadenza predefinita per un certificato autofirmato è un anno.

La procedura seguente illustra come aggiungere i dettagli del provisioning del dispositivo X.509 al codice di esempio. 

1. Aprire un prompt dei comandi. Clonare il repository GitHub per l'esempio di codice di registrazione del dispositivo usando [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/):
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. Nel codice sorgente scaricato passare alla cartella dell'esempio **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** . Aprire il file **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** nell'editor preferito e aggiungere i dettagli seguenti:

    1. Aggiungere `[Provisioning Connection String]` per il servizio di provisioning dal portale, come indicato di seguito:
        1. Passare al servizio di provisioning nel [portale di Azure](https://portal.azure.com). 
        2. Aprire **Criteri di accesso condivisi** e selezionare un criterio con l'autorizzazione *EnrollmentWrite*.
        3. Copiare la **stringa di connessione chiave primaria**. 

            ![Ottenere la stringa di connessione per il provisioning dal portale](./media/quick-enroll-device-x509-java/provisioning-string.png)  

        4. Nel file del codice di esempio **_ServiceEnrollmentGroupSample.java_** sostituire `[Provisioning Connection String]` con la **stringa di connessione chiave primaria**.

            ```Java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    2. Aggiungere il certificato radice per il gruppo di dispositivi. Se è necessario un certificato radice di esempio, usare lo strumento _generatore di certificati X.509_ come segue:
        1. In una finestra di comando passare alla cartella **_azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator_** .
        2. Compilare lo strumento con questo comando:

            ```cmd\sh
            mvn clean install
            ```

        4. Eseguire lo strumento con questi comandi:

            ```cmd\sh
            cd target
            java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
            ```

        5. Quando richiesto, è facoltativamente possibile immettere un _nome comune_ per i certificati.
        6. Lo strumento genera in locale un **certificato client**, la **chiave privata del certificato client** e il **certificato radice**.
        7. Copiare il **certificato radice**, incluse le righe **_-----BEGIN CERTIFICATE-----_** ed **_-----END CERTIFICATE-----_** . 
        8. Assegnare il valore del **certificato radice** al parametro **PUBLIC_KEY_CERTIFICATE_STRING**, come illustrato sotto:

            ```Java
            private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
            ```

        9. Chiudere la finestra di comando o immettere **n** quando richiesto per *Codice di verifica*. 
 
    3. È facoltativamente possibile configurare il servizio di provisioning tramite il codice di esempio:
        - Per aggiungere questa configurazione all'esempio, seguire questa procedura:
            1. Passare all'hub IoT collegato al servizio di provisioning nel [portale di Azure](https://portal.azure.com). Aprire la scheda **Panoramica** dell'hub e copiare il **nome host**. Assegnare questo **nome host** al parametro *IOTHUB_HOST_NAME*.

                ```Java
                private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
                ```
            2. Assegnare un nome descrittivo al parametro *DEVICE_ID* e mantenere per *PROVISIONING_STATUS* il valore predefinito *ENABLED*. 

        - OPPURE, se si sceglie di non configurare il servizio di provisioning, assicurarsi di impostare come commento o eliminare le istruzioni seguenti nel file _ServiceEnrollmentGroupSample.java_:

            ```Java
            enrollmentGroup.setIotHubHostName(IOTHUB_HOST_NAME);                // Optional parameter.
            enrollmentGroup.setProvisioningStatus(ProvisioningStatus.ENABLED);  // Optional parameter.
            ```

    4. Esaminare il codice di esempio, che crea, aggiorna, esegue query ed elimina la registrazione di un gruppo per i dispositivi X.509. Per verificare che la registrazione sia stata eseguita correttamente nel portale, impostare temporaneamente come commento le righe di codice seguenti alla fine del file _ServiceEnrollmentGroupSample.java_:

        ```Java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    5. Salvare il file _ServiceEnrollmentGroupSample.java_. 
 

<a id="runjavasample"></a>

## <a name="build-and-run-sample-group-enrollment"></a>Compilare ed eseguire la registrazione del gruppo di esempio

Il servizio Device Provisioning in Azure IoT supporta due tipi di registrazione:

- [Gruppi di registrazioni](concepts-service.md#enrollment-group): Usato per registrare più dispositivi correlati.
- [Registrazioni singole](concepts-service.md#individual-enrollment): Usato per registrare un singolo dispositivo.

Questa procedura usa un gruppo di registrazioni. Nella sezione successiva viene usata una registrazione singola.

1. Aprire una finestra di comando e passare alla cartella **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** .

2. Compilare il codice di esempio usando questo comando:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Questo comando scarica il pacchetto Maven [`com.microsoft.azure.sdk.iot.provisioning.service`](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) nel computer. Questo pacchetto include i file binari per Java Service SDK, necessari al codice di esempio per la compilazione. Se si è eseguito lo strumento _generatore di certificati X.509_ nella sezione precedente, questo pacchetto sarà già scaricato nel computer. 

3. Eseguire l'esempio usando questi comandi nella finestra di comando:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. Verificare nella finestra di output che la registrazione sia stata eseguita correttamente.

5. Passare al servizio di provisioning nel portale di Azure. Fare clic su **Gestisci registrazioni**. Si noti che il gruppo di dispositivi X.509 viene visualizzato nella scheda **Gruppi di registrazioni** con un *NOME GRUPPO* generato automaticamente. 

    ![Verificare nel portale che la registrazione X.509 sia riuscita](./media/quick-enroll-device-x509-java/verify-x509-enrollment.png)  

## <a name="modifications-to-enroll-a-single-x509-device"></a>Modifiche per registrare un singolo dispositivo X.509

Per registrare un solo dispositivo X.509, modificare il codice di esempio della *registrazione singola* usato in [Enroll TPM device to IoT Hub Device Provisioning Service using Java service SDK](quick-enroll-device-tpm-java.md#javasample) (Registrare un dispositivo TPM nel servizio Device Provisioning in hub IoT usando Java Service SDK), come segue:

1. Copiare il *nome comune* del certificato client X.509 negli Appunti. Per usare lo strumento _generatore di certificati X.509_, come illustrato nella [sezione del codice di esempio precedente](#javasample), immettere un _nome comune_ per il certificato o usare quello predefinito **microsoftriotcore**. Usare questo **nome comune** come valore per la variabile *REGISTRATION_ID*. 

    ```Java
    // Use common name of your X.509 client certificate
    private static final String REGISTRATION_ID = "[RegistrationId]";
    ```

2. Rinominare la variabile *TPM_ENDORSEMENT_KEY* come *PUBLIC_KEY_CERTIFICATE_STRING*. Copiare il proprio certificato client o il **certificato client** dell'output dello strumento _generatore di certificati X.509_ come valore per la variabile *PUBLIC_KEY_CERTIFICATE_STRING*. 

    ```Java
    // Rename the variable *TPM_ENDORSEMENT_KEY* as *PUBLIC_KEY_CERTIFICATE_STRING*
    private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
    ```
3. Nella funzione **main** sostituire la riga `Attestation attestation = new TpmAttestation(TPM_ENDORSEMENT_KEY);` con il codice seguente per usare il certificato client X.509:
    ```Java
    Attestation attestation = X509Attestation.createFromClientCertificates(PUBLIC_KEY_CERTIFICATE_STRING);
    ```

4. Salvare, compilare ed eseguire il file di esempio della *registrazione singola*, seguendo i passaggi illustrati nella sezione [Build and run the sample code for individual enrollment](quick-enroll-device-tpm-java.md#runjavasample) (Compilare ed eseguire il codice di esempio per la registrazione singola).


## <a name="clean-up-resources"></a>Pulire le risorse
Se si prevede di esplorare l'esempio di servizio Java, non eseguire la pulizia delle risorse create in questa guida di avvio rapido. Se non si intende continuare, seguire questa procedura per eliminare tutte le risorse create in questa guida.

1. Chiudere la finestra di output di esempio di Java sul computer.
1. Chiudere la finestra del _generatore di certificati X509_  sul computer.
1. Passare al servizio Device Provisioning nel portale di Azure, selezionare **Gestisci registrazioni** e quindi la scheda **Gruppi di registrazioni**. Selezionare la casella di controllo accanto a *NOME GRUPPO* per i dispositivi X.509 registrati con questa guida di avvio rapido e fare clic sul pulsante **Elimina** nella parte superiore del riquadro.  

## <a name="next-steps"></a>Passaggi successivi
In questa guida di avvio rapido si è registrato un gruppo simulato di dispositivi X.509 nel servizio Device Provisioning. Per informazioni approfondite sul provisioning del dispositivo, passare all'esercitazione sulla configurazione del servizio Device Provisioning nel portale di Azure. 

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials (Esercitazioni sul servizio Device Provisioning in hub IoT di Azure)](./tutorial-set-up-cloud.md)