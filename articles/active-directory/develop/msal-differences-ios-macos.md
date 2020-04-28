---
title: Differenze di MSAL per iOS & macOS | Azure
titleSuffix: Microsoft identity platform
description: Descrive le differenze di utilizzo di Microsoft Authentication Library (MSAL) tra iOS e macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084973"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Differenze tra Microsoft Authentication Library per iOS e macOS

In questo articolo vengono illustrate le differenze di funzionalità tra Microsoft Authentication Library (MSAL) per iOS e macOS.

> [!NOTE]
> Nel Mac, MSAL supporta solo le app macOS.

## <a name="general-differences"></a>Differenze generali

MSAL per macOS è un subset delle funzionalità disponibili per iOS.

MSAL per macOS non supporta:

- tipi di `ASWebAuthenticationSession`browser diversi, ad `SFAuthenticationSession`esempio `SFSafariViewController`,,.
- l'autenticazione negoziata tramite l'app Microsoft Authenticator non è supportata per macOS.

La condivisione del keychain tra le app dello stesso server di pubblicazione è più limitata in macOS 10,14 e versioni precedenti. Usare gli [elenchi di controllo di accesso](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) per specificare i percorsi delle app che devono condividere il keychain. L'utente può visualizzare richieste di Keychain aggiuntive.

In macOS 10.15 + il comportamento di MSAL è lo stesso tra iOS e macOS. MSAL usa i [gruppi di accesso Keychain](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) per la condivisione keychain. 

### <a name="conditional-access-authentication-differences"></a>Differenze di autenticazione dell'accesso condizionale

Per gli scenari di accesso condizionale, quando si usa MSAL per iOS saranno presenti meno richieste utente. Questo perché iOS usa l'app Broker (Microsoft Authenticator) che nega la necessità di richiedere all'utente in alcuni casi.

### <a name="project-setup-differences"></a>Differenze di configurazione del progetto

**macOS**

- Quando si configura il progetto in macOS, assicurarsi che l'applicazione sia firmata con un certificato di sviluppo o di produzione valido. MSAL funziona comunque in modalità non firmata, ma si comporta in modo diverso per quanto riguarda la persistenza della cache. L'app deve essere eseguita solo senza segno a scopo di debug. Se si distribuisce l'app senza firma, verrà:
1. Il 10,14 e versioni precedenti MSAL chiederà all'utente una password di Keychain ogni volta che riavvia l'app.
2. In 10.15 + MSAL richiederà all'utente le credenziali per ogni acquisizione di token. 

- le app macOS non devono implementare la chiamata AppDelegate.

**iOS**

- Per configurare il progetto in modo da supportare il flusso di Service Broker di autenticazione, è necessario eseguire altri passaggi. I passaggi sono indicati nell'esercitazione.
- i progetti iOS devono registrare schemi personalizzati in info. plist. Questa operazione non è necessaria in macOS.
