---
title: Creare un account di Video Indexer connesso ad Azure
titleSuffix: Azure Media Services
description: Informazioni su come creare un account Video Indexer connesso ad Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: fe6be5778997f0ef4a3f53ae45d17352eca60d8e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727261"
---
# <a name="create-a-video-indexer-account"></a>Creazione di un account Video Indexer

Al momento della creazione di un account di Video Indexer, è possibile scegliere un account di valutazione gratuito (in cui si ottiene un certo numero di minuti di indicizzazione gratuiti) o un'opzione a pagamento (senza limiti di quota). Con la versione di valutazione gratuita, Video Indexer offre fino a 600 minuti di indicizzazione gratuita per gli utenti di siti Web e fino a 2.400 minuti di indicizzazione gratuita per gli utenti di API. Con l'opzione a pagamento, è possibile creare un account di Video Indexer connesso alla sottoscrizione di Azure. Si pagano i minuti indicizzati. Per altre informazioni, vedere [Prezzi di Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).

Questo articolo illustra come creare un account di Video Indexer connesso a una sottoscrizione di Azure e a un account di Servizi multimediali di Azure. L'argomento illustra i passaggi per la connessione ad Azure usando il flusso automatico (impostazione predefinita). Viene anche descritto come connettersi ad Azure manualmente (impostazioni avanzate).

Se si passa da una *versione di valutazione* a un account a *pagamento* video indexer, è possibile scegliere di copiare tutti i video e la personalizzazione del modello nel nuovo account, come illustrato nella sezione [importare il contenuto dall'account di valutazione](#import-your-content-from-the-trial-account) .

Questo articolo illustra anche il [collegamento di un account video Indexer ad Azure per enti pubblici](#video-indexer-in-azure-government).

## <a name="prerequisites-for-connecting-to-azure"></a>Prerequisiti per la connessione ad Azure

* Una sottoscrizione di Azure.

    Se non si dispone ancora di una sottoscrizione di Azure, registrarsi per la [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).
* Un dominio Azure Active Directory (Azure AD).

    Se non si dispone di un dominio di Azure AD, creare il dominio nella sottoscrizione di Azure. Per ulteriori informazioni, vedere [gestione dei nomi di dominio personalizzati nel Azure ad](../../active-directory/enterprise-users/domains-manage.md)
* Un utente nel dominio di Azure AD con un ruolo di **amministratore dell'applicazione** . Questo membro verrà usato per la connessione dell'account di Video Indexer ad Azure.

    Questo utente deve essere Azure AD utente con un account aziendale o dell'Istituto di istruzione. Non usare un account personale, ad esempio outlook.com, live.com o hotmail.com.

    ![tutti gli utenti Azure AD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Prerequisiti aggiuntivi per il flusso automatico

* Un utente e un membro del dominio di Azure AD.

    Questo membro verrà usato per la connessione dell'account di Video Indexer ad Azure.

    L'utente deve essere un membro nella sottoscrizione di Azure con un ruolo **Proprietario** o con i ruoli **Collaboratore** e **Amministratore accessi utente**. Un utente può essere aggiunto due volte, con due ruoli. una volta con quello di collaboratore e una seconda volta con quello di amministratore degli accessi. Per altre informazioni, vedere [visualizzare l'accesso a un utente per le risorse di Azure](../../role-based-access-control/check-access.md).

    ![controllo di accesso](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Prerequisiti aggiuntivi per il flusso manuale

* Registrare il provider di risorse EventGrid usando il portale di Azure.

    Nel [portale di Azure](https://portal.azure.com/) passare a **Sottoscrizioni**->[sottoscrizione]->**Provider di risorse**.

    Cercare **Microsoft.Media** e **Microsoft.EventGrid**. Se lo stato non è "Registrato", fare clic su **Registra**. La registrazione richiede alcuni minuti.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="create-a-new-account-on-azure"></a>Creare un nuovo account in Azure 

> [!NOTE]
> Se la sottoscrizione di Azure usa l'autenticazione a più fattori basata su certificati, è fondamentale eseguire i passaggi seguenti in un dispositivo in cui sono installati i certificati necessari.

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
1. Selezionare il pulsante **Crea account senza limiti** :

    ![Crea nuovo account Video Indexer](./media/create-account/create-unlimited-account.png)
1. Quando viene visualizzato l'elenco delle sottoscrizioni, selezionare la sottoscrizione da usare.

    ![Connetti Video Indexer ad Azure](./media/create-account/new-account-on-azure-subscription.png)
1. Selezionare un'area di Azure dalle posizioni supportate: Stati Uniti occidentali 2, Europa settentrionale o Asia orientale.
1. In **Account Servizi multimediali di Azure** scegliere una delle opzioni seguenti:

    * Per creare un nuovo account di Servizi multimediali, selezionare **Crea nuovo gruppo di risorse**. Specificare il nome per il gruppo di risorse.

        Azure creerà il nuovo account nella sottoscrizione, incluso un nuovo account di Archiviazione di Azure.  
    * Per usare un account di Servizi multimediali esistente, selezionare **Usa risorsa esistente**. Nell'elenco degli account selezionare l'account.

        L'account di Servizi multimediali deve avere la stessa area dell'account di Video Indexer.

        > [!NOTE]
        > Per ridurre al minimo la durata dell'indicizzazione e la velocità effettiva bassa, è consigliabile modificare il tipo e il numero di [unità riservate](../previous/media-services-scale-media-processing-overview.md ) nell'account di servizi multimediali a **10 unità riservate S3**. Vedere [usare il portale per modificare le unità riservate](../previous/media-services-portal-scale-media-processing.md). Le unità riservate vengono addebitate all'account, visualizzano i [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/media-services/#analytics).
    * Per configurare manualmente la connessione, selezionare il collegamento **passa a configurazione manuale** .

        Per informazioni dettagliate, vedere la sezione seguente [Connettersi ad Azure manualmente (opzione avanzata)](#connect-to-azure-manually-advanced-option).
1. Al termine dell'operazione, scegliere **Crea**. L'operazione potrebbe richiedere alcuni minuti.

    Dopo la connessione ad Azure, il nuovo account di Video Indexer viene visualizzato nell'elenco degli account:

    ![nuovo account](./media/create-account/new-account.png)
1. Assicurarsi che l'endpoint di streaming dell'account di servizi multimediali sia in esecuzione prima di poter riprodurre i video nell'app Web Video Indexer (premere Start se è stato interrotto).

> [!TIP]
> Per assegnare un nome di visualizzazione intuitivo all'account, passare a **Impostazioni**.

## <a name="connect-to-azure-manually-advanced-option"></a>Connettersi ad Azure manualmente (opzione avanzata)

Se la connessione ad Azure non è riuscita, è possibile tentare di risolvere il problema connettendosi manualmente.

> [!NOTE]
> È consigliabile che nella stessa area siano presenti i tre account seguenti: l'account Video Indexer che si sta connettendo con l'account di servizi multimediali, nonché l'account di archiviazione di Azure connesso allo stesso account di servizi multimediali.

### <a name="create-and-configure-a-media-services-account"></a>Creare e configurare un account di Servizi multimediali di Azure

1. Usare il portale di [Azure](https://portal.azure.com/) per creare un account di Servizi multimediali di Azure, come descritto in [Creare un account](../previous/media-services-portal-create-account.md).

     Verificare che l'account di servizi multimediali sia stato creato con le API classiche. 
 
    ![API classica di servizi multimediali](./media/create-account/enable-classic-api.png)


    Quando si crea un account di archiviazione per l'account di servizi multimediali, selezionare **archiviazione V2** per tipo di account e **ridondanza geografica** (GRS) per i campi di replica.

    ![Nuovo account AMS](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > Assicurarsi di annotare i nomi della risorsa e dell'account di Servizi multimediali. Saranno necessari per i passaggi nella sezione successiva.
1. Modificare il tipo e il numero di [unità riservate](../previous/media-services-scale-media-processing-overview.md ) in **10 unità riservate S3** nell'account di servizi multimediali creato. Vedere [usare il portale per modificare le unità riservate](../previous/media-services-portal-scale-media-processing.md).

    Le unità riservate vengono addebitate all'account, Visualizza i [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/media-services/#analytics).
1. Prima di poter riprodurre i video nell'app Web di Video Indexer, è necessario avviare l' **endpoint di streaming** predefinito del nuovo account di servizi multimediali.

    Nel nuovo account di servizi multimediali selezionare **endpoint di streaming**. Selezionare quindi l'endpoint di streaming e premere Avvia.

    ![Endpoint di streaming](./media/create-account/create-ams-account-se.png)
4. Per Video Indexer di eseguire l'autenticazione con l'API di servizi multimediali, è necessario creare un'app AD. I passaggi seguenti consentono di eseguire il processo di autenticazione Azure AD descritto in [Introduzione all'autenticazione di Azure AD tramite il portale di Azure](../previous/media-services-portal-get-started-with-aad.md):

    1. Nel nuovo account di Servizi multimediali selezionare **Accesso all'API**.
    2. Selezionare il [metodo di autenticazione basata sull'entità servizio](../previous/media-services-portal-get-started-with-aad.md).
    3. Ottenere l'ID client e il segreto client

        Dopo aver selezionato **le chiavi delle impostazioni** -> , aggiungere la **Descrizione**, fare clic su **Salva** e il valore della chiave viene popolato.

        Se la chiave scade, il proprietario dell'account dovrà contattare Video Indexer supporto per rinnovare la chiave.

        > [!NOTE]
        > Assicurarsi di annotare il valore della chiave e l'ID applicazione. Sarà necessaria per i passaggi descritti nella sezione successiva.

### <a name="connect-manually"></a>Connettersi manualmente

Nella finestra di dialogo **Crea un nuovo account in una sottoscrizione di Azure** della pagina [video Indexer](https://www.videoindexer.ai/) selezionare il collegamento **passa a configurazione manuale** .

Nella finestra di dialogo specificare le informazioni seguenti:

|Impostazione|Descrizione|
|---|---|
|Area dell'account di Video Indexer|Il nome dell'area dell'account di Video Indexer. Per ottenere prestazioni migliori e ridurre i costi, è consigliabile specificare il nome dell'area in cui si trovano le risorse di servizi multimediali di Azure e l'account di archiviazione di Azure. |
|Tenant di Azure AD|Il nome del tenant di Azure AD, ad esempio "contoso.onmicrosoft.com". Le informazioni sul tenant possono essere recuperate dal portale di Azure. Posizionare il cursore sul nome dell'utente connesso nell'angolo in alto a destra. Trovare il nome a destra di **Dominio**.|
|ID sottoscrizione|La sottoscrizione di Azure in cui deve essere creata la connessione. L'ID sottoscrizione può essere recuperato dal portale di Azure. Selezionare **tutti i servizi** nel riquadro a sinistra e cercare "sottoscrizioni". Selezionare **Sottoscrizioni** e scegliere l'ID desiderato dall'elenco delle sottoscrizioni.|
|Nome del gruppo di risorse di Servizi multimediali di Azure|Nome del gruppo di risorse in cui è stato creato l'account di Servizi multimediali.|
|Nome della risorsa dei servizi multimediali|Nome dell'account di Servizi multimediali di Azure creato nella sezione precedente.|
|ID applicazione|ID dell'applicazione di Azure AD con le autorizzazioni per l'account di Servizi multimediali specificato creato nella sezione precedente.|
|Chiave applicazione|Chiave dell'applicazione Azure AD creata nella sezione precedente. |

### <a name="import-your-content-from-the-trial-account"></a>Importa il contenuto dall'account di *valutazione*

Quando si crea un nuovo account, è possibile importare il contenuto dall'account di *valutazione* al nuovo account. Se si seleziona l'opzione *Importa* nella finestra di dialogo **Crea un nuovo account in una sottoscrizione di Azure** , tutte le personalizzazioni di supporti e modelli di contenuto verranno copiate dall'account di *valutazione* al nuovo account.

La possibilità di importare il contenuto è valida per gli approcci automatici e manuali descritti in precedenza.

> [!NOTE]
> Il contenuto può essere importato una sola volta da ogni account.
>
> L'account di *valutazione* non è availagle nel cloud di Azure per enti pubblici.

## <a name="azure-media-services-considerations"></a>Considerazioni su servizi multimediali di Azure

Tenere presenti le seguenti considerazioni relative a Servizi multimediali di Azure:

* Se si prevede di connettersi a un account di servizi multimediali esistente, verificare che l'account di servizi multimediali sia stato creato con le API classiche. 
 
    ![API classica di servizi multimediali](./media/create-account/enable-classic-api.png)
* Se ci si connette a un account di servizi multimediali esistente, Video Indexer non modifica la configurazione di media **reserved Units** esistente.

   Potrebbe essere necessario modificare il tipo e il numero di media reserved unit in base al carico pianificato. Tenere presente che se il carico è elevato e le unità o la velocità non sono sufficienti, possono verificarsi errori di timeout durante l'elaborazione video.
* Se viene eseguita la connessione a un nuovo account di Servizi multimediali, Video Indexer avvia automaticamente l'**endpoint di streaming** predefinito nell'account:

    ![Endpoint di streaming di Servizi multimediali](./media/create-account/ams-streaming-endpoint.png)

    Gli endpoint di streaming hanno un tempo di avvio considerevole. Potrebbero quindi essere necessari alcuni minuti a partire dal momento in cui l'account è stato connesso ad Azure fino a quando i video non possono essere trasmessi e controllati nell'app Web Video Indexer.
* Se ci si connette a un account di servizi multimediali esistente, Video Indexer non modifica la configurazione dell'endpoint di streaming predefinito. Se non è presente alcun **endpoint di streaming** in esecuzione, non è possibile guardare i video di questo account di servizi multimediali o in video Indexer.
* Se la connessione viene eseguita automaticamente, Video Indexer imposta le **Unità riservate** multimediali su 10 unità S3:

    ![Unità riservate di Servizi multimediali](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-indexer-account"></a>Automatizzare la creazione dell'account di Video Indexer

Per automatizzare la creazione dell'account è necessario procedere in due passaggi:
 
1. Usare Azure Resource Manager per creare un account di servizi multimediali di Azure e Azure AD applicazione.

    Vedere un esempio del [modello di creazione dell'account di servizi multimediali](https://github.com/Azure-Samples/media-services-v3-arm-templates).
1. Chiamare [Create-account con l'applicazione Servizi multimediali e Azure ad](https://videoindexer.ai.azure.us/account/login?source=apim).

## <a name="video-indexer-in-azure-government"></a>Video Indexer in Azure per enti pubblici

### <a name="prerequisites-for-connecting-to-azure-government"></a>Prerequisiti per la connessione ad Azure per enti pubblici

-   Una sottoscrizione di Azure in [Azure per enti pubblici](../../azure-government/index.yml).
- Un account Azure AD in Azure per enti pubblici.
- Tutti i prerequisiti per le autorizzazioni e le risorse descritte in precedenza in [prerequisiti per la connessione ad Azure](#prerequisites-for-connecting-to-azure).

### <a name="create-new-account-via-the-azure-government-portal"></a>Creare un nuovo account tramite il portale di Azure per enti pubblici

> [!NOTE]
> Il cloud di Azure per enti pubblici non include un'esperienza di *valutazione* di video Indexer.

Per creare un account a pagamento tramite il portale di Video Indexer:

1. Passare a https://videoindexer.ai.azure.us 
1. Accedere con l'account Azure AD di Azure per enti pubblici.
1.  Se non si dispone di account Video Indexer in Azure per enti pubblici di cui si è proprietari o collaboratori, si otterrà un'esperienza vuota dalla quale è possibile iniziare a creare l'account. 

    Il resto del flusso è come descritto in precedenza. solo le aree da selezionare saranno aree governative in cui è disponibile l'indicizzatore video 

    Se si è già un collaboratore o un amministratore di uno o più account Video Indexer in Azure per enti pubblici, si verrà portati a tale account e da qui è possibile avviare una procedura per la creazione di un altro account, se necessario, come descritto in precedenza.
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>Creare un nuovo account tramite l'API in Azure per enti pubblici

Per creare un account a pagamento in Azure per enti pubblici, seguire le istruzioni riportate in [creare un account a pagamento](). Questo endpoint API include solo le aree del cloud per enti pubblici.

### <a name="limitations-of-video-indexer-on-azure-government"></a>Limitazioni dei Video Indexer in Azure per enti pubblici

*   Nessuna moderazione manuale del contenuto disponibile nel cloud per enti pubblici. 

    Nel cloud pubblico quando il contenuto viene ritenuto offensivo in base a una moderazione del contenuto, il cliente può chiedere a un utente di esaminare il contenuto e potenzialmente annullare tale decisione.  
*   Nessun account di valutazione. 
* Descrizione Bing: in gov cloud non viene presentata una descrizione delle celebrità e delle entità denominate identificate. Si tratta solo di una funzionalità dell'interfaccia utente. 

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa esercitazione, eliminare le risorse che non si prevede di usare.

### <a name="delete-a-video-indexer-account"></a>Eliminare un account di Video Indexer

Se si desidera eliminare un account di Video Indexer, è possibile eliminare l'account dal sito Web di Video Indexer. Per eliminare l'account, è necessario essere il proprietario.

Selezionare l'account-> **Impostazioni**  ->  **Elimina questo account**. 

L'account verrà eliminato definitivamente tra 90 giorni.

## <a name="next-steps"></a>Passaggi successivi

È possibile interagire a livello di codice con l'account di valutazione e/o con gli account Video Indexer connessi ad Azure seguendo le istruzioni riportate in: [usare le API](video-indexer-use-apis.md).

È consigliabile usare lo stesso utente di Azure AD usato durante la connessione ad Azure.