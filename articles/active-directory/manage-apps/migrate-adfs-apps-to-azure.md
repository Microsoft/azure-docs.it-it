---
title: Trasferimento dell'autenticazione dell'applicazione da AD FS a Azure Active Directory
description: Informazioni su come usare Azure Active Directory per sostituire Active Directory Federation Services (AD FS), offrendo agli utenti Single Sign-On a tutte le applicazioni.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: ee1d863ccb974b30213179a1aba9e27d5a3a2bda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103418472"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Trasferimento dell'autenticazione dell'applicazione da Azure Active Directory Federation Services ad Azure Active Directory

[Azure Active Directory (Azure ad)](../fundamentals/active-directory-whatis.md) offre una piattaforma di identità universale che fornisce a utenti, partner e clienti una singola identità per accedere alle applicazioni e collaborare da qualsiasi piattaforma e dispositivo. Azure AD dispone [di una suite completa di funzionalità di gestione delle identità](../fundamentals/active-directory-whatis.md). La standardizzazione dell'autenticazione e dell'autorizzazione delle applicazioni per Azure AD offre questi vantaggi.

> [!TIP]
> Questo articolo è stato scritto per i destinatari degli sviluppatori. I Project Manager e gli amministratori che pianificano di spostare un'applicazione in Azure AD devono considerare la possibilità di leggere la [migrazione dell'autenticazione dell'applicazione a Azure ad](migrate-application-authentication-to-azure-active-directory.md).

## <a name="azure-ad-benefits"></a>Vantaggi di Azure AD

Se si dispone di una directory locale che contiene gli account utente, è probabile che siano presenti molte applicazioni a cui gli utenti eseguono l'autenticazione. Ognuna di queste app viene configurata per consentire agli utenti di accedere usando le proprie identità.

Gli utenti possono anche eseguire l'autenticazione direttamente con la Active Directory locale. Active Directory Federation Services (AD FS) è un servizio di identità locale basato su standard. Estende la possibilità di usare la funzionalità di Single Sign-On (SSO) tra partner commerciali attendibili, in modo che gli utenti non debbano accedere separatamente a ogni applicazione. Questa operazione è nota come identità federata.

Molte organizzazioni dispongono di software come un servizio (SaaS) o applicazioni line-of-business personalizzate federate direttamente a AD FS, insieme alle app basate su Microsoft 365 e Azure AD.

  ![Applicazioni connesse direttamente in locale](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> Per aumentare la sicurezza delle applicazioni, l'obiettivo è avere un unico set di controlli di accesso e criteri negli ambienti locali e cloud.

  ![Applicazioni connesse tramite Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Tipi di app da migrare

La migrazione di tutte le autenticazioni di applicazioni a Azure AD è ottimale, in quanto offre un singolo piano di controllo per la gestione delle identità e degli accessi.

Le applicazioni possono usare protocolli moderni o legacy per l'autenticazione. Quando si pianifica la migrazione a Azure AD, è consigliabile eseguire prima la migrazione delle app che usano protocolli di autenticazione moderni, ad esempio SAML e Open ID Connect. Queste app possono essere riconfigurate per l'autenticazione con Azure AD tramite un connettore incorporato dalla raccolta app Azure oppure registrando l'applicazione in Azure AD. Le app che usano protocolli meno recenti possono essere integrate con il proxy di applicazione.

Per altre informazioni, vedere:

* [Uso di Azure ad proxy di applicazione per pubblicare app locali per gli utenti remoti](what-is-application-proxy.md).
* [Cos'è la gestione delle applicazioni?](what-is-application-management.md)
* [Ad FS report attività applicazione per eseguire la migrazione di applicazioni a Azure ad](migrate-adfs-application-activity.md).
* [Monitorare ad FS utilizzando Azure ad Connect Health](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Processo di migrazione

Durante il processo di trasferimento dell'autenticazione dell'app Azure AD, testare le app e la configurazione. Si consiglia di continuare a usare gli ambienti di test esistenti per i test di migrazione quando si passa all'ambiente di produzione. Se un ambiente di test non è attualmente disponibile, è possibile configurarne uno usando [app Azure servizio](https://azure.microsoft.com/services/app-service/) o [macchine virtuali di Azure](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), a seconda dell'architettura dell'applicazione.

È possibile scegliere di configurare un tenant di test Azure AD separato in cui sviluppare le configurazioni dell'app.

Il processo di migrazione potrebbe essere simile al seguente:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>Fase 1-stato attuale: l'app di produzione esegue l'autenticazione con AD FS

  ![Fase 1 della migrazione ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>Fase 2: (facoltativo) puntare un'istanza di test dell'app al tenant del Azure AD di test

Aggiornare la configurazione in modo che punti all'istanza di test dell'app a un tenant di Azure AD di test e apportare le modifiche necessarie. L'app può essere testata con gli utenti nel tenant di test Azure AD. Durante il processo di sviluppo, è possibile usare strumenti come [Fiddler](https://www.telerik.com/fiddler) per confrontare e verificare richieste e risposte.

Se non è possibile configurare un tenant di test separato, ignorare questa fase e puntare un'istanza di test dell'app al tenant di produzione Azure AD come descritto nella fase 3 riportata di seguito.

  ![Fase 2 della migrazione ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>Fase 3: puntare un'istanza di test dell'app al tenant di Azure AD di produzione

Aggiornare la configurazione in modo che punti all'istanza di test dell'app al tenant di produzione Azure AD. È ora possibile eseguire il test con gli utenti nel tenant di produzione. Se necessario, vedere la sezione di questo articolo sulla transizione degli utenti.

  ![Fase 3 della migrazione ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>Fase 4: puntare l'app di produzione al tenant di produzione Azure AD

Aggiornare la configurazione dell'app di produzione in modo che punti al tenant di produzione Azure AD.

  ![Fase 4 della migrazione ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Le app che eseguono l'autenticazione con AD FS possono usare gruppi di Active Directory per le autorizzazioni. Usare [Azure ad Connect Sync](../hybrid/how-to-connect-sync-whatis.md) per sincronizzare i dati di identità tra l'ambiente locale e Azure ad prima di iniziare la migrazione. Verificare i gruppi e l'appartenenza prima della migrazione in modo che sia possibile concedere l'accesso agli stessi utenti durante la migrazione dell'applicazione.

### <a name="line-of-business-apps"></a>App line-of-business

Le app line-of-business sono quelle sviluppate dall'organizzazione o quelle che costituiscono un prodotto in pacchetto standard. Gli esempi includono le app basate su Windows Identity Foundation e le app di SharePoint (non SharePoint Online).

Le app line-of-business che usano OAuth 2,0, OpenID Connect o WS-Federation possono essere integrate con Azure AD come [registrazioni di app](../develop/quickstart-register-app.md). Integrare app personalizzate che usano SAML 2,0 o WS-Federation come [applicazioni non della raccolta](add-application-portal.md) nella pagina applicazioni aziendali della [portale di Azure](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Accesso Single Sign-On basato su SAML

Le app che usano SAML 2,0 per l'autenticazione possono essere configurate per la [Single Sign-on basata su SAML](what-is-single-sign-on.md) (SSO). Con SSO basato su SAML è possibile eseguire il mapping degli utenti a ruoli specifici dell'applicazione in base alle regole definite nelle attestazioni SAML.

Per configurare un'applicazione SaaS per SSO basato su SAML, vedere [Guida introduttiva: configurare Single Sign-on basato su SAML](add-application-portal-setup-sso.md).

  ![Screenshot utente SAML SSO ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Molte applicazioni SaaS hanno un' [esercitazione specifica dell'applicazione](../saas-apps/tutorial-list.md) che illustra la configurazione per l'accesso SSO basato su SAML.

  ![esercitazione sull'app](media/migrate-adfs-apps-to-azure/app-tutorial.png)

La migrazione di alcune app può essere eseguita facilmente. Le app con requisiti più complessi, ad esempio attestazioni personalizzate, possono richiedere una configurazione aggiuntiva in Azure AD e/o Azure AD Connect. Per informazioni sui mapping di attestazioni supportati, vedere [procedura: personalizzare le attestazioni emesse nei token per un'app specifica in un tenant (anteprima)](../develop/active-directory-claims-mapping.md).

Quando si esegue il mapping degli attributi, tenere presenti le limitazioni seguenti:

* Non tutti gli attributi che possono essere emessi in AD FS vengono visualizzati in Azure AD come attributi da emettere ai token SAML, anche se tali attributi sono sincronizzati. Quando si modifica l'attributo, l'elenco a discesa **valore** Mostra i diversi attributi disponibili in Azure ad. Controllare [Azure ad Connect](../hybrid/how-to-connect-sync-whatis.md) la configurazione degli argomenti di sincronizzazione per assicurarsi che un attributo obbligatorio, ad esempio **sAMAccountName**, venga sincronizzato con Azure ad. È possibile utilizzare gli attributi di estensione per creare qualsiasi attestazione che non faccia parte dello schema utente standard in Azure AD.
* Negli scenari più comuni, per un'app sono necessarie solo l'attestazione **NameID** e altre attestazioni dell'ID utente comuni. Per determinare se sono necessarie attestazioni aggiuntive, esaminare le attestazioni che si stanno inviando AD FS.
* Non tutte le attestazioni possono essere emesse perché alcune attestazioni sono protette in Azure AD.
* La possibilità di usare token SAML crittografati è ora disponibile in anteprima. Vedere [procedura: personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](../develop/active-directory-saml-claims-customization.md).

### <a name="software-as-a-service-saas-apps"></a>App SaaS (software as a Service)

Se gli utenti eseguono l'accesso ad app SaaS come Salesforce, ServiceNow o giorni lavorativi e sono integrati con AD FS, si usa l'accesso federato per le app SaaS.

La maggior parte delle applicazioni SaaS può essere configurata in Azure AD. Microsoft dispone di molte connessioni preconfigurate alle app SaaS nella  [raccolta di app Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), semplificando così la transizione. Le applicazioni SAML 2,0 possono essere integrate con Azure AD tramite la raccolta di app Azure AD o come [applicazioni non della raccolta](add-application-portal.md).

Le app che usano OAuth 2,0 o OpenID Connect possono essere integrate in modo analogo con Azure AD come [registrazioni di app](../develop/quickstart-register-app.md). Le app che usano protocolli legacy possono usare [Azure ad proxy di applicazione](application-proxy.md) per l'autenticazione con Azure ad.

Per eventuali problemi relativi all'onboarding delle app SaaS, è possibile contattare l'alias del supporto per l' [integrazione dell'applicazione SaaS](mailto:SaaSApplicationIntegrations@service.microsoft.com).

### <a name="saml-signing-certificates-for-sso"></a>Certificati di firma SAML per SSO

I certificati di firma rappresentano una parte importante di qualsiasi distribuzione SSO. Azure AD crea i certificati di firma per stabilire SSO federato basato su SAML per le applicazioni SaaS. Una volta aggiunte le applicazioni raccolta o non della raccolta, l'applicazione aggiunta verrà configurata utilizzando l'opzione federativo SSO. Vedere [Manage Certificates for federate Single Sign-on in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

### <a name="saml-token-encryption"></a>Crittografia di token SAML

Sia AD FS che Azure AD forniscono la crittografia dei token, ovvero la possibilità di crittografare le asserzioni di sicurezza SAML che passano ad applicazioni. Le asserzioni vengono crittografate con una chiave pubblica e decrittografate dall'applicazione ricevente con la chiave privata corrispondente. Quando si configura la crittografia dei token, caricare i file di certificato X. 509 per fornire le chiavi pubbliche.

Per informazioni su Azure AD la crittografia del token SAML e su come configurarla, vedere [procedura: configurare Azure ad crittografia dei token SAML](howto-saml-token-encryption.md).  

> [!NOTE]
> La crittografia dei token è una funzionalità Premium di Azure Active Directory (Azure AD). Per altre informazioni sulle edizioni, le funzionalità e i prezzi di Azure AD, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>App e configurazioni che possono essere spostate oggi

Attualmente è possibile spostare facilmente le app SAML 2.0 che usano il set standard di elementi di configurazione e attestazioni. Questi elementi standard sono:

* Nome entità utente
* Indirizzo di posta elettronica
* Nome specificato
* Surname
* Un attributo alternativo come **NameID** SAML, ad esempio l'attributo Mail, il prefisso di posta elettronica, l'ID dipendente o gli attributi dell'estensione 1-15 di Azure AD oppure l'attributo **SamAccountName** locale. Per altre informazioni, vedere [Modifica dell'attestazione NameIdentifier](../develop/active-directory-saml-claims-customization.md).
* Attestazioni personalizzate.

Di seguito sono riportate le procedure di configurazione aggiuntive per eseguire la migrazione a Azure AD:

* Le regole di autorizzazione personalizzata o multi-factor authentication in AD FS. È possibile configurarli usando la funzionalità di [accesso condizionale Azure ad](../conditional-access/overview.md) .
* App con più endpoint URL di risposta. È possibile configurarli in Azure AD tramite PowerShell o l'interfaccia portale di Azure.
* App WS-Federation come le app SharePoint che richiedono token SAML versione 1.1, È possibile configurarli manualmente usando PowerShell. È anche possibile aggiungere un modello generico pre-integrato per le applicazioni SharePoint e SAML 1,1 dalla raccolta. Il protocollo SAML 2,0 è supportato.
* Regole complesse per le trasformazioni di rilascio delle attestazioni. Per informazioni sui mapping di attestazioni supportati, vedere:
   *  [Mapping delle attestazioni in Azure Active Directory](../develop/active-directory-claims-mapping.md).
   * [Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>App e configurazioni attualmente non supportate in Azure AD

Attualmente non è possibile eseguire la migrazione delle app che richiedono determinate funzionalità.

#### <a name="protocol-capabilities"></a>Funzionalità del protocollo

Non è possibile eseguire subito la migrazione di app che richiedono le seguenti funzionalità del protocollo:

* Supporto per il modello di WS-Trust ActAs
* Risoluzione artefatto SAML
* Verifica della firma delle richieste SAML firmate
  > [!Note]
  > Le richieste firmate vengono accettate, ma la firma non viene verificata.

  Dato che Azure AD restituisce solo il token agli endpoint preconfigurati nell'applicazione, la verifica della firma probabilmente non è necessaria nella maggior parte dei casi.

#### <a name="claims-in-token-capabilities"></a>Attestazioni nelle funzionalità del token

Non è possibile migrare attualmente le app che richiedono le seguenti attestazioni nelle funzionalità del token.

* Attestazioni da archivi di attributi diversi dalla directory Azure AD, a meno che i dati non vengano sincronizzati con Azure AD. Per ulteriori informazioni, vedere la [Panoramica dell'API di sincronizzazione Azure ad](/graph/api/resources/synchronization-overview?view=graph-rest-beta).
* Rilascio di attributi multivalore di directory. Ad esempio, in questo momento non è possibile emettere un'attestazione multivalore per gli indirizzi proxy.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Mappare le impostazioni dell'app da AD FS a Azure AD

La migrazione richiede la valutazione del modo in cui l'applicazione è configurata in locale e quindi il mapping di tale configurazione a Azure AD. AD FS e Azure AD presentano un funzionamento simile, di conseguenza i concetti relativi alla configurazione degli identificatori e degli URL di accesso, disconnessione e trust si applicano in entrambi i casi. Documentare le impostazioni di configurazione AD FS delle applicazioni in modo che sia possibile configurarle facilmente in Azure AD.

### <a name="map-app-configuration-settings"></a>Mappare le impostazioni di configurazione dell'app

La tabella seguente descrive alcune delle impostazioni di mapping più comuni tra un AD FS attendibilità del componente per Azure AD applicazione aziendale:

* AD FS: trovare l'impostazione nella AD FS attendibilità del componente per l'app. Fare clic con il pulsante destro del mouse sul relying party e scegliere Proprietà.
* Azure AD: l'impostazione viene configurata all'interno [portale di Azure](https://portal.azure.com/) nelle proprietà SSO di ogni applicazione.

| Impostazione di configurazione| AD FS| Come configurare in Azure AD| Token SAML |
| - | - | - | - |
| **URL di accesso dell'app** <p>URL che consente all'utente di accedere all'app in un flusso SAML avviato da un provider di servizi (SP).| N/D| Apri configurazione SAML di base da accesso basato su SAML| N/D |
| **URL di risposta dell'app** <p>URL dell'app dal punto di vista del provider di identità (IdP). Il provider di identità invia l'utente e il token qui dopo che l'utente ha eseguito l'accesso al provider di identità.  Questa operazione è nota anche come **endpoint consumer di asserzione SAML**.| Selezionare la scheda **endpoint**| Apri configurazione SAML di base da accesso basato su SAML| Elemento Destination nel token SAML. Valore di esempio: `https://contoso.my.salesforce.com` |
| **URL di disconnessione dell'app** <p>Si tratta dell'URL a cui vengono inviate le richieste di pulizia della disconnessione quando un utente si disconnette da un'app. Il provider di identità Invia la richiesta di disconnessione dell'utente anche da tutte le altre app.| Selezionare la scheda **endpoint**| Apri configurazione SAML di base da accesso basato su SAML| N/D |
| **Identificatore dell'app** <p>Si tratta dell'identificatore dell'app dal punto di vista dell'IdP. Come identificatore viene usato spesso il valore dell'URL di accesso, ma non sempre.  A volte l'app chiama questo "ID entità".| Selezionare la scheda **identificatori**|Apri configurazione SAML di base da accesso basato su SAML| Esegue il mapping all'elemento **audience** nel token SAML. |
| **Metadati di federazione dell'app** <p>Si tratta del percorso dei metadati di Federazione dell'app. Viene usata dal provider di identità per aggiornare automaticamente specifiche impostazioni di configurazione come gli endpoint o i certificati di crittografia.| Selezionare la scheda **monitoraggio**| N/D. Azure AD non supporta l'utilizzo diretto dei metadati di Federazione dell'applicazione. È possibile importare manualmente i metadati di Federazione.| N/D |
| **ID Identificatore/nome utente** <p>Attributo usato per indicare in modo univoco l'identità utente da Azure AD o AD FS all'app.  Questo attributo è in genere l'UPN o l'indirizzo di posta elettronica dell'utente.| Regole attestazione. Nella maggior parte dei casi, la regola attestazioni rilascia un'attestazione con un tipo che termina con **NameIdentifier**.| È possibile trovare l'identificatore sotto l'intestazione **attributi utente e attestazioni**. Per impostazione predefinita, viene usato il nome UPN| Esegue il mapping all'elemento **NameID** nel token SAML. |
| **Altre attestazioni** <p>Esempi di altre informazioni sulle attestazioni inviate comunemente dal provider di identità all'app includono il nome, il cognome, l'indirizzo di posta elettronica e l'appartenenza al gruppo.| In AD FS sono riportate come altre regole attestazioni per la relying party.| È possibile trovare l'identificatore sotto l'intestazione **attributi utente & attestazioni**. Selezionare **Visualizza e modifica tutti gli altri attributi utente**.| N/D |

### <a name="map-identity-provider-idp-settings"></a>Impostazioni del provider di identità mappa (IdP)

Configurare le applicazioni in modo che puntino a Azure AD rispetto AD FS per SSO. In questo caso, ci stiamo concentrando sulle app SaaS che usano il protocollo SAML. Tuttavia, questo concetto si estende anche alle app line-of-business personalizzate.

> [!NOTE]
> I valori di configurazione per Azure AD seguono il modello in cui l'ID tenant di Azure sostituisce {Tenant-ID} e l'ID applicazione sostituisce {Application-ID}. Queste informazioni sono disponibili nella [portale di Azure](https://portal.azure.com/) in **Proprietà Azure Active Directory >**:

* Selezionare ID directory per visualizzare l'ID tenant.
* Selezionare ID applicazione per visualizzare l'ID applicazione.

 A livello generale, eseguire il mapping dei seguenti elementi chiave di configurazione delle app SaaS per Azure AD.

| Elemento| Valore di configurazione |
| - | - |
| Autorità emittente del provider di identità| https: \/ /STS.Windows.NET/{Tenant-ID}/ |
| URL di accesso del provider di identità| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| URL di disconnessione del provider di identità| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Posizione dei metadati di Federazione| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>Mappare le impostazioni SSO per le app SaaS

Le app SaaS devono comprendere dove inviare le richieste di autenticazione e come convalidare i token ricevuti. La tabella seguente descrive gli elementi per configurare le impostazioni SSO nell'app e i relativi valori o posizioni all'interno AD FS e Azure AD

| Impostazione di configurazione| AD FS| Come configurare in Azure AD |
| - | - | - |
| **URL accesso IdP** <p>URL di accesso del provider di identità dal punto di vista dell'app, in cui l'utente viene reindirizzato per l'accesso.| L'URL di accesso AD FS è il nome del servizio federativo AD FS seguito da "/adfs/ls/." <p>ad esempio `https://fs.contoso.com/adfs/ls/`| Sostituire {Tenant-ID} con l'ID tenant. <p> Per le app che usano il protocollo SAML-P: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Per le app che usano il protocollo WS-Federation: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **URL di disconnessione IdP**<p>URL di disconnessione del provider di identità dal punto di vista dell'app, in cui l'utente viene reindirizzato quando sceglie di disconnettersi dall'app.| L'URL di disconnessione è uguale all'URL di accesso oppure lo stesso URL con l'aggiunta di "WA = wsignout 1.0". ad esempio `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Sostituire {Tenant-ID} con l'ID tenant.<p>Per le app che usano il protocollo SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Per le app che usano il protocollo WS-Federation: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certificato per la firma di token**<p>IdP usa la chiave privata del certificato per firmare i token emessi. Verifica che il token provenga dallo stesso provider di identità per cui è stato configurato il trust nell'app.| Il certificato per la firma di token di AD FS si trova in **Certificati** in Gestione AD FS.| Trovarlo nella portale di Azure nelle **proprietà Single Sign-on** dell'applicazione sotto l'intestazione certificato di **firma SAML**. da dove può essere scaricato per il caricamento nell'app.  <p>Se l'applicazione dispone di più di un certificato, è possibile trovare tutti i certificati nel file XML dei metadati di Federazione. |
| **Identificatore/"emittente"**<p>Identificatore dell'IdP dal punto di vista dell'app, talvolta denominato "ID autorità emittente".<p>Nel token SAML, il valore viene visualizzato come elemento Issuer.| L'identificatore per AD FS è in genere l'identificatore del servizio federativo nella gestione AD FS in **service > modificare le proprietà servizio federativo**. ad esempio `http://fs.contoso.com/adfs/services/trust`| Sostituire {Tenant-ID} con l'ID tenant.<p>https: \/ /STS.Windows.NET/{Tenant-ID}/ |
| **Metadati federativi IdP**<p>Posizione dei metadati di federazione disponibili pubblicamente del provider di identità. Alcune app usano i metadati di federazione come alternativa alla configurazione di URL, identificatore e certificato per la firma di token eseguita singolarmente dall'amministratore.| Trovare l'URL dei metadati della Federazione AD FS in gestione AD FS in **Service > endpoint > metadati di tipo >: metadati federativi**. ad esempio `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Il valore corrispondente per Azure AD segue il modello [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Sostituire {TenantDomainName} con il nome del tenant nel formato "contoso.onmicrosoft.com".   <p>Per altre informazioni, vedere [Metadati della federazione](../azuread-dev/azure-ad-federation-metadata.md). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Rappresentano i criteri di sicurezza AD FS in Azure AD

Quando si trasferisce l'autenticazione dell'app a Azure AD, è possibile creare mapping tra i criteri di sicurezza esistenti e le varianti equivalenti o alternative disponibili nell'Azure AD. Garantendo che questi mapping possano essere eseguiti rispettando gli standard di sicurezza richiesti dai proprietari dell'app, il resto della migrazione delle app risulta notevolmente più semplice.

Per ogni esempio di regola, viene illustrato l'aspetto della regola in AD FS, il codice equivalente del linguaggio della regola AD FS e il modo in cui viene eseguito il mapping a Azure AD.

### <a name="map-authorization-rules"></a>Mapping delle regole di autorizzazione

Di seguito sono riportati alcuni esempi di diversi tipi di regole di autorizzazione in AD FS e il modo in cui eseguirne il mapping a Azure AD.

#### <a name="example-1-permit-access-to-all-users"></a>Esempio 1: consentire l'accesso a tutti gli utenti

Consentire l'accesso a tutti gli utenti in AD FS:

  ![Screenshot mostra la finestra di dialogo Configura singolo Sign-On con SAML.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

Viene eseguito il mapping a Azure AD in uno dei modi seguenti:

1. Imposta **assegnazione utente obbligatoria** su **No**.

    ![modificare i criteri di controllo di accesso per le app SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > Per impostare l' **assegnazione dell'utente** su **Sì** è necessario che gli utenti siano assegnati all'applicazione per ottenere l'accesso. Se impostato su **No**, tutti gli utenti hanno accesso. Questa opzione non controlla ciò che gli utenti vedono nell'esperienza **app personali** .

1. Nella **scheda utenti e gruppi** assegnare l'applicazione al gruppo **tutti gli utenti** automatico. È necessario [abilitare i gruppi dinamici](../enterprise-users/groups-create-rule.md) nel tenant di Azure ad per rendere disponibile il gruppo **tutti gli utenti** predefinito.

    ![App SaaS personali in Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>Esempio 2: consentire un gruppo in modo esplicito

Autorizzazione esplicita per il gruppo in AD FS:

  ![Screenshot mostra la finestra di dialogo Modifica regola per la regola di attestazione Consenti Domain Admins.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

Per eseguire il mapping di questa regola a Azure AD:

1. Nella [portale di Azure](https://portal.azure.com/) [creare un gruppo](../fundamentals/active-directory-groups-create-azure-portal.md) di utenti che corrisponda al gruppo di utenti da ad FS.
1. Assegnare le autorizzazioni dell'app al gruppo:

    ![Aggiungi assegnazione ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>Esempio 3: autorizzare un utente specifico

Autorizzazione utente esplicita in AD FS:

  ![Screenshot mostra la finestra di dialogo Modifica regola per la regola Consenti attestazioni utente specifiche con un tipo di attestazione in ingresso di primario S I D.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Per eseguire il mapping di questa regola a Azure AD:

* Nella [portale di Azure](https://portal.azure.com/)aggiungere un utente all'app tramite la scheda Aggiungi assegnazione dell'app, come illustrato di seguito:

    ![App SaaS personali in Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Mappare le regole di autenticazione a più fattori

Una distribuzione locale di [multi-factor authentication (](../authentication/concept-mfa-howitworks.md) multi-factor authentication) e di ad FS continua a funzionare dopo la migrazione perché si è federati con ad FS. È tuttavia consigliabile eseguire la migrazione alle funzionalità di autenticazione a più fattori predefinite di Azure legate ai flussi di lavoro di accesso condizionale di Azure AD.

Di seguito sono riportati alcuni esempi di tipi di regole di autenticazione a più fattori in AD FS e il modo in cui è possibile eseguirne il mapping ai Azure AD in base a condizioni diverse.

Impostazioni delle regole di autenticazione a più fattori in AD FS:

  ![Screenshot mostra le condizioni per Azure A D nel portale di Azure.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Esempio 1: applicare l'autenticazione a più fattori in base a utenti/gruppi

Il selettore utenti/gruppi è una regola che consente di applicare l'autenticazione a più fattori in un singolo gruppo (SID di gruppo) o per utente (SID primario). Oltre alle assegnazioni di utenti/gruppi, tutte le caselle di controllo aggiuntive nella AD FS funzione dell'interfaccia utente di configurazione dell'autenticazione a più fattori come regole aggiuntive che vengono valutate dopo l'applicazione della regola utenti/gruppi.

Specificare le regole di autenticazione a più fattori per un utente o un gruppo in Azure AD:

1. Creare [nuovi criteri di accesso condizionale](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Selezionare **Assegnazioni**. Aggiungere gli utenti o i gruppi per cui si vuole applicare l'autenticazione a più fattori.
1. Configurare le opzioni di **controllo di accesso** come illustrato di seguito:

    ‎![Screenshot che mostra il riquadro di concessione a cui è possibile concedere l'accesso.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Esempio 2: applicare l'autenticazione a più fattori per i dispositivi non registrati

Specificare le regole di autenticazione a più fattori per i dispositivi non registrati in Azure AD:

1. Creare [nuovi criteri di accesso condizionale](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Impostare le **assegnazioni** su **tutti gli utenti**.
1. Configurare le opzioni di **controllo di accesso** come illustrato di seguito:

    ![Screenshot mostra il riquadro Concedi in cui è possibile concedere l'accesso e specificare altre restrizioni.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

Quando si imposta l'opzione **per più controlli** in modo da **richiedere uno dei controlli selezionati**, significa che se una delle condizioni specificate dalla casella di controllo viene soddisfatta dall'utente, all'utente viene concesso l'accesso all'app.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Esempio 3: applicare l'autenticazione a più fattori in base alla posizione

Specificare le regole di autenticazione a più fattori in base alla posizione di un utente in Azure AD:

1. Creare [nuovi criteri di accesso condizionale](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Impostare le **assegnazioni** su **tutti gli utenti**.
1. [Configurare le località denominate in Azure ad](../reports-monitoring/quickstart-configure-named-locations.md). In caso contrario, la Federazione dall'interno della rete aziendale è attendibile.
1. Configurare le **regole delle condizioni** per specificare i percorsi per i quali si vuole applicare l'autenticazione a più fattori.

    ![Screenshot mostra il riquadro percorsi per le regole delle condizioni.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. Configurare le opzioni di **controllo di accesso** come illustrato di seguito:

    ![Mappare i criteri di controllo di accesso](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Mappa creare attributi come regola attestazioni

Crea attributi come regola attestazioni in AD FS:

  ![Screenshot mostra la finestra di dialogo Modifica regola per la creazione di attributi come attestazioni.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

Per eseguire il mapping della regola a Azure AD:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare **applicazioni aziendali** e quindi **Single Sign-on** per visualizzare la configurazione dell'accesso basato su SAML:

    ![Screenshot che mostra la pagina Single Sign-on per l'applicazione aziendale.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Selezionare **modifica** (evidenziato) per modificare gli attributi:

    ![Questa è la pagina per modificare attributi e attestazioni utente](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Mappare i criteri di controllo degli accessi predefiniti

Criteri di controllo degli accessi predefiniti in AD FS 2016:

  ![Controllo degli accessi Azure AD incorporato](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Per implementare i criteri predefiniti in Azure AD, usare un [nuovo criterio di accesso condizionale](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) e configurare i controlli di accesso oppure usare la finestra di progettazione dei criteri personalizzati in ad FS 2016 per configurare i criteri di controllo di accesso. Nell'Editor regole è presente un elenco completo delle opzioni Consenti e ad eccezione che consentono di apportare tutti i tipi di permutazioni.

  ![Criteri di controllo degli accessi Azure AD](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

In questa tabella sono state elencate alcune opzioni utili ed escluse e il modo in cui vengono mappate a Azure AD.

| Opzione | Come configurare l'opzione Consenti in Azure AD?| Come configurare l'opzione except in Azure AD? |
| - | - | - |
| Da specifico rete| Esegue il mapping alla [località denominata](../reports-monitoring/quickstart-configure-named-locations.md) in Azure ad| Usare l'opzione **Escludi** per [percorsi attendibili](../conditional-access/location-condition.md) |
| Da specifico gruppi| [Impostare un'assegnazione di utenti/gruppi](assign-user-or-group-access-portal.md)| Usare l'opzione **Escludi** in utenti e gruppi |
| Da dispositivi con livello di attendibilità specifico| Impostarlo dal controllo **dello stato del dispositivo** in assegnazioni-condizioni >| Usare l'opzione **Escludi** in condizione stato dispositivo e Includi **tutti i dispositivi** |
| Con attestazioni specifiche nella richiesta| Non è possibile eseguire la migrazione di questa impostazione| Non è possibile eseguire la migrazione di questa impostazione |

Di seguito è riportato un esempio di come configurare l'opzione di esclusione per i percorsi attendibili nella portale di Azure:

  ![Screenshot del mapping dei criteri di controllo di accesso](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Transizione degli utenti da AD FS a Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Sincronizzare i gruppi di AD FS in Azure AD

Quando si mappano le regole di autorizzazione, le app che eseguono l'autenticazione con AD FS possono usare Active Directory gruppi per le autorizzazioni. In tal caso, usare [Azure ad Connect](https://go.microsoft.com/fwlink/?LinkId=615771) per sincronizzare questi gruppi con Azure ad prima di eseguire la migrazione delle applicazioni. Assicurarsi di verificare i gruppi e l'appartenenza prima della migrazione in modo che sia possibile concedere l'accesso agli stessi utenti durante la migrazione dell'applicazione.

Per ulteriori informazioni, vedere [prerequisiti per l'utilizzo degli attributi di gruppo sincronizzati da Active Directory](../hybrid/how-to-connect-fed-group-claims.md).

### <a name="set-up-user-self-provisioning"></a>Configurare il provisioning automatico degli utenti

Alcune applicazioni SaaS supportano la possibilità di eseguire il provisioning automatico degli utenti al primo accesso all'applicazione. In Azure AD, il provisioning delle app si riferisce alla creazione automatica di identità e ruoli utente nelle applicazioni cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) a cui gli utenti devono accedere. Gli utenti di cui viene eseguita la migrazione dispongono già di un account nell'applicazione SaaS. Eventuali nuovi utenti aggiunti dopo la migrazione devono essere sottoposti a provisioning. Testare il [provisioning delle app Saas](../app-provisioning/user-provisioning.md) dopo la migrazione dell'applicazione.

### <a name="sync-external-users-in-azure-ad"></a>Sincronizzare utenti esterni in Azure AD

È possibile configurare gli utenti esterni esistenti in questi due modi AD FS:

* **Utenti esterni con un account locale all'interno dell'organizzazione**: è possibile continuare a usare questi account nello stesso modo in cui funzionano gli account utente interni. Questi account utente esterni hanno un nome di principio all'interno dell'organizzazione, anche se l'indirizzo di posta elettronica dell'account può puntare esternamente. Quando si procede con la migrazione, è possibile sfruttare i vantaggi offerti da [Azure ad offerte B2B](../external-identities/what-is-b2b.md) eseguendo la migrazione di questi utenti in modo da usare la propria identità aziendale quando è disponibile un'identità di questo tipo. In questo modo viene semplificato il processo di accesso per tali utenti, in quanto vengono spesso connessi con il proprio account di accesso aziendale. Anche l'amministrazione dell'organizzazione è più semplice, non è necessario gestire gli account per gli utenti esterni.
* **Identità esterne federate**: se si sta eseguendo la Federazione con un'organizzazione esterna, è possibile adottare alcuni approcci:
  * [Aggiungere Azure Active Directory utenti di collaborazione B2B nel portale di Azure](../external-identities/add-users-administrator.md). È possibile inviare in modo proattivo gli inviti di collaborazione B2B dal portale di amministrazione di Azure AD all'organizzazione partner per i singoli membri per continuare a usare le app e le risorse a cui sono abituati.
  * [Creare un flusso di lavoro di iscrizione B2B self-service](../external-identities/self-service-portal.md) che genera una richiesta per i singoli utenti dell'organizzazione partner usando l'API di invito B2B.

Indipendentemente dal modo in cui vengono configurati gli utenti esterni esistenti, è probabile che dispongano delle autorizzazioni associate al proprio account, in appartenenza al gruppo o autorizzazioni specifiche. Valutare se è necessario eseguire la migrazione o la pulizia di queste autorizzazioni. Gli account all'interno dell'organizzazione che rappresentano un utente esterno devono essere disabilitati dopo che l'utente è stato migrato a un'identità esterna. Il processo di migrazione deve essere discusso con i partner aziendali, in quanto potrebbe verificarsi un'interruzione nella capacità di connettersi alle risorse.

## <a name="migrate-and-test-your-apps"></a>Eseguire la migrazione e il test delle app

Seguire il processo di migrazione descritto in dettaglio in questo articolo. Passare quindi alla [portale di Azure](https://aad.portal.azure.com/) per verificare se la migrazione ha avuto esito positivo.

Seguire queste istruzioni:

1. Selezionare **applicazioni aziendali**  >  **tutte le applicazioni** e trovare l'app nell'elenco.
1. Selezionare **Gestisci**  >  **utenti e gruppi** per assegnare almeno un utente o un gruppo all'app.
1. Selezionare **Gestisci**  >  **accesso condizionale**. Esaminare l'elenco dei criteri e assicurarsi che non si stia bloccando l'accesso all'applicazione con [criteri di accesso condizionale](../conditional-access/overview.md).

A seconda di come si configura l'app, verificare che SSO funzioni correttamente.

| Tipo di autenticazione| Test |
| :- | :- |
| OAuth/OpenID Connect| Selezionare **applicazioni aziendali > le autorizzazioni** e assicurarsi di avere acconsentito all'applicazione nelle impostazioni utente per l'app.|
| SSO basato su SAML | Usare il pulsante [test impostazioni SAML](debug-saml-sso-issues.md) disponibile in **Single Sign-on**. |
| Password-Based SSO |  Scaricare e installare l'estensione per l' [accesso sicuro app](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [](../user-help/my-apps-portal-end-user-access.md). Questa estensione consente di avviare tutte le app cloud dell'organizzazione che richiedono l'uso di un processo SSO. |
| Proxy dell'applicazione | Verificare che il connettore sia in esecuzione e che sia stato assegnato all'applicazione. Per ulteriore assistenza, vedere la [Guida alla risoluzione dei problemi del proxy di applicazione](application-proxy-troubleshoot.md) . |

> [!NOTE]
> I cookie dell'ambiente AD FS precedente vengono mantenuti nei computer degli utenti. Questi cookie potrebbero causare problemi con la migrazione, in quanto gli utenti possono essere indirizzati al vecchio ambiente di accesso AD FS rispetto al nuovo account di accesso Azure AD. Potrebbe essere necessario cancellare manualmente i cookie del browser utente o utilizzando uno script. È anche possibile usare la System Center Configuration Manager o una piattaforma simile.

### <a name="troubleshoot"></a>Risolvere problemi

Se sono presenti errori del test delle applicazioni migrate, la risoluzione dei problemi potrebbe essere il primo passaggio prima di eseguire il fallback alle relying party AD FS esistenti. Vedere [come eseguire il debug di Single Sign-on basate su SAML per le applicazioni in Azure Active Directory](debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Rollback migrazione

Se la migrazione non riesce, è consigliabile lasciare le relying party esistenti nei server di AD FS e rimuovere l'accesso alle relying party. Questo consente un fallback rapido, se necessario, durante la distribuzione.

### <a name="employee-communication"></a>Comunicazione dei dipendenti

Sebbene la finestra di interruzione pianificata possa essere minima, è comunque opportuno pianificare la comunicazione proattiva di questi intervalli di tempo in modo proattivo ai dipendenti passando da AD FS a Azure AD. Assicurarsi che l'esperienza dell'app includa un pulsante di feedback o puntatori al supporto tecnico per individuare eventuali problemi.

Al termine della distribuzione, è possibile informare gli utenti della corretta distribuzione e ricordare tutti i passaggi da eseguire.

* Indicare agli utenti di usare le [app personali](https://myapps.microsoft.com) per accedere a tutte le applicazioni migrate.
* Ricordare agli utenti che potrebbero dover aggiornare le impostazioni dell'autenticazione a più fattori.
* Se viene distribuita Self-Service la reimpostazione della password, gli utenti potrebbero dover aggiornare o verificare i metodi di autenticazione. Vedere [modelli](https://aka.ms/mfatemplates) di comunicazione dell'utente finale di multi-factor authentication e [SSPR](https://aka.ms/ssprtemplates) .

### <a name="external-user-communication"></a>Comunicazione utente esterna

Questo gruppo di utenti è in genere il più importante in caso di problemi. Ciò è particolarmente vero se il comportamento di sicurezza impone un set di regole di accesso condizionale o profili di rischio per partner esterni. Assicurarsi che i partner esterni siano consapevoli della pianificazione della migrazione nel cloud e abbiano un intervallo di tempo durante il quale sono invitati a partecipare a una distribuzione pilota che testa tutti i flussi specifici della collaborazione esterna. Infine, assicurarsi di disporre di un modo per accedere al supporto tecnico in caso di problemi.

## <a name="next-steps"></a>Passaggi successivi

* Leggere  [migrazione dell'autenticazione dell'applicazione a Azure ad](https://aka.ms/migrateapps/whitepaper).
* Configurare [l'accesso condizionale](../conditional-access/overview.md) [e](../authentication/concept-mfa-howitworks.md)l'autenticazione a più fattori.
* Provare a eseguire un esempio di codice dettagliato:[ad FS per Azure ad il PlayBook per la migrazione delle applicazioni per gli sviluppatori](https://aka.ms/adfsplaybook).
