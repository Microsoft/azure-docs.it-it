---
title: File di inclusione
description: File di inclusione che contiene il codice per la creazione di un progetto WebAPI ASP .NET back-end.
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 6f490b6f25112ed8a10bbd865070bd07ea3ee84f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016949"
---
## <a name="create-the-webapi-project"></a>Creare il progetto WebAPI

Le sezioni seguenti illustrano la creazione di un nuovo back-end WebAPI ASP.NET. Questo processo ha tre obiettivi principali:

- **Autenticare i client**: si aggiunge un gestore di messaggi per autenticare le richieste client e associare l'utente alla richiesta.
- **Eseguire la registrazione per le notifiche usando il back-end WebAPI**: si aggiunge un controller per gestire le nuove registrazioni e consentire a un dispositivo client di ricevere le notifiche. Il nome utente autenticato verrà aggiunto automaticamente alla registrazione come [tag](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md).
- **Inviare notifiche ai client**: si aggiunge un controller per consentire agli utenti di attivare un push sicuro per i dispositivi e i client associati al tag.

Creare un nuovo back-end WebAPI ASP.NET eseguendo le azioni seguenti:

> [!IMPORTANT]
> Se si usa Visual Studio 2015 o una versione precedente, prima di procedere con l'esercitazione verificare di avere installato la versione più recente di Gestione pacchetti NuGet per Visual Studio.
>
>A questo scopo, avviare Visual Studio. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**. Cercare **Gestione pacchetti NuGet** nella versione di Visual Studio e verificare che sia installata la versione più recente. Se la versione installata non è la più recente, disinstallarla e reinstallare Gestione pacchetti NuGet.

![Screenshot della finestra di dialogo Estensioni e aggiornamenti con il pacchetto Gestione pacchetti NuGet per Visual Studio evidenziato.][B4]

> [!NOTE]
> Assicurarsi che sia installato Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/) per la distribuzione del sito Web.

1. Avviare Visual Studio o Visual Studio Express.

2. Selezionare **Esplora server** e accedere all'account Azure. Per creare le risorse del sito Web nell'account è necessario eseguire l'accesso.

3. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione di Visual Studio, scegliere **Aggiungi** e fare clic su **Nuovo progetto**.
4. Espandere **Visual C#**, selezionare **Web** e fare clic su **Applicazione Web ASP.NET**.

5. Nella casella **Nome** digitare **AppBackend** e quindi selezionare **OK**.

    ![Finestra Nuovo progetto][B1]

6. Nella finestra **Nuovo progetto ASP.NET** selezionare la casella di controllo **API Web** e quindi selezionare **OK**.

    ![Finestra Nuovo progetto ASP.NET][B2]

7. Selezionare una sottoscrizione nella finestra **Configura app Web di Microsoft Azure** e nell'elenco **Piano di Servizio app** eseguire una delle azioni seguenti:

    * Selezionare un piano di servizio app creato in precedenza.
    * Selezionare **Crea un nuovo piano di servizio app** e quindi creare un piano.

   Per questa esercitazione non è necessario disporre di un database. Dopo aver selezionato il piano di servizio app, fare clic su **OK** per creare il progetto.

    ![Finestra Configura app Web di Microsoft Azure][B5]

    Se questa pagina per la configurazione del piano di servizio app non viene visualizzata, continuare con l'esercitazione. È possibile completare la configurazione in seguito durante la pubblicazione dell'app. 

## <a name="authenticate-clients-to-the-webapi-backend"></a>Autenticare client con il back-end WebAPI

In questa sezione si crea una nuova classe del gestore di messaggi denominata **AuthenticationTestHandler** per il nuovo back-end. Questa classe deriva da [DelegatingHandler](/previous-versions/visualstudio/hh193679(v=vs.118)) e viene aggiunta come gestore di messaggi per poter elaborare tutte le richieste in arrivo nel back-end.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AppBackend**, scegliere **Aggiungi** e quindi selezionare **Classe**.
2. Assegnare alla nuova classe il nome **AuthenticationTestHandler.cs** e fare clic su **Aggiungi** per generarla. Questa classe autentica gli utenti tramite l'*autenticazione di base*. L'app può usare qualsiasi schema di autenticazione.
3. In AuthenticationTestHandler.cs aggiungere le istruzioni `using` seguenti:

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. In AuthenticationTestHandler.cs sostituire la definizione di classe `AuthenticationTestHandler` con il codice seguente:

    Questo gestore autorizza la richiesta quando le tre condizioni seguenti sono vere:

   * La richiesta include un'intestazione di *autorizzazione*.
   * La richiesta usa l'autenticazione *di base* .
   * La stringa del nome utente corrisponde alla stringa della password.

   In caso contrario, la richiesta viene rifiutata. Non si tratta di un vero approccio di autenticazione e autorizzazione. È solo un esempio semplice per questa esercitazione.

   Se il messaggio di richiesta viene autenticato e autorizzato da `AuthenticationTestHandler`, l'utente dell'autenticazione di base viene associato alla richiesta corrente in [HttpContext](/dotnet/api/system.web.httpcontext.current). Le informazioni utente in HttpContext verranno usate da un altro controller (RegisterController) in un secondo momento per aggiungere un [tag](/previous-versions/azure/azure-services/dn530749(v=azure.100)) alla richiesta di registrazione per le notifiche.

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (VerifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool VerifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > Nota sulla sicurezza: la classe `AuthenticationTestHandler` non fornisce un'effettiva autenticazione. Viene usata solo per imitare l'autenticazione di base e non è sicura. È necessario implementare un meccanismo di autenticazione sicuro nelle applicazioni e nei servizi di produzione.
5. Per registrare il gestore di messaggi aggiungere il codice seguente alla fine del metodo `Register` nella classe **App_Start/WebApiConfig.cs**:

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. Salvare le modifiche.

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>Eseguire la registrazione per le notifiche tramite il back-end WebAPI

In questa sezione si aggiunge un nuovo controller al back-end WebAPI per gestire le richieste di registrazione di un utente e un dispositivo per le notifiche tramite la libreria client per gli hub di notifica. Il controller aggiunge un tag user per l'utente che è stato autenticato e collegato a HttpContext da `AuthenticationTestHandler`. Il tag ha il formato stringa, `"username:<actual username>"`.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AppBackend** e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro sinistro selezionare **Online** e nella casella **Cerca** digitare **Microsoft.Azure.NotificationHubs**.

3. Nell'elenco dei risultati fare clic su **Hub di notifica di Microsoft Azure** e quindi selezionare **Installa**. Completare l'installazione e quindi chiudere la finestra di Gestione pacchetti NuGet.

    Questa azione aggiunge un riferimento ad Azure Notification Hubs SDK usando il [pacchetto NuGet Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

4. Creare un nuovo file di classe che rappresenta la connessione con l'hub di notifica usato per inviare le notifiche. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Modelli**, scegliere **Aggiungi** e quindi fare clic su **Classe**. Assegnare alla nuova classe il nome **Notifications.cs** e quindi selezionare **Aggiungi** per generarla.

    ![Finestra Aggiungi nuovo elemento][B6]

5. In Notifications.cs aggiungere l'istruzione `using` seguente all'inizio del file:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. Sostituire la definizione di classe `Notifications` con il codice seguente e sostituire i due segnaposto con la stringa di connessione (con accesso completo) per l'hub di notifica e con il nome dell'hub, disponibile nel [portale di Azure](https://portal.azure.com):

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
    > [!IMPORTANT]
    > Immettere il **nome** e il valore **DefaultFullSharedAccessSignature** dell'hub prima di continuare. 
    
7. Creare quindi un nuovo controller denominato **RegisterController**. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Controller**, scegliere **Aggiungi** e quindi fare clic su **Controller**.

8. Fare clic su **Controller Web API 2 - Vuoto** e selezionare **Aggiungi**.

    ![Finestra Aggiungi scaffolding][B7]

9. Nella casella **Nome controller** digitare **RegisterController** per assegnare un nome alla nuova classe, quindi selezionare **Aggiungi**.

    ![Finestra Aggiungi controller][B8]

10. In RegisterController.cs aggiungere le istruzioni `using` seguenti:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. Aggiungere il codice seguente all'interno della definizione di classe `RegisterController` . In questo codice viene aggiunto un tag user per l'utente associato a HttpContext. L'utente è stato autenticato e associato a HttpContext dal filtro messaggi aggiunto, `AuthenticationTestHandler`. È anche possibile aggiungere controlli facoltativi per verificare che l'utente disponga dei diritti per la registrazione per i tag richiesti.

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

            foreach (RegistrationDescription registration in registrations)
            {
                if (newRegistrationId == null)
                {
                    newRegistrationId = registration.RegistrationId;
                }
                else
                {
                    await hub.DeleteRegistrationAsync(registration);
                }
            }
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "fcm":
                registration = new FcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. Salvare le modifiche.

## <a name="send-notifications-from-the-webapi-backend"></a>Inviare notifiche dal back-end WebAPI

In questa sezione si aggiunge un nuovo controller che consente ai dispositivi client di inviare una notifica. La notifica si basa sul tag username che usa la libreria .NET di Hub di notifica di Azure nel back-end WebAPI ASP.NET.

1. Creare un altro nuovo controller denominato **NotificationsController** seguendo la stessa procedura usata per creare **RegisterController** nella sezione precedente.

2. In NotificationsController.cs aggiungere le istruzioni `using` seguenti:

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. Aggiungere il metodo seguente alla classe **NotificationsController**:

    Questo codice invia un tipo di notifica basato sul parametro `pns` del servizio di notifica della piattaforma (PNS). Il valore `to_tag` viene usato per impostare il tag *username* nel messaggio. Questo tag deve corrispondere a un tag username di una registrazione dell'hub di notifica attiva. Il messaggio di notifica viene estratto dal corpo della richiesta POST ed è formattato per il PNS di destinazione.

    A seconda del servizio di notifica della piattaforma (PNS) usato dai dispositivi supportati per ricevere le notifiche, sono supportate notifiche in una serie di formati. Nei dispositivi Windows è ad esempio possibile usare una [notifica di tipo avviso popup con WNS](/uwp/schemas/tiles/toastschema/schema-root) che non è supportata direttamente da un altro PNS. In tal caso, il back-end deve formattare la notifica come notifica supportata per il PNS dei dispositivi che si intende supportare. Usare quindi l'API di invio appropriata per la [classe NotificationHubClient](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient).

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "fcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendFcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. Premere **F5** per eseguire l'applicazione e verificare l'accuratezza del lavoro svolto sinora. L'app apre un Web browser e viene visualizzata nella home page di ASP.NET.

## <a name="publish-the-new-webapi-backend"></a>Pubblicare il nuovo back-end WebAPI

L'app verrà ora distribuita in un sito Web Azure per renderla accessibile da tutti i dispositivi.

1. Fare clic con il pulsante destro del mouse sul progetto **AppBackend** e scegliere **Pubblica**.

2. Selezionare **Servizio app di Microsoft Azure** come destinazione di pubblicazione e quindi selezionare **Pubblica. Verrà visualizzata la finestra Crea servizio app. Qui è possibile creare tutte le risorse di Azure necessarie per eseguire l'app Web ASP.NET in Azure.

    ![Riquadro Servizio app di Microsoft Azure][B15]

3. Nella finestra **Crea servizio app** selezionare l'account Azure. Selezionare **Modifica tipo** > **App Web**. Mantenere il valore di **Nome app Web** predefinito e selezionare la **sottoscrizione**, il **gruppo di risorse** e il **piano di servizio app**.

4. Selezionare **Crea**.

5. Prendere nota della proprietà **URL sito** nella scheda **Riepilogo**. Questo URL sarà l'*endpoint back-end* più avanti nell'esercitazione.

6. Selezionare **Pubblica**.

Al termine della procedura guidata, l'app Web ASP.NET viene pubblicata in Azure e aperta nel browser predefinito.  L'applicazione sarà visibile in Servizi app di Azure.

L'URL usa il nome dell'app Web specificato in precedenza, con il formato http://<nome_app>.azurewebsites.net.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG