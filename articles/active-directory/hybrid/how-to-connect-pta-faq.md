---
title: 'Azure AD Connect: Autenticazione pass-through - Domande frequenti | Microsoft Docs'
description: Fornisce le risponde alle domande frequenti sull'autenticazione pass-through di Azure Active Directory.
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75f797c40a276323cea9983c5340d2d854160c83
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368478"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Autenticazione pass-through di Azure Active Directory: domande frequenti

Questo articolo risponde alle domande frequenti sull'autenticazione pass-through di Azure Active Directory (Azure AD). Visitare questa pagina regolarmente per eventuali aggiornamenti.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Quale metodo di accesso a Azure AD è preferibile usare tra autenticazione pass-through, sincronizzazione dell'hash delle password e Active Directory Federation Services (AD FS)?

Riesaminare [questa guida](./choose-ad-authn.md) per confrontare i vari metodi di accesso di Azure AD e per scoprire come scegliere il metodo di accesso appropriato per l'organizzazione.

## <a name="is-pass-through-authentication-a-free-feature"></a>L'autenticazione pass-through è una funzionalità gratuita?

L'autenticazione pass-through è una funzionalità gratuita. Per usare Azure AD non sono necessarie edizioni a pagamento.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>[L'accesso condizionale](../conditional-access/overview.md) funziona con l'autenticazione pass-through?

Sì. Tutte le funzionalità di accesso condizionale, tra cui Azure AD Multi-Factor Authentication, funzionano con l'autenticazione pass-through.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>L'autenticazione pass-through supporta "Alternate ID" come nome utente, al posto di "userPrincipalName"?
Sì, l'accesso con un valore non UPN, ad esempio un indirizzo di posta elettronica alternativo, è supportato sia per l'autenticazione pass-through sia per la sincronizzazione dell'hash delle password (pH). Per ulteriori informazioni sull' [ID di accesso alternativo](../authentication/howto-authentication-use-email-signin.md).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>La sincronizzazione dell'hash delle password agisce da fallback per l'autenticazione pass-through?

No. L'autenticazione pass-through _non_ esegue automaticamente il failover sulla sincronizzazione dell'hash delle password. Per evitare errori di accesso dell'utente, è consigliabile configurare l'autenticazione pass-through per la [disponibilità elevata](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Cosa accade quando si passa dalla sincronizzazione dell'hash delle password all'autenticazione pass-through?

Quando si usa Azure AD Connect per cambiare il metodo di accesso dalla sincronizzazione dell'hash delle password all'autenticazione pass-through, l'autenticazione pass-through diventa il metodo di accesso primario per gli utenti in domini gestiti. Si noti che gli hash delle password di tutti gli utenti che sono stati sincronizzati in precedenza con la sincronizzazione dell'hash delle password rimangono archiviati in Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>È possibile installare un connettore del [proxy di applicazione di Azure AD](../manage-apps/application-proxy.md) nello stesso server in cui è presente un agente di autenticazione pass-through?

Sì. Questa configurazione è supportata nelle versioni ridenominate dell'agente di autenticazione pass-through (versione 1.5.193.0 o successive).

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Quali versioni di Azure AD Connect e dell'agente di autenticazione pass-through sono necessarie?

Per il corretto funzionamento di questa funzionalità è necessario disporre della versione 1.1.750.0 di Azure AD Connect (o versioni successive) e della versione 1.5.193.0 dell'agente di autenticazione pass-through (o versioni successive). Installare tutto il software in server Windows Server 2012 R2 o versioni successive.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Cosa accade se la password dell'utente è scaduta e l'utente prova ad accedere usando l'autenticazione pass-through?

Se è stato configurato il [writeback delle password](../authentication/concept-sspr-writeback.md) per un utente specifico e se l'utente esegue l'accesso usando l'autenticazione pass-through, la password può essere modificata o reimpostata. Le password vengono riscritte in Active Directory locale come previsto.

Se non è stato configurato il writeback delle password per un utente specifico o se l'utente non dispone di una licenza di Azure AD valida, la password non può essere aggiornata nel cloud neanche se è scaduta. L'utente vedrà un messaggio simile a questo: "L'organizzazione non consente l'aggiornamento della password in questo sito. Aggiornare la password usando il metodo consigliato dall'organizzazione oppure contattare l'amministratore per chiedere assistenza". L'utente o l'amministratore deve reimpostare la password in Active Directory locale.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>In che modo l'autenticazione pass-through protegge dagli attacchi di forza bruta contro le password?

[Legge le informazioni sul Blocco intelligente](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Cosa comunicano gli agenti di autenticazione pass-through sulle porte 80 e 443?

- Gli agenti di autenticazione inviano le richieste HTTP sulla porta 443 per tutte le operazioni di funzionalità.
- Gli agenti di autenticazione effettuano richieste HTTP sulla porta 80 per scaricare gli elenchi di revoche di certificati (CRL) TLS/SSL.

     >[!NOTE]
     >In aggiornamenti recenti è stato ridotto il numero di porte necessarie per la funzionalità. Se si dispone di versioni precedenti di Azure AD Connect o dell'agente di autenticazione, tenere aperte anche le porte seguenti: 5671, 8080, 9090, 9091, 9350, 9352 e 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Gli agenti di autenticazione pass-through possono comunicare su un server proxy Web in uscita?

Sì. Se Web Proxy Auto-Discovery (WPAD) è abilitato nell'ambiente locale, gli agenti di autenticazione provano automaticamente a individuare e usare un server proxy Web della rete.

Se nell'ambiente in uso non è disponibile il protocollo WPAD, è possibile aggiungere informazioni sul proxy (come illustrato di seguito) per consentire all'agente di autenticazione pass-through di comunicare con Azure AD:
- Configurare le informazioni sul proxy in Internet Explorer prima di installare l'agente di autenticazione pass-through sul server. In questo modo sarà possibile completare l'installazione dell'agente di autenticazione, ma tale agente verrà visualizzato come **non attivo** nel portale di amministrazione.
- Nel server passare a "C:\Program Files\Microsoft Azure AD Connect Authentication Agent".
- Modificare il file di configurazione "AzureADConnectAuthenticationAgentService" e aggiungere le righe seguenti (sostituire "http\://contosoproxy.com:8080" con l'indirizzo proxy in uso):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>È possibile installare due o più agenti di autenticazione pass-through nello stesso server?

No, è possibile installare solo un agente di autenticazione pass-through in un singolo server. Se si intende configurare l'autenticazione pass-through per la disponibilità elevata, [seguire le istruzioni qui](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>È necessario rinnovare manualmente i certificati utilizzati dagli agenti di autenticazione pass-through?

La comunicazione tra ciascun agente di autenticazione pass-through e Azure AD è protetta tramite l'autenticazione basata su certificato. Questi [certificati vengono rinnovati automaticamente ogni pochi mesi per Azure ad](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). e non è necessario rinnovarli manualmente. È possibile eliminare i vecchi certificati scaduti in base alle esigenze.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Come si rimuove un agente di autenticazione pass-through?

Finché l'agente di autenticazione pass-through è in esecuzione, rimane attivo e continua a gestire le richieste di accesso degli utenti. Per disinstallare un agente di autenticazione, andare a **Pannello di controllo -> Programmi -> Programmi e funzionalità** e disinstallare entrambi i programmi **Agente di autenticazione di Microsoft Azure AD Connect** e **Microsoft Azure AD Connect Agent Updater**.

Se si osserva il pannello Autenticazione pass-through nell'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) dopo aver completato il passaggio precedente, si noterà l'agente di autenticazione indicato come **Inattivo**. Si tratta di un comportamento _previsto_. L'agente di autenticazione viene automaticamente eliminato dall'elenco dopo 10 giorni.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Si usa già AD FS per eseguire l'accesso a Azure AD. Come si passa all'autenticazione pass-through?

Se si esegue la migrazione da AD FS (o da altre tecnologia federative) per l'autenticazione pass-through, si consiglia vivamente di seguire la guida per la distribuzione dettagliata pubblicata [qui](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>È possibile usare l'autenticazione pass-through in un ambiente Active Directory a più foreste?

Sì. Gli ambienti a più foreste sono supportati se sono presenti trust tra foreste (bidirezionali) tra le foreste Active Directory e se il routing del suffisso del nome è configurato correttamente.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>L'autenticazione pass-through fornisce bilanciamento del carico su più agenti di autenticazione?

No, l'installazione di più agenti di autenticazione pass-through garantisce solo la [disponibilità elevata](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability), ma non offre un bilanciamento del carico deterministico tra gli agenti di autenticazione. Qualsiasi agente di autenticazione (in modo casuale) può elaborare una richiesta di accesso utente specifica.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Quanti agenti di autenticazione pass-through è necessario installare?

L'installazione di più agenti di autenticazione pass-through garantisce la [disponibilità elevata](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability), ma non offre un bilanciamento del carico deterministico tra gli agenti.

Considerare il carico massimo e medio di richieste di accesso previsto nel tenant. Come benchmark, un singolo agente di autenticazione può gestire da 300 a 400 autenticazioni al secondo in un server standard con CPU a 4 core e 16 GB di RAM.

Per stimare il traffico di rete, usare le indicazioni seguenti relative al dimensionamento:
- Ogni richiesta ha una dimensione di payload di (0.5K + 1K * num_of_agents) byte. Ad esempio, i dati da Azure AD per l'agente di autenticazione. In questo caso, "num_of_agents" indica il numero di agenti di autenticazione registrati nel tenant.
- Ogni risposta ha una dimensione di payload di 1 KB. Ad esempio, i dati dall'agente di autenticazione ad Azure AD.

Per la maggior parte dei clienti, un totale di due o tre agenti di autenticazione è sufficiente ai fini della capacità e della disponibilità elevata. È consigliabile installare gli agenti di autenticazione vicino ai controller di dominio per migliorare la latenza di accesso.

>[!NOTE]
>Esiste un limite di sistema di 40 agenti di autenticazione per ogni tenant.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Perché è necessario un account amministratore globale solo cloud per abilitare l'autenticazione pass-through?

È consigliabile abilitare o disabilitare l'autenticazione pass-through con un account amministratore globale solo cloud. Informazioni sull' [aggiunta di un account amministratore globale solo cloud](../fundamentals/add-users-azure-active-directory.md). Questa procedura è fondamentale per evitare di rimanere bloccati fuori dal tenant.

## <a name="how-can-i-disable-pass-through-authentication"></a>Come si disabilita l'autenticazione pass-through?

Eseguire nuovamente la procedura guidata Azure AD Connect e scegliere un metodo di accesso utente diverso da Autenticazione pass-through. Questa modifica disabilita l'autenticazione pass-through nel tenant e disinstalla l'agente di autenticazione nel server. Gli agenti di autenticazione degli altri server devono essere disinstallati manualmente.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Cosa accade quando si disinstalla un agente di autenticazione pass-through?

Se si disinstalla un agente di autenticazione pass-through in un server, il server non accetta più richieste di accesso. Per evitare l'interruzione della funzionalità di accesso utente al tenant, verificare che sia in esecuzione un altro agente di autenticazione prima di disinstallare l'agente di autenticazione pass-through.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Ho un tenant non recente che è stato originariamente configurato usando AD FS.  Abbiamo recentemente eseguito la migrazione a PTA ma ora non vengono visualizzate le modifiche UPN sincronizzate in Azure AD.  Perché le modifiche UPN non vengono sincronizzate?

R: Nelle circostanze seguenti le modifiche UPN locali potrebbero non venire sincronizzate:

- Il tenant di Azure AD è stato creato prima del 15 giugno 2015
- La federazione iniziale con il tenant di Azure Active Directory è stata creata con Active Directory Federation Services per l'autenticazione
- Si è passati alla gestione degli utenti usando PTA come autenticazione

Ciò avviene perché il comportamento predefinito del tenant creati prima del 15 giugno 2015 prevedeva il blocco delle modifiche UPN.  Se è necessario annullare le modifiche UPN eseguire il cmdlet di PowerShell seguente:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

I tenant creati dopo il 15 giugno 2015 prevedono come comportamento predefinito la sincronizzazione delle modifiche UPN.   



## <a name="next-steps"></a>Passaggi successivi
- [Limitazioni correnti](how-to-connect-pta-current-limitations.md): apprendere quali sono gli scenari supportati.
- [Avvio rapido](how-to-connect-pta-quick-start.md): iniziare a usare l'autenticazione pass-through di Azure AD.
- [Eseguire la migrazione da AD FS all'autenticazione pass-through](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true): una guida dettagliata per la migrazione da AD FS (o altre tecnologie federative) per l'autenticazione pass-through.
- [Blocco smart](../authentication/howto-password-smart-lockout.md): apprendere come configurare la funzionalità di blocco smart nel tenant per proteggere gli account utente.
- [Approfondimento tecnico](how-to-connect-pta-how-it-works.md): comprendere come funziona l'autenticazione pass-through.
- [Risoluzione dei problemi](tshoot-connect-pass-through-authentication.md): apprendere come risolvere i problemi comuni relativi alla funzionalità di autenticazione pass-through.
- [Approfondimento sulla sicurezza](how-to-connect-pta-security-deep-dive.md): ottenere informazioni tecniche approfondite sulla funzionalità di autenticazione pass-through.
- [Accesso Single Sign-On facile di Azure AD](how-to-connect-sso.md): ottenere altre informazioni su questa funzionalità complementare.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): usare il forum di Azure Active Directory per inviare richieste di nuove funzionalità.
