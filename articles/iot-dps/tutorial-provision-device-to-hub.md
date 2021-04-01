---
title: 'Esercitazione: Effettuare il provisioning di un dispositivo con il servizio Device Provisioning in hub IoT di Azure'
description: Questa esercitazione illustra come effettuare il provisioning del dispositivo in un singolo hub IoT con il servizio Device Provisioning in hub IoT di Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 876fd8260b64fba4d3d34a766b4259323c660b76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968078"
---
# <a name="tutorial-provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Esercitazione: Eseguire il provisioning del dispositivo in un hub IoT con il servizio Device Provisioning in hub IoT di Azure

Nell'esercitazione precedente abbiamo appreso come configurare un dispositivo per la connessione al servizio Device Provisioning. In questa esercitazione si apprenderà come usare questo servizio per eseguire il provisioning del dispositivo in un singolo hub IoT usando il provisioning automatico e gli **_elenchi di registrazione_** . Questa esercitazione illustra come:

> [!div class="checklist"]
> * Registrare il dispositivo
> * Avviare il dispositivo
> * Verificare che il dispositivo sia registrato

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere, assicurarsi di configurare il dispositivo come descritto nell'esercitazione [Configurare un dispositivo per il provisioning usando il servizio Device Provisioning in hub IoT di Azure](./tutorial-set-up-device.md).

Se non si ha familiarità con il processo di provisioning automatico, vedere la panoramica sul [provisioning](about-iot-dps.md#provisioning-process) prima di continuare.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Registrare il dispositivo

Questo passaggio prevede l'aggiunta di elementi di sicurezza esclusivi del dispositivo al servizio Device Provisioning. Questi elementi di sicurezza si basano sul [Meccanismo di attestazione](concepts-service.md#attestation-mechanism) del dispositivo, come indicato di seguito:

- Per i dispositivi basati su TPM sono necessari:
    - *Chiave di verifica dell'autenticità*, che è univoca per ogni chip TPM o simulazione e che deve essere richiesta al produttore del chip TPM.  Per altre informazioni leggere [Informazioni sulla chiave di verifica dell'autenticità del TPM](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11)).
    - *ID di registrazione*, usato per identificare in modo univoco un dispositivo nell'ambito o nello spazio dei nomi. L'ID può coincidere o meno con l'ID dispositivo. L'ID è obbligatorio per ogni dispositivo. Per i dispositivi basati su TPM, l'ID di registrazione può essere derivato dal TPM stesso, ad esempio un hash SHA-256 della chiave di verifica dell'autenticità del TPM.

      [![Informazioni di registrazione per TPM nel portale](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- Per i dispositivi basati su X.509 sono necessari:
    - [Certificato emesso nel chip X.509](/windows/win32/seccertenroll/about-x-509-public-key-certificates) o nella simulazione, sotto forma di file con estensione *pem* o *cer*. Per la registrazione individuale è necessario usare il *certificato firmato* per dispositivo per il sistema X.509, mentre per i gruppi di registrazione è necessario usare il *certificato radice*. 

      [![Aggiungere una registrazione singola per l'attestazione X.509 nel portale](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

È possibile registrare il dispositivo nel servizio Device Provisioning in due modi:

- **Enrollment Groups** (Gruppi di registrazione) Rappresenta un gruppo di dispositivi che condividono un meccanismo di attestazione specifico. È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant. Per altre informazioni sull'attestazione dell'identità per i gruppi di registrazione, vedere [Sicurezza](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

    [![Aggiungere una registrazione di gruppo per l'attestazione X.509 nel portale](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **Individual Enrollments** (Registrazioni individuali) Rappresenta una voce per un singolo dispositivo che può eseguire la registrazione al servizio Device Provisioning. Le registrazioni individuali possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM reale o virtuale) come meccanismo di attestazione. È consigliabile usare le registrazioni individuali per i dispositivi che richiedono configurazioni iniziali univoche e per i dispositivi che possono usare solo token di firma di accesso condiviso tramite TPM o TPM virtuale come meccanismo di attestazione. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

Ora si registra il dispositivo con l'istanza del servizio Device Provisioning, usando gli elementi di sicurezza necessari in base al meccanismo di attestazione del dispositivo: 

1. Accedere al portale di Azure, fare clic sul pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio Device Provisioning.

2. Nel pannello di riepilogo del servizio Device Provisioning selezionare **Manage enrollments** (Gestisci registrazioni). Selezionare la scheda **Individual Enrollments** (Registrazioni individuali) o **Enrollment Groups** (Gruppi di registrazione) in base alla configurazione del dispositivo. Fare clic sul pulsante **Aggiungi** in alto. Selezionare **TPM** o **X.509** come *meccanismo* di attestazione dell'identità e immettere gli elementi di sicurezza appropriati come descritto in precedenza. È possibile immettere un nuovo **ID di dispositivo hub IoT**. Al termine, fare clic sul pulsante **Save** (Salva). 

3. Quando la registrazione è completata, il dispositivo verrà visualizzato nel portale come riportato di seguito:

    ![Successful TPM enrollment in the portal (Registrazione TPM nel portale riuscita)](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

Dopo la registrazione, il servizio di provisioning attende che il dispositivo venga avviato per potersi connettere successivamente. Quando il dispositivo viene avviato per la prima volta, la libreria SDK del client interagisce con il chip per estrarre gli elementi di sicurezza dal dispositivo e verifica la registrazione con il servizio Device Provisioning. 

## <a name="start-the-iot-device"></a>Avviare il dispositivo IoT

Il dispositivo IoT può essere un dispositivo reale o un dispositivo simulato. Dato che il dispositivo IoT a questo punto è stato registrato in un'istanza del servizio Device Provisioning, il dispositivo può ora essere avviato e chiamare il servizio di provisioning per ottenere il riconoscimento tramite il meccanismo di attestazione. Dopo il riconoscimento nel servizio di provisioning, il dispositivo verrà assegnato a un hub IoT. 

Sono inclusi esempi di dispositivo simulato, che usano sia l'attestazione TPM che X.509, per C, Java, C#, Node.js e Python. Ad esempio, un dispositivo simulato che usa TPM e [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) seguirà il processo descritto nella sezione [Simulare la sequenza del primo avvio per il dispositivo](quick-create-simulated-device.md#simulate-first-boot-sequence-for-the-device). Lo stesso dispositivo con l'attestazione con certificato X.509 farebbe riferimento a questa sezione per la [sequenza di avvio](quick-create-simulated-device-x509.md#simulate-first-boot-sequence-for-the-device).

Vedere la [guida pratica per MXChip Iot DevKit](how-to-connect-mxchip-iot-devkit.md) come esempio per un dispositivo reale.

Avviare il dispositivo per consentire all'applicazione client del dispositivo di eseguire la registrazione nel servizio Device Provisioning.  

## <a name="verify-the-device-is-registered"></a>Verificare che il dispositivo sia registrato

In seguito all'avvio del dispositivo dovrebbero verificarsi le azioni seguenti:

1. Il dispositivo invia una richiesta di registrazione al servizio Device Provisioning.
2. Per i dispositivi TPM il servizio Device Provisioning invia di nuovo una richiesta di registrazione a cui risponde il dispositivo. 
3. Al termine della registrazione il servizio Device Provisioning invia l'URI dell'hub IoT, l'ID dispositivo e la chiave crittografata di nuovo al dispositivo. 
4. L'applicazione client dell'hub IoT nel dispositivo si connette quindi all'hub. 
5. Se la connessione all'hub riesce, il dispositivo dovrebbe essere visualizzato nel riquadro di esplorazione **Dispositivi IoT** dell'hub IoT. 

    ![Successful connection to hub in the portal (Connessione all'hub nel portale riuscita)](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

Per altre informazioni, vedere l'esempio di client di provisioning del dispositivo [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c). Nell'esempio viene illustrato il provisioning di un dispositivo simulato tramite certificati TPM e X.509 e chiavi simmetriche. Fare riferimento alle guide introduttive sull'attestazione [TPM](./quick-create-simulated-device.md), [X.509](./quick-create-simulated-device-x509.md) e [Chiave simmetrica](./quick-create-simulated-device-symm-key.md) per istruzioni dettagliate sull'uso dell'esempio.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Registrare il dispositivo
> * Avviare il dispositivo
> * Verificare che il dispositivo sia registrato

Passare all'esercitazione successiva per informazioni su come eseguire il provisioning di più dispositivi tra hub con bilanciamento del carico. 

> [!div class="nextstepaction"]
> [Eseguire il provisioning dei dispositivi in più hub IoT con bilanciamento del carico](./tutorial-provision-multiple-hubs.md)