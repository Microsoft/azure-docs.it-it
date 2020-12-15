---
title: Assegnare o rimuovere licenze - Azure Active Directory | Microsoft Docs
description: Istruzioni su come assegnare o rimuovere le licenze di Azure Active Directory da utenti o gruppi.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6900647acf7182529f34c8cc065dbb039de38be
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504407"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Assegnare o rimuovere licenze nel portale di Azure Active Directory

Molti servizi di Azure Active Directory (Azure AD) richiedono la concessione di licenze a ogni utente o gruppo (e membri associati) per quel servizio. Solo gli utenti con licenze attive saranno in grado di accedere e utilizzare i servizi con licenza Azure AD per i quali è vero. Le licenze vengono applicate per ogni tenant e non vengono trasferiti ad altri tenant. 

## <a name="available-license-plans"></a>Piani di licenza disponibili

Sono disponibili diversi piani di licenza per il servizio Azure AD, tra cui:

- Azure AD Free

- Azure AD Premium P1

- Azure AD Premium P2

Per informazioni specifiche su ogni piano di licenza e i dettagli relativi alle licenze associate, vedere [quali sono le licenze necessarie?](https://azure.microsoft.com/pricing/details/active-directory/). Per iscriversi a Azure AD piani di licenza Premium, vedere [qui](./active-directory-get-started-premium.md).

Non tutti i servizi Microsoft sono disponibili in tutte le posizioni. Prima che una licenza possa essere assegnata a un gruppo, è necessario specificare la **Località di utilizzo** per tutti i membri. È possibile impostare questo valore nell'area **Azure Active Directory &gt; Utenti &gt; Profilo &gt; Impostazioni** in Azure AD. Qualsiasi utente la cui località di utilizzo non è specificata eredita il percorso dell'organizzazione Azure AD.

## <a name="view-license-plans-and-plan-details"></a>Visualizzare i piani di licenza e i dettagli del piano

È possibile visualizzare i piani di servizio disponibili, incluse le singole licenze, controllare le date di scadenza in sospeso e visualizzare il numero di assegnazioni disponibili.

### <a name="to-find-your-service-plan-and-plan-details"></a>Per trovare il piano di servizio e i dettagli del piano

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account amministratore licenze nell'organizzazione Azure ad.

1. Selezionare **Azure Active Directory**, e quindi **Licenze**.

    :::image type="content" source="media/license-users-groups/license-details-blade.png" alt-text="Pagina licenze con numero di servizi acquistati e licenze assegnate":::

1. Selezionare **tutti i prodotti** per visualizzare la pagina tutti i prodotti e visualizzare i numeri **totali**, **assegnati**, **disponibili** e **scaduti** per i piani di licenza.

    :::image type="content" source="media/license-users-groups/license-products-blade-with-products.png" alt-text="pagina dei servizi-con i piani di licenza del servizio-informazioni sulle licenze associate":::

    > [!NOTE]
    > I numeri sono definiti come segue: 
    > - Totale: numero totale di licenze acquistate
    > - Assegnato: numero di licenze assegnate agli utenti
    > - Disponibile: numero di licenze disponibili per l'assegnazione, inclusa la scadenza imminente
    > - Scadenza imminente: numero di licenze a breve scadenza

1. Selezionare un nome di piano per visualizzare gli utenti e i gruppi con licenza.

## <a name="assign-licenses-to-users-or-groups"></a>Assegnare licenze a utenti o gruppi

Verificare che chiunque necessiti di usare un servizio Azure AD concesso in licenza abbia la licenza appropriata. È possibile aggiungere i diritti di licenza per gli utenti o per un intero gruppo.

### <a name="to-assign-a-license-to-a-user"></a>Per assegnare una licenza a un utente

1. Nella pagina **prodotti** selezionare il nome del piano di licenza che si desidera assegnare all'utente.

    ![pagina dei servizi, con il piano di licenza del servizio evidenziato](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Nella pagina panoramica piano di licenza selezionare **assegna**.

    ![pagina dei servizi, con l'opzione di assegnazione evidenziata](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Nella pagina **Assegna** selezionare **Utenti e gruppi** e quindi cercare e selezionare l'utente a cui si sta assegnando la licenza.

    ![Pagina Assegna licenza con le opzioni di ricerca e selezione evidenziate](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Selezionare **Opzioni di assegnazione**, verificare che siano attivate le opzioni di licenza appropriate e fare clic su **OK**.

    ![Pagina di opzioni di licenza, con tutte le opzioni disponibili nel piano di licenza](media/license-users-groups/license-option-blade-assignments.png)

    La pagina **Assegna licenza** viene aggiornata per indicare che un utente è selezionato e che le assegnazioni sono configurate.

    > [!NOTE]
    > Non tutti i servizi Microsoft sono disponibili in tutte le posizioni. Prima che una licenza possa essere assegnata a un utente, è necessario specificare la **Località di utilizzo**. È possibile impostare questo valore nell'area **Azure Active Directory &gt; Utenti &gt; Profilo &gt; Impostazioni** in Azure AD. Qualsiasi utente la cui località di utilizzo non è specificata eredita il percorso dell'organizzazione Azure AD.

1. Selezionare **Assegna**.

    L'utente viene aggiunto all'elenco degli utenti con licenza e può accedere ai servizi di Azure AD inclusi.
    > [!NOTE]
    > Le licenze possono anche essere assegnate direttamente a un utente dalla pagina **licenze** dell'utente. Se un utente dispone di una licenza assegnata tramite l'appartenenza a un gruppo e si desidera assegnare la stessa licenza direttamente all'utente, è possibile eseguire questa operazione solo dalla pagina **prodotti** indicata nel passaggio 1.

### <a name="to-assign-a-license-to-a-group"></a>Per assegnare una licenza a un gruppo

1. Nella pagina **prodotti** selezionare il nome del piano di licenza che si desidera assegnare all'utente.

    ![Pannello prodotti, con piano di licenza prodotto evidenziato](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Nella pagina **Azure Active Directory Premium - Piano 2** selezionare **Assegna**.

    ![Pagina Prodotti con l'opzione Assegna evidenziata](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Nella pagina **Assegna** selezionare **Utenti e gruppi** e quindi cercare e selezionare il gruppo a cui si sta assegnando la licenza.

    ![Assegnare una pagina di licenza, con ricerca evidenziata e selezionare le opzioni 2](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Selezionare **Opzioni di assegnazione**, verificare che siano attivate le opzioni di licenza appropriate e fare clic su **OK**.

    ![Pagina di opzioni di licenza, con tutte le opzioni disponibili nel piano di licenza 2](media/license-users-groups/license-option-blade-group-assignments.png)

    La pagina **Assegna licenza** viene aggiornata per indicare che un utente è selezionato e che le assegnazioni sono configurate.

1. Selezionare **Assegna**.

    Il gruppo viene aggiunto all'elenco di gruppi con licenza e tutti i membri hanno accesso ai servizi di Azure AD inclusi.

## <a name="remove-a-license"></a>Rimuovere una licenza

È possibile rimuovere una licenza dalla pagina utente Azure AD utente, dalla pagina Panoramica gruppo per un'assegnazione di gruppo o dalla pagina **licenze** Azure ad per visualizzare gli utenti e i gruppi per una licenza.

### <a name="to-remove-a-license-from-a-user"></a>Per rimuovere una licenza da un utente

1. Nella pagina **utenti con licenza** per il piano di servizio, selezionare l'utente che non deve più avere la licenza. Ad esempio, _Alain Charon_.

1. Selezionare **Rimuovi licenza**.

    ![Pagina Utenti con licenza con l'opzione Rimuovi licenza evidenziata](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Non è possibile rimuovere direttamente le licenze che un utente eredita da un gruppo. In alternativa, è necessario rimuovere l'utente dal gruppo da cui sta ereditando la licenza.

### <a name="to-remove-a-license-from-a-group"></a>Per rimuovere una licenza da un gruppo

1. Nella pagina **gruppi con licenza** per il piano di licenza selezionare il gruppo che non dovrebbe più avere la licenza.

1. Selezionare **Rimuovi licenza**.

    ![Pagina dei gruppi con licenza con l'opzione Rimuovi licenza evidenziata 2](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Quando un account utente locale sincronizzato con Azure AD non rientra nell'ambito della sincronizzazione o quando la sincronizzazione viene rimossa, l'utente viene eliminato temporaneamente nel Azure AD. In tal caso, le licenze assegnate all'utente direttamente o tramite le licenze basate sui gruppi verranno contrassegnate come **sospese** anziché **eliminate**.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver assegnato le licenze, è possibile eseguire i processi seguenti:

- [Identificare e risolvere i problemi relativi alle licenze](../enterprise-users/licensing-groups-resolve-problems.md)

- [Aggiungere gli utenti con licenza a un gruppo per la gestione delle licenze](../enterprise-users/licensing-groups-migrate-users.md)

- [Scenari, limitazioni e problemi noti relativi all'uso dei gruppi per gestire le licenze in Azure Active Directory](../enterprise-users/licensing-group-advanced.md)

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)
