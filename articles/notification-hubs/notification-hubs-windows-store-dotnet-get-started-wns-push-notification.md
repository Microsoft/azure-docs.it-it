---
title: Inviare notifiche alle app della piattaforma UWP (Universal Windows Platform) con Hub di notifica di Azure | Microsoft Docs
description: Informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione della piattaforma UWP (Universal Windows Platform).
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: 'mvc, ms.custom: devx-track-csharp'
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 07a0581cd7fe2e7a9c13f860c862e34da3cfd1ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998289"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-using-azure-notification-hubs"></a>Esercitazione: Inviare notifiche alle app della piattaforma UWP (Universal Windows Platform) con Hub di notifica di Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Questa esercitazione illustra come creare un hub di notifica per inviare notifiche push a un'app della piattaforma UWP (Universal Windows Platform). Si crea un'app di Windows Store vuota che riceve notifiche push tramite Windows Push Notification Service (WNS). Sarà quindi possibile usare l'hub di notifica per trasmettere notifiche push a tutti i dispositivi che eseguono l'app.

> [!NOTE]
> Il codice completo per questa esercitazione è disponibile in [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

Seguire questa procedura:

> [!div class="checklist"]
> * Creare un'app in Windows Store
> * Creare un hub di notifica
> * Creare un'app Windows di esempio
> * Inviare notifiche di prova

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, [creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Microsoft Visual Studio 2017 o versioni successive. Nell'esempio di questa esercitazione viene usato [Visual Studio 2019](https://www.visualstudio.com/products).
- [Strumenti per lo sviluppo di app UWP installati](/windows/uwp/get-started/get-set-up)
- Account di Windows Store attivo
- Verificare che l'impostazione **Ottieni notifiche delle app e da altri mittenti** sia abilitata. 
    - Aprire la finestra **Impostazioni** sul computer.
    - Selezionare il riquadro **Sistema**.
    - Selezionare **Notifiche e azioni** dal menu a sinistra. 
    - Verificare che l'impostazione **Ottieni notifiche delle app e da altri mittenti** sia abilitata. Se non lo è, abilitarla.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni relative a Hub di notifica per le app UWP.

## <a name="create-an-app-in-windows-store"></a>Creare un'app in Windows Store

Per inviare notifiche push ad app UWP, associare l'app a Windows Store. Configurare quindi l'hub di notifica per l'integrazione con WNS.

1. Passare a [Windows Dev Center](https://partner.microsoft.com/dashboard/windows/first-run-experience), accedere con il proprio account Microsoft e quindi selezionare **Create a new app** (Crea una nuova app).

    ![Pulsante New app (Nuova app)](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Digitare un nome per l'app e quindi selezionare **Reserve product name** (Riserva nome del prodotto). Verrà così creata una nuova registrazione a Windows Store per l'app.

    ![Archiviare il nome app](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Espandere **Product management** (Gestione prodotti), selezionare **WNS/MPNS**, quindi selezionare il **sito Live Services**. Effettuare l'accesso all'account Microsoft. La pagina di registrazione dell'applicazione viene aperta in una nuova scheda. In alternativa è possibile passare direttamente alla pagina [Applicazioni personali](https://apps.dev.microsoft.com) e selezionare il nome dell'applicazione per accedere a questa pagina.

    ![Pagina WNS MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Prendere nota della password di **Segreti applicazione** e dell'**Identificatore di sicurezza del pacchetto (SID)** .

    >[!WARNING]
    >Il segreto applicazione e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri utenti né distribuirli con l'app.

## <a name="create-a-notification-hub"></a>Creare un hub di notifica

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>Configurare le impostazioni WNS per l'hub

1. Selezionare **Windows (WNS)** nella categoria **NOTIFICATION SETTINGS** (IMPOSTAZIONI DI NOTIFICA).
2. Immettere i valori per **Package SID** (SID pacchetto) e **Security Key** (Chiave di sicurezza) annotati nella sezione precedente.
3. Fare clic su **Save** sulla barra degli strumenti.

    ![Caselle SID pacchetto e Chiave di sicurezza](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

L'hub di notifica è ora configurato per l'uso di WNS. Sono disponibili le stringhe di connessione per la registrazione dell'app e l'invio di notifiche.

## <a name="create-a-sample-windows-app"></a>Creare un'app Windows di esempio

1. In Visual Studio scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.
2. Nella finestra di dialogo **Crea un nuovo progetto** completare questa procedura:

    1. Nella casella di ricerca in alto digitare **Windows Universal**.
    2. Nei risultati della ricerca selezionare **Blank App (Universal Windows)** (App vuota (Windows universale)) e quindi selezionare **Avanti**.

       ![Finestra di dialogo Nuovo progetto](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. Nella finestra di dialogo **Configura il nuovo progetto** immettere un **Nome progetto** e un **Percorso** per i file di progetto.
    4. Selezionare **Create** (Crea).

3. Accettare le impostazioni predefinite per le versioni di **destinazione** e **minima** della piattaforma e selezionare **OK**.
4. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di app di Windows Store, scegliere **Pubblica** e quindi selezionare **Associa applicazione a Store**. Viene visualizzata la procedura guidata **Associa l'applicazione con Windows Store** .
5. Nella procedura guidata accedere con il proprio account Microsoft.
6. Selezionare l'app registrata nel passaggio 2, quindi **Avanti** e infine **Associa**. Le informazioni di registrazione a Windows Store necessarie verranno così aggiunte al manifesto dell'applicazione.
7. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet**. Verrà visualizzata la finestra **Gestisci pacchetti NuGet**.
8. Nella casella di ricerca immettere **WindowsAzure.Messaging.Managed**, selezionare **Installa** e accettare le condizioni per l'utilizzo.

    ![Finestra Gestisci pacchetti NuGet][20]

    Questa azione scarica, installa e aggiunge un riferimento alla libreria di Hub di notifica di Azure per Windows usando il [pacchetto NuGet Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).
9. Aprire il file di progetto `App.xaml.cs` e aggiungere le istruzioni seguenti:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. Nel file di progetto `App.xaml.cs` individuare la classe `App` e aggiungere la definizione del metodo `InitNotificationsAsync` seguente. Sostituire `<your hub name>` con il nome dell'hub di notifica creato nel portale di Azure e sostituire `<Your DefaultListenSharedAccessSignature connection string>` con la stringa di connessione `DefaultListenSharedAccessSignature` presente nella pagina **Criteri di accesso** dell'hub di notifica:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

    Questo codice recupera il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi lo registra con l'hub di notifica.

    >[!NOTE]
    > Sostituire il segnaposto `hub name` con il nome dell'hub di notifica visualizzato nel portale di Azure. Sostituire anche il segnaposto della stringa di connessione con la stringa di connessione `DefaultListenSharedAccessSignature` ottenuta nella pagina **Criteri di accesso** dell'hub di notifica in una sezione precedente.

11. Nella parte superiore del gestore dell'evento `OnLaunched` in `App.xaml.cs` aggiungere la chiamata seguente al nuovo metodo `InitNotificationsAsync`:

    ```csharp
    InitNotificationsAsync();
    ```

    Questa azione garantisce la registrazione dell'URI di canale nell'hub di notifica a ogni avvio dell'applicazione.

12. Per eseguire l'app, premere **F5**. Verrà visualizzata una finestra di dialogo contenente la chiave di registrazione. Per chiudere la finestra di dialogo, fare clic su **OK**.

    ![Registrazione completata](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

L'app è ora pronta per ricevere notifiche di tipo avviso popup.

## <a name="send-test-notifications"></a>Inviare notifiche di prova

È possibile testare rapidamente la ricezione di notifiche nell'app inviando notifiche nel [portale di Azure](https://portal.azure.com/).

1. Nel portale di Azure passare alla scheda Overview (Informazioni generali) e selezionare **Test Send** (Invio di prova) sulla barra degli strumenti.

    ![Pulsante Test Send (Invio di prova)](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. Nella finestra **Test Send** (Invio di prova) eseguire le operazioni seguenti:
    1. Per **Platforms** (Piattaforme) selezionare **Windows**.
    2. Per **Notification Type** (Tipo di notifica) selezionare **Toast** (Avviso popup).
    3. Selezionare **Send** (Invia).

        ![Riquadro Invio di prova](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Visualizzare il risultato dell'operazione di invio nell'elenco dei **risultati** nella parte inferiore della finestra. È inoltre possibile visualizzare un messaggio di avviso.

    ![Risultato dell'operazione di invio](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
4. Viene visualizzato il messaggio di notifica: **Test message** (messaggio di prova) sul desktop.

    ![Messaggio di notifica](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)

## <a name="next-steps"></a>Passaggi successivi
Sono state inviate notifiche di trasmissione a tutti i dispositivi Windows usando il portale o un'app console. Per informazioni sulle procedure per eseguire il push di notifiche a dispositivi specifici, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a dispositivi specifici](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[toast catalog]: /previous-versions/windows/apps/hh761494(v=win.10)
[tile catalog]: /previous-versions/windows/apps/hh761491(v=win.10)
[badge overview]: /previous-versions/windows/apps/hh779719(v=win.10)
