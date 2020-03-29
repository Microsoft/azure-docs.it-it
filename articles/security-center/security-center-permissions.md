---
title: Autorizzazioni nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo articolo illustra come Centro sicurezza di Azure usa il controllo degli accessi in base al ruolo per assegnare autorizzazioni agli utenti e identificare le azioni consentite per ogni ruolo.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 0e60e782fa65cd5868bebe081673f9a158e07799
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921318"
---
# <a name="permissions-in-azure-security-center"></a>Autorizzazioni nel Centro sicurezza di Azure

Il Centro sicurezza di Azure usa il [controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md), con [ruoli predefiniti](../role-based-access-control/built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Centro sicurezza consente di valutare la configurazione delle risorse per identificare problemi di sicurezza e vulnerabilità. In Centro sicurezza gli utenti possono visualizzare solo informazioni relative a una risorsa quando dispongono del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse cui tali risorse appartengono.

Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

* **Ruolo con autorizzazioni di lettura per la sicurezza**: un utente che appartiene a questo ruolo dispone dei diritti di visualizzazione nel Centro sicurezza. L'utente può visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non può apportare modifiche.
* **Amministratore della protezione**: un utente che appartiene a questo ruolo ha gli stessi diritti del Ruolo con autorizzazioni di lettura per la sicurezza e può anche aggiornare i criteri di sicurezza e rimuovere gli avvisi e le raccomandazioni.

> [!NOTE]
> I ruoli di sicurezza, Ruolo con autorizzazioni di lettura per la sicurezza e Amministratore della protezione, hanno accesso solo al Centro sicurezza. I ruoli di sicurezza non hanno accesso ad altre aree del servizio di Azure come Archiviazione, Web e dispositivi mobili o Internet delle cose.
>
>

## <a name="roles-and-allowed-actions"></a>Ruoli e azioni consentite

La tabella seguente contiene i ruoli e le azioni consentite in Centro sicurezza.

| Ruolo | Modificare i criteri di sicurezza | Applicare i suggerimenti per la sicurezza per una risorsa</br> (incluso con 'Quick Fix!') | Ignorare gli avvisi e le raccomandazioni | Visualizzare gli avvisi e le raccomandazioni |
|:--- |:---:|:---:|:---:|:---:|
| Proprietario della sottoscrizione | ✔ | ✔ | ✔ | ✔ |
| Collaboratore alla sottoscrizione | -- | ✔ | ✔ | ✔ |
| Proprietario del gruppo di risorse | -- | ✔ | -- | ✔ |
| Collaboratore del gruppo di risorse | -- | ✔ | -- | ✔ |
| Reader | -- | -- | -- | ✔ |
| Amministratore della sicurezza | ✔ | -- | ✔ | ✔ |
| Ruolo con autorizzazioni di lettura per la sicurezza | -- | -- | -- | ✔ |

> [!NOTE]
> È consigliabile assegnare il ruolo con il minor numero di autorizzazioni che permetta agli utenti di completare le attività. Assegnare ad esempio il ruolo di lettore agli utenti che devono visualizzare solo le informazioni sull'integrità della sicurezza di una risorsa, ma non eseguono alcuna azione, come l'applicazione di consigli e la modifica di criteri.
>
>

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato descritto come Centro sicurezza usa PC usa il controllo degli accessi in base al ruolo per assegnare autorizzazioni agli utenti e sono state identificate le azioni consentite per ogni ruolo. Ora che è stata acquisita familiarità con le assegnazioni di ruolo necessari per monitorare lo stato di sicurezza della sottoscrizione, modificare i criteri di sicurezza e applicare i consigli, si apprenderà come eseguire queste operazioni:

- [Impostare i criteri di sicurezza nel Centro sicurezza](tutorial-security-policy.md)
- [Gestire i suggerimenti per la sicurezza in Centro sicurezza](security-center-recommendations.md)
- [Monitorare l'integrità della sicurezza delle risorse di Azure](security-center-monitoring.md)
- [Gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza](security-center-managing-and-responding-alerts.md)
- [Monitorare le soluzioni di sicurezza dei partner](security-center-partner-solutions.md)
