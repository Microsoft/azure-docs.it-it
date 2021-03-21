---
title: Autenticazione per soluzioni ibride di gestione delle identità di Azure AD
titleSuffix: Active Directory
description: Questa guida ha lo scopo di aiutare CEO, CIO, CISO, Chief Identity Architect, Enterprise Architect e decisori dei settori IT e della sicurezza che hanno la responsabilità di scegliere un metodo di autenticazione per la soluzione ibrida di gestione delle identità di Azure AD in organizzazioni di medie e grandi dimensioni.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 15d62f40b50617fd1f6e543cb404a0d38361d3bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836496"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Scegliere il metodo di autenticazione appropriato per la soluzione ibrida di gestione delle identità di Azure AD

La scelta del metodo di autenticazione corretto rappresenta la priorità assoluta per le organizzazioni che desiderano spostare le proprie applicazioni nel cloud. È bene non prendere questa decisione con leggerezza, per i motivi seguenti:

1. È la priorità assoluta per un'organizzazione che desideri passare al cloud.

2. Il metodo di autenticazione è un componente fondamentale della presenza di un'organizzazione nel cloud, perché controlla l'accesso a tutti i dati e le risorse presenti nel cloud.

3. È alla base di tutte le altre funzionalità avanzate di sicurezza e dell'esperienza utente in Azure AD.

L'identità è il nuovo piano di controllo della sicurezza IT, pertanto l'autenticazione è il guardiano degli accessi di un'organizzazione al nuovo ambiente cloud. Le organizzazioni hanno bisogno di un piano di controllo delle identità che ne rafforzi la sicurezza e tenga le app cloud al riparo dalle intrusioni.

> [!NOTE]
> Per cambiare metodo di autenticazione sono necessarie attività di pianificazione, test e possibilmente tempo di inattività. L' [implementazione temporanea](./how-to-connect-staged-rollout.md) è un ottimo modo per testare la migrazione degli utenti dalla Federazione all'autenticazione cloud.

### <a name="out-of-scope"></a>Fuori ambito
Le organizzazioni che non dispongono di un footprint di directory in locale esistente non sono interessate dal presente articolo. In genere, tali aziende creano identità solo nel cloud e ciò non richiede una soluzione ibrida di gestione delle identità. Queste identità esistono esclusivamente nel cloud e non sono associate a identità locali corrispondenti.

## <a name="authentication-methods"></a>Metodi di autenticazione
Con la soluzione ibrida di gestione delle identità di Azure AD come nuovo piano di controllo, l'autenticazione è alla base dell'accesso al cloud. La scelta del metodo di autenticazione appropriato è una prima decisione fondamentale nella configurazione di una soluzione ibrida di gestione delle identità di Azure AD. Implementare il metodo di autenticazione configurato mediante Azure AD Connect, che esegue anche il provisioning degli utenti nel cloud.

Per scegliere un metodo di autenticazione è necessario prendere in considerazione il tempo, l'infrastruttura esistente, la complessità e i costi di implementazione della propria scelta. Questi fattori variano in base all'organizzazione e possono cambiare nel corso del tempo.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure Active Directory supporta i metodi di autenticazione per le soluzioni ibride di gestione delle identità descritti di seguito.

### <a name="cloud-authentication"></a>Autenticazione cloud
Quando si sceglie questo metodo di autenticazione, è Azure AD a gestire la procedura di accesso degli utenti. Affiancandolo a un accesso Single Sign-On trasparente, gli utenti possono accedere alle applicazioni cloud senza dover immettere nuovamente le proprie credenziali. Con l'autenticazione cloud è possibile scegliere tra due opzioni:

**Sincronizzazione dell'hash delle password di Azure AD**. È il modo più semplice per abilitare l'autenticazione per gli oggetti directory locali in Azure AD. Gli utenti possono usare gli stessi nome utente e password usati in locale senza la necessità di implementare infrastrutture aggiuntive. Alcune funzionalità premium di Azure AD, come ad esempio Identity Protection e [Azure AD Domain Services](../../active-directory-domain-services/tutorial-create-instance.md), richiedono la sincronizzazione dell'hash delle password indipendentemente dal metodo di autenticazione scelto.

> [!NOTE]
> Le password non vengono mai archiviate in testo non crittografato o crittografate con un algoritmo reversibile in Azure AD. Per altre informazioni sul processo di sincronizzazione dell'hash delle password, vedere [Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Autenticazione pass-through di Azure AD**. Fornisce un semplice processo di convalida delle password per i servizi di autenticazione di Azure AD mediante un agente software eseguito in uno o più server locali. I server convalidano gli utenti direttamente con l'istanza locale di Active Directory, che assicura che la convalida delle password non venga eseguita nel cloud.

Le società che per questioni di sicurezza devono applicare immediatamente gli stati degli account utente locali, i criteri di gestione delle password e gli orari di accesso potrebbero usare questo metodo di autenticazione. Per altre informazioni sul processo effettivo di autenticazione pass-through, vedere [Accesso utente con l'autenticazione pass-through di Azure AD](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Autenticazione federata
Se si sceglie questo metodo di autenticazione, Azure AD trasferisce il processo di autenticazione a un sistema di autenticazione attendibile separato, ad esempio un sistema Active Directory Federation Services (AD FS) locale, per convalidare la password dell'utente.

Il sistema di autenticazione può fornire ulteriori requisiti di autenticazione avanzati. Ne sono esempi l'autenticazione basata su smart card e l'autenticazione a più fattori di terze parti. Per altre informazioni, vedere [Implementazione di Active Directory Federation Services](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

La sezione seguente aiuta a decidere quale metodo di autenticazione sia più adatto alle proprie esigenze mediante un albero delle decisioni. Questo risulta utile per scegliere se implementare l'autenticazione federata o cloud per la propria soluzione ibrida di gestione delle identità di Azure AD.

## <a name="decision-tree"></a>Albero delle decisioni

![Albero delle decisioni per l'autenticazione di Azure Active Directory](./media/choose-ad-authn/azure-ad-authn-image1.png)

Dettagli relativi alle domande nell'albero delle decisioni:

1. Azure AD può gestire l'accesso per gli utenti senza basarsi sui componenti di un'istanza locale per la verifica delle password.
2. Azure AD può trasferire l'accesso dell'utente a un provider di autenticazione attendibile, ad esempio AD FS di Microsoft.
3. Se è necessario applicare criteri di sicurezza di Active Directory a livello di utente, ad esempio account scaduto, account disabilitato, password scaduta, account bloccato e ore di accesso per ogni accesso dell'utente, Azure AD richiede che in locale siano presenti alcuni componenti.
4. Funzionalità di accesso non supportate da Azure AD in modo nativo:
   * Accesso tramite smart card o certificati.
   * Accesso tramite server MFA locale.
   * Accesso tramite una soluzione di autenticazione di terze parti.
   * Soluzione di autenticazione locale multisito.
5. Azure AD Identity Protection richiede la sincronizzazione dell'hash delle password indipendentemente dal metodo di accesso scelto, per fornire il report *Utenti con credenziali perse*. Le organizzazioni possono eseguire il failover alla sincronizzazione dell'hash delle password se il metodo di accesso principale ha esito negativo ed è stato configurato prima dell'evento di errore.

> [!NOTE]
> Azure AD Identity Protection richiede licenze di [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="detailed-considerations"></a>Considerazioni dettagliate

### <a name="cloud-authentication-password-hash-synchronization"></a>Autenticazione cloud: Sincronizzazione dell'hash delle password

* **Lavoro richiesto**. La sincronizzazione dell'hash delle password richiede il minimo sforzo a livello di distribuzione, manutenzione e infrastruttura.  Questo livello di lavoro si applica in genere alle organizzazioni che necessitano solo degli utenti per accedere a Microsoft 365, app SaaS e altre risorse basate su Azure AD. Quando abilitata, la sincronizzazione dell'hash delle password rientra nel processo del servizio di sincronizzazione di Azure AD Connect e viene eseguita ogni due minuti.

* **Esperienza utente**. Per migliorare l'esperienza di accesso degli utenti, distribuire Seamless SSO con la sincronizzazione dell'hash delle password. Seamless SSO elimina i prompt non necessari dopo che gli utenti hanno eseguito l'accesso.

* **Scenari avanzati**. Le organizzazioni possono scegliere di usare informazioni tratte dalle identità con i report di Azure AD Identity Protection con Azure AD Premium P2, ad esempio i report sulle credenziali perse. Windows Hello for Business ha [requisiti specifici quando si usa la sincronizzazione dell'hash delle password](/windows/access-protection/hello-for-business/hello-identity-verification). [Azure AD Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) richiede la sincronizzazione dell'hash delle password per eseguire il provisioning degli utenti con le relative credenziali aziendali nel dominio gestito.

    Le organizzazioni che richiedono l'autenticazione a più fattori con sincronizzazione dell'hash delle password devono usare Azure AD Multi-Factor Authentication o [controlli personalizzati di accesso condizionale](../../active-directory/conditional-access/controls.md#custom-controls-preview). Queste organizzazioni non possono usare metodi di autenticazione a più fattori di terze parti o locali che si basano sulla federazione.

> [!NOTE]
> L'accesso condizionale di Azure AD richiede licenze [Azure AD Premium P1](https://azure.microsoft.com/pricing/details/active-directory/).

* **Continuità aziendale**. La sincronizzazione dell'hash delle password con l'autenticazione cloud è altamente disponibile come servizio cloud in grado di consentire la scalabilità a tutti i data center Microsoft. Per avere la certezza che la sincronizzazione dell'hash delle password non resti inattiva per lunghi periodi di tempo, distribuire un secondo server Azure AD Connect in modalità di staging in una configurazione standby.

* **Considerazioni**. Attualmente, la sincronizzazione dell'hash delle password non applica immediatamente le modifiche apportate allo stato degli account locali. In questo caso, l'utente ha accesso alle applicazioni cloud fino a quando lo stato dell'account utente è sincronizzato con Azure AD. Per superare questa limitazione, le organizzazioni possono eseguire un nuovo ciclo di sincronizzazione dopo gli aggiornamenti in blocco agli stati degli account utente locali effettuati dagli amministratori, come ad esempio la disabilitazione di account.

> [!NOTE]
> Gli stati Password scaduta e Account bloccato non sono attualmente sincronizzati in Azure AD con Azure AD Connect. Quando si modifica la password di un utente e si imposta il flag di *cambiamento obbligatorio password all'accesso successivo*, l'hash della password non verrà sincronizzato con Azure AD tramite Azure AD Connect fino a quando l'utente non modificherà la password.

Per la procedura di implementazione, vedere [Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

### <a name="cloud-authentication-pass-through-authentication"></a>Autenticazione cloud: Autenticazione pass-through  

* **Lavoro richiesto**. Per l'autenticazione pass-through sono necessari uno o più agenti leggeri (se ne consigliano tre) installati sui server esistenti. Questi agenti devono avere accesso ad Active Directory Domain Services locale, inclusi i controller di dominio AD locali. Richiedono inoltre l'accesso in uscita a Internet e l'accesso ai controller di dominio. Per questo motivo la distribuzione degli agenti in una rete perimetrale non è supportata.

    L'autenticazione pass-through richiede infatti un accesso di rete senza limitazioni ai controller di dominio. Tutto il traffico di rete è crittografato e limitato alle richieste di autenticazione. Per altre informazioni su questo processo, vedere l'[approfondimento sulla sicurezza](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) per l'autenticazione pass-through.

* **Esperienza utente**. Per migliorare l'esperienza di accesso degli utenti, distribuire Seamless SSO con l'autenticazione pass-through. Seamless SSO elimina i prompt non necessari s dopo che gli utenti hanno effettuato l'accesso.

* **Scenari avanzati**. L'autenticazione pass-through applica i criteri di account locali al momento dell'accesso. Ad esempio, l'accesso viene negato quando lo stato dell'account di un utente locale è disabilitato o bloccato oppure quando la [password scade](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) o il tentativo di accesso non rientra negli orari di accesso consentiti per l'utente.

    Le organizzazioni che richiedono l'autenticazione a più fattori con l'autenticazione pass-through devono usare Azure AD Multi-Factor Authentication (multi-factor authentication) o [controlli personalizzati di accesso condizionale](../../active-directory/conditional-access/controls.md#custom-controls-preview). Queste organizzazioni non possono usare un metodo di autenticazione a più fattori di terze parti o locale che si basa sulla federazione. Le funzionalità avanzate richiedono che la sincronizzazione dell'hash delle password venga implementata indipendentemente dalla scelta dell'autenticazione pass-through. Un esempio è il report Credenziali perse di Identity Protection.

* **Continuità aziendale**. È consigliabile distribuire due agenti di autenticazione pass-through aggiuntivi, oltre al primo agente sul server Azure AD Connect. Questa distribuzione aggiuntiva assicura una disponibilità elevata delle richieste di autenticazione. Quando si dispone di tre agenti di distribuzione, un agente può avere comunque esito negativo quando un altro agente è inattivo per manutenzione.

    L'implementazione della sincronizzazione dell'hash delle password in aggiunta all'autenticazione pass-through offre anche un altro vantaggio. Funge infatti da metodo di autenticazione di backup quando il metodo principale non è più disponibile.

* **Considerazioni**. È possibile usare la sincronizzazione dell'hash delle password come metodo di autenticazione di backup per l'autenticazione pass-through, quando gli agenti non possono convalidare le credenziali di un utente per un errore significativo locale. Il failover alla sincronizzazione dell'hash delle password non avviene automaticamente ed è necessario usare Azure AD Connect per cambiare metodo di accesso manualmente.

    Per altre considerazioni sull'autenticazione pass-through, incluso il supporto ID alternativo, vedere le [domande frequenti](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Per la procedura di distribuzione, fare riferimento all'[implementazione dell'autenticazione pass-through](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Autenticazione federata

* **Lavoro richiesto**. Un sistema di autenticazione federata si affida a un sistema esterno attendibile per autenticare gli utenti. Alcune aziende scelgono di riutilizzare i propri investimenti in sistemi federati esistenti con la soluzione ibrida di gestione delle identità di Azure AD. La manutenzione e la gestione del sistema federato non rientra nella sfera di controllo di Azure Active Directory. È compito dell'organizzazione assicurarsi che il sistema federato usato venga implementato in modo sicuro e sia in grado di gestire il carico di autenticazione.

* **Esperienza utente**. L'esperienza utente di autenticazione federata dipende dall'implementazione delle funzionalità, dalla topologia e dalla configurazione della farm federativa. Alcune organizzazioni hanno bisogno di questa flessibilità per adattare e configurare l'accesso alla farm federativa in base alle proprie esigenze di sicurezza. Ad esempio, è possibile configurare gli utenti e i dispositivi connessi internamente perché effettuino l'accesso automaticamente, senza richiedere l'immissione di credenziali. Questa configurazione funziona in quanto gli utenti hanno già effettuato l'accesso dai propri dispositivi. Se necessario, alcune funzionalità di sicurezza avanzate possono complicare la procedura di accesso degli utenti.

* **Scenari avanzati**. Una soluzione di autenticazione federata è necessaria per i clienti con requisiti di autenticazione non supportati da Azure AD in modo nativo. Vedere le Informazioni dettagliate utili per la [scelta dell'opzione di accesso più adatta](/archive/blogs/samueld/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365). Considerare i requisiti comuni seguenti:

  * Autenticazione che richiede smart card o certificati.
  * Server MFA locali o provider di procedure di autenticazione a più fattori di terze parti che richiedono un provider di identità federata.
  * Autenticazione tramite soluzioni di autenticazione di terze parti. Vedere l'[Elenco di compatibilità di federazione di Azure AD](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Accesso che richiede un sAMAccountName, ad esempio DOMINIO\nome utente, anziché un nome dell'entità utente (UPN), ad esempio user@domain.com.

* **Continuità aziendale**. I sistemi federati richiedono in genere un array di server con bilanciamento del carico, noto come farm. Questa farm è configurata in una topologia di rete interna e perimetrale per garantire la disponibilità elevata per le richieste di autenticazione.

    Implementare la sincronizzazione dell'hash delle password insieme all'autenticazione federata come metodo di autenticazione di backup quando il metodo di autenticazione principale non è più disponibile, ad esempio quando i server locali non sono disponibili. Alcune organizzazioni di grandi dimensioni richiedono una soluzione di federazione per supportare più punti di ingresso Internet configurati con geo-DNS per richieste di autenticazione a bassa latenza.

* **Considerazioni**. I sistemi federati richiedono in genere un investimento più significativo in infrastruttura locale. La maggior parte delle organizzazioni sceglie questa opzione se ha già investito in un sistema di federazione locale e se l'uso di un singolo provider di identità è un requisito aziendale imprescindibile. La federazione prevede modalità d'uso e risoluzione dei problemi più complesse rispetto alle soluzioni di autenticazione cloud.

Per un dominio non instradabile che non può essere verificato in Azure AD, è necessaria una configurazione aggiuntiva per implementare l'accesso tramite ID utente. Questo requisito è noto come supporto per l'ID di accesso alternativo. Per requisiti e limitazioni, vedere [Configurazione dell'ID di accesso alternativo](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id). Se si sceglie di usare un provider di autenticazione a più fattori di terze parti con la federazione, verificare che il provider supporti WS-Trust per consentire l'aggiunta dei dispositivi ad Azure AD.

Per la procedura di distribuzione, vedere [Distribuzione di server federativi](/windows-server/identity/ad-fs/deployment/deploying-federation-servers).

> [!NOTE]
> Quando si implementa la soluzione ibrida di gestione delle identità di Azure AD, è necessario implementare una delle topologie supportate di Azure AD Connect. Altre informazioni sulle configurazioni supportate o meno sono disponibili in [Topologie per Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Diagrammi dell'architettura

I diagrammi seguenti definiscono i componenti dell'architettura generale necessari per ogni metodo di autenticazione che è possibile usare con la soluzione ibrida di gestione delle identità di Azure AD. Questi diagrammi forniscono una panoramica utile per confrontare le differenze tra le soluzioni.

* Semplicità della soluzione di sincronizzazione dell'hash delle password:

    ![Soluzione ibrida di gestione delle identità di Azure AD con sincronizzazione dell'hash delle password](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Requisiti dell'autenticazione pass-through a livello di agente, con due agenti per la ridondanza:

    ![Soluzione ibrida di gestione delle identità di Azure AD con autenticazione pass-through](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Componenti necessari per la federazione nella rete interna e perimetrale dell'organizzazione:

    ![Soluzione ibrida di gestione delle identità di Azure AD con autenticazione federata](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Confronto dei metodi

|Considerazioni|Sincronizzazione dell'hash delle password + Seamless SSO|Autenticazione pass-through + Seamless SSO|Federazione con ADFS|
|:-----|:-----|:-----|:-----|
|Dove si verifica l'autenticazione?|Nel cloud|Nel cloud dopo la verifica della password di protezione con l'agente di autenticazione locale|Locale|
|Quali sono i requisiti del server locale oltre il sistema di provisioning Azure AD Connect?|nessuno|Un server per ogni agente di autenticazione aggiuntivo|Due o più server AD FS<br><br>Due o più server WAP nella rete perimetrale|
|Quali sono i requisiti di rete e Internet locali oltre al sistema di provisioning?|nessuno|[Accesso a Internet in uscita](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) dai server in cui sono in esecuzione gli agenti di autenticazione|[Accesso Internet in ingresso](/windows-server/identity/ad-fs/overview/ad-fs-requirements) ai server WAP nelle reti perimetrali<br><br>Accesso di rete in ingresso ai server AD FS dai server WAP nelle reti perimetrali<br><br>Bilanciamento del carico di rete|
|Esiste un requisito per il certificato TLS/SSL?|No|No|Sì|
|Esiste una soluzione di monitoraggio dello stato?|Facoltativo|Stato agente fornito dall'[interfaccia di amministrazione di Azure Active Directory](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Gli utenti ottengono l'accesso Single Sign-On alle risorse cloud dai dispositivi aggiunti al dominio all'interno della rete aziendale?|Sì, con l'[accesso Single Sign-On facile](../../active-directory/hybrid/how-to-connect-sso.md)|Sì, con l'[accesso Single Sign-On facile](../../active-directory/hybrid/how-to-connect-sso.md)|Sì|
|Quali tipi di accesso sono supportati?|UserPrincipalName + Password<br><br>Autenticazione integrata di Windows con [accesso SSO facile](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[ID di accesso alternativo](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + Password<br><br>Autenticazione integrata di Windows con [accesso SSO facile](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[ID di accesso alternativo](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + Password<br><br>sAMAccountName + Password<br><br>Autenticazione integrata di Windows<br><br>[Autenticazione con certificato e smart card](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[ID di accesso alternativo](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Windows Hello for Business è supportato?|[Modello di attendibilità chiavi](/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Modello di attendibilità chiavi](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Richiede il livello di funzionalità del dominio di Windows Server 2016*|[Modello di attendibilità chiavi](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modello di attendibilità certificati](/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Quali sono le opzioni di autenticazione a più fattori?|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Controlli personalizzati con accesso condizionale*](../../active-directory/conditional-access/controls.md)|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Controlli personalizzati con accesso condizionale*](../../active-directory/conditional-access/controls.md)|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Server di Azure MFA](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[MFA di terze parti](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Controlli personalizzati con accesso condizionale*](../../active-directory/conditional-access/controls.md)|
|Quali stati dell'account utente sono supportati?|Account disabilitati<br>(fino a 30 minuti di ritardo)|Account disabilitati<br><br>Account bloccato<br><br>Account scaduto<br><br>Password scaduta<br><br>Orari di accesso|Account disabilitati<br><br>Account bloccato<br><br>Account scaduto<br><br>Password scaduta<br><br>Orari di accesso|
|Quali sono le opzioni di accesso condizionale?|[Accesso condizionale di Azure AD con Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Accesso condizionale di Azure AD con Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Accesso condizionale di Azure AD con Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[Regole di attestazione per AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Il blocco dei protocolli legacy è supportato?|[Sì](../../active-directory/conditional-access/overview.md)|[Sì](../../active-directory/conditional-access/overview.md)|[Sì](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|È possibile personalizzare il logo, l'immagine e la descrizione nelle pagine di accesso?|[Sì, con Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Sì, con Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Sì](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Quali scenari avanzati sono supportati?|[Smart Password Lockout](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Report Credenziali perse con Azure AD Premium P2](../identity-protection/overview-identity-protection.md)|[Smart Password Lockout](../../active-directory/authentication/howto-password-smart-lockout.md)|Sistema di autenticazione multisito a bassa latenza<br><br>[Blocco della Extranet di AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integrazione con i sistemi di gestione delle identità di terze parti](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> I controlli personalizzati nell'accesso condizionale di Azure AD attualmente non supportano la registrazione dispositivo.

## <a name="recommendations"></a>Consigli
Il sistema di gestione delle identità garantisce che gli utenti abbiano accesso alle app cloud e line-of-business trasferite e rese disponibili nel cloud. Per mantenere produttivi gli utenti autorizzati e tenere i malintenzionati alla larga dai dati sensibili dell'organizzazione, l'autenticazione controlla l'accesso alle app.

Usare o abilitare la sincronizzazione dell'hash delle password indipendentemente dal metodo di autenticazione scelto, per i motivi seguenti:

1. **Disponibilità elevata e ripristino di emergenza**. L'autenticazione pass-through e federata si basano sull'infrastruttura locale. Per l'autenticazione pass-through, il footprint locale include l'hardware del server e le funzionalità di rete richiesti dagli agenti di autenticazione pass-through. Per la federazione, il footprint locale è ancora maggiore. Richiede infatti che i server nella rete perimetrale inoltrino tramite proxy le richieste di autenticazione ai server federativi interni.

    Per evitare singoli punti di guasto, distribuire server ridondanti. Le richieste di autenticazione verranno sempre soddisfatte anche in caso di guasto di un componente. Sia l'autenticazione pass-through che la federazione fanno anche affidamento sui controller di dominio, anch'essi soggetti a guasti, per rispondere alle richieste di autenticazione. Molti di questi componenti hanno bisogno di manutenzione per conservare uno stato di integrità. Le interruzioni si verificano con maggiore probabilità quando la manutenzione non viene pianificata e implementata correttamente. Eventuali interruzioni possono essere evitate usando la sincronizzazione dell'hash delle password, perché il servizio di autenticazione cloud di Microsoft Azure Active Directory è scalabile a livello globale ed è sempre disponibile.

2. **Sopravvivenza alle interruzioni locali**.  Le conseguenze di un'interruzione locale causata da attacchi informatici o emergenze possono essere significative e spaziano da un danno alla reputazione del marchio alla paralisi delle organizzazioni che non sono in grado di gestire l'attacco. Recentemente, molte organizzazioni sono state vittime di attacchi di malware, inclusi ransomware mirati, che hanno reso inattivi i server locali. Nel fornire il proprio supporto ai clienti che devono affrontare questi tipi di attacchi, Microsoft ha notato due categorie di organizzazioni:

   * Le organizzazioni che in precedenza avevano attivato la sincronizzazione dell'hash delle password oltre all'autenticazione federata o pass-through hanno cambiato metodo di autenticazione principale in modo da usare tale sincronizzazione. Sono tornati online in poche ore. Utilizzando l'accesso alla posta elettronica tramite Microsoft 365, hanno lavorato per risolvere i problemi e accedere ad altri carichi di lavoro basati sul cloud.

   * Le organizzazioni che in precedenza non avevano abilitato la sincronizzazione dell'hash delle password sono dovute ricorrere a sistemi di posta elettronica consumer esterni non attendibili per le comunicazioni per risolvere i problemi. In questi casi, hanno impiegato settimane per ripristinare l'infrastruttura di identità locale e solo successivamente gli utenti sono stati in grado di accedere di nuovo alle app basate sul cloud.

3. **Identity Protection** uno dei modi migliori per proteggere gli utenti nel cloud è Azure AD Identity Protection con Azure AD Premium P2. Microsoft esegue continuamente l'analisi di Internet alla ricerca degli elenchi di nomi utente e password venduti e resi disponibili sul dark web dai malintenzionati. Azure AD è in grado di usare queste informazioni per verificare se uno qualsiasi dei nomi utente e delle password dell'organizzazione sono compromessi. È pertanto essenziale abilitare la sincronizzazione dell'hash delle password indipendentemente dal metodo di autenticazione usato, che si tratti di autenticazione federata o pass-through. Le credenziali perse vengono presentate in forma di report. Usare queste informazioni per impedire o imporre agli utenti di cambiare la password quando cercano di accedere con una password persa.

## <a name="conclusion"></a>Conclusioni

Questo articolo descrive diverse opzioni di autenticazione che le organizzazioni possono configurare e implementare per supportare l'accesso alle app cloud. Per soddisfare esigenze aziendali, tecniche e di sicurezza diverse, le organizzazioni possono scegliere tra sincronizzazione dell'hash delle password, autenticazione pass-through e federazione.

Prendere in considerazione ogni metodo di autenticazione. Il lavoro richiesto per distribuire la soluzione e l'esperienza utente della procedura di accesso soddisfano i requisiti aziendali? Valutare se l'organizzazione necessita degli scenari avanzati e delle funzionalità di continuità aziendale disponibili con ogni metodo di autenticazione. Infine, valutare le considerazioni relative a ogni metodo di autenticazione. Ne esiste almeno uno che impedisce di implementare la soluzione scelta?

## <a name="next-steps"></a>Passaggi successivi

Oggigiorno, le minacce sono presenti 24 ore al giorno e provengono da ovunque. L'implementazione del metodo di autenticazione corretto consente di attenuare i rischi di sicurezza e proteggere le identità.

Ciò è possibile tramite l'[Introduzione](../fundamentals/active-directory-whatis.md) ad Azure Active Directory e la distribuzione di una soluzione di autenticazione appropriata per l'organizzazione.

Se si intende eseguire la migrazione dall'autenticazione federata all'autenticazione cloud, sono disponibili altre informazioni sulla [modifica del metodo di accesso](../../active-directory/hybrid/plan-connect-user-signin.md). Per semplificare la pianificazione e l'implementazione della migrazione, usare tali [piani di distribuzione del progetto](../fundamentals/active-directory-deployment-plans.md) o prendere in considerazione l'uso della nuova funzionalità di [implementazione a fasi](../../active-directory/hybrid/how-to-connect-staged-rollout.md) per eseguire la migrazione degli utenti federati all'uso dell'autenticazione cloud in un approccio graduale.