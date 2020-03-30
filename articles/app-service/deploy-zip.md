---
title: Distribuire codice con un file postale o WAR
description: Informazioni su come distribuire l'app in Servizio app di Azure con un file ZIP (o un file WAR per sviluppatori Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945163"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Distribuire l'app in Servizio app di Azure con un file ZIP o WAR

Questo articolo descrive come usare un file ZIP o WAR per distribuire l'app Web in [Servizio app di Azure](overview.md). 

La distribuzione tramite file ZIP utilizza lo stesso servizio Kudu usato per le distribuzioni basate su integrazione continua. Kudu supporta le funzionalità seguenti per la distribuzione tramite file ZIP: 

- Eliminazione dei file rimasti da una distribuzione precedente.
- Opzione per attivare il processo di compilazione predefinito, che include il ripristino del pacchetto.
- Personalizzazione della distribuzione, inclusa l'esecuzione di script di distribuzione.  
- Log di distribuzione. 
- Un limite di dimensione del file di 2048 MB.

Per altre informazioni, vedere [Documentazione Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

La distribuzione del file WAR distribuisce il file [WAR](https://wikipedia.org/wiki/WAR_(file_format)) in Servizio app di Azure per l'esecuzione dell'app Web Java. Vedere [Distribuire il file WAR](#deploy-war-file).

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo, [creare un'app del servizio app](/azure/app-service/)o usare un'app creata per un'altra esercitazione.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
L'endpoint precedente non funziona per i servizi app Linux in questo momento. Considerare invece l'utilizzo di FTP o dell'API di [distribuzione zip.](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment)

## <a name="deploy-zip-file-with-azure-cli"></a>Distribuire il file ZIP con l'interfaccia della riga di comando di Azure

Distribuire il file ZIP caricato nell'app Web usando il comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

L'esempio seguente distribuisce il file ZIP caricato. Quando si utilizza un'installazione locale dell'interfaccia della riga di comando di Azure, specificare il percorso del file ZIP locale per `--src`.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Questo comando distribuisce i file e le directory del file ZIP nella cartella predefinita dell'applicazione del servizio app (`\home\site\wwwroot`) e riavvia l'app.

Per impostazione predefinita, il motore di distribuzione presuppone che un file zip sia pronto per essere eseguito così com'è e non esedida alcuna automazione della compilazione. Per abilitare la stessa automazione di `SCM_DO_BUILD_DURING_DEPLOYMENT` compilazione in una distribuzione [Git](deploy-local-git.md), impostare l'impostazione dell'app eseguendo il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Per altre informazioni, vedere [Documentazione Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Distribuire il file WAR

Per distribuire un file WAR al servizio `https://<app-name>.scm.azurewebsites.net/api/wardeploy`app, inviare una richiesta POST a . La richiesta POST deve contenere il file WAR nel corpo del messaggio. Le credenziali di distribuzione per l'app vengono fornite nella richiesta usando l'autenticazione di base HTTP.

Utilizzare `/api/wardeploy` sempre quando si distribuiscono file WAR. Questa API espanderà il file WAR e lo inserirà nell'unità di file condivisa. l'utilizzo di altre API di distribuzione può causare un comportamento incoerente. 

Per l'autenticazione HTTP di base sono necessarie le credenziali di distribuzione del servizio app. Per informazioni su come impostare le credenziali di distribuzione, vedere [Impostare e reimpostare le credenziali a livello di utente](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Con cURL

L'esempio seguente usa lo strumento cURL per distribuire un file WAR. Sostituire i segnaposto `<username>`, `<war-file-path>` e `<app-name>`. Quando richiesto da cURL, digitare la password.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Con PowerShell

Nell'esempio seguente viene utilizzato [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) per caricare il file con estensione war. Sostituire i segnaposto `<group-name>`, `<app-name>` e `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passaggi successivi

Per scenari di distribuzione più avanzati, provare a eseguire la [distribuzione in Azure con Git](deploy-local-git.md). La distribuzione basata su Git in Azure abilita il controllo della versione, il ripristino del pacchetto, MSBuild e altro ancora.

## <a name="more-resources"></a>Altre risorse

* [Kudu: distribuzione da un file ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenziali per la distribuzione del Servizio app di Azure](deploy-ftp.md)
