---
title: 'Azure AD Connect: eseguire la migrazione dalla Federazione a pH per Azure AD | Microsoft Docs'
description: Questo articolo include informazioni sulla migrazione di un ambiente ibrido di gestione delle identità dalla federazione alla sincronizzazione dell'hash delle password.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dca888bf9e3dc75e80764949a11d95efe3514635
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861817"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Eseguire la migrazione dalla federazione alla sincronizzazione degli hash delle password per Azure Active Directory

Questo articolo descrive come spostare i domini dell'organizzazione da Active Directory Federation Services (AD FS) alla sincronizzazione dell'hash delle password.

> [!NOTE]
> Per cambiare metodo di autenticazione sono necessarie attività di pianificazione, test e possibilmente tempo di inattività. L'implementazione di gestione [temporanea](how-to-connect-staged-rollout.md) fornisce un modo alternativo per testare e migrare gradualmente dalla Federazione all'autenticazione cloud usando la sincronizzazione dell'hash delle password.
>
> Se si prevede di usare l'implementazione di gestione temporanea, è necessario ricordare di disattivare le funzionalità di implementazione di gestione temporanea dopo aver terminato il ritaglio.  Per altre informazioni, vedere [eseguire la migrazione all'autenticazione cloud usando l'implementazione di gestione temporanea](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Prerequisiti per la migrazione alla sincronizzazione dell'hash delle password

I prerequisiti seguenti sono necessari per passare dall'uso di AD FS all'uso della sincronizzazione dell'hash delle password.


### <a name="update-azure-ad-connect"></a>Aggiornare Azure AD Connect

Come requisito minimo, per eseguire i passaggi per la migrazione alla sincronizzazione dell'hash delle password, è necessario disporre di [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Questa versione contiene modifiche significative alla modalità di conversione delle informazioni di accesso e riduce a minuti il tempo complessivo per la migrazione dalla federazione all'autenticazione cloud, che in precedenza poteva richiedere anche ore.


> [!IMPORTANT]
> In documenti, strumenti e blog non aggiornati potrebbe essere indicato che la conversione degli utenti è obbligatoria quando si convertono i domini dall'identità federata all'identità gestita. La *conversione degli utenti* non è più obbligatoria. Microsoft sta aggiornando la documentazione e gli strumenti in base a questa modifica.

Per aggiornare Azure AD Connect, completare i passaggi descritti in [Azure ad Connect: eseguire l'aggiornamento alla versione più recente](./how-to-upgrade-previous-version.md).

### <a name="password-hash-synchronization-required-permissions"></a>Autorizzazioni necessarie per la sincronizzazione degli hash delle password

È possibile configurare Azure AD Connect usando le impostazioni rapide o un'installazione personalizzata. Se è stata usata l'opzione di installazione personalizzata, le [autorizzazioni necessarie](./reference-connect-accounts-permissions.md) per la sincronizzazione dell'hash delle password potrebbero non essere presenti.

Per sincronizzare gli hash delle password, l'account del servizio Active Directory Domain Services di Azure AD Connect richiede le autorizzazioni seguenti:

* Replica modifiche directory
* Replica modifiche directory - Tutto

È quindi consigliabile verificare che queste autorizzazioni siano configurate per tutti i domini nella foresta.

### <a name="plan-the-migration-method"></a>Pianificare il metodo di migrazione

È possibile scegliere tra due metodi per eseguire la migrazione dalla gestione dell'identità federata alla sincronizzazione dell'hash delle password e all'accesso Single Sign-On facile. La scelta del metodo dipende dal modo in cui l'istanza di AD FS è stata originariamente configurata.

* **Azure AD Connect**. Se originariamente AD FS è stato configurato usando Azure AD Connect, *è necessario* passare alla sincronizzazione degli hash delle password usando la procedura guidata di Azure AD Connect.

   ‎Quando si modifica il metodo di accesso dell'utente, Azure AD Connect esegue automaticamente il cmdlet **Set-MsolDomainAuthentication**. Azure AD Connect annulla automaticamente la federazione di tutti i domini federati verificati nel tenant di Azure AD.

   > [!NOTE]
   > Al momento, se Azure AD Connect è stato originariamente usato per configurare AD FS, non è possibile evitare l'annullamento della federazione di tutti i domini nel tenant quando si imposta la sincronizzazione degli hash delle password come metodo di accesso utente. ‎
* **Azure AD Connect con PowerShell**. È possibile usare questo metodo solo se AD FS non è stato originariamente configurato tramite Azure AD Connect. Per questa opzione, è comunque necessario modificare il metodo di accesso utente tramite la procedura guidata di Azure AD Connect. La differenza principale di questa opzione è che la procedura guidata non esegue automaticamente il cmdlet **Set-MsolDomainAuthentication**. Con questa opzione, si ha pieno controllo su quali domini vengono convertiti e in quale ordine.

Per comprendere quale metodo è preferibile usare, completare i passaggi nelle sezioni seguenti.

#### <a name="verify-current-user-sign-in-settings"></a>Verificare le impostazioni di accesso utente correnti

Per verificare le impostazioni di accesso utente correnti:

1. Accedere al [portale di Azure AD](https://aad.portal.azure.com/) usando un account amministratore globale.
2. Nella sezione **Accesso utente** verificare le impostazioni di questi campi:
   * **Federazione** deve essere impostato su **Abilitata**.
   * **Accesso Single Sign-On facile** deve essere impostato su **Disabilitato**.
   * **Autenticazione pass-through** deve essere impostato su **Disabilitato**.

   ![Screenshot delle impostazioni nella sezione Accesso utente di Azure AD Connect](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Verificare la configurazione di Azure AD Connect

1. Nel server di Azure AD Connect aprire Azure AD Connect. Selezionare **Configura**.
2. Nella pagina **Attività aggiuntive** selezionare **Visualizza la configurazione corrente** e quindi fare clic su **Avanti**.<br />

   ![Screenshot dell'opzione Visualizza la configurazione corrente selezionata nella pagina Attività aggiuntive](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Nella pagina **Verifica della soluzione** prendere nota dello stato di **Sincronizzazione dell'hash delle password**.<br /> 

   * Se **Sincronizzazione dell'hash delle password** è impostato su **Disabilitata**, completare i passaggi elencati in questo articolo per abilitarla.
   * Se la **sincronizzazione dell'hash delle password** è impostata su **abilitato**, è possibile ignorare la sezione **passaggio 1: abilitare la sincronizzazione dell'hash delle password** in questo articolo.
4. Nella pagina **Verifica della soluzione** scorrere fino a visualizzare **Active Directory Federation Services (AD FS)**.<br />

   * ‎Se in questa sezione viene visualizzata la configurazione di AD FS, si può presupporre con sicurezza che AD FS sia stato originariamente configurato usando Azure AD Connect. È possibile convertire i domini dall'identità federata all'identità gestita usando l'opzione **Cambia l'accesso utente** di Azure AD Connect. Il processo è descritto in dettaglio nella sezione **opzione A: passare dalla Federazione alla sincronizzazione degli hash delle password usando Azure ad Connect**.
   * Se AD FS non è elencato nelle impostazioni correnti, è necessario convertire manualmente i domini dall'identità federata all'identità gestita usando PowerShell. Per altre informazioni su questo processo, vedere la sezione **opzione B: passare dalla Federazione alla sincronizzazione degli hash delle password usando Azure ad Connect e PowerShell**.

### <a name="document-current-federation-settings"></a>Documentare le impostazioni di federazione correnti

Per trovare le impostazioni di federazione correnti, eseguire il cmdlet **Get-MsolDomainFederationSettings**:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Esempio:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Verificare le impostazioni che potrebbero essere state personalizzate per la documentazione relativa alla progettazione e alla distribuzione della federazione. Cercare in particolare le personalizzazioni in **PreferredAuthenticationProtocol**, **SupportsMfa** e **PromptLoginBehavior**.

Per altre informazioni, vedere questi articoli:

* [Supporto del parametro prompt=login di AD FS](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

> [!NOTE]
> Se **SupportsMfa** è impostato su **True**, si sta usando una soluzione di autenticazione a più fattori locale per inserire una richiesta di verifica come secondo fattore nel flusso di autenticazione utente. Questa configurazione non funziona più per gli scenari di autenticazione Azure AD dopo la conversione del dominio dall'autenticazione federata a quella gestita. Dopo la disabilitazione della Federazione, la relazione viene troncata alla Federazione locale e sono incluse le schede di autenticazione a più fattori locali. 
>
> Usare invece il Azure AD Multi-Factor Authentication servizio basato sul cloud per eseguire la stessa funzione. Valutare attentamente i requisiti di autenticazione a più fattori prima di continuare. Prima di convertire i domini, assicurarsi di comprendere come usare Azure AD Multi-Factor Authentication, le implicazioni relative alle licenze e il processo di registrazione dell'utente.

#### <a name="back-up-federation-settings"></a>Eseguire il backup delle impostazioni di federazione

Anche se non è possibile apportare modifiche ad altre relying party nella farm AD FS durante i processi descritti in questo articolo, è consigliabile avere una copia di backup valida corrente della farm AD FS per un eventuale ripristino. È possibile creare un backup valido corrente usando lo [strumento di ripristino rapido di AD FS](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) fornito gratuitamente da Microsoft. Questo strumento può essere usato per eseguire il backup di AD FS e per ripristinare una farm esistente o crearne una nuova.

Se si sceglie di non utilizzare lo strumento di ripristino rapido AD FS, è necessario esportare almeno la piattaforma di identità Microsoft 365 relying party Trust ed eventuali regole attestazioni personalizzate associate aggiunte. È possibile esportare il trust della relying party e le regole associate per le attestazioni usando l'esempio di PowerShell seguente:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Considerazioni relative alla distribuzione e all'uso di AD FS

Questa sezione include considerazioni sulla distribuzione e dettagli sull'uso di AD FS.

### <a name="current-ad-fs-use"></a>Uso di AD FS corrente

Prima di eseguire la conversione da identità federata a identità gestita, esaminare attentamente il modo in cui si usano attualmente AD FS per Azure AD, Microsoft 365 e altre applicazioni (relying party Trust). In particolare, considerare gli scenari descritti nella tabella seguente:

| Se | Risultato |
|-|-|
| Si prevede di usare AD FS con altre applicazioni (diverse da Azure AD e Microsoft 365). | Dopo la conversione dei domini, si useranno sia AD FS che Azure AD. Considerare l'esperienza utente. In alcuni scenari, è possibile che gli utenti debbano eseguire l'autenticazione due volte: una volta per Azure AD (dove un utente ottiene l'accesso SSO ad altre applicazioni, ad esempio Microsoft 365) e di nuovo per tutte le applicazioni ancora associate AD FS come attendibilità relying party. |
| L'istanza di AD FS è un componente altamente personalizzabile e si basa su specifiche impostazioni di personalizzazione definite nel file onload.js, ad esempio se si è modificata l'esperienza di accesso per consentire agli utenti di usare il proprio nome solo nel formato **SamAccountName**, invece di un nome di entità utente (UPN), o se l'organizzazione visualizza informazioni personalizzate distintive dell'azienda. Il file onload.js non può essere duplicato in Azure AD. | Prima di continuare, è necessario verificare che Azure AD possa soddisfare i requisiti di personalizzazione corrente. Per altre informazioni e indicazioni, vedere le sezioni relative alla personalizzazione di AD FS e all'uso di informazioni personalizzate distintive dell'azienda in AD FS.|
| Si usa AD FS per bloccare le versioni precedenti dei client di autenticazione.| Provare a sostituire AD FS controlli che bloccano le versioni precedenti dei client di autenticazione usando una combinazione di [controlli di accesso condizionale](../conditional-access/concept-conditional-access-conditions.md) e [regole di accesso client di Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules). |
| Si richiede agli utenti di eseguire l'autenticazione a più fattori in una soluzione server di autenticazione a più fattori locale quando gli utenti eseguono l'autenticazione ad AD FS.| In un dominio con identità gestite, non è possibile inserire una richiesta di autenticazione a più fattori tramite la soluzione di autenticazione a più fattori locale nel flusso di autenticazione. Tuttavia, è possibile usare il servizio Azure AD Multi-Factor Authentication per l'autenticazione a più fattori dopo la conversione del dominio.<br /><br /> Se gli utenti non usano attualmente Azure AD Multi-Factor Authentication, è necessario un solo passaggio di registrazione dell'utente. È necessario eseguire le opportune operazioni preliminari e comunicare agli utenti la registrazione pianificata. |
| Attualmente si usano i criteri di controllo di accesso (AuthZ Rules) in AD FS per controllare l'accesso ai Microsoft 365.| Prendere in considerazione la sostituzione dei criteri con i [criteri di accesso condizionale](../conditional-access/overview.md) Azure ad equivalenti e le [regole di accesso client di Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules).|

### <a name="common-ad-fs-customizations"></a>Personalizzazioni di AD FS comuni

Questa sezione descrive le personalizzazioni di AD FS comuni.

#### <a name="insidecorporatenetwork-claim"></a>Attestazione InsideCorporateNetwork

AD FS rilascia l'attestazione **InsideCorporateNetwork** se l'utente che esegue l'autenticazione si trova all'interno della rete aziendale. Questa attestazione può quindi essere passata ad Azure AD. L'attestazione viene usata per ignorare l'autenticazione a più fattori basata sul percorso di rete dell'utente. Per informazioni su come determinare se questa funzionalità è attualmente abilitata in AD FS, vedere [Indirizzi IP attendibili per utenti federati](../authentication/howto-mfa-adfs.md).

L'attestazione **InsideCorporateNetwork** non è disponibile dopo che i domini vengono convertiti alla sincronizzazione dell'hash delle password. In alternativa a questa funzionalità è possibile usare [Posizioni specifiche in Azure AD](../reports-monitoring/quickstart-configure-named-locations.md).

Dopo aver configurato le località denominate, è necessario aggiornare tutti i criteri di accesso condizionale configurati in modo da includere o escludere la rete **tutti i percorsi attendibili** o i valori **IP attendibili** dell'autenticazione a più fattori per riflettere le nuove posizioni

Per ulteriori informazioni sulla condizione di **posizione** nell'accesso condizionale, vedere [Active Directory percorsi di accesso condizionale](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivi aggiunti ad Azure AD ibrido

Quando si aggiunge un dispositivo a Azure AD, è possibile creare regole di accesso condizionale che impongono che i dispositivi soddisfino gli standard di accesso per la sicurezza e la conformità. Gli utenti possono anche accedere a un dispositivo usando un account aziendale o dell'istituto di istruzione invece di un account personale. Quando si usano dispositivi aggiunti ad Azure AD ibrido, è possibile aggiungere ad Azure AD i dispositivi aggiunti a un dominio di Active Directory. L'ambiente federato potrebbe essere stato configurato per usare questa funzionalità.

Per assicurarsi che il join ibrido continui a funzionare per tutti i dispositivi aggiunti al dominio dopo la conversione dei domini in sincronizzazione dell'hash delle password, per i client Windows 10 è necessario usare Azure AD Connect opzioni del dispositivo per sincronizzare gli account computer Active Directory per Azure AD. 

Per gli account computer di Windows 8 e Windows 7, la funzionalità di aggiunta a un ambiente ibrido usa l'accesso Single Sign-On facile per registrare il computer in Azure AD. Non è necessario sincronizzare gli account computer di Windows 8 e Windows 7 come si fa per i dispositivi Windows 10. È tuttavia necessario distribuire un file workplacejoin.exe aggiornato (tramite un file con estensione msi) nei client Windows 8 e Windows 7 in modo che possano registrarsi usando l'accesso Single Sign-On facile. [Scaricare il file con estensione msi](https://www.microsoft.com/download/details.aspx?id=53554).

Per altre informazioni, vedere [Configurare i dispositivi aggiunti ad Azure AD ibrido](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Personalizzazione

Se l'organizzazione [ha personalizzato le pagine di accesso di AD FS](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) per visualizzare informazioni più pertinenti all'organizzazione, valutare l'opportunità di eseguire [personalizzazioni simili nella pagina di accesso di Azure AD](../fundamentals/customize-branding.md).

Anche se sono disponibili opzioni di personalizzazione simili, molto probabilmente dopo la conversione si riscontreranno nelle pagine di accesso differenze dal punto di vista grafico. Potrebbe essere necessario fornire informazioni sulle modifiche previste nelle comunicazioni agli utenti.

> [!NOTE]
> La personalizzazione dell'organizzazione è disponibile solo se si acquista la licenza Premium o Basic per Azure Active Directory o se si dispone di una licenza di Microsoft 365.

## <a name="plan-deployment-and-support"></a>Pianificare la distribuzione e il supporto

Completare le attività descritte in questa sezione per pianificare la distribuzione e il supporto.

### <a name="plan-the-maintenance-window"></a>Pianificare l'intervallo di manutenzione

Anche se il processo di conversione dei domini è relativamente rapido, Azure AD potrebbe continuare a inviare alcune richieste di autenticazione ai server di AD FS per un periodo massimo di quattro ore dopo il completamento della conversione. Durante questo periodo di quattro ore, e a seconda delle varie cache sul lato del servizio, è possibile che Azure AD non accetti queste autenticazioni e che venga restituito un errore. L'utente può ancora eseguire l'autenticazione con AD FS, ma Azure AD non accetta più il token rilasciato dell'utente perché tale trust di federazione è stato rimosso.

Questo problema interessa solo gli utenti che accedono ai servizi tramite un Web browser durante l'intervallo successivo alla conversione prima che venga cancellata la cache sul lato del servizio. I client legacy (Exchange ActiveSync, Outlook 2010 o 2013) non dovrebbero essere interessati perché Exchange Online conserva una cache delle credenziali per un determinato periodo di tempo. La cache viene usata per autenticare di nuovo l'utente senza avvisare. L'utente non deve tornare ad AD FS. Le credenziali archiviate nel dispositivo per questi client vengono usate per autenticarli di nuovo senza avvisare dopo che il contenuto di questa cache viene cancellato. Non è previsto che gli utenti ricevano richieste di password come risultato del processo di conversione dei domini. 

I client di autenticazione moderni (Office 2016 e Office 2013, iOS e app Android) usano un token di aggiornamento valido per ottenere nuovi token per l'accesso continuo alle risorse invece di tornare ad AD FS. Questi client non sono soggetti a richieste di password risultanti dal processo di conversione dei domini. I client continueranno a funzionare senza alcuna configurazione aggiuntiva.

> [!IMPORTANT]
> Non arrestare l'ambiente AD FS o rimuovere il Microsoft 365 relying party trust fino a quando non si è verificato che tutti gli utenti possano eseguire l'autenticazione con l'autenticazione cloud.

### <a name="plan-for-rollback"></a>Pianificare per il rollback

Se si riscontra un problema importante che non può essere risolto rapidamente, è possibile decidere di eseguire il ripristino della soluzione alla federazione. È importante pianificare le operazioni da eseguire se la distribuzione non viene implementata come previsto. Se la conversione del dominio o degli utenti non riesce durante la distribuzione oppure se si deve eseguire il ripristino alla federazione, è necessario comprendere in che modo attenuare le eventuali interruzioni del servizio e ridurre l'effetto sugli utenti.

#### <a name="to-roll-back"></a>Per eseguire il ripristino dello stato precedente

Per pianificare il ripristino dello stato precedente, vedere la documentazione relativa alla progettazione e alla distribuzione della federazione per i dettagli della specifica distribuzione. Il processo deve includere queste attività:

* Conversione dei domini gestiti in domini federati tramite il cmdlet **Convert-MSOLDomainToFederated**.
* Se necessario, configurazione di altre regole per le attestazioni.

### <a name="plan-communications"></a>Pianificare le comunicazioni

Una parte importante del processo di pianificazione della distribuzione e del supporto consiste nell'assicurarsi che gli utenti vengano informati in modo proattivo riguardo alle modifiche imminenti. Gli utenti devono sapere in anticipo che cosa potrebbe verificarsi e che cosa devono fare. 

Dopo la distribuzione della sincronizzazione degli hash delle password e dell'accesso SSO facile, l'esperienza di accesso dell'utente per l'accesso a Microsoft 365 e ad altre risorse autenticate tramite Azure AD modifiche. Gli utenti che sono all'esterno della rete vedono solo la pagina di accesso di Azure AD. Questi utenti non vengono reindirizzati alla pagina basata su form presentata dai server proxy applicazione Web con accesso all'esterno.

Includere gli elementi seguenti nella strategia di comunicazione:

* Notificare agli utenti il rilascio imminente o l'avvenuto rilascio di funzionalità usando:
   * Posta elettronica e altri canali di comunicazione interna.
   * Oggetti visivi, ad esempio poster.
   * Comunicazioni live, dei dirigenti o di altro tipo.
* Designare chi personalizzerà e invierà le comunicazioni e quando questo dovrà avvenire.

## <a name="implement-your-solution"></a>Implementare la soluzione

Ora che la soluzione è stata pianificata, è possibile implementarla. L'implementazione consiste nelle operazioni seguenti:

* Abilitazione della sincronizzazione dell'hash delle password.
* Esecuzione delle attività preliminari per l'accesso Single Sign-On facile.
* Conversione del metodo di accesso alla sincronizzazione dell'hash delle password e abilitazione dell'accesso Single Sign-On facile.

### <a name="step-1-enable-password-hash-synchronization"></a>Passaggio 1: abilitare la sincronizzazione dell'hash delle password

Il primo passaggio per implementare questa soluzione consiste nell'abilitare la sincronizzazione dell'hash delle password usando la procedura guidata di Azure AD Connect. La sincronizzazione dell'hash delle password è una funzionalità facoltativa che è possibile abilitare negli ambienti che usano la federazione. Questa operazione non influisce sul flusso di autenticazione. In questo caso, Azure AD Connect inizierà a sincronizzare gli hash delle password senza influire sugli utenti che eseguono l'accesso usando la federazione.

Per questo motivo, è consigliabile completare questo passaggio come attività preparatoria, ben prima di modificare il metodo di accesso ai domini. In questo modo si avrà tempo a sufficienza per verificare il corretto funzionamento della sincronizzazione degli hash delle password.

Per abilitare la sincronizzazione degli hash delle password:

1. Nel server di Azure AD Connect avviare la procedura guidata di Azure AD Connect e quindi selezionare **Configura**.
2. Selezionare **Personalizza opzioni di sincronizzazione** e quindi fare clic su **Avanti**.
3. Nella pagina **Connessione ad Azure AD** specificare il nome utente e la password di un account amministratore globale.
4. Nella pagina **Connessione delle directory** fare clic su **Avanti**.
5. Nella pagina **Filtro di domini e unità organizzative** fare clic su **Avanti**.
6. Nella pagina **Funzionalità facoltative** selezionare **Sincronizzazione password** e quindi fare clic su **Avanti**.
 
   ![Screenshot dell'opzione Sincronizzazione password selezionata nella pagina Funzionalità facoltative](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Selezionare **Avanti** nelle pagine rimanenti. Nell'ultima pagina selezionare **Configura**.
8. Azure AD Connect inizia a sincronizzare gli hash delle password alla successiva sincronizzazione.

Dopo che la sincronizzazione degli hash delle password è abilitata, gli hash delle password per tutti gli utenti nell'ambito della sincronizzazione di Azure AD Connect vengono ridefiniti e scritti in Azure AD. A seconda del numero di utenti, questa operazione potrebbe richiedere pochi minuti o alcune ore.

Ai fini della pianificazione, è necessario stimare che in un'ora vengono elaborati circa 20.000 utenti.

Per verificare che la sincronizzazione degli hash delle password funzioni correttamente, completare l'attività **Risoluzione dei problemi** nella procedura guidata di Azure AD Connect:

1. Aprire una nuova sessione di Windows PowerShell nel server di Azure AD Connect usando l'opzione Esegui come amministratore.
2. Eseguire `Set-ExecutionPolicy RemoteSigned` o `Set-ExecutionPolicy Unrestricted`.
3. Avviare la procedura guidata di Azure AD Connect.
4. Passare alla pagina **Attività aggiuntive**, selezionare **Risoluzione dei problemi** e quindi fare clic su **Avanti**.
5. Nella pagina **Risoluzione dei problemi** selezionare **Avvia** per avviare il menu per la risoluzione dei problemi in PowerShell.
6. Nel menu principale scegliere **Troubleshoot password hash synchronization** (Risolvere i problemi di sincronizzazione degli hash delle password).
7. Nel sottomenu scegliere **Password hash synchronization does not work at all** (La sincronizzazione degli hash delle password non funziona).

Per risolvere i problemi, vedere [Risolvere i problemi di sincronizzazione dell'hash delle password con la sincronizzazione di Azure AD Connect](./tshoot-connect-password-hash-synchronization.md).

### <a name="step-2-prepare-for-seamless-sso"></a>Passaggio 2: preparare l'accesso SSO facile

Per consentire agli utenti di usufruire dell'accesso Single Sign-On facile, è necessario aggiungere un URL di Azure AD alle impostazioni dell'area Intranet degli utenti usando Criteri di gruppo in Active Directory.

Per impostazione predefinita, i Web browser calcolano automaticamente l'area corretta, Internet o Intranet, in base a un URL. Ad esempio, **http: \/ \/ Contoso/** esegue il mapping all'area intranet e **http: \/ \/ Intranet.contoso.com** esegue il mapping all'area Internet (perché l'URL contiene un punto). I browser inviano ticket Kerberos a un endpoint del cloud, come l'URL di Azure AD, solo se l'URL in questione viene esplicitamente aggiunto all'area Intranet del browser.

Completare questi passaggi per [implementare](./how-to-connect-sso-quick-start.md) le modifiche necessarie nei dispositivi.

> [!IMPORTANT]
> L'introduzione di questa modifica non cambia il modo in cui gli utenti accedono ad Azure AD. È tuttavia importante applicare questa configurazione a tutti i dispositivi prima di proseguire. Per accedere ai dispositivi a cui non è stata applicata questa configurazione, gli utenti devono immettere un nome utente e una password per l'accesso ad Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Passaggio 3: modificare il metodo di accesso per la sincronizzazione degli hash delle password e abilitare la funzionalità seamless SSO

Sono disponibili due opzioni per cambiare il metodo di accesso alla sincronizzazione degli hash delle password e abilitare l'accesso Single Sign-On facile.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Opzione A: passare dalla Federazione alla sincronizzazione dell'hash delle password usando Azure AD Connect

Usare questo metodo se si è inizialmente configurato l'ambiente di AD FS usando Azure AD Connect. In *caso contrario*, questo metodo non può essere usato.

Prima di tutto, cambiare il metodo di accesso:

1. Nel server di Azure AD Connect avviare la procedura guidata di Azure AD Connect.
2. Selezionare **cambia l'accesso utente** e quindi fare clic su **Avanti**. 

   ![Screenshot dell'opzione Cambia l'accesso utente selezionata nella pagina Attività aggiuntive](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Nella pagina **Connessione ad Azure AD** specificare il nome utente e la password di un account amministratore globale.
4. Nella pagina **accesso utente** selezionare il **pulsante sincronizzazione hash password**. Selezionare la casella di controllo **Non convertire gli account utente**. L'opzione è deprecata. Selezionare **Abilita Single Sign-On** e quindi fare clic su **Avanti**.

   ![Screenshot della pagina Abilita Single Sign-On](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > A partire da Azure AD Connect versione 1.1.880.0, la casella di controllo **Accesso Single Sign-On facile** è selezionata per impostazione predefinita.

   > [!IMPORTANT]
   > È possibile ignorare tranquillamente gli avvisi in cui viene segnalato che la conversione degli utenti e la sincronizzazione completa degli hash delle password sono passaggi obbligatori per la conversione dalla federazione all'autenticazione cloud. Si noti che questi passaggi non sono più necessari. Se questi avvisi continuano a essere visualizzati, assicurarsi di eseguire la versione più recente di Azure AD Connect e di usare la versione più recente di questa guida. Per altre informazioni, vedere la sezione [Aggiornare Azure AD Connect](#update-azure-ad-connect).

5. Nella pagina **Abilita Single Sign-on** immettere le credenziali dell'account amministratore di dominio e quindi fare clic su **Avanti**.

   ![Screenshot della pagina Abilita Single Sign-On in cui è possibile immettere le credenziali dell'account amministratore di dominio.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Le credenziali dell'account amministratore di dominio sono necessarie per abilitare l'accesso Single Sign-on facile. Il processo esegue le operazioni descritte di seguito, che richiedono queste autorizzazioni elevate. Le credenziali dell'account amministratore di dominio non vengono archiviate in Azure AD Connect o in Azure AD, ma vengono usate solo per attivare la funzionalità. Al termine del processo, le credenziali vengono rimosse.
   >
   > 1. Un account computer denominato AZUREADSSOACC, che rappresenta Azure AD, viene creato nell'istanza di Active Directory locale.
   > 2. La chiave di decrittografia Kerberos dell'account computer viene condivisa in modo sicuro con Azure AD.
   > 3. Vengono creati due nomi di entità servizio (SPN, Service Principal Name) Kerberos per rappresentare due URL usati durante l'accesso ad Azure AD.

6. Nella pagina **Pronto per la configurazione** verificare che la casella di controllo **Avvia il processo di sincronizzazione al termine della configurazione** sia selezionata. Quindi selezionare **Configura**.

      ![Screenshot della pagina Pronto per la configurazione](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > A questo punto, tutti i domini federati passeranno all'autenticazione gestita. La sincronizzazione degli hash delle password è il nuovo metodo di autenticazione.

7. Nel portale di Azure ad selezionare **Azure Active Directory**  >  **Azure ad Connect**.
8. Verificare le impostazioni di questi campi:
   * **Federazione** deve essere impostato su **Disabilitato**.
   * **Accesso Single Sign-On facile** deve essere impostato su **Abilitato**.
   * **Sincronizzazione password** deve essere impostato su **Abilitata**.<br /> 

   ![Screenshot che mostra le impostazioni nella sezione di accesso utente del portale di Azure AD.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Proseguire con la sezione [Test e passaggi successivi](#testing-and-next-steps).

   > [!IMPORTANT]
   > Ignorare la sezione **opzione B: passare dalla Federazione alla sincronizzazione degli hash delle password usando Azure ad Connect e PowerShell**. I passaggi in questa sezione non sono applicabili se si è scelta l'opzione A per convertire il metodo di accesso alla sincronizzazione degli hash delle password e abilitare l'accesso Single Sign-On facile.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Opzione B: passare dalla Federazione alla sincronizzazione dell'hash delle password con Azure AD Connect e PowerShell

Usare questa opzione se non si sono inizialmente configurati i domini federati usando Azure AD Connect. Durante questo processo, si abilita l'accesso Single Sign-On facile e si convertono i domini da federati a gestiti.

1. Nel server di Azure AD Connect avviare la procedura guidata di Azure AD Connect.
2. Selezionare **cambia l'accesso utente** e quindi fare clic su **Avanti**.
3. Nella pagina **Connessione ad Azure AD** specificare il nome utente e la password di un account amministratore globale.
4. Nella pagina **Accesso utente** selezionare il pulsante **Sincronizzazione dell'hash delle password**. Selezionare **Abilita Single Sign-On** e quindi fare clic su **Avanti**.

   Prima di abilitare la sincronizzazione dell'hash delle password: ![ screenshot che mostra l'opzione non configurare nella pagina di accesso dell'utente](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Dopo aver abilitato la sincronizzazione dell'hash delle password: ![ screenshot che mostra le nuove opzioni nella pagina di accesso dell'utente](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > A partire da Azure AD Connect versione 1.1.880.0, la casella di controllo **Accesso Single Sign-On facile** è selezionata per impostazione predefinita.

5. Nella pagina **Abilita Single Sign-on** immettere le credenziali dell'account amministratore di dominio e quindi fare clic su **Avanti**.

   > [!NOTE]
   > Le credenziali dell'account amministratore di dominio sono necessarie per abilitare l'accesso Single Sign-on facile. Il processo esegue le operazioni descritte di seguito, che richiedono queste autorizzazioni elevate. Le credenziali dell'account amministratore di dominio non vengono archiviate in Azure AD Connect o in Azure AD, ma vengono usate solo per attivare la funzionalità. Al termine del processo, le credenziali vengono rimosse.
   >
   > 1. Un account computer denominato AZUREADSSOACC, che rappresenta Azure AD, viene creato nell'istanza di Active Directory locale.
   > 2. La chiave di decrittografia Kerberos dell'account computer viene condivisa in modo sicuro con Azure AD.
   > 3. Vengono creati due nomi di entità servizio (SPN, Service Principal Name) Kerberos per rappresentare due URL usati durante l'accesso ad Azure AD.

6. Nella pagina **Pronto per la configurazione** verificare che la casella di controllo **Avvia il processo di sincronizzazione al termine della configurazione** sia selezionata. Quindi selezionare **Configura**.

   ![Screenshot che mostra il pulsante Configura nella pagina Pronto per la configurazione](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Quando si seleziona il pulsante **Configura**, l'accesso SSO facile viene configurato come indicato nel passaggio precedente. La configurazione della sincronizzazione degli hash delle password non viene modificata perché è stata abilitata in precedenza.

   > [!IMPORTANT]
   > In questa fase non vengono apportate modifiche al modo in cui gli utenti eseguono l'accesso.

7. Nel portale di Azure AD verificare queste impostazioni:
   * **Federazione** deve essere impostato su **Abilitata**.
   * **Accesso Single Sign-On facile** deve essere impostato su **Abilitato**.
   * **Sincronizzazione password** deve essere impostato su **Abilitata**.

   ![Screenshot che mostra le impostazioni nella sezione Accesso utente](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Convertire i domini da federati a gestiti

A questo punto, la federazione è ancora abilitata e operativa per i domini. Per proseguire la distribuzione, ogni dominio deve essere convertito da federato a gestito in modo da forzare l'autenticazione utente tramite la sincronizzazione degli hash delle password.

> [!IMPORTANT]
> Non è necessario convertire tutti i domini nello stesso momento. È possibile scegliere di iniziare con un dominio di test nel tenant di produzione oppure con il dominio che ha il minor numero di utenti.

Completare la conversione usando il modulo Azure AD PowerShell:

1. In PowerShell accedere ad Azure AD usando un account amministratore globale.
2. Per convertire il primo dominio, usare il comando seguente:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Nel portale di Azure ad selezionare **Azure Active Directory**  >  **Azure ad Connect**.
4. Verificare che il dominio sia stato convertito in dominio gestito con il comando seguente:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Test e passaggi successivi

Eseguire le attività seguenti per verificare la sincronizzazione degli hash delle password e completare il processo di conversione.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Testare l'autenticazione usando la sincronizzazione degli hash delle password 

Quando il tenant usava l'identità federata, gli utenti venivano reindirizzati dalla pagina di accesso di Azure AD all'ambiente di AD FS. Ora che il tenant è configurato per l'uso della sincronizzazione degli hash delle password invece che dell'autenticazione federata, gli utenti non vengono reindirizzati all'ambiente AD FS, ma passano direttamente alla pagina di accesso di Azure AD.

Per testare la sincronizzazione degli hash delle password:

1. Aprire Internet Explorer in modalità InPrivate per evitare che la funzionalità di accesso Single Sign-On facile esegua automaticamente l'accesso.
2. Passare alla pagina di accesso di Office 365 ( [https://portal.office.com](https://portal.office.com/) ).
3. Immettere l'UPN di un utente e quindi fare clic su **Avanti**. Assicurarsi di immettere l'UPN di un utente ibrido che è stato sincronizzato dall'istanza di Active Directory locale e che in precedenza ha usato l'autenticazione federata. Verrà visualizzata una pagina in cui immettere il nome utente e la password:

   ![Screenshot che mostra la pagina di accesso in cui immettere un nome utente](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Screenshot che mostra la pagina di accesso in cui immettere una password](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Dopo che si è immessa la password e si è selezionato **Accedi**, si verrà reindirizzati al portale di Office 365.

   ![Screenshot che mostra il portale di Office 365](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Testare l'accesso Single Sign-On facile

1. Accedere a un computer aggiunto al dominio che è connesso alla rete aziendale.
2. In Internet Explorer o Chrome passare a uno degli URL seguenti (sostituire "contoso" con il nome del proprio dominio):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   L'utente verrà rapidamente reindirizzato alla pagina di accesso di Azure AD e vedrà il messaggio "Tentativo di accesso per conto dell'utente". All'utente non verrà chiesto di specificare un nome utente o una password.<br />

   ![Screenshot che mostra la pagina di accesso di Azure AD con il messaggio](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. L'utente verrà reindirizzato e l'accesso verrà eseguito automaticamente:

   > [!NOTE]
   > Seamless SSO funziona nei servizi Microsoft 365 che supportano l'hint di dominio (ad esempio, myapps.microsoft.com/contoso.com). Attualmente, il portale di Microsoft 365 (portal.office.com) non supporta gli hint di dominio. Gli utenti devono immettere un UPN. Dopo che è stato immesso un UPN, la funzionalità di accesso Single Sign-On facile recupera il ticket Kerberos per conto dell'utente. L'utente esegue l'accesso senza immettere una password.

   > [!TIP]
   > Per un'esperienza di accesso Single Sign-On migliorata, valutare l'opportunità di distribuire [Aggiunta ad Azure AD ibrido in Windows 10](../devices/overview.md).

### <a name="remove-the-relying-party-trust"></a>Rimuovere il trust della relying party

Dopo aver convalidato che tutti gli utenti e i client vengono autenticati tramite Azure AD, è possibile rimuovere il Microsoft 365 relying party attendibilità.

Se non si usa AD FS per altri scopi, ovvero per altri trust di relying party, a questo punto si possono rimuovere senza problemi le autorizzazioni di AD FS.

### <a name="rollback"></a>Rollback

Se si riscontra un problema importante che non può essere risolto rapidamente, può essere opportuno eseguire il ripristino della soluzione alla federazione.

Vedere la documentazione relativa alla progettazione e alla distribuzione della federazione per i dettagli della specifica distribuzione. Il processo deve includere queste attività:

* Convertire i domini gestiti all'autenticazione federata usando il cmdlet **Convert-MSOLDomainToFederated**.
* Se necessario, configurare altre regole per le attestazioni.

### <a name="sync-userprincipalname-updates"></a>Sincronizzare gli aggiornamenti di userPrincipalName

In genere, gli aggiornamenti dell'attributo **UserPrincipalName**, che usa il servizio di sincronizzazione dall'ambiente locale, vengono bloccati a meno che non siano vere entrambe le condizioni seguenti:

* L'utente è in un dominio con identità gestite (non federate).
* All'utente non è stata assegnata una licenza.

Per informazioni su come verificare o attivare questa funzionalità, vedere [Sincronizzare gli aggiornamenti di userPrincipalName](./how-to-connect-syncservice-features.md).

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il team di supporto della propria organizzazione dovrebbe essere in grado di comprendere come risolvere i problemi di autenticazione che si verificano durante o dopo la conversione dei domini da federati a gestiti. Usare la documentazione seguente per consentire al team di supporto di acquisire familiarità con i passaggi comuni per la risoluzione dei problemi e le azioni appropriate che possono essere utili per isolare e risolvere un problema.

[Risolvere i problemi di Azure Active Directory sincronizzazione degli hash delle password](./tshoot-connect-password-hash-synchronization.md)

[Risolvere i problemi relativi all'accesso Single Sign-On facile di Azure Active Directory](./tshoot-connect-sso.md)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rinnovare la chiave di decrittografia di Kerberos per l'accesso Single Sign-On facile

È importante rinnovare spesso la chiave di decrittografia di Kerberos dell'account computer AZUREADSSOACC, che rappresenta Azure AD. L'account computer AZUREADSSOACC viene creato nella foresta locale di Active Directory. È consigliabile rinnovare la chiave di decrittografia di Kerberos almeno ogni 30 giorni in modo che la frequenza di rinnovo sia allineata a quella con cui i membri del dominio di Active Directory inviano gli aggiornamenti delle password. Nessun dispositivo associato è collegato all'oggetto account computer AZUREADSSOACC ed è quindi necessario eseguire il rinnovo manualmente.

Avviare il rinnovo della chiave di decrittografia di Kerberos per l'accesso Single Sign-On facile nel server locale che esegue Azure AD Connect.

Per altre informazioni, vedere [Come è possibile rinnovare la chiave di decrittografia di Kerberos dell'account computer AZUREADSSOACC?](./how-to-connect-sso-faq.md)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni, vedere [Concetti relativi alla progettazione per Azure AD Connect](plan-connect-design-concepts.md).
* Scegliere l' [autenticazione corretta](./choose-ad-authn.md).
* Vedere le informazioni sulle [topologie supportate](plan-connect-design-concepts.md).
