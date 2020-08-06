---
title: Nascondere o eliminare un pacchetto di accesso nella gestione dei diritti-Azure AD
description: Informazioni su come nascondere o eliminare un pacchetto di accesso in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22aa97c6e627c2072636ca2e079877ff0f608b68
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798801"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Nascondere o eliminare un pacchetto di accesso in Azure AD gestione dei diritti

Per impostazione predefinita, i pacchetti di Access sono individuabili. Ciò significa che se un criterio consente a un utente di richiedere il pacchetto di accesso, visualizzerà automaticamente il pacchetto di accesso elencato nel portale di accesso personale. Tuttavia, è possibile modificare l'impostazione **nascosta** in modo che il pacchetto di accesso non sia elencato nel portale di accesso personale dell'utente.

Questo articolo descrive come nascondere o eliminare un pacchetto di accesso.

## <a name="change-the-hidden-setting"></a>Modificare l'impostazione nascosta

Per modificare l'impostazione **nascosta** per un pacchetto di Access, attenersi alla seguente procedura.

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nella pagina Overview fare clic su **Edit**.

1. Impostare l'impostazione **nascosta** .

    Se è impostato su **No**, il pacchetto di accesso verrà elencato nel portale di accesso personale dell'utente.

    Se è impostato su **Sì**, il pacchetto di accesso non verrà elencato nel portale di accesso personale dell'utente. L'unico modo in cui un utente può visualizzare il pacchetto di accesso è il **collegamento al portale** di accesso diretto al pacchetto di accesso. Per ulteriori informazioni, vedere [la pagina relativa alla condivisione del collegamento per richiedere un pacchetto di accesso](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Eliminare un pacchetto di accesso

Un pacchetto di accesso può essere eliminato solo se non sono presenti assegnazioni utente attive. Per eliminare un pacchetto di accesso, seguire questa procedura.

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nel menu a sinistra fare clic su **assegnazioni** e rimuovere l'accesso per tutti gli utenti.

1. Nel menu a sinistra fare clic su **Panoramica** e quindi su **Elimina**.

1. Nel messaggio di eliminazione visualizzato, fare clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso](entitlement-management-access-package-assignments.md)
- [Visualizzare report e log](entitlement-management-reports.md)
