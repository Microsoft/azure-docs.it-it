---
title: Convalidare le regole per l'appartenenza dinamica ai gruppi (anteprima)-Azure AD | Microsoft Docs
description: Come testare i membri rispetto a una regola di appartenenza per un gruppo dinamico in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1048284e8e7a492bf0810a16e29409546ed414b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547560"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Convalida di una regola di appartenenza dinamica a un gruppo (anteprima) in Azure Active Directory

Azure Active Directory (Azure AD) ora fornisce i mezzi per convalidare le regole di gruppo dinamiche (in anteprima pubblica). Nella scheda **Convalida regole** è possibile convalidare la regola dinamica rispetto ai membri del gruppo di esempio per confermare che la regola funziona come previsto. Quando si creano o si aggiornano le regole dinamiche del gruppo, gli amministratori desiderano sapere se un utente o un dispositivo sarà membro del gruppo. In questo modo è possibile valutare se l'utente o il dispositivo soddisfano i criteri della regola e la risoluzione dei problemi quando l'appartenenza non è prevista.

## <a name="step-by-step-walk-through"></a>Procedura dettagliata per le procedure dettagliate

Per iniziare, passare a **Azure Active Directory**  >  **gruppi**. Selezionare un gruppo dinamico esistente o creare un nuovo gruppo dinamico e fare clic su regole di appartenenza dinamica. È quindi possibile visualizzare la scheda **Convalida regole** .

![Trovare la scheda Convalida regole e iniziare con una regola esistente](./media/groups-dynamic-rule-validation/validate-tab.png)

Nella scheda **Convalida regole** è possibile selezionare gli utenti per convalidare le relative appartenenze. è possibile selezionare 20 utenti o dispositivi alla volta.

![Aggiungere utenti per convalidare la regola esistente rispetto a](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Dopo aver scelto gli utenti o i dispositivi dal selettore e **selezionare**, la convalida verrà avviata automaticamente e verranno visualizzati i risultati della convalida.

![Visualizzare i risultati della convalida della regola](./media/groups-dynamic-rule-validation/validate-tab-results.png)

I risultati indicano se un utente è un membro del gruppo. Se la regola non è valida o si verifica un problema di rete, il risultato verrà visualizzato come **sconosciuto**. Nel caso di **Unknown**, il messaggio di errore dettagliato descrive il problema e le azioni necessarie.

![Visualizzare i dettagli dei risultati della convalida della regola](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

È possibile modificare la regola e la convalida delle appartenenze verrà attivata. Per visualizzare i motivi per cui l'utente non è un membro del gruppo, fare clic su "Visualizza dettagli" e i dettagli di verifica visualizzeranno il risultato di ogni espressione che compone la regola. Fare clic su **OK** per uscire.

## <a name="next-steps"></a>Passaggi successivi

- [Regole di appartenenza dinamiche per i gruppi](groups-dynamic-membership.md)
