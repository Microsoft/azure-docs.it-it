---
title: 'Azure AD Connect: autenticazione pass-through | Documentazione Microsoft'
description: Questo articolo descrive l'autenticazione pass-through di Azure Active Directory (Azure AD) e come consenta gli accessi ad Azure AD tramite la convalida delle password degli utenti in Active Directory locale.
services: active-directory
keywords: cos'è l'autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa763c875b06bd7e22be0e814838f2e79b24e283
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85358022"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Accesso utente con l'autenticazione pass-through di Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>L'autenticazione pass-through di Azure Active Directory

L'autenticazione pass-through di Azure Active Directory (Azure AD) consente agli utenti di accedere sia ad applicazioni in locale che a quelle basate sul cloud usando le stesse password. Questa funzionalità offre agli utenti un'esperienza migliore, visto che hanno una password in meno da ricordare, e riduce i costi del supporto tecnico poiché è meno probabile che gli utenti dimentichino come eseguire l'accesso. Quando gli utenti eseguono l'accesso usando Azure AD, la funzionalità convalida le loro password direttamente con Active Directory locale.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Questa funzionalità è un'alternativa rispetto alla [sincronizzazione dell'hash delle password di Azure AD](how-to-connect-password-hash-synchronization.md), che offre alle organizzazioni gli stessi vantaggi dell'autenticazione cloud. Tuttavia, alcune organizzazioni che desiderano rafforzare la sicurezza di Active Directory locale e i criteri password possono scegliere di usare l'autenticazione pass-through. Riesaminare [questa guida](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) per confrontare i vari metodi di accesso di Azure AD e per scoprire come scegliere il metodo di accesso appropriato per l'organizzazione.

![Autenticazione pass-through di Azure AD](./media/how-to-connect-pta/pta1.png)

È possibile combinare l'autenticazione pass-through con la funzionalità [Accesso Single Sign-On facile](how-to-connect-sso.md). In questo modo, quando gli utenti accedono ad applicazioni dai computer aziendali all'interno della rete aziendale, non devono digitare la password per eseguire l'accesso.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Vantaggi principali dell'uso dell'autenticazione pass-through di Azure AD

- *Miglioramento dell'esperienza utente*
  - Gli utenti usano le stesse password per accedere ad applicazioni in locale e basate su cloud.
  - Gli utenti passano meno tempo con il supporto tecnico per risolvere problemi relativi alle password.
  - Gli utenti possono completare le attività di[ gestione self-service delle password](../authentication/active-directory-passwords-overview.md) nel cloud.
- *Facilità di distribuzione e gestione*
  - Non sono necessarie distribuzioni locali o configurazioni di rete complesse.
  - È necessario solo installare un agente leggero in locale.
  - Nessun sovraccarico di gestione. L'agente riceve automaticamente miglioramenti e correzioni di bug.
- *Sicurezza*
  - Le password locali non vengono mai archiviate nel cloud in alcuna forma.
  - Consente di proteggere gli account utente operando senza problemi con i [criteri di accesso condizionale di Azure AD](../active-directory-conditional-access-azure-portal.md), tra cui l'autenticazione a più fattori (MFA)[ e l'autenticazione legacy di blocco](../conditional-access/concept-conditional-access-conditions.md), e [impedendo attacchi di forza bruta alle password](../authentication/howto-password-smart-lockout.md).
  - L'agente esegue solo le connessioni in uscita dalla rete. Pertanto, non è necessario installarlo in una rete perimetrale.
  - La comunicazione tra un agente e Azure AD è protetta tramite l'autenticazione basata su certificato. Questi certificati vengono rinnovati automaticamente a intervalli di pochi mesi da Azure AD.
- *A disponibilità elevata*
  - È possibile installare altri agenti su più server locali per ottenere una disponibilità elevata delle richieste di accesso.

## <a name="feature-highlights"></a>Funzionalità in primo piano

- Supporta l'accesso utente in tutte le applicazioni basate su browser e nelle applicazioni client di Microsoft Office che usano l'[autenticazione moderna](https://aka.ms/modernauthga).
- I nomi utente per l'accesso possono essere il nome utente predefinito locale (`userPrincipalName`) o un altro attributo configurato in Azure AD Connect (noto come `Alternate ID`).
- La funzionalità funziona senza interruzioni con le funzionalità di [accesso condizionale](../active-directory-conditional-access-azure-portal.md) , ad esempio multi-factor authentication (multi-factor authentication), per proteggere gli utenti.
- Può essere integrata con la [gestione delle password self-service](../authentication/active-directory-passwords-overview.md) basata sul cloud, che include le attività di writeback delle password in Active Directory locale e di protezione delle password tramite il divieto di specificare password usate comunemente.
- Gli ambienti a più foreste sono supportati se sono presenti relazioni di trust tra le foreste AD e se il routing del suffisso del nome è configurato correttamente.
- È una funzionalità gratuita e non serve alcuna delle edizioni a pagamento di Azure AD per utilizzarla.
- Può essere abilitato tramite [Azure ad Connect](whatis-hybrid-identity.md).
- Usa un agente leggero locale che resta in ascolto e risponde alle richieste di convalida delle password.
- L'installazione di più agenti garantisce la disponibilità elevata di richieste di accesso.
- [Protegge](../authentication/howto-password-smart-lockout.md) gli account locali dagli attacchi di forza bruta alle password nel cloud.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva](how-to-connect-pta-quick-start.md) : iniziare a usare l'autenticazione pass-through Azure ad.
- [Eseguire la migrazione da AD FS all'autenticazione pass-through](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true): una guida dettagliata per la migrazione da AD FS (o altre tecnologie federative) per l'autenticazione pass-through.
- [Blocco intelligente](../authentication/howto-password-smart-lockout.md): configurare la funzionalità Blocco intelligente nel tenant per proteggere gli account utente.
- [Limitazioni correnti](how-to-connect-pta-current-limitations.md): informazioni su quali scenari sono supportati e quali non lo sono.
- [Approfondimento tecnico](how-to-connect-pta-how-it-works.md): informazioni sul funzionamento di questa funzionalità.
- [Domande frequenti](how-to-connect-pta-faq.md): risposte alle domande più frequenti.
- [Risoluzione dei problemi](tshoot-connect-pass-through-authentication.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [Approfondimento sulla sicurezza](how-to-connect-pta-security-deep-dive.md): informazioni tecniche approfondite sulla funzionalità.
- [Seamless Single Sign-On di Azure AD](how-to-connect-sso.md): altre informazioni su questa funzionalità complementare.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.
