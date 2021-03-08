---
title: Invio di notifiche push con Hub di notifica di Azure e Node.js
description: Informazioni su come usare Hub di notifica per inviare notifiche push da un'applicazione Node.js.
keywords: notifica push,notifiche push,push node.js,push ios
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-js
ms.openlocfilehash: eb41593938c670199be38140118f276142ceed43
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453224"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Invio di notifiche push con Hub di notifica di Azure e Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Panoramica

> [!IMPORTANT]
> Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si ha un account, creare un account di valutazione gratuita in pochi minuti tramite la [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Questa guida illustra come inviare notifiche push con Hub di notifica di Azure direttamente da un'applicazione [Node.js](https://nodejs.org).

Gli scenari presentati includono l'invio di notifiche push ad applicazioni nelle piattaforme seguenti:

- Android
- iOS
- Piattaforma UWP (Universal Windows Platform)
- Windows Phone

## <a name="notification-hubs"></a>Hub di notifica

Hub di notifica di Azure offre un'infrastruttura scalabile, multipiattaforma e di semplice utilizzo per l'invio di notifiche push ai dispositivi mobili. Per informazioni dettagliate sull'infrastruttura del servizio, vedere la pagina [Hub di notifica di Azure](/previous-versions/azure/azure-services/jj927170(v=azure.100)) .

## <a name="create-a-nodejs-application"></a>Creazione di un'applicazione Node.js

Il primo passaggio in questa esercitazione consiste nel creare una nuova applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure][nodejswebsite] e il [servizio cloud Node.js][Node.js Cloud Service] mediante Windows PowerShell oppure il [sito Web con WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Configurare l'applicazione per l'uso di Hub di notifica

Per usare Hub di notifica di Azure, è necessario scaricare e usare il [pacchetto Azure](https://www.npmjs.com/package/azure)di Node.js, che comprende un set integrato di librerie di supporto che comunicano con i servizi REST di notifica push.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usare Node Package Manager (NPM) per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Linux) e passare alla cartella in cui è stata creata l'applicazione vuota.
2. Eseguire `npm install azure-sb` nella finestra di comando.
3. È possibile eseguire manualmente il comando `ls` o `dir` per verificare che sia stata creata una cartella `node_modules`.
4. All'interno di tale cartella trovare il pacchetto **azure** , che contiene le librerie necessarie per accedere a Hub di notifica.

> [!NOTE]
> Altre informazioni sull'installazione di NPM sono disponibili nel [blog NPM](https://blog.npmjs.org/post/85484771375/how-to-install-npm)ufficiale.

### <a name="import-the-module"></a>Importare il modulo
Usando un editor di testo, aggiungere quanto segue nella parte superiore del file `server.js` dell'applicazione:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Configurare una connessione all'Hub di notifica di Azure

L'oggetto `NotificationHubService` consente di lavorare con gli hub di notifica. Il codice seguente consente di creare un oggetto `NotificationHubService` per l'hub di notifica denominato `hubname`. Aggiungerlo nella parte superiore del file `server.js` dopo l'istruzione per importare il modulo Azure:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Ottenere il valore `connectionstring` della connessione dal [portale di Azure] seguendo questa procedura:

1. Nel riquadro di spostamento a sinistra fare clic su **Sfoglia**.
2. Selezionare **Hub di notifica** e quindi individuare l'hub da utilizzare per l'esempio. Se è necessario assistenza per la creazione di un nuovo hub di notifica, è possibile fare riferimento all' [esercitazione su Windows Store Introduzione](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
3. Selezionare **Impostazioni**.
4. Fare clic su **Criteri di accesso**. Vengono visualizzate la stringhe di connessione di accesso completo e condiviso.

![Portale di Azure - Hub di notifica](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> La stringa di connessione può essere recuperata anche usando il cmdlet **Get-AzureSbNamespace** specificato da [Azure PowerShell](/powershell/azure/) o il comando **azure sb namespace show** con l'[interfaccia della riga di comando di Azure](/cli/azure/install-classic-cli).

## <a name="general-architecture"></a>Architettura generale

L'oggetto `NotificationHubService` espone le istanze seguenti dell'oggetto per l'invio di notifiche push a specifici dispositivi e specifiche applicazioni:

- **Android**: usare l'oggetto `GcmService`, disponibile in `notificationHubService.gcm`
- **iOS**: usare l'oggetto `ApnsService`, accessibile in `notificationHubService.apns`
- **Windows Phone**: usare l'oggetto `MpnsService`, disponibile in `notificationHubService.mpns`
- **Piattaforma UWP (Universal Windows Platform)**: usare l'oggetto `WnsService`, disponibile in `notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Procedura: Inviare notifiche push ad applicazioni Android

L'oggetto `GcmService` specifica un metodo `send` che è possibile usare per inviare notifiche push alle applicazioni Android. Di seguito sono illustrati i parametri accettati dal metodo `send`:

- **Tags** : l'identificatore tag. Se non viene specificato alcun tag, la notifica viene inviata a tutti i client.
- **Payload** : il payload JSON o la stringa non elaborata del messaggio.
- **Callback** : la funzione di richiamata.

Per ulteriori informazioni sul formato di payload, vedere la [documentazione del payload](https://payload.readthedocs.io/en/latest/).

Nel codice seguente viene usata l'istanza di `GcmService` esposta da `NotificationHubService` per inviare una notifica push a tutti i client registrati.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Procedura: Inviare notifiche push ad applicazioni iOS

Come per le applicazioni Android descritte sopra, l'oggetto `ApnsService` specifica un metodo `send` che è possibile usare per inviare notifiche push alle applicazioni iOS. Di seguito sono illustrati i parametri accettati dal metodo `send`:

- **Tags** : l'identificatore tag. Se non viene specificato alcun tag, la notifica viene inviata a tutti i client.
- **Payload** : il payload JSON o stringa del messaggio.
- **Callback** : la funzione di richiamata.

Per ulteriori informazioni sul formato di payload, vedere la sezione **contenuto della notifica** della [Guida di UserNotifications](https://developer.apple.com/documentation/usernotifications).

Nel codice seguente viene usata l'istanza di `ApnsService` esposta da `NotificationHubService` per inviare un messaggio di avviso a tutti i client:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Procedura: Inviare notifiche push ad applicazioni Windows Phone

L'oggetto `MpnsService` specifica un metodo `send` che è possibile usare per inviare notifiche push alle applicazioni Windows Phone. Di seguito sono illustrati i parametri accettati dal metodo `send`:

- **Tags** : l'identificatore tag. Se non viene specificato alcun tag, la notifica viene inviata a tutti i client.
- **Payload** : il payload XML del messaggio.
- **TargetName**  -  `toast` per le notifiche di tipo avviso popup. `token` per le notifiche di tipo riquadro.
- **NotificationClass** : la priorità della notifica. Per i valori validi, vedere la sezione relativa agli **elementi dell'intestazione HTTP** nel documento sul [push di notifiche da un server](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) .
- **Options** : intestazioni delle richieste facoltative.
- **Callback** : la funzione di richiamata.

Per un elenco dei valori validi per `TargetName`, `NotificationClass`, e le opzioni di intestazione, vedere la pagina [Pushing Notifications from a Server](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) (Notifiche push da un server).

Nel codice di esempio seguente viene usata l'istanza di `MpnsService` esposta da `NotificationHubService` per inviare una notifica push di tipo avviso popup:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Procedura: Inviare notifiche push alle applicazioni della piattaforma UWP (Universal Windows Platform)

L'oggetto `WnsService` specifica un metodo `send` che è possibile usare per inviare notifiche push alle applicazioni della piattaforma UWP (Universal Windows Platform).  Di seguito sono illustrati i parametri accettati dal metodo `send`:

- **Tags** : l'identificatore tag. Se non viene specificato alcun tag, la notifica viene inviata a tutti i client registrati.
- **Payload** : il payload XML del messaggio.
- **Type** : il tipo di notifica.
- **Options** : intestazioni delle richieste facoltative.
- **Callback** : la funzione di richiamata.

Per un elenco dei valori validi per i tipi e le intestazioni delle richieste, vedere [Intestazioni delle richieste e delle risposte per il servizio di notifica push](/previous-versions/windows/apps/hh465435(v=win.10)).

Nel codice seguente viene usata l'istanza di `WnsService` esposta da `NotificationHubService` per inviare una notifica push di tipo avviso popup a un'app UWP:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Passaggi successivi

I frammenti di codice di esempio riportati sopra consentono di creare facilmente l'infrastruttura del servizio per recapitare notifiche push a un'ampia gamma di dispositivi. A questo punto, dopo aver appreso le nozioni di base dell'uso di Hub di notifica con Node.js, usare i collegamenti seguenti per scoprire come estendere ulteriormente queste funzionalità.

- Vedere la documentazione MSDN su [Hub di notifica di Azure](/previous-versions/azure/azure-services/jj927170(v=azure.100)).
- Visitare il repository [Azure SDK for Node] su GitHub per altri esempi e dettagli relativi all'implementazione.

[Azure SDK per Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter_sqlexpression
[Azure Service Bus Notification Hubs]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: ../app-service/quickstart-nodejs.md
[webmatrix]: /aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Portale di Azure]: https://portal.azure.com
