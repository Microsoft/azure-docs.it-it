---
title: Creare una strategia di gestione del controllo di accesso resiliente-Azure AD
description: Questo documento fornisce materiale sussidiario sulle strategie che un'organizzazione deve adottare per garantire la resilienza e ridurre il rischio di blocco durante interruzioni impreviste
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15d2b029937c58d45a2c1148c568cd396cea336a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84634643"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Creare una strategia di gestione di controllo di accesso resiliente con Azure Active Directory

>[!NOTE]
> Le informazioni contenute in questo documento rappresentano la posizione attuale di Microsoft Corporation sulle problematiche trattate alla data di pubblicazione. Poiché Microsoft deve rispondere alle mutevoli condizioni del mercato, questo non deve essere interpretato come un impegno da parte di Microsoft, che non garantisce l'accuratezza delle informazioni presentate dopo la data di pubblicazione.

Le organizzazioni che proteggono i loro sistemi IT basandosi su un controllo di accesso singolo, ad esempio l'autenticazione a più fattori (MFA) o un unico percorso di rete, sono soggette a errori di accesso alle loro app e risorse se tale controllo di accesso singolo non è disponibile o è non è configurato correttamente. Una calamità naturale, ad esempio, può causare l'indisponibilità di segmenti di grandi dimensioni di un'infrastruttura di telecomunicazioni o reti aziendali. Un'interruzione di questo tipo potrebbe togliere la possibilità di accedere agli utenti finali e agli amministratori.

Questo documento fornisce materiale sussidiario sulle strategie che un'organizzazione deve adottare per garantire la resilienza e ridurre il rischio di blocco durante interruzioni impreviste con i seguenti scenari:

 1. Le organizzazioni possono aumentare la resilienza per ridurre il rischio di blocco **prima di un'interruzione** implementando le strategie di mitigazione o piani di emergenza.
 2. Grazie alle strategie di mitigazione e ai piani di emergenza, le organizzazioni possono continuare ad accedere alle app e risorse scelte **durante un'interruzione**.
 3. Le organizzazioni devono assicurarsi di conservare le informazioni, come ad esempio i log, **dopo un'interruzione** e prima di eseguire il rollback di eventuali contingenze implementate.
 4. Le organizzazioni che non hanno implementato strategie di prevenzione o piani alternativi potrebbero implementare **opzioni di emergenza** per affrontare l'interruzione.

## <a name="key-guidance"></a>Materiale sussidiario chiave

Da questo documento si traggono quattro punti chiave:

* Evitare il blocco dell'amministratore usando account di accesso di emergenza.
* Implementare l'autenticazione a più fattori usando l'accesso condizionale (CA) anziché l'autenticazione a più fattori per utente.
* Attenuare il blocco degli utenti usando più controlli di accesso condizionale (CA).
* Ridurre i blocchi dell'utente effettuando il provisioning di più metodi o equivalenti di autenticazione per ciascun utente.

## <a name="before-a-disruption"></a>Prima di un'interruzione

La riduzione di un'interruzione reale deve essere l'obiettivo principale di un'organizzazione nella gestione dei problemi di controllo di accesso che potrebbero verificarsi. La riduzione del rischio include la pianificazione di un evento reale e l'implementazione di strategie per assicurarsi che i controlli e le operazioni di accesso non siano coinvolti durante le interruzioni.

### <a name="why-do-you-need-resilient-access-control"></a>Perché è necessario il controllo di accesso resiliente?

 L'identità è il piano di controllo degli utenti che accedono alle app e risorse. Il sistema di identità consente di controllare quali utenti ottengono accesso alle applicazioni e in quali condizioni, ad esempio tramite controlli di accesso o requisiti di autenticazione. Quando uno o più requisiti di autenticazione o controllo di accesso non sono disponibili per l'autenticazione degli utenti a causa di circostanze impreviste, le organizzazioni possono ritrovarsi in una o entrambe le situazioni seguenti:

* **Blocco amministratore:** Gli amministratori non possono gestire il tenant o i servizi.
* **Blocco utente:** Gli utenti non possono accedere alle app o alle risorse.

### <a name="administrator-lockout-contingency"></a>Piano di emergenza per il blocco dell'amministratore

Per sbloccare l'accesso dell'amministratore al tenant, è consigliabile creare account di accesso di emergenza. Questi account, noti anche come account *break glass*, consentono l'accesso per gestire la configurazione di Azure AD quando non sono disponibili le normali procedure di accesso con privilegi. Devono essere creati almeno due account di accesso di emergenza seguendo le [indicazioni relative agli account di accesso di emergenza]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

### <a name="mitigating-user-lockout"></a>Riduzione del blocco dell'utente

 Per ridurre il rischio di blocco degli utenti, usare i criteri di accesso condizionale con più controlli per offrire agli utenti la possibilità di scegliere la modalità di accesso alle app e alle risorse. Se si permette a un utente di scegliere tra, ad esempio, accedere con autenticazione a più fattori **o** da un dispositivo gestito **o** dalla rete aziendale, se uno dei controlli di accesso non è disponibile l'utente ha altre opzioni per continuare a lavorare.

#### <a name="microsoft-recommendations"></a>Elementi consigliati di Microsoft

Incorporare i controlli di accesso seguenti nei criteri di accesso condizionale esistenti per l'organizzazione:

1. Effettuare il provisioning di più metodi di autenticazione per ogni utente che si basano su canali di comunicazione diversi, ad esempio l'app Microsoft Authenticator (basata su internet), il token OATH (generato sul dispositivo) e l'autenticazione via SMS (telefonica). Il seguente script di PowerShell consentirà di identificare in anticipo i metodi aggiuntivi che gli utenti devono registrare: [script per l'analisi del metodo di autenticazione a più fattori di Azure](https://docs.microsoft.com/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).
2. Distribuire Windows Hello for Business nei dispositivi Windows 10 per soddisfare i requisiti di autenticazione a più fattori direttamente dall'accesso del dispositivo.
3. Usare dispositivi attendibili tramite [Aggiunta ad Azure AD ibrido](https://docs.microsoft.com/azure/active-directory/devices/overview) o [ dispositivi gestiti di Microsoft Intune](https://docs.microsoft.com/intune/planning-guide). Un dispositivo attendibile migliorerà l'esperienza dell'utente, in quanto il dispositivo stesso è in grado di soddisfare i requisiti avanzati di autenticazione dei criteri senza una richiesta di autenticazione a più fattori per l'utente. L'autenticazione a più fattori sarà poi necessaria durante la registrazione di un nuovo dispositivo e durante l'accesso alle app o risorse da dispositivi non attendibili.
4. Usare criteri di protezione dell'identità di Azure AD basati sul rischio, i quali impediscono l'accesso quando l'utente o l'accesso è a rischio, piuttosto che criteri di autenticazione a più fattori fissi.
5. Se si sta proteggendo l'accesso VPN usando l'estensione server dei criteri di rete di Azure, è consigliabile eseguire la Federazione della soluzione VPN come [app SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications) e determinare la categoria dell'app come indicato di seguito. 

>[!NOTE]
> I criteri basati sul rischio richiedono licenze di [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).

L'esempio seguente descrive i criteri da creare per fornire un controllo di accesso resiliente all'utente che vuole accedere alle app e risorse. In questo esempio, saranno necessari un gruppo di sicurezza **AppUsers** con gli utenti di destinazione ai quali si vuole consentire l'accesso, uno denominato **CoreAdmins** con gli amministratori di core e uno denominato ** EmergencyAccess** con gli account di accesso di emergenza.
Questo set di criteri di esempio concederà, agli utenti selezionati in **AppUsers**, l'accesso alle app selezionate se sono connessi da un dispositivo attendibile OPPURE se forniscono un'autenticazione avanzata, come l'autenticazione a più fattori. Il criterio esclude gli account di emergenza e gli amministratori di core.

**Set di criteri di mitigazione dell'accesso condizionale:**

* Criterio 1: bloccare l'accesso agli utenti esterni ai gruppi di destinazione
  * Utenti e gruppi: Includi tutti gli utenti. Escludi AppUsers CoreAdmins ed EmergencyAccess
  * App Cloud: Includi tutte le app
  * Condizioni: (nessuna)
  * Grant Control: blocco
* Criterio 2: concedere l'accesso a AppUsers che richiedono l'autenticazione a più fattori o un dispositivo attendibile.
  * Utenti e gruppi: includere AppUsers. Escludere CoreAdmins ed EmergencyAccess
  * App Cloud: Includi tutte le app
  * Condizioni: (nessuna)
  * Concessione del controllo: concedere l'accesso, richiedere l'autenticazione a più fattori, richiedere la conformità del dispositivo. Per più controlli: richiedere uno dei controlli selezionati.

### <a name="contingencies-for-user-lockout"></a>Contingenze per blocco dell'utente

In alternativa, l'organizzazione può anche creare dei criteri di emergenza. Per creare i criteri di emergenza, è necessario definire i criteri di compromesso tra continuità aziendale, costo operativo, costo finanziario e rischi relativi alla sicurezza. Ad esempio, è possibile attivare un criterio di emergenza solo in un subset di utenti, per un subset di app, per un subset di client o da un subset di percorsi. I criteri di emergenza forniranno l'accesso ad app e risorse agli amministratori e agli utenti finali durante un'interruzione se non è stato implementato alcun metodo di mitigazione dei rischi. Microsoft consiglia di abilitare i criteri di contingenza in [modalità solo rapporto](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-report-only) quando non è in uso, in modo che gli amministratori possano monitorare il potenziale impatto dei criteri qualora debbano essere attivati.

 Comprendere l'esposizione durante un'interruzione aiuta a ridurre i rischi ed è una parte essenziale del processo di pianificazione. Per creare il piano di emergenza, determinare innanzitutto i seguenti requisiti aziendali dell'organizzazione:

1. Determinare in anticipo le app mission-critical: quali sono le app a cui è necessario concedere l'accesso, anche con un livello di rischio/sicurezza inferiore? Compilare un elenco di queste app e assicurarsi che gli altri stakeholder (business, sicurezza, legali, leadership) concordino sul fatto che se tutto il controllo di accesso è indisponibile, l'esecuzione di queste app deve comunque continuare. È probabile che si vadano a formare le seguenti categorie:
   * **Categoria 1: app di importanza strategica fondamentale** che non possono essere indisponibili per più di pochi minuti, ad esempio le app che hanno un effetto diretto sui ricavi dell'organizzazione.
   * **Categoria 2: app importanti** che devono tornare accessibili per l'azienda entro poche ore.
   * **Categoria 3: app di priorità bassa** che possono sopportare un'interruzione di alcuni giorni.
2. Per le app nella categoria 1 e 2, Microsoft consiglia di pianificare in anticipo quale tipo di livello di accesso si desidera consentire:
   * Si desidera consentire l'accesso completo o una sessione con restrizioni, come la limitazione dei download?
   * Si desidera consentire l'accesso a parte dell'app, ma non all'intera app?
   * Si desidera consentire l'accesso all'information worker e bloccare l'accesso dell'amministratore fino al ripristino del controllo di accesso?
3. Per tali app, è consigliato inoltre pianificare quali vie di accesso saranno aperte deliberatamente e quali invece saranno chiuse:
   * Si desidera consentire esclusivamente l'accesso dal browser e bloccare i rich client in grado di salvare i dati offline?
   * Si desidera consentire l'accesso solo agli utenti all'interno della rete aziendale e mantenere il blocco degli utenti esterni?
   * Si desidera consentire l'accesso esclusivamente da determinati paesi o aree geografiche durante l'interruzione?
   * Si desidera che i criteri di emergenza, in particolare nel casi di app di importanza strategica fondamentale, abbiano esito positivo o negativo se un controllo di accesso alternativo non è disponibile?

#### <a name="microsoft-recommendations"></a>Elementi consigliati di Microsoft

Un criterio di accesso condizionale di emergenza è un **criterio di backup** che omette l'autenticazione a più fattori di Azure, l'autenticazione a più fattori di terze parti, i controlli basati sul rischio o sui dispositivi. Per ridurre al minimo l'arresto imprevisto quando è abilitato un criterio di emergenza, il criterio deve rimanere in modalità solo report quando non è in uso. Gli amministratori possono monitorare il potenziale impatto dei criteri di contingenza usando la cartella di lavoro delle informazioni dettagliate sull'accesso condizionale. Quando l'organizzazione decide di attivare il piano di emergenza, gli amministratori possono abilitare i criteri e disabilitare i normali criteri basati sul controllo.

>[!IMPORTANT]
> La disabilitazione dei criteri che applicano la sicurezza sugli utenti, anche solo temporaneamente, ridurrà il livello di sicurezza mentre il piano di emergenza è in funzione.

* Configurare un set di criteri di fallback se un'interruzione in un tipo di credenziali o in un meccanismo di controllo di accesso ha effetti sull'accesso alle app. Configurare un criterio nello stato solo report che richiede l'aggiunta a un dominio come controllo, come backup per un criterio attivo che richiede un provider di autenticazione a più fattori di terze parti.
* Ridurre il rischio di password indovinate da malintenzionati, quando l'autenticazione a più fattori non è necessaria, seguendo le procedure consigliate nel white paper relativo alle [indicazioni sulle password](https://aka.ms/passwordguidance).
* Distribuire [Reimpostazione self-service delle password di Azure AD (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) e [Protezione della password di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) per assicurarsi che gli utenti non usino password comuni e termini esclusi.
* Usare criteri che limitano l'accesso all'interno delle app se non si raggiunge un determinato livello di autenticazione, anziché eseguire semplicemente il fallback all'accesso completo. Ad esempio:
  * Configurare un criterio di backup che invia l'attestazione di sessione con restrizioni a Exchange e SharePoint.
  * Se l'organizzazione usa Microsoft Cloud App Security, è consigliabile eseguire il fallback a un criterio che coinvolga MCAS, quindi che MCAS consenta l'accesso di sola lettura, ma non il caricamento.
* Assegnare un nome ai criteri per esseri sicuri di trovarli facilmente durante un'interruzione. Includere gli elementi seguenti nel nome dei criteri:
  * Un *numero di etichetta* per i criteri.
  * Il testo da visualizzare. Questo criterio è destinato solo ai casi di emergenza. Ad esempio: **Abilita in emergenza**
  * L'*interruzione* a cui si applica il criterio. Ad esempio, **durante l'interferenza** dell'autenticazione a più fattori
  * Un *numero di sequenza* per mostrare l'ordine in cui è necessario attivare i criteri.
  * Le *app* a cui si applica il criterio.
  * I *controlli* a cui si applicherà il criterio.
  * Le *condizioni* richieste.
  
Lo standard di denominazione per i criteri di emergenza avrà il formato seguente: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Nell'esempio seguente, **un esempio di criterio CA di emergenza per ripristinare l'accesso alle app di collaborazione cruciali**, è una tipica contingenza aziendale. In questo scenario, l'organizzazione in genere richiede l'autenticazione a più fattori per tutti gli accessi a Exchange Online e SharePoint Online. In questo caso, l'interruzione coinvolge il servizio del provider di autenticazione a più fattori per il cliente (Azure, provider locale o terze parti). Questo criterio riduce l'interruzione permettendo a utenti di destinazione specifici di accedere a tali app da dispositivi Windows attendibili esclusivamente quando l'accesso all'app si verifica tramite la rete aziendale attendibile. Anche gli account di emergenza e degli amministratori di core saranno esclusi da queste restrizioni. Gli utenti di destinazione otterranno quindi l'accesso a Exchange Online e SharePoint Online, mentre gli altri utenti continueranno a non poter accedere alle app a causa dell'interruzione del servizio. In questo esempio, saranno necessari un percorso di rete denominato **CorpNetwork**, un gruppo di sicurezza **ContingencyAccess** con gli utenti di destinazione, uno denominato **CoreAdmins** con gli amministratori di core e uno denominato **EmergencyAccess** con gli account di accesso di emergenza. L'emergenza richiede quattro criteri per garantire l'accesso desiderato. 

**Esempio A: criteri di accesso condizionale di emergenza per il ripristino dell'accesso alle app di collaborazione di importanza strategica fondamentale:**

* Criteri 1: Richiedi dispositivi aggiunti a un dominio per Exchange e SharePoint
  * Nome: EM001-ENABLE IN EMERGENCY: interferenza dell'autenticazione a più fattori [1/4]-Exchange SharePoint-Richiedi Aggiunta ad Azure AD ibrido
  * Utenti e gruppi: includere ContingencyAccess. Escludere CoreAdmins ed EmergencyAccess
  * App Cloud: Exchange Online e SharePoint Online
  * Condizioni: any
  * Concessione del controllo: Richiedi aggiunta a un dominio
  * Stato: solo report
* Criterio 2: bloccare le piattaforme diverse da Windows
  * Nome: EM002-ENABLE IN EMERGENCY: interferenza dell'autenticazione a più fattori [2/4]-Exchange SharePoint-bloccare l'accesso ad eccezione di Windows
  * Utenti e gruppi: Includi tutti gli utenti. Escludere CoreAdmins ed EmergencyAccess
  * App Cloud: Exchange Online e SharePoint Online
  * Condizioni: la piattaforma del dispositivo include tutte le piattaforme, Escludi Windows
  * Grant Control: blocco
  * Stato: solo report
* Criterio 3: bloccare le reti diverse da CorpNetwork
  * Nome: EM003-ENABLE IN EMERGENCY: interferenza dell'autenticazione a più fattori [3/4]-Exchange SharePoint-bloccare l'accesso ad eccezione della rete aziendale
  * Utenti e gruppi: Includi tutti gli utenti. Escludere CoreAdmins ed EmergencyAccess
  * App Cloud: Exchange Online e SharePoint Online
  * Condizioni: i percorsi includono qualsiasi percorso, escluso CorpNetwork
  * Grant Control: blocco
  * Stato: solo report
* Criterio 4: bloccare EAS in modo esplicito
  * Nome: EM004-ENABLE IN EMERGENCY: interferenza dell'autenticazione a più fattori [4/4]-Exchange-blocca EAS per tutti gli utenti
  * Utenti e gruppi: Includi tutti gli utenti
  * App Cloud: Includi Exchange Online
  * Condizioni: app client: Exchange Active Sync
  * Grant Control: blocco
  * Stato: solo report

Ordine di attivazione:

1. Escludere ContingencyAccess, CoreAdmins ed EmergencyAccess dai criteri di autenticazione a più fattori esistenti. Verificare che un utente in ContingencyAccess possa accedere a SharePoint Online ed Exchange Online.
2. Abilitare i criteri 1: verificare che gli utenti di dispositivi aggiunti a un dominio che non sono inclusi nei gruppi di esclusione siano in grado di accedere a Exchange Online e SharePoint Online. Verificare che gli utenti nel gruppo Exclude possano accedere a SharePoint Online ed Exchange da qualsiasi dispositivo.
3. Abilitare i criteri 2: verificare che gli utenti non inclusi nel gruppo di esclusione non possano arrivare a SharePoint Online ed Exchange Online dai dispositivi mobili. Verificare che gli utenti nel gruppo Exclude possano accedere a SharePoint ed Exchange da qualsiasi dispositivo (Windows/iOS/Android).
4. Abilita criterio 3: verificare che gli utenti non inclusi nei gruppi di esclusione non possano accedere a SharePoint e scambiare la rete aziendale anche con un computer aggiunto al dominio. Verificare che gli utenti nel gruppo di esclusione possano accedere a SharePoint ed Exchange da qualsiasi rete.
5. Abilita criterio 4: verificare che tutti gli utenti non possano ottenere Exchange Online dalle applicazioni di posta elettronica native sui dispositivi mobili.
6. Disabilitare i criteri di autenticazione a più fattori esistenti per SharePoint Online ed Exchange Online.

In questo esempio successivo, **Esempio B: criteri di accesso condizionale di emergenza per consentire l'accesso a Salesforce da dispositivi mobili**, viene ripristinata l'accesso a un app aziendale. In questo scenario, in genere, il cliente richiede che l'accesso a Salesforce (configurato per l'accesso singolo con Azure AD) da parte degli addetti alla vendita con dispositivi mobili sia consentito solo se tramite dispositivi conformi. L'interruzione, in questo caso, consiste in un problema nella valutazione della conformità dei dispositivi. Si è verificata un'interruzione a un orario delicato, dove il team vendite deve accedere a Salesforce per chiudere delle operazioni commerciali. Questi criteri di emergenza concederanno l'accesso a Salesforce da un dispositivo mobile agli utenti fondamentali, in modo da poter continuare a chiudere operazioni commerciali senza interrompere le attività. In questo esempio, **SalesforceContingency** contiene tutti gli addetti alle vendite che hanno bisogno di mantenere l'accesso, mentre **SalesAdmins** include gli amministratori di Salesforce necessari.

**Esempio B: criteri di accesso condizionale di emergenza:**

* Criterio 1: bloccare tutti gli utenti non presenti nel team SalesContingency
  * Nome: EM001-ENABLE IN EMERGENCY: interferenza della conformità del dispositivo [1/2]-Salesforce-blocca tutti gli utenti tranne SalesforceContingency
  * Utenti e gruppi: Includi tutti gli utenti. Escludere SalesAdmins e SalesforceContingency
  * App Cloud: Salesforce.
  * Condizioni: nessuna
  * Grant Control: blocco
  * Stato: solo report
* Criterio 2: bloccare il team di vendita da qualsiasi piattaforma diversa da mobile (per ridurre la superficie di attacco)
  * Nome: EM002-ENABLE IN EMERGENCY: interferenza della conformità del dispositivo [2/2]-Salesforce-bloccare tutte le piattaforme eccetto iOS e Android
  * Utenti e gruppi: includere SalesforceContingency. Escludere SalesAdmins
  * App Cloud: Salesforce
  * Condizioni: la piattaforma del dispositivo include tutte le piattaforme, esclusi iOS e Android
  * Grant Control: blocco
  * Stato: solo report

Ordine di attivazione:

1. Escludere SalesAdmins e SalesforceContingency dai criteri di conformità del dispositivo esistenti per Salesforce. Verificare che un utente nel gruppo SalesforceContingency possa accedere a Salesforce.
2. Abilitare i criteri 1: verificare che gli utenti esterni a SalesContingency non possano accedere a Salesforce. Verificare che gli utenti in SalesAdmins e SalesforceContingency possano accedere a Salesforce.
3. Abilitare i criteri 2: verificare che gli utenti nel gruppo SalesContingency non possano accedere a Salesforce dai computer portatili Windows/Mac, ma possono comunque accedere ai dispositivi mobili. Verificare che SalesAdmin possa comunque accedere a Salesforce da qualsiasi dispositivo.
4. Disabilitare i criteri di conformità del dispositivo esistenti per Salesforce.

### <a name="contingencies-for-user-lockout-from-on-prem-resources-nps-extension"></a>Contingenze per il blocco degli utenti da risorse locali (estensione NPS)

Se si sta proteggendo l'accesso VPN usando l'estensione server dei criteri di rete di Azure, è consigliabile eseguire la Federazione della soluzione VPN come [app SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications) e determinare la categoria dell'app come indicato di seguito. 

Se è stata distribuita Azure AD estensione di server dei criteri di rete multi-factor authentication per proteggere le risorse locali, ad esempio VPN e gateway di Desktop remoto, con l'autenticazione a più fattori, è consigliabile prendere in considerazione se si è pronti per disabilitare l'autenticazione a più fattori in caso di emergenza.

In questo caso, è possibile disabilitare l'estensione NPS, di conseguenza il server NPS verificherà solo l'autenticazione primaria e non imporrà l'autenticazione a più fattori sugli utenti.

Disabilitare l'estensione NPS: 
-   Esportare la chiave del registro di sistema \SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters HKEY_LOCAL_MACHINE come backup. 
-   Eliminare i valori del registro di sistema per "AuthorizationDLLs" e "ExtensionDLLs", non la chiave Parameters. 
-   Riavviare il servizio IAS (Network Policy Service) per rendere effettive le modifiche 
-   Determinare se l'autenticazione principale per la VPN è riuscita.

Dopo che il servizio è stato recuperato e si è pronti per applicare l'autenticazione a più fattori sugli utenti, abilitare l'estensione NPS: 
-   Importante la chiave del registro di sistema da backup HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters 
-   Riavviare il servizio IAS (Network Policy Service) per rendere effettive le modifiche 
-   Determinare se l'autenticazione principale e l'autenticazione secondaria per la VPN hanno esito positivo.
-   Esaminare il server dei criteri di rete e il registro VPN per determinare gli utenti che hanno eseguito l'accesso durante la finestra di emergenza.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Distribuire la sincronizzazione dell'hash delle password, anche in caso di federazione o uso dell'autenticazione pass-through

Il blocco dell'utente può inoltre verificarsi se sussistono le condizioni seguenti:

- L'organizzazione usa una soluzione a identità ibrida con autenticazione pass-through o federazione.
- I sistemi di identità in locale (ad esempio Active Directory, AD FS o un componente dipendente) non sono disponibili. 
 
Per essere più resiliente, l'organizzazione dovrebbe [abilitare la sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), in quanto consente di [passare all'uso della sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) se i sistemi di identità in locale sono inattivi.

#### <a name="microsoft-recommendations"></a>Elementi consigliati di Microsoft
 Abilitare la sincronizzazione dell'hash delle password usando la procedura guidata di Azure AD Connect, indipendentemente dal fatto che l'organizzazione usi l'autenticazione pass-through o la federazione.

>[!IMPORTANT]
> Per usare la sincronizzazione dell'hash delle password, non è necessario convertire gli utenti dalla federazione all'autenticazione gestita.

## <a name="during-a-disruption"></a>Durante un'interruzione

Se si è scelto di implementare il piano di mitigazione, sarà possibile sopravvivere automaticamente a un'interruzione del controllo di accesso singolo. Tuttavia, se si è scelto di creare un piano di emergenza, sarà possibile attivare i criteri di emergenza durante l'interruzione del controllo di accesso:

1. Abilitare i criteri di emergenza che concedono l'accesso ad app specifiche, da reti specifiche, agli utenti di destinazione.
2. Disabilitare i normali criteri basati sui controlli.

### <a name="microsoft-recommendations"></a>Elementi consigliati di Microsoft

A seconda delle mitigazioni o emergenze usate durante un'interruzione, l'organizzazione potrebbe garantire l'accesso mediante le sole password. Non adottare nessuna misura di protezione è un rischio per la sicurezza notevole da valutare con attenzione. Le organizzazioni devono:

1. Come parte della strategia di controllo modifiche, documentare tutte le modifiche e lo stato precedente per poter eseguire il rollback di qualsiasi emergenza implementata non appena i controlli di accesso sono completamente operativi.
2. Presupporre che i malintenzionati tenteranno di rubare password tramite attacchi di tipo password spray o phishing quando si disabilita l'autenticazione a più fattori. Inoltre, i malintenzionati potrebbero avere già password che, se in precedenza non consentivano l'accesso a nessuna risorsa, possono essere tentate durante questo intervallo. Per gli utenti fondamentali, come i dirigenti, è possibile ridurre parzialmente questo rischio reimpostando le loro password prima di disabilitare l'autenticazione a più fattori.
3. Archiviare tutte le attività di accesso per identificare chi accede a cosa durante la fase in cui l'autenticazione a più fattori è disabilitata.
4. [Valutazione di tutti i rilevamenti di rischio segnalati](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) durante questa finestra.

## <a name="after-a-disruption"></a>Dopo un'interruzione

Annullare le modifiche apportate nell'ambito del piano di emergenza attivato una volta ripristinato il servizio che ha causato l'interruzione. 

1. Abilitare i criteri normali
2. Disabilitare i criteri di emergenza in modalità di sola segnalazione. 
3. Eseguire il rollback delle modifiche apportate e documentate durante l'interruzione.
4. Se si usa un account di accesso di emergenza, ricordarsi di rigenerare le credenziali e proteggere fisicamente i dettagli delle nuove credenziali come parte delle procedure di account di accesso di emergenza.
5. Continua a valutare [tutti i rilevamenti dei rischi segnalati](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) dopo l'interferenza per attività sospette.
6. Revocare tutti i token di aggiornamento rilasciati [usando PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) e destinati a un set di utenti. La revoca di tutti i token di aggiornamento è importante per gli account con privilegi usati durante l'interruzione. Questa operazione costringerà gli utenti a ripetere l'autenticazione e rispettare il controllo dei criteri ripristinati.

## <a name="emergency-options"></a>Opzioni di emergenza

 In caso di emergenza e l'organizzazione non ha implementato in precedenza un piano di mitigazione o di emergenza, seguire le indicazioni riportate nella sezione [contingenze per il blocco degli utenti](#contingencies-for-user-lockout) se usano già i criteri di accesso condizionale per applicare l'autenticazione a più fattori.
Se l'organizzazione usa criteri di autenticazione a più fattori obsoleti per l'utente, è possibile prendere in considerazione l'alternativa seguente:

1. Se si dispone dell'indirizzo IP in uscita della rete aziendale, è possibile aggiungerli come indirizzi IP attendibili per abilitare l'autenticazione esclusivamente sulla rete aziendale.
   1. Se non si ha l'inventario degli indirizzi IP in uscita, o se è necessario abilitare l'accesso all'interno e all'esterno della rete aziendale, è possibile aggiungere l'intero spazio indirizzi IPv4 come indirizzi IP attendibili specificando 0.0.0.0/1 e 128.0.0.0/1.

>[!IMPORTANT]
 > Se si ampliano gli indirizzi IP attendibili per sbloccare l'accesso, i rilevamenti dei rischi associati a indirizzi IP (ad esempio, spostamento impossibile o posizioni non note) non verranno generati.

>[!NOTE]
 > La configurazione di [indirizzi IP attendibili](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) per l'autenticazione a più fattori di Azure è disponibile solo con [licenze Azure AD Premium](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Altre informazioni

* [Documentazione di Autenticazione di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Gestire gli account amministrativi di accesso di emergenza in Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Configurare località denominate in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Windows Hello for Business Deployment Guide](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) (Guida alla distribuzione di Windows Hello fo Business)
  * [Materiale sussidiario sulle password - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Quali sono le condizioni in Azure Active Directory l'accesso condizionale?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Che cosa sono i controlli di accesso nell'accesso condizionale Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
* [Che cos'è la modalità solo report di accesso condizionale?](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only)
