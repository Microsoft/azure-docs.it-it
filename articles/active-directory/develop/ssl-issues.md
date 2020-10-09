---
title: Risolvere i problemi relativi a TLS/SSL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle operazioni da eseguire per diversi problemi usando i certificati TLS/SSL con MSAL. Libreria Objective-C.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80881078"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Procedura: risolvere i problemi relativi a MSAL per iOS e macOS TLS/SSL

Questo articolo fornisce informazioni utili per la risoluzione dei problemi che possono verificarsi durante l'uso di [Microsoft Authentication Library (MSAL) per iOS e MacOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Problemi di rete

**Errore-1200**: "si è verificato un errore SSL e non è possibile effettuare una connessione protetta al server".

Questo errore indica che la connessione non è protetta. Si verifica quando un certificato non è valido. Per ulteriori informazioni, tra cui il server che non ha superato il controllo TLS, fare riferimento a `NSURLErrorFailingURLErrorKey` nel `userInfo` dizionario dell'oggetto Error.

Questo errore è stato dalla libreria di rete di Apple. Un elenco completo dei codici di errore NSURL si trova in NSURLError. h negli SDK macOS e iOS. Per ulteriori informazioni su questo errore, vedere [codici di errore del sistema di caricamento degli URL](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Problemi relativi ai certificati

Se l'URL che fornisce un certificato non valido si connette al server che si intende usare come parte del flusso di autenticazione, è consigliabile iniziare a diagnosticare il problema verificando l'URL con un servizio di convalida SSL, ad esempio il [test del server SSL](https://www.ssllabs.com/ssltest/analyze.html). Verifica il server su una vasta gamma di scenari e browser e controlla la presenza di molte vulnerabilità note.

Per impostazione predefinita, la nuova funzionalità [ATS (app Transport Security)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) di Apple applica criteri di sicurezza più rigorosi alle app che usano certificati TLS/SSL. Per impostazione predefinita, alcuni sistemi operativi e Web browser hanno iniziato ad applicare alcuni di questi criteri. Per motivi di sicurezza, è consigliabile non disabilitare ATS.

I certificati che usano hash SHA-1 hanno vulnerabilità note. La maggior parte dei Web browser moderni non consente certificati con hash SHA-1.

## <a name="captive-portals"></a>Portali Captive

Un portale captive presenta una pagina Web a un utente quando accede per la prima volta a una rete di Wi-Fi e non ha ancora concesso l'accesso alla rete. Intercetta il traffico Internet fino a quando l'utente non soddisfa i requisiti del portale. Errori di rete perché l'utente non è in grado di connettersi alle risorse di rete finché l'utente non si connette tramite il portale.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [Portali Captive](https://en.wikipedia.org/wiki/Captive_portal) e la nuova funzionalità [ATS (app Transport Security)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) di Apple.
