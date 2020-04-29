---
title: Installare un gateway dati locale
description: Prima di poter accedere ai dati in locale delle App per la logica di Azure, è necessario scaricare e installare il gateway dati locale
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: f2f8b9f207993c49201d03d3d1fed3c5800e8780
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80673825"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installare il gateway dati locale per App per la logica di Azure

Prima di potersi [connettere alle origini dati locali da app](../logic-apps/logic-apps-gateway-connection.md)per la logica di Azure, scaricare e installare il [gateway dati](https://aka.ms/on-premises-data-gateway-installer) locale in un computer locale. Il gateway funziona come un Bridge che fornisce il trasferimento rapido dei dati e la crittografia tra le origini dati in locale e le app per la logica. È possibile usare la stessa installazione del gateway con altri servizi cloud, ad esempio Power BI, Power automatici, Power Apps e Azure Analysis Services. Per informazioni su come usare il gateway con questi servizi, vedere i seguenti articoli:

* [Microsoft Power automatizza il gateway dati locale](/power-automate/gateway-reference)
* [Gateway dati locale di Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Gateway dati locale di Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway dati locale di Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

Questo articolo illustra come scaricare, installare e configurare il gateway dati locale in modo che sia possibile accedere alle origini dati locali da app per la logica di Azure. È anche possibile ottenere altre informazioni sul [funzionamento del gateway dati](#gateway-cloud-service) più avanti in questo argomento. Per altre informazioni sul gateway, vedere [che cos'è un gateway locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)? Per automatizzare le attività di installazione e gestione del gateway, visitare PowerShell Gallery per i [cmdlet di PowerShell datagateway](https://www.powershellgallery.com/packages/DataGateway/3000.15.15).

<a name="requirements"></a>

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha un account Azure con una sottoscrizione, [iscriversi per ottenere un account Azure gratuito](https://azure.microsoft.com/free/).

  * È necessario che l'account di Azure appartenga a una singola [Directory o a un tenant di Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md#terminology). È necessario usare lo stesso account Azure per l'installazione e l'amministrazione del gateway nel computer locale.

  * Durante l'installazione del gateway, si accede con l'account Azure, che collega l'installazione del gateway al proprio account Azure e solo a tale account. In seguito, nella portale di Azure, è necessario usare lo stesso account Azure e Azure AD tenant quando si crea una risorsa del gateway di Azure che registra e attesta l'installazione del gateway. In app per la logica di Azure, trigger e azioni locali usare la risorsa gateway per la connessione alle origini dati locali.

    > [!NOTE]
    > È possibile collegare tra loro solo un'installazione del gateway e una risorsa del gateway di Azure. Non è possibile collegare la stessa installazione del gateway a più account Azure o risorse del gateway di Azure. Un account Azure può tuttavia essere collegato a più installazioni del gateway e risorse del gateway di Azure. In un'azione o un trigger locale è possibile scegliere tra le diverse sottoscrizioni di Azure e quindi selezionare una risorsa gateway associata.

  * È necessario eseguire l'accesso con un account aziendale o dell'Istituto di istruzione, noto anche come account *aziendale* , simile `username@contoso.com`a. Non è possibile usare account di @hotmail.com Azure B2B (Guest) o account Microsoft personali, ad @outlook.comesempio o.

    > [!TIP]
    > Se è stata effettuata l'iscrizione per un'offerta di Office 365 e non è stato fornito l'indirizzo di posta elettronica `username@domain.onmicrosoft.com`aziendale, l'indirizzo potrebbe essere simile a. L'account viene archiviato all'interno di un tenant in un Azure Active Directory (Azure AD). Nella maggior parte dei casi, il nome dell'entità utente (UPN) per l'account Azure AD è uguale a quello dell'indirizzo di posta elettronica.
    >
    > Per usare una [sottoscrizione standard di Visual Studio](https://visualstudio.microsoft.com/vs/pricing/) collegata a una account Microsoft, creare prima [un tenant in Azure ad](../active-directory/develop/quickstart-create-new-tenant.md) o usare la directory predefinita. Aggiungere un utente con una password alla directory e quindi concedere a tale utente l'accesso alla sottoscrizione di Azure. Sarà quindi possibile accedere durante l'installazione del gateway con questo nome utente e questa password.

* Di seguito sono riportati i requisiti per il computer locale:

  **Requisiti minimi**

  * .NET Framework 4.7.2
  * Windows 7 versione a 64 bit o Windows Server 2008 R2 (o versione successiva)

  **Requisiti consigliati**

  * CPU 8 core
  * 8 GB di memoria
  * versione a 64 bit di Windows Server 2012 R2 o versione successiva
  * Archiviazione SSD (Solid-State Drive) per lo spooling

  > [!NOTE]
  > Il gateway non supporta Windows Server Core.

* **Considerazioni correlate**

  * Installare il gateway dati locale solo in un computer locale e non in un controller di dominio. Non è necessario installare il gateway nello stesso computer dell'origine dati. È necessario un solo gateway per tutte le origini dati, pertanto non è necessario installare il gateway per ogni origine dati.

    > [!TIP]
    > Per ridurre al minimo la latenza, è possibile installare il gateway il più vicino possibile all'origine dati o nello stesso computer, presupponendo che si disponga delle autorizzazioni.

  * Installare il gateway in un computer che si trova su una rete cablata, connessa a Internet, sempre acceso e non passa alla modalità sospensione. In caso contrario, il gateway non può essere eseguito e le prestazioni potrebbero subire su una rete wireless.

  * Se si prevede di usare l'autenticazione di Windows, assicurarsi di installare il gateway in un computer membro dello stesso ambiente di Active Directory delle origini dati.

  * L'area selezionata per l'installazione del gateway è la stessa che è necessario selezionare quando in seguito si crea la risorsa del gateway di Azure per l'app per la logica. Per impostazione predefinita, questa area è la stessa in cui si trova il tenant di Azure AD che gestisce l'account Azure. Tuttavia, è possibile modificare il percorso durante l'installazione del gateway.

  * Se si sta aggiornando l'installazione del gateway alla versione più recente, disinstallare prima il gateway corrente per un'esperienza di pulizia.

  * Il gateway dispone di due modalità: la modalità standard e la modalità personale, che si applicano solo ai Power BI. Non è possibile avere più di un gateway in esecuzione nella stessa modalità nello stesso computer.

  * App per la logica di Azure supporta operazioni di lettura e scrittura tramite il gateway. Tuttavia, queste operazioni hanno [limiti sulle dimensioni del payload](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installare il gateway dati

1. [Scaricare ed eseguire il programma di installazione di gateway in un computer locale](https://aka.ms/on-premises-data-gateway-installer).

1. Verificare i requisiti minimi, tenere il percorso di installazione predefinito, accettare le condizioni per l'utilizzo e quindi selezionare **Installa**.

   ![Esaminare i requisiti e accettare le condizioni per l'utilizzo](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Al termine dell'installazione del gateway, fornire l'indirizzo di posta elettronica per l'account Azure e quindi selezionare **Accedi**, ad esempio:

   ![Accedere con l'account aziendale o dell'istituto di istruzione](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   L'installazione del gateway può essere collegata a un solo account Azure.

1. Selezionare **registra un nuovo gateway in questo computer** > **Avanti**. Questo passaggio registra l'installazione del gateway con il [servizio cloud gateway](#gateway-cloud-service).

   ![Registrare il gateway nel computer locale](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Specificare le informazioni seguenti per l'installazione del gateway:

   * Nome del gateway univoco nel tenant di Azure AD
   * Chiave di ripristino, che deve contenere almeno otto caratteri che si desidera utilizzare
   * Conferma per la chiave di ripristino

   ![Fornire informazioni per l'installazione del gateway](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Salvare e conservare la chiave di ripristino in un luogo sicuro. Questa chiave è necessaria se si vuole modificare il percorso, spostare, ripristinare o sostituire un'installazione del gateway.

   Si noti l'opzione da **aggiungere a un cluster di gateway esistente**, che è possibile selezionare quando si installano gateway aggiuntivi per gli [scenari a disponibilità elevata](#high-availability).

1. Controllare l'area per il servizio cloud gateway e il [bus di servizio di Azure](https://azure.microsoft.com/services/service-bus/) usato dall'installazione del gateway. Per impostazione predefinita, quest'area è la stessa località del tenant di Azure AD per l'account Azure.

   ![Confermare l'area per il servizio gateway e il bus di servizio](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Per accettare l'area predefinita, selezionare **Configura**. Tuttavia, se l'area predefinita non è quella più vicina, è possibile modificare l'area.

   *Perché modificare l'area per l'installazione del gateway?*

   Ad esempio, per ridurre la latenza, è possibile modificare l'area del gateway nella stessa area dell'app per la logica. In alternativa, è possibile selezionare l'area più vicina all'origine dati locale. La *risorsa per il gateway in Azure* e l'app per la logica possono avere posizioni diverse.

   1. Accanto all'area corrente, selezionare **Cambia area**.

      ![Modificare l'area del gateway corrente](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Nella pagina successiva aprire l'elenco **Seleziona area** , selezionare l'area desiderata, quindi fare clic su **fine**.

      ![Selezionare un'altra area per il servizio gateway](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Esaminare le informazioni nella finestra di conferma finale. Questo esempio usa lo stesso account per app per la logica, Power BI, Power Apps e Power automatizzate, in modo che il gateway sia disponibile per tutti questi servizi. Quando si è pronti, selezionare **Chiudi**.

   ![Confermare le informazioni sul gateway dati](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. [Creare ora la risorsa di Azure per l'installazione del gateway](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>Controllare o modificare le impostazioni di comunicazione

Il gateway dati locale dipende dal [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md) per la connettività cloud e stabilisce le connessioni in uscita corrispondenti all'area di Azure associata del gateway. Se l'ambiente di lavoro richiede che il traffico attraversi un proxy o un firewall per accedere a Internet, questa restrizione potrebbe impedire la connessione del gateway dati locale al servizio cloud gateway e al bus di servizio di Azure. Il gateway ha diverse impostazioni di comunicazione che è possibile modificare. Per altre informazioni, vedere gli argomenti seguenti:

* [Modificare le impostazioni di comunicazione per il gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Configurare le impostazioni del proxy per il gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Supporto per la disponibilità elevata

Per evitare singoli punti di errore per l'accesso ai dati locali, è possibile avere più installazioni del gateway (solo modalità standard) con ognuna in un computer diverso e impostarle come cluster o gruppo. In questo modo, se il gateway primario non è disponibile, le richieste di dati vengono instradate al secondo gateway e così via. Poiché è possibile installare un solo gateway standard in un computer, è necessario installare ogni gateway aggiuntivo presente nel cluster in un computer diverso. Tutti i connettori che funzionano con il gateway dati locale supportano la disponibilità elevata.

* È necessario disporre già di almeno un'installazione del gateway con lo stesso account Azure del gateway primario e la chiave di ripristino per l'installazione.

* Il gateway primario deve eseguire l'aggiornamento del gateway di novembre 2017 o versione successiva.

Dopo aver configurato il gateway principale, quando si installa un altro gateway, selezionare **Aggiungi a un cluster di gateway esistente**, selezionare il gateway principale, ovvero il primo gateway installato e fornire la chiave di ripristino per il gateway. Per altre informazioni, vedere [Cluster a disponibilità elevata per il gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Modificare la posizione, eseguire la migrazione, ripristinare o sostituire un gateway esistente

Per modificare la posizione del gateway, spostare l'installazione del gateway in un nuovo computer, ripristinare un gateway danneggiato o assumere la proprietà di un gateway esistente, è necessaria la chiave di ripristino che è stata specificata durante l'installazione del gateway.

1. Eseguire il programma di installazione del gateway nel computer che dispone del gateway esistente. Se non si dispone del programma di installazione del gateway più recente, [scaricare la versione più recente del gateway](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Prima di ripristinare il gateway nel computer in cui è presente l'installazione del gateway originale, è necessario prima disinstallare il gateway nel computer. Questa azione disconnette il gateway originale.
   > Se si rimuove o si elimina un cluster di gateway per qualsiasi servizio cloud, non è possibile ripristinare tale cluster.

1. Dopo l'apertura del programma di installazione, accedere con lo stesso account Azure usato per installare il gateway.

1. Selezionare **Esegui migrazione, ripristino o acquisizione di un gateway** > esistente**Avanti**, ad esempio:

   ![Selezionare "Eseguire la migrazione, ripristinare o acquisire la proprietà di un gateway esistente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Selezionare tra i cluster e i gateway disponibili e immettere la chiave di ripristino per il gateway selezionato, ad esempio:

   ![Selezionare il gateway e fornire la chiave di ripristino](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Per modificare l'area, selezionare **Cambia area**e selezionare la nuova area.

1. Quando si è pronti, selezionare **Configura** per poter completare l'attività.

## <a name="tenant-level-administration"></a>Amministrazione a livello di tenant

Per ottenere la visibilità di tutti i gateway dati locali in un tenant di Azure AD, gli amministratori globali in tale tenant possono accedere all'interfaccia di [amministrazione di Power Platform](https://powerplatform.microsoft.com) come amministratore tenant e selezionare l'opzione **gateway dati** . Per altre informazioni, vedere [amministrazione a livello di tenant per il gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Riavviare il gateway

Per impostazione predefinita, l'installazione del gateway nel computer locale viene eseguita come account di servizio di Windows denominato "servizio gateway dati locale". Tuttavia, l'installazione del gateway usa `NT SERVICE\PBIEgwService` il nome per le credenziali dell'account "Accedi come" e ha le autorizzazioni di accesso come servizio.

> [!NOTE]
> L'account del servizio Windows è diverso dall'account usato per la connessione alle origini dati locali e dall'account Azure usato quando si accede ai servizi cloud.

Analogamente a qualsiasi altro servizio Windows, è possibile avviare e arrestare il gateway in diversi modi. Per altre informazioni, vedere [riavviare un gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Funzionamento del gateway

Gli utenti dell'organizzazione possono accedere ai dati locali per i quali hanno già accesso autorizzato. Tuttavia, prima che questi utenti possano connettersi all'origine dati locale, è necessario installare e configurare un gateway dati locale. In genere, un amministratore è la persona che installa e configura un gateway. Queste azioni potrebbero richiedere autorizzazioni di amministratore del server o una conoscenza speciale dei server locali.

Il gateway consente di semplificare le comunicazioni in background più veloci e sicure. Questa comunicazione scorre tra un utente nel cloud, il servizio cloud gateway e l'origine dati locale. Il servizio cloud gateway crittografa e archivia le credenziali dell'origine dati e i dettagli del gateway. Il servizio instrada anche le query e i relativi risultati tra l'utente, il gateway e l'origine dati locale.

Il gateway funziona con i firewall e usa solo connessioni in uscita. Tutto il traffico ha origine come traffico in uscita protetto dall'agente gateway. Il gateway inoltra i dati dalle origini locali nei canali crittografati tramite il [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md). Il bus di servizio crea un canale tra il gateway e il servizio chiamante, ma non archivia alcun dato. Tutti i dati che attraversano il gateway sono crittografati.

![Architettura per il gateway dati locale](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> A seconda del servizio cloud, potrebbe essere necessario configurare un'origine dati per il gateway.

Questi passaggi descrivono cosa accade quando si interagisce con un elemento connesso a un'origine dati locale:

1. Il servizio cloud crea una query, insieme alle credenziali crittografate per l'origine dati. Il servizio invia quindi la query e le credenziali alla coda del gateway per l'elaborazione.

1. Il servizio cloud del gateway analizza la query e inserisce la richiesta nel bus di servizio di Azure.

1. Il bus di servizio di Azure invia le richieste in sospeso al gateway.

1. Il gateway ottiene la query, decrittografa le credenziali e si connette a una o più origini dati con tali credenziali.

1. Il gateway Invia la query all'origine dati per l'esecuzione di.

1. I risultati vengono quindi inviati dall'origine dati al gateway e quindi al servizio cloud del gateway. Infine, il servizio cloud del gateway usa i risultati.

### <a name="authentication-to-on-premises-data-sources"></a>Autenticazione a origini dati locali

Le credenziali archiviate vengono usate per la connessione dal gateway alle origini dati locali. Indipendentemente dall'utente, il gateway usa le credenziali archiviate per connettersi. Potrebbero essere presenti eccezioni di autenticazione per servizi specifici, ad esempio DirectQuery e LiveConnect per Analysis Services in Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

I servizi cloud Microsoft usano [Azure ad](../active-directory/fundamentals/active-directory-whatis.md) per autenticare gli utenti. Un tenant Azure AD contiene i nomi utente e i gruppi di sicurezza. In genere, l'indirizzo di posta elettronica usato per l'accesso è uguale al nome dell'entità utente (UPN) per l'account.

### <a name="what-is-my-upn"></a>Che cos'è il nome UPN?

Se non si è un amministratore di dominio, è possibile che non si conosca l'UPN. Per trovare l'UPN per l'account, eseguire il `whoami /upn` comando dalla workstation. Sebbene il risultato appaia come un indirizzo di posta elettronica, il risultato è l'UPN per l'account di dominio locale.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Sincronizzare un'istanza di Active Directory locale con Azure AD

Il nome UPN per gli account di Active Directory locali e gli account di Azure AD devono essere uguali. Assicurarsi quindi che ogni account Active Directory locale corrisponda all'account di Azure AD. I servizi cloud conoscono solo gli account in Azure AD. Non è quindi necessario aggiungere un account all'Active Directory locale. Se l'account non esiste in Azure AD, non è possibile usare tale account.

Ecco i modi in cui è possibile associare gli account di Active Directory locali con Azure AD.

* Aggiungere gli account manualmente al Azure AD.

  Creare un account nel portale di Azure o nell'interfaccia di amministrazione di Microsoft 365. Verificare che il nome dell'account corrisponda all'UPN per l'account Active Directory locale.

* Sincronizzare gli account locali con il tenant di Azure AD usando lo strumento Azure Active Directory Connect.

  Lo strumento Azure AD Connect fornisce opzioni per la sincronizzazione della directory e la configurazione dell'autenticazione. Queste opzioni includono sincronizzazione dell'hash delle password, autenticazione pass-through e Federazione. Se non si è un amministratore tenant o un amministratore di dominio locale, contattare l'amministratore IT per ottenere Azure AD Connect configurazione. Azure AD Connect garantisce che il nome UPN del Azure AD corrisponda al nome UPN del Active Directory locale. Questa corrispondenza consente di usare Analysis Services connessioni dinamiche con funzionalità di Power BI o Single Sign-On (SSO).

  > [!NOTE]
  > La sincronizzazione degli account con lo strumento Azure AD Connect crea nuovi account nel tenant di Azure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Domande frequenti e risoluzione dei problemi

Per altre informazioni, vedere gli argomenti seguenti:

* [Domande frequenti sul gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Risolvere i problemi del gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorare e ottimizzare le prestazioni del gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi ai dati locali da app per la logica](../logic-apps/logic-apps-gateway-connection.md)
* [Funzionalità di Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connettori per App per la logica di Azure](../connectors/apis-list.md)
