---
title: Consente di compilare app per l'accesso Azure AD utenti
titleSuffix: Microsoft identity platform
description: Viene illustrato come compilare un'applicazione multi-tenant che può accedere a un utente da qualsiasi tenant Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 03/17/2020
ms.author: ryanwi
ms.reviewer: jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 3578562839069eb4b9c99b16d938efe48821fcec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631308"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Procedura: Come consentire l'accesso a qualsiasi utente di Azure Active Directory usando il modello di applicazione multi-tenant

Se si offre un'applicazione Software as a Service (SaaS) a molte organizzazioni, è possibile configurare l'applicazione per poter consentire accessi da qualsiasi tenant di Azure Active Directory (Azure AD). Questa configurazione viene chiamata *creazione dell'applicazione multi-tenant*. Gli utenti in qualsiasi tenant Azure AD saranno in grado di accedere all'applicazione dopo il consenso ad usare il loro account con l'applicazione.

Se è disponibile un'applicazione esistente con un proprio sistema di account o che supporta altri tipi di accesso da altri provider di cloud, l'aggiunta dell'accesso di Azure AD da qualsiasi tenant è semplice. È sufficiente registrare l'app, aggiungere il codice di accesso tramite OAuth2, OpenID Connect o SAML e inserire un [pulsante "Accedi con Microsoft"][AAD-App-Branding] nell'applicazione.

> [!NOTE]
> Questo articolo presuppone che l'utente abbia già familiarità con la creazione di un'applicazione single-tenant per Azure AD. In caso contrario, iniziare con una delle guide introduttive elencate nella [home page della guida per gli sviluppatori][AAD-Dev-Guide].

Quattro semplici passaggi consentono di convertire l'applicazione in un'applicazione multi-tenant di Azure AD:

1. [Aggiornare la registrazione dell'applicazione in modo che sia multi-tenant](#update-registration-to-be-multi-tenant)
2. [Aggiornare il codice per l'invio delle richieste all'endpoint /common](#update-your-code-to-send-requests-to-common)
3. [Aggiornare il codice per gestire più valori dell'autorità di certificazione](#update-your-code-to-handle-multiple-issuer-values)
4. [Informarsi sul consenso dell'utente e dell'amministratore e apportare le modifiche appropriate al codice](#understand-user-and-admin-consent)

Esaminiamo in dettaglio ogni passaggio. È anche possibile passare direttamente all'esempio [creare un'applicazione Web SaaS multi-tenant che chiama Microsoft Graph usando Azure ad e OpenID Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Aggiornare la registrazione in modo che sia multi-tenant

Per impostazione predefinita, le registrazioni di API o app Web in Azure AD sono single-tenant. È possibile rendere la registrazione multi-tenant individuando l'opzione **tipi di conto supportati** nel riquadro **autenticazione** della registrazione dell'applicazione nella [portale di Azure][AZURE-portal] e impostando gli **account in qualsiasi directory dell'organizzazione**.

Un'applicazione può diventare multi-tenant se l'URI dell'ID app dell'applicazione è univoco a livello globale. L'URI dell'ID App è uno dei modi in cui un'applicazione viene identificata nei messaggi di protocollo. Per un'applicazione single-tenant, è sufficiente che l'URI dell'ID App sia univoco all'interno del tenant. Per un'applicazione multi-tenant, è necessario che sia univoco a livello globale in modo da Azure AD possa trovare l'applicazione in tutti i tenant. L'univocità globale viene applicata richiedendo che l'URI dell'ID App abbia un nome host corrispondente a un dominio verificato del tenant di Azure AD.

Per impostazione predefinita, le app create tramite il portale di Azure hanno un URI di ID app univoco a livello globale impostato al momento della creazione dell'app, ma è possibile modificare questo valore. Ad esempio, se il nome del tenant è contoso.onmicrosoft.com, l'URI ID app sarà `https://contoso.onmicrosoft.com/myapp`. Se il tenant ha un dominio verificato `contoso.com`, l'URI ID app valido sarà `https://contoso.com/myapp`. Se l'URI dell'ID App non segue questo modello, l'impostazione di un'applicazione come multi-tenant ha esito negativo.

> [!NOTE]
> Per impostazione predefinita, le registrazioni native client e [le applicazioni della piattaforma Microsoft Identity](./v2-overview.md) sono multi-tenant. Non è necessario intraprendere alcuna azione per rendere multi-tenant queste registrazioni dell'applicazione.

## <a name="update-your-code-to-send-requests-to-common"></a>Aggiornare il codice per l'invio delle richieste all'endpoint /common

In un'applicazione single-tenant le richieste di accesso vengono inviate all'endpoint di accesso del tenant. Ad esempio, per contoso.onmicrosoft.com l'endpoint sarà: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Le richieste inviate all'endpoint del tenant possono eseguire l'accesso degli utenti (o guest) in questo tenant alle applicazioni in questo tenant.

Con un'applicazione multi-tenant, l'applicazione non determina in anticipo il tenant di provienienza dell'utente, pertanto non è possibile inviare richieste all'endpoint del tenant. Al contrario, le richieste vengono inviate a un endpoint che esegue il multiplexing tra tutti i tenant di Azure Active Directory: `https://login.microsoftonline.com/common`

Quando la piattaforma di identità Microsoft riceve una richiesta per l'endpoint/Common, firma l'utente in e, di conseguenza, individua il tenant da cui l'utente fa parte. L'endpoint/Common funziona con tutti i protocolli di autenticazione supportati dal Azure AD: OpenID Connect, OAuth 2,0, SAML 2,0 e WS-Federation.

La risposta di accesso all'applicazione di accesso contiene un token che rappresenta l'utente. Il valore dell'autorità di certificazione nel token indica a un'applicazione il tenant di provenienza dell'utente. Quando l'endpoint /common restituisce una risposta, il valore dell'autorità di certificazione nel token corrisponde al tenant dell'utente.

> [!IMPORTANT]
> L'endpoint /common non è un tenant o un'autorità di certificazione, ma è semplicemente un multiplexer. Quando si usa l'endpoint /common, è necessario aggiornare la logica dell'applicazione per la convalida dei token in modo da tenerne conto.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Aggiornare il codice per gestire più valori dell'autorità di certificazione

Le applicazioni Web e le API Web ricevono e convalidano i token dalla piattaforma di identità Microsoft.

> [!NOTE]
> Mentre le applicazioni client native richiedono e ricevono token dalla piattaforma di identità Microsoft, lo eseguono per inviarli alle API, in cui vengono convalidati. Le applicazioni native non convalidano i token e devono gestirli come opachi.

Verrà ora esaminato il modo in cui un'applicazione convalida i token ricevuti dalla piattaforma di identità Microsoft. Un'applicazione single-tenant prende in genere un valore dell'endpoint come:

```http
    https://login.microsoftonline.com/contoso.onmicrosoft.com
```

e lo usa per creare un URL di metadati, in questo caso OpenID Connect, come:

```http
    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

per scaricare due informazioni critiche che vengono usate per convalidare i token: il valore dell'autorità di certificazione e le chiavi di firma del tenant. Ogni tenant di Azure AD ha un valore univoco dell'autorità di certificazione del formato:

```http
    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

dove il valore GUID è la versione sicura di ridenominazione dell'ID tenant del tenant. Se si seleziona il collegamento dei metadati precedente per `contoso.onmicrosoft.com`, è possibile visualizzare il valore dell'autorità di certificazione nel documento.

Quando un'applicazione single-tenant convalida un token, controlla la firma del token con le chiavi di firma del documento di metadati. Questo test consente di verificare che il valore dell'autorità di certificazione nel token corrisponda a quello che è stato trovato nel documento di metadati.

Poiché l'endpoint /common non corrisponde a un tenant e non è un'autorità di certificazione, il valore dell'autorità di certificazione nei metadati per /common ha un URL basato su modello invece di un valore effettivo:

```http
    https://sts.windows.net/{tenantid}/
```

Un'applicazione multi-tenant non può quindi convalidare i token semplicemente confrontando il valore dell'autorità di certificazione nei metadati con il valore `issuer` nel token. Un'applicazione multi-tenant richiede una logica per decidere quali valori di autorità di certificazione sono validi in base alla parte ID del tenant del valore dell'autorità di certificazione.

Se ad esempio un'applicazione multi-tenant consente l'accesso solo da tenant specifici che hanno eseguito l'accesso ai servizi, è necessario verificare il valore dell'autorità di certificazione o il valore di attestazione `tid` nel token per assicurarsi che sia incluso nel relativo elenco di sottoscrittori. Se un'applicazione multi-tenant gestisce solo persone e non adotta decisioni di accesso basate su tenant, è possibile ignorare il valore dell'autorità di certificazione.

Negli [esempi multi-tenant][AAD-Samples-MT] la convalida dell'autorità di certificazione è disabilitata per consentire l'accesso a qualsiasi tenant di Azure AD.

## <a name="understand-user-and-admin-consent"></a>Informazioni sul consenso dell'utente e dell'amministratore

Per fare in modo che un utente possa accedere a un'applicazione in Azure AD, l'applicazione deve essere rappresentata nel tenant dell'utente. Ciò consente alle organizzazioni di eseguire operazioni come applicare criteri univoci quando gli utenti dal tenant accedono all'applicazione. Per un'applicazione single-tenant, questa registrazione è semplice; quello che si verifica quando si registra l'applicazione nel [portale di Azure][AZURE-portal].

Per un'applicazione multi-tenant, la registrazione iniziale per l'applicazione si trova nel tenant di Azure AD usato dallo sviluppatore. Quando un utente di un tenant diverso accede all'applicazione per la prima volta, Azure AD richiede il consenso alle autorizzazioni richieste dall'applicazione. Se fornisce il consenso, viene creata una rappresentazione dell'applicazione denominata *entità servizio* nel tenant dell'utente ed è possibile procedere con l'accesso. Viene anche creata una delega nella directory che registra il consenso dell'utente all'applicazione. Per informazioni dettagliate sugli oggetti applicazione e ServicePrincipal dell'applicazione e sul modo in cui sono correlati tra loro, vedere oggetti [applicazione e oggetti entità servizio][AAD-App-SP-Objects].

![Illustra il consenso all'app a un solo livello][Consent-Single-Tier]

Questa esperienza di consenso è interessata dalle autorizzazioni richieste dall'applicazione. Microsoft Identity Platform supporta due tipi di autorizzazioni, solo app e Delegate.

* Un'autorizzazione delegata concede a un'applicazione la possibilità di agire come utente connesso per un sottoinsieme di operazioni che l'utente può eseguire. Ad esempio, è possibile concedere a un'applicazione l'autorizzazione delegata per la lettura del calendario dell'utente connesso.
* Un'autorizzazione solo app viene concessa direttamente all'identità dell'applicazione. Ad esempio, è possibile concedere a un'applicazione l'autorizzazione solo app per leggere l'elenco di utenti in un tenant, indipendentemente dall'utente che ha eseguito l'accesso all'applicazione.

Alcune autorizzazioni possono essere concesse da un utente normale, mentre altre richiedono il consenso dell'amministratore tenant.

### <a name="admin-consent"></a>Consenso dell'amministratore

Le autorizzazioni solo app richiedono il consenso dell'amministratore tenant. Se l'applicazione richiede un'autorizzazione solo per app e un utente tenta di accedere all'applicazione, viene visualizzato un messaggio di errore che informa che l'utente non è in grado di fornire il consenso.

Alcune autorizzazioni delegate richiedono anche il consenso dell'amministratore tenant. Ad esempio, il writeback in Azure AD come utente connesso richiede il consenso dell'amministratore tenant. Come le autorizzazioni solo app, se un utente comune tenta di accedere a un'applicazione che richiede un'autorizzazione delegata per la quale è necessario il consenso dell'amministratore, viene visualizzato un errore nell'applicazione. La richiesta del consenso di amministratore da parte di un'applicazione è determinata dallo sviluppatore che ha pubblicato la risorsa ed è presente nella documentazione per la risorsa. La documentazione sulle autorizzazioni per l' [API Microsoft Graph][MSFT-Graph-permission-scopes] indica le autorizzazioni che richiedono il consenso dell'amministratore.

Se l'applicazione usa autorizzazioni che richiedono il consenso dell'amministratore, è necessario che sia presente un movimento, ad esempio un pulsante o un collegamento, con cui l'amministratore può avviare l'azione. La richiesta inviata dall'applicazione per questa azione è la richiesta di autorizzazione OAuth2/OpenID Connect standard, che include anche il parametro `prompt=admin_consent` della stringa di query. Dopo che l'amministratore ha fornito il consenso e l'entità servizio è stata creata nel tenant del cliente, le successive richieste di accesso non richiedono il parametro `prompt=admin_consent`. Poiché l'amministratore ha deciso che le autorizzazioni richieste sono accettabili, a nessun altro utente nel tenant viene richiesto il consenso da questo punto in poi.

Un amministratore tenant può disabilitare la possibilità che gli utenti normali possano il consenso alle applicazioni. Se questa funzionalità è disabilitata, è necessario usare il consenso dell'amministratore come obbligatorio sempre per l'applicazione nel tenant. Se si vuole testare l'applicazione con il consenso dell'utente finale disabilitato, è possibile trovare l'opzione di configurazione nel [portale di Azure][AZURE-portal] nella sezione **[impostazioni utente](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** in **applicazioni aziendali**.

Il parametro `prompt=admin_consent` può essere usato anche dalle applicazioni che richiedono autorizzazioni che non necessitano del consenso dell'amministratore. Questo parametro viene usato, ad esempio, quando l'applicazione richiede un'esperienza in cui il tenant amministratore "si iscrive" una volta e a nessun altro utente viene richiesto il consenso da tale punto in poi.

Se un'applicazione richiede il consenso dell'amministratore e un amministratore accede senza che il parametro `prompt=admin_consent` venga inviato, quando l'amministratore concede correttamente il consenso all'applicazione, il consenso sarà valido **solo per l'account utente**. Gli utenti normali non potranno ancora eseguire l'accesso o fornire il consenso all'applicazione. Questa funzionalità è utile se si vuole assegnare all'amministratore tenant la possibilità di esplorare l'applicazione prima di consentire l'accesso ad altri utenti.

> [!NOTE]
> Alcune applicazioni offrono un'esperienza in cui gli utenti normali sono inizialmente in grado di fornire il consenso e successivamente l'applicazione può coinvolgere l'amministratore e richiedere le autorizzazioni che necessitano del consenso dell'amministratore. Non esiste alcun modo per eseguire questa operazione con la registrazione di un'applicazione v 1.0 attualmente in Azure AD; Tuttavia, l'utilizzo dell'endpoint della piattaforma di identità Microsoft (v 2.0) consente alle applicazioni di richiedere le autorizzazioni in fase di esecuzione anziché al momento della registrazione, che consente questo scenario. Per altre informazioni, vedere [endpoint della piattaforma di identità Microsoft][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Consenso e applicazioni multilivello

L'applicazione può avere più livelli, ognuno rappresentato dalla propria registrazione in Azure AD. Un esempio è un'applicazione nativa che esegue una chiamata a un'API Web o un'applicazione Web che esegue una chiamata a un'API Web. In entrambi i casi, il client (app nativa o app Web) richiede le autorizzazioni per eseguire la chiamata alla risorsa (API Web). Per fare in modo il client venga autorizzato correttamente nel tenant del cliente, tutte le risorse a cui richiede le autorizzazioni devono esistere già nel tenant del cliente. Se questa condizione non viene soddisfatta, Azure AD restituisce un errore indicante che prima deve essere aggiunta la risorsa.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Più livelli in un tenant singolo

Può trattarsi di un problema se l'applicazione logica è costituita da due o più registrazioni di applicazioni, ad esempio un client e una risorsa separati. Come ottenere prima la risorsa nel tenant del cliente? Azure AD si occupa di questo caso, concedendo al client e alla risorsa l'autorizzazione in un unico passaggio. L'utente visualizza la somma totale delle autorizzazioni richieste dal client e dalla risorsa nella pagina del consenso. Per abilitare questo comportamento, la registrazione dell'applicazione della risorsa deve includere l'ID app del client come `knownClientApplications` nel [manifesto dell'applicazione][AAD-App-Manifest]. Ad esempio:

```aad-app-manifest
    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

Ciò viene illustrato in un client nativo multilivello che esegue la chiamata all'esempio di API Web nella sezione [Contenuti correlati](#related-content) alla fine di questo articolo. Il diagramma seguente fornisce una panoramica del consenso per un'app multilivello registrata in un singolo tenant.

![Illustra il consenso all'app client nota multilivello][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Più livelli in tenant multipli

Un caso simile si verifica se i diversi livelli di un'applicazione vengono registrati in tenant diversi. Si consideri, ad esempio, il caso di compilazione di un'applicazione client nativa che chiama l'API di Exchange Online. Per sviluppare l'applicazione nativa e successivamente eseguire l'applicazione nativa nel tenant del cliente, è necessario che sia presente l'entità servizio Exchange Online. In questo caso lo sviluppatore e il cliente devono acquistare Exchange Online per creare l'entità servizio nei tenant.

Se si tratta di un'API creata da un'organizzazione diversa da Microsoft, lo sviluppatore dell'API deve fornire un modo per consentire ai clienti di fornire il consenso dell'applicazione ai tenant dei clienti. La progettazione consigliata è destinata allo sviluppatore di terze parti a compilare l'API in modo che possa fungere anche da client Web per implementare l'iscrizione. Per eseguire questa operazione:

1. Seguire le sezioni precedenti per verificare che l'API implementi i requisiti del codice/registrazione dell'applicazione multi-tenant.
2. Oltre ad esporre gli ambiti o i ruoli dell'API, assicurarsi che la registrazione includa l'autorizzazione "Accedi e leggi profilo utente" (fornita per impostazione predefinita).
3. Implementare una pagina di iscrizione/accesso nel client Web e seguire le linee guida del [consenso dell'amministratore](#admin-consent).
4. Quando l'utente acconsente all'applicazione, vengono creati i collegamenti all'entità servizio e alla delega di consenso nel tenant e l'applicazione nativa può ottenere i token per l'API.

Il diagramma seguente fornisce una panoramica del consenso per un'app multilivello registrata in diversi tenant.

![Illustra il consenso per un'app multilivello multilivello][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Revoca del consenso

Gli utenti e gli amministratori possono revocare il consenso all'applicazione in qualsiasi momento:

* Gli utenti revocano l'accesso alle singole applicazioni rimuovendole dall'elenco [Applicazioni riquadro di accesso][AAD-Access-Panel].
* Per revocare l'accesso alle applicazioni, gli amministratori possono rimuoverli usando la sezione [applicazioni aziendali](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) del [portale di Azure][AZURE-portal].

Se un amministratore fornisce il consenso a un'applicazione per tutti gli utenti in un tenant, gli utenti non possono revocare l'accesso singolarmente. Solo l'amministratore può revocare l'accesso e soltanto per l'intera applicazione.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Applicazioni multi-tenant e memorizzazione nella cache dei token di accesso

Le applicazioni multi-tenant possono anche ottenere i token di accesso per eseguire chiamate alle API protette da Azure AD. Un errore comune quando si usa Active Directory Authentication Library (ADAL) con un'applicazione multi-tenant è quello di richiedere inizialmente un token per un utente tramite /common, ricevere una risposta e quindi richiedere un token successivo per lo stesso utente usando sempre /common. Poiché la risposta da Azure AD proviene da un tenant, non /common, la libreria ADAL memorizza nella cache il token come proveniente dal tenant. Nella chiamata successiva a /common per ottenere un token di accesso per l'utente non è presente la voce della cache e all'utente viene richiesto di accedere di nuovo. Per evitare questo errore della cache, assicurarsi che le chiamate successive per un utente già connesso vengano eseguite all'endpoint del tenant.

## <a name="related-content"></a>Contenuti correlati

* [Esempio di applicazione multi-tenant](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Linee guida sulla personalizzazione per le applicazioni][AAD-App-Branding]
* [Oggetti applicazione e oggetti entità servizio][AAD-App-SP-Objects]
* [Integrazione di applicazioni con Azure Active Directory][AAD-Integrating-Apps]
* [Panoramica del Framework di consenso][AAD-Consent-Overview]
* [Microsoft Graph API permission scopes][MSFT-Graph-permission-scopes] (Ambiti di autorizzazione dell'API Microsoft Graph)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come compilare un'applicazione che consente a un utente di accedere da qualsiasi tenant di Azure AD. Dopo aver abilitato Single Sign-On (SSO) tra l'app e Azure AD, è anche possibile aggiornare l'applicazione per accedere alle API esposte da risorse Microsoft, ad esempio Microsoft 365. In questo modo è possibile offrire un'esperienza personalizzata nell'applicazione, ad esempio mostrando informazioni contestuali per gli utenti, come l'immagine del profilo o il successivo appuntamento nel calendario. Per altre informazioni sull'esecuzione di chiamate API a Azure AD e Microsoft 365 servizi come Exchange, SharePoint, OneDrive, OneNote e altro ancora, vedere [Microsoft Graph API][MSFT-Graph-overview].

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken