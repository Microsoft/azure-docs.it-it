---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 7700f1c92aecab76dbc347814b7b161bc3d822a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67180203"
---
### <a name="to-configure-and-register-the-device"></a>Per configurare e registrare il dispositivo
1. Accedere all'interfaccia di Windows PowerShell sulla console seriale del dispositivo StorSimple. Per istruzioni, vedere [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console) . **Assicurarsi di seguire la procedura esattamente o non si sarà in grado di accedere alla console.**
2. Nella sessione visualizzata premere **invio** una volta per visualizzare un prompt dei comandi.
3. Verrà richiesto di scegliere la lingua che si desidera impostare per il dispositivo. Specificare la lingua e quindi premere **invio**.
   
    ![StorSimple configurare e registrare il dispositivo 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. Nel menu della console seriale visualizzato scegliere l'opzione 1, **Log in with full access** (Accedi con accesso completo).
   
    ![StorSimple registrare il dispositivo 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Completare i passaggi seguenti per configurare le impostazioni di rete minime richieste per il dispositivo.
   
   > [!IMPORTANT]
   > È necessario eseguire questa procedura sul controller attivo del dispositivo. Nel messaggio dell’intestazione del menu della console seriale è indicato lo stato del controller. Se non si è connessi al controller attivo, disconnettersi e quindi connettersi al controller attivo.
   
   1. Al prompt dei comandi, digitare la password. La password predefinita è **Password1**.
   2. Digitare il comando seguente:
      
        `Invoke-HcsSetupWizard`
   3. Verrà visualizzata una procedura guidata per configurare le impostazioni di rete per il dispositivo. Fornire le informazioni seguenti:
      
      * Indirizzo IP per l'interfaccia di rete DATA 0
      * Subnet mask
      * Gateway
      * Indirizzo IP per il server DNS primario
      * Indirizzo IP per il server NTP primario
      
      > [!NOTE]
      > Potrebbe essere necessario attendere alcuni minuti affinché la subnet mask e le impostazioni DNS vengano applicate.
    
   4. Facoltativamente, configurare il server proxy Web.
      
      > [!IMPORTANT]
      > Sebbene la configurazione del proxy Web sia facoltativa, tenere presente che se si utilizza un proxy Web, è possibile configurarlo solo qui. Per ulteriori informazioni, andare a [Configurare il proxy Web per il dispositivo](../articles/storsimple/storsimple-configure-web-proxy.md).
     
6. Premere Ctrl + C per uscire dalla configurazione guidata.
8. Eseguire il cmdlet seguente per scegliere il dispositivo nel portale di Microsoft Azure per enti pubblici (per impostazione predefinita viene scelto il portale di Azure classico pubblico). Entrambi i controller verranno riavviati. È consigliabile usare due sessioni PuTTY per connettersi contemporaneamente a entrambi i controller, in modo da visualizzare quando ciascun controller viene riavviato.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Verrà visualizzato un messaggio di conferma. Accettare il valore predefinito (**Y**).
9. Eseguire il cmdlet seguente per riprendere la configurazione:
   
    `Invoke-HcsSetupWizard`
   
    ![Riprendere la configurazione guidata](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. Accettare le impostazioni di rete. Dopo aver accettato ciascuna impostazione, verrà visualizzato un messaggio di convalida.
11. Per motivi di sicurezza la password di amministratore del dispositivo scade dopo la prima sessione e sarà necessario modificarla ora. Quando richiesto, fornire una password di amministratore del dispositivo. Una password di amministratore dispositivo valida deve avere una lunghezza compresa tra gli 8 e i 15 caratteri. La password deve contenere tre dei seguenti tipi di caratteri: minuscole, maiuscole, numeri e caratteri speciali.
    
    <br/>![StorSimple registrare il dispositivo 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. Il passaggio finale dell'installazione guidata registra il dispositivo nel servizio Gestione dispositivi StorSimple. A tale scopo, è necessario il codice di registrazione del servizio ottenuto in [Passaggio 2: Ottenere la chiave di registrazione del servizio](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Dopo aver fornito il codice di registrazione, potrebbe essere necessario attendere 2-3 minuti prima che il dispositivo venga registrato.
    
    > [!NOTE]
    > È possibile premere Ctrl + C in qualsiasi momento per uscire dall'installazione guidata. Se sono state immesse tutte le impostazioni di rete (indirizzo IP per Data 0, Subnet mask e Gateway), le voci verranno conservate.
    
    ![Registrazione di StorSimple in corso](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Dopo la registrazione del dispositivo, verrà visualizzato un codice di crittografia dei dati di servizio. Copiare questo codice e salvarlo in un luogo sicuro. **Questa chiave viene richiesta con la chiave di registrazione del servizio per registrare altri dispositivi con il servizio Gestione dispositivi StorSimple.** Per ulteriori informazioni sul codice, consultare [Sicurezza di StorSimple](../articles/storsimple/storsimple-8000-security.md) .
    
    ![StorSimple registrare il dispositivo 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > Per copiare il testo dalla finestra della console seriale, è sufficiente selezionarlo. È quindi possibile incollarlo negli Appunti o in qualsiasi editor di testo.
    > 
    > NON usare **CTRL+C** per copiare la chiave di crittografia dei dati del servizio. Se si usa **CTRL+C** l'installazione guidata verrà chiusa. Di conseguenza, la password di amministratore del dispositivo non verrà modificata e verrà ripristinata la password predefinita del dispositivo.
    
14. Uscire dalla console seriale.
15. Tornare al portale per enti pubblici di Azure e completare la seguente procedura:
    
    1. Passare al servizio Gestione dispositivi StorSimple.
    2. Fare clic su **Dispositivi**. Nell'elenco di dispositivi identificare il dispositivo da distribuire. Verificare che il dispositivo sia connesso correttamente al servizio controllando lo stato. Lo stato del dispositivo deve essere **Online**.
            
        Se lo stato del dispositivo è **Offline**, attendere un paio di minuti prima che il dispositivo passi in modalità online.
       
        Se il dispositivo è ancora offline dopo alcuni minuti, è necessario assicurarsi che la rete firewall sia stata configurata come descritto nei [requisiti di rete per il dispositivo StorSimple](../articles/storsimple/storsimple-8000-system-requirements.md).
       
        Verificare che la porta 9354 sia aperta per le comunicazioni in uscita, perché viene usata dal bus di servizio per le comunicazioni da servizio a dispositivo di Gestione dispositivi StorSimple.

