---
title: Che cosa sono i report di Azure Active Directory? | Microsoft Docs
description: Offre una panoramica generale sui report di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c07500ec432d176b2cf74aa95f0a1c943348f9f
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229517"
---
# <a name="what-are-azure-active-directory-reports"></a>Che cosa sono i report di Azure Active Directory?

I report di Azure Active Directory (Azure AD) offrono una visualizzazione completa delle attività nell'ambiente specifico. I dati forniti consentono di:

- Determinare l'utilizzo di app e servizi da parte degli utenti
- Rilevare rischi potenziali che pregiudicano l'integrità dell'ambiente
- Risolvere problemi che influiscono negativamente sulla produttività degli utenti  

L'architettura dei report si basa su due elementi fondamentali:

- [Report sulla sicurezza](#security-reports)
- [Report attività](#activity-reports)

![Report](./media/overview-reports/01.png)


## <a name="security-reports"></a>Report sulla sicurezza

I report sulla sicurezza consentono di proteggere le identità dell'organizzazione. Esistono due tipi di report sulla sicurezza:

- **Utenti contrassegnati per il rischio**: il [report sulla sicurezza relativo agli utenti contrassegni per il rischio](../identity-protection/overview-identity-protection.md) offre una panoramica degli account utente che potrebbero essere stati compromessi.

- **Accessi a rischio**: il [report sulla sicurezza relativo agli accessi a rischio](../identity-protection/overview-identity-protection.md) offre una indicazione sui tentativi di accesso che potrebbero essere stati eseguiti da qualcuno che non è il legittimo proprietario di un account utente. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Licenza di Azure AD necessaria per accedere a un report sulla sicurezza  

Tutte le edizioni di Azure Active Directory offrono report sugli utenti contrassegnati per il rischio e sugli accessi a rischio. Tuttavia, il livello di granularità dei report varia a seconda delle edizioni: 

- Nelle edizioni **Azure Active Directory Free e Basic**  è incluso un elenco degli utenti contrassegnati per il rischio e degli accessi a rischio. 

- Nell'edizione **Azure Active Directory Premium 1** questo modello consente anche di esaminare alcuni rilevamenti dei rischi sottostanti che sono stati riscontrati per ogni report. 

- L'edizione **Azure Active Directory Premium 2** offre informazioni più dettagliate sui rilevamenti dei rischi sottostanti e permette anche di configurare criteri di sicurezza che rispondono automaticamente a livelli di rischio configurati.


## <a name="activity-reports"></a>Report sull’attività

I report sull'attività consentono di comprendere il comportamento degli utenti nell'organizzazione. Azure Active Directory prevede due tipi di report sull'attività:

- **Log di controllo**: il [report sull'attività relativo ai log di controllo](concept-audit-logs.md) consente di accedere alla cronologia di ogni attività eseguita nel tenant.

- **Accessi**: il [report sull'attività relativo agli accessi](concept-sign-ins.md) permette di determinare chi ha eseguito le attività segnalate nel report dei log di controllo.


### <a name="audit-logs-report"></a>Report dei log di controllo 

Il [report dei log di controllo](concept-audit-logs.md) include i record delle attività di sistema per la conformità. Questi dati consentono di risolvere scenari comuni, ad esempio:

- Un utente del tenant ha avuto accesso a un gruppo amministrativo e si vuole sapere chi ha concesso tale accesso. 

- Si vuole ottenere un elenco degli utenti che accedono a un'app specifica caricata di recente, per sapere se viene usata.

- Si vuole conoscere il numero di reimpostazioni della password eseguite nel tenant.


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Informazioni sulla licenza di Azure AD necessaria per accedere al report dei log di controllo  

Il report dei log di controllo è disponibile per le funzionalità per le quali si possiede una licenza. Se è disponibile una licenza per una funzionalità specifica, si ha anche accesso alle relative informazioni del log di controllo. Per un confronto dettagliato delle funzionalità in base ai [diversi tipi di licenza](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses), vedere la [pagina dei prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Per altre informazioni, vedere [Caratteristiche e funzionalità di Azure Active Directory](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad).

### <a name="sign-ins-report"></a>Report sugli accessi

Il [report relativo agli accessi](concept-sign-ins.md) permette di rispondere a domande come:

- Qual è il modello di accesso di un utente?
- Quanti utenti hanno effettuato l'accesso nell'arco di una settimana?
- Qual è lo stato di questi accessi?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Informazioni sulla licenza di Azure AD necessaria per accedere al report sull'attività relativo agli accessi  

Per visualizzare il report sull'attività relativo agli accessi, è necessario che al tenant sia associata una licenza di Azure AD Premium

## <a name="programmatic-access"></a>Accesso a livello di codice

Oltre all'interfaccia utente, Azure AD offre anche l'[accesso a livello di codice](concept-reporting-api.md) ai dati dei report tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti. 

## <a name="next-steps"></a>Passaggi successivi

- [Report sugli accessi a rischio](../identity-protection/overview-identity-protection.md)
- [Report dei log di controllo](concept-audit-logs.md)
- [Report dei log di accesso](concept-sign-ins.md)