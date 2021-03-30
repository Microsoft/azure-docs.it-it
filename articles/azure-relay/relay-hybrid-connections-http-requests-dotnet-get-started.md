---
title: Connessioni ibride di inoltro di Azure-richieste HTTP in .NET
description: Scrivere un'applicazione console C# per le richieste HTTP per Connessioni ibride di Inoltro di Azure in .NET.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 7a11abb984da3601a4d6aa921224e01f94d0871c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88922583"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Introduzione alle richieste HTTP per Connessioni ibride di Inoltro di Azure in .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

In questa guida introduttiva vengono create applicazioni mittente e ricevitore .NET che inviano e ricevono messaggi usando il protocollo HTTP. Le applicazioni usano la funzionalità Connessioni ibride di Inoltro di Azure. Per informazioni di carattere generale su Inoltro di Azure, vedere [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md). 

In questa guida introduttiva vengono completati i passaggi seguenti:

1. Creare uno spazio dei nomi di inoltro usando il portale di Azure.
2. Creare una connessione ibrida nello spazio dei nomi usando il portale di Azure.
3. Scrivere un'applicazione console server (listener) per ricevere messaggi.
4. Scrivere un'applicazione console client (mittente) per inviare messaggi.
5. Eseguire le applicazioni. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* [Visual Studio 2015 o versione successiva](https://www.visualstudio.com). Negli esempi di questa esercitazione viene usato Visual Studio 2017.
* Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-namespace"></a>Creare uno spazio dei nomi
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Creare una connessione ibrida
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Creare un'applicazione server (listener)
In Visual Studio scrivere un'applicazione console in C# per ascoltare e ricevere messaggi dall'inoltro.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Creare un'applicazione client (mittente)
In Visual Studio scrivere un'applicazione console in C# per inviare messaggi all'inoltro.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni
1. Eseguire l'applicazione server. Nella finestra della console viene visualizzato il testo seguente:

    ```
    Online
    Server listening
    ```
1. Eseguire l'applicazione client. Viene visualizzato `hello!` nella finestra client. Il client ha inviato una richiesta HTTP al server e il server ha risposto con `hello!`. 
3. A questo punto, per chiudere le finestre della console, premere **INVIO** in entrambe le finestre della console. 

Congratulazioni, è stata creata un'applicazione Connessioni ibride completa.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono state create applicazioni client e server .NET che usano HTTP per inviare e ricevere messaggi. La funzionalità Connessioni ibride di Inoltro di Azure supporta anche l'uso di WebSocket per l'invio e la ricezione di messaggi. Per informazioni sull'uso di WebSocket con Connessioni ibride di Inoltro di Azure, vedere la [guida introduttiva di WebSocket](relay-hybrid-connections-dotnet-get-started.md).

In questa guida introduttiva è stato usato .NET Framework per creare applicazioni client e server. Per informazioni su come scrivere applicazioni client e server con Node.js, vedere la [guida introduttiva di WebSocket per Node.js](relay-hybrid-connections-node-get-started.md) o la [guida introduttiva di HTTP per Node.js](relay-hybrid-connections-http-requests-dotnet-get-started.md).
