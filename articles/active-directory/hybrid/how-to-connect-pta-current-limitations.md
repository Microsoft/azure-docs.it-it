---
title: 'Azure AD Connect: Autenticazione pass-through - Limitazioni correnti | Microsoft Docs'
description: Questo articolo descrive le limitazioni correnti dell'autenticazione pass-through di Azure Active Directory (Azure AD).
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e9c4489f59f72e4d0b5c7a0b911da188eb0828c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280197"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticazione pass-through di Azure Active Directory - Limitazioni correnti

>[!IMPORTANT]
>L'autenticazione pass-through di Azure Active Directory (Azure AD) è una funzionalità gratuita che può essere usata senza acquistare edizioni a pagamento. L'autenticazione pass-through è disponibile solo nell'istanza di Azure AD a livello globale, non nel [cloud Microsoft Azure Germania](https://www.microsoft.de/cloud-deutschland) o nel [cloud Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenari supportati

Sono supportati gli scenari indicati di seguito:

- Accessi utente alle applicazioni basate su Web browser.
- Accessi utente ai client di Outlook usando protocolli legacy, ad esempio Exchange ActiveSync, EAS, SMTP, POP e IMAP.
- Accessi utente ad applicazioni client legacy di Office e applicazioni di Office che supportano [l'autenticazione moderna](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview): versioni di Office 2013 e 2016.
- Accessi utente alle applicazioni di protocolli legacy, ad esempio PowerShell versione 1.0 e altre.
- Azure AD Join per dispositivi Windows 10.
- Password di app per Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Scenari non supportati

Gli scenari seguenti _non_ sono supportati:

- Rilevamento di utenti con [credenziali perse](../identity-protection/overview-identity-protection.md).
- Azure AD Domain Services necessita della sincronizzazione dell'hash delle password per l'abilitazione nel tenant. I tenant che usano _solo_ l'autenticazione pass-through non funzionano per gli scenari che richiedono Azure AD Domain Services.
- L'autenticazione pass-through non è integrata con [Azure AD Connect Health](./whatis-azure-ad-connect.md).

> [!IMPORTANT]
> Come soluzione alternativa _solo_ per gli scenari non supportati, (ad eccezione dell'integrazione Azure AD Connect Health) abilitare la sincronizzazione dell'hash delle password nella pagina [Funzionalità facoltative](how-to-connect-install-custom.md#optional-features) della procedura guidata di Azure AD Connect.
> 
> [!NOTE]
> Abilitando questa funzionalità è possibile effettuare il failover dell'autenticazione se il funzionamento dell'infrastruttura locale è interrotto. Il failover dall'autenticazione pass-through alla sincronizzazione dell'hash delle password non avviene automaticamente. È necessario cambiare il metodo di accesso manualmente con Azure AD Connect. Se il server che esegue Azure AD Connect smette di funzionare, è necessario chiedere assistenza al supporto tecnico Microsoft per disattivare l'autenticazione pass-through.

## <a name="next-steps"></a>Passaggi successivi
- [Avvio rapido](how-to-connect-pta-quick-start.md): iniziare a usare l'autenticazione pass-through di Azure AD.
- [Eseguire la migrazione da AD FS all'autenticazione pass-through](https://aka.ms/ADFSTOPTADPDownload): una guida dettagliata per la migrazione da AD FS (o altre tecnologie federative) per l'autenticazione pass-through.
- [Blocco smart](../authentication/howto-password-smart-lockout.md): apprendere come configurare la funzionalità di blocco smart nel tenant per proteggere gli account utente.
- [Approfondimento tecnico](how-to-connect-pta-how-it-works.md): comprendere come funziona l'autenticazione pass-through.
- [Domande frequenti](how-to-connect-pta-faq.md): trovare risposte alle domande frequenti sulla funzionalità di autenticazione pass-through.
- [Risoluzione dei problemi](tshoot-connect-pass-through-authentication.md): apprendere come risolvere i problemi comuni relativi alla funzionalità di autenticazione pass-through.
- [Approfondimento sulla sicurezza](how-to-connect-pta-security-deep-dive.md): ottenere informazioni tecniche approfondite sulla funzionalità di autenticazione pass-through.
- [Accesso Single Sign-On facile di Azure AD](how-to-connect-sso.md): ottenere altre informazioni su questa funzionalità complementare.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): usare il forum di Azure Active Directory per inviare richieste di nuove funzionalità.
