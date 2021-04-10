---
title: Inviare notifiche push ad Android con Hub di notifica di Azure e Firebase SDK versione 1.0.0-preview1
description: Questa esercitazione illustra come usare Hub di notifica di Azure e Google Firebase Cloud Messaging per inviare notifiche push ai dispositivi Android (versione 1.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 091ada7f099aca17152fc806e61dce6ab979852e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055162"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>Esercitazione: Inviare notifiche push ai dispositivi Android con Firebase SDK versione 1.0.0-preview1

Questa esercitazione illustra come usare Hub di notifica di Azure e la versione aggiornata di Firebase Cloud Messaging (FCM) SDK (1.0.0-preview1) per inviare notifiche push a un'applicazione Android. In questa esercitazione verrà creata un'app Android vuota che riceve notifiche push tramite Firebase Cloud Messaging (FCM).

Il codice completo per questa esercitazione può essere scaricato da [GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh).

Questa esercitazione illustra i passaggi seguenti:

- Creare un progetto di Android Studio.
- Creare un progetto Firebase che supporta Firebase Cloud Messaging.
- Creare un hub di notifica.
- Connettere l'app all'hub.
- Testare l'app.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

Sono necessari anche gli elementi seguenti:

- È consigliabile usare la versione più recente di [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).
- Il supporto minimo è il livello API 19.

## <a name="create-an-android-studio-project"></a>Creare un progetto di Android Studio

Il primo passaggio consiste nel creare un progetto in Android Studio:

1. Avviare Android Studio.

2. Selezionare **File**, quindi **New** (Nuovo) e infine **New Project** (Nuovo progetto).

3. Nella pagina **Choose your project** (Scegliere il progetto) selezionare **Empty Activity** (Attività vuota) e quindi **Next** (Avanti).

4. Nella pagina **Configure your project** (Configurare il progetto) eseguire queste operazioni:
   1. Immettere un nome per l'applicazione.
   2. Specificare un percorso in cui salvare i file del progetto.
   3. Selezionare **Fine**.

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="Configurare il progetto":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>Creare un progetto Firebase che supporta FCM

1. Accedere alla [console di Firebase](https://firebase.google.com/console/). Creare un nuovo progetto Firebase se non è già disponibile.

2. Dopo aver creato il progetto, selezionare **Add Firebase to your Android app** (Aggiungi Firebase all'app Android).

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="Aggiungere Firebase":::

3. Nella pagina **Add Firebase to your Android app** (Aggiungi Firebase all'app Android) eseguire queste operazioni:

   1. Per **Android package name** (Nome pacchetto Android), copiare il valore di **applicationId** nel file **build.gradle** dell'applicazione. In questo esempio è `com.fabrikam.fcmtutorial1app`.

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="Specificare il nome del pacchetto":::

   2. Selezionare **Registra l'app**.

4. Selezionare **Download google-services.json** (Scarica google-services.json), salvare il file nella cartella **app** del progetto e quindi selezionare **Avanti**.

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="Scaricare il servizio Google":::

5. Nella console di Firebase selezionare il file COG per il progetto. Selezionare quindi **Project Settings** (Impostazioni progetto).

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="Impostazioni del progetto":::

6. Se il file **google-services.json** non è stato scaricato nella cartella **app** del progetto di Android Studio, è possibile eseguire questa operazione in questa pagina.

7. Passare alla scheda **Cloud Messaging**.

8. Copiare e salvare il valore di **Chiave server** per un uso successivo. Questo valore viene usato per configurare l'hub.

## <a name="configure-a-notification-hub"></a>Configurare un hub di notifica

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Scegliere **Tutti i servizi** dal menu a sinistra e quindi selezionare **Hub di notifica** nella sezione **Dispositivi mobili**. Selezionare l'icona a forma di stella accanto al nome del servizio per aggiungere il servizio alla sezione **PREFERITI** nel menu a sinistra. Dopo aver aggiunto **Hub di notifica** a **PREFERITI**, selezionarlo nel menu a sinistra.

3. Nella pagina **Hub di notifica** selezionare **Aggiungi** sulla barra degli strumenti.

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="Aggiungere l'hub":::

4. Nella pagina **Hub di notifica** eseguire queste operazioni:

   1. Immettere un nome in **Hub di notifica**.

   2. Immettere un nome in **Crea un nuovo spazio dei nomi**. Uno spazio dei nomi contiene uno o più hub.

   3. Selezionare un valore nell'elenco a discesa **Località**. Questo valore specifica la posizione in cui creare l'hub.

   4. In **Gruppo di risorse** selezionare un gruppo di risorse esistente o crearne uno nuovo.

   5. Selezionare **Crea**.

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="Creare l'hub":::

5. Selezionare **Notifiche** (icona a forma di campanello) e quindi selezionare **Vai alla risorsa**. È anche possibile aggiornare l'elenco nella pagina **Hub di notifica** e selezionare l'hub.

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="Selezionare l'hub":::

6. Selezionare **Criteri di accesso** dall'elenco. Prendere nota delle due stringhe di connessione disponibili. Sono necessarie in un secondo momento per gestire le notifiche push.

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="Criteri di accesso":::

   > [!IMPORTANT]
   > Non usare il criterio **DefaultFullSharedAccessSignature** nell'applicazione. Questo criterio deve essere usato solo nel back-end dell'app.

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Configurare le impostazioni di Firebase Cloud Messaging per l'hub

1. Nel riquadro a sinistra in **Settings** (Impostazioni) selezionare **Google (GCM/FCM)** .

2. Immettere la **chiave del server** per il progetto FCM salvato in precedenza.

3. Sulla barra degli strumenti selezionare **Save** (Salva).

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="Chiave server":::

4. Il portale di Azure visualizza un messaggio per indicare che l'hub è stato aggiornato correttamente. Il pulsante **Save** (Salva) è disabilitato.

L'hub di notifica è ora configurato per l'uso di Firebase Cloud Messaging. Sono anche disponibili le stringhe di connessione necessarie per inviare notifiche a un dispositivo e registrare un'app per la ricezione di notifiche.

## <a name="connect-your-app-to-the-notification-hub"></a>Connettere l'app all'hub di notifica

### <a name="add-google-play-services-to-the-project"></a>Aggiungere Google Play Services al progetto

1. In Android Studio selezionare **Strumenti** nel menu e quindi **SDK Manager**.

2. Selezionare la versione di destinazione di Android SDK che viene usata nel progetto. Quindi selezionare **Mostra i dettagli del pacchetto**.

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="SDK manager":::

3. Selezionare **API Google**, se non è già installato.

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="API":::

4. Passare alla scheda **SDK Tools**. Se lo strumento Google Play Services non è già installato, fare clic su **Google Play Services** come illustrato nell'immagine di seguito. Selezionare **Applica** per installarlo. Prendere nota del percorso dell'SDK per l'uso in un passaggio successivo.

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Play services":::

5. Se viene visualizzata la finestra di dialogo **Confirm Change** (Conferma modifica), selezionare **OK**. Il programma di installazione componenti installa i componenti richiesti. Al termine dell'installazione dei componenti, selezionare **Finish** (Fine).

6. Selezionare **OK** per chiudere la finestra di dialogo **Settings for New Projects** (Impostazioni per nuovi progetti).

### <a name="add-azure-notification-hubs-libraries"></a>Aggiungere le librerie di Hub di notifica di Azure

1. Nel file **build.gradle** per l'app aggiungere le righe seguenti nella sezione dependencies:

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk-fcm:1.1.4'
   implementation 'androidx.appcompat:appcompat:1.0.0'
   ```

2. Aggiungere il repository seguente dopo la sezione dependencies:

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>Aggiungere il supporto di Google Firebase

1. Aggiungere il plug-in seguente alla fine del file se non è già presente.

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. Selezionare **Sync Now** (Sincronizza ora) sulla barra degli strumenti.

### <a name="add-code"></a>Aggiungere codice

1. Creare un oggetto **NotificationHubListener**, che gestisce l'intercettazione dei messaggi provenienti da Hub di notifica di Azure.

   ```java
   public class CustomNotificationListener implements NotificationListener {

      @override
      public void onNotificationReceived(Context context, RemoteMessage message) {
    
         /* The following notification properties are available. */
         Notification notification = message.getNotification();
         String title = notification.getTitle();
         String body = notification.getBody();
         Map<String, String> data = message.getData();
    
         if (message != null) {
            Log.d(TAG, "Message Notification Title: " + title);
            Log.d(TAG, "Message Notification Body: " + message);
         }

         if (data != null) {
             for (Map.Entry<String, String> entry : data.entrySet()) {
                 Log.d(TAG, "key, " + entry.getKey() + " value " + entry.getValue());
             }
         }
      }
   }
   ```

2. Nel metodo `OnCreate` della classe `MainActivity` aggiungere il codice seguente per avviare il processo di registrazione di Hub di notifica quando viene creata l'attività:

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.start(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. Sulla barra dei menu di Android Studio selezionare **Build** (Compila), quindi selezionare **Rebuild Project** (Ricompila progetto) per assicurarsi che il codice non contenga errori. Se si riceve un errore relativo all'icona **ic_launcher**, rimuovere l'istruzione seguente dal file AndroidManifest.xml:

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. Assicurarsi di avere un dispositivo virtuale per l'esecuzione dell'app. Se non è presente, aggiungerne uno nel modo seguente:

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="Gestione dispositivi":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="Dispositivi virtuali":::
   3. Eseguire l'app nel dispositivo selezionato e verificare che venga registrata correttamente nell'hub.

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="Registrazione del dispositivo":::

      > [!NOTE]
      > La registrazione può non riuscire durante l'avvio iniziale, fino a quando non viene chiamato il metodo `onTokenRefresh()` del servizio Instance ID. Un aggiornamento dovrebbe avviare una registrazione corretta con l'hub di notifica.

## <a name="send-a-test-notification"></a>Invio di una notifica di prova

È possibile inviare notifiche push all'hub di notifica dal [portale di Azure](https://portal.azure.com/), come indicato di seguito:

1. Nella pagina Hub di notifica per l'hub nel portale di Azure selezionare **Invio di prova** nella sezione **Risoluzione dei problemi**.

2. Per **Piattaforme** selezionare **Android**.

3. Selezionare **Send** (Invia). Non verrà visualizzata alcuna notifica nel dispositivo Android, perché nel dispositivo non è stata ancora eseguita l'app per dispositivi mobili. Dopo aver eseguito l'app per dispositivi mobili, selezionare di nuovo il pulsante **Invia** per visualizzare il messaggio di notifica.

4. Osservare i risultati dell'operazione nell'elenco in fondo alla pagine del portale.

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="Inviare una notifica di prova":::

5. Il messaggio di notifica viene visualizzato nel dispositivo.

Le notifiche push vengono in genere inviate in un servizio back-end come App per dispositivi mobili o ASP.NET usando una libreria compatibile. Se non è disponibile una libreria per il back-end è anche possibile usare direttamente l'API REST per inviare messaggi di notifica.

## <a name="run-the-mobile-app-on-emulator"></a>Eseguire l'app per dispositivi mobili nell'emulatore

Prima di testare le notifiche push all'interno dell'emulatore, assicurarsi che l'immagine dell'emulatore supporti il livello Google API scelto per l'app. Se l'immagine non supporta le API Google native, è possibile che venga generata l'eccezione **SERVICE_NOT_AVAILABLE**.

Assicurarsi anche di avere aggiunto l'account Google all'emulatore in esecuzione in **Impostazioni** > **Account**. In caso contrario, i tentativi di registrazione con FCM possono generare un'eccezione **AUTHENTICATION_FAILED**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato Firebase Cloud Messaging per trasmettere notifiche a tutti i dispositivi Android registrati nel servizio. Per informazioni sulle procedure per eseguire il push di notifiche a dispositivi specifici, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
>[Esercitazione: Inviare notifiche push a specifici utenti](push-notifications-android-specific-users-firebase-cloud-messaging.md)

Di seguito è riportato un elenco di altre esercitazioni sull'invio di notifiche:

- App per dispositivi mobili di Azure: per un esempio di come inviare notifiche dal back-end di un'app per dispositivi mobili integrata con Hub di notifica, vedere [Aggiungere notifiche push all'app iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

- ASP.NET: [usare Hub di notifica per inviare notifiche push agli utenti](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).

- Azure Notification Hubs Java SDK: vedere [Come usare Hub di notifica da Java](notification-hubs-java-push-notification-tutorial.md) per l'invio di notifiche da Java. È stato testato in Eclipse per lo sviluppo per Android.

- PHP: [Come usare Hub di notifica da PHP](notification-hubs-php-push-notification-tutorial.md).
