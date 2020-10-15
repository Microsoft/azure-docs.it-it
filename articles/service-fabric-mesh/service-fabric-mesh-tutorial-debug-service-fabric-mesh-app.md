---
title: Eseguire il debug di un'app Web di Azure Service Fabric Mesh in esecuzione nell'ambiente locale
description: In questa esercitazione viene eseguito il debug di un'applicazione Azure Service Fabric Mesh in esecuzione nel cluster locale.
author: georgewallace
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 56cc8b4010dc17cf2b723a72898034de8d6a7175
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843295"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Esercitazione: eseguire il debug di un'applicazione Azure Service Fabric Mesh in esecuzione nel cluster di sviluppo locale

Questa esercitazione è la seconda parte di una serie e illustra come compilare ed eseguire il debug di un'applicazione Azure Service Fabric Mesh nel cluster di sviluppo locale.

In questa esercitazione si apprenderà:

> [!div class="checklist"]
> * Cosa accade quando si crea un'applicazione Azure Service Fabric Mesh
> * Come impostare un punto di interruzione per osservare una chiamata da servizio a servizio

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'app Service Fabric Mesh in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Eseguire il debug di un'app Service Fabric Mesh in esecuzione nel cluster di sviluppo locale
> * [Distribuire un'app Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Aggiornare un'app Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Pulire le risorse di Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Assicurarsi di [configurare l'ambiente di sviluppo](service-fabric-mesh-howto-setup-developer-environment-sdk.md), installando tra l'altro il runtime di Service Fabric, l'SDK, Docker e Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Scaricare l'applicazione attività di esempio

Se non si è creata l'applicazione attività di esempio nella [prima parte di questa serie di esercitazioni](service-fabric-mesh-tutorial-create-dotnetcore.md), è possibile scaricarla. In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

L'applicazione si trova nella directory `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Compilare ed eseguire il debug nel cluster locale

Subito dopo il caricamento del progetto nel cluster locale viene compilata e distribuita automaticamente un'immagine Docker. Il processo potrebbe richiedere un po' di tempo. Per monitorare lo stato nel riquadro **Output** di Visual Studio, impostare l'elenco a discesa **Mostra output di** del riquadro Output su **Strumenti di Service Fabric**.

Premere **F5** per compilare ed eseguire il servizio in locale. Durante l'esecuzione e il debug del progetto in locale, Visual Studio:

* Si assicurerà che Docker per Windows sia in esecuzione e che sia impostato in modo da usare Windows come sistema operativo del contenitore.
* Scaricherà eventuali immagini di base mancanti di Docker. Questa parte potrebbe richiedere tempo.
* Compilerà o ricompilerà l'immagine di Docker usata per ospitare il progetto di codice.
* Distribuirà ed eseguirà il contenitore nel cluster di sviluppo locale di Service Fabric.
* Eseguirà i servizi e raggiungerà gli eventuali punti di interruzione impostati.

Quando la distribuzione locale è completata e l'app è in esecuzione in Visual Studio, viene aperta una finestra del browser con una pagina Web di esempio predefinita.

## <a name="debugging-tips"></a>Suggerimenti per il debug

Per rendere molto più rapida la prima esecuzione del debug (F5), seguire le istruzioni in [Ottimizzare le prestazioni di Visual Studio](service-fabric-mesh-howto-optimize-vs.md).

Attualmente si verifica un problema a causa del quale la chiamata a `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` provoca un errore di connessione al servizio. Questa situazione può verificarsi ogni volta che l'indirizzo IP dell'host cambia. Per risolvere il problema:

1. Rimuovere l'app dal cluster locale (in Visual Studio **Compila** > **Pulisci soluzione**).
2. Da Local Cluster Manager (Gestione cluster locale) di Service Fabric selezionare **Stop Local Cluster** (Arresta cluster locale) e quindi **Start Local Cluster** (Avvia cluster locale).
3. Ridistribuire l'app (in Visual Studio **F5**).

Se viene visualizzato l'errore **No Service Fabric local cluster is running** (Nessun cluster locale di Service Fabric è in esecuzione), assicurarsi che Service Fabric Local Cluster Manager (LCM) sia in esecuzione, fare clic con il pulsante destro del mouse sull'icona LCM sulla barra delle applicazioni e quindi fare clic su **Start Local Cluster** (Avvia cluster locale). Dopo l'avvio, tornare a Visual Studio e premere **F5**.

Se viene visualizzato un errore **404** all'avvio dell'app, è possibile che le variabili di ambiente in **service.yaml** non siano corrette. Assicurarsi che `ApiHostPort` e `ToDoServiceName` siano impostati conformemente alle istruzioni della sezione [Creare le variabili di ambiente](./service-fabric-mesh-tutorial-create-dotnetcore.md#create-environment-variables).

Se sono presenti errori di compilazione in **service.yaml**, assicurarsi che per impostare i rientri delle righe vengano usati gli spazi e non le tabulazioni. Inoltre, per il momento, è necessario compilare l'app usando le impostazioni locali inglese.

### <a name="debug-in-visual-studio"></a>Eseguire il debug in Visual Studio

Per il debug di un'applicazione Service Fabric Mesh in Visual Studio viene usato un cluster di sviluppo locale di Service Fabric. Per scoprire in che modo gli elementi attività vengono recuperati dal servizio back-end, eseguire il debug nel metodo OnGet().
1. Nel progetto **WebFrontEnd** aprire **Pages** > **Index.cshtml** > **Index.cshtml.cs** e impostare un punto di interruzione nel metodo **OnGet** (riga 17).
2. Nel progetto **ToDoService** aprire **TodoController.cs** e impostare un punto di interruzione nel metodo **Get** (riga 15).
3. Tornare al browser e aggiornare la pagina. È stato raggiunto il punto di interruzione nel metodo `OnGet()` del front-end Web. È possibile esaminare la variabile `backendUrl` per conoscere la combinazione delle variabili di ambiente definite nel file **service.yaml** nell'URL usato per contattare il servizio back-end.
4. Eseguire l'istruzione/routine (F10) della chiamata `client.GetAsync(backendUrl).GetAwaiter().GetResult())` per raggiungere il punto di interruzione `Get()` del controller. In questo metodo è possibile scoprire in che modo l'elenco degli elementi attività viene recuperato dall'elenco in memoria.
5. Al termine, premere **MAIUSC+F5** per arrestare il debug del progetto in Visual Studio.

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso:

> [!div class="checklist"]
> * Cosa accade quando si crea un'applicazione Azure Service Fabric Mesh
> * Come impostare un punto di interruzione per osservare una chiamata da servizio a servizio

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Distribuire un'app Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
