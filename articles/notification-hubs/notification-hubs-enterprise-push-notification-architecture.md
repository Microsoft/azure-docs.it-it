---
title: Architettura push aziendale di Notification Hubs
description: Informazioni sull'uso di Hub di notifica di Azure in un ambiente aziendaleLearn about using Azure Notification Hubs in an enterprise environment
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 0104547a432f7f78d74731e11926bcd82088cef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264034"
---
# <a name="enterprise-push-architectural-guidance"></a>Guida all'architettura push aziendale

Al giorno d'oggi, le aziende stanno gradualmente passando alla creazione di applicazioni per dispositivi mobili sia per gli utenti finali (esterni) che per i dipendenti (interni). Le aziende dispongono di sistemi back-end già esistenti, ovvero mainframe o applicazioni LoB che è necessario integrare nell'architettura delle applicazioni per dispositivi mobili. In questa Guida verrà illustrato come eseguire questa integrazione nel modo migliore possibile, fornendo possibili soluzioni per scenari comuni.

Una richiesta frequente riguarda l'invio di notifiche push agli utenti tramite l'applicazione per dispositivi mobili in uso quando si verifica un evento di interesse nei sistemi back-end. Ad esempio, un cliente della banca che dispone dell'app bancaria della banca su un iPhone desidera ricevere una notifica quando viene effettuato un addebito superiore a un determinato importo dal conto o da uno scenario Intranet in cui un dipendente del reparto finanziario che dispone di un'app di approvazione del budget in un Windows Phone desidera ricevere una notifica alla ricezione della richiesta di approvazione.

È probabile che l'elaborazione del conto o dell'approvazione venga eseguita in un qualche sistema back-end che deve avviare un'operazione push verso l'utente. È possibile che siano presenti diversi sistemi back-end che devono eseguire la stessa tipologia di logica per eseguire un push quando un evento attiva una notifica. In questo caso la complessità è dovuta alla necessità di integrare numerosi back-end con un singolo sistema di push, dove gli utenti finali possono aver eseguito la sottoscrizione a diverse notifiche e dove possono essere usate più applicazioni mobili. Ad esempio, applicazioni per dispositivi mobili intranet che possono ricevere notifiche da diversi sistemi back-end. I sistemi back-end non conoscono o non hanno necessità di conoscere la semantica e/o la tecnologia di push. Per questo motivo, una soluzione comunemente usata fino ad ora consiste nell'introdurre un componente che esegue il polling dei sistemi back-end per qualsiasi evento di interesse ed è responsabile dell'invio di messaggi push al client.

Una soluzione migliore consiste nell'uso del modello Bus di servizio di Azure - Argomento/Sottoscrizione. Tale modello, infatti, riduce la complessità della soluzione e la rende scalabile.

Di seguito è descritta l'architettura generale della soluzione, descritta con numerose app per dispositivi mobili ma ugualmente applicabile nel caso in cui ne venga usata una soltanto.

## <a name="architecture"></a>Architecture

![][1]

L'elemento chiave di questo diagramma dell'architettura è il bus di servizio di Azure, che fornisce un modello di programmazione di tipo argomenti/sottoscrizioni. Per altre informazioni su tale modello, vedere [Come usare gli argomenti e le sottoscrizioni del bus di servizio]. Il destinatario, che in questo caso è il back-end mobile (in genere Il servizio mobile di [Azure,]che avvia un push alle app per dispositivi mobili), non riceve messaggi direttamente dai sistemi back-end, ma un livello di astrazione intermedio fornito dal bus di servizio di [Azure,]che consente al back-end mobile di ricevere messaggi da uno o più sistemi back-end. È necessario creare un argomento del bus di servizio per ciascuno dei sistemi back-end, ad esempio Contabilità, Risorse umane e Finanza. Si tratta fondamentalmente di "argomenti" rilevanti che danno luogo a messaggi da inviare come notifiche push. I sistemi back-end inviano messaggi a questi argomenti. Un back-end Mobile può sottoscrivere uno o più di tali argomenti creando una sottoscrizione del bus di servizio. Questo autorizza il back-end Mobile a ricevere una notifica dal sistema back-end corrispondente. Il back-end Mobile continua a rimanere in ascolto per rilevare i messaggi inviati alla sottoscrizione e, non appena ne arriva uno, lo invia come notifica all'hub di notifica. L'hub di notifica invia infine il messaggio all'app per dispositivi mobili. Ecco l'elenco dei componenti principali:

1. Sistema back-end (sistemi LoB/legacy)
   * Crea un argomento del bus di servizio
   * Invia un messaggio
1. Back-end Mobile
   * Crea una sottoscrizione al servizio
   * Riceve un messaggio (dal sistema back-end)
   * Invia la notifica al client (tramite Hub di notifica di Azure)
1. Applicazione per dispositivi mobili
   * Riceve e visualizza la notifica

### <a name="benefits"></a>Vantaggi

1. Il disaccoppiamento tra il ricevitore (app/servizio per dispositivi mobili tramite Hub di notifica) e il mittente(sistemi back-end) consente l'integrazione di sistemi back-end aggiuntivi con modifiche minime.
1. Questo consente di ricevere eventi da uno o più sistemi back-end anche nello scenario con più app per dispositivi mobili.  

## <a name="sample"></a>Esempio

### <a name="prerequisites"></a>Prerequisiti

Completare le seguenti esercitazioni per acquisire familiarità con i concetti e con i comuni passaggi di creazione e configurazione:

1. [Come usare gli argomenti e le sottoscrizioni del bus di servizio]: questa esercitazione illustra nei dettagli l'utilizzo di argomenti/sottoscrizioni del bus di servizio. Viene inoltre mostrato come creare uno spazio dei nomi per contenere argomenti/sottoscrizioni e come inviare e ricevere messaggi da questi ultimi.
2. [Introduzione ad Hub di notifica] : questa esercitazione illustra come configurare un'app di Windows Store e usare Hub di notifica per registrare e quindi ricevere le notifiche.

### <a name="sample-code"></a>Codice di esempio

Il codice completo è disponibile nella pagina relativa agli [esempi di Hub di notifica]. Il codice è suddiviso in tre componenti:

1. **EnterprisePushBackendSystem**

    a. Il progetto usa il pacchetto NuGet **WindowsAzure.ServiceBus** ed è basato su quanto riportato in [Come usare gli argomenti e le sottoscrizioni del bus di servizio].

    b. Questa applicazione è una app console C# per simulare un sistema LoB che avvia il messaggio da recapitare all'app per dispositivi mobili.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. `CreateTopic` viene usato per creare un argomento del bus di servizio.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage` viene usato per inviare i messaggi a questo argomento del bus di servizio. Questo codice invia semplicemente, a cadenza periodica, un insieme di messaggi casuali all'argomento. Nella realtà, è presente un sistema back-end che invia i messaggi quando si verifica un evento.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **ReceiveAndSendNotification**

    a. Il progetto usa i pacchetti NuGet *WindowsAzure.ServiceBus* e **Microsoft.Web.WebJobs.Publish** ed è basato su quanto riportato in [Come usare gli argomenti e le sottoscrizioni del bus di servizio].

    b. L'app console seguente viene eseguita come processo [Web di Azure]. Questa app deve infatti essere eseguita continuamente per ascoltare i messaggi dei sistemi LOB/back-end. Questa applicazione fa parte del back-end per dispositivi mobili.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. `CreateSubscription` viene usato per creare una sottoscrizione del bus di servizio per l'argomento in cui il sistema back-end invia i messaggi. A seconda dello scenario aziendale, questo argomento crea una o più sottoscrizioni agli argomenti corrispondenti. Può, ad esempio, ricevere messaggi dal sistema Risorse umane, dal sistema Finanza e così via.

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification` viene usato per leggere il messaggio inviato dall'argomento usando la relativa sottoscrizione. Se l'operazione di lettura ha esito positivo, viene creata una notifica (nello scenario di esempio una notifica di tipo avviso popup nativo di Windows) da inviare all'applicazione per dispositivi mobili usando Hub di notifica di Azure.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. Per pubblicare quest'app come **processo Web**, in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Pubblica come processo Web di Azure**

    ![][2]

    f. Selezionare il proprio profilo di pubblicazione e, se non è già presente, creare un nuovo sito Web di Azure che ospita questo processo Web, quindi fare clic su **Pubblica**.

    ![][3]

    g. Configurare il processo per l'esecuzione continua, in modo che, quando si accede al [portale di Azure], venga visualizzata una schermata simile alla seguente:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Questa applicazione è un'applicazione Windows Store che riceve notifiche di tipo avviso popup dal processo Web in esecuzione come parte del back-end Mobile e le visualizza. Questo codice si basa su quanto riportato in [Introduzione ad Hub di notifica - Esercitazione per Windows Universal].  

    b. Verificare che l'applicazione sia abilitata alla ricezione di notifiche di tipo avviso popup.

    c. Assicurarsi che all'avvio dell'app, dopo aver sostituito `HubName` e `DefaultListenSharedAccessSignature`, venga chiamato il seguente codice di registrazione di Hub di notifica:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
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

### <a name="running-the-sample"></a>Esecuzione dell'esempio

1. Assicurarsi che il processo Web venga eseguito correttamente e che sia pianificato per l'esecuzione continua.
2. Eseguire **EnterprisePushMobileApp**, che avvia l'app Windows Store.
3. Eseguire l'applicazione console **EnterprisePushBackendSystem** che simula il back-end LOB e avvia l'invio di messaggi. Verranno visualizzate notifiche di tipo avviso popup simili all'immagine seguente:

    ![][5]

4. All'inizio i messaggi sono stati inviati ad argomenti del bus di servizio, operazione monitorata da sottoscrizioni del bus di servizio nel processo Web. Una volta ricevuto un messaggio, è stata creata una notifica che è stata inviata all'app per dispositivi mobili. Per confermare l'elaborazione, è possibile esaminare i log del processo Web quando si accede al collegamento Log relativo al processo Web nel [portale di Azure]:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[esempi di Hub di notifica]: https://github.com/Azure/azure-notificationhubs-samples
[Servizio mobile di Azure]: https://azure.microsoft.com/documentation/services/mobile-services/
[Bus di servizio di AzureAzure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Come usare argomenti/sottoscrizioni del bus di servizio]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Processo Web di Azure]: ../app-service/webjobs-create.md
[Introduzione ad Hub di notifica]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portale di Azure]: https://portal.azure.com/
