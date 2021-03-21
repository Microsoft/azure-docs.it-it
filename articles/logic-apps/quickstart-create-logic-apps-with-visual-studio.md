---
title: Automatizzare le attività e i flussi di lavoro con Visual Studio
description: Creare, pianificare ed eseguire flussi di lavoro automatizzati per l'integrazione aziendale usando App per la logica di Azure e Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/27/2020
ms.openlocfilehash: 8181097425045e5b1ed838c5fcc08b0069185908
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99051958"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Guida introduttiva: Creare attività, processi e flussi di lavoro automatizzati con App per la logica di Azure - Visual Studio

Con [App per la logica di Azure](../logic-apps/logic-apps-overview.md) e Visual Studio è possibile creare flussi di lavoro che automatizzano le attività e i processi per l'integrazione di applicazioni, dati, sistemi e servizi tra aziende e organizzazioni. Questo argomento di avvio rapido mostra come progettare e compilare questi flussi di lavoro creando app per la logica in Visual Studio e distribuendole in Azure. Benché sia possibile eseguire queste attività nel portale di Azure, Visual Studio consente di aggiungere app per la logica al controllo del codice sorgente, pubblicare versioni diverse e creare modelli di Azure Resource Manager per ambienti di distribuzione differenti.

Se non si ha familiarità con App per la logica di Azure e si desidera solo comprenderne i concetti di base, vedere l'[argomento di avvio rapido per la creazione di un'app per la logica nel portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md). Progettazione app per la logica funziona in modo analogo nel portale di Azure e in Visual Studio.

In questo argomento di avvio rapido verrà creata con Visual Studio la stessa app per la logica dell'argomento di avvio rapido per il portale di Azure. Verrà anche descritto come [creare un'app di esempio in Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md) e come [creare e gestire app per la logica tramite l'interfaccia della riga di comando di Azure](quickstart-logic-apps-azure-cli.md). Questa app per la logica monitora il feed RSS del sito Web e invia un messaggio di posta elettronica per ogni nuovo elemento di tale feed. L'app per la logica completa sarà simile a questo flusso di lavoro di alto livello:

![Screenshot che mostra il flusso di lavoro generale di un'app per la logica completata.](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). Se è disponibile una sottoscrizione di Azure per enti pubblici, completare questi passaggi aggiuntivi per [configurare Visual Studio per Cloud Azure per enti pubblici](#azure-government).

* Scaricare e installare questi strumenti, se non sono già disponibili:

  * [Visual Studio 2019, 2017 o 2015 - Community Edition o superiore](https://aka.ms/download-visual-studio). Questo argomento di avvio rapido usa Visual Studio Community 2017.

    > [!IMPORTANT]
    > Quando si installa Visual Studio 2019 o 2017, assicurarsi di selezionare il carico di lavoro **Sviluppo di Azure**.

  * [Microsoft Azure SDK per .NET (2.9.1 o versione successiva)](https://azure.microsoft.com/downloads/). Altre informazioni su [Azure SDK per .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Gli strumenti più recenti per App per la logica di Azure per l'estensione di Visual Studio e la versione desiderata:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    È anche possibile scaricare e installare Strumenti App per la logica di Azure direttamente da Visual Studio Marketplace o [installare questa estensione da Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Assicurarsi di riavviare Visual Studio al termine dell'installazione.

* Accesso al Web mentre si usa la finestra integrata Progettazione app per la logica

  La finestra di progettazione richiede una connessione Internet per creare le risorse in Azure e leggere le proprietà e i dati dai connettori nell'app per la logica.

* Un account di posta elettronica supportato da App per la logica, ad esempio un account Outlook per Microsoft 365, Outlook.com o Gmail. Per altri provider, [vedere qui l'elenco dei connettori](/connectors/). Questo esempio usa Outlook per Office 365. Se si usa un altro provider, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa.

  > [!IMPORTANT]
  > Se si vuole usare il connettore Gmail, solo gli account G Suite Business possono usare questo connettore senza restrizioni nelle app per la logica. Se si dispone di un account Gmail consumer, è possibile usare questo connettore solo con servizi approvati da Google specifici oppure è possibile [creare un'app client Google da usare per l'autenticazione con il connettore Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Per altre informazioni, vedere [Informative sulla privacy e sulla sicurezza dei dati per i connettori Google in App per la logica di Azure](../connectors/connectors-google-data-security-privacy-policy.md).

* Se l'app per la logica deve comunicare attraverso un firewall che limita il traffico a indirizzi IP specifici, il firewall deve consentire l'accesso *sia* per gli indirizzi [IP in](logic-apps-limits-and-config.md#outbound) [ingresso](logic-apps-limits-and-config.md#inbound) che in uscita usati dal servizio o dal runtime di app per la logica nell'area di Azure in cui è presente l'app per la logica. Se l'app per la logica usa anche [connettori gestiti](../connectors/apis-list.md#managed-api-connectors), ad esempio il connettore Office 365 Outlook o il connettore SQL oppure usa [connettori personalizzati](/connectors/custom-connectors/), il firewall deve anche consentire l'accesso per *tutti* gli [indirizzi IP in uscita del connettore gestito](logic-apps-limits-and-config.md#outbound) nell'area di Azure dell'app per la logica.

<a name="azure-government"></a>

## <a name="set-up-visual-studio-for-azure-government"></a>Configurare Visual Studio per Azure per enti pubblici

### <a name="visual-studio-2017"></a>Visual Studio 2017

È possibile usare l'[estensione Azure Environment Selector di Visual Studio](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), che è possibile scaricare e installare da [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector).

### <a name="visual-studio-2019"></a>Visual Studio 2019

Per usare le sottoscrizioni di Azure per enti pubblici in App per la logica di Azure, è necessario [aggiungere un endpoint di individuazione per Cloud Azure per enti pubblici in Visual Studio](../azure-government/documentation-government-connect-vs.md). *Prima di accedere a Visual Studio con l'account Azure per enti pubblici*, è tuttavia necessario rinominare il file JSON generato dopo l'aggiunta dell'endpoint di individuazione seguendo questa procedura:

1. Chiudere Visual Studio.

1. Trovare il file JSON generato e denominato `Azure U.S. Government-A3EC617673C6C70CC6B9472656832A26.Configuration` in questo percorso:

   `%localappdata%\.IdentityService\AadConfigurations`
 
1. Rinominare il file JSON in `AadProvider.Configuration.json`.

1. Riavviare Visual Studio.

1. Completare i passaggi per accedere con l'account Azure per enti pubblici.

Per ripristinare questa configurazione, eliminare il file JSON nel percorso seguente e riavviare Visual Studio:

`%localappdata%\.IdentityService\AadConfigurations\AadProvider.Configuration.json`

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Creare un progetto Gruppo di risorse di Azure

Per iniziare, creare un [progetto Gruppo di risorse di Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). È possibile accedere ad altre informazioni sui [gruppi di risorse e le risorse di Azure](../azure-resource-manager/management/overview.md).

1. Avviare Visual Studio. Accedere con l'account Azure.

1. Scegliere **Nuovo** > **Progetto** dal menu **File** (scelta rapida da tastiera: CTRL + MAIUSC + N)

   ![Scegliere Nuovo > Progetto dal menu File](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. In **Installato** selezionare **Visual C#** o **Visual Basic**. Selezionare **Cloud** > **Gruppo di risorse di Azure**. Assegnare un nome al progetto, ad esempio:

   ![Creare un progetto Gruppo di risorse di Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > I nomi dei gruppi di risorse possono contenere solo lettere, numeri, punti (`.`), caratteri di sottolineatura (`_`), trattini (`-`) e parentesi (`(`, `)`) ma non possono *terminare* con punti (`.`).
   >
   > Se l'opzione **Cloud** o **Gruppo di risorse di Azure** non è visualizzata, assicurarsi di installare Azure SDK per Visual Studio.

   Se si usa Visual Studio 2019, procedere come segue:

   1. Nella finestra di dialogo **Crea un nuovo progetto** selezionare il progetto **Gruppo di risorse di Azure** per Visual C# o Visual Basic. Selezionare **Avanti**.

   1. Specificare un nome per il gruppo di risorse di Azure da usare e altre informazioni sul progetto. Selezionare **Crea**.

1. Selezionare il modello **App per la logica** nell'elenco. Selezionare **OK**.

   ![Selezionare il modello App per la logica](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Dopo la creazione del progetto in Visual Studio, verrà visualizzata la finestra Esplora soluzioni con la soluzione. Nella soluzione il file **LogicApp.json** non solo contiene la definizione dell'app per la logica, ma è anche un modello di Azure Resource Manager che è possibile usare per la distribuzione.

   ![Esplora soluzioni con la nuova soluzione di app per la logica e il file di distribuzione](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Creare un'app per la logica vuota

Dopo aver creato il progetto Gruppo di risorse di Azure, creare l'app per la logica con il modello **App per la logica vuota**.

1. In Esplora soluzioni aprire il menu di scelta rapida per il file **LogicApp.json**. Selezionare **Open With Logic App Designer** (Apri con Progettazione app per la logica) (scelta rapida da tastiera: CTRL + L)

   ![Aprire il file con estensione json dell'app per la logica con Progettazione app per la logica](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di aver installato gli aggiornamenti più recenti per Visual Studio.

   Visual Studio richiede la sottoscrizione di Azure e un gruppo di risorse di Azure per la creazione e la distribuzione di risorse per l'app per la logica e le connessioni.

1. Per **Sottoscrizione** selezionare la sottoscrizione di Azure. Per **Gruppo di risorse** selezionare **Crea nuovo** per creare un altro gruppo di risorse di Azure.

   ![Selezionare la sottoscrizione di Azure, un gruppo di risorse e la posizione delle risorse](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Impostazione | Valore di esempio | Descrizione |
   | ------- | ------------- | ----------- |
   | Account utente | Fabrikam <br> sophia-owen@fabrikam.com | Account usato per l'accesso a Visual Studio |
   | **Sottoscrizione** | Pagamento in base al consumo <br> (sophia-owen@fabrikam.com) | Il nome della sottoscrizione di Azure e l'account associato |
   | **Gruppo di risorse** | MiaAppLogica-GR <br> (Stati Uniti occidentali) | Gruppo di risorse di Azure e la posizione per l'archiviazione e la distribuzione delle risorse per l'app per la logica |
   | **Posizione** | **Uguale a quello del gruppo di risorse** | Percorso specifico e relativo tipo per la distribuzione dell'app per la logica. Il tipo di percorso è un'area di Azure o un [ambiente del servizio di integrazione](connect-virtual-network-vnet-isolated-environment.md) esistente. <p>Per questa Guida introduttiva, impostare il tipo di percorso su **Area** e il percorso su un valore **uguale a quello del gruppo di risorse**. <p>**Nota**: dopo aver creato il progetto del gruppo di risorse, è possibile [modificare il tipo di percorso e il percorso](manage-logic-apps-with-visual-studio.md#change-location), ma un tipo di percorso diverso influisce sull'app per la logica in diversi modi. |
   ||||

1. Viene visualizzato Progettazione app per la logica, che contiene un video introduttivo e i trigger più usati. Scorrere verso il basso dopo il video e i trigger fino a **Modelli** e selezionare **App per la logica vuota**.

   ![Selezionare App per la logica vuota](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Creare il flusso di lavoro dell'app per la logica

Aggiungere quindi un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) RSS che verrà attivato quando viene visualizzato un nuovo elemento del feed. Ogni app per la logica inizia con un trigger, che viene attivato quando vengono soddisfatti criteri specifici. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica che esegue il flusso di lavoro.

1. In Progettazione app per la logica selezionare **Tutti** sotto la casella di ricerca. Nella casella di ricerca immettere "rss". Nell'elenco di trigger selezionare questo trigger: **Quando viene pubblicato un elemento del feed**

   ![Creare l'app per la logica aggiungendo un trigger e azioni](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Dopo che il trigger viene visualizzato nella finestra di progettazione, completare la creazione dell'app per la logica seguendo i passaggi del flusso di lavoro descritti nell'[argomento di avvio rapido per il portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger), quindi tornare a questo articolo. Al termine, l'app per la logica avrà un aspetto simile all'esempio seguente:

   ![App per la logica completata](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Salvare la soluzione di Visual Studio. (scelta rapida da tastiera: CTRL+S).

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Distribuire l'app per la logica in Azure

Prima di poter eseguire e testare l'app per la logica, distribuire l'app in Azure da Visual Studio.

1. Nel menu di scelta rapida del progetto in Esplora soluzioni scegliere **Distribuisci** > **Nuovo**. Se richiesto, accedere con il proprio account di Azure.

   ![Creare la distribuzione dell'app per la logica](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Per questa distribuzione, mantenere la sottoscrizione di Azure, il gruppo di risorse e le altre impostazioni. Selezionare **Distribuisci**.

   ![Distribuire l'app per la logica nel gruppo di risorse di Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Se viene visualizzata la finestra **Modifica parametri**, specificare un nome di risorsa per l'app per la logica. Salvare le impostazioni.

   ![Specificare il nome della distribuzione per l'app per la logica](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   All'avvio della distribuzione, lo stato di distribuzione dell'app sarà visualizzato nella finestra **Output** di Visual Studio. Se lo stato non è visualizzato, aprire l'elenco **Mostra output di** e selezionare il proprio gruppo di risorse di Azure.

   ![Output dello stato di distribuzione](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Se i connettori selezionati richiedono input, viene visualizzata in background una finestra di PowerShell che richiede tutte le password o chiavi segrete necessarie. Dopo avere immesso queste informazioni, la distribuzione continuerà.

   ![Finestra di PowerShell](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Al termine della distribuzione, l'app per la logica sarà attiva nel portale di Azure e verrà eseguita solo in base alla pianificazione specificata (ogni minuto). Se il trigger trova nuovi elementi, viene attivato e viene quindi creata un'istanza del flusso di lavoro che esegue le azioni dell'app per la logica. L'app per la logica invia un messaggio di posta elettronica per ogni nuovo elemento. In caso contrario, se il trigger non trova nuovi elementi, non viene attivato e non crea un'istanza del flusso di lavoro. L'app per la logica attende fino all'intervallo successivo prima di un nuovo controllo.

   Ecco alcuni esempi di messaggi di posta elettronica inviati dall'app per la logica. Se non si riceve alcun messaggio di posta elettronica, controllare la cartella della posta indesiderata.

   ![Outlook invia messaggi di posta elettronica per ogni nuovo elemento RSS](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Congratulazioni, l'app per la logica è stata compilata e distribuita con Visual Studio. Per gestire l'app per la logica ed esaminarne la cronologia di esecuzione, vedere [Gestire app per la logica con Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Aggiungere la nuova app per la logica

Quando è disponibile un progetto Gruppo di risorse esistente, è possibile aggiungere una nuova app per la logica vuota al progetto usando la finestra Struttura JSON.

1. In Esplora soluzioni aprire il file `<logic-app-name>.json`.

1. Nel menu **Visualizza** selezionare **Altre finestre** > **Struttura JSON**.

1. Per aggiungere una risorsa al file del modello, selezionare **Aggiungi risorsa** nella parte superiore della finestra della struttura JSON. In alternativa, nella finestra della struttura JSON aprire il menu di scelta rapida relativo alle **risorse** e selezionare **Aggiungi nuova risorsa**.

   ![Finestra Struttura JSON](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. Nella casella di ricerca della finestra di dialogo **Aggiungi risorsa** trovare `logic app`e selezionare **App per la logica**. Assegnare un nome all'app per la logica e quindi selezionare **Aggiungi**.

   ![Aggiungere una risorsa](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando l'app per la logica non è più necessaria, eliminare il gruppo di risorse che la contiene, insieme alle risorse correlate.

1. Accedere al [portale di Azure](https://portal.azure.com) con lo stesso account usato per creare l'app per la logica.

1. Nel menu del portale di Azure selezionare **Gruppi di risorse** oppure cercare e selezionare **Gruppi di risorse** da qualsiasi pagina. Selezionare il gruppo di risorse dell'app per la logica.

1. Nella pagina **Panoramica** selezionare **Elimina gruppo di risorse**. Immettere il nome del gruppo di risorse per conferma e selezionare **Elimina**.

   !["Gruppi di risorse" > "Panoramica" > "Elimina gruppo di risorse"](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Eliminare la soluzione di Visual Studio dal computer locale.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata, distribuita ed eseguita un'app per la logica con Visual Studio. Per informazioni sulla gestione e l'esecuzione di distribuzioni avanzate per app per la logica con Visual Studio, vedere questi articoli:

> [!div class="nextstepaction"]
> [Gestire app per la logica con Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
