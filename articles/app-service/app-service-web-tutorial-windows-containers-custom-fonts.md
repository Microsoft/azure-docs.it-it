---
title: 'Esercitazione: App legacy con contenitore (anteprima)'
description: Informazioni su come eseguire la migrazione di un contenitore Windows personalizzato in Servizio app di Azure e come distribuire un software personalizzato nel contenitore.
ms.topic: tutorial
ms.date: 10/22/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: 8e755c5b9a57eb66fc47364fb2fcdcbe30c2d09e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85205623"
---
# <a name="migrate-an-aspnet-app-to-azure-app-service-using-a-windows-container-preview"></a>Eseguire la migrazione di un'app ASP.NET in Servizio app di Azure usando un contenitore Windows (anteprima)

Il [servizio app di Azure](overview.md) offre stack di applicazioni predefiniti in Windows, ad esempio ASP.NET o Node.js, eseguiti in IIS. L'ambiente Windows preconfigurato blocca il sistema operativo impedendo l'accesso amministrativo, le installazioni di software, le modifiche alla Global Assembly Cache e così via (vedere [Funzionalità del sistema operativo in Servizio app di Azure](operating-system-functionality.md)). Tuttavia, usando un contenitore Windows personalizzato nel servizio app è possibile apportare le modifiche del sistema operativo necessarie per l'app, per poter eseguire in modo semplice la migrazione di un'app locale che richiede una configurazione personalizzata di software e sistema operativo. Questa esercitazione illustra come eseguire la migrazione al servizio app di un'app ASP.NET che usa tipi di carattere personalizzati installati nella libreria dei tipi di carattere Windows. Si distribuisce un'immagine Windows configurata in modo personalizzato da Visual Studio a [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/) e quindi la si esegue nel servizio app.

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-running.png)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- <a href="https://hub.docker.com/" target="_blank">Iscriversi per ottenere un account Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Installare Docker per Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Impostare Docker per eseguire contenitori Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installare Visual Studio 2019</a> con i carichi di lavoro **Sviluppo ASP.NET e Web** e **Sviluppo di Azure**. Se Visual Studio 2019 è già installato:
    - Installare gli aggiornamenti più recenti in Visual Studio facendo clic su **?**  > **Controlla aggiornamenti**.
    - Aggiungere i carichi di lavoro in Visual Studio facendo clic su **Strumenti** > **Ottieni strumenti e funzionalità**.

## <a name="set-up-the-app-locally"></a>Configurare l'app in locale

### <a name="download-the-sample"></a>Scaricare l'esempio

In questo passaggio si configura il progetto .NET locale.

- [Scaricare il progetto di esempio](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- Estrarre (decomprimere) il file *custom-font-win-container.zip*.

Il progetto di esempio contiene un'applicazione ASP.NET semplice che usa un tipo di carattere personalizzato installato nella libreria dei tipi di carattere Windows. Non è necessario installare i tipi di carattere, ma si tratta di un esempio di app integrata con il sistema operativo sottostante. Per eseguire la migrazione di un'app di questo tipo al servizio app, è necessario riprogettare il codice per rimuovere l'integrazione oppure è possibile eseguire la migrazione dell'app senza modifiche in un contenitore Windows personalizzato.

### <a name="install-the-font"></a>Installare il tipo di carattere

In Esplora risorse passare a _custom-font-win-container-master/CustomFontSample_, fare clic con il pulsante destro del mouse su _FrederickatheGreat-Regular.ttf_ e scegliere **Installa**.

Questo tipo di carattere è disponibile pubblicamente in [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>Eseguire l'app

Aprire il file *custom-font-win-container/CustomFontSample.sln* in Visual Studio. 

Digitare `Ctrl+F5` per eseguire l'app senza debug. L'app viene visualizzata nel browser predefinito. 

![Finestra di dialogo Nuovo progetto ASP.NET](media/app-service-web-tutorial-windows-containers-custom-fonts/local-app-in-browser.png)

Poiché usa un tipo di carattere installato, l'app non può essere eseguita nell'ambiente sandbox del servizio app. È tuttavia possibile distribuirla usando un contenitore Windows, perché è possibile installare il tipo di carattere nel contenitore Windows.

### <a name="configure-windows-container"></a>Configurare il contenitore Windows

In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **CustomFontSample** e scegliere **Aggiungi** > **Container Orchestration Support** (Supporto orchestrazione contenitori).

![Finestra di dialogo Nuovo progetto ASP.NET](media/app-service-web-tutorial-windows-containers-custom-fonts/enable-container-orchestration.png)

Selezionare **Docker Compose** > **OK**.

Il progetto è ora configurato per l'esecuzione in un contenitore Windows. Verrà aggiunto un file _Dockerfile_ al progetto **CustomFontSample** e un progetto **docker-compose** verrà aggiunto alla soluzione. 

Da Esplora soluzioni aprire **Dockerfile**.

È necessario usare un'[immagine padre supportata](app-service-web-get-started-windows-container.md#use-a-different-parent-image). Modificare l'immagine padre sostituendo la riga `FROM` con il codice seguente:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

Alla fine del file aggiungere la riga seguente e quindi salvare il file:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

_InstallFont.ps1_ è disponibile nel progetto **CustomFontSample**. Si tratta di un semplice script che installa il tipo di carattere. Una versione più complessa dello script è disponibile in [Script Center](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

> [!NOTE]
> Per testare il contenitore Windows in locale, assicurarsi di aver avviato Docker nel computer locale.
>

## <a name="publish-to-azure-container-registry"></a>Eseguire la pubblicazione in Registro Azure Container

[Registro Azure Container](https://docs.microsoft.com/azure/container-registry/) consente di archiviare le immagini per le distribuzioni di contenitori. È possibile configurare il servizio app per usare le immagini ospitate in Registro Azure Container.

### <a name="open-publish-wizard"></a>Aprire la pubblicazione guidata

In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **CustomFontSample** e scegliere **Pubblica**.

![Finestra di dialogo Nuovo progetto ASP.NET](media/app-service-web-tutorial-windows-containers-custom-fonts/open-publish-wizard.png)

### <a name="create-registry-and-publish"></a>Creare il registro ed eseguire la pubblicazione

Nella pubblicazione guidata selezionare **Registro contenitori** > **Crea nuovo Registro Azure Container** > **Pubblica**.

![Finestra di dialogo Nuovo progetto ASP.NET](media/app-service-web-tutorial-windows-containers-custom-fonts/create-registry.png)

### <a name="sign-in-with-azure-account"></a>Accedere con l'account di Azure

Nella finestra di dialogo **Crea un nuovo Registro Azure Container** selezionare **Aggiungi un account** e accedere alla sottoscrizione di Azure. Se è già stato eseguito l'accesso, selezionare l'account contenente la sottoscrizione desiderata dall'elenco a discesa.

![Accedere ad Azure](./media/app-service-web-tutorial-windows-containers-custom-fonts/add-an-account.png)

### <a name="configure-the-registry"></a>Configurare il registro

Configurare il nuovo registro contenitori in base ai valori suggeriti nella tabella seguente. Al termine, fare clic su **Crea**.

| Impostazione  | Valore consigliato | Per ulteriori informazioni |
| ----------------- | ------------ | ----|
|**Prefisso DNS**| Mantenere il nome del registro generato oppure modificarlo scegliendo un altro nome univoco. |  |
|**Gruppo di risorse**| Fare clic su **Nuovo**, digitare **myResourceGroup** e fare clic su **OK**. |  |
|**SKU**| Basic | [Piani tariffari](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Percorso del registro**| Europa occidentale | |

![Configurare il Registro Azure Container](./media/app-service-web-tutorial-windows-containers-custom-fonts/configure-registry.png)

Verrà aperta una finestra del terminale in cui viene visualizzato l'avanzamento della distribuzione dell'immagine. Attendere il completamento della distribuzione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-web-app"></a>Creare un'app Web

Dal menu a sinistra scegliere **Crea una risorsa** > **Web** > **App Web per contenitori**.

### <a name="configure-app-basics"></a>Configurare le informazioni di base dell'app

Nella scheda **Informazioni di base** configurare le impostazioni in base alla tabella seguente e quindi fare clic su **Avanti: Docker**.

| Impostazione  | Valore consigliato | Per ulteriori informazioni |
| ----------------- | ------------ | ----|
|**Sottoscrizione**| Assicurarsi che sia selezionata la sottoscrizione corretta. |  |
|**Gruppo di risorse**| Selezionare **Crea nuovo**, digitare **myResourceGroup** e scegliere **OK**. |  |
|**Nome**| Digitare un nome univoco. | L'URL dell'app Web è `http://<app-name>.azurewebsites.net`, dove `<app-name>` è il nome dell'app. |
|**Pubblica**| Contenitore Docker | |
|**Sistema operativo**| Windows | |
|**Area**| Europa occidentale | |
|**Piano Windows**| Selezionare **Crea nuovo**, digitare **myAppServicePlan** e scegliere **OK**. | |

La scheda **Informazioni di base** avrà un aspetto simile al seguente:

![](media/app-service-web-tutorial-windows-containers-custom-fonts/configure-app-basics.png)

### <a name="configure-windows-container"></a>Configurare il contenitore Windows

Nella scheda **Docker** configurare il contenitore Windows personalizzato come illustrato nella tabella seguente e selezionare **Rivedi e crea**.

| Impostazione  | Valore consigliato |
| ----------------- | ------------ |
|**Origine immagine**| Registro Azure Container |
|**Registro**| Selezionare il [registro creato in precedenza](#publish-to-azure-container-registry). |
|**Immagine**| customfontsample |
|**Tag**| più recenti |

### <a name="complete-app-creation"></a>Completare la creazione dell'app

Fare clic su **Crea** e attendere che Azure crei le risorse necessarie.

## <a name="browse-to-the-web-app"></a>Passare all'app Web

Al termine dell'operazione di Azure, verrà visualizzata una casella di notifica.

![](media/app-service-web-tutorial-windows-containers-custom-fonts/portal-create-finished.png)

1. Fare clic su **Vai alla risorsa**.

2. Nella pagina dell'app fare clic sul collegamento sotto **URL**.

Verrà aperta la nuova pagina del browser illustrata di seguito:

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-starting.png)

Attendere alcuni minuti e riprovare, finché non viene visualizzata la pagina iniziale, con il carattere desiderato:

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-running.png)

**Congratulazioni** È stata eseguita la migrazione di un'applicazione ASP.NET in Servizio app di Azure in un contenitore Windows.

## <a name="see-container-start-up-logs"></a>Visualizzare i log di avvio del contenitore

Il caricamento del contenitore Windows potrebbe richiedere tempo. Per visualizzare lo stato di avanzamento, passare all'URL seguente sostituendo *\<app-name>* con il nome dell'app.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

I log trasmessi sono simili al seguente:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```
