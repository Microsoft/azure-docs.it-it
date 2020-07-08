---
title: Eseguire la migrazione da Servizio di controllo di accesso di Microsoft Azure | Microsoft Docs
description: Informazioni sulle opzioni per lo spostamento di app e servizi da Servizio di controllo di accesso (ACS) di Azure.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: bae052e06aae4881dd7203a5616b35e9c96997fb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551726"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Procedura: Eseguire la migrazione da Servizio di controllo di accesso di Microsoft Azure

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Servizio di controllo di accesso di Microsoft Azure, un servizio di Azure Active Directory (Azure AD), verrà ritirato il 7 novembre 2018. Le applicazioni e i servizi che attualmente utilizzano questo servizio devono eseguire la migrazione completa a un meccanismo di autenticazione diverso entro tale data. Questo articolo presenta i consigli per i clienti attuali che pianificano la deprecazione dell'uso di Controllo di accesso. Se attualmente non si utilizza Controllo di accesso, non è necessario intraprendere alcuna azione.

## <a name="overview"></a>Panoramica

Controllo di accesso è un servizio di autenticazione cloud che offre un modo semplice per autenticare e autorizzare gli utenti per l'accesso a servizi e applicazioni Web. Consente il factoring di molte funzionalità di autenticazione e autorizzazione dal codice. Controllo di accesso viene utilizzato principalmente dagli sviluppatori e architetti di client Microsoft .NET, applicazioni Web ASP.NET e servizi Web Windows Communication Foundation (WCF).

I casi d'uso per Controllo di accesso possono essere suddivisi in tre categorie principali:

- Autenticazione per determinati servizi cloud Microsoft, tra cui bus di servizio di Azure e Dynamics CRM. Le applicazioni client ottengono token da Controllo di accesso da usare per l'autenticazione in questi servizi per eseguire diverse azioni.
- Aggiunta di autenticazione ad applicazioni Web, sia personalizzate che preconfenzionate (come SharePoint). Tramite l'autenticazione "passiva" di Controllo di accesso, le applicazioni Web possono supportare l'accesso con un account Microsoft (precedentemente Live ID) e con account di Google, Facebook, Yahoo, Azure AD e Active Directory Federation Services (AD FS).
- Protezione di servizi Web personalizzati con token rilasciati da Controllo di accesso. Tramite l'autenticazione "attiva", i servizi Web possono garantire che l'accesso sia consentito solo a client noti che hanno eseguito l'autenticazione con Controllo di accesso.

Questi casi d'uso e le rispettive strategie di migrazione consigliate vengono illustrati nelle sezioni seguenti.

> [!WARNING]
> Nella maggior parte dei casi sono necessarie modifiche significative al codice per la migrazione delle app e dei servizi esistenti in tecnologie più recenti. È consigliabile iniziare subito a pianificare ed eseguire la migrazione, per evitare qualsiasi rischio di interruzione o di tempo di inattività.

Controllo di accesso include i componenti seguenti:

- Un servizio token di sicurezza, che riceve richieste di autenticazione e restituisce token di sicurezza.
- Il portale di Azure classico, in cui vengono creati, eliminati, attivati e disattivati gli spazi dei nomi di Controllo di accesso.
- Un portale di gestione di Controllo di accesso separato, in cui personalizzare e configurare gli spazi dei nomi di Controllo di accesso.
- Un servizio di gestione che può essere usato per automatizzare le funzioni dei portali.
- Un motore di regole di trasformazione dei token che può essere usato per definire logica complessa per la manipolazione del formato di output dei token rilasciati da Controllo di accesso.

Per usare questi componenti, è necessario creare uno o più spazi dei nomi di Controllo di accesso. Uno *spazio dei nomi* è un'istanza dedicata di Controllo di accesso con cui comunicano applicazioni e servizi. Uno spazio dei nomi è isolato da tutti gli altri clienti di Controllo di accesso. Altri clienti di Controllo di accesso utilizzano spazi dei nomi dedicati. Uno spazio dei nomi in Controllo di accesso dispone di un URL dedicato simile al seguente:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Tutte le comunicazioni con il servizio token di sicurezza e tutte le operazioni di gestione vengono eseguite in corrispondenza di questo URL. Si utilizzano percorsi diversi per scopi diversi. Per determinare se le applicazioni o i servizi in uso utilizzano Controllo di accesso, verificare la presenza di qualsiasi tipo di traffico a https://&lt;spaziodeinomi&gt;.accesscontrol.windows.net. Il traffico verso questo URL è gestito da Controllo di accesso e deve essere sospeso. 

A questo fa eccezione il traffico verso l'indirizzo `https://accounts.accesscontrol.windows.net`, che è già gestito da un servizio diverso e **non è** interessato dal ritiro di Controllo di accesso. 

Per altre informazioni su Controllo di accesso, vedere [Servizio di controllo di accesso 2.0 (archiviato)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Scoprire quali app saranno interessate

Seguire i passaggi descritti in questa sezione per scoprire quali app saranno interessate dal ritiro di ACS.

### <a name="download-and-install-acs-powershell"></a>Scaricare e installare ACS PowerShell

1. Passare a PowerShell Gallery e scaricare [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. Installare il modulo eseguendo

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Ottenere un elenco di tutti i comandi disponibili eseguendo

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Per ottenere informazioni su un comando specifico, eseguire:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    dove `[Command-Name]` è il nome del comando ACS.

### <a name="list-your-acs-namespaces"></a>Elencare gli spazi dei nomi ACS

1. Connettersi ad ACS usando il cmdlet **Connect-AcsAccount**.
  
    Potrebbe essere necessario eseguire `Set-ExecutionPolicy -ExecutionPolicy Bypass` prima di poter eseguire i comandi ed essere l'amministratore di tali sottoscrizioni per eseguire i comandi.

2. Elencare le sottoscrizioni di Azure disponibili usando il cmdlet **Get-AcsSubscription**.
3. Elencare gli spazi dei nomi ACS usando il cmdlet **Get-AcsNamespace**.

### <a name="check-which-applications-will-be-impacted"></a>Controllare quali applicazioni saranno interessate

1. Usare lo spazio dei nomi del passaggio precedente e accedere a `https://<namespace>.accesscontrol.windows.net`

    Se ad esempio uno degli spazi dei nomi è contoso-test, passare a `https://contoso-test.accesscontrol.windows.net`

2. In **Relazioni di attendibilità** selezionare **Applicazioni relying party** per visualizzare l'elenco di app che saranno interessate dal ritiro di ACS.
3. Ripetere i passaggi 1 e 2 per qualsiasi altro spazio dei nomi ACS disponibile.

## <a name="retirement-schedule"></a>Pianificazione del ritiro

A partire dal mese di novembre 2017, tutti i componenti di Controllo di accesso sono completamente supportati e operativi. L'unica restrizione è [l'impossibilità di creare nuovi spazi dei nomi di Controllo di accesso tramite il portale di Azure classico](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Di seguito è riportata la pianificazione della deprecazione dei componenti di Controllo di accesso:

- **Novembre 2017**: l'esperienza di amministrazione di Azure AD nel portale di Azure classico [è stata ritirata](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). A questo punto, la gestione degli spazi dei nomi per Controllo di accesso sarà disponibile presso un nuovo URL dedicato: `https://manage.windowsazure.com?restoreClassic=true`. Utilizzare questo URl per visualizzare gli spazi dei nomi esistenti, abilitarli, disabilitarli ed eventualmente eliminarli.
- **2 aprile 2018**: il portale di Azure classico è stato completamente ritirato. Pertanto, la gestione degli spazi dei nomi di Controllo di accesso non è più disponibile tramite un URL. A questo punto, è impossibile disabilitare o abilitare, eliminare o enumerare gli spazi dei nomi di Controllo di accesso. Il portale di gestione di Controllo di accesso, tuttavia, sarà completamente funzionante e disponibile all'indirizzo `https://\<namespace\>.accesscontrol.windows.net`. Tutti gli altri componenti di Controllo di accesso continuano a funzionare normalmente.
- **7 novembre 2018**: tutti i componenti di Controllo di accesso vengono arrestati in modo permanente. Sono inclusi il portale di gestione di Controllo di accesso, il servizio di gestione, il servizio token di sicurezza e il motore di regole di trasformazione dei token. A questo punto, tutte le richieste inviate al controllo di accesso (che si trova in \<namespace\> . AccessControl.Windows.NET) hanno esito negativo. È necessario eseguire la migrazione di tutte le app e di tutti i servizi esistenti ad altre tecnologie molto prima di questa data.

> [!NOTE]
> Un criterio disabilita gli spazi dei nomi che non hanno richiesto un token per un periodo di tempo. A partire dall'inizio del mese di settembre 2018, questo periodo di tempo corrisponde attualmente a 14 giorni di inattività, ma verrà ridotto a 7 giorni di inattività nelle prossime settimane. Se ci sono spazi dei nomi Controllo di accesso attualmente disabilitati, è possibile [scaricare e installare ACS PowerShell](#download-and-install-acs-powershell) per riabilitare gli spazi dei nomi.

## <a name="migration-strategies"></a>Strategie di migrazione

Le sezioni seguenti illustrano i consigli generali per la migrazione da Controllo di accesso ad altre tecnologie Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Client dei servizi cloud Microsoft

Ogni servizio cloud Microsoft che accetta token rilasciati da Controllo di accesso supporta ora almeno una forma alternativa di autenticazione. Il meccanismo di autenticazione corretto varia per ogni servizio. È consigliabile consultare la documentazione specifica per ogni servizio per linee guida ufficiali. Per comodità, ogni set di documentazione viene indicato di seguito:

| Servizio | Indicazioni |
| ------- | -------- |
| Bus di servizio di Azure | [Eseguire la migrazione a firme di accesso condiviso](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Inoltro del bus di servizio di Azure | [Eseguire la migrazione a firme di accesso condiviso](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Cache gestita di Azure | [Eseguire la migrazione a Cache Redis di Azure](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Eseguire la migrazione alle API Servizi cognitivi](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Servizi BizTalk | [Eseguire la migrazione alla funzionalità app per la logica del servizio app di Azure](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Servizi multimediali di Azure | [Eseguire la migrazione all'autenticazione di Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Backup di Azure | [Aggiornare l'agente di backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Clienti di SharePoint

I clienti di SharePoint 2013, 2016 e SharePoint Online hanno un servizio ACS a lungo termine per scopi di autenticazione in scenari cloud, locali e ibridi. Il ritiro di ACS influirà su alcune funzionalità e su alcuni casi d'uso di SharePoint, ma non su altri. La tabella seguente include indicazioni di riepilogo sulla migrazione per alcune delle funzionalità di SharePoint più diffuse che sfruttano ACS:

| Funzionalità | Indicazioni |
| ------- | -------- |
| Autenticazione degli utenti da Azure AD | In precedenza Azure AD non supportava i token SAML 1.1 richiesti da SharePoint per l'autenticazione e ACS veniva usato come intermediario per rendere SharePoint compatibile con i formati di token di Azure AD. A questo punto, è possibile [connettere SharePoint direttamente a Azure ad usando app Azure ad Gallery SharePoint nell'app locale](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Autenticazione dell'app & autenticazione da server a server in SharePoint locale](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Non interessata dal ritiro di ACS. Non è necessario apportare alcuna modifica. | 
| [Autorizzazione di attendibilità bassa per componenti aggiuntivi di SharePoint (ospitati da provider e da SharePoint)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Non interessata dal ritiro di ACS. Non è necessario apportare alcuna modifica. |
| [Ricerca ibrida su cloud di SharePoint](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Non interessata dal ritiro di ACS. Non è necessario apportare alcuna modifica. |

### <a name="web-applications-that-use-passive-authentication"></a>Applicazioni Web che usano l'autenticazione passiva

Per le applicazioni Web che usano Controllo di accesso per l'autenticazione utente, Controllo di accesso offre le seguenti caratteristiche e funzionalità ad architetti e sviluppatori di applicazioni Web:

- Integrazione profonda con Windows Identity Foundation (WIF).
- Federazione con account Google, Facebook, Yahoo, Azure Active Directory, AD FS Microsoft.
- Supporto per i protocolli di autenticazione seguenti: OAuth 2.0 bozza 13, WS-Trust e Web Services Federation (WS-Federation).
- Supporto per i formati di token seguenti: token JSON Web (JWT), SAML 1.1, SAML 2.0 e token Web semplice (SWT).
- Un'esperienza di individuazione dell'area di autenticazione principale, integrata in WIF, che consente agli utenti di scegliere il tipo di account da usare per l'accesso. Questa esperienza è ospitata dall'applicazione Web e completamente personalizzabile.
- Trasformazione dei token che consente la personalizzazione avanzata delle attestazioni ricevute dall'applicazione Web dal Controllo di accesso, tra cui:
    - Pass-through delle attestazioni dai provider di identità.
    - Aggiunta di altre attestazioni personalizzate.
    - Semplice logica if-then per il rilascio di attestazioni in determinate condizioni.

Non è purtroppo disponibile un unico servizio che offra tutte queste funzionalità equivalenti. È consigliabile valutare le funzionalità di Controllo di accesso effettivamente necessarie e quindi scegliere tra [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) o un altro servizio di autenticazione cloud.

#### <a name="migrate-to-azure-active-directory"></a>Migrazione ad Azure Active Directory

Un approccio da prendere in considerazione prevede l'integrazione delle app e dei servizi direttamente in Azure AD. Azure AD è il provider di identità basato sul cloud per account aziendali o dell'istituto di istruzione Microsoft. Azure AD è il provider di identità per Office 365, Azure e altro ancora. Offre funzionalità di autenticazione federata simili a quelle di Controllo di accesso, ma non supporta tutte le funzionalità di Controllo di accesso. 

L'esempio principale è costituito dalla federazione con i provider di identità di social networking, ad esempio Facebook, Google e Yahoo. Se gli utenti accedono con questi tipi di credenziali, Azure AD non è la soluzione adatta. 

Azure AD inoltre non supporta necessariamente gli stessi protocolli di autenticazione di Controllo di accesso. Ad esempio, anche se sia Controllo di accesso sia Azure AD supportano OAuth, ci sono sottili differenze tra ciascuna implementazione. Implementazioni diverse richiedono modifiche del codice come parte della migrazione.

Azure AD offre tuttavia alcuni vantaggi potenziali ai clienti di Controllo di accesso. Supporta in modalità nativa gli account Microsoft aziendali e dell'istituto di istruzione ospitati nel cloud, usati in genere dai clienti di Controllo di accesso. 

Un tenant di Azure AD può anche essere federato a una o più istanze di Active Directory locale tramite AD FS. In questo modo, l'app può autenticare gli utenti basati su cloud e gli utenti ospitati in locale. Supporta inoltre il protocollo WS-Federation, che rende relativamente semplice l'integrazione con un'applicazione Web tramite WIF.

La tabella seguente confronta le funzionalità di Controllo di accesso rilevanti per le applicazioni Web con le funzionalità disponibili in Azure AD. 

A livello generale, *Azure Active Directory è probabilmente la scelta migliore per la migrazione se si consente agli utenti di accedere solo con i rispettivi account Microsoft aziendali e dell'istituto di istruzione*.

| Funzionalità | Supporto di Controllo di accesso | Supporto di Azure AD |
| ---------- | ----------- | ---------------- |
| **Tipi di account** | | |
| Account Microsoft aziendali o dell'istituto di istruzione | Supportato | Supportato |
| Account da Active Directory di Windows Server e AD FS |- Supportato tramite federazione con un tenant di Azure AD <br />- Supportato tramite federazione diretta con AD FS | Supportato solo tramite la federazione con un tenant di Azure AD | 
| Account da altri sistemi di gestione delle identità aziendali |- Possibile tramite federazione con un tenant di Azure AD <br />- Supportato tramite federazione diretta | Possibile tramite la federazione con un tenant di Azure AD |
| Account Microsoft per uso personale | Supportato | Supportato tramite il protocollo OAuth v2.0 di Azure AD, ma non tramite altri protocolli | 
| Account Facebook, Google, Yahoo | Supportato | Non supportato in alcun modo |
| **Compatibilità con protocolli e SDK** | | |
| WIF | Supportato | Supportato ma sono disponibili istruzioni limitate |
| WS-Federation | Supportato | Supportato |
| OAuth 2.0 | Supporto per la bozza 13 | Supporto per RFC 6749, la specifica più moderna |
| WS-Trust | Supportato | Non supportato |
| **Formati di token** | | |
| Token JSON Web | Supportato nella versione Beta | Supportato |
| SAML 1.1 | Supportato | Anteprima |
| SAML 2.0 | Supportato | Supportato |
| Token Web semplice | Supportato | Non supportato |
| **Personalizzazioni** | | |
| Individuazione dell'area di autenticazione principale/interfaccia utente per la scelta dell'account personalizzabili | Codice scaricabile che può essere incorporato nelle app | Non supportato |
| Caricare certificati per la firma di token personalizzati | Supportato | Supportato |
| Personalizzare le attestazioni nei token |- Eseguire il pass-through delle attestazioni di input dai provider di identità<br />- Ottenere un token di accesso dal provider di identità come attestazione<br />- Rilasciare attestazioni di output in base ai valori delle attestazioni di input<br />- Rilasciare attestazioni di output con valori costanti |- Non è possibile eseguire il pass-through di attestazioni da provider di identità federati<br />- Non è possibile ottenere un token di accesso dal provider di identità come attestazione<br />- Non è possibile rilasciare attestazioni di output in base ai valori delle attestazioni di input<br />- È possibile rilasciare attestazioni di output con valori costanti<br />- È possibile rilasciare attestazioni di output in base alle proprietà degli utenti sincronizzati con Azure AD |
| **Automazione** | | |
| Automatizzare le attività di configurazione e gestione | Supportato tramite il servizio di gestione del Controllo di accesso | Supportato con l'API Microsoft Graph |

Se si decide che Azure AD sia l'approccio di migrazione ottimale per le applicazioni e i servizi in uso, è necessario conoscere le due modalità disponibili per l'integrazione dell'app con Azure AD.

Per utilizzare WS-Federation o WIF per l'integrazione con Azure AD, è consigliabile attenersi all'approccio descritto in [Configurare l'accesso Single Sign-On federato per un'applicazione non della raccolta](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Questo articolo fa riferimento alla configurazione di Azure AD per l'accesso Single Sign-On basato su SAML ma è applicabile anche alla configurazione di WS-Federation. L'uso di questo approccio richiede una licenza di Azure AD Premium. Questo approccio presenta due vantaggi:

- Si ottiene la flessibilità completa della personalizzazione dei token di Azure AD. È possibile personalizzare le attestazioni rilasciate da Azure AD per associarle alle attestazioni generate da Controllo di accesso. In particolare è inclusa l'attestazione di ID utente o Identificatore nome. Per continuare a ricevere identificatori utente coerenti per gli utenti anche dopo il passaggio a tecnologie diverse, assicurarsi che gli ID utente rilasciati da Azure AD corrispondano a quelli rilasciati da Controllo di accesso.
- È possibile configurare un certificato per la firma di token specifico per l'applicazione e controllarne la durata.

> [!NOTE]
> Questo approccio richiede una licenza di Azure AD Premium. Se si è un cliente di Controllo di accesso e si necessita di una licenza premium per la configurazione dell'accesso Single Sign-On per un'applicazione, contattare Microsoft. Verranno fornite licenze per sviluppatori.

Un approccio alternativo consiste nell'attenersi a [questo esempio di codice](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) che fornisce istruzioni leggermente diverse sulla configurazione di WS-Federation. L'esempio di codice non usa WIF, ma il middleware OWIN di ASP.NET 4.5. Le istruzioni per la registrazione dell'app, tuttavia, sono valide per le app che usano WIF e non richiedono una licenza di Azure AD Premium. 

Se si sceglie questo approccio, è necessario comprendere il [rollover della chiave di firma in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Questo approccio usa la chiave di firma globale di Azure AD per rilasciare token. Per impostazione predefinita, WIF non aggiorna automaticamente le chiavi di firma. Quando Azure AD ruota le rispettive chiavi di firma globali, l'implementazione di WIF deve essere preparata per l'accettazione delle modifiche. Per altre informazioni, vedere [Important information about signing key rollover in Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx) (Informazioni importanti sul rollover della chiave di firma in Azure AD).

Se si riesce a eseguire l'integrazione con Azure AD tramite i protocolli OAuth o OpenID Connect, è consigliabile scegliere questo approccio. Documentazione e indicazioni complete sull'integrazione di Azure AD nell'applicazione Web sono disponibili nella [Guida per gli sviluppatori di Azure AD](https://aka.ms/aaddev).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrazione ad Azure Active Directory B2C

L'altro approccio da prendere in considerazione per la migrazione è relativo ad Azure AD B2C. Azure AD B2C è un servizio di autenticazione cloud che, analogamente a Controllo di accesso, consente agli sviluppatori di eseguire l'outsourcing della logica di autenticazione e di gestione delle identità in un servizio cloud. Si tratta di un servizio a pagamento, con livello gratuito e livello Premium, progettato per applicazioni rivolte ai clienti che possono avere fino a milioni di utenti attivi.

Analogamente a Controllo di accesso, una delle funzionalità più attraenti di Azure AD B2C è costituita dal supporto di molti tipi di account diversi. Con Azure AD B2C, è possibile accedere gli utenti con account Microsoft oppure account Facebook, Google, LinkedIn, GitHub o Yahoo e altro ancora. Azure AD B2C supporta inoltre "account locali" o nome utente e password creati in modo specifico dagli utenti per l'applicazione. Azure AD B2C offre anche un'estendibilità avanzata, che consente di personalizzare i flussi di accesso. 

Azure AD B2C, tuttavia, non supporta la varietà di protocolli di autenticazione e i formati di token che potrebbero essere richiesti dai clienti di Controllo di accesso. Non è inoltre possibile usare Azure AD B2C per ottenere token ed eseguire query per informazioni aggiuntive sull'utente dal provider di identità, Microsoft o altro.

La tabella seguente confronta le funzionalità di Controllo di accesso rilevanti per le applicazioni Web con le funzionalità disponibili in Azure AD B2C. A livello generale, *Azure AD B2C è probabilmente la scelta ottimale per la migrazione se l'applicazione è rivolta ai consumer o se supporta molti tipi diversi di account*.

| Funzionalità | Supporto di Controllo di accesso | Supporto di Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Tipi di account** | | |
| Account Microsoft aziendali o dell'istituto di istruzione | Supportato | Supportato tramite criteri personalizzati  |
| Account da Active Directory di Windows Server e AD FS | Supportato tramite federazione diretta con AD FS | Supportato tramite federazione SAML con criteri personalizzati |
| Account da altri sistemi di gestione delle identità aziendali | Supportato tramite federazione diretta su WS-Federation | Supportato tramite federazione SAML con criteri personalizzati |
| Account Microsoft per uso personale | Supportato | Supportato | 
| Account Facebook, Google, Yahoo | Supportato | Facebook e Google supportati in modalità nativa, Yahoo supportato tramite la federazione di OpenID Connect con criteri personalizzati |
| **Compatibilità con protocolli e SDK** | | |
| Windows Identity Foundation (WIF) | Supportato | Non supportato |
| WS-Federation | Supportato | Non supportato |
| OAuth 2.0 | Supporto per la bozza 13 | Supporto per RFC 6749, la specifica più moderna |
| WS-Trust | Supportato | Non supportato |
| **Formati di token** | | |
| Token JSON Web | Supportato nella versione Beta | Supportato |
| SAML 1.1 | Supportato | Non supportato |
| SAML 2.0 | Supportato | Non supportato |
| Token Web semplice | Supportato | Non supportato |
| **Personalizzazioni** | | |
| Individuazione dell'area di autenticazione principale/interfaccia utente per la scelta dell'account personalizzabili | Codice scaricabile che può essere incorporato nelle app | Interfaccia utente completamente personalizzabile tramite CSS personalizzati |
| Caricare certificati per la firma di token personalizzati | Supportato | Chiavi di accesso personalizzate, non certificati, supportati tramite criteri personalizzati |
| Personalizzare le attestazioni nei token |- Eseguire il pass-through delle attestazioni di input dai provider di identità<br />- Ottenere un token di accesso dal provider di identità come attestazione<br />- Rilasciare attestazioni di output in base ai valori delle attestazioni di input<br />- Rilasciare attestazioni di output con valori costanti |- È possibile eseguire il pass-through delle attestazioni dai provider di identità; alcune attestazioni richiedono criteri personalizzati<br />- Non è possibile ottenere un token di accesso dal provider di identità come attestazione<br />- È possibile rilasciare attestazioni di output in base ai valori delle attestazioni di input tramite criteri personalizzati<br />- È possibile rilasciare attestazioni di output con valori costanti tramite criteri personalizzati |
| **Automazione** | | |
| Automatizzare le attività di configurazione e gestione | Supportato tramite il servizio di gestione del Controllo di accesso |-Creazione di utenti consentiti tramite l'API Microsoft Graph<br />- Non è possibile creare tenant, applicazioni o criteri di B2C a livello di codice |

Se si decide che Azure AD B2C è l'approccio ottimale per le applicazioni e i servizi in uso, iniziare dalle risorse seguenti:

- [Documentazione di Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Prezzi di Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Eseguire la migrazione a Ping Identity o Auth0

In alcuni casi, è possibile che Azure AD e Azure AD B2C non siano sufficienti a sostituire Controllo di accesso nelle applicazioni Web senza apportare modifiche importanti al codice. Ecco alcuni esempi tipici:

- Applicazioni Web che utilizzano WIF o WS-Federation per l'accesso con provider di identità di social network, come Google o Facebook.
- Applicazioni Web che eseguono la federazione diretta a un provider di identità aziendale tramite il protocollo WS-Federation.
- Applicazioni Web che richiedono il token di accesso rilasciato da un provider di identità di social network, ad esempio Google o Facebook, come attestazione nei token rilasciati da Controllo di accesso.
- Applicazioni Web con regole di trasformazione dei token complesse che non possono essere riprodotte da Azure AD o Azure AD B2C.
- Applicazioni web multi-tenant che utilizzano ACS per la gestione centralizzata della federazione a molti provider di identità diversi

In questi casi, è necessario considerare la migrazione dell'applicazione web a un altro servizio di autenticazione cloud. Si consiglia di valutare le opzioni seguenti. Ciascuna delle opzioni seguenti offre funzionalità simili a Controllo di accesso:

![Questa immagine mostra il logo di Auth0](./media/active-directory-acs-migration/rsz-auth0.png) 

[Auth0](https://auth0.com/acs) è un servizio di identità cloud flessibile che ha creato [linee guida alla migrazione di alto livello per clienti di Controllo di accesso](https://auth0.com/acs) e supporta quasi tutte le funzionalità di ACS.

![Questa immagine mostra il logo di identità ping](./media/active-directory-acs-migration/rsz-ping.png)

[Ping Identity](https://www.pingidentity.com) offre due soluzioni simili ad ACS. PingOne è un servizio di identità cloud che supporta molte delle stesse funzionalità di ACS e PingFederate è un prodotto di identità locale simile che offre maggiore flessibilità. Fare riferimento alle [linee guida di Ping sul ritiro di ACS](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) per ulteriori informazioni sull'utilizzo di questi prodotti.

L'obiettivo della collaborazione con Ping Identity e Auth0 è quello di assicurare a tutti i clienti di Controllo di accesso un percorso di migrazione per le loro app e i loro servizi in grado di ridurre al minimo il lavoro necessario per il passaggio da Controllo di accesso.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Servizi Web con autenticazione attiva

Per i servizi Web protetti con token rilasciati da Controllo di accesso, Controllo di accesso offre le caratteristiche e funzionalità seguenti:

- Possibilità di registrare una o più *identità del servizio* nello spazio dei nomi di Controllo di accesso. Le identità del servizio sono utilizzabili per richiedere i token.
- Supporto per i protocolli OAuth WRAP e OAuth 2.0 bozza 13 per la richiesta di token tramite i tipi di credenziali seguenti:
    - Semplice password creata per l'identità del servizio
    - Token Web semplice firmato che usa una chiave simmetrica o un certificato X509
    - Token SAML rilasciato da un provider di identità attendibile (in genere un'istanza di AD FS)
- Supporto per i formati di token seguenti: JWT, SAML 1.1, SAML 2.0 e SWT.
- Semplici regole di trasformazione dei token.

Le identità del servizio di Controllo di accesso sono in genere usate per l'implementazione dell'autenticazione di tipo S2S (server-to-server). 

#### <a name="migrate-to-azure-active-directory"></a>Migrazione ad Azure Active Directory

Per questo tipo di flusso di autenticazione è consigliabile eseguire la migrazione ad [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD è il provider di identità basato sul cloud per account aziendali o dell'istituto di istruzione Microsoft. Azure AD è il provider di identità per Office 365, Azure e altro ancora. 

Azure AD può essere usato anche per l'autenticazione S2S tramite l'implementazione di Azure AD della concessione delle credenziali client OAuth. La tabella seguente confronta le funzionalità di Controllo di accesso per l'autenticazione S2S con quelle disponibili in Azure AD.

| Funzionalità | Supporto di Controllo di accesso | Supporto di Azure AD |
| ---------- | ----------- | ---------------- |
| Come registrare un servizio Web | Creare un relying party nel portale di gestione di Controllo di accesso | Creare un'applicazione Web di Azure AD nel portale di Azure |
| Come registrare un client | Creare un'identità del servizio nel portale di gestione di Controllo di accesso | Creare un'altra applicazione Web di Azure AD nel portale di Azure |
| Protocollo usato |- Protocollo OAuth WRAP<br />- Concessione di credenziali client OAuth 2.0 bozza 13 | Concessione di credenziali client OAuth 2.0 |
| Metodi di autenticazione client |- Password semplice<br />- Token Web semplice firmato<br />- Token SAML dal provider di identità federato |- Password semplice<br />- Token JWT firmato |
| Formati del token |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | Solo token JSON Web |
| Trasformazione di token |- Aggiungere attestazioni personalizzate<br />- Logica semplice di emissione di attestazioni if-then | Aggiungere attestazioni personalizzate | 
| Automatizzare le attività di configurazione e gestione | Supportato tramite il servizio di gestione del Controllo di accesso | Supportato con l'API Microsoft Graph |

Per indicazioni sull'implementazione di scenari S2S, vedere le risorse seguenti:

- Sezione da servizio a servizio della Guida per gli [sviluppatori di Azure ad](https://aka.ms/aaddev)
- [Esempio di codice Daemon che usa credenziali client per password semplice](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Esempio di codice Daemon che usa credenziali client per il certificato](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Eseguire la migrazione a Ping Identity o Auth0

In alcuni casi, è possibile che le credenziali del client di Azure AD e l'implementazione della concessione di OAuth non siano sufficienti a sostituire Controllo di accesso nell'architettura in uso senza apportare modifiche importanti al codice. Ecco alcuni esempi tipici:

- Autenticazione S2S che usa formati di token diversi da JWT.
- Autenticazione S2S che usa un token di input fornito da un provider di identità esterna.
- Autenticazione S2S con regole di trasformazione dei token che non è possibile riprodurre con Azure AD.

In questi casi, è possibile valutare la migrazione dell'applicazione Web a un altro servizio di autenticazione cloud. Si consiglia di valutare le opzioni seguenti. Ciascuna delle opzioni seguenti offre funzionalità simili a Controllo di accesso:

![Questa immagine mostra il logo di Auth0](./media/active-directory-acs-migration/rsz-auth0.png)

[Auth0](https://auth0.com/acs) è un servizio di identità cloud flessibile che ha creato [linee guida alla migrazione di alto livello per clienti di Controllo di accesso](https://auth0.com/acs) e supporta quasi tutte le funzionalità di ACS.

![Questa immagine mostra l'identità ping logo ](./media/active-directory-acs-migration/rsz-ping.png)
 di[ping](https://www.pingidentity.com) Identity offre due soluzioni simili a ACS. PingOne è un servizio di identità cloud che supporta molte delle stesse funzionalità di ACS e PingFederate è un prodotto di identità locale simile che offre maggiore flessibilità. Fare riferimento alle [linee guida di Ping sul ritiro di ACS](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) per ulteriori informazioni sull'utilizzo di questi prodotti.

L'obiettivo della collaborazione con Ping Identity e Auth0 è quello di assicurare a tutti i clienti di Controllo di accesso un percorso di migrazione per le loro app e i loro servizi in grado di ridurre al minimo il lavoro necessario per il passaggio da Controllo di accesso.

#### <a name="passthrough-authentication"></a>Autenticazione pass-through

Per l'autenticazione pass-through con trasformazione arbitraria dei token, non è disponibile alcuna tecnologia Microsoft equivalente al Servizio di controllo di accesso. Se è ciò di cui i clienti hanno bisogno, Auth0 potrebbe essere la soluzione approssimativa più adatta.

## <a name="questions-concerns-and-feedback"></a>Domande, dubbi e commenti

Siamo consapevoli che molti clienti di Controllo di accesso non trovino un percorso di migrazione ideale dopo la lettura di questo articolo. Alcuni potrebbero avere bisogno di assistenza o linee guida per determinare il piano ottimale. In caso di domande sugli scenari di migrazione, inserire un commento in questa pagina.
