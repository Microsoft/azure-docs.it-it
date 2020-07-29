---
title: Domande frequenti sulla distribuzione - Servizio app di Azure | Microsoft Docs
description: È possibile ottenere risposte alle domande frequenti sulla distribuzione per la funzionalità App Web del servizio app di Azure.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 25f43051f11d42c4f8565039d3168f2ae162eb37
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74671693"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Domande frequenti sulla distribuzione per app Web in Azure

Questo articolo offre risposte alle domande frequenti sui problemi di distribuzione per la [funzionalità App Web del servizio app di Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Quando si iniziano a usare le app Web del servizio app, come si pubblica il codice?

Di seguito sono riportate alcune opzioni per la pubblicazione del codice dell'app Web:

*   Eseguire la distribuzione usando Visual Studio. Se è disponibile la soluzione Visual Studio, fare clic con il pulsante destro del mouse sul progetto dell'applicazione Web e quindi scegliere **Pubblica**.
*   Eseguire la distribuzione usando un client FTP. Nel portale di Azure, scaricare il profilo di pubblicazione per l'app Web nella quale si intende distribuire il codice. Caricare quindi i file in \site\wwwroot usando le stesse credenziali FTP del profilo di pubblicazione.

Per altre informazioni, vedere [Distribuire l'app nel servizio app](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Viene visualizzato un messaggio di errore quando si prova a eseguire la distribuzione da Visual Studio. Ricerca per categorie risolvere l'errore?

Se viene visualizzato il messaggio seguente, è possibile che si stia usando una versione precedente dell'SDK: "Si è verificato un errore durante la distribuzione per la risorsa 'YourResourceName' nel gruppo di risorse 'YourResourceGroup': MissingRegistrationForLocation: La sottoscrizione non è registrata per il tipo di risorsa 'components' nella località 'Stati Uniti centrali'. Ripetere la registrazione per questo provider per poter accedere a questo percorso. " 

Per risolvere questo errore, eseguire l'aggiornamento all'[SDK più recente](https://azure.microsoft.com/downloads/). Se viene visualizzato questo messaggio e si ha l'SDK più recente, inviare una richiesta di supporto.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Come si distribuisce un'applicazione ASP.NET da Visual Studio al servizio app?
<a id="deployasp"></a>

L'esercitazione [creare la prima app web ASP.NET in Azure in cinque minuti](app-service-web-get-started-dotnet.md) illustra come distribuire un'applicazione Web ASP.NET in un'app Web nel servizio app usando Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Quali sono i diversi tipi di servizi di credenziali di distribuzione?

Il servizio app supporta due tipi di credenziali per la distribuzione Git locale e la distribuzione FTP/S. Per altre informazioni su come configurare le credenziali di distribuzione, vedere [Configurare le credenziali di distribuzione per il servizio app](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Qual è la struttura di file o directory dell'app Web del servizio app?

Per informazioni sulla struttura dei file dell'app del servizio app, vedere [Struttura di file in Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Come si risolve il problema "Errore FTP 550. Spazio su disco insufficiente" quando si prova a caricare i file tramite FTP?

Se viene visualizzato questo messaggio, è probabile che sia in esecuzione una quota disco nel piano di servizio per l'app Web. Potrebbe essere necessario passare a un livello di servizio superiore in base alle esigenze di spazio su disco. Per altre informazioni sui piani tariffari e i limiti delle risorse, vedere il [piano tariffario del servizio app](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Come si configura la distribuzione continua dell'app Web del servizio app?

È possibile impostare la distribuzione continua da diverse risorse, tra cui, Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox e altri repository Git. Queste opzioni sono disponibili nel portale. [Distribuzione continua nel servizio app](deploy-continuous-deployment.md) è un'utile esercitazione che illustra come configurare la distribuzione continua.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Come si risolvono i problemi con la distribuzione continua da GitHub e Bitbucket?

Per informazioni sull'analisi dei problemi con la distribuzione continua da GitHub o Bitbucket, vedere [Investigating continuous deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment) (Analisi della distribuzione continua).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Non è possibile accedere al sito tramite FTP e pubblicare il codice. Come si risolve questo problema?

Per risolvere i problemi FTP:

1. Verificare di immettere il nome host e le credenziali corretti. Per informazioni dettagliate sui diversi tipi di credenziali e sul loro uso, vedere [Deployment credentials](https://github.com/projectkudu/kudu/wiki/Deployment-credentials) (Credenziali di distribuzione).
2. Verificare che le porte FTP non siano bloccate da un firewall. Le porte devono avere le impostazioni seguenti:
    * Porta di connessione di controllo FTP: 21
    * Porta di connessione dati FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Come si pubblica il codice nel servizio app?

Avvio rapido di Azure è progettato per semplificare la distribuzione dell'app con lo stack di distribuzione e il metodo di propria scelta. Per usare Avvio rapido, nel portale di Azure passare al servizio app e in **Distribuzione** selezionare **Avvio rapido**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Perché l'app a volte si riavvia dopo la distribuzione nel servizio app?

Per informazioni sulle circostanze in cui la distribuzione di un'applicazione può provocare un riavvio, vedere [Deployment vs runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts") (Problemi di distribuzione e runtime). Come descritto nell'articolo, il servizio app distribuisce i file nella cartella wwwroot. Non riavvia mai direttamente l'app.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Come integrare il codice di Azure DevOps con il servizio app?

Sono disponibili due opzioni per l'uso della distribuzione continua con Azure DevOps:

*   Usare un progetto Git. Connettersi tramite il servizio app tramite il centro distribuzione.
*   Usare un progetto di controllo della versione di Team Foundation. Eseguire la distribuzione usando l'agente di compilazione per il servizio app.

La distribuzione continua di codice per entrambe le opzioni dipende dai flussi di lavoro di sviluppo esistenti e dalle procedure di archiviazione. Per altre informazioni, vedere questi articoli: 

*   [Implementare la distribuzione continua dell'app in un sito Web di Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Configurare un'organizzazione di Azure DevOps in modo da distribuirla in un'app Web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Come si usa FTP o FTPS per distribuire l'app nel servizio app?

Per informazioni sull'uso di FTP o FTPS per distribuire l'app Web nel servizio app, vedere [Distribuire l'app nel servizio app usando FTP/S](deploy-ftp.md).
