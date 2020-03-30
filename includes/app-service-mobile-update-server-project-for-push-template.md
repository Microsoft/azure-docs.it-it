---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68857418"
---
In questa sezione viene aggiornato il codice nel progetto di back-end dell'app per dispositivi mobili esistente per inviare una notifica push ogni volta che viene aggiunto un nuovo elemento. Questo processo si basa sulla funzionalità dei [modelli](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) di Hub di notifica di Azure, che abilita i push multipiattaforma. I diversi client vengono registrati per le notifiche push usando i modelli e un unico push universale può raggiungere tutte le piattaforme client.

Scegliere una delle seguenti procedure che corrisponde al tipo di progetto di back-end&mdash;, che sia [ back-end .NET](#dotnet) o [back-end Node.js](#nodejs).

### <a name="net-back-end-project"></a><a name="dotnet"></a>Progetto di back-end .NET

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server. Scegliere quindi **Gestisci pacchetti NuGet**. Cercare `Microsoft.Azure.NotificationHubs` e quindi selezionare **Installa**. Questo processo installa la libreria Hub di notifica per l'invio di notifiche dal back-end.
2. Nel progetto server aprire **Controller** > **TodoItemController.cs**. Aggiungere quindi le istruzioni using seguenti:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. Nel metodo **PostTodoItem** aggiungere il codice seguente dopo la chiamata a **InsertAsync**:  

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    Questo processo invia una notifica modello contenente item.Text quando viene inserito un nuovo elemento.

4. Pubblicare di nuovo il progetto server.

### <a name="nodejs-back-end-project"></a><a name="nodejs"></a>Progetto di back-end Node.js

1. Impostare il progetto back-end.
2. Sostituire il codice esistente in todoitem.js con il codice seguente:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    Questo processo invia una notifica modello contenente item.text quando viene inserito un nuovo elemento.

3. In caso di modifica del file nel computer locale, ripubblicare il progetto server.
