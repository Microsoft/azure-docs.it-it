---
title: Risolvere i problemi di verifica dell'editore | Azure
titleSuffix: Microsoft identity platform
description: Viene descritto come risolvere i problemi di verifica dell'editore per la piattaforma di identità Microsoft chiamando Microsoft Graph API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: acce282dcaac971716a97d7722543d78e28b7a34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517668"
---
# <a name="troubleshoot-publisher-verification"></a>Risolvere i problemi di verifica dell'autore
Se non si riesce a completare il processo o si verifica un comportamento imprevisto con la [Verifica dell'editore](publisher-verification-overview.md), è consigliabile iniziare eseguendo le operazioni seguenti se si ricevono errori o si verifica un comportamento imprevisto: 

1. Esaminare i [requisiti](publisher-verification-overview.md#requirements) e verificare che siano stati soddisfatti tutti.

1. Vedere le istruzioni per [contrassegnare un'app come con autore verificato](mark-app-as-publisher-verified.md) e assicurarsi che tutti i passaggi siano stati eseguiti correttamente.

1. Controllare l'elenco dei [problemi comuni](#common-issues).

1. Riprodurre la richiesta usando [Graph Explorer](#making-microsoft-graph-api-calls) per raccogliere informazioni aggiuntive ed escludere eventuali problemi nell'interfaccia utente.

## <a name="common-issues"></a>Problemi comuni
Di seguito sono riportati alcuni problemi comuni che possono verificarsi durante il processo. 

- **Non conosco il mio ID Microsoft Partner Network (MPN ID) o non so chi è il contatto principale per l'account** 
    1. Accedere alla [pagina di registrazione di MPN](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Accedere con un account utente nel tenant di Azure AD primario dell'organizzazione 
    1. Se esiste già un account MPN, verrà riconosciuto e l'utente verrà aggiunto all'account 
    1. Passare alla [pagina del profilo del partner](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) in cui sono elencati l'ID MPN e il contatto dell'account primario

- **Non conosco chi Azure AD amministratore globale (noto anche come amministratore della società o amministratore tenant), come posso trovarli? Cosa accade all'amministratore dell'applicazione o all'amministratore di applicazioni cloud?**
    1. Accedere al [portale di Azure AD](https://aad.portal.azure.com) usando un account utente nel tenant primario dell'organizzazione
    1. Passare a [Gestione ruoli](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Fare clic sul ruolo di amministratore desiderato
    1. Verrà visualizzato l'elenco degli utenti assegnati al ruolo

- **Non si conoscono gli amministratori dell'account MPN** Passare alla [pagina di gestione degli utenti MPN](https://partner.microsoft.com/pcv/users) e filtrare l'elenco di utenti per vedere quali sono gli utenti con i diversi ruoli di amministratore.

- **Si verifica un errore che segnala che l'ID MPN non è valido o che non è possibile accedervi.**
    1. Passare al [profilo partner](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) e verificare che: 
        - L'ID MPN sia corretto. 
        - Non siano visualizzati errori o "azioni in sospeso" e lo stato di verifica nel profilo di business legale e nelle informazioni sul partner sia "autorizzato" o "operazione riuscita".
    1. Passare alla [pagina di gestione del tenant di MPN](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement) e verificare che il tenant in cui è registrata l'app e di cui si sta usando un account utente per l'accesso sia incluso nell'elenco dei tenant associati. Per aggiungere un tenant aggiuntivo, seguire le istruzioni riportate [qui](/partner-center/multi-tenant-account). Tenere presente che a tutti gli amministratori globali di qualsiasi tenant aggiunto verranno concessi privilegi di amministratore globale per l'account del centro per i partner.
    1. Passare alla [pagina Gestione utenti MPN](https://partner.microsoft.com/pcv/users) e verificare che l'utente che si sta eseguendo l'accesso sia un amministratore globale, un amministratore MPN o un account amministratore. Per aggiungere un utente a un ruolo in centro per i partner, seguire le istruzioni riportate [qui](/partner-center/create-user-accounts-and-set-permissions).

- **Quando si accede al portale di Azure AD, non viene visualizzata alcuna app registrata. Perché?** 
    È possibile che le registrazioni dell'app siano state create usando un account utente diverso in questo tenant, un account personale/utente o un tenant diverso. Assicurarsi di aver eseguito l'accesso con l'account corretto nel tenant in cui sono state create le registrazioni dell'app.

- **Si verifica un errore relativo all'autenticazione a più fattori. Cosa dovrei fare?** 
    Assicurarsi che la [funzionalità autenticazione](../fundamentals/concept-fundamentals-mfa-get-started.md) a più fattori sia abilitata e **necessaria** per l'utente con cui si esegue l'accesso e per questo scenario. Ad esempio, l'autenticazione a più fattori può essere:
    - Sempre necessario per l'utente con cui si esegue l'accesso
    - [Obbligatorio per gestione di Azure](../conditional-access/howto-conditional-access-policy-azure-management.md).
    - [Obbligatorio per il tipo di amministratore](../conditional-access/howto-conditional-access-policy-admin-mfa.md) con cui si esegue l'accesso.

## <a name="making-microsoft-graph-api-calls"></a>Eseguire chiamate API Microsoft Graph 

Se si verifica un problema, ma non si riesce a capirne il motivo in base a ciò che viene visualizzato nell'interfaccia utente, può essere utile eseguire ulteriori operazioni di risoluzione dei problemi usando chiamate API Microsoft Graph per eseguire le stesse operazioni che è possibile eseguire nel portale di registrazione delle app.

Il modo più semplice per effettuare queste richieste consiste nell'usare [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). È anche possibile prendere in considerazione altre opzioni, ad esempio l'uso [Postman](https://www.postman.com/) o l'uso di PowerShell per [richiamare una richiesta Web](/powershell/module/microsoft.powershell.utility/invoke-webrequest).  

È possibile usare Microsoft Graph sia per impostare l'autore verificato per l'app che per annullarne l'impostazione e controllare il risultato dopo l'esecuzione di una di queste operazioni. Il risultato può essere visualizzato sia nell'oggetto [applicazione](/graph/api/resources/application) corrispondente alla registrazione dell'app che in qualsiasi [entità servizio](/graph/api/resources/serviceprincipal) di cui è stata creata un'istanza da tale app. Per altre informazioni sulla relazione tra questi oggetti, vedere: [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](app-objects-and-service-principals.md).  

Di seguito sono riportati alcuni esempi di richieste utili:  

### <a name="set-verified-publisher"></a>Impostare l'autore verificato 

Richiesta

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Risposta 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* è l'ID MPN. 

### <a name="unset-verified-publisher"></a>Annullare l'impostazione dell'autore verificato 

Richiesta:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Risposta 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Ottenere informazioni sull'autore verificato dall'applicazione 
 
```
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Ottenere informazioni sull'autore verificato dall'entità servizio 
```
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Informazioni di riferimento sugli errori 

Di seguito è riportato un elenco dei potenziali codici di errore che possono essere ricevuti, sia durante la risoluzione dei problemi con Microsoft Graph che nel corso del processo nel portale di registrazione delle app.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess

L'ID MPN specificato (`MPNID`) non esiste o non è possibile accedervi. Specificare un ID MPN valido e riprovare.
    
In genere, l'utente che ha eseguito l'accesso non è membro del ruolo appropriato per l'account MPN nel centro per i partner. per ulteriori informazioni, vedere i [requisiti](publisher-verification-overview.md#requirements) per un elenco dei ruoli idonei e vedere i [problemi comuni](#common-issues) . Può anche essere causato dal tenant in cui l'app è registrata non viene aggiunta all'account MPN o da un ID MPN non valido.

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound

L'ID MPN specificato (`MPNID`) non è valido. Specificare un ID MPN valido e riprovare.
    
Si verifica in genere quando viene fornito un ID MPN che corrisponde a un account di località partner (PLA). Sono supportati solo gli account globali partner. Per ulteriori informazioni, vedere la [struttura dell'account del centro](/partner-center/account-structure) per i partner.

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid

L'ID MPN specificato (`MPNID`) non è valido. Specificare un ID MPN valido e riprovare.
    
Generalmente causata dall'ID MPN errato fornito.

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted

L'ID MPN (`MPNID`) specificato non ha completato il processo di verifica. Completare questo processo in Partner Center e riprovare. 
    
Generalmente causata da quando l'account MPN non ha completato il processo di [Verifica](/partner-center/verification-responses) .

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount

L'ID MPN specificato (`MPNID`) non è valido. Specificare un ID MPN valido e riprovare. 
   
Generalmente causata dall'ID MPN errato fornito.

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount

L'ID MPN specificato (`MPNID`) non è valido. Specificare un ID MPN valido e riprovare.
    
Generalmente causata dall'ID MPN errato fornito.

### <a name="applicationnotfound"></a>ApplicationNotFound

Impossibile trovare l'applicazione di destinazione (`AppId`). Specificare un ID di applicazione valido e riprovare.
    
Si verifica in genere quando viene eseguita la verifica tramite API Graph e l'ID dell'applicazione fornita non è corretto. Nota: è necessario specificare l'ID dell'applicazione, non l'AppId/ClientID.

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed

Questa funzionalità non è supportata in un tenant Azure AD B2C.

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed

Questa funzionalità non è supportata in un tenant verificato tramite posta elettronica.

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication

L'applicazione di destinazione ( `AppId` ) deve avere un set di domini editore. Impostare un dominio dell'autore e riprovare.

Si verifica quando un [dominio del server di pubblicazione](howto-configure-publisher-domain.md) non è configurato nell'app.

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch

Il dominio dell'autore dell'applicazione di destinazione (`publisherDomain`) non corrisponde al dominio usato per eseguire la verifica tramite posta elettronica in Partner Center (`pcDomain`). Assicurarsi che questi domini corrispondano e riprovare. 
    
Si verifica quando il dominio del [server di pubblicazione](howto-configure-publisher-domain.md) dell'applicazione o uno dei [domini personalizzati](../fundamentals/add-custom-domain.md) aggiunti al tenant di Azure ad corrisponde al dominio utilizzato per eseguire la verifica tramite posta elettronica nel centro per i partner.

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher

Non si dispone delle autorizzazioni necessarie per impostare la proprietà del server di pubblicazione verificato nell'applicazione (<`AppId` ) 
  
In genere, l'utente che ha eseguito l'accesso non è membro del ruolo appropriato per l'account MPN in Azure AD. vedere i [requisiti](publisher-verification-overview.md#requirements) per un elenco dei ruoli idonei e vedere i [problemi comuni](#common-issues) per ulteriori informazioni.

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided

L'ID MPN non è stato specificato nel corpo della richiesta o il tipo di contenuto della richiesta non è "application/json".

### <a name="msanotsupported"></a>MSANotSupported 

Questa funzionalità non è supportata per gli account utente Microsoft. Sono supportate solo le applicazioni registrate in Azure AD da un utente Azure AD.

### <a name="interactionrequired"></a>InteractionRequired

Si verifica quando non è stata eseguita l'autenticazione a più fattori prima di tentare di aggiungere un server di pubblicazione verificato all'app. Per ulteriori informazioni, vedere [problemi comuni](#common-issues) . Nota: è necessario eseguire l'autenticazione a più fattori nella stessa sessione quando si tenta di aggiungere un server di pubblicazione verificato. Se l'autenticazione a più fattori è abilitata ma non è necessaria per essere eseguita nella sessione, la richiesta avrà esito negativo. 

Il messaggio di errore visualizzato sarà: "a causa di una modifica della configurazione effettuata dall'amministratore o perché è stato spostato in una nuova posizione, è necessario usare l'autenticazione a più fattori per continuare".

### <a name="unabletoaddpublisher"></a>UnableToAddPublisher

Il messaggio di errore visualizzato è: "Impossibile aggiungere un server di pubblicazione verificato a questa applicazione. Per assistenza, contattare l'amministratore. "

Verificare prima di tutto che siano stati soddisfatti i [requisiti di verifica dell'editore](publisher-verification-overview.md#requirements).

Quando viene effettuata una richiesta di aggiunta di un server di pubblicazione verificato, viene usato un numero di segnali per eseguire una valutazione dei rischi di sicurezza. Se la richiesta viene determinata come rischiosa, viene restituito un errore. Per motivi di sicurezza, Microsoft non divulga i criteri specifici utilizzati per determinare se una richiesta è rischiosa o meno.

## <a name="next-steps"></a>Passaggi successivi

Se sono state esaminate tutte le informazioni precedenti e si riceve ancora un errore da Microsoft Graph, raccogliere quante più informazioni possibili tra quelle indicate di seguito relative alla richiesta non riuscita e [contattare il supporto tecnico Microsoft](developer-support-help-options.md#create-an-azure-support-request).

- Timestamp 
- CorrelationId 
- ObjectID o UserPrincipalName dell'utente che ha eseguito l'accesso 
- ObjectId dell'applicazione di destinazione
- AppId dell'applicazione di destinazione
- TenantId in cui è registrata l'app
- ID MPN
- Richiesta REST eseguita 
- Codice di errore e messaggio restituiti