---
title: Azure Service Fabric con VS Code Introduzione
description: Questo articolo fornisce una panoramica sulla creazione di applicazioni di Service Fabric usando Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-javascript
ms.openlocfilehash: 1480b5e1ddab86761adb1c7caef808de89a3d947
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87429000"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric per Visual Studio Code

L'[estensione Reliable Services di Service Fabric per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) fornisce gli strumenti necessari a creare, compilare ed eseguire il debug di applicazioni di Service Fabric su sistemi operativi Windows, Linux e macOS.

Questo articolo offre una panoramica dei requisiti e dell'installazione dell'estensione, nonché dell'uso dei vari comandi forniti dall'estensione. 

> [!IMPORTANT]
> Le applicazioni Java di Service Fabric possono essere sviluppate su computer Windows, ma possono essere distribuite solo in cluster Linux di Azure. Il debug di applicazioni Java non è supportato in Windows.

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti seguenti devono essere installati in tutti gli ambienti.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](./service-fabric-get-started.md)
* Generatori yeoman: installare i generatori appropriati per l'applicazione

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

I prerequisiti seguenti devono essere installati negli ambienti di sviluppo Java:

* [Java SDK](https://aka.ms/azure-jdks) (versione 1.8)
* [Gradle](https://gradle.org/install/)
* [Debugger per l'estensione di Visual Studio Code Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug), necessario per il debug di servizi Java. Il debug di servizi Java è supportato solo in Linux. È possibile eseguire l'installazione facendo clic sull'icona delle estensioni nella **barra attività** in Visual Studio Code e ricercando l'estensione oppure dal marketplace di VS Code.

I prerequisiti seguenti devono essere installati negli ambienti di sviluppo .NET Core/C#:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (versione 2.0.0 o successiva)
* [C# per Visual Studio Code (con tecnologia OmniSharp), estensione di Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp), necessaria per eseguire il debug di servizi di linguaggio C#. È possibile eseguire l'installazione facendo clic sull'icona delle estensioni nella **barra attività** in Visual Studio Code e ricercando l'estensione oppure dal marketplace di VS Code.

## <a name="setup"></a>Configurazione

1. Aprire Visual Studio Code.
2. Fare clic sull'icona dell'estensione nella **barra attività** sulla sinistra di Visual Studio Code. Cercare "Service Fabric". Fare clic su **Installa** per l'estensione di Reliable Services di Service Fabric.

## <a name="commands"></a>Comandi
L'estensione di Reliable Services di Service Fabric per Visual Studio Code offre molti comandi per consentire agli sviluppatori di creare e distribuire i progetti di Service Fabric. È possibile chiamare i comandi dal **riquadro comandi** selezionando `(Ctrl + Shift + p)`, quindi digitando il nome del comando nella barra degli input e scegliendo il comando desiderato dall'elenco dei messaggi di richiesta. 

* Service Fabric: Creare l'applicazione 
* Service Fabric: Pubblicare l'applicazione 
* Service Fabric: Distribuire l'applicazione 
* Service Fabric: Rimuovere l'applicazione  
* Service Fabric: Compilare l'applicazione 
* Service Fabric: Compilare l'applicazione 

### <a name="service-fabric-create-application"></a>Service Fabric: Creare l'applicazione

Il comando **Service Fabric: Creare l'applicazione** crea una nuova applicazione di Service Fabric nell'area di lavoro corrente. A seconda che i generatori yeoman siano installati nel computer di sviluppo, è possibile creare diversi tipi di applicazioni di Service Fabric, inclusi i progetti Java, C#, contenitore e guest. 

1.  Selezionare il comando **Service Fabric: Crea applicazione**
2.  Selezionare il tipo di nuova applicazione di Service Fabric. 
3.  Immettere il nome dell'applicazione da creare
3.  Selezionare il tipo di servizio che si vuole aggiungere all'applicazione di Service Fabric. 
4.  Seguire le istruzioni per assegnare un nome al servizio. 
5.  Viene visualizzata la nuova applicazione di Service Fabric nell'area di lavoro.
6.  Aprire la cartella dell'applicazione in modo che diventi la cartella radice dell'area di lavoro. È possibile continuare l'esecuzione dei comandi da qui.

### <a name="service-fabric-add-service"></a>Service Fabric: Aggiungere un servizio
Il comando **Service Fabric: Aggiungere un servizio** aggiunge un nuovo servizio a un'applicazione Service Fabric esistente. L'applicazione a cui verrà aggiunto il servizio deve essere la directory radice dell'area di lavoro. 

1.  Selezionare il comando **Service Fabric: Aggiungi servizio** .
2.  Selezionare il tipo di nuova applicazione di Service Fabric. 
3.  Selezionare il tipo di servizio che si vuole aggiungere all'applicazione di Service Fabric. 
4.  Seguire le istruzioni per assegnare un nome al servizio. 
5.  Il nuovo servizio viene visualizzato nella directory del progetto. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Pubblicare l'applicazione
Il comando **Service Fabric: Pubblicare l'applicazione** distribuisce l'applicazione di Service Fabric in un cluster remoto. Il cluster di destinazione può essere protetto o meno. Se i parametri non sono impostati in cloud.json, l'applicazione viene distribuita nel cluster locale.

1.  La prima volta che viene compilata l'applicazione, viene generato un file cloud.json nella directory del progetto.
2.  Inserire i valori per il cluster da connettere nel file cloud.json.
3.  Selezionare il comando **Service Fabric: Pubblicare l'applicazione**.
4.  Consente di visualizzare il cluster di destinazione con Service Fabric Explorer per verificare che sia stata installata l'applicazione. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Distribuire l'applicazione (localhost)
Il comando **Service Fabric: Distribuire l'applicazione** distribuisce l'applicazione di Service Fabric in un cluster locale. Assicurarsi che il cluster locale sia in esecuzione prima di usare il comando. 

1. Selezionare il comando **Service Fabric: Distribuire l'applicazione**
2. Visualizzare il cluster locale con Service Fabric Explorer (http: \/ /localhost: 19080/Explorer) per verificare che l'applicazione sia stata installata. Questa operazione potrebbe richiedere tempo.
3. È anche possibile usare il comando **Service Fabric: Pubblicare l'applicazione** senza alcun parametro impostato nel file cloud.json da distribuire in un cluster locale.

> [!NOTE]
> La distribuzione di applicazioni Java nel cluster locale non è supportata nei computer Windows.

### <a name="service-fabric-remove-application"></a>Service Fabric: Rimuovere l'applicazione
Il comando **Service Fabric: Rimuovere l'applicazione** rimuove un'applicazione di Service Fabric dal cluster che è stata distribuita in precedenza per l'uso dell'estensione di Visual Studio Code. 

1.  Selezionare il comando **Service Fabric: Rimuovere l'applicazione**.
2.  Consente di visualizzare il cluster con Service Fabric Explorer per confermare la rimozione dell'applicazione. Questa operazione potrebbe richiedere tempo.

### <a name="service-fabric-build-application"></a>Service Fabric: Compilare l'applicazione
Il comando **Service Fabric: compila applicazione** può compilare applicazioni Java o C# Service Fabric. 

1.  Assicurarsi di essere nella cartella radice dell'applicazione prima di eseguire questo comando. Il comando identifica il tipo di applicazione (C# o Java) e compila l'applicazione di conseguenza.
2.  Selezionare il comando **Service Fabric: Compilare l'applicazione**.
3.  L'output del processo di compilazione viene scritto nel terminale integrato.

### <a name="service-fabric-clean-application"></a>Service Fabric: Compilare l'applicazione
Il comando **Service Fabric: Pulire l'applicazione** elimina tutti i file con estensione jar e le librerie native che sono stati generati dalla compilazione. Valido per solo le applicazioni Java. 

1.  Assicurarsi di essere nella cartella radice dell'applicazione prima di eseguire questo comando. 
2.  Selezionare il comando **Service Fabric: Pulire l'applicazione**.
3.  L'output del processo di pulizia viene scritto nel terminale integrato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [sviluppare applicazioni C# di Service Fabric ed eseguirne il debug con Visual Studio Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Informazioni su come [sviluppare applicazioni Java di Service Fabric ed eseguirne il debug con Visual Studio Code](./service-fabric-develop-java-applications-with-vs-code.md).
