---
title: 'Avvio rapido: API REST Servizio Azure SignalR'
description: Informazioni su come usare l'API REST con il Servizio Azure SignalR negli esempi seguenti. Trovare i dettagli della specifica API REST.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 2613f91e8c7f1ad3a05792a9a165f4560c09a637
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94874526"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Guida di avvio rapido: Trasmettere messaggi in tempo reale dall'app console

Il servizio Azure SignalR fornisce l'[API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) per supportare scenari di comunicazione da server a client, ad esempio la trasmissione. È possibile scegliere qualsiasi linguaggio di programmazione che possa effettuare chiamate all'API REST. È possibile inviare messaggi a tutti i client connessi, a un client specifico in base al nome o a un gruppo di client.

In questa guida di avvio rapido si apprenderà come inviare messaggi da un'app da riga di comando alle app client connesse in C#.

## <a name="prerequisites"></a>Prerequisiti

Questa guida di avvio rapido può essere eseguita su macOS, Windows o Linux.

* [ASP.NET Core SDK](https://www.microsoft.com/net/download/core)
* Un editor di testo o editor di codice di propria scelta.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsapi).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com/> con il proprio account Azure.

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsapi).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsapi).

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Mentre il servizio è in distribuzione, è possibile passare alla preparazione del codice. Clonare l'[app di esempio da GitHub](https://github.com/aspnet/AzureSignalR-samples.git), impostare la stringa di connessione del servizio SignalR ed eseguire localmente l'applicazione.

1. Aprire una finestra del terminale Git. Passare a una cartella in cui si vuole clonare il progetto di esempio.

1. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsapi).

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

Questo esempio è un'app console che illustra l'uso del servizio Azure SignalR. Fornisce due modalità:

- Modalità server: uso di semplici comandi per chiamare l'API REST del servizio Azure SignalR.
- Modalità client: connessione al servizio Azure SignalR e ricezione di messaggi dal server.

È anche possibile apprendere come generare un token di accesso per l'autenticazione con il servizio Azure SignalR.

### <a name="build-the-executable-file"></a>Creare il file eseguibile

Viene usato macOS osx.10.13-x64 come esempio. È possibile trovare i [riferimenti](/dotnet/core/rid-catalog) sulla compilazione in altre piattaforme.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Avviare un client

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Avviare un server

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsapi).

## <a name="run-the-sample-without-publishing"></a>Eseguire l'esempio senza eseguire la pubblicazione

È anche possibile eseguire il comando seguente per avviare un server o un client

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Usare segreti utente per specificare la stringa di connessione

È possibile eseguire `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` nella directory radice dell'esempio. Successivamente, non è più necessaria l'opzione `-c "<ConnectionString>"`.

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsapi).

## <a name="usage"></a>Uso

Dopo l'avvio del server, usare il comando per inviare il messaggio:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

È possibile avviare più client con nomi diversi.

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsapi).

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> Integrazione con IDE di terze parti

Il servizio Azure SignalR consente l'integrazione di servizi di terze parti con il sistema.

### <a name="definition-of-technical-specifications"></a>Definizione delle specifiche tecniche

La tabella seguente illustra tutte le versioni delle API REST supportate fino al presente. È anche possibile trovare il file di definizione per ogni versione specifica

Versione | Stato API | Porta | Specifica
--- | --- | --- | ---
`1.0-preview` | Disponibile | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Disponibile | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

L'elenco delle API disponibili per ogni versione specifica è disponibile nell'elenco seguente.

API | 1.0-preview | 1.0
--- | --- | ---
[Trasmettere a tutti](#broadcast) | **&#x2713;** | **&#x2713;**
[Trasmettere a un gruppo](#broadcast-group) | **&#x2713;** | **&#x2713;**
Trasmettere ad alcuni gruppi | **&#x2713;** (deprecato) | `N / A`
[Inviare a un utente](#send-user) | **&#x2713;** | **&#x2713;**
Inviare ad alcuni utenti | **&#x2713;** (deprecato) | `N / A`
[Aggiunta di un utente a un gruppo](#add-user-to-group) | `N / A` | **&#x2713;**
[Rimozione di un utente da un gruppo](#remove-user-from-group) | `N / A` | **&#x2713;**
[Verificare l'esistenza dell'utente](#check-user-existence) | `N / A` | **&#x2713;**
[Rimuovere un utente da tutti i gruppi](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[Inviare a una connessione](#send-connection) | `N / A` | **&#x2713;**
[Aggiungere una connessione a un gruppo](#add-connection-to-group) | `N / A` | **&#x2713;**
[Rimuovere una connessione da un gruppo](#remove-connection-from-group) | `N / A` | **&#x2713;**
[Chiudere una connessione client](#close-connection) | `N / A` | **&#x2713;**
[Integrità dei servizi](#service-health) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Trasmettere a tutti

Versione | Metodo HTTP API | URL richiesta | Corpo della richiesta
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Vedere sopra.

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Trasmettere a un gruppo

Versione | Metodo HTTP API | URL richiesta | Corpo della richiesta
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Vedere sopra.

<a name="send-user"> </a>
### <a name="sending-to-a-user"></a>Invio a un utente

Versione | Metodo HTTP API | URL richiesta | Corpo della richiesta
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Vedere sopra.

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Aggiunta di un utente a un gruppo

Versione | Metodo HTTP API | URL richiesta
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Rimozione di un utente da un gruppo

Versione | Metodo HTTP API | URL richiesta
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>Verificare l'esistenza dell'utente in un gruppo

Versione dell'API | Metodo HTTP API | URL richiesta
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

Codice di stato della risposta | Descrizione
---|---
`200` | Utente esistente
`404` | Utente non esistente

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>Rimuovere un utente da tutti i gruppi

Versione dell'API | Metodo HTTP API | URL richiesta
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>Inviare il messaggio a una connessione

Versione dell'API | Metodo HTTP API | URL richiesta | Request Body
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>Aggiungere una connessione a un gruppo

Versione dell'API | Metodo HTTP API | URL richiesta
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>Rimuovere una connessione da un gruppo

Versione dell'API | Metodo HTTP API | URL richiesta
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>Chiudere una connessione client

Versione dell'API | Metodo HTTP API | URL richiesta
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>Integrità del servizio

Versione dell'API | Metodo HTTP API | URL richiesta
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

Codice di stato della risposta | Descrizione
---|---
`200` | Servizio funzionante
`5xx` | Errore del servizio

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsapi).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsapi).

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è imparato a usare l'API REST per trasmettere messaggi in tempo reale dal servizio SignalR ai client. Nella guida di avvio rapido successiva si apprenderà come sviluppare e distribuire Funzioni di Azure con il binding del servizio SignalR, che è basato sull'API REST.

> [!div class="nextstepaction"]
> [Sviluppare Funzioni di Azure con i binding del servizio SignalR](signalr-quickstart-azure-functions-csharp.md)
