---
title: Distribuire il contenuto tramite FTP/S
description: Informazioni su come distribuire l'app nel servizio app di Azure usando FTP o FTPS. Migliorare la sicurezza del sito Web disabilitando l'FTP non crittografato.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: ce8c32b1afdf4178e3ffdc09e9c9176436fa771b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605077"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Distribuire l'app nel servizio app di Azure usando FTP/S

Questo articolo illustra come usare FTP o FTPS per distribuire l'app Web, il back-end dell'app per dispositivi mobili o l'app per le API nel [servizio app di Azure](./overview.md).

L'endpoint FTP/S per l'app è già attivo. Non è necessaria alcuna configurazione per abilitare la distribuzione FTP/S.

## <a name="open-ftp-dashboard"></a>Aprire un dashboard FTP

1. Nella [portale di Azure](https://portal.azure.com)cercare e selezionare **Servizi app**.

    ![Cercare i servizi app.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Selezionare l'app Web che si vuole distribuire.

    ![Selezionare l'app.](media/app-service-continuous-deployment/select-your-app.png)

3. Selezionare Dashboard del **centro di distribuzione**  >  **FTP**  >  .

    ![Aprire un dashboard FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Ottenere informazioni di connessione a FTP

Nel dashboard FTP selezionare **copia** per copiare l'endpoint FTPS e le credenziali dell'app.

![Copiare le informazioni FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

È consigliabile usare **Credenziali dell'app** per distribuire l'app perché si tratta di informazioni univoche per ogni app. Tuttavia, se si fa clic su **Credenziali utente**, è possibile impostare le credenziali a livello di utente da usare per l'accesso FTP/S a tutte le app del servizio app della sottoscrizione.

> [!NOTE]
> Per eseguire l'autenticazione a un endpoint FTP/FTPS utilizzando credenziali a livello di utente, è necessario specificare un nome utente nel formato seguente: 
>
>`<app-name>\<user-name>`
>
> Poiché le credenziali a livello di utente sono collegate all'utente e non a una risorsa specifica, il nome utente deve essere in questo formato per indirizzare l'azione di accesso all'endpoint corretto dell'app.
>

## <a name="deploy-files-to-azure"></a>Distribuire file in Azure

1. Nel client FTP, ad esempio [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/) o [WinSCP](https://winscp.net/index.php), usare le specifiche informazioni raccolte per connettersi all'app.
2. Copiare i file e la struttura di directory corrispondente nella directory [**/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) in Azure o nella directory **/site/wwwroot/App_Data/Jobs/** per i processi Web.
3. Passare all'URL dell'app per verificare che l'applicazione venga eseguita correttamente. 

> [!NOTE] 
> Diversamente dalle [distribuzioni basate su Git](deploy-local-git.md), la distribuzione FTP non supporta le automazioni di distribuzione seguenti: 
>
> - Ripristino delle dipendenze (ad esempio, automazioni NuGet, NPM, PIP e Composer)
> - Compilazione di file binari .NET
> - Generazione di web.config ([esempio di Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Generare questi file necessari manualmente nel computer locale e quindi distribuirli insieme all'app.
>

## <a name="enforce-ftps"></a>Applicare FTPS

Per una maggiore sicurezza, è consigliabile consentire il protocollo FTP solo su TLS/SSL. È anche possibile disabilitare FTP e FTPS se non si usa la distribuzione FTP.

Nella pagina delle risorse dell'app, in [portale di Azure](https://portal.azure.com), selezionare **configurazione**  >  **Impostazioni generali** dal dispositivo di spostamento a sinistra.

Per disabilitare l'FTP non crittografato, selezionare **FTPS solo** nello **stato FTP**. Per disabilitare completamente FTP e FTPS, selezionare **disattivato**. Al termine fare clic su **Salva**. Se si usa **solo FTPS**, è necessario applicare TLS 1,2 o versione successiva passando al pannello delle **Impostazioni TLS/SSL** dell'app Web. TLS 1.0 e 1.1 non sono supportati con **Solo FTPS**.

![Disabilitare FTP/FTPS](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

Per la distribuzione FTP tramite l'[interfaccia della riga di comando di Azure](/cli/azure), vedere [Creare un'app Web e distribuire i file con FTP](./scripts/cli-deploy-ftp.md).

Per la distribuzione FTP tramite [Azure PowerShell](/cli/azure), vedere [Caricare i file in un'app Web tramite FTP](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Risolvere i problemi di distribuzione FTP

- [Distribuire l'app nel servizio app di Azure usando FTP/S](#deploy-your-app-to-azure-app-service-using-ftps)
  - [Aprire un dashboard FTP](#open-ftp-dashboard)
  - [Ottenere informazioni di connessione a FTP](#get-ftp-connection-information)
  - [Distribuire file in Azure](#deploy-files-to-azure)
  - [Applicare FTPS](#enforce-ftps)
  - [Automatizzazione con gli script](#automate-with-scripts)
  - [Risolvere i problemi di distribuzione FTP](#troubleshoot-ftp-deployment)
    - [Come è possibile risolvere i problemi di distribuzione FTP?](#how-can-i-troubleshoot-ftp-deployment)
    - [Non è possibile eseguire l'FTP e pubblicare il codice. Come è possibile risolvere il problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
    - [Come è possibile connettersi a FTP nel Servizio app di Azure tramite la modalità passiva?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)
  - [Passaggi successivi](#next-steps)
  - [Altre risorse](#more-resources)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Come è possibile risolvere i problemi di distribuzione FTP?

Il primo passo per la risoluzione dei problemi di distribuzione FTP consiste nell'isolare un problema di distribuzione da un problema di runtime dell'applicazione.

Un problema di distribuzione comporta in genere che non venga distribuito alcun file o che vengano distribuiti file non corretti all'app. È possibile risolverlo analizzando la distribuzione FTP o selezionando un percorso di distribuzione alternativo (ad esempio il controllo del codice sorgente).

Un problema di runtime dell'applicazione determina in genere la distribuzione del set corretto di file all'app, ma un comportamento dell'app non corretto. È possibile risolverlo esaminando il comportamento di runtime del codice e analizzando i percorsi di errore specifici.

Per determinare se un problema è di distribuzione o di runtime, vedere [Deployment vs. runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues) (Problemi di distribuzione e di runtime).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Non sono in grado di eseguire il protocollo FTP e di pubblicare il codice. Come è possibile risolvere il problema?
Verificare di avere immesso il nome host e le [credenziali](#open-ftp-dashboard) corretti. Verificare anche che le porte FTP seguenti nel computer non siano bloccate da un firewall:

- Porta di connessione di controllo FTP: 21
- Porta di connessione dati FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Come è possibile connettersi a FTP nel Servizio app di Azure tramite la modalità passiva?
Servizio app di Azure supporta la connessione tramite la modalità attiva e passiva. La modalità passiva è preferibile perché le macchine di distribuzione sono in genere protette da un firewall (nel sistema operativo o nell'ambito di una rete domestica o aziendale). Vedere un'[esempio della documentazione WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Passaggi successivi

Per scenari di distribuzione più avanzati, provare a eseguire la [distribuzione in Azure con Git](deploy-local-git.md). La distribuzione basata su Git in Azure abilita il controllo della versione, il ripristino del pacchetto, MSBuild e altro ancora.

## <a name="more-resources"></a>Altre risorse

* [Credenziali per la distribuzione del Servizio app di Azure](deploy-configure-credentials.md)