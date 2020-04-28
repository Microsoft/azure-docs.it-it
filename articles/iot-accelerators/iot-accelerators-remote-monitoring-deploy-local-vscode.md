---
title: Distribuire la soluzione di monitoraggio remoto localmente-Visual Studio Code-Azure | Microsoft Docs
description: Questa guida pratica illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto nel computer locale tramite Visual Studio Code a scopo di test e sviluppo.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73890949"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Distribuire l'acceleratore di soluzioni di monitoraggio remoto localmente - Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Questo articolo illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale a scopo di test e sviluppo. Si apprenderà come eseguire i microservizi in Visual Studio Code. Una distribuzione di microservizi locale usa i servizi cloud seguenti: hub Internet delle cose, Cosmos DB, analisi di flusso di Azure e Azure Time Series Insights.

## <a name="prerequisites"></a>Prerequisiti

Per distribuire i servizi di Azure usati dall'acceleratore di soluzioni di monitoraggio remoto, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Impostazioni del computer

Per completare la distribuzione in locale, è necessario che nel computer di sviluppo locale siano installati gli strumenti seguenti:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Estensione C# di VS Code](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) - Questo software è un prerequisito per l'interfaccia della riga di comando di PCS usata dagli script per creare le risorse di Azure. Non usare Node.js v10.

> [!NOTE]
> Visual Studio Code è disponibile per Windows, Mac e Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Eseguire i microservizi

In questa sezione vengono eseguiti i microservizi di monitoraggio remoto. Eseguire l'interfaccia utente Web in modo nativo, il servizio di simulazione dispositivi in Docker e i microservizi in Visual Studio Code.

### <a name="build-the-code"></a>Compilare il codice

Passare ad azure-iot-pcs-remote-monitoring-dotnet\services nel prompt dei comandi ed eseguire i comandi seguenti per compilare il codice.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Distribuire tutti gli altri microservizi nel computer locale

I passaggi seguenti illustrano come eseguire i microservizi di monitoraggio remoto in Visual Studio Code:

1. Avviare Visual Studio Code.
1. In VS Code aprire la cartella **Azure-le cose-PCS-Remote-Monitoring-DotNet** .
1. Creare una nuova cartella denominata **. VSCODE** nella cartella **Azure--PCS-Remote-Monitoring-DotNet** .
1. Copiare i file **Launch. JSON** e **Tasks. JSON** da services\scripts\local\launch\idesettings\vscode alla cartella **. VSCODE** appena creata.
1. Aprire il **Pannello debug** in vs code ed eseguire la configurazione **Esegui tutti i microservizi** . Questa configurazione esegue il microservizio di simulazione dispositivi in Docker ed esegue gli altri microservizi nel debugger.

L'output dell'esecuzione di **Run all microsoervices** nel Console di debug ha un aspetto simile al seguente:

[![Distribuire i microservizi locali](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Eseguire l'interfaccia utente Web

In questo passaggio si avvia l'interfaccia utente Web. Passare alla cartella **azure-iot-pcs-remote-monitoring-dotnet\webui** nella copia locale ed eseguire i comandi seguenti:

```cmd
npm install
npm start
```

Al termine dell'avvio, il browser Visualizza la pagina **http:\//localhost: 3000/dashboard**. Gli errori in questa pagina sono previsti. Per visualizzare l'applicazione senza errori, completare la procedura seguente.

### <a name="configure-and-run-nginx"></a>Configurare ed eseguire NGINX

Configurare un server proxy inverso per collegare l'applicazione Web e i microservizi in esecuzione nel computer locale:

* Copiare il file **nginx.conf** dalla cartella **webui\scripts\localhost** locale nella directory di installazione **nginx\conf**.
* Eseguire **nginx**.

Per altre informazioni sull'esecuzione **nginx**, vedere [nginx per Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Connettersi al dashboard

Per accedere al dashboard della soluzione di monitoraggio remoto, passare a\/http:/localhost: 9000 nel browser.

## <a name="clean-up"></a>Eseguire la pulizia

Per evitare l'addebito di costi non necessari, al termine dei test rimuovere i servizi cloud dalla sottoscrizione di Azure. Per rimuovere i servizi, passare al [portale di Azure](https://ms.portal.azure.com) ed eliminare il gruppo di risorse creato dallo script **start.cmd**.

È anche possibile eliminare la copia locale del repository per il monitoraggio remoto che è stata creata clonando il codice sorgente da GitHub.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione](quickstart-remote-monitoring-deploy.md).
