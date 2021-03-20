---
title: Configurare il keychain
titleSuffix: Microsoft identity platform
description: Informazioni su come configurare keychain in modo che l'app possa memorizzare i token nella cache nel keychain.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 06e197a6e445c7dc1179be696318905f2132ee36
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85477737"
---
# <a name="configure-keychain"></a>Configurare il keychain

Quando [Microsoft Authentication Library per iOS e MacOS](msal-overview.md) (MSAL) accede a un utente o aggiorna un token, tenta di memorizzare nella cache i token nel keychain. La memorizzazione nella cache dei token nel Keychain consente a MSAL di fornire Single Sign-On invisibile (SSO) tra più app distribuite dallo stesso sviluppatore Apple. L'accesso SSO viene effettuato tramite la funzionalità gruppi di accesso keychain. Per ulteriori informazioni, vedere la [documentazione relativa agli elementi Keychain](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)di Apple.

Questo articolo illustra come configurare i diritti delle app in modo che MSAL possa scrivere i token memorizzati nella cache nel Keychain iOS e macOS.

## <a name="default-keychain-access-group"></a>Gruppo di accesso a keychain predefinito

### <a name="ios"></a>iOS

Per impostazione predefinita, MSAL in iOS usa il `com.microsoft.adalcache` gruppo di accesso. Questo è il gruppo di accesso condiviso usato dagli SDK MSAL e Autenticazione di Azure AD Library (ADAL) e garantisce la migliore esperienza di Single Sign-On (SSO) tra più app dello stesso server di pubblicazione.

In iOS aggiungere il `com.microsoft.adalcache` gruppo di portachiavi al diritto dell'app in Xcode in **Impostazioni progetto**  >  **funzionalità**  >  **condivisione Keychain**

### <a name="macos"></a>macOS

Per impostazione predefinita, MSAL in macOS usa il `com.microsoft.identity.universalstorage` gruppo di accesso.

A causa delle limitazioni dei portachiavi macOS, MSAL `access group` non esegue direttamente la conversione nell'attributo del gruppo di accesso a keychain (vedere [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) in MacOS 10,14 e versioni precedenti. Tuttavia, si comporta in modo analogo da una prospettiva SSO garantendo che più applicazioni distribuite dallo stesso sviluppatore Apple possano avere l'accesso SSO invisibile all'utente.

In macOS 10,15 e versioni successive (macOS Catalina), MSAL usa l'attributo del gruppo di accesso a Keychain per ottenere l'accesso SSO invisibile all'utente, in modo analogo a iOS.

## <a name="custom-keychain-access-group"></a>Gruppo di accesso a keychain personalizzato

Se si vuole usare un gruppo di accesso a keychain diverso, è possibile passare il gruppo personalizzato durante la creazione `MSALPublicClientApplicationConfig` prima della creazione, come indicato di seguito `MSALPublicClientApplication` :

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Disabilitare la condivisione Keychain

Se non si vuole condividere lo stato SSO tra più app o se si usa un gruppo di accesso a keychain, disabilitare la condivisione del keychain passando l'ID bundle dell'applicazione come keychainGroup:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Handle-34018 errore (non è stato possibile impostare l'elemento nel Keychain)

Errore-34018 normalmente significa che il keychain non è stato configurato correttamente. Verificare che il gruppo di accesso a keychain configurato in MSAL corrisponda a quello configurato nei diritti.

## <a name="ensure-your-application-is-properly-signed"></a>Assicurarsi che l'applicazione sia firmata correttamente

In macOS, le applicazioni possono essere eseguite senza essere firmate dallo sviluppatore. Anche se la maggior parte delle funzionalità di MSAL continuerà a funzionare, l'accesso SSO tramite l'accesso Keychain richiede la firma dell'applicazione. Se si verificano più richieste di Keychain, assicurarsi che la firma dell'applicazione sia valida.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui gruppi di accesso a keychain, vedere l'articolo relativo alla [condivisione dell'accesso di Apple per gli elementi Keychain tra una raccolta di app](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) .
