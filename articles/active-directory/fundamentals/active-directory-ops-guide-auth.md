---
title: Guida di riferimento per le operazioni di gestione dell'autenticazione Azure Active Directory
description: Questa guida di riferimento per le operazioni descrive i controlli e le azioni da eseguire per proteggere la gestione dell'autenticazione
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: f91928812dd2f9f850b5e4c3af16ce5c82608e05
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371053"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Guida di riferimento per le operazioni di gestione dell'autenticazione Azure Active Directory

Questa sezione della Guida di riferimento per le [operazioni di Azure ad](active-directory-ops-guide-intro.md) descrive i controlli e le azioni da eseguire per proteggere e gestire le credenziali, definire l'esperienza di autenticazione, assegnare i delegati, misurare l'utilizzo e definire i criteri di accesso in base alla postura di sicurezza dell'organizzazione.

> [!NOTE]
> Questi consigli sono aggiornati alla data di pubblicazione, ma possono cambiare nel tempo. Le organizzazioni devono valutare continuamente le proprie procedure di identità, in quanto i prodotti e i servizi Microsoft evolvono nel tempo.

## <a name="key-operational-processes"></a>Principali processi operativi

### <a name="assign-owners-to-key-tasks"></a>Assegnare i proprietari alle attività principali

La gestione di Azure Active Directory richiede l'esecuzione continua delle attività e dei processi operativi principali, che potrebbero non essere parte di un progetto di implementazione. È ancora importante configurare queste attività per ottimizzare l'ambiente. Le attività principali e i proprietari consigliati includono:

| Attività | Proprietario |
| :- | :- |
| Gestire la configurazione del ciclo di vita della Single Sign-On (SSO) in Azure AD | Team operativo IAM |
| Progettare criteri di accesso condizionale per applicazioni Azure AD | Team di architettura InfoSec |
| Archiviare l'attività di accesso in un sistema SIEM | Team operativo di InfoSec |
| Archiviare gli eventi di rischio in un sistema SIEM | Team operativo di InfoSec |
| Valutazione e analisi dei report di sicurezza | Team operativo di InfoSec |
| Valutazione ed analisi degli eventi di rischio | Team operativo di InfoSec |
| Valutazione e analisi degli utenti contrassegnati per i report di rischio e vulnerabilità da Azure AD Identity Protection | Team operativo di InfoSec |

> [!NOTE]
> Azure AD Identity Protection richiede una licenza Azure AD Premium P2. Per trovare la licenza corretta per i propri requisiti, vedere [confronto tra le funzionalità disponibili a livello generale delle edizioni Azure ad free e Azure ad Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Quando si esamina l'elenco, è possibile che sia necessario assegnare un proprietario per le attività che non dispongono di un proprietario o modificare la proprietà per le attività che non sono allineate alle raccomandazioni precedenti.

#### <a name="owner-recommended-reading"></a>Lettura consigliata dal proprietario

- [Assegnazione dei ruoli di amministratore in Azure Active Directory](../roles/permissions-reference.md)
- [Governance in Azure](../../governance/index.yml)

## <a name="credentials-management"></a>Gestione di credenziali

### <a name="password-policies"></a>Criteri per la password

Gestire le password in modo sicuro è una delle parti più importanti della gestione delle identità e degli accessi e spesso la principale destinazione degli attacchi. Azure AD supporta diverse funzionalità che consentono di evitare che un attacco venga eseguito correttamente.

Usare la tabella seguente per trovare la soluzione consigliata per attenuare il problema che deve essere risolto:

| Problema | Recommendation |
| :- | :- |
| Nessun meccanismo per la protezione da password vulnerabili | Abilita Azure AD la [reimpostazione della password self-service (SSPR)](../authentication/concept-sspr-howitworks.md) e la [protezione con password](../authentication/concept-password-ban-bad-on-premises.md) |
| Nessun meccanismo per rilevare le password perse | Abilitare la [sincronizzazione dell'hash delle password](../hybrid/how-to-connect-password-hash-synchronization.md) (pH) per ottenere informazioni dettagliate |
| Uso di AD FS e non è possibile passare all'autenticazione gestita | Abilita [ad FS blocco intelligente Extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) e/o [Azure ad blocco intelligente](../authentication/howto-password-smart-lockout.md) |
| Il criterio password usa regole basate sulla complessità, ad esempio lunghezza, più set di caratteri o scadenza | Riconsiderare a favore delle [procedure consigliate di Microsoft](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) e cambiare l'approccio alla gestione delle password e distribuire [Azure ad la protezione delle password](../authentication/concept-password-ban-bad.md). |
| Gli utenti non sono registrati per l'uso di multi-factor authentication | [Registrare tutte le informazioni di sicurezza dell'utente](../identity-protection/howto-identity-protection-configure-mfa-policy.md) in modo che possano essere usate come meccanismo per verificare l'identità dell'utente insieme alla password |
| Non esiste alcuna revoca delle password in base al rischio dell'utente | Distribuire i [criteri di rischio utente di Azure ad Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) per forzare le modifiche delle password sulle credenziali perse usando SSPR |
| Non esiste un meccanismo di blocco intelligente per proteggere l'autenticazione dannosa da attori non validi provenienti da indirizzi IP identificati | Distribuire l'autenticazione gestita dal cloud con la sincronizzazione dell'hash delle password o [l'autenticazione pass-through](../hybrid/how-to-connect-pta-quick-start.md) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Lettura consigliata per i criteri password

- [Procedure consigliate per Azure AD e AD FS: difesa da attacchi a spruzzo di password-Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Abilitare la reimpostazione della password self-service e la protezione con password

Gli utenti che hanno la necessità di modificare o reimpostare le password sono una delle principali fonti di volume e i costi delle chiamate help desk. Oltre ai costi, la modifica della password come strumento per attenuare i rischi per gli utenti è un passaggio fondamentale per migliorare il comportamento di sicurezza dell'organizzazione.

Come minimo, è consigliabile distribuire Azure AD la [reimpostazione della password self-service](../authentication/concept-sspr-howitworks.md) (SSPR) e la protezione delle [password](../authentication/howto-password-ban-bad-on-premises-deploy.md) locale per eseguire le operazioni seguenti:

- Devia help desk le chiamate.
- Sostituire l'uso di password temporanee.
- Sostituire tutte le soluzioni di gestione delle password self-service esistenti che si basano su una soluzione locale.
- [Eliminare le password vulnerabili](../authentication/concept-password-ban-bad.md) nell'organizzazione.

> [!NOTE]
> Per le organizzazioni con una sottoscrizione Azure AD Premium P2, è consigliabile distribuire SSPR e usarlo come parte di un [criterio di rischio utente Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md).

### <a name="strong-credential-management"></a>Gestione delle credenziali avanzata

Le password non sono sufficientemente sicure per impedire agli attori malintenzionati di ottenere l'accesso all'ambiente. Come minimo, qualsiasi utente con un account con privilegi deve essere abilitato per multi-factor authentication. Idealmente, è consigliabile abilitare la [registrazione combinata](../authentication/concept-registration-mfa-sspr-combined.md) e richiedere a tutti gli utenti di registrarsi per l'autenticazione a più fattori e SSPR usando l' [esperienza di registrazione combinata](../user-help/security-info-setup-signin.md). Infine, è consigliabile adottare una strategia per [fornire resilienza](../authentication/concept-resilient-controls.md) per ridurre il rischio di blocco a causa di circostanze impreviste.

![Flusso dell'esperienza utente combinata](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Resilienza dell'autenticazione di interruzione locale

Oltre ai vantaggi della semplicità e all'abilitazione del rilevamento di credenziali perse, Azure AD la sincronizzazione dell'hash delle password (pH) e l'autenticazione a più fattori di Azure consentono agli utenti di accedere alle applicazioni SaaS e Microsoft 365 nonostante le interruzioni locali a causa di attacchi cibernetici, ad esempio [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). È anche possibile abilitare pH insieme alla Federazione. L'abilitazione di pH consente un fallback di autenticazione quando i servizi federativi non sono disponibili.

Se nell'organizzazione locale manca una strategia di resilienza delle interruzioni o ne è presente una che non è integrata con Azure AD, è necessario distribuire Azure AD pH e definire un piano di ripristino di emergenza che includa pH. L'abilitazione di Azure AD pH consentirà agli utenti di eseguire l'autenticazione con Azure AD se il Active Directory locale non è disponibile.

![flusso di sincronizzazione dell'hash delle password](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Per comprendere meglio le opzioni di autenticazione, vedere [scegliere il metodo di autenticazione appropriato per la soluzione di identità ibrida Azure Active Directory](../hybrid/choose-ad-authn.md).

### <a name="programmatic-usage-of-credentials"></a>Utilizzo di credenziali a livello di codice

Azure AD script che usano PowerShell o le applicazioni che usano l'API Microsoft Graph richiedono l'autenticazione protetta. Una gestione delle credenziali scadente che esegue gli script e gli strumenti aumenta il rischio di furto di credenziali. Se si usano script o applicazioni che si basano su password hardcoded o richieste di password, è necessario prima esaminare le password nei file di configurazione o nel codice sorgente, quindi sostituire tali dipendenze e usare le identità gestite di Azure, Integrated-Windows l'autenticazione o i [certificati](../reports-monitoring/tutorial-access-api-with-certificates.md) , quando possibile. Per le applicazioni in cui le soluzioni precedenti non sono possibili, provare a usare [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Se si determina che esistono entità servizio con credenziali password e non si è certi del modo in cui le credenziali password sono protette da script o applicazioni, contattare il proprietario dell'applicazione per comprendere meglio i modelli di utilizzo.

Microsoft consiglia inoltre di contattare i proprietari dell'applicazione per comprendere i modelli di utilizzo se sono presenti entità servizio con credenziali password.

## <a name="authentication-experience"></a>Esperienza di autenticazione

### <a name="on-premises-authentication"></a>Autenticazione locale

L'autenticazione federata con autenticazione integrata di Windows (IWA) o l'autenticazione gestita Single Sign-On (SSO) semplice con sincronizzazione dell'hash delle password o autenticazione pass-through è la migliore esperienza utente all'interno della rete aziendale con i controller di dominio locali. Riduce al minimo i tempi di richiesta delle credenziali e riduce il rischio di attacchi di phishing da parte degli utenti. Se si sta già usando l'autenticazione gestita dal cloud con pH o PTA, ma gli utenti devono ancora digitare la password durante l'autenticazione in locale, è necessario [distribuire immediatamente SSO](../hybrid/how-to-connect-sso.md)facile. D'altra parte, se si è attualmente federati con piani per la migrazione all'autenticazione gestita dal cloud, è necessario implementare l'accesso SSO facile come parte del progetto di migrazione.

### <a name="device-trust-access-policies"></a>Criteri di accesso attendibilità dispositivo

Come gli utenti dell'organizzazione, anche i dispositivi rappresentano identità importanti da proteggere. È possibile usare l'identità di un dispositivo per proteggere le risorse in qualsiasi momento e da qualunque posizione. L'autenticazione del dispositivo e l'accounting per il tipo di attendibilità migliorano il comportamento di sicurezza e l'usabilità da:

- Evitare attriti, ad esempio con l'autenticazione a più fattori, quando il dispositivo è attendibile
- Blocco dell'accesso da dispositivi non attendibili
- Per i dispositivi Windows 10, è [possibile fornire Single Sign-on alle risorse locali in modo uniforme](../devices/azuread-join-sso.md).

È possibile portare a termine questo obiettivo portando le identità dei dispositivi e gestendo tali identità in Azure AD usando uno dei metodi seguenti:

- Le organizzazioni possono usare [Microsoft Intune](/intune/what-is-intune) per gestire il dispositivo e applicare i criteri di conformità, attestare l'integrità del dispositivo e impostare i criteri di accesso condizionale a seconda che il dispositivo sia conforme. Microsoft Intune possibile gestire i dispositivi iOS, i desktop Mac (tramite l'integrazione con JAMF), i desktop Windows (usando in modo nativo la gestione dei dispositivi mobili per Windows 10 e la co-gestione con Microsoft endpoint Configuration Manager) e i dispositivi mobili Android.
- [Azure ad ibrido join](../devices/hybrid-azuread-join-managed-domains.md) fornisce la gestione con criteri di gruppo o Microsoft endpoint Configuration Manager in un ambiente con Active Directory dispositivi di computer aggiunti a un dominio. Le organizzazioni possono distribuire un ambiente gestito tramite pH o PTA con seamless SSO. I dispositivi Azure AD massimizzano la produttività degli utenti tramite SSO nelle risorse cloud e locali, consentendo al tempo stesso di proteggere l'accesso alle risorse cloud e locali con [accesso condizionale](../conditional-access/overview.md) .

Se si dispone di dispositivi Windows aggiunti a un dominio che non sono registrati nel cloud o di dispositivi Windows aggiunti a un dominio registrati nel cloud ma senza criteri di accesso condizionale, è necessario registrare i dispositivi non registrati e, in entrambi i casi, [usare Azure ad ibrido join come controllo](../conditional-access/require-managed-devices.md) nei criteri di accesso condizionale.

![Screenshot della concessione nei criteri di accesso condizionale che richiedono un dispositivo ibrido](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Se si gestiscono dispositivi con MDM o Microsoft Intune, ma non si usano controlli dispositivo nei criteri di accesso condizionale, è consigliabile usare Richiedi che il [dispositivo sia contrassegnato come conforme](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) come controllo in tali criteri.

![Screenshot della concessione nei criteri di accesso condizionale che richiedono la conformità del dispositivo](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Lettura consigliata criteri di accesso attendibilità dispositivo

- [Procedura: Pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido](../devices/hybrid-azuread-join-plan.md)
- [Configurazioni di identità e accesso dei dispositivi](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello for Business

In Windows 10, [Windows Hello for business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) sostituisce le password con l'autenticazione a due fattori avanzata nei PC. Windows Hello for business offre un'esperienza multi-factor authentication semplificata per gli utenti e riduce la dipendenza dalle password. Se non si è iniziato a implementare i dispositivi Windows 10 o se ne sono stati distribuiti solo parzialmente, è consigliabile eseguire l'aggiornamento a Windows 10 e [abilitare Windows Hello for business](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) in tutti i dispositivi.

Per ulteriori informazioni sull'autenticazione senza password, vedere [un mondo senza password con Azure Active Directory](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Autenticazione e assegnazione dell'applicazione

### <a name="single-sign-on-for-apps"></a>Single Sign-on per le app

Fornire un meccanismo di Single Sign-On standardizzato per l'intera organizzazione è fondamentale per ottimizzare l'esperienza utente, la riduzione del rischio, la possibilità di creare report e governance. Se si utilizzano applicazioni che supportano SSO con Azure AD ma sono attualmente configurate per l'utilizzo di account locali, è necessario riconfigurare tali applicazioni in modo che utilizzino SSO con Azure AD. Analogamente, se si utilizzano applicazioni che supportano SSO con Azure AD ma utilizzano un altro provider di identità, è necessario riconfigurare tali applicazioni in modo da utilizzare SSO anche con Azure AD. Per le applicazioni che non supportano i protocolli federativi ma che supportano l'autenticazione basata su form, è consigliabile configurare l'applicazione per l'uso dell'insieme di credenziali delle [password](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) con Azure ad proxy di applicazione.

![Accesso basato su password AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Se non si dispone di un meccanismo per individuare le applicazioni non gestite nell'organizzazione, è consigliabile implementare un processo di individuazione utilizzando una soluzione CASB (cloud Access Security broker Solution), ad esempio [Microsoft cloud app Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Infine, se si dispone di un Azure AD raccolta di app e si usano applicazioni che supportano SSO con Azure AD, è consigliabile [elencare l'applicazione nella raccolta di app](../azuread-dev/howto-app-gallery-listing.md).

#### <a name="single-sign-on-recommended-reading"></a>Lettura consigliata per l'accesso Single Sign-on

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migrazione di applicazioni AD FS a Azure AD

La [migrazione di app da ad FS a Azure ad](../manage-apps/migrate-adfs-apps-to-azure.md) consente funzionalità aggiuntive per la sicurezza, una gestione più coerente e un'esperienza di collaborazione migliore. Se si dispone di applicazioni configurate in AD FS che supportano SSO con Azure AD, è necessario riconfigurare tali applicazioni in modo che utilizzino SSO con Azure AD. Se le applicazioni sono configurate in AD FS con configurazioni non comuni non supportate da Azure AD, è necessario contattare i proprietari dell'app per capire se la configurazione speciale è un requisito assoluto dell'applicazione. Se non è necessario, è necessario riconfigurare l'applicazione per l'uso di SSO con Azure AD.

![Azure AD come provider di identità primario](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure ad Connect Health per ADFS](../hybrid/how-to-connect-health-adfs.md) può essere utilizzato per raccogliere i dettagli di configurazione relativi a ogni applicazione potenzialmente migrata al Azure ad.

### <a name="assign-users-to-applications"></a>Assegnare gli utenti alle applicazioni

Per [assegnare gli utenti alle applicazioni](../manage-apps/assign-user-or-group-access-portal.md) , è consigliabile eseguire il mapping con i gruppi, perché consentono una maggiore flessibilità e capacità di gestione su larga scala. I vantaggi derivanti dall'uso dei gruppi includono l' [appartenenza dinamica](../enterprise-users/groups-dynamic-membership.md) a gruppi e la [delega ai proprietari di app](../fundamentals/active-directory-accessmanagement-managing-group-owners.md). Pertanto, se si usano già e si gestiscono i gruppi, è consigliabile eseguire le azioni seguenti per migliorare la gestione su larga scala:

- Delegare la gestione e la governance dei gruppi ai proprietari dell'applicazione.
- Consente l'accesso self-service all'applicazione.
- Definire gruppi dinamici se gli attributi utente possono determinare in modo coerente l'accesso alle applicazioni.
- Implementare l'attestazione per i gruppi usati per l'accesso alle applicazioni usando le verifiche di [accesso Azure ad](../governance/access-reviews-overview.md).

D'altra parte, se si trovano applicazioni con assegnazione a singoli utenti, assicurarsi di implementare la [governance](../governance/index.yml) per tali applicazioni.

#### <a name="assign-users-to-applications-recommended-reading"></a>Assegnare gli utenti alle applicazioni consigliate per la lettura

- [Assegnare utenti e gruppi a un'applicazione in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Delegare le autorizzazioni di registrazione all'app in Azure Active Directory](../roles/delegate-app-roles.md)
- [Regole di appartenenza dinamica per i gruppi in Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Criteri di accesso

### <a name="named-locations"></a>Posizioni specifiche

Con le [località denominate](../reports-monitoring/quickstart-configure-named-locations.md) in Azure ad, è possibile etichettare gli intervalli di indirizzi IP attendibili nell'organizzazione. Azure AD usa le località denominate per:

- Impedisci falsi positivi negli eventi di rischio. L'accesso da un percorso di rete attendibile riduce il rischio di accesso di un utente.
- Configurare l'[Accesso condizionale basato sulla località](../reports-monitoring/quickstart-configure-named-locations.md).

![Località denominate](./media/active-directory-ops-guide/active-directory-ops-img10.png)

In base alla priorità, usare la tabella seguente per trovare la soluzione consigliata che meglio soddisfa le esigenze dell'organizzazione:

| **Priorità** | **Scenario** | **Consiglio** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Se si usa pH o PTA e le località denominate non sono state definite | Definire le località denominate per migliorare il rilevamento degli eventi di rischio |
| 2 | Se si è federati e non si usa l'attestazione "insideCorporateNetwork" e le località denominate non sono state definite | Definire le località denominate per migliorare il rilevamento degli eventi di rischio |
| 3 | Se non si usano le località denominate nei criteri di accesso condizionale e non è presente alcun rischio o controllo del dispositivo nei criteri di accesso condizionale | Configurare i criteri di accesso condizionale per includere le località denominate |
| 4 | Se si è federati e si usa l'attestazione "insideCorporateNetwork" e le località denominate non sono state definite | Definire le località denominate per migliorare il rilevamento degli eventi di rischio |
| 5 | Se si usano indirizzi IP attendibili con l'autenticazione a più fattori anziché le località denominate e contrassegnarle come attendibili | Definire le località denominate e contrassegnarle come attendibili per migliorare il rilevamento degli eventi di rischio |

### <a name="risk-based-access-policies"></a>Criteri di accesso in base al rischio

Azure AD possibile calcolare il rischio per ogni accesso e ogni utente. L'uso dei rischi come criterio nei criteri di accesso può offrire un'esperienza utente migliore, ad esempio un minor numero di richieste di autenticazione e una maggiore sicurezza, ad esempio, solo gli utenti che richiedono la richiesta e automatizzano la risposta e la correzione.

![Criteri di rischio di accesso](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Se si possiedono già Azure AD Premium licenze P2 che supportano l'uso dei rischi nei criteri di accesso, ma non vengono usati, è consigliabile aggiungere i rischi per la sicurezza.

#### <a name="risk-based-access-policies-recommended-reading"></a>Lettura consigliata per i criteri di accesso in base al rischio

- [Procedura: Configurare i criteri di rischio di accesso](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Procedura: Configurare i criteri di rischio utente](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Criteri di accesso alle applicazioni client

Microsoft Intune Application Management (MAM) offre la possibilità di eseguire il push di controlli di protezione dei dati, ad esempio crittografia di archiviazione, PIN, pulitura dell'archiviazione remota e così via, in applicazioni per dispositivi mobili client compatibili come Outlook Mobile. Inoltre, è possibile creare criteri di accesso condizionale per [limitare l'accesso](../conditional-access/app-based-conditional-access.md) ai servizi cloud, ad esempio Exchange Online, da app approvate o compatibili.

Se i dipendenti installano applicazioni che supportano MAM, ad esempio app per dispositivi mobili di Office, per accedere alle risorse aziendali, ad esempio Exchange Online o SharePoint Online, e supportano anche BYOD (Bring Your Own Device), è consigliabile distribuire i criteri MAM dell'applicazione per gestire la configurazione dell'applicazione nei dispositivi personali senza registrazione MDM e quindi aggiornare i criteri di accesso condizionale per consentire l'accesso solo da client

![Controllo di concessione dell'accesso condizionale](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Se i dipendenti installano applicazioni con supporto MAM per le risorse aziendali e l'accesso è limitato ai dispositivi gestiti da Intune, è consigliabile distribuire i criteri MAM dell'applicazione per gestire la configurazione dell'applicazione per i dispositivi personali e aggiornare i criteri di accesso condizionale per consentire l'accesso solo dai client che supportano MAM.

### <a name="conditional-access-implementation"></a>Implementazione dell'accesso condizionale

L'accesso condizionale è uno strumento essenziale per migliorare il comportamento di sicurezza dell'organizzazione. Pertanto, è importante attenersi alle procedure consigliate seguenti:

- Assicurarsi che a tutte le applicazioni SaaS sia applicato almeno un criterio
- Evitare di combinare il filtro **tutte le app** con il controllo **blocco** per evitare il rischio di blocco
- Evitare di usare **tutti gli utenti** come filtro e aggiungere inavvertitamente i **Guest**
- **Eseguire la migrazione di tutti i criteri "Legacy" al portale di Azure**
- Rilevare tutti i criteri per utenti, dispositivi e applicazioni
- Usare i criteri di accesso condizionale per [implementare](../conditional-access/plan-conditional-access.md)l'autenticazione a più fattori, anziché usare l'autenticazione a più fattori **per utente**
- Disporre di un piccolo set di criteri di base che possono essere applicati a più applicazioni
- Definire gruppi di eccezioni vuoti e aggiungerli ai criteri per avere una strategia di eccezione
- Pianificare gli account [break Glass](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) senza controlli multi-factor authentication
- Assicura un'esperienza coerente tra Microsoft 365 applicazioni client, ad esempio teams, OneDrive, Outlook e così via. implementando lo stesso set di controlli per servizi come Exchange Online e SharePoint Online
- L'assegnazione ai criteri deve essere implementata tramite gruppi, non singoli utenti
- Eseguire verifiche regolari dei gruppi di eccezioni usati nei criteri per limitare il tempo di inattività della sicurezza da parte degli utenti. Se si è proprietari di Azure AD P2, è possibile usare le verifiche di accesso per automatizzare il processo

#### <a name="conditional-access-recommended-reading"></a>Lettura consigliata per l'accesso condizionale

- [Procedure consigliate per l'accesso condizionale in Azure Active Directory](../conditional-access/best-practices.md)
- [Configurazioni di identità e accesso dei dispositivi](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Informazioni di riferimento sulle impostazioni di accesso condizionale Azure Active Directory](../conditional-access/concept-conditional-access-conditions.md)
- [Criteri comuni di accesso condizionale](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Area di superficie di accesso

### <a name="legacy-authentication"></a>Autenticazione legacy

Credenziali complesse, ad esempio l'autenticazione a più fattori, non possono proteggere le app usando protocolli di autenticazione legacy, che lo rendono il vettore di attacco preferito da attori malintenzionati Il blocco dell'autenticazione legacy è fondamentale per migliorare il comportamento di sicurezza dell'accesso.

L'autenticazione legacy è un termine che fa riferimento ai protocolli di autenticazione usati da app come:

- Client di Office meno recenti che non usano l'autenticazione moderna (ad esempio, Office 2010 client)
- Client che usano protocolli di posta elettronica, ad esempio IMAP/SMTP/POP

Poiché gli utenti malintenzionati preferiscono questi protocolli, quasi il [100% degli attacchi con spray per la password](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) usa protocolli di autenticazione legacy. Gli hacker usano i protocolli di autenticazione legacy, perché non supportano l'accesso interattivo, che è necessario per ulteriori problemi di sicurezza, ad esempio l'autenticazione a più fattori e l'autenticazione del dispositivo.

Se l'autenticazione legacy viene ampiamente utilizzata nell'ambiente in uso, è consigliabile pianificare la migrazione dei client legacy ai client che supportano [l'autenticazione moderna](/office365/enterprise/modern-auth-for-office-2013-and-2016) appena possibile. Nello stesso token, se si dispone già di alcuni utenti che usano l'autenticazione moderna, ma altri ancora usano l'autenticazione legacy, è necessario seguire questa procedura per bloccare i client di autenticazione legacy:

1. Usare i [report delle attività di accesso](../reports-monitoring/concept-sign-ins.md) per identificare gli utenti che usano ancora l'autenticazione legacy e la correzione dei piani:

   a. Eseguire l'aggiornamento ai client che supportano l'autenticazione moderna per gli utenti interessati.
   
   b. Pianificare un intervallo di tempo cutover per bloccare i passaggi riportati di seguito.
   
   c. Identificare le applicazioni legacy che hanno una dipendenza rigida dall'autenticazione legacy. Vedere il passaggio 3 riportato di seguito.

2. Disabilitare i protocolli legacy nell'origine (ad esempio cassetta postale di Exchange) per gli utenti che non usano l'autenticazione legacy per evitare un'esposizione più approfondita.
3. Per gli account rimanenti (idealmente non umani, ad esempio gli account del servizio), usare [l'accesso condizionale per limitare i protocolli legacy](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) dopo l'autenticazione.

#### <a name="legacy-authentication-recommended-reading"></a>Lettura consigliata per l'autenticazione legacy

- [Abilitare o disabilitare l'accesso POP3 o IMAP4 alle cassette postali in Exchange Server](/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Concessioni di consenso

In un attacco di concessione di consenso illecito, l'autore dell'attacco crea un'applicazione registrata Azure AD che richiede l'accesso ai dati, ad esempio le informazioni di contatto, la posta elettronica o i documenti. Gli utenti potrebbero concedere il consenso alle applicazioni dannose tramite attacchi di phishing durante l'atterraggio su siti Web dannosi.

Di seguito è riportato un elenco di app con autorizzazioni che possono essere esaminate per i servizi cloud Microsoft:

- App con app o delegate \* . Autorizzazioni ReadWrite
- Le app con autorizzazioni delegate possono leggere, inviare o gestire i messaggi di posta elettronica per conto dell'utente
- App a cui viene concesso usando le autorizzazioni seguenti:

| Risorsa | Autorizzazione |
| :- | :- |
| Exchange Online | EAS. AccessAsUser. All |
| | EWS. AccessAsUser. All |
| | Mail.Read |
| API Microsoft Graph | Mail.Read |
| | Mail. Read. Shared |
| | Mail. ReadWrite |

- Le app hanno concesso la rappresentazione utente completa dell'utente che ha eseguito l'accesso. Esempio:

|Risorsa | Autorizzazione |
| :- | :- |
| API Microsoft Graph| Directory.AccessAsUser.All |
| API REST di Azure | user_impersonation |

Per evitare questo scenario, è necessario fare riferimento a [rilevare e correggere le concessioni di consenso illecito in Office 365](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) per identificare e correggere le applicazioni con concessioni illecite o applicazioni che dispongono di più concessioni rispetto a quelle necessarie. A questo punto, [rimuovere completamente il self-service](../manage-apps/configure-user-consent.md) e [stabilire le procedure di governance](../manage-apps/configure-admin-consent-workflow.md). Infine, pianificare le verifiche regolari delle autorizzazioni dell'app e rimuoverle quando non sono necessarie.

#### <a name="consent-grants-recommended-reading"></a>Concessioni di consenso consigliate

- [Autorizzazioni API Microsoft Graph](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Impostazioni utente e gruppo

Di seguito sono elencate le impostazioni di utenti e gruppi che possono essere bloccate se non è necessaria un'attività aziendale esplicita:

#### <a name="user-settings"></a>Impostazioni utente

- **Utenti esterni** : la collaborazione esterna può avvenire a livello organico nell'azienda con servizi quali team, Power bi, SharePoint Online e Azure Information Protection. Se si dispone di vincoli espliciti per controllare la collaborazione esterna avviata dall'utente, è consigliabile abilitare gli utenti esterni usando [Azure ad gestione dei diritti](../governance/entitlement-management-overview.md) o un'operazione controllata, ad esempio tramite la help desk. Se non si vuole consentire la collaborazione esterna biologica per i servizi, è possibile [impedire ai membri di invitare completamente gli utenti esterni](../external-identities/delegate-invitations.md). In alternativa, è anche possibile [consentire o bloccare domini specifici](../external-identities/allow-deny-list.md) negli inviti degli utenti esterni.
- **Registrazioni** per l'app: quando registrazioni app sono abilitate, gli utenti finali possono caricare le applicazioni e concedere l'accesso ai dati. Un esempio tipico di registrazione delle app è costituito dagli utenti che consentono i plug-in di Outlook o da assistenti vocali come Alexa e Siri per leggere la posta elettronica e il calendario o inviare messaggi di posta elettronica per loro conto. Se il cliente decide di disattivare la registrazione dell'app, è necessario che i team InfoSec e IAM siano interessati alla gestione delle eccezioni (registrazioni di app necessarie in base ai requisiti aziendali), perché dovrebbero registrare le applicazioni con un account amministratore e molto probabilmente richiedono la progettazione di un processo per rendere operativo il processo.
- **Portale di amministrazione** : le organizzazioni possono bloccare il pannello Azure AD nel portale di Azure in modo che gli amministratori non possano accedere alla gestione Azure AD nel portale di Azure e confonderli. Passare alle impostazioni utente nel portale di gestione di Azure AD per limitare l'accesso:

![Accesso limitato al portale di amministrazione](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Non gli amministratori può comunque accedere alle interfacce di gestione Azure AD tramite la riga di comando e altre interfacce programmatiche.

#### <a name="group-settings"></a>Impostazioni dei gruppi

**Gestione dei gruppi self-service/gli utenti possono creare gruppi di sicurezza/gruppi di Microsoft 365.** Se non è presente alcuna iniziativa self-service per i gruppi nel cloud, i clienti potrebbero decidere di disattivarla fino a quando non saranno pronti a usare questa funzionalità.

#### <a name="groups-recommended-reading"></a>Gruppi consigliati per la lettura

- [Informazioni su Collaborazione B2B di Azure Active Directory](../external-identities/what-is-b2b.md)
- [Integrazione di applicazioni con Azure Active Directory](../develop/quickstart-register-app.md)
- [App, autorizzazioni e consenso in Azure Active Directory.](../develop/quickstart-register-app.md)
- [Usare i gruppi per gestire l'accesso alle risorse in Azure Active Directory](./active-directory-manage-groups.md)
- [Impostazione della gestione self-service dell'accesso alle applicazioni in Azure Active Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Traffico proveniente da posizioni impreviste

Gli utenti malintenzionati provengono da varie parti del mondo. Gestire questo rischio usando i criteri di accesso condizionale con la posizione come condizione. La [condizione della posizione](../conditional-access/location-condition.md) di un criterio di accesso condizionale consente di bloccare l'accesso per le posizioni da cui non esiste alcun motivo aziendale per accedere.

![Crea una nuova posizione denominata](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Se disponibile, usare una soluzione di gestione di informazioni ed eventi di sicurezza (SIEM) per analizzare e trovare i modelli di accesso tra le aree. Se non si usa un prodotto SIEM o non si inseriscono informazioni di autenticazione da Azure AD, è consigliabile usare [monitoraggio di Azure](../../azure-monitor/overview.md) per identificare i modelli di accesso tra le aree.

## <a name="access-usage"></a>Accesso all'utilizzo

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Log Azure AD archiviati e integrati con i piani di risposta agli eventi imprevisti

L'accesso a attività di accesso, controlli ed eventi di rischio per Azure AD è fondamentale per la risoluzione dei problemi, l'analisi dell'utilizzo e le indagini forensi. Azure AD fornisce l'accesso a queste origini tramite le API REST con un periodo di conservazione limitato. Un sistema SIEM (Security Information and Event Management) o una tecnologia di archiviazione equivalente è la chiave per l'archiviazione a lungo termine dei controlli e del supporto. Per abilitare l'archiviazione a lungo termine dei log di Azure AD, è necessario aggiungerli alla soluzione SIEM esistente o usare [monitoraggio di Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md). Log di archiviazione che possono essere usati nell'ambito dei piani di risposta agli eventi imprevisti e delle indagini.

#### <a name="logs-recommended-reading"></a>Letture consigliate per i log

- [Informazioni di riferimento sull'API di controllo di Azure Active Directory](/graph/api/resources/directoryaudit?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [Riferimento API del report sull'attività di accesso di Azure Active Directory](/graph/api/resources/signin?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [Ottenere dati con l'API di creazione report di Azure AD con i certificati](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph per Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Informazioni di riferimento sull'API di gestione di Office 365](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Come usare il pacchetto di contenuto Azure Active Directory Power BI](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Riepilogo

Esistono 12 aspetti di un'infrastruttura di identità sicura. Questo elenco consente di proteggere e gestire ulteriormente le credenziali, definire l'esperienza di autenticazione, assegnare i delegati, misurare l'utilizzo e definire i criteri di accesso in base al comportamento di sicurezza dell'organizzazione.

- Assegnare i proprietari alle attività principali.
- Implementare le soluzioni per rilevare password deboli o perse, migliorare la gestione e la protezione delle password e proteggere ulteriormente l'accesso degli utenti alle risorse.
- Consente di gestire l'identità dei dispositivi per proteggere le risorse in qualsiasi momento e da qualsiasi posizione.
- Implementare l'autenticazione con password.
- Fornire un meccanismo di Single Sign-On standardizzato nell'intera organizzazione.
- Esegui la migrazione delle app da AD FS a Azure AD per garantire una maggiore sicurezza e una gestione più coerente.
- Assegnare gli utenti alle applicazioni usando i gruppi per consentire una maggiore flessibilità e la possibilità di gestire su larga scala.
- Configurare i criteri di accesso in base al rischio.
- Bloccare i protocolli di autenticazione legacy.
- Rilevare e correggere le concessioni di consenso illecite.
- Bloccare le impostazioni di utenti e gruppi.
- Abilita l'archiviazione a lungo termine dei log di Azure AD per la risoluzione dei problemi, l'analisi di utilizzo e le indagini forensi.

## <a name="next-steps"></a>Passaggi successivi

Introduzione alle [azioni e ai controlli operativi di governance di identità](active-directory-ops-guide-govern.md).