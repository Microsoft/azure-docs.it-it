---
title: Guida alle licenze per la collaborazione B2B-Azure Active Directory | Microsoft Docs
description: La Collaborazione B2B di Azure Active Directory non richiede le licenze di Azure AD a pagamento, ma è anche possibile ottenere le funzionalità a pagamento per gli utenti guest B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74868848"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Linee guida sulla Collaborazione B2B di Azure Active Directory

Con la collaborazione business-to-business (B2B) di Azure Active Directory (Azure AD), è possibile invitare utenti esterni (o "utenti guest") a usare i servizi di Azure AD a pagamento. Alcune funzionalità sono gratuite, ma per tutte le funzionalità a pagamento Azure AD è possibile invitare fino a cinque utenti guest per ogni licenza di Azure AD Edition di cui si è proprietari per un dipendente o un utente non Guest nel tenant.

> [!NOTE]
> Per informazioni dettagliate sui prezzi Azure AD e sulle funzionalità di collaborazione B2B, fare riferimento a [Azure Active Directory prezzi](https://azure.microsoft.com/pricing/details/active-directory/) .

Le licenze degli utenti guest B2B vengono calcolate e segnalate automaticamente in base al rapporto 1:5. 

In più, gli utenti guest possono usare le funzionalità gratuite di Azure AD senza alcun requisito di licenza aggiuntiva. Gli utenti guest hanno accesso alle funzionalità gratuite di Azure AD anche se non si hanno licenze di Azure AD a pagamento. 

## <a name="examples-calculating-guest-user-licenses"></a>Esempi: Calcolare le licenze utente guest
Dopo aver determinato il numero di utenti guest necessario ad accedere ai servizi di Azure AD a pagamento, assicurarsi di avere licenze a pagamento di Azure AD sufficienti a coprire gli utenti guest nel rapporto 1: 5 obbligatorio. Di seguito sono riportati alcuni esempi:

- Si vuole invitare gli utenti Guest 100 per le app o i servizi Azure AD e fornire la gestione e il provisioning degli accessi. Per 50 di questi utenti guest, è anche necessario richiedere l'autenticazione a più fattori e l'accesso condizionale. Pertanto, per queste funzionalità sono necessarie 10 Azure AD Premium P1 licenze. Se si prevede di usare le funzionalità di Identity Protection con gli utenti guest, per includerli saranno necessarie le licenze P2 di Azure AD Premium con lo stesso rapporto 1:5.
- Si vogliono invitare 60 utenti guest che richiedono tutti l'autenticazione a più fattori, quindi è necessario avere almeno 12 licenze P1 di Azure AD Premium. Si hanno 10 dipendenti con licenze P1 di Azure AD Premium, che consentono fino a 50 utenti guest con il rapporto licenze 1:5. È necessario acquistare due licenze Premium P1 aggiuntive per includere 10 utenti guest aggiuntivi.

## <a name="next-steps"></a>Passaggi successivi

Vedere le risorse seguenti su Collaborazione B2B di Azure AD:

* [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](faq.md)
