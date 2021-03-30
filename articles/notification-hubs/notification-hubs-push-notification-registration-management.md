---
title: Gestione delle registrazioni
description: In questo argomento viene illustrato come registrare i dispositivi con gli hub di notifica al fine di ricevere notifiche push.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 07/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7807d28da459656938acb399eb8c621e4c292372
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89001570"
---
# <a name="registration-management"></a>Gestione delle registrazioni

In questo argomento viene illustrato come registrare i dispositivi con gli hub di notifica al fine di ricevere notifiche push. Vengono descritte le registrazioni a livello generale, quindi sono presentati i due modelli principali per la registrazione dei dispositivi: la registrazione dal dispositivo direttamente nell'hub di notifica e la registrazione tramite un back-end dell'applicazione.

## <a name="what-is-device-registration"></a>Che cos'è la registrazione di un dispositivo

La registrazione del dispositivo con un hub di notifica viene eseguita tramite una **registrazione** o un'**installazione**.

### <a name="registrations"></a>Registrazioni

La registrazione associa l'handle del servizio di notifica della piattaforma (PNS) per un dispositivo con tag ed eventualmente un modello. L'handle PNS può essere un URI, un token del dispositivo o un ID di registrazione FCM. I tag vengono usati per instradare le notifiche al set corretto di handle di dispositivo. Per altre informazioni, vedere [routing ed espressioni Tag](notification-hubs-tags-segment-push-message.md). I modelli vengono usati per implementare una trasformazione a livello di singola registrazione. Per altre informazioni, vedere [Modelli](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Hub di notifica di Azure supporta un massimo di 60 Tag per dispositivo.

### <a name="installations"></a>Installazioni

Un'installazione è una registrazione avanzata che include un contenitore di proprietà correlate al push. È comunque l'approccio migliore e più recente alla registrazione dei dispositivi. Tuttavia, non è supportato dall'SDK .NET lato client ([SDK dell'hub di notifica per le operazioni di back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)), per adesso.  Questo significa che in caso di registrazione dal dispositivo client stesso, è necessario utilizzare l’approccio [API REST hub di notifica](/rest/api/notificationhubs/create-overwrite-installation) per supportare le installazioni. Se si utilizza un servizio di back-end, è possibile utilizzare l’ [SDK dell’hub di notifica per le operazioni di back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Ecco alcuni vantaggi chiave dell'uso delle installazioni:

- La creazione o l'aggiornamento di un'installazione è completamente idempotente. È quindi possibile riprovare a eseguire l'operazione senza preoccuparsi di registrazioni duplicate.
- Il modello di installazione supporta un formato di tag speciale ( `$InstallationId:{INSTALLATION_ID}` ) che consente l'invio di una notifica direttamente al dispositivo specifico. Se, ad esempio, il codice dell'app imposta un ID di installazione `joe93developer` per questo particolare dispositivo, uno sviluppatore può fare riferimento a questo dispositivo quando invia una notifica al `$InstallationId:{joe93developer}` tag. In questo modo è possibile fare riferimento a un dispositivo specifico senza dover eseguire altre operazioni di codifica.
- L'uso delle installazioni consente inoltre di eseguire aggiornamenti parziali delle registrazioni. L'aggiornamento parziale di un'installazione è richiesto con un metodo PATCH che usa lo [standard JSON-Patch](https://tools.ietf.org/html/rfc6902). Questo è utile quando si intende aggiornare i tag nella registrazione. Non è necessario disattivare l'intera registrazione e quindi inviare di nuovo tutti i tag precedenti.

Un'installazione può contenere le proprietà seguenti. Per un elenco completo delle proprietà di installazione, vedere [Creare o sovrascrivere un'installazione con API REST](/rest/api/notificationhubs/create-overwrite-installation) o [Proprietà Installation](/dotnet/api/microsoft.azure.notificationhubs.installation).

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Per impostazione predefinita, le registrazioni e le installazioni non scadono.

Le registrazioni e le installazioni devono contenere un handle PNS valido per ogni dispositivo/canale. Poiché gli handle PNS possono essere ottenuti solo in un'app client sul dispositivo, un modello consiste nell'eseguire la registrazione direttamente sul dispositivo con l'app client. D'altra parte, le considerazioni sulla sicurezza e la logica di business relativa ai tag potrebbero richiedere di gestire la registrazione del dispositivo nel back-end dell'app.

> [!NOTE]
> L'API di installazione non supporta il servizio Baidu (anche se l'API di registrazione). 

### <a name="templates"></a>Modelli

Se si intende usare [modelli](notification-hubs-templates-cross-platform-push-messages.md), l'installazione del dispositivo contiene anche tutti i modelli associati al dispositivo in un formato JSON (vedere l'esempio precedente). I nomi dei modelli consentono di usare diversi modelli per lo stesso dispositivo.

Il nome di ogni modello è associato al corpo di un modello e a un set di tag facoltativo. Inoltre, ogni piattaforma può avere ulteriori proprietà del modello. Per Windows Store (che usa WNS) e Windows Phone 8 (che usa MPNS), un set di intestazioni aggiuntivo può far parte del modello. Nel caso degli APN, è possibile impostare una proprietà di scadenza su una costante o un'espressione del modello. Per un elenco completo delle proprietà di installazione, vedere l'argomento [Creare o sovrascrivere un'installazione con REST](/rest/api/notificationhubs/create-overwrite-installation) .

### <a name="secondary-tiles-for-windows-store-apps"></a>Riquadri secondari per le app di Windows Store

Per le applicazioni client di Windows Store, inviare notifiche ai riquadri secondari equivale a inviarle a quello primario. Questo è supportato anche nelle installazioni. I riquadri secondari hanno un diverso ChannelUri, che viene gestito in modo trasparente dall'SDK nell'app client.

Il dizionario SecondaryTiles usa lo stesso TileId che viene usato per creare l'oggetto SecondaryTiles nell'app di Windows Store. Come nel caso del valore ChannelUri primario, i valori ChannelUri dei riquadri secondari possono cambiare in qualsiasi momento. Per mantenere aggiornate le installazioni nell'hub di notifica, il dispositivo deve aggiornarle con i valori ChannelUri correnti dei riquadri secondari.

## <a name="registration-management-from-the-device"></a>Gestione delle registrazioni dal dispositivo

Quando si gestisce la registrazione del dispositivo dalle app client, il back-end è responsabile solo dell'invio delle notifiche. Le app client mantengono aggiornati gli handle PNS e registrano i tag. Questo modello è illustrato nell'immagine seguente.

![Registrazione dal dispositivo](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Il dispositivo recupera l'handle PNS dal PNS, quindi esegue la registrazione direttamente con l'hub di notifica. Una volta che la registrazione ha esito positivo, il back-end dell'app può inviare una notifica relativa alla registrazione. Per altre informazioni su come inviare le notifiche, vedere [Routing ed espressioni tag](notification-hubs-tags-segment-push-message.md).

In questo caso si useranno solo i diritti Listen per accedere agli hub di notifica dal dispositivo. Per altre informazioni, vedere [Sicurezza](notification-hubs-push-notification-security.md).

La registrazione dal dispositivo è il metodo più semplice, ma presenta alcuni svantaggi:

- Un'app client può aggiornare solo i propri tag quando l'app è attiva. Ad esempio, se un utente dispone di due dispositivi che registrano tag relativi a squadre sportive, quando il primo dispositivo esegue la registrazione per un ulteriore tag (ad esempio, i Seahawk), il secondo dispositivo non riceverà le notifiche relative ai Seahawk finché l'app nel secondo dispositivo non viene eseguita una seconda volta. Più in generale, quando i tag interessano più dispositivi, la gestione dei tag dal back-end rappresenta una soluzione migliore.
- Poiché le app possono essere violate, la protezione della registrazione a tag specifici richiede una maggiore attenzione, come illustrato nell'articolo [sicurezza](notification-hubs-push-notification-security.md).

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Codice di esempio per la registrazione con un hub di notifica da un dispositivo tramite un'installazione

Al momento, questa operazione è supportata solo tramite l' [API REST degli hub di notifica](/rest/api/notificationhubs/create-overwrite-installation).

È inoltre possibile usare il metodo PATCH tramite lo [standard JSON-Patch](https://tools.ietf.org/html/rfc6902) per l'aggiornamento dell'installazione.

```csharp
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored an installation ID in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Codice di esempio per la registrazione con un hub di notifica da un dispositivo tramite una registrazione

Questi metodi creano o aggiornano una registrazione per il dispositivo in cui vengono chiamati. Ciò significa che, per aggiornare l'handle o i tag, è necessario sovrascrivere l'intera registrazione. Tenere presente che le registrazioni sono temporanee, quindi è necessario disporre sempre di un archivio affidabile con i tag correnti necessari per il dispositivo specifico.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device ID from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration ID in device
// storage. Then when the app starts, you can check if a registration ID already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration ID in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
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

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Gestione delle registrazioni da un back-end

La gestione delle registrazioni dal back-end richiede la scrittura di codice aggiuntivo. L'app dal dispositivo deve fornire l'handle PNS aggiornato al back-end a ogni avvio dell'app (insieme ai tag e ai modelli) e il back-end deve aggiornare tale handle nell'hub di notifica. Questa progettazione è illustrata nell'immagine seguente.

![Gestione delle registrazioni](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

I vantaggi della gestione delle registrazioni dal back-end includono la possibilità di modificare i tag delle registrazioni anche quando l'app corrispondente nel dispositivo è inattiva e di autenticare l'app client prima di aggiungere un tag alla relativa registrazione.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Codice di esempio per la registrazione con un hub di notifica da un back-end tramite un'installazione

Il dispositivo client ottiene comunque il proprio handle PNS e le proprietà di installazione pertinenti come prima e chiama un'API personalizzata nel back-end che può eseguire la registrazione e autorizzare i tag e così via. Il back-end può sfruttare l' [SDK di hub di notifica per le operazioni back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

È inoltre possibile usare il metodo PATCH tramite lo [standard JSON-Patch](https://tools.ietf.org/html/rfc6902) per l'aggiornamento dell'installazione.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Codice di esempio per la registrazione con un hub di notifica da un dispositivo tramite un ID di registrazione

Dal back-end dell'app è possibile eseguire operazioni CRUD di base sulle registrazioni. Ad esempio:

```csharp
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by ID
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

Il back-end deve gestire la concorrenza tra gli aggiornamenti delle registrazioni. Il bus di servizio offre il controllo della concorrenza ottimistica per la gestione delle registrazioni. A livello HTTP, questo viene implementato attraverso l'uso di ETag sulle operazioni di gestione delle registrazioni. Questa funzionalità viene usata in modo trasparente dagli SDK Microsoft, che generano un'eccezione se un aggiornamento viene rifiutato a causa della concorrenza. Il back-end dell'app è responsabile della gestione di queste eccezioni e dei nuovi tentativi di aggiornamento, se necessario.
