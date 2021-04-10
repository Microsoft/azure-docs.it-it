---
title: Azure Active Directory Application Proxy domande frequenti
description: Informazioni sulle risposte alle domande frequenti sull'uso del proxy di applicazione Azure AD per pubblicare applicazioni locali locali per gli utenti remoti.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: b76934d3f16ec5f87ed1380135d22bc5d943652c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779768"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Domande frequenti sul proxy di applicazione Active Directory (Azure AD)

In questa pagina vengono fornite le risposte alle domande frequenti sul proxy di applicazione Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Abilitazione di Azure Active Directory Application Proxy

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Quale licenza è necessaria per usare Azure AD proxy di applicazione?

Per usare Azure AD proxy di applicazione, è necessario disporre di una licenza Azure AD Premium P1 o P2. Per ulteriori informazioni sulle licenze, vedere [Azure Active Directory prezzi](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="what-happens-to-azure-ad-application-proxy-in-my-tenant-if-my-license-expires"></a>Cosa accade al proxy di applicazione Azure AD nel tenant, se la licenza scade?
Se la licenza scade, il proxy di applicazione verrà disabilitato automaticamente. Le informazioni dell'applicazione verranno salvate per un massimo di un anno.

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Perché il pulsante di abilitazione del proxy di applicazione è disattivato?

Assicurarsi di avere almeno una licenza Azure AD Premium P1 o P2 e un connettore proxy di applicazione Azure AD installato. Dopo aver installato correttamente il primo connettore, il servizio proxy applicazione Azure AD verrà abilitato automaticamente.

## <a name="connector-configuration"></a>Configurazione del connettore

### <a name="why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version"></a>Perché il connettore usa ancora una versione precedente e non è stato aggiornato automaticamente alla versione più recente?

È possibile che il servizio di aggiornamento non funzioni correttamente o che non siano disponibili nuovi aggiornamenti che il servizio può installare.

Il servizio di aggiornamento è integro se è in esecuzione e non sono presenti errori registrati nel registro eventi (registri applicazioni e servizi-> Microsoft-> AadApplicationProxy-> Updater-> admin). 

> [!IMPORTANT]
> Per l'aggiornamento automatico vengono rilasciate solo le versioni principali. Si consiglia di aggiornare manualmente il connettore solo se necessario. Ad esempio, non è possibile attendere una versione principale, perché è necessario correggere un problema noto oppure si vuole usare una nuova funzionalità. Per ulteriori informazioni sulle nuove versioni, il tipo di versione (download, aggiornamento automatico), correzioni di bug e nuove funzionalità, vedere [Azure ad proxy di applicazione: cronologia delle versioni](application-proxy-release-version-history.md).

Per aggiornare manualmente un connettore:

-  Scaricare la versione più recente del connettore. (Il proxy di applicazione è presente nel portale di Azure. È anche possibile trovare il collegamento in [Azure ad proxy di applicazione: cronologia delle versioni](application-proxy-release-version-history.md).
-   Il programma di installazione riavvia i servizi del connettore del proxy di applicazione Azure AD. In alcuni casi, potrebbe essere necessario riavviare il server se il programma di installazione non è in grado di sostituire tutti i file. Per questo motivo, è consigliabile chiudere tutte le applicazioni, ad esempio Visualizzatore eventi, prima di iniziare l'aggiornamento.
-   Eseguire il programma di installazione. Il processo di aggiornamento è rapido e non richiede l'immissione di credenziali e il connettore non verrà riregistrato.

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>I servizi del connettore proxy di applicazione possono essere eseguiti in un contesto utente diverso da quello predefinito?

No, questo scenario non è supportato. Le impostazioni predefinite sono le seguenti:

- Connettore del proxy di applicazione di Microsoft AAD-WAPCSvc-servizio di rete
- Microsoft AAD Application Proxy Connector Updater-WAPCUpdaterSvc-NT Authority\System

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>Un utente Guest può registrare il connettore per il tenant (Guest) con l'amministratore globale o con il ruolo di amministratore dell'applicazione?

No, attualmente non è possibile. Il tentativo di registrazione viene sempre effettuato sul tenant principale dell'utente.

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>L'applicazione back-end è ospitata in più server Web e richiede la persistenza della sessione utente (appiccicosa). Come è possibile ottenere la persistenza della sessione? 

Per indicazioni, vedere [disponibilità elevata e bilanciamento del carico delle applicazioni e dei connettori del proxy di applicazione](application-proxy-high-availability-load-balancing.md).

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Terminazione TLS (ispezione TLS/HTTPS o accelerazione) sul traffico dai server del connettore ad Azure supportato?

Il connettore del proxy di applicazione esegue l'autenticazione basata sui certificati in Azure. La terminazione TLS (ispezione TLS/HTTPS o accelerazione) interrompe questo metodo di autenticazione e non è supportata. Il traffico dal connettore ad Azure deve ignorare tutti i dispositivi che eseguono la terminazione TLS.  

### <a name="is-tls-12-required-for-all-connections"></a>È necessario TLS 1,2 per tutte le connessioni?
Sì. Per fornire ai nostri clienti la crittografia migliore della categoria, il servizio Application Proxy limita l'accesso ai soli protocolli TLS 1.2. Queste modifiche sono state implementate gradualmente e sono effettive a partire dal 31 agosto 2019. Per mantenere la connessione con il servizio Application Proxy, assicurarsi che tutte le combinazioni client-server e browser-server siano aggiornate per l'uso di TLS 1.2. Queste includono i client usati dagli utenti per accedere alle applicazioni pubblicate tramite Application Proxy. Vedere [Preparazione per l'uso di TLS 1.2 in Office 365](/microsoft-365/compliance/prepare-tls-1.2-in-office-365) per riferimenti e risorse utili.

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>È possibile inserire un dispositivo proxy di inoltro tra i server del connettore e il server applicazioni back-end?
Sì, questo scenario è supportato a partire dalla versione del connettore 1.5.1526.0. Vedere [usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>È necessario creare un account dedicato per registrare il connettore con Azure AD proxy di applicazione?

Nessun motivo. Tutti gli account di amministratore globale o di amministratore dell'applicazione funzioneranno. Le credenziali immesse durante l'installazione non vengono usate dopo il processo di registrazione. Al contrario, viene emesso un certificato al connettore, che viene usato per l'autenticazione da quel punto in poi.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Come è possibile monitorare le prestazioni del connettore del proxy di applicazione Azure AD?

Sono presenti contatori di performance monitor installati insieme al connettore. A tale scopo:  

1. Selezionare **Start**, digitare "perfmon" e premere INVIO.
2. Selezionare **Performance Monitor** e fare clic sull' **+** icona verde.
3. Aggiungere i contatori del **connettore del proxy di applicazione di Microsoft AAD** che si desidera monitorare.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Il connettore del proxy di applicazione Azure AD deve trovarsi nella stessa subnet della risorsa?

Il connettore non deve trovarsi nella stessa subnet. Tuttavia, richiede la risoluzione dei nomi (DNS, file hosts) per la risorsa e la connettività di rete necessaria (routing alla risorsa, porte aperte sulla risorsa e così via). Per indicazioni, vedere [considerazioni sulla topologia di rete quando si usa Azure Active Directory Application Proxy](application-proxy-network-topology.md).

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>In quali versioni di Windows Server è possibile installare un connettore?

Il proxy di applicazione richiede Windows Server 2012 R2 o versione successiva. Attualmente esiste una limitazione per HTTP2 per Windows Server 2019. Per usare correttamente il connettore in Windows Server 2019, è necessario aggiungere la chiave del registro di sistema seguente e riavviare il server:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
```

## <a name="application-configuration"></a>Configurazione dell'applicazione

### <a name="i-am-receiving-an-error-about-an-invalid-certificate-or-possible-wrong-password"></a>Si riceve un errore relativo a un certificato non valido o a una password errata

Dopo aver caricato il certificato SSL, viene visualizzato il messaggio "certificato non valido, possibile password errata" nel portale.

Ecco alcuni suggerimenti per la risoluzione di questo errore:
- Verificare la presenza di problemi con il certificato. Installarlo nel computer locale. Se non si verificano problemi, il certificato è valido.
- Verificare che la password non contenga caratteri speciali. Per il test, la password deve contenere solo i caratteri 0-9, A-Z e a-z.
- Se il certificato è stato creato con il provider di archiviazione chiavi del software Microsoft, è necessario usare l'algoritmo RSA.

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Qual è la lunghezza del timeout di back-end predefinito e "Long"? Il timeout può essere esteso?

La lunghezza predefinita è pari a 85 secondi. L'impostazione "Long" è 180 secondi. Il limite di timeout non può essere esteso.

### <a name="can-a-service-principal-manage-application-proxy-using-powershell-or-microsoft-graph-apis"></a>Un'entità servizio può gestire il proxy di applicazione usando PowerShell o API Microsoft Graph?

No, attualmente non è supportata.

### <a name="what-happens-if-i-delete-cwap_authsecret-the-client-secret-in-the-app-registration"></a>Cosa accade se si elimina CWAP_AuthSecret (segreto client) nella registrazione dell'app?

Il segreto client, denominato anche *CWAP_AuthSecret*, viene aggiunto automaticamente all'oggetto applicazione (registrazione dell'app) quando viene creata l'app del proxy dell'applicazione Azure ad.

Il segreto client è valido per un anno. Un nuovo segreto client di un anno viene creato automaticamente prima della scadenza del segreto client valido corrente. Tre CWAP_AuthSecret segreti client vengono mantenuti sempre nell'oggetto applicazione. 

> [!IMPORTANT]
> L'eliminazione di CWAP_AuthSecret interrompe la pre-autenticazione per Azure AD proxy di applicazione. Non eliminare CWAP_AuthSecret.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Ricerca per categorie modificare la pagina di destinazione caricata dall'applicazione?

Dalla pagina registrazioni applicazioni è possibile modificare l'URL della Home page dell'URL esterno desiderato della pagina di destinazione. La pagina specificata verrà caricata quando l'applicazione viene avviata da app personali o dal portale di Office 365. Per i passaggi di configurazione, vedere [impostare un Home page personalizzato per le app pubblicate usando Azure ad proxy di applicazione](./application-proxy-configure-custom-home-page.md)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>È possibile pubblicare solo le applicazioni basate su IIS? Che cosa accade per le applicazioni Web in esecuzione su server Web non Windows? Il connettore deve essere installato in un server in cui è installato IIS?

No, non è previsto alcun requisito IIS per le applicazioni pubblicate. È possibile pubblicare applicazioni Web in esecuzione su server diversi da Windows Server. Tuttavia, potrebbe non essere possibile usare la preautenticazione con un server non Windows, a seconda che il server Web supporti la negoziazione (autenticazione Kerberos). IIS non è richiesto nel server in cui è installato il connettore.

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>È possibile configurare il proxy di applicazione per aggiungere l'intestazione HSTS?
Il proxy di applicazione non aggiunge automaticamente l'intestazione HTTP Strict-Transport-Security alle risposte HTTPS, ma manterrà l'intestazione se si trova nella risposta originale inviata dall'applicazione pubblicata. La dimostrazione di un'impostazione per abilitare questa funzionalità è nella roadmap. Se si è interessati a un'anteprima che consente di aggiungerla alle risposte, rivolgersi a aadapfeedback@microsoft.com per informazioni dettagliate.

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Quando è consigliabile usare il metodo PrincipalsAllowedToDelegateToAccount quando si configura la delega vincolata Kerberos (delega vincolata Kerberos)?

Il metodo PrincipalsAllowedToDelegateToAccount viene usato quando i server del connettore si trovano in un dominio diverso dall'account del servizio dell'applicazione Web. Richiede l'uso della delega vincolata basata sulle risorse.
Se i server del connettore e l'account del servizio dell'applicazione Web si trovano nello stesso dominio, è possibile utilizzare Active Directory utenti e computer per configurare le impostazioni di delega in ogni account del computer connettore, consentendo loro di delegare al nome SPN di destinazione.

Se i server del connettore e l'account del servizio dell'applicazione Web si trovano in domini diversi, viene utilizzata la delega basata sulle risorse. Le autorizzazioni di delega sono configurate nel server Web di destinazione e nell'account del servizio dell'applicazione Web. Questo metodo di delega vincolata è relativamente nuovo. Il metodo è stato introdotto in Windows Server 2012, che supporta la delega tra domini consentendo al proprietario della risorsa (servizio Web) di controllare quali account del computer e del servizio possono delegare. Non è disponibile alcuna interfaccia utente per semplificare questa configurazione, quindi è necessario usare PowerShell.
Per ulteriori informazioni, vedere il white paper informazioni sulla [delega vincolata Kerberos con proxy di applicazione](https://aka.ms/kcdpaper).

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>L'autenticazione NTLM funziona con Azure AD proxy di applicazione?

Non è possibile usare l'autenticazione NTLM come metodo di pre-autenticazione o Single Sign-On. L'autenticazione NTLM può essere utilizzata solo quando è possibile negoziare direttamente tra il client e l'applicazione Web pubblicata. L'utilizzo dell'autenticazione NTLM comporta in genere la visualizzazione di un messaggio di richiesta di accesso nel browser.

### <a name="can-i-use-the-logon-identity-on-premises-user-principal-name-or-on-premises-sam-account-name-in-a-b2b-iwa-single-sign-on-scenario"></a>È possibile usare l'identità di accesso "nome dell'entità utente locale" o "nome account SAM locale" in uno scenario di Single Sign-On IWA B2B?

No, questo non funziona perché un utente guest in Azure AD non ha l'attributo richiesto da nessuna delle identità di accesso indicate in precedenza.

In questo caso sarà presente un fallback a "nome entità utente". Per altri dettagli sullo scenario B2B, vedere [concedere agli utenti B2B l'accesso Azure ad alle applicazioni locali](../external-identities/hybrid-cloud-to-on-premises.md).

## <a name="pass-through-authentication"></a>Autenticazione pass-through

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>È possibile usare I criteri di accesso condizionale per le applicazioni pubblicate con l'autenticazione pass-through?

I criteri di accesso condizionale vengono applicati solo per gli utenti pre-autenticati correttamente in Azure AD. Con l'autenticazione pass-through non viene attivato Azure AD autenticazione, pertanto non è possibile applicare i criteri di accesso condizionale. Con l'autenticazione pass-through, è necessario implementare i criteri di autenticazione a più fattori nel server locale, se possibile, o abilitando la pre-autenticazione con Azure AD proxy di applicazione.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>È possibile pubblicare un'applicazione Web con il requisito di autenticazione del certificato client?

No, questo scenario non è supportato perché il proxy di applicazione terminerà il traffico TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Pubblicazione del Gateway Desktop remoto

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Come è possibile pubblicare Desktop remoto Gateway su Azure AD proxy di applicazione?

Vedere [pubblicare desktop remoto con Azure ad proxy di applicazione](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>È possibile usare la delega vincolata Kerberos (Single Sign-On-autenticazione integrata di Windows) nello scenario di pubblicazione del Gateway Desktop remoto?

No, questo scenario non è supportato.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Gli utenti non usano Internet Explorer 11 e lo scenario di pre-autenticazione non funziona. È normale?

Sì, è previsto. Lo scenario di pre-autenticazione richiede un controllo ActiveX, che non è supportato nei browser di terze parti.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>Il client Web di Desktop remoto (HTML5) è supportato?

Sì, questo scenario è attualmente disponibile in anteprima pubblica. Vedere [pubblicare desktop remoto con Azure ad proxy di applicazione](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Dopo aver configurato lo scenario di pre-autenticazione, ho notato che l'utente deve eseguire l'autenticazione due volte: prima nel modulo di accesso Azure AD e quindi nel modulo di accesso RDWeb. È normale? Come posso ridurlo a un accesso?

Sì, è previsto. Se il computer dell'utente è Azure AD aggiunto, l'utente accederà Azure AD automaticamente. L'utente deve fornire le proprie credenziali solo nel modulo di accesso RDWeb.

## <a name="sharepoint-publishing"></a>Pubblicazione di SharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Come è possibile pubblicare SharePoint su Azure AD proxy di applicazione?

Vedere [abilitare l'accesso remoto a SharePoint con Azure ad proxy di applicazione](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>È possibile usare l'app per dispositivi mobili SharePoint (iOS/Android) per accedere a un server SharePoint pubblicato?

L' [app per dispositivi mobili SharePoint](/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) non supporta attualmente Azure Active Directory pre-autenticazione.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Pubblicazione di Active Directory Federation Services (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>È possibile usare Azure AD proxy di applicazione come AD FS proxy (ad esempio proxy applicazione Web)?

No. Azure AD proxy di applicazione è progettato per funzionare con Azure AD e non soddisfa i requisiti per fungere da proxy di AD FS.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense-and-remote-desktop-web-client-html5"></a>WebSocket supporta il lavoro per applicazioni diverse da Qlik Sense e Desktop remoto Web client (HTML5)?

Attualmente, il supporto del protocollo WebSocket è ancora in anteprima pubblica e potrebbe non funzionare per altre applicazioni. Alcuni clienti hanno avuto un successo misto usando il protocollo WebSocket con altre applicazioni. Se si testano tali scenari, si apprezzeranno i risultati. Inviare commenti e suggerimenti all'indirizzo aadapfeedback@microsoft.com .

Le funzionalità (registri eventi, PowerShell e Servizi Desktop remoto) nell'interfaccia di amministrazione di Windows (WAC) non funzionano attualmente con Azure AD proxy di applicazione.

## <a name="link-translation"></a>Conversione collegamento

### <a name="does-using-link-translation-affect-performance"></a>L'uso della traduzione del collegamento influisce sulle prestazioni?

Sì. La conversione del collegamento influiscono sulle prestazioni. Il servizio proxy applicazione esegue l'analisi dell'applicazione per i collegamenti hardcoded e li sostituisce con i rispettivi URL esterni pubblicati prima di presentarli all'utente. 

Per ottenere prestazioni ottimali, è consigliabile utilizzare URL interni ed esterni identici configurando [domini personalizzati](./application-proxy-configure-custom-domain.md). Se l'uso di domini personalizzati non è possibile, è possibile migliorare le prestazioni di conversione dei collegamenti usando l'estensione di accesso sicuro app personali o il browser Microsoft Edge su dispositivi mobili. Vedere [Reindirizzamento dei collegamenti hardcoded per le app pubblicate con Azure ad proxy di applicazione](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Caratteri jolly

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Ricerca per categorie utilizzare caratteri jolly per pubblicare due applicazioni con lo stesso nome di dominio personalizzato ma con protocolli diversi, uno per HTTP e uno per HTTPS?

Questo scenario non è supportato direttamente. Le opzioni per questo scenario sono:

1. Pubblicare gli URL HTTP e HTTPS come applicazioni separate con un carattere jolly, ma assegnare a ognuno di essi un dominio personalizzato diverso. Questa configurazione funzionerà poiché hanno URL esterni diversi.

2. Pubblicare l'URL HTTPS tramite un'applicazione con caratteri jolly. Pubblicare le applicazioni HTTP separatamente usando questi cmdlet di PowerShell del proxy di applicazione:
   - [Gestione applicazioni del proxy di applicazione](/powershell/module/azuread/#application_proxy_application_management&preserve-view=true)
   - [Gestione connettore del proxy di applicazione](/powershell/module/azuread/#application_proxy_connector_management&preserve-view=true)
