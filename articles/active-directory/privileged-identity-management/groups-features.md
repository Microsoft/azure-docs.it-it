---
title: Gestione di gruppi di Azure AD con privilegi in PIM (Privileged Identity Management) | Microsoft Docs
description: Come gestire i membri e i proprietari dei gruppi con accesso con privilegi in PIM (Privileged Identity Management)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759781fd61cd42d05f2823d390e99d128dd2fcac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512393"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>Funzionalità di gestione per i gruppi di Azure AD con accesso con privilegi (anteprima)

In PIM (Privileged Identity Management) è ora possibile assegnare l'idoneità per l'appartenenza o la proprietà dei gruppi con accesso con privilegi. A partire da questa versione di anteprima, è possibile assegnare i ruoli predefiniti di Azure Active Directory (Azure AD) ai gruppi cloud e usare PIM per gestire l'idoneità e l'attivazione come membri e proprietari di gruppi. Per altre informazioni sui gruppi assegnabili a ruoli in Azure AD, vedere [Usare i gruppi cloud per gestire le assegnazioni di ruoli in Azure Active Directory (anteprima)](../roles/groups-concept.md).

>[!Important]
> Per assegnare un gruppo dotato dell'accesso con privilegi a un ruolo per l'accesso amministrativo a Exchange, al Centro sicurezza e conformità o a SharePoint, usare l'esperienza **Ruoli e amministratori** del portale di Azure AD e non l'esperienza dei gruppi con accesso con privilegi per rendere l'utente o il gruppo idoneo per l'attivazione nel gruppo.

## <a name="require-different-policies-for-each-role-assignable-group"></a>Richiedere criteri diversi per ogni gruppo assegnabile a ruoli

Alcune organizzazioni usano strumenti come la collaborazione B2B (business-to-business) di Azure AD per invitare i propri partner come utenti guest nell'organizzazione di Azure AD. Invece di un singolo criterio JIT per tutte le assegnazioni a un ruolo con privilegi, è possibile creare due diversi gruppi con accesso con privilegi, ognuno con specifici criteri. È possibile applicare requisiti meno stringenti per i dipendenti attendibili e requisiti più stringenti come il flusso di lavoro di approvazione per i partner che richiedono l'attivazione nel gruppo assegnato.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>Attivare più assegnazioni di ruoli in una singola richiesta

Con l'anteprima dei gruppi con accesso con privilegi, è possibile concedere agli amministratori specifici del carico di lavoro l'accesso rapido a più ruoli con una singola richiesta JIT. Ad esempio, per gli amministratori di Office di livello 3 potrebbe essere necessario l'accesso JIT ai ruoli di amministratore di Exchange, delle app di Office, di Teams e della ricerca per esaminare accuratamente gli eventi imprevisti giornalieri. In passato, per realizzare questo obiettivo sarebbero state necessarie quattro richieste consecutive, un processo che richiede tempo. Ora è invece possibile creare un gruppo assegnabile a ruoli denominato "Amministratori di Office di livello 3", assegnarlo a ognuno dei quattro ruoli indicati in precedenza (o a qualsiasi ruolo predefinito di Azure AD) e abilitarlo per l'accesso con privilegi nella sezione Attività del gruppo. Una volta abilitato il gruppo per l'accesso con privilegi, è possibile configurare le impostazioni JIT per i relativi membri e assegnare amministratori e proprietari come idonei. Quando gli amministratori vengono inseriti nel gruppo, diventano membri di tutti e quattro i ruoli di Azure AD.

## <a name="extend-and-renew-group-assignments"></a>Estendere e rinnovare le assegnazioni dei gruppi

Dopo aver configurato le assegnazioni di proprietario o membro con vincoli temporali, ci si potrebbe chiedere cosa succede se un'assegnazione scade. In questa nuova versione sono disponibili due opzioni per questo scenario:

- Estensione: quando un'assegnazione di ruolo sta per scadere, l'utente può usare Privileged Identity Management per richiedere un'estensione
- Rinnovo: quando un'assegnazione di ruolo sta per scadere, l'utente può usare Privileged Identity Management per richiedere un rinnovo

Per entrambe le azioni avviate dall'utente è necessaria l'approvazione di un amministratore globale o di un amministratore ruolo con privilegi. Gli amministratori non dovranno più occuparsi della gestione di queste scadenze. Possono semplicemente aspettare le richieste di estensione o di rinnovo e approvarle se sono valide.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un proprietario o un membro di un gruppo con accesso con privilegi](groups-assign-member-owner.md)
- [Approvare o rifiutare le richieste di attivazione per i membri e i proprietari dei gruppi con accesso con privilegi](groups-approval-workflow.md)
