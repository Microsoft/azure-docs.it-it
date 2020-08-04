---
title: "Eseguire il debug e l'iterazione in Kubernetes: Visual Studio Code e Node.js"
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Questa guida di avvio rapido illustra come usare Azure Dev Spaces e Visual Studio Code per eseguire il debug e l'iterazione rapida di un'applicazione Node.js nel servizio Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 370d49b5287a4d5beafc8c2cc5d8f48c269715db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006945"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-nodejs---azure-dev-spaces"></a>Avvio rapido: Eseguire il debug e l'iterazione in Kubernetes con Visual Studio Code e Node.js - Azure Dev Spaces

In questo argomento di avvio rapido si configura Azure Dev Spaces con un cluster Kubernetes gestito e si usa un'app Node.js in Visual Studio Code per sviluppare ed eseguire il debug in modo iterativo del codice nei contenitori. Azure Dev Spaces consente di eseguire il debug e i test di tutti i componenti dell'applicazione nel servizio Azure Kubernetes con una configurazione minima del computer di sviluppo. 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [La versione più recente di Node.js](https://nodejs.org/download/).
- [Visual Studio Code](https://code.visualstudio.com/download).
- L'estensione [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) per Visual Studio Code.
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Creare un cluster del servizio Azure Kubernetes

È necessario creare un cluster del servizio Azure Kubernetes in un'[area supportata][supported-regions]. I comandi seguenti creano un gruppo di risorse denominato *MyResourceGroup* e un cluster del servizio Azure Kubernetes denominato *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Abilitare Azure Dev Spaces nel cluster del servizio Azure Kubernetes

Usare il comando `use-dev-spaces` per abilitare Dev Spaces nel cluster del servizio Azure Kubernetes e seguire i prompt. Il comando seguente abilita Dev Spaces nel cluster *MyAKS* all'interno del gruppo *MyResourceGroup* e crea uno spazio di Dev Spaces *predefinito*.

> [!NOTE]
> Il comando `use-dev-spaces` installerà anche l'interfaccia della riga di comando di Azure Dev Spaces se non è già installata. Non è possibile installare l'interfaccia della riga di comando di Azure Dev Spaces in Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Ottenere il codice dell'applicazione di esempio

In questo articolo verrà usata l'[applicazione di esempio Azure Dev Spaces](https://github.com/Azure/dev-spaces) per dimostrare l'uso di Azure Dev Spaces.

Clonare l'applicazione da GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Preparare l'applicazione di esempio in Visual Studio Code

Aprire Visual Studio Code, selezionare **File**, quindi **Apri**, passare alla directory *dev-spaces/samples/nodejs/getting-started/webfrontend* e selezionare **Apri**.

In Visual Studio Code è ora aperto il progetto *webfrontend*. Per eseguire l'applicazione nello spazio di sviluppo, generare gli asset per grafici Helm e Docker usando l'estensione Azure Dev Spaces nel riquadro comandi.

Per aprire il riquadro comandi in Visual Studio Code, selezionare **Visualizza** e quindi **Riquadro comandi**. Iniziare a digitare `Azure Dev Spaces` e selezionare **Azure Dev Spaces: Preparare i file di configurazione per Azure Dev Spaces**.

![Preparare i file di configurazione per Azure Dev Spaces](./media/common/command-palette.png)

Quando Visual Studio Code richiede anche di configurare l'endpoint pubblico, scegliere `Yes` per abilitare un endpoint pubblico.

![Selezione dell'endpoint pubblico](media/common/select-public-endpoint.png)

Questo comando consente di preparare il progetto per l'esecuzione in Azure Dev Spaces generando un Dockerfile e un grafico Helm. Genera inoltre una directory *.vscode* con la configurazione di debug alla radice del progetto.

> [!TIP]
> Il [Dockerfile e il grafico Helm](how-dev-spaces-works-prep.md#prepare-your-code) per il progetto vengono usati da Azure Dev Spaces per compilare ed eseguire il codice, ma è possibile modificare questi file se si vuole cambiare il modo in cui il progetto viene compilato ed eseguito.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Compilare ed eseguire il codice in Kubernetes da Visual Studio Code

Selezionare l'icona **Debug** a sinistra e quindi **Launch Server (AZDS)** (Avvia server AZDS) nella parte superiore.

![Comando Launch Server](media/get-started-node/debug-configuration-nodejs.png)

Questo comando compila ed esegue il servizio in Azure Dev Spaces. La finestra del **terminale** nella parte inferiore visualizza l'output della compilazione e gli URL per il servizio che esegue Azure Dev Spaces. La **Console di debug** visualizza l'output del log.

> [!Note]
> Se nel **riquadro comandi** non vengono visualizzati comandi di Azure Dev Spaces, assicurarsi di aver installato l'[estensione Visual Studio Code per Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Verificare inoltre di aver aperto la directory *dev-spaces/samples/nodejs/getting-started/webfrontend* in Visual Studio Code.

Per vedere il servizio in esecuzione, aprire l'URL pubblico.

Selezionare **Debug** e quindi **Arresta debug** per arrestare il debugger.

## <a name="update-code"></a>Aggiornare il codice

Per distribuire una versione aggiornata del servizio, è possibile aggiornare qualsiasi file del progetto ed eseguire di nuovo **Launch Server** (Avvia server). Ad esempio:

1. Se l'applicazione è ancora in esecuzione, selezionare **Debug** e quindi **Arresta debug** per arrestarla.
1. Aggiornare la [riga 13 di `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) in:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Salvare le modifiche.
1. Eseguire di nuovo **Launch Server** (Avvia server).
1. Passare al servizio in esecuzione e osservare le modifiche.
1. Selezionare **Debug** e quindi **Arresta debug** per arrestare l'applicazione.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Impostazione e uso di punti di interruzione per il debug

Avviare il servizio scegliendo **Launch Server (AZDS)** (Avvia server AZDS).

Tornare nella visualizzazione Explorer selezionando **Visualizza** e quindi **Explorer**. Aprire *server.js* e fare clic in un punto della riga 13 per posizionarvi il cursore. Per impostare un punto di interruzione premere **F9** oppure selezionare **Debug** e quindi **Attiva/Disattiva punto di interruzione**.

Aprire il servizio in un browser e notare che non vengono visualizzati messaggi. Tornare in Visual Studio Code e notare che la riga 13 è evidenziata. Il punto di interruzione impostato ha sospeso il servizio in corrispondenza della riga 13. Per riprendere il servizio, premere **F5** oppure selezionare **Debug** e quindi **Continua**. Tornare nel browser e notare che ora il messaggio è visualizzato.

Durante l'esecuzione del servizio in Kubernetes con un debugger collegato, si ha accesso completo alle informazioni di debug, ad esempio su stack di chiamate, variabili locali ed eccezioni.

Rimuovere il punto di interruzione posizionando il cursore sulla riga 13 di *server.js* e premendo **F9**.

Selezionare **Debug** e quindi **Arresta debug** per arrestare il debugger.

## <a name="update-code-from-visual-studio-code"></a>Aggiornare il codice da Visual Studio Code

Cambiare la modalità di debug in **Attach to a Server (AZDS)** (Collega a un server AZDS) e avviare il servizio:

![Screenshot dell'interfaccia utente di VS Code con l'opzione Attach to Server (Collega al server) selezionata.](media/get-started-node/attach-nodejs.png)

Questo comando compila ed esegue il servizio in Azure Dev Spaces. Avvia inoltre un processo [nodemon](https://nodemon.io) nel contenitore del servizio e lo collega a VS Code. Il processo *nodemon* consente il riavvio automatico quando vengono apportate modifiche al codice sorgente, velocizzando lo sviluppo del ciclo interno in modo simile allo sviluppo nel computer locale.

Dopo averlo avviato, accedere e interagire con il servizio nel browser.

Mentre il servizio è in esecuzione, tornare in VS Code e aggiornare la riga 13 di *server.js*. Ad esempio:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Salvare il file e tornare nel servizio in un browser. Interagire con il servizio e notare che è visualizzato il messaggio aggiornato.

Durante l'esecuzione di *nodemon*, il processo Node viene riavviato automaticamente non appena vengono rilevate modifiche al codice. Questo processo di riavvio automatico è simile all'esperienza di modifica e riavvio del servizio nel computer locale, offrendo un'esperienza di sviluppo del ciclo interno.

## <a name="clean-up-your-azure-resources"></a>Pulire le risorse di Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come Azure Dev Spaces consente di sviluppare applicazioni più complesse in più contenitori e su come è possibile semplificare lo sviluppo collaborativo usando versioni o rami diversi del codice in spazi diversi.

> [!div class="nextstepaction"]
> [Uso di più contenitori e sviluppo in team](multi-service-nodejs.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
