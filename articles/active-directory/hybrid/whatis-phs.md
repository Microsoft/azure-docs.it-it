---
title: Informazioni sulla sincronizzazione dell'hash delle password con Azure AD | Microsoft Docs
description: Informazioni sulla sincronizzazione dell'hash delle password.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 568f284a22f170c2a6a0d60d9e2cfed0645937ba
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261376"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Informazioni sulla sincronizzazione dell'hash delle password con Azure AD
La sincronizzazione dell'hash delle password è uno dei metodi di accesso usati per gestire le identità ibride. Azure AD Connect consente di sincronizzare un hash della password di un utente da un'istanza di Active Directory locale a un'istanza di Azure AD basata sul cloud.

La sincronizzazione dell'hash delle password è un'estensione della funzionalità di sincronizzazione della directory implementata dal relativo servizio Azure AD Connect. È possibile usare questa funzionalità per accedere a servizi di Azure AD come Office 365. Per accedere al servizio, è necessario usare la stessa password usata per accedere all'istanza di Active Directory locale.

![Cos'è Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

La sincronizzazione dell'hash delle password consente di ridurre a una sola il numero di password necessarie agli utenti. Grazie alla sincronizzazione dell'hash delle password è possibile:

* Migliorare la produttività degli utenti.
* Ridurre i costi del supporto tecnico.  

Facoltativamente, è possibile configurare la sincronizzazione dell'hash delle password come backup se si decide di usare la [federazione con Active Directory Federation Services (ADFS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) come metodo di accesso.

Per usare la sincronizzazione dell'hash delle password nel proprio ambiente, è necessario:

* Installare Azure AD Connect.  
* Configurare la sincronizzazione della directory tra l'istanza di Active Directory locale e l'istanza di Azure Active Directory.
* Abilitare la sincronizzazione dell'hash delle password.



Per altre informazioni, vedere [Informazioni sull'identità ibrida](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'identità ibrida](whatis-hybrid-identity.md)
- [Informazioni su Azure AD Connect e Connect Health](whatis-azure-ad-connect.md)
- [Informazioni sull'autenticazione pass-through](how-to-connect-pta.md)
- [Informazioni sulla federazione](whatis-fed.md)
- [Informazioni su Single Sign-On](how-to-connect-sso.md)
- [Funzionamento della sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md)
