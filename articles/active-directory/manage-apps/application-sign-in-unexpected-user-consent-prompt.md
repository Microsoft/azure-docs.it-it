---
title: Richiesta di consenso imprevista al momento dell'accesso a un'applicazione | Microsoft Docs
description: Come risolvere il problema relativo a una richiesta di consenso imprevista visualizzata da un utente per un'applicazione che è stata integrata con Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6253458bb31388e5f7e6f84b7720cce0dc22886c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259961"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Richiesta di consenso imprevista al momento dell'accesso a un'applicazione

Molte applicazioni integrate in Azure Active Directory richiedono autorizzazioni di accesso a varie risorse per funzionare. Quando anche queste risorse sono integrate in Azure Active Directory, le autorizzazioni per il relativo accesso vengono richieste tramite il framework di consenso di Azure AD. 

Questo fa sì che venga visualizzata una richiesta di consenso al primo utilizzo dell'applicazione, in genere si tratta di un'operazione che viene eseguita una sola volta. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenari in cui gli utenti visualizzano richieste di consenso

Ulteriori richieste aggiuntive sono possibili in vari scenari:

* Il set di autorizzazioni richiesto dall'applicazione è stato modificato.

* L'utente che ha originariamente autorizzato l'applicazione non è un amministratore e ora un altro utente (non amministratore) sta utilizzando l'applicazione per la prima volta.

* L'utente che ha originariamente autorizzato l'applicazione è un amministratore, ma non ha dato il consenso per conto dell'intera organizzazione.

* L'applicazione utilizza il [consenso incrementale e dinamico](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) per richiedere ulteriori autorizzazioni dopo che inizialmente è stato concesso il consenso. Questo tipo di consenso viene frequentemente usato quando funzionalità facoltative di un'applicazione richiedono autorizzazioni ulteriori a quelle richieste per la funzionalità di base.

* Il consenso è stato revocato dopo essere stato inizialmente consentito.

* Lo sviluppatore ha configurato l'applicazione in modo che visualizzi una richiesta di consenso ogni volta che viene usata (nota: non è una procedura consigliata).

## <a name="next-steps"></a>Passaggi successivi

-   [App, autorizzazioni e consenso in Azure Active Directory (endpoint v1.0)](../develop/quickstart-register-app.md)

-   [Ambiti, autorizzazioni e consenso in Azure Active Directory (endpoint v2.0)](../develop/v2-permissions-and-consent.md)