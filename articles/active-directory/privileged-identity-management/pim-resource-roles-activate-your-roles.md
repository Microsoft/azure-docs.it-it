---
title: Attivare i ruoli delle risorse di Azure in PIM-Azure AD | Microsoft Docs
description: Informazioni su come attivare i ruoli di risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f38ef7db114705392bd1d3dc6f9a4562a809e20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86023874"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Attiva i ruoli delle risorse di Azure in Privileged Identity Management

Usare Privileged Identity Management (PIM) per consentire ai membri del ruolo idonei per le risorse di Azure di pianificare l'attivazione per una data e un'ora future. Possono anche selezionare una durata specifica dell'attivazione entro il valore massimo configurato dagli amministratori.

Questo articolo è per i membri che devono attivare il proprio ruolo di risorse di Azure in Privileged Identity Management.

## <a name="activate-a-role"></a>Attivare un ruolo

Quando è necessario eseguire un ruolo di risorse di Azure, è possibile richiedere l'attivazione usando l'opzione di navigazione **ruoli personali** in Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure ad Privileged Identity Management**. Per informazioni su come aggiungere il riquadro Privileged Identity Management al dashboard, vedere iniziare a [usare Privileged Identity Management](pim-getting-started.md).

1. Selezionare **ruoli personali**.

    ![Pagina ruoli personali che mostra i ruoli che è possibile attivare](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Selezionare i **ruoli delle risorse di Azure** per visualizzare un elenco dei ruoli delle risorse di Azure idonei.

   ![Ruoli personali-pagina ruoli risorse di Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. Nell'elenco dei **ruoli delle risorse di Azure** trovare il ruolo da attivare.

    ![Ruoli delle risorse di Azure-elenco dei ruoli idonei](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Selezionare **Activate (attiva** ) per aprire la pagina Activate (attiva).

     ![Il riquadro di attivazione aperto con ambito, ora di inizio, durata e motivo](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. Se il ruolo richiede l'autenticazione a più fattori, fare clic su **Verificare la propria identità prima di procedere**. È sufficiente eseguire l'autenticazione una volta per sessione.

    ![Verificare l'identità con l'autenticazione a più fattori prima dell'attivazione del ruolo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Selezionare **verifica l'identità** e seguire le istruzioni per fornire la verifica aggiuntiva di sicurezza.

    ![Schermata per fornire la verifica della sicurezza, ad esempio un codice PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se si desidera specificare un ambito ridotto, selezionare **ambito** per aprire il riquadro filtro risorse.

    È consigliabile richiedere solo l'accesso alle risorse necessarie. Nel riquadro Filtro della risorsa, è possibile specificare i gruppi di risorse o le risorse a cui è necessario accedere.

    ![Attiva-riquadro filtro risorse per specificare l'ambito](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessario, specificare un'ora di inizio di attivazione personalizzata. Il membro verrà attivato dopo l'ora selezionata.

1. Nella casella **Motivo** immettere il motivo della richiesta di attivazione.

    ![Riquadro di attivazione completato con ambito, ora di inizio, durata e motivo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Selezionare **Attiva**.

    Se il [ruolo richiede l'approvazione](pim-resource-roles-approval-workflow.md) per l'attivazione, nell'angolo superiore destro del browser verrà visualizzata una notifica che informa che la richiesta è in attesa di approvazione.

    ![La richiesta di attivazione è una notifica di approvazione in sospeso](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Visualizzare lo stato della richiesta da attivare

È possibile visualizzare lo stato delle richieste in attesa da attivare.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare **richieste personali** per visualizzare un elenco delle richieste di ruolo di Azure ad e di risorse di Azure.

    ![Richieste personali-pagina delle risorse di Azure che mostra le richieste in sospeso](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Scorrere verso destra per visualizzare la colonna **Stato richiesta**.

## <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

Nel caso in cui non è richiesta l'attivazione di un ruolo che richiede l'approvazione, è possibile annullare una richiesta in sospeso in qualsiasi momento.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare **richieste personali**.

1. Per il ruolo che si desidera annullare, selezionare il collegamento **Annulla** .

    Quando si seleziona Annulla, la richiesta verrà annullata. Per attivare nuovamente il ruolo, è necessario inviare una nuova richiesta per l'attivazione.

   ![Elenco di richieste con azione di annullamento evidenziato](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Le autorizzazioni non vengono concesse dopo l'attivazione di un ruolo

Quando si attiva un ruolo in Privileged Identity Management, è possibile che l'attivazione non venga propagata immediatamente a tutti i portali che richiedono il ruolo privilegiato. In alcuni casi, anche se la modifica viene propagata, la memorizzazione nella cache Web in un portale può comportare che la modifica non abbia effetto immediato. Se l'attivazione viene ritardata, è necessario eseguire le operazioni seguenti.

1. Disconnettersi dal portale di Azure e quindi eseguire nuovamente l'accesso.
1. In Privileged Identity Management verificare di essere elencati come membri del ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Attivare i ruoli di Azure AD in Privileged Identity Management](pim-how-to-activate-role.md)
