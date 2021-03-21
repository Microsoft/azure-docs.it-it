---
title: Configurare Rendering remoto per Unity
description: Come inizializzare il rendering remoto di Azure in un progetto Unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 48f01058d8e879a9610e76638215214c059982fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594215"
---
# <a name="set-up-remote-rendering-for-unity"></a>Configurare Rendering remoto per Unity

Per abilitare il rendering remoto di Azure (ARR) in Unity, vengono forniti metodi dedicati che interessano alcuni aspetti specifici di Unity.

## <a name="startup-and-shutdown"></a>Avvio e arresto

Per inizializzare il rendering remoto, usare `RemoteManagerUnity` . Questa classe chiama l'oggetto generico, `RenderingConnection` ma implementa già i dettagli specifici di Unity. Unity USA ad esempio un sistema di coordinate specifico. Quando `RemoteManagerUnity.Initialize` si chiama, viene configurata la convenzione corretta. La chiamata richiede anche di fornire la fotocamera Unity da usare per la visualizzazione del contenuto sottoposto a rendering remoto.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Per arrestare il rendering remoto, chiamare `RemoteManagerStatic.ShutdownRemoteRendering()` .

Dopo `RenderingSession` la creazione e la scelta di una sessione di rendering primaria, è necessario registrarla con `RemoteManagerUnity` :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Codice di esempio completo

Il codice seguente illustra tutti i passaggi necessari per inizializzare il rendering remoto di Azure in Unity:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
SessionConfiguration sessionConfig = new SessionConfiguration();
// ... fill out sessionConfig ...
RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

// start a session
CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(new RenderingSessionCreationOptions(RenderingSessionVmSize.Standard, 0, 30));
RenderingSession session = result.Session;

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

await session.ConnectAsync(new RendererInitOptions());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Funzioni di convenienza

### <a name="session-state-events"></a>Eventi dello stato della sessione

`RemoteManagerUnity.OnSessionUpdate` genera eventi per quando cambia lo stato della sessione. per informazioni dettagliate, vedere la documentazione del codice.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` è un componente facoltativo per semplificare la configurazione e la gestione delle sessioni. Contiene opzioni per arrestare automaticamente la sessione quando l'applicazione è in uscita o la modalità di riproduzione viene chiusa nell'editor, oltre a rinnovare automaticamente il lease della sessione quando necessario. Memorizza nella cache i dati, ad esempio le proprietà della sessione (vedere la relativa `LastProperties` variabile) ed espone gli eventi per le modifiche dello stato della sessione e gli errori di sessione.

Non può essere presente più di un'istanza di `ARRServiceUnity` alla volta. È concepito per iniziare più rapidamente implementando alcune funzionalità comuni. Per un'applicazione di dimensioni maggiori, può essere preferibile eseguire tali operazioni, tuttavia.

Per un esempio di come configurare e usare, `ARRServiceUnity` vedere [esercitazione: visualizzazione di modelli con rendering remoto](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="next-steps"></a>Passaggi successivi

* [Installare il pacchetto Rendering remoto per Unity](install-remote-rendering-unity-package.md)
* [Esercitazione: Visualizzazione di un modello di cui è stato eseguito il rendering in remoto](../../tutorials/unity/view-remote-models/view-remote-models.md)
