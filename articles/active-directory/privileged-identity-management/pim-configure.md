---
title: Informazioni su Privileged Identity Management - Azure AD | Microsoft Docs
description: Fornisce una panoramica su Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 09/29/2020
ms.author: curtand
ms.custom: pim ; azuread-video-2020
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c7003ce3e8c65f6fdb7f3d847947114aee251f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370186"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Che cos'è Azure AD Privileged Identity Management?

 Privileged Identity Management (PIM) di Azure Active Directory (Azure AD) è un servizio che permette di gestire, controllare e monitorare l'accesso a risorse importanti nell'organizzazione. Queste risorse sono incluse in Azure AD, in Azure e in altri Microsoft Online Services, ad esempio Microsoft 365 o Microsoft Intune. Il video seguente illustra le funzionalità e i concetti importanti di PIM.
<br><br>

> [!VIDEO https://www.youtube.com/embed/f-0K7mRUPpQ]

## <a name="reasons-to-use"></a>Vantaggi

Le organizzazioni vogliono ridurre al minimo il numero di utenti che hanno accesso a informazioni o risorse protette per ridurre le probabilità che un attore malintenzionato acceda al sistema, o che un utente autorizzato abbia inavvertitamente effetto su una risorsa sensibile. Tuttavia, gli utenti devono comunque eseguire operazioni con privilegi, nelle app di Azure AD, Azure, Microsoft 365 o SaaS. Le organizzazioni possono concedere agli utenti l'accesso con privilegi JIT alle risorse di Azure e ad Azure AD. È necessario procedere con una supervisione di ciò che gli utenti fanno con i loro privilegi di amministratori.

## <a name="what-does-it-do"></a>Funzione

Privileged Identity Management fornisce l'attivazione del ruolo basata sul tempo e sull'approvazione per attenuare i rischi di autorizzazioni di accesso eccessive, inutili o usate in modo improprio per le risorse di importanza strategica. Di seguito sono riportate alcune delle funzionalità principali di Privileged Identity Management:

- Concedere l'accesso con privilegi **JIT** ad Azure AD e alle risorse di Azure
- Assegnare l'accesso **con vincoli di tempo** alle risorse usando dati di inizio e fine
- Richiedere l'**approvazione** per attivare i ruoli con privilegi
- Applicare l'**autenticazione a più fattori** per attivare qualsiasi ruolo
- Usare la **giustificazione** per comprendere i motivi delle attivazioni da parte degli utenti
- Ricevere **notifiche** all'attivazione dei ruoli con privilegi
- Condurre **verifiche di accesso** per assicurarsi che gli utenti necessitino ancora dei ruoli
- Scaricare la **cronologia di controllo** per il controllo interno o esterno

## <a name="what-can-i-do-with-it"></a>Che si può fare con questa funzionalità?

Dopo aver configurato Privileged Identity Management, saranno visibili le opzioni **Attività**, **Gestisci** e **Attività** nel menu di spostamento a sinistra. In qualità di amministratore, è possibile scegliere tra opzioni come la gestione dei **ruoli di Azure AD**, la gestione dei **ruoli delle risorse di Azure** o i gruppi di accesso con privilegi. Quando si sceglie ciò che si vuole gestire, verrà visualizzato il set di opzioni appropriato in base all'opzione.

![Screenshot di Privileged Identity Management nel portale di Azure](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Chi può fare cosa?

Per i ruoli di Azure AD in Privileged Identity Management, solo un utente che appartiene al ruolo Amministratore ruolo con privilegi o Amministratore globale può gestire le assegnazioni per altri amministratori. È possibile [concedere l'accesso ad altri amministratori per gestire Privileged Identity Management](pim-how-to-give-access-to-pim.md). Gli amministratori globali, gli amministratori della sicurezza e i ruoli con autorizzazioni di lettura globali possono visualizzare le assegnazioni ai ruoli di Azure AD in Privileged Identity Management.

Per i ruoli delle risorse di Azure in Privileged Identity Management, le assegnazioni per altri amministratori possono essere gestite solo da un amministratore della sottoscrizione, da un proprietario della risorsa o da un amministratore Accesso utenti della risorsa. Gli utenti amministratori del ruolo con privilegi, gli amministratori della sicurezza o i Ruoli con autorizzazioni di lettura per la sicurezza non dispongono, per impostazione predefinita, dell'accesso per visualizzare le assegnazioni ai ruoli delle risorse di Azure in Privileged Identity Management.

## <a name="scenarios"></a>Scenari

Privileged Identity Management supporta gli scenari seguenti:

### <a name="privileged-role-administrator-permissions"></a>Autorizzazioni dell'amministratore del ruolo con privilegi

- Abilitare l’approvazione per ruoli specifici
- Specificare gli utenti o i gruppi di responsabili approvazione per l'approvazione delle richieste
- Visualizzare la cronologia delle richieste e delle approvazioni per tutti i ruoli con privilegi

### <a name="approver-permissions"></a>Autorizzazioni del responsabile approvazione

- Visualizzare le approvazioni (richieste) in sospeso
- Approvare o rifiutare le richieste di elevazione del ruolo (singolarmente e in blocco)
- Fornire una giustificazione per l'approvazione o il rifiuto

### <a name="eligible-role-user-permissions"></a>Autorizzazioni dell'utente del ruolo idonee

- Richiedere l’attivazione di un ruolo che richiede l’approvazione
- Visualizzare lo stato della richiesta da attivare
- Completare l’attività in Azure AD se l’attivazione è stata approvata

## <a name="terminology"></a>Terminologia

Per una migliore comprensione di Privileged Identity Management e della documentazione, esaminare la terminologia seguente.

| Termine o concetto | Categoria di assegnazione di ruolo | Descrizione |
| --- | --- | --- |
| idoneo | Type | Un'assegnazione di ruolo che richiede a un utente di eseguire una o più azioni per usare il ruolo. Se un utente è stato reso idoneo per un ruolo, potrà attivare il ruolo quando avrà bisogno di svolgere le attività con privilegi. Non esiste alcuna differenza sostanziale tra l'accesso concesso a un utente con l'assegnazione permanente e quello con l'assegnazione di idoneità al ruolo. L'unica differenza è che alcuni utenti non necessitano dell'accesso continuo. |
| active | Type | Un'assegnazione di ruolo che non richiede a un utente di eseguire alcuna azione per usare il ruolo. Gli utenti con questo tipo di assegnazione hanno i privilegi assegnati al ruolo. |
| activate |  | Il processo di esecuzione di una o più azioni per usare un ruolo per cui un utente è idoneo. Le azioni possono includere il completamento di un controllo di autenticazione a più fattori (MFA), l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte di responsabili dell'approvazione designati. |
| assegnato | State | Un utente che ha un'assegnazione di ruolo attiva. |
| attivato | State | Un utente che ha un'assegnazione di ruolo idonea, ha eseguito le azioni per attivare il ruolo ed è ora attivo.  Dopo l'attivazione, l'utente può usare il ruolo per un periodo di tempo preconfigurato prima che sia necessario ripeterla. |
| idonea permanente | Duration | Un'assegnazione di ruolo in cui un utente è sempre idoneo ad attivare il ruolo. |
| attiva permanente | Duration | Un'assegnazione di ruolo in cui un utente può sempre usare il ruolo senza eseguire alcuna azione. |
| idonea dopo | Duration | Un'assegnazione di ruolo in cui un utente è idoneo ad attivare il ruolo tra una data di inizio e una data di fine specificate. |
| attiva dopo | Duration | Un'assegnazione di ruolo in cui un utente può usare il ruolo senza eseguire alcuna azione tra una data di inizio e fine specificate. |
| accesso JIT (Just-In-Time) |  | Un modello in base al quale gli utenti ricevono autorizzazioni temporanee per eseguire attività con privilegi, che impedisce a utenti non autorizzati o malintenzionati di ottenere l'accesso dopo la scadenza delle autorizzazioni. L'accesso viene concesso solo quando l'utente ne ha necessità. |
| principio di accesso con privilegi minimi |  | Una procedura consigliata per la sicurezza in base alla quale ogni utente viene dotato solo dei privilegi minimi necessari per eseguire le attività che è autorizzato a svolgere. Ciò consente di ridurre al minimo il numero di amministratori globali usando in alternativa ruoli di amministratore specifici per determinati scenari. |

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Per altre informazioni sulle licenze per gli utenti, vedere [Requisiti di licenza per usare Privileged Identity Management](subscription-requirements.md).

## <a name="next-steps"></a>Passaggi successivi

- [Requisiti di licenza per usare Privileged Identity Management](subscription-requirements.md)
- [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Distribuire Privileged Identity Management](pim-deployment-plan.md)
