---
title: SSO tra ADAL & app MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come condividere l'accesso SSO tra app ADAL e MSAL
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 396e9cfeace8791a59dec4a9c9c7203212f57304
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584248"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Procedura: eseguire l'accesso SSO tra app ADAL e MSAL in macOS e iOS

Microsoft Authentication Library (MSAL) per iOS può condividere lo stato SSO con [adal Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) tra le applicazioni. Puoi eseguire la migrazione delle tue app a MSAL in base alle tue esigenze, assicurandosi che gli utenti possano comunque trarre vantaggio dall'accesso SSO tra app, anche con una combinazione di app basate su ADAL e MSAL.

Per indicazioni sulla configurazione dell'accesso Single Sign-on tra le app con MSAL SDK, vedere [SSO invisibile tra più app](single-sign-on-macos-ios.md#silent-sso-between-apps). Questo articolo è incentrato sull'accesso Single Sign-on tra ADAL e MSAL.

Le specifiche che implementano SSO dipendono dalla versione di ADAL in uso.

## <a name="adal-27x"></a>ADAL 2.7. x

In questa sezione vengono descritte le differenze SSO tra MSAL e ADAL 2.7. x

### <a name="cache-format"></a>Formato cache

ADAL 2.7. x può leggere il formato della cache MSAL. Non è necessario eseguire alcuna operazione speciale per SSO tra app con la versione ADAL 2.7. x. Tuttavia, tenere presenti le differenze negli identificatori di account supportati da tali librerie.

### <a name="account-identifier-differences"></a>Differenze tra gli identificatori di account

MSAL e ADAL usano identificatori di account diversi. ADAL USA UPN come identificatore dell'account primario. MSAL usa un identificatore di account non visualizzabile basato su un ID oggetto e un ID tenant per gli account AAD e un' `sub` attestazione per altri tipi di account.

Quando si riceve un `MSALAccount` oggetto nel risultato MSAL, questo contiene un identificatore di account nella `identifier` Proprietà. L'applicazione deve usare questo identificatore per le richieste automatiche successive.

Oltre a `identifier` , `MSALAccount` l'oggetto contiene un identificatore visualizzabile denominato `username` . Che viene convertito `userId` in adal. `username` non è considerato un identificatore univoco e può essere modificato in qualsiasi momento, quindi deve essere usato solo per gli scenari di compatibilità con le versioni precedenti con ADAL. MSAL supporta le query cache usando `username` o `identifier` , in cui è consigliabile eseguire query per `identifier` .

La tabella seguente riepiloga le differenze tra gli identificatori di account tra ADAL e MSAL:

| Identificatore account                | MSAL                                                         | ADAL 2.7. x      | ADAL precedenti (prima di ADAL 2.7. x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| identificatore visualizzabile            | `username`                                                   | `userId`        | `userId`                       |
| Identificatore univoco non visualizzabile | `identifier`                                                 | `homeAccountId` | N/D                            |
| Nessun ID account noto               | Eseguire query su tutti gli account tramite l' `allAccounts:` API in `MSALPublicClientApplication` | N/D             | N/D                            |

Si tratta dell' `MSALAccount` interfaccia che fornisce gli identificatori seguenti:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO da MSAL a ADAL

Se si dispone di un'app MSAL e di un'app ADAL e l'utente accede per la prima volta all'app basata su MSAL, è possibile ottenere l'accesso SSO nell'app ADAL salvando l' `username` `MSALAccount` oggetto dall'oggetto e passandolo all'app basata su adal come `userId` . ADAL può quindi trovare le informazioni sull'account in modo invisibile all'utente con l' `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` API.

### <a name="sso-from-adal-to-msal"></a>SSO da ADAL a MSAL

Se si dispone di un'app MSAL e di un'app ADAL e l'utente accede per la prima volta all'app basata su ADAL, è possibile usare gli identificatori utente ADAL per le ricerche di account in MSAL. Questo vale anche per la migrazione da ADAL a MSAL.

#### <a name="adals-homeaccountid"></a>HomeAccountId di ADAL

ADAL 2.7. x restituisce l' `homeAccountId` oggetto nell' `ADUserInformation` oggetto nel risultato tramite questa proprietà:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` in ADAL è equivalente `identifier` a in MSAL. È possibile salvare questo identificatore da usare in MSAL per le ricerche di account con l' `accountForIdentifier:error:` API.

#### <a name="adals-userid"></a>ADAL `userId`

Se `homeAccountId` non è disponibile o si dispone solo dell'identificatore visualizzabile, è possibile usare adal `userId` per cercare l'account in MSAL.

In MSAL, cercare prima di tutto un account per `username` o `identifier` . Usare sempre `identifier` per l'esecuzione di query, se presente, e usare solo `username` come fallback. Se l'account viene trovato, usare l'account nelle `acquireTokenSilent` chiamate.

Objective-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



API di ricerca account supportate da MSAL:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2. x-2.6.6

In questa sezione vengono descritte le differenze SSO tra MSAL e ADAL 2. x-2.6.6.

Le versioni precedenti di ADAL non supportano in modo nativo il formato della cache MSAL. Tuttavia, per garantire una migrazione uniforme da ADAL a MSAL, MSAL è in grado di leggere il formato di cache ADAL precedente senza richiedere di nuovo le credenziali utente.

Poiché `homeAccountId` non è disponibile nelle versioni precedenti di Adal, è necessario cercare gli account usando `username` :

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Ad esempio:

Objective-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



In alternativa, è possibile leggere tutti gli account, che leggeranno anche le informazioni sull'account da ADAL:

Objective-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Flussi di autenticazione e scenari di applicazioni](authentication-flows-app-scenarios.md)
