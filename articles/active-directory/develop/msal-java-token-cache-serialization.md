---
title: Serializzazione della cache di token personalizzata (MSAL4j)
titleSuffix: Microsoft identity platform
description: Informazioni su come serializzare la cache dei token per MSAL per Java
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 31b908eacd5f9f8610ad2c0a985fa7e0be8d1505
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85479335"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Serializzazione della cache dei token personalizzati in MSAL per Java

Per salvare in modo permanente la cache dei token tra le istanze dell'applicazione, sarà necessario personalizzare la serializzazione. Le classi e le interfacce Java necessarie per la serializzazione della cache dei token sono le seguenti:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): interfaccia che rappresenta la cache del token di sicurezza.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): interfaccia che rappresenta l'operazione di esecuzione del codice prima e dopo l'accesso. @Override *BeforeCacheAccess* e *afterCacheAccess* con la logica responsabile della serializzazione e deserializzazione della cache.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): interfaccia che rappresenta il contesto in cui viene eseguito l'accesso alla cache del token. 

Di seguito è riportata un'implementazione Naive della serializzazione personalizzata della serializzazione/deserializzazione della cache dei token. Non copiarlo e incollarlo in un ambiente di produzione.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Altre informazioni

Informazioni su [come ottenere e rimuovere gli account dalla cache dei token usando MSAL per Java](msal-java-get-remove-accounts-token-cache.md).
