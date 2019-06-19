---
title: Roadmap di installazione di Azure AD Connect e Azure AD Connect Health. | Microsoft Docs
description: Questo documento offre una panoramica delle opzioni e dei percorsi di installazione disponibili per Azure AD Connect e Azure AD Connect Health.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d6cce859aa26935f0b6007fe8e5ff2c2239b210
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109458"
---
# <a name="azure-ad-connect-and-azure-ad-connect-health-installation-roadmap"></a>Roadmap di installazione di Azure AD Connect e Azure AD Connect Health

## <a name="install-azure-ad-connect"></a>Installare Azure AD Connect

> [!IMPORTANT]
> Microsoft non supporta la modifica o l'uso del servizio di sincronizzazione Azure AD Connect al di fuori delle azioni descritte in modo formale. Ognuna di queste azioni potrebbe provocare uno stato incoerente o non supportato del sevizio di sincronizzazione Azure AD Connect. Microsoft pertanto non offre il supporto tecnico per distribuzioni di questo tipo.

Il download per Azure AD Connect è disponibile nell' [Area download Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).

| Soluzione | Scenario |
| --- | --- |
| Prima di iniziare: [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) |<li>Passaggi da completare prima di iniziare a installare Azure AD Connect.</li> |
| [Impostazioni rapide](how-to-connect-install-express.md) |<li>Se è disponibile una singola foresta Active Directory, è consigliabile usare questa opzione.</li> <li>Accesso utente con la stessa password tramite la sincronizzazione delle password.</li> |
| [Impostazioni personalizzate](how-to-connect-install-custom.md) |<li>Da usare quando sono presenti più foreste. Supporta numerose [topologie](plan-connect-topologies.md) locali.</li> <li>Personalizzare l'opzione di accesso, ad esempio, l'autenticazione pass-through, con AD FS per la federazione o un 1 di identità di terze parti.</li> <li>Personalizzare le funzionalità di sincronizzazione, ad esempio filtro e writeback.</li> |
| [Aggiornamento da DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Da usare se è presente un server DirSync esistente già in esecuzione.</li> |
| [Aggiornamento da Azure AD Sync o Azure AD Connect](how-to-upgrade-previous-version.md) |<li>Sono disponibili diversi metodi.</li> |

[Dopo l'installazione](how-to-connect-post-installation.md) sarà necessario verificare se funziona come previsto e assegnare le licenze agli utenti.

### <a name="next-steps-to-install-azure-ad-connect"></a>Installare Azure AD Connect - Passaggi successivi
|Argomento |Collegamento|  
| --- | --- |
|Scaricare Azure AD Connect | [Scaricare Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Eseguire l'installazione con le Impostazioni rapide | [Installazione rapida di Azure AD Connect](./how-to-connect-install-express.md)|
|Eseguire l'installazione mediante le impostazioni personalizzate | [Installazione personalizzata di Azure AD Connect](./how-to-connect-install-custom.md)|
|Aggiornamento da DirSync | [Aggiornamento dallo strumento di sincronizzazione di Azure AD (DirSync)](./how-to-dirsync-upgrade-get-started.md)|
|Dopo l'installazione | [Verificare l'installazione e assegnare le licenze](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Altre informazioni su come installare Azure AD Connect
Preparazione per la gestione delle attività [operative](how-to-connect-sync-operations.md) . È consigliabile avere un server di standby per agevolare il failover in caso di [emergenza](how-to-connect-sync-staging-server.md#disaster-recovery). Se si prevede di apportare modifiche frequenti alla configurazione, è consigliabile valutare l'uso di un server in [modalità di staging](how-to-connect-sync-staging-server.md) .

|Argomento |Collegamento|  
| --- | --- |
|Topologie supportate | [Topologie per Azure AD Connect](plan-connect-topologies.md)|
|Concetti relativi alla progettazione | [Concetti relativi alla progettazione per Azure AD Connect](plan-connect-design-concepts.md)|
|Account usati per l'installazione | [Altre informazioni sulle credenziali e le autorizzazioni di Azure AD Connect](reference-connect-accounts-permissions.md)|
|Pianificazione per la gestione delle attività operative | [Servizio di sincronizzazione Azure AD Connect: Attività operative e considerazioni](how-to-connect-sync-operations.md)|
|Opzioni di accesso utente | [Opzioni di accesso utente di Azure AD Connect](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>Configurare le funzionalità di sincronizzazione
Azure AD Connect include numerose funzionalità che è possibile abilitare o che sono abilitate per impostazione predefinita. Alcune funzionalità possono talvolta richiedere più attività di configurazione in topologie e scenari specifici.

[filtro](how-to-connect-sync-configure-filtering.md) consente di limitare gli oggetti da sincronizzare con Azure AD. Per impostazione predefinita, vengono sincronizzati tutti gli utenti, i contatti, i gruppi e i computer Windows 10. È possibile modificare il filtro sulla base di domini, unità organizzative o attributi.

La [sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md) consente di sincronizzare l'hash delle password di Active Directory con Azure AD. L'utente finale può usare la stessa password in locale e nel cloud, ma gestirla da una sola posizione. Poiché usa l'istanza di Active Directory locale come autorità di certificazione, è anche possibile usare i criteri password personali.

[writeback delle password](../authentication/quickstart-sspr.md) consente agli utenti di modificare e reimpostare le proprie password nel cloud, applicando i criteri per le password locali.

[Il writeback dei dispositivi](how-to-connect-device-writeback.md) consentirà un dispositivo registrato in Azure AD essere eseguito il writeback in Active Directory locale in modo che può essere utilizzato per l'accesso condizionale.

La funzionalità di [prevenzione delle eliminazioni accidentali](how-to-connect-sync-feature-prevent-accidental-deletes.md) è attivata per impostazione predefinita e proteggere la directory cloud da numerose eliminazioni contemporanee. Per impostazione predefinita, consente 500 eliminazioni per ogni esecuzione. È possibile modificare questa impostazione in base alle dimensioni dell'organizzazione.

[aggiornamento automatico](how-to-connect-install-automatic-upgrade.md) è abilitato per impostazione predefinita per le installazioni con impostazioni rapide e assicura che Azure AD Connect sia sempre aggiornato alla versione più recente.

### <a name="next-steps-to-configure-sync-features"></a>Configurare le funzionalità di sincronizzazione - Passaggi successivi
|Argomento |Collegamento|  
| --- | --- |
|Configurare il filtro | [Servizio di sincronizzazione Azure AD Connect: Configurare il filtro](how-to-connect-sync-configure-filtering.md)|
|Sincronizzazione dell'hash delle password | [Sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md)|
|Autenticazione pass-through | [Autenticazione pass-through](how-to-connect-pta.md)
|writeback delle password | [Introduzione alla gestione delle password](../authentication/quickstart-sspr.md)|
|Writeback dispositivi | [Abilitazione del writeback dei dispositivi in Azure AD Connect](how-to-connect-device-writeback.md)|
|prevenzione delle eliminazioni accidentali | [Servizio di sincronizzazione Azure AD Connect: prevenzione delle eliminazioni accidentali](how-to-connect-sync-feature-prevent-accidental-deletes.md)|
|aggiornamento automatico | [Azure AD Connect: aggiornamento automatico](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Personalizzare il servizio di sincronizzazione Azure AD Connect
Il servizio di sincronizzazione Azure AD Connect viene fornito con una configurazione predefinita adatta alla maggior parte dei clienti e delle topologie. In alcune situazioni, tuttavia, la configurazione predefinita non funziona e richiede alcune rettifiche. Le modifiche sono supportate, purché in linea con quanto documentato in questa sezione e negli argomenti collegati.

Se è la prima volta che si usa una topologia di sincronizzazione, è consigliabile vedere le nozioni di base e i termini usati nella sezione relativa ai [concetti tecnici](how-to-connect-sync-technical-concepts.md). Azure AD Connect è l'evoluzione di MIIS2003, ILM2007 e FIM2010. Anche se alcuni elementi sono identici, sono state introdotte numerose modifiche.

La [configurazione predefinita](concept-azure-ad-connect-sync-default-configuration.md) presuppone che possano essere presenti più foreste. In queste topologie un oggetto utente può essere rappresentato come un contatto in un'altra foresta. L'utente può anche avere una cassetta postale collegata in un'altra foresta di risorse. Il comportamento della configurazione predefinita è descritto in [utenti e contatti](concept-azure-ad-connect-sync-user-and-contacts.md).

Il modello di configurazione sincronizzato è chiamato [provisioning dichiarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md). I flussi di attributi avanzati usano [funzioni](reference-connect-sync-functions-reference.md) per esprimere le trasformazioni degli attributi. È possibile visualizzare ed esaminare l'intera configurazione con gli strumenti disponibili in Azure AD Connect. Per apportare modifiche alla configurazione, assicurarsi di seguire le [procedure consigliate](how-to-connect-sync-best-practices-changing-default-configuration.md) per semplificare l'adozione delle nuove versioni.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Personalizzare il servizio di sincronizzazione Azure AD Connect - Passaggi successivi
|Argomento |Collegamento|  
| --- | --- |
|Tutti gli articoli sul servizio di sincronizzazione Azure AD Connect | [Servizio di sincronizzazione Azure AD Connect](how-to-connect-sync-whatis.md)|
|concetti tecnici | [Servizio di sincronizzazione Azure AD Connect: concetti tecnici](how-to-connect-sync-technical-concepts.md)|
|Informazioni sulla configurazione predefinita | [Servizio di sincronizzazione Azure AD Connect: informazioni sulla configurazione predefinita](concept-azure-ad-connect-sync-default-configuration.md)|
|Informazioni su utenti e contatti | [Servizio di sincronizzazione Azure AD Connect: informazioni su utenti e contatti](concept-azure-ad-connect-sync-user-and-contacts.md)|
|provisioning dichiarativo | [Servizio di sincronizzazione Azure AD Connect: informazioni sulle espressioni di provisioning dichiarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)|
|Modificare la configurazione predefinita | [Procedure consigliate per modificare la configurazione predefinita](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Configurare le funzionalità di federazione

Azure AD Connect offre numerose funzionalità che semplificano la federazione con Azure AD tramite AD FS e la gestione della relazione di trust federativa. Azure AD Connect supporta AD FS in Windows Server 2012 R2 o versioni successive.

[Aggiornare il certificato SSL della farm AD FS](how-to-connect-fed-ssl-update.md) anche se non si usa Azure AD Connect per gestire la relazione di trust federativa.

[Aggiungere un server AD FS](how-to-connect-fed-management.md#addadfsserver) alla farm per espandere la farm come necessario.

[Ripristinare la relazione di trust](how-to-connect-fed-management.md#repairthetrust) con Azure AD in pochi semplici clic.

AD FS può essere configurato per supportare [più domini](how-to-connect-install-multiple-domains.md). Ad esempio, possono essere presenti più domini principali da usare per la federazione.

Se il server AD FS non è stato configurato per aggiornare automaticamente i certificati da Azure AD o se si utilizza una soluzione non AD FS, si riceve una notifica quando occorre [aggiornare i certificati](how-to-connect-fed-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Configurare le funzionalità di federazione - Passaggi successivi
|Argomento |Collegamento|  
| --- | --- |
|Tutti gli articoli su AD FS | [Azure AD Connect e federazione](how-to-connect-fed-whatis.md)|
|Configurare AD FS con sottodomini | [Supporto di più domini per la federazione con Azure AD](how-to-connect-install-multiple-domains.md)|
|Gestire la farm AD FS | [Gestione e personalizzazione di AD FS con Azure AD Connect](how-to-connect-fed-management.md)|
|Aggiornare manualmente i certificati di federazione | [Rinnovo dei certificati di federazione per Office 365 e Azure AD](how-to-connect-fed-o365-certs.md)|


## <a name="get-started-with-azure-ad-connect-health"></a>Introduzione ad Azure AD Connect Health
Per iniziare a usare Azure AD Connect Health sono molto semplici, seguire questa procedura:

1. [Ottenere Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) o [avviare una versione di valutazione](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Scaricare e installare gli agenti di Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) nei server di identità.
3. Visualizzare il dashboard di Azure AD Connect Health all'indirizzo [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Tenere presente che, prima di visualizzare i dati nel dashboard di Azure AD Connect Health, è necessario installare gli agenti di Azure AD Connect Health nei server di destinazione.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Scaricare e installare l'agente di Azure AD Connect Health
* Verificare che siano [soddisfatti i requisiti](how-to-connect-health-agent-install.md#requirements) per Azure AD Connect Health.
* Introduzione ad Azure AD Connect Health per AD FS
    * [Scaricare l'agente di Azure AD Connect Health per AD FS.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Vedere le istruzioni per l'installazione](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Introduzione ad Azure AD Connect Health per la sincronizzazione
    * [Scaricare e installare la versione più recente di Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). L'agente di integrità per la sincronizzazione verrà installato come parte dell'installazione di Azure AD Connect (versione 1.0.9125.0 o versione successiva).
* Introduzione ad Azure AD Connect Health per Active Directory Domain Services
    * [Scaricare l'agente di Azure AD Connect Health per Active Directory Domain Services](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Vedere le istruzioni per l'installazione](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).


## <a name="azure-ad-connect-health-portal"></a>Portale di Azure AD Connect Health
Il portale di Azure AD Connect Health visualizza gli avvisi, il monitoraggio delle prestazioni e l'analisi di utilizzo. L'URL https://aka.ms/aadconnecthealth reindirizza al pannello principale di Azure AD Connect Health. Un pannello è paragonabile a una finestra. Nel pannello principale sono disponibili opzioni per l'**Avvio rapido**, per i servizi di Azure AD Connect Health e altre opzioni di configurazione. Vedere lo screenshot seguente e le brevi spiegazioni a seguire. Dopo la distribuzione degli agenti, il servizio integrità identifica automaticamente i servizi monitorati da Azure AD Connect Health.

> [!NOTE]
> Per informazioni sulle licenze, vedere [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md) o la [pagina dei prezzi di Azure AD](https://aka.ms/aadpricing).
    
![portale di Azure AD Connect Health](./media/whatis-hybrid-identity-health/portalsidebar.png)

* **Avvio rapido**: quando si seleziona questa opzione, si apre il pannello **Avvio rapido**. È possibile scaricare l'agente di Azure AD Connect Health selezionando **Scaricare gli strumenti**. È anche possibile accedere alla documentazione e fornire commenti e suggerimenti.
* **Azure Active Directory Connect (Sincronizzazione)** : questa opzione visualizza i server di Azure AD Connect attualmente monitorati da Azure AD Connect Health. La voce **Errori di sincronizzazione** mostra errori di sincronizzazione di base del primo servizio di sincronizzazione caricato per categorie. Quando si seleziona la voce **Servizi di sincronizzazione**, il pannello visualizzato mostra informazioni sui server Azure AD Connect. Per altre informazioni sulle funzionalità, vedere [Uso di Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md).
* **Active Directory Federation Services**: questa opzione visualizza tutti i servizi AD FS attualmente monitorati da Azure AD Connect Health. Quando si seleziona un'istanza, il pannello che si apre visualizza le informazioni su tale istanza del servizio. Queste informazioni includono una panoramica, le proprietà, gli avvisi, informazioni di monitoraggio e analisi di utilizzo. Per altre informazioni sulle funzionalità, vedere [Uso di Azure AD Connect Health con AD FS](how-to-connect-health-adfs.md).
* **Active Directory Domain Services**: questa opzione visualizza tutte le foreste di Active Directory Domain Services attualmente monitorate da Azure AD Connect Health. Quando si seleziona una foresta, il pannello che si apre visualizza le informazioni su tale foresta. Queste informazioni includono una panoramica dei dati essenziali, il dashboard Controller di dominio, il dashboard Stato replica, avvisi e monitoraggio. Per altre informazioni sulle funzionalità, vedere [Uso di Azure AD Connect Health con Active Directory Domain Services](how-to-connect-health-adds.md).
* **Configurare**: questa sezione include le opzioni per attivare o disattivare quanto segue:
  - L’**aggiornamento automatico** dell'agente di Azure AD Connect Health alla versione più recente: l'agente di Azure AD Connect Health viene aggiornato automaticamente ogni volta che sono disponibili nuove versioni. Questa opzione è abilitata per impostazione predefinita. 
  - **Accesso ai dati** di integrità della directory di Azure AD da parte di Microsoft solo ai fini della risoluzione dei problemi: se questa opzione è abilitata, Microsoft può accedere agli stessi dati visualizzati dall'utente. Queste informazioni possono essere utili per la risoluzione dei problemi e per fornire l'assistenza necessaria. Questa opzione è disabilitata per impostazione predefinita


* **Controllo degli accessi in base al ruolo (IAM)** è la sezione per gestire l'accesso ai dati di Connect Health data in base al ruolo. 

## <a name="next-steps"></a>Fasi successive

- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Impostazioni personalizzate](how-to-connect-install-custom.md)
- [Sincronizzazione degli hash delle password](how-to-connect-password-hash-synchronization.md)|
- [Autenticazione pass-through](how-to-connect-pta.md)
- [Azure AD Connect e federazione](how-to-connect-fed-whatis.md)
- [Installare gli agenti di Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Servizio di sincronizzazione Azure AD Connect](how-to-connect-sync-whatis.md)
