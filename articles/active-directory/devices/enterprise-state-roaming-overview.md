---
title: Informazioni su Enterprise State Roaming in Azure Active Directory
description: Enterprise State Roaming offre agli utenti un'esperienza unificata in tutti i dispositivi Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77194280"
---
# <a name="what-is-enterprise-state-roaming"></a>Informazioni su Enterprise State Roaming

Con Windows 10 gli utenti di [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) ottengono la possibilità di sincronizzare in modo sicuro le proprie impostazioni utente e i dati delle impostazioni dell'applicazione sul cloud. Enterprise State Roaming offre agli utenti un'esperienza unificata in tutti i dispositivi Windows e riduce il tempo necessario per la configurazione di un nuovo dispositivo. Enterprise State Roaming funziona in modo simile alla [sincronizzazione delle impostazioni degli utenti](https://go.microsoft.com/fwlink/?linkid=2015135) standard introdotta per la prima volta in Windows 8. Il servizio Enterprise State Roaming offre anche i vantaggi seguenti:

* **Separazione di dati aziendali e dell'utente** : le organizzazioni mantengono i controlli dei propri dati e non esiste alcuna combinazione di dati aziendali in un account cloud di utenti o di dati utente in un account cloud aziendale.
* **Sicurezza migliorata** : i dati vengono crittografati automaticamente prima di lasciare il dispositivo Windows 10 dell'utente mediante Azure Rights Management (Azure RMS) e i dati inattivi rimangono crittografati sul cloud. Tutto il contenuto inattivo rimane crittografato sul cloud, ad eccezione degli spazi dei nomi, ad esempio i nomi delle impostazioni e i nomi delle app Windows.  
* **Servizi di gestione e monitoraggio migliorati** : garantiscono maggiore controllo e maggiore visibilità sugli utenti che sincronizzano le impostazioni nell’organizzazione e sui relativi dispositivi tramite l’integrazione del portale di Azure AD. 

Enterprise State Roaming è disponibile in più aree di Azure. L'elenco aggiornato delle aree disponibili è riportato nella pagina [Servizi di Azure in base all'area](https://azure.microsoft.com/regions/#services) in Azure Active Directory.

| Articolo | Descrizione |
| --- | --- |
| [Abilitare Enterprise State Roaming in Azure Active Directory](enterprise-state-roaming-enable.md) |Il servizio Enterprise State Roaming è disponibile per qualsiasi organizzazione con una sottoscrizione Premium di Azure Active Directory (Azure AD). Per altre informazioni su come ottenere una sottoscrizione di Azure AD, vedere la pagina del [prodotto Azure AD](https://azure.microsoft.com/services/active-directory). |
| [Domande frequenti su impostazioni e dati in roaming](enterprise-state-roaming-faqs.md) |Questo articolo offre le risposte ad alcune possibili domande degli amministratori IT in merito alle impostazioni e alla sincronizzazione dei dati delle app. |
| [Impostazioni di criteri di gruppo e MDM per la sincronizzazione delle impostazioni](enterprise-state-roaming-group-policy-settings.md) |Windows 10 offre impostazioni relative a Criteri di gruppo e ai criteri di gestione di dispositivi mobili per limitare la sincronizzazione delle impostazioni. |
| [Riferimento alle impostazioni di roaming di Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Elenco delle impostazioni di cui verrà effettuato il roaming e/o il backup in Windows 10. |
| [Risoluzione dei problemi](enterprise-state-roaming-troubleshooting.md) |Questo articolo illustra alcuni passaggi di base per la risoluzione dei problemi e contiene un elenco di problemi noti. |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'abilitazione di Enterprise State Roaming, vedere [Abilitare Enterprise State Roaming](enterprise-state-roaming-enable.md).
