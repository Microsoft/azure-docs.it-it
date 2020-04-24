---
title: Proteggi la tua infrastruttura di identità Azure AD
titleSuffix: Azure Active Directory
description: Questo documento descrive un elenco di azioni importanti che gli amministratori devono implementare per proteggere meglio l'organizzazione con le funzionalità di Azure AD
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: e0db8edfdfa380697a1d8d7e262a7a84da2fb7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565537"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Cinque passaggi per proteggere l'infrastruttura di identità

Chi legge questo documento è consapevole dell'importanza della sicurezza e sicuramente è già responsabile della sicurezza dell'organizzazione. Per convincere altri dell'importanza della sicurezza, invitarli a leggere l'ultimo [Microsoft Security Intelligence Report](https://go.microsoft.com/fwlink/p/?linkid=2073747).

Questo documento consente di migliorare l'approccio alla sicurezza con le funzionalità di Azure Active Directory usando un elenco di controllo di cinque passaggi per difendere l'organizzazione da attacchi informatici.

Questo elenco di controllo permette di distribuire rapidamente le azioni critiche consigliate per proteggere subito l'organizzazione spiegando in che modo:

* Rafforzare le credenziali.
* Ridurre la superficie di attacco.
* Automatizzare la risposta alle minacce.
* Usare l'Intelligence per il cloud.
* Abilitare la self-service per l'utente finale.

Assicurarsi di tenere traccia delle funzionalità e dei passaggi completati durante la lettura di questo elenco di controllo.

> [!NOTE]
> Molti consigli forniti in questo documento si applicano solo alle applicazioni, configurate per l'uso di Azure Active Directory come provider di identità. La configurazione di app per il Single Sign-On garantisce i vantaggi associati a criteri per le credenziali, rilevamento delle minacce, controllo, registrazione e altre funzionalità aggiunte a queste applicazioni. L'[accesso Single Sign-On tramite Azure Active Directory](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md) costituisce l'elemento su cui si basano questi consigli.

I consigli riportati in questo documento sono allineati con [Identity Secure Score](../../active-directory/fundamentals/identity-secure-score.md), un sistema automatizzato per la valutazione della configurazione di sicurezza dell'identità del tenant di Azure AD. Le organizzazioni possono usare la pagina Identity Secure score nel portale Azure AD per individuare le lacune nella configurazione di sicurezza corrente per assicurarsi che seguano le [procedure](identity-management-best-practices.md) consigliate Microsoft correnti per la sicurezza. L'implementazione di ogni consiglio nella pagina di Secure Score aumenterà il punteggio e consentirà di tenere traccia dello stato di avanzamento. Inoltre, sarà possibile confrontare la propria implementazione con altri settori oppure organizzazioni di dimensioni simili.

![Identity Secure Score](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Molte delle funzionalità descritte in questo articolo richiedono una sottoscrizione di Azure AD Premium, mentre alcune sono gratuite. Per ulteriori informazioni, consultare l'elenco di controllo [Azure Active Directory prezzi](https://azure.microsoft.com/pricing/details/active-directory/) e [distribuzione Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2) .

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Prima di iniziare: proteggere gli account con privilegi mediante MFA

Prima di iniziare questo elenco di controllo, verificare che l'account non venga compromesso durante la lettura. È innanzitutto necessario proteggere gli account con privilegi.

Gli utenti malintenzionati che ottengono il controllo di account con privilegi possono causare enormi danni, pertanto è fondamentale proteggere questi account prima di tutto. Abilitare e richiedere [Azure multi-factor authentication](../../active-directory/authentication/multi-factor-authentication.md) (multi-factor authentication) per tutti gli amministratori dell'organizzazione usando [Azure ad impostazioni predefinite di sicurezza](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) o [l'accesso condizionale](../../active-directory/conditional-access/plan-conditional-access.md). Implementare MFA, se non è stato già fatto. È davvero importante.

Tutto pronto? Iniziamo con l'elenco di controllo.

## <a name="step-1---strengthen-your-credentials"></a>Passaggio 1 - Rafforzare le credenziali

La maggior parte delle violazioni della sicurezza delle organizzazioni ha origine con un account compromesso mediante uno di alcuni metodi, tra cui un attacco password spraying, riproduzione della violazione o phishing. Altre informazioni su questi attacchi sono disponibili in questo video (45 minuti):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Assicurarsi che l'organizzazione usi l'autenticazione avanzata

Poiché le password vengono individuate, sottoposte a phishing, sottratte con malware o riusate con una certa frequenza, è fondamentale supportarle con un tipo di credenziali avanzate: altre informazioni su [Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md).

Per abilitare facilmente il livello di base della sicurezza delle identità, è possibile usare l'abilitazione con un solo clic con [Azure ad impostazioni predefinite di sicurezza](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Le impostazioni predefinite di sicurezza applicano l'autenticazione a più fattori di Azure per tutti gli utenti in un tenant e bloccano gli accessi dai protocolli legacy a livello di tenant.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Iniziare a vietare le password attaccate più spesso e disattivare le regole di complessità e scadenza tradizionali.

Molte organizzazioni usano le regole tradizionali di complessità (che richiedono caratteri speciali, numeri, lettere maiuscole e lettere minuscole) e di scadenza delle password. Le [ricerche di Microsoft](https://aka.ms/passwordguidance) hanno dimostrato che questi criteri portano gli utenti a scegliere password più facili da indovinare.

La funzionalità di [divieto dinamico delle password](../../active-directory/authentication/concept-password-ban-bad.md) di Azure AD usa il comportamento degli utenti malintenzionati per impedire l'impostazione di password facilmente individuabili. Questa funzionalità è sempre attiva per gli utenti del cloud, ma è ora disponibile anche per le organizzazioni ibride che distribuiscono la [protezione tramite password di Azure AD per Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). La protezione tramite password di Azure AD impedisce agli utenti di scegliere queste password comuni e può essere estesa per bloccare password che contengono parole chiave personalizzate specificate. Ad esempio, è possibile impedire agli utenti di scegliere password che contengono nomi di prodotti della società o di una squadra sportiva locale.

Microsoft consiglia di adottare i criteri moderni di impostazione delle password seguenti, basati sulle [linee guida di NIST](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Richiedere password di almeno 8 caratteri. Password più lunghe non sono necessariamente migliori, in quanto gli utenti si trovano a scegliere password prevedibili, a salvarle in file o a prenderne nota.
2. Disabilitare le regole di scadenza, che spingono gli utenti a indovinare facilmente le password, ad esempio **Spring2019.**
3. Disabilitare i requisiti di composizione di caratteri e impedire agli utenti di scegliere password comunemente sottoposte ad attacchi, in quanto potrebbero scegliere sostituzioni di caratteri prevedibili.

È possibile usare [PowerShell per impedire la scadenza delle password](../../active-directory/authentication/concept-sspr-policy.md) per gli utenti, se si creano direttamente identità in Azure AD. Le organizzazioni ibride devono implementare questi criteri usando le [impostazioni dei criteri del gruppo di dominio](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) o [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Proteggersi da credenziali perse e aggiungere resilienza da interruzioni

Se l'organizzazione usa una soluzione di identità ibrida con la federazione o l'autenticazione pass-through, è consigliabile abilitare la sincronizzazione dell'hash delle password per i due motivi seguenti:

* Il report [Utenti con credenziali perse](../../active-directory/reports-monitoring/concept-risk-events.md) nella gestione di Azure AD avvisa l'utente di coppie di nome utente e password esposte nel Dark Web. Una quantità incredibile di password viene persa tramite phishing, malware e riuso delle password nei siti di terze parti che verranno sottoposti a violazioni in un secondo momento. Microsoft individua molte di queste credenziali perse e, in questo report, indica se corrispondono alle credenziali di un'organizzazione, ma solo se l'utente [abilita la sincronizzazione dell'hash delle password](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).
* In caso di interruzione locale (ad esempio, in un attacco ransomware) è possibile passare all'uso dell' [autenticazione cloud con la sincronizzazione dell'hash delle password](choose-ad-authn.md). Questo metodo di autenticazione del backup consentirà di continuare ad accedere alle app configurate per l'autenticazione con Azure Active Directory, incluso Office 365. In questo caso, non è necessario che il personale IT ricorra agli account di posta elettronica personali per condividere i dati fino a quando l'interruzione locale non viene risolta.

Altre informazioni sul funzionamento della [sincronizzazione dell'hash delle password](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

> [!NOTE]
> Se si abilita la sincronizzazione dell'hash delle password e si usa Azure AD Domain Services, anche gli hash Kerberos (AES 256) e, facoltativamente, gli hash NTLM (RC4, senza salting) verranno crittografati e sincronizzati con Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementare il blocco intelligente della Extranet di AD FS

Le organizzazioni che configurano applicazioni per l'autenticazione diretta con Azure AD usufruiscono del [blocco intelligente di Azure AD](../../active-directory/authentication/concept-sspr-howitworks.md). Se si usa AD FS in Windows Server 2012 R2, implementare la [protezione di blocco della Extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection) di AD FS. Se si usa AD FS in Windows Server 2016, implementare il [blocco intelligente della Extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Il blocco intelligente della Extranet di AD FS protegge dagli attacchi di forza bruta che mirano ad AD FS impedendo al tempo stesso il blocco degli utenti in Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Sfruttare i vantaggi di credenziali sicure e più facili da usare

Con [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) è possibile sostituire le password con l'autenticazione avanzata a due fattori nei PC e nei dispositivi mobili. Questo processo di autenticazione è costituito da un nuovo tipo di credenziale utente, che è associata in modo sicuro a un dispositivo e usa un sensore biometrico o un PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Passaggio 2 - Ridurre la superficie di attacco

Poiché le password vengono frequentemente compromesse, è fondamentale per l'organizzazione ridurre al minimo la superficie di attacco. Eliminare l'uso di protocolli precedenti e meno sicuri, limitare l'accesso di punti di ingresso ed esercitare un controllo più significativo di accesso amministrativo alle risorse consentono di ridurre la superficie di attacco.

### <a name="block-legacy-authentication"></a>Bloccare l'autenticazione legacy

Le app che usano propri metodi legacy per l'autenticazione con Azure AD e l'accesso ai dati aziendali pongono un altro rischio per le organizzazioni. Esempi di app che usano l'autenticazione legacy sono i client POP3, IMAP4 o SMTP. Le app con autenticazione legacy eseguono l'autenticazione per conto dell'utente e impediscono ad Azure AD di eseguire valutazioni avanzate sulla sicurezza. L'autenticazione moderna alternativa ridurrà il rischio per la sicurezza, perché supporta l'autenticazione a più fattori e l'accesso condizionale. È consigliabile implementare le tre azioni seguenti:

1. Bloccare l'[autenticazione legacy se si usa AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Configurare [SharePoint Online ed Exchange Online per l'uso dell'autenticazione moderna](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md).
3. Se si dispone di Azure AD Premium, utilizzare i [criteri di accesso condizionale](../../active-directory/conditional-access/overview.md) per bloccare l'autenticazione legacy, altrimenti utilizzare [Azure ad impostazioni predefinite di sicurezza](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Bloccare punti di ingresso di autenticazione non validi

Con un approccio di presunzione di violazione è consigliabile ridurre l'impatto di credenziali utente compromesse quando si verifica questa condizione. Per ogni app nell'ambiente considerare i casi d'uso validi: quali gruppi, quali reti, quali dispositivi e altri elementi sono autorizzati, quindi bloccare il resto. Con [l'accesso condizionale Azure ad](../../active-directory/conditional-access/overview.md), è possibile controllare il modo in cui gli utenti autorizzati accedono alle app e alle risorse in base a condizioni specifiche definite dall'utente.

### <a name="restrict-user-consent-operations"></a>Limitare le operazioni di consenso dell'utente

È importante comprendere le diverse esperienze di [consenso dell'applicazione Azure ad](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience), i [tipi di autorizzazioni e il consenso](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)e le loro implicazioni sul comportamento di sicurezza dell'organizzazione. Per impostazione predefinita, tutti gli utenti in Azure AD possono concedere alle applicazioni che sfruttano la piattaforma di identità Microsoft di accedere ai dati dell'organizzazione. Sebbene gli utenti possano acconsentire autonomamente, consentono agli utenti di acquisire facilmente applicazioni utili che si integrano con Microsoft 365, Azure e altri servizi, ma possono rappresentare un rischio se non vengono usate e monitorate con attenzione.

Microsoft consiglia di [disabilitare le future operazioni di consenso utente](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access#i-want-to-disable-all-future-user-consent-operations-to-any-application) per ridurre la superficie di attacco e mitigare questo rischio. Se il consenso dell'utente finale è disabilitato, le concessioni di consenso precedenti verranno comunque rispettate, ma tutte le future operazioni di consenso dovranno essere eseguite da un amministratore. Il consenso dell'amministratore può essere richiesto dagli utenti tramite un [flusso di lavoro di richiesta di consenso dell'amministratore](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) integrato o tramite processi di supporto personalizzati. Prima di disabilitare il consenso dell'utente finale, usare le [raccomandazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-consent-requests) per pianificare questa modifica nell'organizzazione. Per le applicazioni a cui si vuole consentire l'accesso a tutti gli utenti, è consigliabile [concedere il consenso per conto di tutti gli utenti](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent), assicurandosi che gli utenti che non hanno ancora acconsentito singolarmente possano accedere all'app. Se non si vuole che queste applicazioni siano disponibili per tutti gli utenti in tutti gli scenari, usare l' [assegnazione dell'applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) e [l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) per limitare l'accesso degli utenti alle app.

Assicurarsi che gli utenti possano richiedere l'approvazione dell'amministratore per le nuove applicazioni per ridurre l'attrito degli utenti, ridurre al minimo il volume di supporto e impedire agli utenti di iscriversi alle applicazioni con credenziali non Azure AD. Una volta regolate le operazioni di consenso, gli amministratori devono controllare regolarmente l'app e le autorizzazioni concesse.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementare Azure AD Privileged Identity Management

Un altro impatto determinato dalla presunzione di violazione è la necessità di ridurre al minimo le probabilità che un account compromesso possa essere usato con un ruolo con privilegi.

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) consente di ridurre al minimo gli account con privilegi grazie alla possibilità di:

* Identificare e gestire gli utenti assegnati a ruoli amministrativi.
* Capire quali ruoli con privilegi eccessivi o inutilizzati è opportuno rimuovere.
* Definire regole per verificare che i ruoli con privilegi siano protetti con autenticazione a più fattori.
* Definire regole per verificare che i ruoli con privilegi vengano concessi solo per il tempo sufficiente a completare l'attività con privilegi.

Abilitare Azure AD PIM, visualizzare gli utenti a cui sono assegnati ruoli amministrativi e rimuovere gli account non necessari in questi ruoli. Per gli utenti con privilegi rimanenti, spostarli da permanenti a idonei. Infine, definire criteri appropriati per verificare che possano ottenere accesso a questi ruoli con privilegi, in modo sicuro e con il controllo modifiche necessario.

Come parte della distribuzione del processo di account con privilegi, seguire la [procedura consigliata per creare almeno due account di emergenza](../../active-directory/users-groups-roles/directory-admin-roles-secure.md) per assicurarsi di avere ancora accesso a Azure ad se ci si blocca.

## <a name="step-3---automate-threat-response"></a>Passaggio 3 - Automatizzare la risposta alle minacce

Azure Active Directory offre molte funzionalità che intercettano automaticamente gli attacchi, per rimuovere la latenza tra rilevamento e risposta. Riducendo il tempo usato dai criminali per insinuarsi nell'ambiente, è possibile ridurre anche costi e rischi. Ecco i passaggi concreti che è possibile attuare.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementare criteri di sicurezza per il rischio utente mediante Azure AD Identity Protection

Rischio utente indica la probabilità che l'identità dell'utente sia stata compromessa e calcolata in base ai [rilevamenti dei rischi utente](../../active-directory/identity-protection/overview.md) associati all'identità di un utente. I criteri di rischio utente sono criteri di accesso condizionale che valutano il livello di rischio per un utente o un gruppo specifico. Basato su un livello di rischio basso, medio o elevato, un criterio può essere configurato per bloccare l'accesso o richiedere una modifica sicura della password mediante autenticazione a più fattori. Microsoft consiglia di richiedere una modifica sicura della password per gli utenti con rischio alto.

![Utenti contrassegnati per il rischio](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementare criteri di rischio di accesso mediante Azure AD Identity Protection

Il rischio di accesso è la probabilità che un utente diverso dal proprietario dell'account tenti di eseguire l'accesso usando l'identità. Un [criterio di rischio](../../active-directory/identity-protection/overview.md) di accesso è un criterio di accesso condizionale che valuta il livello di rischio per un utente o un gruppo specifico. Basato sul livello di rischio (alto/medio/basso), un criterio può essere configurato per bloccare l'accesso o forzare l'autenticazione a più fattori. Verificare di forzare l'autenticazione a più fattori nei rischi di accesso a partire dal livello medio.

![Accedere da indirizzi IP anonimi](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Passaggio 4: usare l'Intelligence per il cloud

Il controllo e la registrazione di eventi relativi alla sicurezza e di avvisi correlati sono componenti essenziali di una strategia di protezione efficace. I report e i log di sicurezza offrono un record elettronico delle attività sospette e aiutano a rilevare criteri che possono indicare tentativi esterni di penetrazione nella rete e attacchi interni. È possibile utilizzare il controllo per monitorare le attività degli utenti, documentare la conformità alle normative, eseguire analisi forensi e altro ancora. Gli avvisi forniscono notifiche di eventi di sicurezza.

### <a name="monitor-azure-ad"></a>Monitorare Azure AD

Le funzionalità e i servizi di Microsoft Azure offrono opzioni di controllo e registrazione di sicurezza configurabili che consentono di identificare e correggere i gap nei meccanismi e nei criteri di sicurezza per evitare violazioni. È possibile usare le funzionalità di [registrazione e controllo di Azure](log-audit.md) e i [report delle attività di controllo nel portale di Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorare Azure AD Connect Health in ambienti ibridi

Il [monitoraggio di AD FS con Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) offre informazioni approfondite sui potenziali problemi e conferisce maggiore visibilità sugli attacchi nell'infrastruttura AD FS. Azure AD Connect Health fornisce avvisi con dettagli, procedure di risoluzione e collegamenti alla documentazione correlata, analisi di utilizzo per varie metriche correlate al traffico di autenticazione, monitoraggio e report delle prestazioni.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorare eventi di Azure AD Identity Protection

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) è uno strumento di notifica, monitoraggio e creazione di report che consente di rilevare le potenziali vulnerabilità che interessano le identità dell'organizzazione. Rileva i rilevamenti dei rischi, ad esempio le credenziali perse, il viaggio impossibile e gli accessi da dispositivi infetti, indirizzi IP anonimi, indirizzi IP associati all'attività sospetta e posizioni sconosciute. Abilitare avvisi di notifica per ricevere un messaggio di posta elettronica degli utenti a rischio e/o un messaggio di posta elettronica di riepilogo settimanale.

Azure AD Identity Protection offre due importanti report da monitorare quotidianamente:
1. Report sugli accessi a rischio, che evidenzieranno le attività di accesso dell'utente che è necessario analizzare: il legittimo proprietario può non aver eseguito l'accesso.
2. Report sugli utenti a rischio, che evidenzieranno gli account utente che potrebbero essere stati compromessi: ad esempio, perdita di credenziali o accesso dell'utente da posizioni differenti che causa un evento di tipo comunicazione impossibile.

![Utenti contrassegnati per il rischio](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>App di controllo e autorizzazioni per il consenso

È possibile indurre gli utenti a passare a un sito Web compromesso o a app che otterranno l'accesso alle informazioni sul profilo e ai dati utente, ad esempio la posta elettronica. Un attore malintenzionato può usare le autorizzazioni per il consenso ricevute per crittografare il contenuto della cassetta postale e richiedere un riscatto per riottenere i dati della cassetta postale. Gli [amministratori devono esaminare e controllare](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) le autorizzazioni fornite dagli utenti o disabilitare la possibilità per gli utenti di fornire il consenso per impostazione predefinita.

Oltre a controllare le autorizzazioni fornite dagli utenti, è possibile [individuare applicazioni OAuth rischiose o indesiderate](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth) in ambienti Premium.

## <a name="step-5---enable-end-user-self-service"></a>Passaggio 5: abilitare la self-service dell'utente finale

Per quanto possibile è opportuno bilanciare sicurezza e produttività. In linea con lo stesso approccio mentale con cui un utente predispone una base per la sicurezza a lungo termine, è possibile rimuovere attrito dall'organizzazione potenziando gli utenti, ma rimanendo sempre vigili.

### <a name="implement-self-service-password-reset"></a>Implementare la reimpostazione self-service delle password

La [reimpostazione della password self-service di Azure ad (SSPR)](../../active-directory/authentication/quickstart-sspr.md) offre agli amministratori IT un modo semplice per consentire agli utenti di reimpostare o sbloccare le password o gli account senza help desk o l'intervento dell'amministratore. Il sistema include report dettagliati che consentono di tenere traccia del momento in cui gli utenti hanno reimpostato le proprie password, insieme alle notifiche per segnalare un abuso o abusi.

### <a name="implement-self-service-group-and-application-access"></a>Implementare l'accesso alle applicazioni e ai gruppi self-service

Azure AD offre agli amministratori non amministratori la possibilità di gestire l'accesso alle risorse, usando gruppi di sicurezza, gruppi di Office 365, ruoli applicazione e cataloghi di pacchetti di accesso.  La [gestione dei gruppi self-service](../../active-directory/users-groups-roles/groups-self-service-management.md) consente ai proprietari del gruppo di gestire i propri gruppi senza che sia necessario assegnare un ruolo amministrativo. Gli utenti possono anche creare e gestire i gruppi di Office 365 senza affidarsi agli amministratori per gestire le richieste e i gruppi non usati scadono automaticamente.  [Azure ad la gestione dei diritti](../../active-directory/governance/entitlement-management-overview.md) consente ulteriormente la delega e la visibilità, con flussi di lavoro di richiesta di accesso completi e scadenza automatica.  È possibile delegare agli utenti non amministratori la possibilità di configurare i propri pacchetti di accesso per i gruppi, i team, le applicazioni e i siti di SharePoint Online di cui si è proprietari, con criteri personalizzati per gli utenti che devono approvare l'accesso, inclusa la configurazione dei responsabili e degli sponsor dei partner commerciali come responsabili approvazione.

### <a name="implement-azure-ad-access-reviews"></a>Implementare verifiche di accesso di Azure AD

Con le verifiche di [accesso di Azure ad](../../active-directory/governance/access-reviews-overview.md), è possibile gestire l'accesso ai pacchetti e alle appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo con privilegi per garantire la manutenzione di uno standard di sicurezza.  Una supervisione regolare da parte degli utenti, dei proprietari delle risorse e di altri revisori garantisce che gli utenti non mantengano l'accesso per periodi di tempo prolungati quando non sono più necessari.

## <a name="summary"></a>Riepilogo

Sono molti gli aspetti che ruotano attorno a un'infrastruttura di identità sicura, ma questo elenco di controllo di cinque passaggi consente di crearne una più sicura e protetta:

* Rafforzare le credenziali.
* Ridurre la superficie di attacco.
* Automatizzare la risposta alle minacce.
* Usare l'Intelligence per il cloud.
* Garantire un livello di sicurezza più prevedibile e completo per gli utenti finali con self-help.

Apprezziamo la serietà con cui gli utenti si approcciano alla sicurezza delle identità e ci auguriamo che questo documento rappresenti un utile piano di azione per migliorare il livello di sicurezza di un'organizzazione.

## <a name="next-steps"></a>Passaggi successivi

Se occorre assistenza per pianificare e distribuire i consigli, fare riferimento ai [piani di distribuzione del progetto di Azure AD](https://aka.ms/deploymentplans).

Se si è certi che tutti questi passaggi siano completi, usare il [Punteggio sicuro](../../active-directory/fundamentals/identity-secure-score.md)per l'identità di Microsoft, che consente di aggiornare le [procedure consigliate](identity-management-best-practices.md) e le minacce per la sicurezza più recenti.
