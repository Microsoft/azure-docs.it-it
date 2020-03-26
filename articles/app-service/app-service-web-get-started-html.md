---
title: "Guida introduttiva: Creare un'app Web HTML statica"
description: Distribuire la prima app Hello World HTML nel servizio app di Azure in pochi minuti. A questo scopo si usa Git, uno dei molti strumenti disponibili per eseguire distribuzioni nel servizio app.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: e975708eddad9b9b4a07b49e1f753d604d77ff94
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047563"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Creare un'app Web HTML statica in Azure

[Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Questa guida introduttiva illustra come distribuire un sito HTML e CSS di base nel servizio app di Azure. Questa guida introduttiva verrà completata in [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), ma gli stessi comandi possono essere eseguiti anche in locale con l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

![Home page dell'app di esempio](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

In Cloud Shell creare una directory quickstart e passare ad essa.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Eseguire quindi il comando seguente per clonare il repository dell'app di esempio nella directory quickstart.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>Creare un'app Web

Passare alla directory contenente il codice di esempio ed eseguire il comando `az webapp up`.

Nell'esempio seguente sostituire <app_name> con un nome di app univoco.

```bash
cd html-docs-hello-world
```

```azurecli
az webapp up --location westeurope --name <app_name> --html
```

Il comando `az webapp up` esegue le azioni seguenti:

- Crea un gruppo di risorse predefinito.

- Crea un piano di servizio app predefinito.

- Crea un'app con il nome specificato.

- [Distribuisce con zipdeploy](https://docs.microsoft.com/azure/app-service/deploy-zip) i file dalla directory di lavoro corrente all'app Web.

L'esecuzione del comando può richiedere alcuni minuti. Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:

```json
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "westeurope",
  "name": "<app_name>",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/<username>/quickstart/html-docs-hello-world ",
  < JSON data removed for brevity. >
}
```

Annotare il valore `resourceGroup`. È necessario per la sezione [Pulire le risorse](#clean-up-resources).

## <a name="browse-to-the-app"></a>Passare all'app

In un browser passare all'URL dell'app: `http://<app_name>.azurewebsites.net`.

La pagina è in esecuzione come un'app Web del servizio app di Azure.

![Home page dell'app di esempio](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

**Congratulazioni** La distribuzione della prima app HTML nel Servizio app è stata completata.

## <a name="update-and-redeploy-the-app"></a>Aggiornare e ridistribuire l'app

In Cloud Shell digitare `nano index.html` per aprire l'editor di testo Nano. Nel tag di intestazione `<h1>` modificare "Azure App Service - Sample Static HTML Site" in "Azure App Service", come mostrato più avanti.

![Nano index.html](media/app-service-web-get-started-html/nano-index-html.png)

Salvare le modifiche e uscire da Nano. Usare il comando `^O` per salvare e `^X` per uscire.

Ora l'applicazione verrà ridistribuita con lo stesso comando `az webapp up`.

```azurecli
az webapp up --location westeurope --name <app_name> --html
```

Al termine della distribuzione, tornare alla finestra del browser aperta nel passaggio **Passare all'app** e aggiornare la pagina.

![Home page dell'app di esempio aggiornata](media/app-service-web-get-started-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>Gestire la nuova app Azure

Per gestire l'app Web create, nel [portale di Azure](https://portal.azure.com), cercare e selezionare **Servizi app**. 

![Selezionare Servizi app nel portale di Azure](./media/app-service-web-get-started-html/portal0.png)

Nella pagina **Servizi app** selezionare il nome dell'app di Azure.

![Passaggio all'app di Azure nel portale](./media/app-service-web-get-started-html/portal1.png)

Verrà visualizzata la pagina di panoramica dell'app Web. Qui è possibile eseguire attività di gestione di base come l'esplorazione, l'arresto, l'avvio, il riavvio e l'eliminazione dell'app.

![Pannello del servizio app nel portale di Azure](./media/app-service-web-get-started-html/portal2.png)

Il menu a sinistra fornisce varie pagine per la configurazione dell'app.

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Se si ritiene che queste risorse non saranno necessarie in futuro, eliminare il gruppo di risorse eseguendo questo comando in Cloud Shell. Si ricorda che il nome del gruppo di risorse è stato generato automaticamente nel passaggio [Creare un'app Web](#create-a-web-app).

```azurecli
az group delete --name appsvc_rg_Windows_westeurope
```

L'esecuzione del comando può richiedere un minuto.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire il mapping di un dominio personalizzato](app-service-web-tutorial-custom-domain.md)
