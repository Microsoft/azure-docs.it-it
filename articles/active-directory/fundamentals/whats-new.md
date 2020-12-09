---
title: Novità Note sulla versione - Azure Active Directory | Microsoft Docs
description: Informazioni sulle novità con Azure Active Directory; quali le note sulla versione più recenti, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 710e31c18338243f9405a071b8fa544fe44044be
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903157"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Per ricevere notifiche che indicano che occorre visitare di nuovo questa pagina per visualizzare gli aggiornamenti, copiare e incollare questo URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` nel lettore di feed ![icona del lettore di feed RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si stanno cercando elementi più vecchi di sei mesi, è possibile trovarli in [Archivio per le novità di Azure Active Directory](whats-new-archive.md).

---
## <a name="november-2020"></a>Novembre 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1,0, TLS 1,1 e 3DES

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Tutte le applicazioni Azure AD  
**Funzionalità del prodotto:** Standard

Azure Active Directory depreca i protocolli seguenti in Azure Active Directory aree mondiali entro il 30 giugno 2021:

- TLS 1.0
- TLS 1.1
- pacchetto di crittografia 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Gli ambienti interessati sono:
- Cloud commerciale di Azure
- Office 365 GCC e WW

Annuncio correlato tutte le combinazioni client-server e browser-server devono usare TLS 1,2 e i pacchetti di crittografia moderni per mantenere una connessione sicura a Azure Active Directory per Azure, Office 365 e Microsoft 365 Services. Questa modifica è correlata a [Azure Active Directory TLS 1,0 & 1,1 e alla deprecazione del pacchetto di crittografia 3DES in US gov cloud](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 novembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel novembre 2020 abbiamo aggiunto le seguenti 52 nuove applicazioni nella raccolta di app con supporto federativo:

[Travel & Expense Management](https://app.expenseonce.com/Account/Login), [tribeloo](../saas-apps/tribeloo-tutorial.md), [selettore file Itslearning](https://pmteam.itslearning.com/), [controllo Crisis](../saas-apps/crises-control-tutorial.md), [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum-Study Island](https://app.studyisland.com/cfw/login/), [Virtual Risk Manager](../saas-apps/virtual-risk-manager-tutorial.md), [timu](../saas-apps/timu-tutorial.md), [guarder Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview-Recruit](https://recruit.talview.com/login), Real time translator, [Klaxoon](https://access.klaxoon.com/login), [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [ExpenseManager](https://api.expense-manager.com/), [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [it-Trak tenant Experience Platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/), [Eva check-in](https://my.evacheckin.com/organization), [HowNow webapp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [coupa Risk valutazione](../saas-apps/coupa-risk-assess-tutorial.md), [Lucid (tutti i prodotti)](../saas-apps/lucid-tutorial.md), [GoBright](https://portal.brightbooking.eu/), SailPoint [IDENTITYNOW](../saas-apps/sailpoint-identitynow-tutorial.md),[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md) [, ProdPad,](../saas-apps/prodpad-tutorial.md) [AWS Scarica](../saas-apps/aws-clientvpn-tutorial.md), [appsec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [Freight Measure](https://www.gpcsl.com/freight.html), [bonifica cloud](../saas-apps/terraform-cloud-tutorial.md), [Nature Research](../saas-apps/nature-research-tutorial.md), [Play Digital Signing](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md) [, Prolorus, Hirebridge](../saas-apps/prolorus-tutorial.md) [ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Sunrise software Relations CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [Mentor® by eDriving: business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Anteprima pubblica: ruoli personalizzati per le app aziendali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso
 
 [I ruoli RBAC personalizzati per la gestione delle applicazioni aziendali delegate](../users-groups-roles/roles-custom-available-permissions.md) sono ora in anteprima pubblica. Queste nuove autorizzazioni si basano sui ruoli personalizzati per la gestione della registrazione delle app, che consente un controllo con granularità fine sugli accessi degli amministratori. Nel corso del tempo, verranno rilasciate autorizzazioni aggiuntive per delegare la gestione dei Azure AD.

Alcuni scenari di delega comuni:
- assegnazione di utenti e gruppi che possono accedere alle applicazioni Single Sign-On basate su SAML
- creazione di applicazioni della raccolta Azure AD
- aggiornamento e lettura delle configurazioni SAML di base per applicazioni Single Sign-On basate su SAML
- gestione dei certificati di firma per applicazioni Single Sign-On basate su SAML
- aggiornamento degli indirizzi di posta elettronica di notifica dei certificati per l'accesso in scadenza per le applicazioni Single Sign-On basate su SAML
- aggiornamento della firma del token SAML e dell'algoritmo di accesso per le applicazioni Single Sign-On basate su SAML
- creare, eliminare e aggiornare attributi utente e attestazioni per applicazioni Single Sign-On basate su SAML
- possibilità di attivare, disattivare e riavviare i processi di provisioning
- aggiornamenti al mapping degli attributi
- possibilità di leggere le impostazioni di provisioning associate all'oggetto
- possibilità di leggere le impostazioni di provisioning associate all'entità servizio
- possibilità di autorizzare l'accesso alle applicazioni per il provisioning

---

### <a name="azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Azure AD proxy di applicazione supporta in modo nativo Single Sign-On l'accesso alle applicazioni che usano intestazioni per l'autenticazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso
 
Il proxy dell'applicazione Azure Active Directory (Azure AD) supporta in modo nativo Single Sign-On l'accesso alle applicazioni che usano intestazioni per l'autenticazione. È possibile configurare i valori di intestazione richiesti dall'applicazione in Azure AD. I valori dell'intestazione verranno inviati all'applicazione tramite il proxy di applicazione. Per altre informazioni, vedere [Single Sign-on basato su intestazione per le app locali con app Azure ad proxy](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Disponibilità generale-iscrizione e accesso a Azure AD B2C Phone con criteri personalizzati

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

Con l'iscrizione e l'accesso ai numeri di telefono, gli sviluppatori e le aziende possono consentire ai clienti di iscriversi e accedere usando una password monouso inviata al numero di telefono dell'utente tramite SMS. Questa funzionalità consente inoltre al cliente di modificare il numero di telefono se perde l'accesso al telefono. Grazie alla potenza dei criteri personalizzati, gli sviluppatori e le aziende possono comunicare il proprio marchio tramite la personalizzazione delle pagine. Informazioni su come [configurare l'iscrizione e l'accesso tramite telefono con criteri personalizzati in Azure ad B2C](../../active-directory-b2c/phone-authentication.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 novembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blog in](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [TIC-TAC mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Per altre informazioni, vedere [automatizzare il provisioning degli utenti in applicazioni SaaS con Azure ad](../manage-apps/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Anteprima pubblica: Sign-In di posta elettronica con ProxyAddresses ora distribuibile tramite implementazione temporanea

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Gli amministratori tenant possono ora usare l'implementazione temporanea per distribuire Sign-In di posta elettronica con ProxyAddresses a gruppi di Azure AD specifici. Questo può essere utile quando si prova la funzionalità prima di distribuirla nell'intero tenant tramite i criteri di individuazione dell'area di autenticazione principale. Le istruzioni per la distribuzione di Sign-In di posta elettronica con ProxyAddresses tramite l'implementazione temporanea sono disponibili nella [documentazione](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Anteprima limitata-diagnostica di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Con la versione di anteprima iniziale della diagnostica di accesso, gli amministratori possono ora esaminare gli accessi degli utenti. Gli amministratori possono ricevere informazioni contestuali, specifiche e pertinenti e istruzioni su ciò che si è verificato durante l'accesso e su come risolvere i problemi. Il livello di diagnostica è disponibile sia nel pannello di Azure AD sia nella diagnostica e nell'accesso condizionale. Gli scenari di diagnostica descritti in questa versione sono l'accesso condizionale, la Multi-Factor Authentication e l'accesso riuscito.
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Miglioramento delle proprietà di accesso non note

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

  Rilevamento delle proprietà di accesso non noto aggiornato. I clienti possono riscontrare rilevamenti delle proprietà di accesso non note più ad alto rischio. Per ulteriori informazioni, vedere [che cos'è il rischio?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>È ora disponibile l'aggiornamento dell'anteprima pubblica dell'agente di provisioning cloud (versione: 1.1.281.0)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di Azure AD cloud  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
L'agente di provisioning cloud è stato rilasciato in anteprima pubblica ed è ora disponibile tramite il portale. Questa versione contiene diversi miglioramenti, tra cui il supporto per GMSA per i domini, che offre una migliore sicurezza, cicli di sincronizzazione iniziale migliorati e supporto per gruppi di grandi dimensioni. Per altri dettagli, vedere la [cronologia](../app-provisioning/provisioning-agent-release-version-history.md) delle versioni di rilascio. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>Endpoint API chiave di ripristino di BitLocker ora in/informationProtection

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione dell'accesso ai dispositivi  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi
 
In precedenza era possibile ripristinare le chiavi BitLocker tramite l'endpoint/BitLocker. Alla fine verrà deprecato questo endpoint e i clienti devono iniziare a consumare l'API che ora rientra in/informationProtection. 

Per riflettere queste modifiche, vedere [API di ripristino di BitLocker](https://docs.microsoft.com/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) per gli aggiornamenti della documentazione.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Disponibilità generale del supporto del proxy di applicazione per Servizi Desktop remoto client Web HTML5

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso
 
Il supporto del proxy di applicazione Azure AD per il client Web di Servizi Desktop remoto (RDS) è ora disponibile a livello generale. Il client Web RDS consente agli utenti di accedere a Desktop remoto infrastruttura tramite qualsiasi browser che supporta HTLM5, ad esempio Microsoft Edge, Internet Explorer 11, Google Chrome e così via. Gli utenti possono interagire con le app Remote o con i desktop, come avviene con un dispositivo locale da qualsiasi luogo. 

Utilizzando Azure AD proxy di applicazione, è possibile aumentare la sicurezza della distribuzione di Servizi Desktop remoto applicando i criteri di pre-autenticazione e di accesso condizionale per tutti i tipi di app rich client. Per altre informazioni, vedere [pubblicare desktop remoto con Azure ad proxy di applicazione](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Il nuovo servizio Dynamic Group migliorato è in versione di anteprima pubblica

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione
 
Il servizio Dynamic Group migliorato è ora disponibile in anteprima pubblica. I nuovi clienti che creano gruppi dinamici nei tenant utilizzeranno il nuovo servizio. Il tempo necessario per creare un gruppo dinamico sarà proporzionale alle dimensioni del gruppo che viene creato al posto delle dimensioni del tenant. Questo aggiornamento migliorerà significativamente le prestazioni per i tenant di grandi dimensioni quando i clienti creeranno gruppi più piccoli. 

Il nuovo servizio mira anche a completare l'aggiunta e la rimozione dei membri a causa di modifiche degli attributi entro pochi minuti. Inoltre, i singoli errori di elaborazione non bloccano l'elaborazione del tenant. Per ulteriori informazioni sulla creazione di gruppi dinamici, vedere la [documentazione](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>Ottobre 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure AD agenti ibridi locali interessati dalle modifiche ai certificati TLS di Azure

**Tipo:** Modifica pianificata  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Piattaforma

Microsoft sta aggiornando i servizi di Azure per l'uso di certificati TLS emessi da un set diverso di autorità di certificazione (CA) radice. Questo aggiornamento è dovuto ai certificati della CA correnti non conformi a uno dei requisiti di base del forum CA/browser. Questa modifica influirà Azure AD gli agenti ibridi installati in locale che hanno ambienti finalizzati con un elenco fisso di certificati radice e dovranno essere aggiornati per considerare attendibile le nuove autorità emittenti di certificati.

Questa modifica provocherà un'interferenza del servizio se non si esegue immediatamente un'azione. Questi agenti includono i [connettori del proxy di applicazione](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) per l'accesso remoto agli agenti di [autenticazione pass-through](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) locali che consentono agli utenti di accedere alle applicazioni usando le stesse password e gli agenti di anteprima del [provisioning del cloud](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) che eseguono ad Azure ad la sincronizzazione. 

Se si dispone di un ambiente con regole del firewall impostate per consentire le chiamate in uscita solo per il download di un elenco di revoche di certificati (CRL) specifico, sarà necessario consentire i seguenti URL CRL e OCSP. Per informazioni dettagliate sulla modifica e sugli URL CRL e OCSP per consentire l'accesso a, vedere  [modifiche ai certificati TLS di Azure](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Gli eventi di provisioning verranno rimossi dai log di controllo e pubblicati esclusivamente nei log di provisioning

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
L'attività da parte del [servizio di provisioning](../app-provisioning/user-provisioning.md) scim viene registrata sia nei log di controllo che nei log di provisioning. Sono incluse attività quali la creazione di un utente in ServiceNow, il gruppo in GSuite o l'importazione di un ruolo da AWS. In futuro, questi eventi verranno pubblicati solo nei log di provisioning. Questa modifica viene implementata per evitare eventi duplicati nei log e costi aggiuntivi sostenuti dai clienti che utilizzano i log in log Analytics. 

Al termine di una data verrà fornito un aggiornamento. Questa deprecazione non è pianificata per l'anno di calendario 2020. 

> [!NOTE]
> Questa operazione non influisca sugli eventi nei log di controllo al di fuori degli eventi di sincronizzazione generati dal servizio di provisioning. Gli eventi come la creazione di un'applicazione, i criteri di accesso condizionale, un utente nella directory e così via continueranno a essere generati nei log di controllo. [Altre informazioni](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure AD agenti ibridi locali interessati dalle modifiche ai certificati di Azure Transport Layer Security (TLS)

**Tipo:** Modifica pianificata  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Piattaforma
 
Microsoft sta aggiornando i servizi di Azure per l'uso di certificati TLS emessi da un set diverso di autorità di certificazione (CA) radice. Verrà aggiornato a causa dei certificati correnti della CA che non seguono uno dei requisiti di base del forum CA/browser. Questa modifica avrà un effetto Azure AD gli agenti ibridi installati in locale che hanno ambienti con protezione avanzata con un elenco fisso di certificati radice. Questi agenti dovranno essere aggiornati per considerare attendibili le nuove autorità di certificazione.

Questa modifica provocherà un'interferenza del servizio se non si esegue immediatamente un'azione. Questi agenti includono: 
- [Connettori del proxy di applicazione](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) per l'accesso remoto a locale 
- Agenti di [autenticazione pass-through](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) che consentono agli utenti di accedere alle applicazioni usando le stesse password
- Gli agenti di [anteprima del provisioning nel cloud](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) che eseguono ad Azure ad la sincronizzazione. 

Se si dispone di un ambiente con regole del firewall impostate per consentire le chiamate in uscita solo per il download di un elenco di revoche di certificati (CRL) specifico, è necessario consentire CRL e URL OCSP. Per informazioni dettagliate sulla modifica e sugli URL CRL e OCSP per consentire l'accesso a, vedere  [modifiche ai certificati TLS di Azure](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory la deprecazione di TLS 1,0, TLS 1,1 e 3DES in US Gov cloud

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Tutte le applicazioni Azure AD  
**Funzionalità del prodotto:** Standard
 
Azure Active Directory depreca i protocolli seguenti entro il 31 marzo 2021:
- TLS 1.0
- TLS 1.1
- pacchetto di crittografia 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Tutte le combinazioni client-server e browser-server devono usare TLS 1,2 e i pacchetti di crittografia moderni per mantenere una connessione sicura a Azure Active Directory per Azure, Office 365 e Microsoft 365 Services.

Gli ambienti interessati sono:
- US Gov di Azure
- [Office 365 GCC High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Assegnare le applicazioni ai ruoli in AU e nell'ambito dell'oggetto

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso
 
Questa funzionalità consente di assegnare un'applicazione (SPN) a un ruolo di amministratore nell'ambito dell'unità amministrativa. Per altre informazioni, vedere [assegnare ruoli con ambito a un'unità amministrativa](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>A questo punto è possibile disabilitare ed eliminare gli utenti Guest quando viene negato l'accesso a una risorsa

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance delle identità
 
Disable ed Delete è un controllo avanzato delle verifiche di accesso Azure AD per aiutare le organizzazioni a gestire al meglio gli utenti esterni in gruppi e app. Se Guest viene negato in una verifica di accesso, **Disable ed Delete** li bloccherà automaticamente per 30 giorni. Dopo 30 giorni, verranno rimossi completamente dal tenant.

Per altre informazioni su questa funzionalità, vedere [disabilitare ed eliminare identità esterne con le verifiche di accesso Azure ad (anteprima)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Gli autori della verifica di accesso possono aggiungere messaggi personalizzati nei messaggi di posta elettronica ai revisori

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance delle identità
 
In Azure AD verifiche di accesso, gli amministratori che creano le recensioni possono ora scrivere un messaggio personalizzato nei revisori. I revisori visualizzeranno il messaggio nel messaggio di posta elettronica ricevuto che richiede di completare la revisione. Per altre informazioni sull'uso di questa funzionalità, vedere il passaggio 14 della sezione [creare una o più](../governance/create-access-review.md#create-one-or-more-access-reviews) verifiche di accesso.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-ottobre 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Integration Assistant per Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
L'esperienza di Integration Assistant (anteprima) è ora disponibile per Azure AD B2C Registrazioni app. Questa esperienza consente di configurare l'applicazione per scenari comuni. Altre informazioni sulle procedure consigliate e sulle raccomandazioni per la [piattaforma di identità Microsoft](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Visualizzare l'ID del modello di ruolo nell'interfaccia utente portale di Azure

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure  
**Funzionalità del prodotto:** Controllo di accesso
 

È ora possibile visualizzare l'ID modello di ogni ruolo Azure AD nel portale di Azure. In Azure AD selezionare  **Descrizione** del ruolo selezionato. 

È consigliabile che i clienti usino gli ID dei modelli di ruolo nel codice e nello script di PowerShell, anziché il nome visualizzato. L'ID del modello di ruolo è supportato per l'uso degli oggetti [directoryRoles](/graph/api/resources/directoryrole) e [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) . Per ulteriori informazioni sugli ID dei modelli di ruolo, vedere [ID dei modelli di ruolo](../roles/permissions-reference.md#role-template-ids).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>I connettori API per Azure AD B2C i flussi utente di iscrizione sono ora in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 

I connettori API sono ora disponibili per l'uso con Azure Active Directory B2C. I connettori API consentono di usare le API Web per personalizzare i flussi utente di iscrizione e l'integrazione con i sistemi cloud esterni. È possibile usare i connettori API per:

- Integrazione con flussi di lavoro di approvazione personalizzati
- Convalida dati di input utente
- Sovrascrivi attributi utente 
- Eseguire la logica di business personalizzata 

 Per altre informazioni, vedere [usare i connettori API per personalizzare ed estendere](../../active-directory-b2c/api-connectors-overview.md) la documentazione di iscrizione.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Proprietà state per le organizzazioni connesse nella gestione dei diritti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Funzionalità del **prodotto** per la gestione delle directory: gestione dei diritti
 

 Tutte le organizzazioni connesse avranno ora una proprietà aggiuntiva denominata "state". Lo stato controllerà il modo in cui l'organizzazione connessa verrà usata nei criteri che fanno riferimento a "tutte le organizzazioni connesse configurate". Il valore sarà "configurata" (ovvero l'organizzazione è nell'ambito dei criteri che usano la clausola "All") o "proposed", ovvero l'organizzazione non è nell'ambito.  

Le organizzazioni connesse create manualmente avranno un'impostazione predefinita "configurata". Nel frattempo, gli utenti creati automaticamente (creati tramite criteri che consentono a qualsiasi utente da Internet di richiedere l'accesso) verranno impostati per impostazione predefinita su "proposto".  Tutte le organizzazioni connesse create prima del 9 2020 settembre verranno impostate su "configurate". Gli amministratori possono aggiornare questa proprietà in base alle esigenze. [Altre informazioni](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory le identità esterne hanno ora impostazioni di sicurezza avanzate Premium per B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
Le funzionalità di accesso condizionale e di rilevamento dei rischi basate sul rischio di Identity Protection sono ora disponibili in [Azure ad B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Con queste funzionalità di sicurezza avanzate, i clienti possono ora:
- Usare Intelligent Insights per valutare i rischi con le app B2C e gli account degli utenti finali. I rilevamenti includono viaggi atipici, indirizzi IP anonimi, indirizzi IP collegati al malware e Azure AD Intelligence per le minacce. Sono disponibili anche i report basati sull'API e sul portale.
- Risolvere automaticamente i rischi configurando criteri di autenticazione adattiva per gli utenti B2C. Gli sviluppatori e gli amministratori di app possono attenuare i rischi in tempo reale richiedendo l'autenticazione a più fattori o bloccando l'accesso a seconda del livello di rischio utente rilevato, con controlli aggiuntivi disponibili in base alla posizione, al gruppo e all'app.
- Integrazione con Azure AD B2C flussi utente e criteri personalizzati. Le condizioni possono essere attivate da flussi utente predefiniti in Azure AD B2C o possono essere incorporati in criteri personalizzati B2C. Come per gli altri aspetti del flusso utente B2C, la messaggistica dell'esperienza utente finale può essere personalizzata. La personalizzazione è secondo le alternative per la voce, il marchio e la mitigazione dell'organizzazione.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-ottobre 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel 2020 ottobre sono state aggiunte le seguenti 27 nuove applicazioni nella raccolta di app con supporto federativo:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee-Productivity superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/), [zone Technologies Portal](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [ecoChallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [Reminder appuntamento](https://app.appointmentreminder.co.nz/account/login), [cloud. Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [greetly](https://app.greetly.com/), [OrgVitality SSO} (.. /Saas-Apps/orgvitality-SSO-tutorial.MD), [Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md), [flusso ciclo CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [vendite al dettaglio Zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [MEVISIO](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure Virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>È ora possibile trasmettere i log di provisioning a log Analytics

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 

Pubblicare i log di provisioning in log Analytics per:
- Archiviare i log di provisioning per più di 30 giorni
- Definire avvisi e notifiche personalizzati
- Creare dashboard per visualizzare i log
- Eseguire query complesse per analizzare i log 

Per informazioni su come usare la funzionalità, vedere comprendere il modo in cui il [provisioning si integra con i log di monitoraggio di Azure](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>I log di provisioning possono ora essere visualizzati dai proprietari delle applicazioni

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
È ora possibile consentire ai proprietari delle applicazioni di monitorare l'attività del servizio di provisioning e risolvere i problemi senza fornire loro un ruolo privilegiato o crearne un collo di bottiglia. [Altre informazioni](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Ridenominazione di 10 ruoli Azure Active Directory

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Ruoli di Azure  
**Funzionalità del prodotto:** Controllo di accesso
 
Alcuni ruoli predefiniti di Azure Active Directory (AD) hanno nomi diversi da quelli visualizzati nell'interfaccia di amministrazione di Microsoft 365, nel portale di Azure AD e Microsoft Graph. Questa incoerenza può causare problemi nei processi automatizzati. Con questo aggiornamento, verranno rinominati 10 nomi di ruolo per renderli coerenti. Nella tabella seguente sono presenti i nuovi nomi di ruolo:

![Tabella dei nuovi nomi di ruolo](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Supporto di Azure AD B2C per il flusso del codice di autenticazione per le spa con MSAL JS 2. x

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
MSAL.js versione 2. x ora include il supporto per il flusso del codice di autorizzazione per le app Web a pagina singola (Spa). Azure AD B2C supporta ora l'uso del tipo di app SPA sul portale di Azure e l'uso del flusso del codice di autorizzazione MSAL.js con PKCE per le app a singola pagina. Questo consentirà alle Spa di usare Azure AD B2C per gestire l'accesso SSO con browser più recenti e rispettare le raccomandazioni del protocollo di autenticazione più recenti. Introduzione alla registrazione di [un'applicazione a singola pagina (Spa) in Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) esercitazione.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Aggiornamenti da ricordare Multi-Factor Authentication (multi-factor authentication) in un'impostazione attendibile del dispositivo

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Recentemente è stato aggiornato il [ricordare multi-factor authentication (](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) multi-factor authentication) in una funzionalità del dispositivo attendibile per estendere l'autenticazione per un massimo di 365 giorni. Le licenze Azure Active Directory (Azure AD) Premium possono anche usare i [criteri di frequenza di accesso condizionale](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) , che offrono maggiore flessibilità per le impostazioni di riautenticazione.

Per un'esperienza utente ottimale, è consigliabile usare la frequenza di accesso con accesso condizionale per estendere le durate della sessione su dispositivi attendibili, posizioni o sessioni a basso rischio come alternativa all'autenticazione a più fattori in un'impostazione attendibile del dispositivo. Per iniziare, esaminare [le linee guida più recenti per ottimizzare l'esperienza di riautenticazione](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---

## <a name="september-2020"></a>Settembre 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 settembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Sensibilizzazione sulla sicurezza di Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Aggiornamento dell'anteprima pubblica del provisioning cloud

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD **funzionalità del prodotto** per il provisioning cloud: gestione del ciclo di vita delle identità
 
Azure AD Connect aggiornamento dell'anteprima pubblica del provisioning nel cloud offre due miglioramenti principali sviluppati dai commenti dei clienti: 

- Esperienza di mapping degli attributi tramite portale di Azure

    Con questa funzionalità gli amministratori IT possono eseguire il mapping degli attributi utente, gruppo o contatto da AD a Azure AD usando diversi tipi di mapping attualmente presenti. Il mapping degli attributi è una funzionalità usata per standardizzare i valori degli attributi che fluiscono da Active Directory a Azure Active Directory. È possibile determinare se eseguire direttamente il mapping del valore dell'attributo come da AD a Azure AD o utilizzare espressioni per trasformare i valori dell'attributo durante il provisioning degli utenti. [Altre informazioni](../cloud-provisioning/how-to-attribute-mapping.md)

- Esperienza utente di test o provisioning su richiesta

    Dopo aver configurato la configurazione, è possibile eseguire un test per verificare se la trasformazione utente funziona come previsto prima di applicarla a tutti gli utenti nell'ambito. Con il provisioning su richiesta, gli amministratori IT possono immettere il nome distinto (DN) di un utente di Active Directory e verificare se vengono sincronizzati come previsto. Il provisioning su richiesta offre un ottimo modo per garantire che i mapping degli attributi in precedenza funzionino come previsto. [Altre informazioni](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Ripristino di BitLocker controllato in Azure AD-anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione dell'accesso ai dispositivi  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi
 
Quando gli amministratori IT o gli utenti finali leggono le chiavi di ripristino di BitLocker a cui hanno accesso, Azure Active Directory ora genera un log di controllo che acquisisce chi ha eseguito l'accesso alla chiave di ripristino. Lo stesso controllo fornisce i dettagli del dispositivo a cui è stata associata la chiave BitLocker.

Gli utenti finali possono [accedere alle chiavi di ripristino tramite il mio account](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). Gli amministratori IT possono accedere alle chiavi di ripristino tramite l' [API della chiave di ripristino di BitLocker in beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) o tramite il portale di Azure ad. Per altre informazioni, vedere [visualizzare o copiare le chiavi BitLocker nel portale di Azure ad](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Ruolo predefinito amministratore dispositivi Teams

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti con il ruolo di [amministratore dei dispositivi team](../roles/permissions-reference.md#teams-devices-administrator) possono gestire i [dispositivi certificati](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) dai team dall'interfaccia di amministrazione dei team. 

Questo ruolo consente all'utente di visualizzare tutti i dispositivi a colpo d'occhio, con la possibilità di cercare e filtrare i dispositivi. L'utente può anche controllare i dettagli di ogni dispositivo, inclusi l'account connesso e la marca e il modello del dispositivo. L'utente può modificare le impostazioni del dispositivo e aggiornare le versioni del software. Questo ruolo non concede le autorizzazioni per controllare l'attività dei team e la qualità della chiamata del dispositivo.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Funzionalità di query avanzate per oggetti directory

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Grafico MS  
**Funzionalità del prodotto:** esperienza di sviluppo
 
Tutte le nuove funzionalità di query introdotte per gli oggetti directory in API Azure AD sono ora disponibili nell'endpoint v 1.0 e pronto per la produzione. Gli sviluppatori possono contare, cercare, filtrare e ordinare gli oggetti directory e i collegamenti correlati usando gli operatori OData standard.

Per altre informazioni, vedere la documentazione [qui](https://aka.ms/BlogPostMezzoGA)ed è anche possibile inviare commenti e suggerimenti con questo [breve sondaggio](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Anteprima pubblica: valutazione di accesso continuo per i tenant che hanno configurato i criteri di accesso condizionale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
La versione di valutazione dell'accesso continuo (CAE) è ora disponibile in anteprima pubblica per Azure AD tenant con criteri di accesso condizionale. Con CAE, gli eventi e i criteri di sicurezza critici vengono valutati in tempo reale. Sono incluse la disabilitazione dell'account, la reimpostazione della password e la modifica della posizione. Per altre informazioni, vedere [valutazione dell'accesso continuo](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Anteprima pubblica: chiedere agli utenti di richiedere un pacchetto di accesso domande aggiuntive per migliorare le decisioni relative all'approvazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 
Gli amministratori possono ora richiedere che gli utenti che richiedono un pacchetto di accesso rispondano a domande aggiuntive oltre alla giustificazione aziendale nel portale di accesso personale della gestione dei diritti Azure AD. Le risposte degli utenti verranno quindi visualizzate agli approvatori per consentire loro di prendere una decisione di approvazione dell'accesso più accurata. Per ulteriori informazioni, vedere [raccogliere ulteriori informazioni sul richiedente per l'approvazione (anteprima)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Anteprima pubblica: gestione avanzata degli utenti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione utenti  
**Funzionalità del prodotto:** Gestione utenti
 

Il portale Azure AD è stato aggiornato per semplificare la ricerca degli utenti nelle pagine tutti gli utenti e utenti eliminati. Le modifiche nell'anteprima includono: 
- Proprietà utente più visibili, tra cui ID oggetto, stato di sincronizzazione della directory, tipo di creazione ed emittente di identità.
- La ricerca consente ora la ricerca combinata di nomi, messaggi di posta elettronica e ID oggetto.
- Filtro migliorato per tipo di utente (membro, Guest e nessuno), stato di sincronizzazione della directory, tipo di creazione, nome della società e nome di dominio.
- Nuove funzionalità di ordinamento per proprietà quali nome, nome dell'entità utente e data di eliminazione.
- Un nuovo numero totale di utenti che viene aggiornato con qualsiasi ricerca o filtro.

Per ulteriori informazioni, vedere la pagina relativa ai miglioramenti apportati [alla gestione degli utenti (anteprima) in Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nuovo campo note per le applicazioni aziendali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Funzionalità del **prodotto** app aziendali: SSO

È possibile aggiungere note di testo gratuite alle applicazioni aziendali. È possibile aggiungere tutte le informazioni rilevanti che consentiranno di dirigere le applicazioni in applicazioni aziendali. Per altre informazioni, vedere [Guida introduttiva: configurare le proprietà di un'applicazione nel tenant di Azure Active Directory (Azure ad)](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 settembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel settembre 2020 abbiamo aggiunto le seguenti 34 nuove applicazioni nella raccolta di app con supporto federativo:

[VMware Horizon-Unified Access Gateway](), [PULSe Secure PCS](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [luccichi Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [Gender fitness](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [grammatical](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), Saviynt [, BizMerlinHR,](https://marketplace.bizmerlin.net/bmone/signup) [mobile Locker](../saas-apps/mobile-locker-tutorial.md), [ZEngine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [priva Identity & Access Management](https://my.priva.com/), [Nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [FEXA](../saas-apps/fexa-tutorial.md), [firma protetta Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [firma protetta Enterprise Portal installazione di AAD](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [WISTEC online](https://wisteconline.com/auth/oidc), [Oracle PeopleSoft-protetto da F5 Big-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md) [Saviynt](../saas-apps/saviynt-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui: https://aka.ms/AppsTutorial .

Per elencare l'applicazione nella raccolta di app Azure AD, leggere i dettagli qui: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nuovo ruolo di delega nella gestione dei diritti Azure AD: Access Package Assignment Manager

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 
Un nuovo ruolo gestione assegnazione pacchetti di accesso è stato aggiunto in Azure AD gestione dei diritti per fornire autorizzazioni granulari per gestire le assegnazioni. È ora possibile delegare le attività a un utente in questo ruolo, che può delegare le assegnazioni di gestione di un pacchetto di accesso a un proprietario dell'azienda. Tuttavia, un gestore di assegnazione dei pacchetti di accesso non può modificare i criteri del pacchetto di accesso o altre proprietà impostate dagli amministratori. 

Con questo nuovo ruolo, si traggono vantaggio dai privilegi minimi necessari per delegare la gestione delle assegnazioni e mantenere il controllo amministrativo su tutte le altre configurazioni dei pacchetti di accesso. Per altre informazioni, vedere [ruoli di gestione dei diritti](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Modifiche al flusso di onboarding di Privileged Identity Management

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
In precedenza, l'onboarding in Privileged Identity Management (PIM) richiedeva il consenso dell'utente e un flusso di onboarding nel pannello di PIM che includeva la registrazione in Azure AD multi-factor authentication. Con la recente integrazione dell'esperienza PIM nel pannello ruoli e amministratori Azure AD, questa esperienza verrà rimossa. Qualsiasi tenant con licenza P2 valida verrà caricato automaticamente in PIM.

Il caricamento in PIM non ha alcun effetto negativo diretto sul tenant. È possibile prevedere le seguenti modifiche:
- Opzioni di assegnazione aggiuntive, ad esempio Active vs. idonee con l'ora di inizio e di fine quando si effettua un'assegnazione nel pannello ruoli e amministratori PIM o Azure AD. 
- Meccanismi di ambito aggiuntivi, come le unità amministrative e i ruoli personalizzati, introdotti direttamente nell'esperienza di assegnazione. 
- Se si è un amministratore globale o un amministratore del ruolo con privilegi, è possibile iniziare a ricevere alcuni messaggi di posta elettronica aggiuntivi come il digest settimanale di PIM. 
- È anche possibile vedere l'entità servizio MS-PIM nel log di controllo relativa all'assegnazione di ruolo. Questa modifica prevista non influirà sul normale flusso di lavoro.

 Per altre informazioni, vedere [iniziare a usare Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Gestione dei diritti di Azure AD: il riquadro Seleziona delle risorse del pacchetto di accesso ora Visualizza per impostazione predefinita le risorse attualmente presenti nel catalogo selezionato

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 

Nel flusso di creazione del pacchetto di accesso, nella scheda ruoli risorsa, il comportamento di selezione riquadro è in corso di modifica. Attualmente, il comportamento predefinito prevede la visualizzazione di tutte le risorse di proprietà dell'utente e delle risorse aggiunte al catalogo selezionato. 

Questa esperienza verrà modificata per visualizzare solo le risorse attualmente aggiunte al catalogo per impostazione predefinita, in modo che gli utenti possano selezionare facilmente le risorse dal catalogo. L'aggiornamento aiuterà a individuare le risorse da aggiungere ai pacchetti di accesso e a ridurre il rischio di aggiungere inavvertitamente le risorse di proprietà dell'utente che non fanno parte del catalogo. Per altre informazioni, vedere [creare un nuovo pacchetto di accesso in Azure ad gestione dei diritti](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Agosto 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Aggiornamenti ai requisiti del firewall di Azure server Multi-Factor Authentication

**Tipo:** Modifica pianificata  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
A partire dall'1 ottobre 2020, i requisiti del firewall del server Azure multi-factor authentication richiedono intervalli IP aggiuntivi.

Se nell'organizzazione sono presenti regole del firewall in uscita, aggiornare le regole in modo che i server multi-factor authentication possano comunicare con tutti gli intervalli di indirizzi IP necessari. Gli intervalli IP sono documentati in [Azure server multi-factor authentication requisiti del firewall](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Modifiche imminenti all'esperienza utente nel punteggio di identità sicure

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Funzionalità del **prodotto** Identity Protection: protezione & della sicurezza delle identità

Si sta aggiornando il portale Identity Secure score per allinearsi alle modifiche introdotte nella [nuova versione](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)di Microsoft Secure score. 

La versione di anteprima con le modifiche sarà disponibile all'inizio di settembre. Le modifiche apportate alla versione di anteprima includono:
- "Punteggio sicuro identità" rinominato "Punteggio sicuro per l'identità" per l'allineamento del marchio con Microsoft Secure Score
- Punti normalizzati alla scala standard e segnalati in percentuali anziché punti

In questa versione di anteprima i clienti possono passare dall'esperienza esistente alla nuova esperienza. Questa anteprima durerà fino alla fine del 2020 novembre. Al termine dell'anteprima, i clienti verranno automaticamente indirizzati alla nuova esperienza UX.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nuove autorizzazioni di accesso guest con restrizioni in Azure AD-anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Controllo di accesso   
**Funzionalità del prodotto:** Gestione utenti

Sono state aggiornate le autorizzazioni a livello di directory per gli utenti guest. Queste autorizzazioni consentono agli amministratori di richiedere ulteriori restrizioni e controlli per l'accesso utente Guest esterno. Gli amministratori possono ora aggiungere ulteriori restrizioni per l'accesso degli utenti esterni ai profili e alle informazioni di appartenenza. Con questa funzionalità di anteprima pubblica, i clienti possono gestire l'accesso degli utenti esterni su larga scala offuscando le appartenenze ai gruppi, inclusa la limitazione degli utenti guest nella visualizzazione delle appartenenze ai gruppi in cui si trovano.

Per altre informazioni, vedere autorizzazioni di [accesso Guest limitate](../enterprise-users/users-restrict-guest-permissions.md) e [autorizzazioni predefinite per gli utenti](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Disponibilità generale delle query Delta per le entità servizio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Grafico MS  
**Funzionalità del prodotto:** esperienza di sviluppo
 
Microsoft Graph query Delta supporta ora il tipo di risorsa nella versione 1.0:
- Entità servizio

Ora i client possono tenere traccia delle modifiche apportate alle risorse in modo efficiente e offrono la soluzione migliore per sincronizzare le modifiche apportate a tali risorse con un archivio dati locale. Per informazioni su come configurare queste risorse in una query, vedere [usare una query Delta per tenere traccia delle modifiche nei dati Microsoft Graph](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Disponibilità generale delle query Delta per oAuth2PermissionGrant

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Grafico MS  
**Funzionalità del prodotto:** esperienza di sviluppo

Microsoft Graph query Delta supporta ora il tipo di risorsa nella versione 1.0:
- OAuth2PermissionGrant

I client possono ora tenere traccia delle modifiche apportate alle risorse in modo efficiente e offrono la soluzione migliore per sincronizzare le modifiche apportate a tali risorse con un archivio dati locale. Per informazioni su come configurare queste risorse in una query, vedere [usare una query Delta per tenere traccia delle modifiche nei dati Microsoft Graph](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-agosto 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel 2020 agosto sono state aggiunte le 25 nuove applicazioni seguenti nella raccolta di app con supporto federativo:

[Backup365](https://portal.backup365.io/login), [pulpito](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [connettore Enlyft Dynamics 365](http://enlyft.com/), [soluzioni software per l'utilizzo dello spazio Serraview](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), notality, [visibilmente](../saas-apps/visibly-tutorial.md) [,](https://web.uniq.app/) [Zylo](../saas-apps/zylo-tutorial.md), Edmentum-corsi di [valutazione esatti percorso](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), [WireWheel](../saas-apps/wirewheel-tutorial.md), [Zix Compliance and Capture](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), la piattaforma di [controllo business aziendale di GreenLight](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [Genetec clearance](https://www.clearance.network/), [ISAMs](../saas-apps/isams-tutorial.md) [, VeraSMART, amiko](../saas-apps/verasmart-tutorial.md) [, twingate](https://amiko.web.rivero.app/) [,](https://auth.twingate.com/signup) [imbuto leasing](https://nestiolistings.com/sso/oidc/azure/authorize/), [Scalefusion](https://scalefusion.com/users/sign_in/) [, Bpanda,](https://goto.bpanda.com/login) [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect) [,](https://www.wandera.com/) [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Foreste di risorse ora disponibili per Azure AD DS 

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure ad Domain Services   
**Funzionalità del prodotto:** Azure AD Domain Services
 
La funzionalità delle foreste di risorse in Azure AD Domain Services è ora disponibile a livello generale. È ora possibile abilitare l'autorizzazione senza sincronizzazione dell'hash delle password per usare Azure AD Domain Services, inclusa l'autorizzazione per smart card. Per altre informazioni, vedere [la pagina relativa ai concetti e alle funzionalità dei set di repliche per Azure Active Directory Domain Services (anteprima)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>È ora disponibile il supporto della replica a livello di area per Azure AD domini gestiti di DS

**Tipo:** Nuova funzionalità   
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services
 
È possibile espandere un dominio gestito in modo che ogni tenant di Azure AD contenga più di un set di repliche. I set di repliche possono essere aggiunti a qualsiasi rete virtuale con peering in qualsiasi area di Azure che supporta Azure AD Domain Services. I set di replica aggiuntivi in aree di Azure diverse forniscono il ripristino di emergenza geografico per le applicazioni legacy se un'area di Azure passa offline. Per altre informazioni, vedere [la pagina relativa ai concetti e alle funzionalità dei set di repliche per Azure Active Directory Domain Services (anteprima)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Disponibilità generale di Azure AD My Sign-Ins

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
Azure AD My Sign-Ins è una nuova funzionalità che consente agli utenti aziendali di esaminare la cronologia di accesso per verificare la presenza di attività insolite. Inoltre, questa funzionalità consente agli utenti finali di segnalare "questo non è stato" o "questo è stato me" nelle attività sospette. Per altre informazioni sull'uso di questa funzionalità, vedere [visualizzare e cercare le attività di accesso recenti dalla pagina My Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Il provisioning utenti basato su SAP SuccessFactors HR per Azure AD è ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
È ora possibile integrare SAP SuccessFactors come origine autorevole di identità con Azure AD e automatizzare il ciclo di vita delle identità end-to-end usando eventi HR come nuovi assunzioni e terminazioni per eseguire il provisioning e il deprovisioning degli account in Azure AD. 

Per altre informazioni su come configurare il provisioning in ingresso di SAP SuccessFactors in Azure AD, vedere l'esercitazione [configurare SAP SuccessFactors per Active Directory il provisioning degli utenti](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Supporto di MS API Graph per la connessione Open ID personalizzato per Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
In precedenza, i provider di connessione Open ID personalizzati potevano essere aggiunti o gestiti solo tramite il portale di Azure. Ora i Azure AD B2C clienti possono aggiungerli e gestirli tramite Microsoft Graph versione beta di API. Per informazioni su come configurare questa risorsa con le API, vedere [tipo di risorsa IdentityProvider](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Assegnare Azure AD ruoli predefiniti ai gruppi cloud

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso

È ora possibile assegnare Azure AD ruoli predefiniti ai gruppi cloud con questa nuova funzionalità. Ad esempio, è possibile assegnare il ruolo di amministratore di SharePoint a Contoso_SharePoint_Admins gruppo. È anche possibile usare PIM per rendere il gruppo un membro idoneo del ruolo, anziché concedere l'accesso permanente. Per informazioni su come configurare questa funzionalità, vedere [usare i gruppi di cloud per gestire le assegnazioni di ruolo in Azure Active Directory (anteprima)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Il ruolo predefinito del leader aziendale di Insights è ora disponibile

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti nel ruolo di leader aziendale di Insights possono accedere a un set di dashboard e informazioni dettagliate tramite l' [applicazione M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Questo include l'accesso completo a tutti i dashboard e le funzionalità di esplorazione dei dati e delle informazioni fornite. Tuttavia, gli utenti con questo ruolo non hanno accesso alle impostazioni di configurazione del prodotto, che è responsabilità del ruolo di amministratore di Insights. Per ulteriori informazioni su questo ruolo, vedere [autorizzazioni per i ruoli di amministratore in Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Il ruolo predefinito amministratore di Insights è ora disponibile

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti nel ruolo di amministratore di Insights possono accedere al set completo di funzionalità amministrative nell' [applicazione M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Un utente con questo ruolo può leggere le informazioni della directory, monitorare l'integrità dei servizi, i ticket di supporto file e accedere agli aspetti delle impostazioni dell'amministratore di Insights. Per ulteriori informazioni su questo ruolo, vedere [autorizzazioni per i ruoli di amministratore in Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>L'amministratore dell'applicazione e l'amministratore di applicazioni cloud possono gestire le proprietà di estensione delle applicazioni

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso
 
In precedenza, solo l'amministratore globale poteva gestire la [proprietà di estensione](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Questa funzionalità è ora abilitata anche per l'amministratore dell'applicazione e l'amministratore di applicazioni cloud.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>4.6.263.0 hotfix MIM 2016 SP2 e connettori 1.1.1301.0

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

È disponibile un [pacchetto hotfix rollup (Build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) per Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Questo pacchetto di rollup contiene gli aggiornamenti per i componenti Gestione certificati MIM, Gestione sincronizzazione MIM e PAM. Inoltre, i connettori generici MIM Build 1.1.1301.0 include aggiornamenti per il connettore Graph.

---
 
## <a name="july-2020"></a>Luglio 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>In qualità di amministratore IT, voglio destinare le app client usando l'accesso condizionale

**Tipo:** Modifica pianificata   
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Con la versione GA della condizione delle app client nell'accesso condizionale, i nuovi criteri vengono ora applicati per impostazione predefinita a tutte le applicazioni client. Sono inclusi i client di autenticazione legacy. I criteri esistenti rimarranno invariati, ma l'interruttore *configura sì/no* verrà rimosso dai criteri esistenti per individuare facilmente le app client a cui vengono applicati i criteri. 

Quando si creano nuovi criteri, assicurarsi di escludere gli utenti e gli account di servizio che utilizzano ancora l'autenticazione legacy. in caso contrario, verranno bloccati. [Altre informazioni](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Correzioni imminenti per la conformità di SCIM

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il servizio di provisioning Azure AD sfrutta lo standard SCIM per l'integrazione con le applicazioni. L'implementazione dello standard SCIM è in continua evoluzione e si prevede di apportare modifiche al comportamento in merito alla modalità di esecuzione delle operazioni di PATCH, nonché di impostare la proprietà "Active" su una risorsa. [Altre informazioni](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>L'impostazione del proprietario del gruppo nel portale di amministrazione di Azure verrà modificata

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

È possibile configurare le impostazioni del proprietario nella pagina di impostazioni generale per limitare i privilegi di assegnazione dei proprietari a un gruppo limitato di utenti nel portale di amministrazione di Azure e nel pannello di accesso. Sarà presto possibile assegnare i privilegi del proprietario del gruppo non solo su questi due portali UX, ma anche applicare i criteri nel back-end per garantire un comportamento coerente tra gli endpoint, ad esempio PowerShell e Microsoft Graph. 

Si inizierà a disabilitare l'impostazione corrente per i clienti che non lo usano e offrirà un'opzione per definire l'ambito degli utenti per i privilegi del proprietario del gruppo nei prossimi mesi. Per istruzioni sull'aggiornamento delle impostazioni di gruppo, vedere Modificare le informazioni sul gruppo usando [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Il servizio di registrazione Azure Active Directory sta terminando il supporto per TLS 1,0 e 1,1

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Funzionalità del prodotto:** Piattaforma

Transport Layer Security (TLS) 1,2 e i server e i client di aggiornamento comunicheranno presto con Registrazione dispositivo Azure Active Directory servizio. Il supporto per TLS 1,0 e 1,1 per la comunicazione con Registrazione dispositivo Azure AD servizio viene ritirato:
- Il 31 agosto 2020, in tutti i cloud sovrani (GCC High, DoD e così via)
- Il 30 ottobre 2020, in tutti i cloud commerciali

[Altre](../devices/reference-device-registration-tls-1-2.md) informazioni su TLS 1,2 per il servizio di registrazione Azure ad.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Accesso a Windows Hello for business visibile nei log di accesso Azure AD

**Tipo:** Correzione  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Windows Hello for business consente agli utenti finali di accedere ai computer Windows con un movimento, ad esempio un PIN o una biometrica. Azure AD gli amministratori potrebbero voler distinguere gli accessi di Windows Hello for business da altri accessi Windows come parte del percorso di un'organizzazione per l'autenticazione senza password. 

Gli amministratori possono ora verificare se un'autenticazione di Windows ha usato Windows Hello for business controllando la scheda Dettagli autenticazione per un evento di accesso di Windows nel pannello Azure AD Sign-Ins nel portale di Azure. Le autenticazioni di Windows Hello for business includeranno "WindowsHelloForBusiness" nel campo metodo di autenticazione. Per ulteriori informazioni sull'interpretazione dei log di Sign-In, consultare la [documentazione relativa ai log di accesso](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Correzioni per il comportamento di eliminazione del gruppo e miglioramenti delle prestazioni

**Tipo:** Correzione  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
In precedenza, quando un gruppo è stato modificato da "in-scope" a "out-of-scope" e un amministratore ha fatto clic su Restart prima del completamento della modifica, l'oggetto gruppo non è stato eliminato. A questo punto l'oggetto gruppo verrà eliminato dall'applicazione di destinazione quando esce dall'ambito (disabilitato, eliminato, non assegnato o non ha superato il filtro di ambito). [Altre informazioni](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Anteprima pubblica: gli amministratori possono ora aggiungere contenuto personalizzato nel messaggio di posta elettronica ai revisori durante la creazione di una verifica di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance delle identità
 
Quando viene creata una nuova verifica di accesso, il revisore riceve un messaggio di posta elettronica che richiede di completare la verifica di accesso. Molti clienti hanno chiesto di aggiungere contenuto personalizzato al messaggio di posta elettronica, ad esempio le informazioni di contatto, o altri contenuti di supporto aggiuntivi per guidare il revisore. 

Ora disponibile in versione di anteprima pubblica, gli amministratori possono specificare contenuto personalizzato nel messaggio di posta elettronica inviato ai revisori aggiungendo contenuto nella sezione "avanzata" delle verifiche di accesso Azure AD. Per istruzioni sulla creazione [di verifiche di accesso, vedere creare una verifica di accesso di gruppi e applicazioni in Azure ad verifiche di accesso](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Flusso del codice di autorizzazione per le app a singola pagina disponibili

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** esperienza di sviluppo
 
A causa delle restrizioni dei cookie di terze parti del browser moderni, ad esempio Safari ITP, le Spa dovranno usare il flusso del codice di autorizzazione anziché il flusso implicito per mantenere SSO e MSAL.js v 2. x supporterà il flusso del codice di autorizzazione. 

Sono disponibili aggiornamenti corrispondenti per il portale di Azure, quindi è possibile aggiornare la SPA in modo che sia di tipo "Spa" e usare il flusso del codice di autenticazione. Vedere [utenti di accesso e ottenere un token di accesso in una spa JavaScript usando il flusso del codice di autenticazione](../develop/quickstart-v2-javascript-auth-code.md) per ulteriori indicazioni.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD proxy di applicazione supporta ora il client Web Servizi Desktop remoto

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Azure AD proxy di applicazione supporta ora il client Web di Servizi Desktop remoto (RDS). Il client Web RDS consente agli utenti di accedere a Desktop remoto infrastruttura tramite qualsiasi browser che supporta HTLM5, ad esempio Microsoft Edge, Internet Explorer 11, Google Chrome e così via. Gli utenti possono interagire con le app Remote o con i desktop, come avviene con un dispositivo locale da qualsiasi luogo. Con Azure AD proxy di applicazione è possibile aumentare la sicurezza della distribuzione di Servizi Desktop remoto applicando i criteri di pre-autenticazione e di accesso condizionale per tutti i tipi di app rich client. Per informazioni aggiuntive, vedere [pubblicare desktop remoto con Azure ad proxy di applicazione](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Prossima generazione Azure AD B2C flussi utente in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
L'esperienza di flusso utente semplificata offre la parità di funzionalità con funzionalità di anteprima ed è la casa per tutte le nuove funzionalità. Gli utenti saranno in grado di abilitare nuove funzionalità nello stesso flusso utente, riducendo la necessità di creare più versioni con ogni nuova versione della funzionalità. Infine, la nuova UX intuitiva semplifica la selezione e la creazione dei flussi utente. Provare subito a [creare un flusso utente](../../active-directory-b2c/tutorial-create-user-flows.md). 

Per ulteriori informazioni sui flussi degli utenti, vedere la pagina relativa [alle versioni dei flussi utente in Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 luglio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel luglio 2020 abbiamo aggiunto le seguenti 55 nuove applicazioni nella raccolta di app con supporto federativo:

[Clap Your Hands](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects receptionist](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [COCOOM](https://start.cocoom.com/), [Coins costruzioni cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote mt](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/) [, GREMINDERS,](https://app.greminders.com/o365-oauth) [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [DataSite](../saas-apps/datasite-tutorial.md), [Blogin](../saas-apps/blogin-tutorial.md) [, IntSights, kpifire](../saas-apps/intsights-tutorial.md) [,](../saas-apps/kpifire-tutorial.md) [TextLine](../saas-apps/textline-tutorial.md), [Cloud Academy-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [community Spark](../saas-apps/community-spark-tutorial.md), [chat](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Single Sign-on di hub dati Michigan, in](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md) [uscita](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [right-hand Cybersecurity ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [verme](../saas-apps/verme-tutorial.md), [lenses.io](../saas-apps/lensesio-tutorial.md), [momenta](../saas-apps/momenta-tutorial.md), [Rise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), addicate [bot](https://tellme365liteweb.azurewebsites.net/), [Inspire](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/), [Smartschool (School Management System)](https://smartschoolz.com/login), [Zepto-Intelligent cronometraggio](https://user.zepto-ai.com/signin), [studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [KEMP LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 luglio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per la nuova app integrata [LinkedIn Learning](../saas-apps/linkedin-learning-provisioning-tutorial.md).

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Visualizzare le assegnazioni di ruolo in tutti gli ambiti e la possibilità di scaricarli in un file CSV

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso
 
È ora possibile visualizzare le assegnazioni di ruolo in tutti gli ambiti per un ruolo nella scheda "ruoli e amministratori" del portale di Azure AD. È anche possibile scaricare le assegnazioni di ruolo per ogni ruolo in un file CSV. Per istruzioni sulla visualizzazione e l'aggiunta di assegnazioni di ruolo, vedere [visualizzare e assegnare ruoli di amministratore in Azure Active Directory](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication Software Development (Azure AMF SDK) deprecato

**Tipo:** Deprecato  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Azure Multi-Factor Authentication Software Development (Azure AMF SDK) ha raggiunto la fine del ciclo di vita il 14 novembre 2018, come annunciato per la prima volta nel novembre 2017. Microsoft arresterà il servizio SDK in vigore il 30 settembre 2020. Tutte le chiamate effettuate all'SDK avranno esito negativo.

Se l'organizzazione USA Azure multi-factor authentication SDK, è necessario eseguire la migrazione entro il 30 settembre 2020:
- Azure multi-factor authentication SDK per MIM: se si usa l'SDK con MIM, è necessario eseguire la migrazione al server Azure multi-factor authentication e attivare Privileged Access Management (PAM) seguendo queste [istruzioni](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Azure multi-factor authentication SDK per app personalizzate: è consigliabile integrare l'app in Azure AD e usare l'accesso condizionale per applicare l'autenticazione a più fattori. Per iniziare, vedere questa [pagina](../manage-apps/plan-an-application-integration.md). 

---

## <a name="june-2020"></a>Giugno 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Condizione di rischio utente nei criteri di accesso condizionale

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Il supporto dei rischi per gli utenti in Azure AD i criteri di accesso condizionale consente di creare più criteri basati sul rischio utente. Per utenti e app diversi possono essere necessari livelli di rischio utente minimi diversi. In base al rischio utente, è possibile creare criteri per bloccare l'accesso, richiedere l'autenticazione a più fattori, modificare la password sicura o reindirizzare a Microsoft Cloud App Security per applicare i criteri di sessione, ad esempio il controllo aggiuntivo.

La condizione di rischio utente richiede Azure AD Premium P2 perché usa Azure Identity Protection, un'offerta P2. per altre informazioni sull'accesso condizionale, vedere [Azure ad documentazione sull'accesso condizionale](../conditional-access/index.yml).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO supporta ora le app che richiedono l'impostazione di SPNameQualifier quando richiesto

**Tipo:** Correzione  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
Per alcune applicazioni SAML è necessario che SPNameQualifier venga restituito nell'oggetto dell'asserzione quando richiesto. A questo punto Azure AD risponde correttamente quando viene richiesto un SPNameQualifier nel criterio NameID della richiesta. Questa operazione funziona anche per l'accesso avviato da SP e l'accesso avviato da IdP verrà eseguito.  Per ulteriori informazioni sul protocollo SAML in Azure Active Directory, vedere il [protocollo Single Sign-On SAML](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD collaborazione B2B supporta l'invito di utenti MSA e Google in tenant di Azure per enti pubblici

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 

I tenant di Azure per enti pubblici che usano le funzionalità di collaborazione B2B possono ora invitare gli utenti con un account Microsoft o Google. Per sapere se il tenant può usare queste funzionalità, seguire le istruzioni in [come è possibile stabilire se la collaborazione B2B è disponibile nel tenant di Azure US Government?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>L'oggetto utente in MS Graph V1 include ora le proprietà externalUserState e externalUserStateChangedDateTime

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 

Le proprietà externalUserState e externalUserStateChangedDateTime possono essere usate per trovare i guest B2B invitati che non hanno accettato gli inviti ma anche l'automazione della compilazione, ad esempio l'eliminazione di utenti che non hanno accettato gli inviti dopo un certo numero di giorni. Queste proprietà sono ora disponibili in MS Graph V1. Per informazioni sull'uso di queste proprietà, vedere [tipo di risorsa utente](/graph/api/resources/user).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Gestire le sessioni di autenticazione in Azure AD accesso condizionale è ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Le funzionalità di gestione delle sessioni di autenticazione consentono di configurare la frequenza con cui gli utenti devono fornire le credenziali di accesso e se devono fornire le credenziali dopo aver chiuso e riaperto i browser per offrire maggiore sicurezza e flessibilità nell'ambiente in uso.
 
Inoltre, la gestione delle sessioni di autenticazione usata per l'autenticazione del primo fattore si applica solo ai dispositivi Azure AD aggiunti, Azure AD ibrido Uniti e Azure AD registrati. La gestione della sessione di autenticazione verrà ora applicata anche a multi-factor authentication. Per altre informazioni, vedere [configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](../conditional-access/howto-conditional-access-session-lifetime.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 giugno

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel giugno 2020 abbiamo aggiunto le 29 nuove applicazioni seguenti nella raccolta di app con supporto federativo:

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [Raketa](../saas-apps/raketa-tutorial.md), [Segment](../saas-apps/segment-tutorial.md), [ai auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.io](../saas-apps/proto.io-tutorial.md), [Gatekeeper](https://www.gatekeeperhq.com/), [Hub Planner](../saas-apps/hub-planner-tutorial.md), [ansira-partner go-to-Market casella degli strumenti](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation su cloud](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [Kisi Physical Security](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [zero](https://www.teamzero.com/), [Instation](https://instation.invillia.com/), [EDX for business SAML 2,0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning Platform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [Field ID](../saas-apps/field-id-tutorial.md), [curriculas SAML](../saas-apps/curricula-saml-tutorial.md), [Perforce Core-Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md), [concompliance cloud](https://cloud.metacompliance.com/), [Smallstep](https://smallstep.com/sso-ssh/)  

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial . Per elencare l'applicazione nella raccolta di app Azure AD, leggere i dettagli qui: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>I connettori API per l'iscrizione self-service di identità esterne sono ora disponibili in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
I connettori API delle identità esterne consentono di sfruttare le API Web per integrare l'iscrizione self-service con i sistemi cloud esterni. Ciò significa che è ora possibile richiamare le API Web come passaggi specifici in un flusso di iscrizione per attivare flussi di lavoro personalizzati basati sul cloud. Ad esempio, è possibile usare i connettori API per:

- Eseguire l'integrazione con un flusso di lavoro di approvazione personalizzato.
- Esegui la correzione dell'identità
- Convalida dati di input utente
- Sovrascrivi attributi utente
- Eseguire la logica di business personalizzata

Per altre informazioni su tutte le esperienze possibili con i connettori API, vedere [usare i connettori API per personalizzare ed estendere l'iscrizione self-service](../external-identities/api-connectors-overview.md)o [personalizzare le identità esterne self-service iscrizione con le integrazioni dell'API Web](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Esegui il provisioning su richiesta e ottieni utenti nelle tue app in pochi secondi

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il servizio di provisioning Azure AD attualmente funziona su base ciclica. Il servizio viene eseguito ogni 40 minuti. La [funzionalità di provisioning su richiesta](https://aka.ms/provisionondemand) consente di selezionare un utente ed eseguirne il provisioning in pochi secondi. Questa funzionalità consente di risolvere rapidamente i problemi di provisioning, senza dover eseguire un riavvio per forzare l'avvio del ciclo di provisioning. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nuova autorizzazione per l'uso di Azure AD gestione dei diritti in Graph

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Gestione dei diritti
 
Una nuova autorizzazione delegata EntitlementManagement. Read. All è ora disponibile per l'uso con l'API di gestione dei diritti in Microsoft Graph beta. Per altre informazioni sulle API disponibili, vedere [uso dell'API di gestione dei diritti Azure ad](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>API di Identity Protection disponibili nella versione 1.0

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Le API Microsoft Graph riskyUsers e riskDetections sono ora disponibili a livello generale. Ora che sono disponibili nell'endpoint v 1.0, viene invitato a usarli nell'ambiente di produzione. Per ulteriori informazioni, consultare la documentazione di [Microsoft Graph](/graph/api/resources/identityprotectionroot).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Le etichette di riservatezza per applicare i criteri ai gruppi di Microsoft 365 sono ora disponibili a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione
 

È ora possibile creare etichette di riservatezza e usare le impostazioni delle etichette per applicare i criteri a gruppi di Microsoft 365, inclusi i criteri di accesso utente esterno (pubblico o privato). È possibile creare un'etichetta con l'informativa sulla privacy come privata e i criteri di accesso utente esterno per non consentire l'aggiunta di utenti guest. Quando un utente applica questa etichetta a un gruppo, il gruppo sarà privato e nessun utente Guest potrà essere aggiunto al gruppo. 

Le etichette di riservatezza sono importanti per proteggere i dati cruciali per l'azienda e consentono di gestire i gruppi su larga scala, in modo conforme e sicuro. Per informazioni sull'uso delle etichette di riservatezza, vedere [assegnare etichette di riservatezza ai gruppi di Microsoft 365 in Azure Active Directory (anteprima)](../enterprise-users/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Aggiornamenti per il supporto di Microsoft Identity Manager per clienti Azure AD Premium

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il supporto di Azure è ora disponibile per i componenti di integrazione Azure AD di Microsoft Identity Manager 2016, fino alla fine del supporto esteso per Microsoft Identity Manager 2016. Per altre informazioni, vedere l' [aggiornamento del supporto per Azure ad Premium clienti che usano Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>L'uso di condizioni di appartenenza a gruppi nella configurazione delle attestazioni SSO è aumentato

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
In precedenza, il numero di gruppi che è possibile usare quando si modificano le attestazioni in modo condizionale in base all'appartenenza al gruppo all'interno di una singola configurazione di applicazione era limitato a 10. L'uso di condizioni di appartenenza a gruppi nella configurazione delle attestazioni SSO è ora aumentato fino a un massimo di 50 gruppi. Per ulteriori informazioni sulla configurazione delle attestazioni, vedere la pagina relativa alla [configurazione delle attestazioni SSO delle applicazioni aziendali](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Abilitazione della formattazione di base sul componente testo della pagina di accesso in informazioni personalizzate distintive dell'azienda.

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
La funzionalità di personalizzazione dell'azienda nell'esperienza di accesso Azure AD/Microsoft 365 è stata aggiornata per consentire al cliente di aggiungere collegamenti ipertestuali e formattazione semplice, inclusi il tipo di carattere, la sottolineatura e il corsivo in grassetto. Per istruzioni sull'uso di questa funzionalità, vedere [aggiungere informazioni personalizzate alla pagina di accesso Azure Active Directory dell'organizzazione](./customize-branding.md).

---

### <a name="provisioning-performance-improvements"></a>Miglioramenti delle prestazioni del provisioning

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il servizio di provisioning è stato aggiornato per ridurre il tempo necessario per il completamento di un [ciclo incrementale](../app-provisioning/how-provisioning-works.md#incremental-cycles) . Questo significa che gli utenti e i gruppi verranno sottoposti a provisioning nelle applicazioni più velocemente rispetto a quanto precedentemente fatto. Tutti i nuovi processi di provisioning creati dopo 6/10/2020 trarranno automaticamente vantaggio dai miglioramenti delle prestazioni. Tutte le applicazioni configurate per il provisioning prima del 6/10/2020 dovranno essere riavviate una volta dopo 6/10/2020 per sfruttare i miglioramenti delle prestazioni. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Annunciare la deprecazione di ADAL e MS Graph parità

**Tipo:** Deprecato  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi

Ora che sono disponibili le librerie di autenticazione Microsoft (MSAL), non verranno più aggiunte nuove funzionalità alle librerie di autenticazione Azure Active Directory (ADAL) e le patch di sicurezza verranno terminate il 30 giugno 2022. Per ulteriori informazioni su come eseguire la migrazione a MSAL, vedere [eseguire la migrazione di applicazioni a Microsoft Authentication Library (MSAL)](../develop/msal-migration.md).

Abbiamo inoltre completato il lavoro per rendere disponibili tutte le funzionalità di Azure AD Graph tramite MS Graph. Quindi, Azure AD API Graph riceveranno solo bugfix e correzioni per la sicurezza fino al 30 giugno 2022. Per altre informazioni, vedere [aggiornare le applicazioni per l'uso di Microsoft Authentication Library e dell'API Microsoft Graph](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
