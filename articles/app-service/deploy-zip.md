---
title: Distribuire il codice con un file ZIP o WAR
description: Informazioni su come distribuire l'app in Servizio app di Azure con un file ZIP (o un file WAR per sviluppatori Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9f59576ea66b72a492e1e6c665a51258861842dd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833019"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Distribuire l'app in Servizio app di Azure con un file ZIP o WAR

Questo articolo descrive come usare un file ZIP o WAR per distribuire l'app Web in [Servizio app di Azure](overview.md). 

La distribuzione tramite file ZIP utilizza lo stesso servizio Kudu usato per le distribuzioni basate su integrazione continua. Kudu supporta le funzionalità seguenti per la distribuzione tramite file ZIP: 

- Eliminazione dei file rimasti da una distribuzione precedente.
- Opzione per attivare il processo di compilazione predefinito, che include il ripristino del pacchetto.
- Personalizzazione della distribuzione, inclusa l'esecuzione di script di distribuzione.  
- Log di distribuzione. 
- Un limite di dimensioni del file di 2048 MB.

Per altre informazioni, vedere [Documentazione Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

La distribuzione del file WAR distribuisce il file [WAR](https://wikipedia.org/wiki/WAR_(file_format)) in Servizio app di Azure per l'esecuzione dell'app Web Java. Vedere [Distribuire il file WAR](#deploy-war-file).

> [!NOTE]
> Quando si usa , i file verranno copiati solo se i `ZipDeploy` timestamp non corrispondono a quanto già distribuito. La generazione di un file ZIP tramite un processo di compilazione che memorizza gli output nella cache può comportare distribuzioni più veloci. Per [altre informazioni, vedere Distribuzione da un file ZIP o da un URL.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo, [creare un'app](./index.yml)del servizio app o usare un'app creata per un'altra esercitazione.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
L'endpoint precedente non funziona al momento per i servizi app Linux. In alternativa, è consigliabile usare FTP [o l'API di distribuzione ZIP.](faq-app-service-linux.md#continuous-integration-and-deployment)

## <a name="deploy-zip-file-with-azure-cli"></a>Distribuire il file ZIP con l'interfaccia della riga di comando di Azure

Distribuire il file ZIP caricato nell'app Web usando il comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip).  

L'esempio seguente distribuisce il file ZIP caricato. Quando si utilizza un'installazione locale dell'interfaccia della riga di comando di Azure, specificare il percorso del file ZIP locale per `--src`.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Questo comando distribuisce i file e le directory del file ZIP nella cartella predefinita dell'applicazione del servizio app (`\home\site\wwwroot`) e riavvia l'app.

Per impostazione predefinita, il motore di distribuzione presuppone che un file ZIP sia pronto per essere eseguito così com'è e non esegue alcuna automazione della compilazione. Per abilitare la stessa automazione della compilazione di una distribuzione [Git,](deploy-local-git.md)impostare `SCM_DO_BUILD_DURING_DEPLOYMENT` l'impostazione dell'app eseguendo il comando seguente nel [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Per altre informazioni, vedere [Documentazione Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Distribuire il file WAR

Per distribuire un file WAR nel servizio app, inviare una richiesta POST a `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . La richiesta POST deve contenere il file WAR nel corpo del messaggio. Le credenziali di distribuzione per l'app vengono fornite nella richiesta usando l'autenticazione di base HTTP.

Usare sempre `/api/wardeploy` quando si distribuiscono file WAR. Questa API espanderà il file WAR e lo inserirà nell'unità file condivisa. L'uso di altre API di distribuzione può comportare un comportamento incoerente. 

Per l'autenticazione HTTP di base sono necessarie le credenziali di distribuzione del servizio app. Per informazioni su come impostare le credenziali di distribuzione, vedere [Impostare e reimpostare le credenziali a livello di utente](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Con cURL

L'esempio seguente usa lo strumento cURL per distribuire un file WAR. Sostituire i segnaposto `<username>`, `<war-file-path>` e `<app-name>`. Quando richiesto da cURL, digitare la password.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Con PowerShell

L'esempio seguente usa [Publish-AzWebapp per](/powershell/module/az.websites/publish-azwebapp) caricare il file war. Sostituire i segnaposto `<group-name>`, `<app-name>` e `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passaggi successivi

Per scenari di distribuzione più avanzati, provare a eseguire la [distribuzione in Azure con Git](deploy-local-git.md). La distribuzione basata su Git in Azure abilita il controllo della versione, il ripristino del pacchetto, MSBuild e altro ancora.

## <a name="more-resources"></a>Altre risorse

* [Kudu: distribuzione da un file ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenziali per la distribuzione del Servizio app di Azure](deploy-ftp.md)
