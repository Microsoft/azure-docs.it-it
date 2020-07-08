---
title: Eseguire il provisioning di dispositivi legacy con chiavi simmetriche-servizio Device provisioning in hub Azure
description: Come usare le chiavi simmetriche per eseguire il provisioning di dispositivi legacy con l'istanza del servizio Device provisioning (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 4d1a92f3ebf32d2270eb77ec9c79fe860ba090e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75434706"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Come effettuare il provisioning di dispositivi legacy usando chiavi simmetriche

Un problema comune a molti dispositivi legacy è che spesso hanno un'identità costituita da una singola informazione. Queste informazioni di identità sono in genere un indirizzo MAC o un numero di serie. È possibile che i dispositivi legacy non dispongano di certificato, TPM o qualsiasi altra funzionalità di sicurezza che può essere usata per identificare in modo sicuro il dispositivo. Il servizio Device Provisioning per hub IoT include l'attestazione con chiave simmetrica. L'attestazione con chiave simmetrica può essere usata per identificare un dispositivo basato su informazioni come l'indirizzo MAC o un numero di serie.

Se è possibile installare facilmente un [modulo di protezione hardware (HSM)](concepts-security.md#hardware-security-module) e un certificato, ciò può rappresentare un approccio più efficace per l'identificazione e il provisioning dei dispositivi. Poiché con questo approccio è consentito ignorare l'aggiornamento del codice distribuito in tutti i dispositivi e non viene incorporata una chiave privata nell'immagine del dispositivo.

Questo articolo presuppone che un modulo di protezione hardware o un certificato non siano opzioni valide. Tuttavia, si presuppone che si disponga di un metodo di aggiornamento del codice dispositivo al fine di usare il servizio Device Provisioning per effettuare il provisioning dei dispositivi. 

Questo articolo presuppone anche che l'aggiornamento del dispositivo venga eseguito in un ambiente sicuro per impedire accessi non autorizzati alla chiave master di gruppo o alla chiave di dispositivo derivata.

Questo articolo si riferisce a una workstation basata su Windows. Tuttavia, è possibile eseguire le procedure in Linux. Per un esempio di Linux, vedere [Come effettuare il provisioning per la multi-tenancy](how-to-provision-multitenant.md).

> [!NOTE]
> L'esempio usato in questo articolo è scritto in C. È disponibile anche un [esempio di chiave simmetrica per il provisioning dei dispositivi in C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) . Per usare questo esempio, scaricare o clonare il repository [Azure-Azure-Samples-CSharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) e seguire le istruzioni inline nel codice di esempio. È possibile seguire le istruzioni riportate in questo articolo per creare un gruppo di registrazioni con chiavi simmetriche usando il portale e per trovare l'ambito ID e le chiavi primarie e secondarie del gruppo di registrazione necessarie per eseguire l'esempio. È anche possibile creare registrazioni singole usando l'esempio.

## <a name="overview"></a>Panoramica

Verrà definito un ID di registrazione univoco per ogni dispositivo in base alle informazioni che lo identificano. Ad esempio, l'indirizzo MAC o il numero di serie.

Con il servizio Device Provisioning verrà creato un gruppo di registrazione che usa l'[attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md). Il gruppo di registrazione include una chiave master del gruppo. Tale chiave master verrà usata per eseguire l'hash di ogni ID di registrazione univoco per produrre una chiave di dispositivo univoca per ogni dispositivo. Il dispositivo userà tale chiave di dispositivo derivata con il rispettivo ID di registrazione univoco per eseguire l'attestazione con il servizio Device Provisioning e per ottenere l'assegnazione a un hub IoT.

Il codice del dispositivo illustrato in questo articolo seguirà lo stesso modello di [Avvio rapido: eseguire il provisioning di un dispositivo simulato con chiavi simmetriche](quick-create-simulated-device-symm-key.md). Il codice consente di simulare un dispositivo usando un campione dall'[SDK IoT di Azure](https://github.com/Azure/azure-iot-sdk-c). Il dispositivo simulato esegue l'attestazione con un gruppo di registrazione anziché con una registrazione singola come illustrato nella Guida introduttiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

* Completamento della guida introduttiva per [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md).

I prerequisiti seguenti si riferiscono a un ambiente di sviluppo Windows. Per Linux o macOS, vedere la sezione appropriata in [Preparare l'ambiente di sviluppo](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) nella documentazione dell'SDK.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) con il carico di lavoro ['Sviluppo di applicazioni desktop con C++'](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) abilitato. Sono supportati anche Visual Studio 2015 e Visual Studio 2017.

* La versione più recente di [Git](https://git-scm.com/download/) installata.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparare un ambiente di sviluppo per Azure IoT C SDK

In questa sezione si preparerà un ambiente di sviluppo usato per compilare [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

L'SDK include il codice di esempio per il dispositivo simulato. Il dispositivo simulato tenterà di effettuare il provisioning durante la sequenza di avvio del dispositivo.

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

5. Eseguire il comando seguente che compila una versione dell'SDK specifica per la piattaforma di sviluppo client. Verrà generata una soluzione di Visual Studio per il dispositivo simulato nella directory `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Se `cmake` non trova il compilatore C++, si potrebbero verificare errori di compilazione durante l'esecuzione del comando precedente. In tal caso, provare a eseguire questo comando nel [prompt dei comandi di Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Al termine della compilazione, le ultime righe di output saranno simili all'output seguente:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>Creare un gruppo di registrazioni con chiave simmetrica

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'istanza del servizio Device Provisioning.

2. Selezionare la scheda **Gestisci registrazioni**, quindi fare clic sul pulsante **Aggiungi gruppo di registrazioni** nella parte superiore della pagina. 

3. In **Aggiungi gruppo di registrazioni** immettere le informazioni seguenti e fare clic sul pulsante **Salva**.

   - **Nome del gruppo**: immettere **mylegacydevices**.

   - **Tipo di attestazione**: selezionare **Chiave simmetrica**.

   - **Genera chiavi automaticamente**: selezionare questa casella.

   - **Selezionare la modalità per assegnare dispositivi a hub**: selezionare **Configurazione statica** in modo che sia possibile l'assegnazione a un hub specifico.

   - **Selezionare l'hub IoT a cui questo gruppo può essere assegnato**: selezionare uno degli hub.

     ![Aggiungere il gruppo di registrazione per l'attestazione con chiave simmetrica](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Dopo aver salvato la registrazione, la **chiave primaria** e la **chiave secondaria** verranno generate e aggiunte alla voce di registrazione. Il gruppo di registrazione con chiave simmetrica viene visualizzato come **mylegacydevices**nella colonna *Nome gruppo* della scheda *Gruppi di registrazione*. 

    Aprire la registrazione e copiare il valore della **chiave primaria** generata. Questa chiave è la chiave master di gruppo.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Scegliere un ID di registrazione univoco per il dispositivo

È necessario definire un ID di registrazione univoco per identificare ogni dispositivo. È possibile usare l'indirizzo MAC, il numero di serie o informazioni univoche dal dispositivo. 

In questo esempio si usa una combinazione di indirizzo MAC e numero di serie che formano la stringa seguente per un ID di registrazione.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Creare un ID di registrazione univoco per il dispositivo. I caratteri validi sono i caratteri alfanumerici minuscoli e il trattino ("-").


## <a name="derive-a-device-key"></a>Derivare una chiave di dispositivo 

Per generare la chiave di dispositivo, usare la chiave master di gruppo per calcolare un valore [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) dell'ID di registrazione univoco per il dispositivo e convertire il risultato nel formato Base64.

Non includere la chiave master di gruppo nel codice del dispositivo.


#### <a name="linux-workstations"></a>Workstation di Linux

Se si usa una workstation di Linux, è possibile usare openssl per generare la chiave di dispositivo derivata come illustrato nell'esempio seguente.

Sostituire il valore della **CHIAVE** con la **chiave primaria** annotata in precedenza.

Sostituire il valore di **REG_ID**(ID_REG) con l'ID di registrazione.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Workstation basate su Windows

Se si usan una workstation basata su Windows, è possibile usare PowerShell per generare le chiavi di dispositivo derivate come illustrato nell'esempio seguente.

Sostituire il valore della **CHIAVE** con la **chiave primaria** annotata in precedenza.

Sostituire il valore di **REG_ID**(ID_REG) con l'ID di registrazione.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Il dispositivo userà la chiave di dispositivo derivata con l'ID registrazione univoco per eseguire l'attestazione con chiave simmetrica per il gruppo di registrazione durante il provisioning.



## <a name="create-a-device-image-to-provision"></a>Creare un'immagine del dispositivo per eseguire il provisioning

In questa sezione si aggiornerà un esempio di provisioning denominato **prov\_dev\_client\_sample** che si trova in Azure IoT C SDK precedentemente configurato. 

Questo codice di esempio simula una sequenza di avvio di dispositivo che invia la richiesta di provisioning all'istanza del servizio Device Provisioning. La sequenza di avvio consente di riconoscere il dispositivo e di assegnarlo all'hub IoT configurato nel gruppo di registrazione.

1. Nel portale di Azure selezionare la scheda **Panoramica** per il servizio Device Provisioning e prendere nota del valore di **_Ambito ID_**.

    ![Estrarre le informazioni dell'endpoint del servizio Device Provisioning dal pannello del portale](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. In Visual Studio aprire il file di soluzione **azure_iot_sdks.sln** generato in precedenza tramite l'esecuzione di CMake. Il file di soluzione deve trovarsi nel percorso seguente:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Nella finestra *Esplora soluzioni* di Visual Studio passare alla cartella **Provision\_Samples**. Espandere il progetto di esempio denominato **prov\_dev\_client\_sample**. Espandere **File di origine** e aprire **prov\_dev\_client\_sample.c**.

4. Trovare la costante `id_scope` e sostituire il valore con il valore **Ambito ID** copiato in precedenza. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Trovare la definizione per la funzione `main()` nello stesso file. Assicurarsi che la variabile`hsm_type` sia impostata su `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` come illustrato di seguito:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Individuare la chiamata a `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** che è impostata come commento.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Rimuovere il commento dalla chiamata alla funzione e sostituire i valori di segnaposto (incluse le parentesi angolari) con l'ID registrazione univoco per il dispositivo e la chiave di dispositivo derivata che è stata generata.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Salvare il file.

7. Fare clic con il pulsante destro del mouse sul progetto **prov\_dev\_client\_sample** e scegliere **Imposta come progetto di avvio**. 

8. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. Nella richiesta di ricompilare il progetto fare clic su **Sì** per ricompilare il progetto prima dell'esecuzione.

    Il seguente output è un esempio di dispositivo simulato che si avvia correttamente e si connette all'istanza del servizio di provisioning da assegnare all'hub IoT:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. Nel portale passare all'hub cose a cui è stato assegnato il dispositivo simulato e fare clic sulla scheda **dispositivi** . Al completamento del provisioning del simulato nell'hub, il relativo ID dispositivo viene visualizzato nel pannello **dispositivi** Internet, con *lo stato* **abilitato**. Potrebbe essere necessario fare clic sul pulsante **Aggiorna** nella parte superiore. 

    ![Il dispositivo viene registrato con l'hub IoT](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Problemi di sicurezza

Tenere presente che in tal modo la chiave di dispositivo derivata viene inclusa come parte dell'immagine. Ciò non rappresenta una procedura di sicurezza consigliata. Questo è uno dei motivi per cui sicurezza e facilità d'uso sono compromessi. 





## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sul nuovo provisioning, vedere [concetti relativi al provisioning dei dispositivi dell'hub](concepts-device-reprovision.md) . 
* [Guida introduttiva: eseguire il provisioning di un dispositivo simulato con chiavi simmetriche](quick-create-simulated-device-symm-key.md)
* Per ulteriori informazioni sul deprovisioning, vedere [come eseguire il deprovisioning di dispositivi precedentemente sottoposti a provisioning automatico](how-to-unprovision-devices.md) 











