---
title: Dipendenze del servizio di accesso condizionale-Azure Active Directory
description: Informazioni sul modo in cui vengono usate le condizioni nell'accesso condizionale Azure Active Directory per attivare un criterio.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6592add5e33ba240c0f1d9fdbd23d82adfe5229
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258609"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Che cosa sono le dipendenze del servizio in Azure Active Directory l'accesso condizionale? 

Con i criteri di accesso condizionale, è possibile specificare i requisiti di accesso ai siti Web e ai servizi. Ad esempio, i requisiti di accesso possono includere la richiesta di autenticazione a più fattori o di [dispositivi gestiti](require-managed-devices.md). 

Quando si accede direttamente a un sito o a un servizio, l'effetto di un criterio correlato è in genere facile da valutare. Se, ad esempio, si dispone di un criterio che richiede l'autenticazione a più fattori per SharePoint Online, viene applicato l'autenticazione a più fattori per ogni accesso al portale Web di SharePoint. Tuttavia, non è sempre semplice valutare l'effetto di un criterio perché sono presenti app cloud con dipendenze da altre app cloud. Ad esempio, Microsoft teams può fornire l'accesso alle risorse in SharePoint Online. Quindi, quando si accede a Microsoft teams nello scenario corrente, si è anche soggetti ai criteri di autenticazione a più fattori di SharePoint. 

> [!TIP]
> L'uso dell'app [office 365](concept-conditional-access-cloud-apps.md#office-365) è destinato a tutte le app di Office per evitare problemi con le dipendenze del servizio nello stack di Office.

## <a name="policy-enforcement"></a>Imposizione dei criteri 

Se è stata configurata una dipendenza del servizio, è possibile applicare i criteri tramite l'imposizione ad associazione anticipata o ad associazione tardiva. 

- L' **imposizione di criteri ad associazione anticipata** indica che un utente deve soddisfare i criteri del servizio dipendenti prima di accedere all'app chiamante. Ad esempio, un utente deve soddisfare i criteri di SharePoint prima di accedere a Microsoft teams. 
- L' **imposizione di criteri ad associazione tardiva** si verifica dopo che l'utente accede all'app chiamante. L'imposizione è rinviata a quando si chiamano le richieste dell'app, un token per il servizio downstream. Gli esempi includono MS teams che accedono a Planner e Office.com che accedono a SharePoint. 

Il diagramma seguente illustra le dipendenze del servizio MS teams. Le frecce solide indicano l'imposizione ad associazione anticipata. la freccia tratteggiata per Planner indica l'imposizione ad associazione tardiva. 

![Dipendenze del servizio MS Teams](./media/service-dependencies/01.png)

Come procedura consigliata, è consigliabile impostare criteri comuni tra app e servizi correlati, quando possibile. Un comportamento di sicurezza coerente offre la migliore esperienza utente. Ad esempio, l'impostazione di un criterio comune tra Exchange Online, SharePoint Online, Microsoft teams e Skype for business riduce significativamente le richieste impreviste che possono verificarsi da criteri diversi applicati ai servizi downstream. 

Un ottimo modo per eseguire questa operazione con le applicazioni nello stack di Office consiste nell'usare l' [app office 365](concept-conditional-access-cloud-apps.md#office-365) anziché indirizzare le singole applicazioni.

La tabella seguente elenca le dipendenze del servizio aggiuntive, in cui le app client devono soddisfare  

| App client         | Servizio downstream                          | Imposizione |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Gestione Microsoft Azure (portale e API) | Ad associazione anticipata |
| Classe Microsoft | Exchange                                    | Ad associazione anticipata |
|                     | SharePoint                                  | Ad associazione anticipata |
| Microsoft Teams     | Exchange                                    | Ad associazione anticipata |
|                     | Microsoft Planner                                  | Ad associazione tardiva  |
|                     | Microsoft Stream                            | Ad associazione tardiva  |
|                     | SharePoint                                  | Ad associazione anticipata |
|                     | Skype for Business Online                   | Ad associazione anticipata |
| Portale di Office       | Exchange                                    | Ad associazione tardiva  |
|                     | SharePoint                                  | Ad associazione tardiva  |
| Gruppi di Outlook      | Exchange                                    | Ad associazione anticipata |
|                     | SharePoint                                  | Ad associazione anticipata |
| PowerApps           | Gestione Microsoft Azure (portale e API) | Ad associazione anticipata |
|                     | Microsoft Azure Active Directory              | Ad associazione anticipata |
| Project             | Dynamics CRM                                | Ad associazione anticipata |
| Skype for Business  | Exchange                                    | Ad associazione anticipata |
| Visual Studio       | Gestione Microsoft Azure (portale e API) | Ad associazione anticipata |
| Microsoft Forms     | Exchange                                    | Ad associazione anticipata |
|                     | SharePoint                                  | Ad associazione anticipata |
| Microsoft To-Do     | Exchange                                    | Ad associazione anticipata |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come implementare l'accesso condizionale nell'ambiente in uso, vedere [Pianificare la distribuzione dell'accesso condizionale in Azure Active Directory](plan-conditional-access.md).
