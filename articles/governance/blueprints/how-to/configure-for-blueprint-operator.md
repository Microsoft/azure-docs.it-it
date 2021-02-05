---
title: Configurare l'ambiente per l'operatore Blueprint
description: Informazioni su come configurare l'ambiente Azure per l'uso con il ruolo predefinito operatore di Azure.
ms.date: 02/05/2021
ms.topic: how-to
ms.openlocfilehash: 0e0f6680ab39481a480919af10fadc0f7103a1fb
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591602"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Configurare l'ambiente per un operatore di Blueprint

La gestione delle definizioni dei progetti e delle assegnazioni di progetto può essere assegnata a team diversi. È comune che un progettista o un team di governance sia responsabile della gestione del ciclo di vita delle definizioni del progetto, mentre un team operativo è responsabile della gestione delle assegnazioni di tali definizioni di progetto controllate centralmente.

Il ruolo predefinito **operatore progetto** è stato progettato in modo specifico per l'uso in questo tipo di scenario. Il ruolo consente ai team dei tipi di operazioni di gestire l'assegnazione delle definizioni del progetto dell'organizzazione, ma non di modificarle. Questa operazione richiede una configurazione nell'ambiente Azure e in questo articolo vengono illustrati i passaggi necessari.

## <a name="grant-permission-to-the-blueprint-operator"></a>Concedere l'autorizzazione all'operatore Blueprint

Il primo passaggio consiste nel concedere il ruolo di **operatore Blueprint** all'account o al gruppo di sicurezza (scelta consigliata) che sta per assegnare i progetti. Questa azione deve essere eseguita al livello più elevato nella gerarchia del gruppo di gestione che comprende tutti i gruppi di gestione e le sottoscrizioni a cui il team operativo deve disporre dell'accesso di assegnazione progetto. Quando si concedono tali autorizzazioni, è consigliabile seguire il principio dei privilegi minimi.

1. Consigliabile [Creare un gruppo di sicurezza e aggiungere membri](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Aggiungere un'assegnazione di ruolo](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) dell' **operatore Blueprint** all'account o al gruppo di sicurezza

## <a name="user-assign-managed-identity"></a>Utente-assegna identità gestita

Una definizione di progetto può usare identità gestite assegnate dal sistema o dall'utente. Tuttavia, quando si usa il ruolo **operatore progetto** , la definizione del progetto deve essere configurata in modo da usare un'identità gestita assegnata dall'utente. Inoltre, all'account o al gruppo di sicurezza a cui viene concesso il ruolo di **operatore Blueprint** deve essere concesso il ruolo di **operatore identità gestito** nell'identità gestita assegnata dall'utente. Senza questa autorizzazione, le assegnazioni di progetto hanno esito negativo a causa della mancanza di autorizzazioni.

1. [Creare un'identità gestita assegnata dall'utente](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) per l'uso da un progetto assegnato.

1. Concedere all'identità gestita assegnata dall'utente tutti i ruoli o le autorizzazioni richieste dalla definizione di progetto per l'ambito previsto.

1. [Aggiungere un'assegnazione di ruolo](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) dell' **operatore di identità gestito** all'account o al gruppo di sicurezza. Definire l'ambito dell'assegnazione di ruolo alla nuova identità gestita assegnata dall'utente.

1. Come **operatore del progetto**, [assegnare un progetto](../create-blueprint-portal.md#assign-a-blueprint) che usi la nuova identità gestita assegnata dall'utente.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).