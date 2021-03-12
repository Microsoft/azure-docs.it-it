---
title: Utenti e gruppi nei criteri di accesso condizionale-Azure Active Directory
description: Utenti e gruppi in un Azure AD criteri di accesso condizionale
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f316b17096e1241fe23cbf2c965122fd8966522
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630878"
---
# <a name="conditional-access-users-and-groups"></a>Accesso condizionale: utenti e gruppi

Un criterio di accesso condizionale deve includere un'assegnazione utente come uno dei segnali nel processo decisionale. Gli utenti possono essere inclusi o esclusi dai criteri di accesso condizionale. Azure Active Directory valuta tutti i criteri e garantisce che tutti i requisiti siano soddisfatti prima di concedere l'accesso all'utente. Oltre a questo articolo, abbiamo un video su [come includere o escludere gli utenti dai criteri di accesso condizionale](https://www.youtube.com/watch?v=5DsW1hB3Jqs) in cui viene illustrato il processo descritto di seguito. 

![Utente come segnale nelle decisioni prese dall'accesso condizionale](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Includi utenti

Questo elenco di utenti include in genere tutti gli utenti a cui è destinata un'organizzazione in un criterio di accesso condizionale. 

Quando si creano criteri di accesso condizionale, è possibile includere le opzioni seguenti.

- nessuno
   - Nessun utente selezionato
- tutti gli utenti
   - Tutti gli utenti presenti nella directory, inclusi i guest B2B.
- Selezionare Utenti e gruppi
   - Tutti gli utenti guest ed esterni
      - Questa selezione include tutti i guest B2B e gli utenti esterni, incluso qualsiasi utente con l' `user type` attributo impostato su `guest` . Questa selezione si applica anche a qualsiasi utente esterno connesso da un'organizzazione diversa, ad esempio un provider di soluzioni cloud (CSP). 
   - Ruoli della directory
      - Consente agli amministratori di selezionare i ruoli di directory Azure AD predefiniti specifici utilizzati per determinare l'assegnazione dei criteri. Ad esempio, le organizzazioni possono creare criteri più restrittivi per gli utenti assegnati al ruolo di amministratore globale. Altri tipi di ruolo non sono supportati, inclusi i ruoli della directory con ambito unità amministrativa, i ruoli personalizzati.
   - Utenti e gruppi
      - Consente la destinazione di set di utenti specifici. Ad esempio, le organizzazioni possono selezionare un gruppo che contiene tutti i membri del reparto risorse umane quando si seleziona un'app per le risorse umane come app cloud. Un gruppo può essere un gruppo di qualsiasi tipo in Azure AD, inclusi gruppi di sicurezza e distribuzione dinamici o assegnati. I criteri verranno applicati a utenti e gruppi annidati.

> [!IMPORTANT]
> Quando si selezionano gli utenti e i gruppi inclusi in un criterio di accesso condizionale, esiste un limite al numero di singoli utenti che è possibile aggiungere direttamente a un criterio di accesso condizionale. Se è presente una grande quantità di singoli utenti che devono essere aggiunti direttamente a un criterio di accesso condizionale, è consigliabile inserire gli utenti in un gruppo e assegnare il gruppo ai criteri di accesso condizionale.

> [!WARNING]
> Se gli utenti o i gruppi sono membri di oltre 2048 gruppi, l'accesso potrebbe essere bloccato. Questo limite si applica sia all'appartenenza a gruppi diretta che al gruppo annidato.

> [!WARNING]
> I criteri di accesso condizionale non supportano gli utenti assegnati a un ruolo della directory [con ambito a un'unità amministrativa](../roles/admin-units-assign-roles.md) o a ruoli della directory con ambito diretto a un oggetto, ad esempio tramite [ruoli personalizzati](../roles/custom-create.md).

## <a name="exclude-users"></a>Escludi utenti

Quando le organizzazioni includono ed escludono un utente o un gruppo, l'utente o il gruppo viene escluso dal criterio, in quanto un'azione di esclusione esegue l'override di un criterio Includi nei criteri. Le esclusioni vengono comunemente usate per gli account di accesso di emergenza o break-Glass. Altre informazioni sugli account di accesso di emergenza e sul motivo per cui sono importanti sono disponibili negli articoli seguenti: 

* [Gestire gli account di accesso di emergenza in Azure AD](../roles/security-emergency-access.md)
* [Creare una strategia di gestione di controllo di accesso resiliente con Azure Active Directory](../authentication/concept-resilient-controls.md)

Quando si creano criteri di accesso condizionale, è possibile escludere le opzioni seguenti.

- Tutti gli utenti guest ed esterni
   - Questa selezione include tutti i guest B2B e gli utenti esterni, incluso qualsiasi utente con l' `user type` attributo impostato su `guest` . Questa selezione si applica anche a qualsiasi utente esterno connesso da un'organizzazione diversa, ad esempio un provider di soluzioni cloud (CSP). 
- Ruoli della directory
   - Consente agli amministratori di selezionare ruoli di directory Azure AD specifici utilizzati per determinare l'assegnazione. Ad esempio, le organizzazioni possono creare criteri più restrittivi per gli utenti assegnati al ruolo di amministratore globale.
- Utenti e gruppi
   - Consente la destinazione di set di utenti specifici. Ad esempio, le organizzazioni possono selezionare un gruppo che contiene tutti i membri del reparto risorse umane quando si seleziona un'app per le risorse umane come app cloud. Un gruppo può essere un gruppo di qualsiasi tipo in Azure AD, inclusi gruppi di sicurezza e distribuzione dinamici o assegnati.

### <a name="preventing-administrator-lockout"></a>Prevenzione del blocco amministratore

Per impedire a un amministratore di bloccarsi dalla propria directory quando si crea un criterio applicato a **tutti gli utenti** e a **tutte le app**, verrà visualizzato il seguente avviso.

> Non bloccarsi. Si consiglia di applicare un criterio a un piccolo set di utenti prima di verificare che si comportano come previsto. È inoltre consigliabile escludere almeno un amministratore da questo criterio. In questo modo si garantisce che sia ancora possibile accedere a un criterio e aggiornarlo se è necessaria una modifica. Verificare gli utenti e le app interessati.

Per impostazione predefinita, il criterio fornirà un'opzione per escludere l'utente corrente dal criterio, ma questa impostazione predefinita può essere sostituita dall'amministratore, come illustrato nella figura seguente. 

![Avviso: non bloccarsi.](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

[Cosa fare se si è bloccati dal portale di Azure?](troubleshoot-conditional-access.md#what-to-do-if-you-are-locked-out-of-the-azure-portal)

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: App o azioni cloud](concept-conditional-access-cloud-apps.md)

- [Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)
