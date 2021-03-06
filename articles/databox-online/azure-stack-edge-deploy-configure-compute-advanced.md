---
title: Esercitazione su come filtrare e analizzare i dati per la distribuzione avanzata con il calcolo in Azure Stack Edge Pro | Microsoft Docs
description: Informazioni su come configurare il ruolo di calcolo in Azure Stack Edge Pro e usarlo per trasformare i dati per il flusso di distribuzione avanzata prima dell'invio ad Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: alkohli
ms.openlocfilehash: 81953f573c47d229fcaccd7c11f62155acd2f119
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063645"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro-for-advanced-deployment-flow"></a>Esercitazione: Trasformare i dati con Azure Stack Edge Pro per il flusso di distribuzione avanzata

Questa esercitazione illustra come configurare un ruolo di calcolo per il flusso di distribuzione avanzata nel dispositivo Azure Stack Edge Pro. Dopo la configurazione del ruolo di calcolo, Azure Stack Edge Pro può trasformare i dati prima che vengano inviati ad Azure.

Il calcolo può essere configurato per un flusso di distribuzione semplice o avanzata sul dispositivo.

| Criteri | Distribuzione semplice                                | Distribuzione avanzata                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Destinatari     | Amministratori IT                                | Sviluppatori                            |
| Type             | Usare il servizio Azure Stack Edge per distribuire moduli      | Usare il servizio hub IoT per distribuire moduli |
| Moduli distribuiti | Single                                           | Moduli concatenati o multipli           |


Il completamento di questa procedura può richiedere circa 20-30 minuti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare il calcolo
> * Aggiungere condivisioni
> * Aggiungere un trigger
> * Aggiungere un ruolo di calcolo
> * Verificare la trasformazione e il trasferimento dei dati

 
## <a name="prerequisites"></a>Prerequisiti

Prima di configurare un ruolo di calcolo nel dispositivo Azure Stack Edge Pro, assicurarsi di:

- Aver attivato il dispositivo Azure Stack Edge Pro come descritto in [Connettere, configurare e attivare Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configurare il calcolo

Per configurare il calcolo in Azure Stack Edge Pro, creare una risorsa hub IoT.

1. Nel portale di Azure della risorsa Azure Stack Edge passare a **Panoramica**. Nel riquadro destro selezionare il riquadro **IoT Edge**.

    ![Introduzione al calcolo](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Nel riquadro **Abilita servizio IoT Edge** selezionare **Aggiungi**. Viene abilitato il servizio IoT Edge, che consente di distribuire i moduli IoT Edge in locale nel dispositivo.

    ![Introduzione al calcolo 2](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. In **Crea servizio IoT Edge** immettere quanto segue:

   
    |Campo  |Valore  |
    |---------|---------|
    |Subscription     |Selezionare una sottoscrizione per la risorsa hub IoT. È possibile selezionare la stessa sottoscrizione usata dalla risorsa Azure Stack Edge.        |
    |Gruppo di risorse     |Immettere un nome per il gruppo di risorse della risorsa hub IoT. È possibile selezionare lo stesso gruppo di risorse usato dalla risorsa Azure Stack Edge.         |
    |Hub IoT     | Scegliere **Nuovo** o **Esistente**. <br> Per impostazione predefinita, per creare una risorsa IoT viene usato un livello Standard (S1). Per usare una risorsa IoT di un livello gratuito, crearne uno e quindi selezionare la risorsa esistente.      |
    |Nome     |Accettare il nome predefinito o immetterne un altro per la risorsa hub IoT.         |

    ![Introduzione al calcolo 3](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Selezionare **Rivedi e crea**. La creazione della risorsa hub IoT richiede qualche minuto. Una volta creata la risorsa hub IoT, la pagina **Panoramica** viene aggiornata per indicare che il servizio IoT Edge è in esecuzione. 

    Quando il servizio IoT Edge è configurato nel dispositivo Edge, crea due dispositivi: un dispositivo IoT e un dispositivo IoT Edge. Entrambi i dispositivi possono essere visualizzati nella risorsa dell'hub IoT. Nel dispositivo IoT Edge viene eseguito anche un runtime IoT Edge. Attualmente per il dispositivo IoT Edge è disponibile solo la piattaforma Linux.

    Per verificare che il ruolo di calcolo Edge sia stato configurato, selezionare **Servizio IoT Edge > Proprietà** e visualizzare il dispositivo IoT e il dispositivo IoT Edge. 

    ![Introduzione al calcolo 4](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-4.png)
    

## <a name="add-shares"></a>Aggiungere condivisioni

Per la distribuzione avanzata di questa esercitazione, saranno necessarie due condivisioni: una condivisione Edge e un'altra condivisione locale Edge.

1. Aggiungere una condivisione Edge nel dispositivo seguendo questa procedura:

    1. Nella risorsa Azure Stack Edge passare a **IoT Edge > Condivisioni**.
    2. Sulla barra dei comandi della pagina **Condivisioni** selezionare **+ Aggiungi condivisione**.
    3. Nel pannello **Aggiungi condivisione** specificare il nome della condivisione e selezionarne il tipo.
    4. Per montare la condivisione Edge, selezionare la casella di controllo **Usa la condivisione con il calcolo Edge**.
    5. Selezionare **Account di archiviazione**, **Servizio di archiviazione**, un utente esistente e quindi **Crea**.

        ![Aggiungere una condivisione Edge](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    Dopo la creazione della condivisione Edge, si riceverà una notifica di creazione riuscita. L'elenco condivisione viene aggiornato per riflettere la nuova condivisione.

2. Aggiungere una condivisione locale Edge nel dispositivo Edge ripetendo tutti i passaggi della procedura precedente e selezionando la casella di controllo **Configura come condivisione locale Edge**. I dati nella condivisione locale rimangono nel dispositivo.

    ![Aggiungere una condivisione locale Edge](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Nel pannello **Condivisioni** viene visualizzato l'elenco aggiornato delle condivisioni.

    ![Elenco aggiornato delle condivisioni](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Per visualizzare le proprietà della nuova condivisione locale creata, selezionarla dall'elenco. Nella casella **Punto di montaggio locale per i moduli di calcolo Edge** copiare il valore corrispondente a questa condivisione.

    Questo punto di montaggio locale verrà usato durante la distribuzione del modulo.

    ![Casella "Punto di montaggio locale per i moduli di calcolo Edge"](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Per visualizzare le proprietà della condivisione Edge creata, selezionarla dall'elenco. Nella casella **Punto di montaggio locale per i moduli di calcolo Edge** copiare il valore corrispondente a questa condivisione.

    Questo punto di montaggio locale verrà usato durante la distribuzione del modulo.

    ![Aggiungere un modulo personalizzato](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Aggiungere un trigger

1. Passare alla risorsa Azure Stack Edge e quindi a **IoT Edge > Trigger**. Selezionare **+ Aggiungi trigger**.

    ![Aggiunta di trigger](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Nel pannello **Aggiungi trigger** immettere i valori seguenti.

    |Campo  |valore  |
    |---------|---------|
    |Nome trigger     | Nome univoco per il trigger.         |
    |Tipo di trigger     | Selezionare il trigger di **File**. Un trigger di file viene attivato ogni volta che si verifica un evento file, ad esempio la scrittura di un file nella condivisione di input. Un trigger pianificato invece, viene attivato in base a una pianificazione definita dall'utente. Per questo esempio è necessario un trigger di file.    |
    |Condivisione di input     | Selezionare una condivisione di input. In questo caso, la condivisione di input è la condivisione locale Edge. Il modulo usato in questo esempio sposta i file dalla condivisione locale Edge a una condivisione Edge in cui vengono caricati nel cloud.        |

    ![Aggiunta di trigger 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. L'utente riceverà una notifica al termine della creazione del trigger. L'elenco dei trigger viene aggiornato per visualizzare il trigger appena creato. Selezionare il trigger appena creato.

    ![Aggiunta di trigger 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Copiare e salvare la route di esempio. Si modificherà e si userà questa route di esempio in un secondo momento nell'hub IoT.

    `"sampleroute&quot;: &quot;FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Aggiunta di trigger 4](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Aggiungere un modulo

In questo dispositivo Edge non sono presenti moduli personalizzati. È possibile aggiungere un modulo personalizzato o predefinito. Per informazioni su come creare un modulo personalizzato, vedere [Sviluppare un modulo C# per il dispositivo Azure Stack Edge Pro](azure-stack-edge-create-iot-edge-module.md).

In questa sezione si aggiungerà un modulo personalizzato al dispositivo IoT Edge creato in [Sviluppare un modulo C# per il dispositivo Azure Stack Edge Pro](azure-stack-edge-create-iot-edge-module.md). Questo modulo personalizzato sposta i file da una condivisione locale Edge nel dispositivo Edge a una condivisione cloud Edge nel dispositivo. La condivisione cloud esegue quindi il push dei file all'account di archiviazione di Azure associato.

1. Passare alla risorsa Azure Stack Edge e quindi a **IoT Edge > Panoramica**. Nel riquadro **Moduli** selezionare **Passa all'hub IoT di Azure**.

    ![Selezionare distribuzione avanzata](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. Nella risorsa hub IoT passare a **Dispositivo IoT Edge** e selezionare il dispositivo IoT Edge.

    ![Passare a Dispositivo IoT Edge nell'hub IoT](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-2.png)

3. In **Dettagli dispositivo**, selezionare **Imposta i moduli**.

    ![Collegamento Imposta moduli](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-3.png)

4. In **Aggiungi moduli** eseguire queste operazioni:

    1. Immettere il nome, l'indirizzo, il nome utente e la password per le impostazioni del registro contenitori per il modulo personalizzato.
    Il nome, l'indirizzo e le credenziali elencate vengono usati per recuperare i moduli con un URL corrispondente. Per distribuire questo modulo, in **Moduli di distribuzione**, selezionare **Modulo IoT Edge**. Questo modulo IoT Edge è un contenitore Docker che è possibile distribuire nel dispositivo IoT Edge associato al proprio dispositivo Azure Stack Edge Pro.

        ![Pagina Imposta moduli](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Specificare le impostazioni per il modulo personalizzato IoT Edge. Immettere i valori seguenti.
     
        |Campo  |Valore  |
        |---------|---------|
        |Nome     | Nome univoco per il modulo. Questo modulo è un contenitore Docker che può essere distribuito nel dispositivo IoT Edge associato ad Azure Stack Edge Pro.        |
        |URI immagine     | URI dell'immagine del contenitore corrispondente per il modulo.        |
        |Sono richieste credenziali     | Se questa opzione è selezionata, per recuperare i moduli con un URL corrispondente vengono usati un nome utente e una password.        |
    
        Nella casella **Opzioni di creazione del contenitore** immettere i punti di montaggio locali per i moduli Edge copiati nei passaggi precedenti per la condivisione Edge e per la condivisione locale Edge.

        > [!IMPORTANT]
        > I percorsi usati qui sono montati nel contenitore, quindi devono corrispondere a ciò che la funzionalità nel contenitore si aspetta. Se si sta seguendo [Create a custom module](azure-stack-edge-create-iot-edge-module.md#update-the-module-with-custom-code) (Creare un modulo personalizzato), il codice specificato in tale modulo si aspetta i percorsi copiati. Non modificare questi percorsi.
    
        Nella casella **Opzioni di creazione del contenitore** è possibile incollare l'esempio seguente:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Specificare eventuali variabili di ambiente usate per il modulo. Le variabili di ambiente offrono informazioni utili per definire l'ambiente in cui il modulo viene eseguito.

        ![Casella Opzioni di creazione del contenitore](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Se necessario, configurare le impostazioni avanzate del runtime di Edge e quindi fare clic su **Avanti**.

        ![Aggiungere un modulo personalizzato 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5. In **Specifica route** impostare le route tra i moduli.  
   
   ![Specifica route](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-7.png)

    È possibile sostituire *route* con questa stringa di route copiata in precedenza. In questo esempio immettere il nome della condivisione locale che eseguirà il push dei dati nella condivisione cloud. Sostituire `modulename` con il nome del modulo. Selezionare **Avanti**.
        
    ```
    "route&quot;: &quot;FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Sezione Specifica route](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-8.png)

6. In **Rivedi distribuzione** controllare tutte le impostazioni e quindi selezionare **Invia** per inviare il modulo per la distribuzione.

   ![Pagina Imposta moduli 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Questa azione avvia la distribuzione del modulo. Al termine della distribuzione, lo **Stato di runtime** del modulo risulta in **esecuzione**.

    ![Aggiungere un modulo personalizzato 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Verificare la trasformazione, il trasferimento dei dati

Il passaggio finale consiste nell'assicurarsi che il modulo sia connesso e in esecuzione come previsto. Lo stato di runtime del modulo deve essere In esecuzione per il dispositivo IoT Edge nella risorsa dell'hub IoT.

Eseguire i passaggi seguenti per verificare la trasformazione e il trasferimento dei dati ad Azure.
 
1. In Esplora file connettersi alla condivisione locale Edge e alla condivisione Edge create in precedenza.

   ![Verificare la trasformazione dei dati](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1. Aggiungere i dati alla condivisione locale.

   ![Verificare la trasformazione dei dati 2](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    I dati vengono spostati nella condivisione cloud.

    ![Verificare la trasformazione dei dati 3](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Viene quindi eseguito il push dei dati dalla condivisione cloud all'account di archiviazione. Per visualizzare i dati, passare all'account di archiviazione e quindi selezionare **Azure Storage Explorer**. È possibile visualizzare i dati caricati nell'account di archiviazione.

    ![Verificare la trasformazione dei dati 4](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Il processo di convalida è stato completato.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Configurare il calcolo
> * Aggiungere condivisioni
> * Aggiungere un trigger
> * Aggiungere un ruolo di calcolo
> * Verificare la trasformazione e il trasferimento dei dati

Per informazioni su come amministrare il dispositivo Azure Stack Edge Pro, vedere:

> [!div class="nextstepaction"]
> [Usare l'interfaccia utente Web locale per amministrare un dispositivo Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
