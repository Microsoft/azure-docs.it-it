---
title: API Microsoft Graph per PIM (anteprima) - Azure AD | Microsoft Docs
description: Fornisce informazioni sull'uso delle API Microsoft Graph per Azure AD Privileged Identity Management (PIM, anteprima).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6da6bffbc54bfa6e9c39ddace665eb7cfec58614
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75638664"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>API Microsoft Graph per Privileged Identity Management (anteprima)

È possibile eseguire tutte le attività di Privileged Identity Management con le [API Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/overview) per Azure Active Directory. Questo articolo descrive alcuni concetti importanti per l'uso delle API Microsoft Graph per Privileged Identity Management.

Per informazioni dettagliate sulle API Microsoft Graph, esaminare le [informazioni di riferimento sulle API di Azure Active Directory Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> Le API nella versione beta in Microsoft Graph sono disponibili in anteprima e sono soggetti a modifiche. L'uso di queste API nelle applicazioni di produzione non è supportato.
>
> Nonostante nei comandi dell'API Graph siano supportate tutte le operazioni PIM, per approvare le richieste di attivazione si usa un sistema diverso. L'API Graph per l'approvazione è attualmente in fase di sviluppo e verrà rilasciata nei prossimi mesi.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per chiamare le API Microsoft Graph per Privileged Identity Management, è necessario avere **una o più** delle autorizzazioni seguenti:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Impostare autorizzazioni

Per poter chiamare le API Microsoft Graph per Privileged Identity Management, le applicazioni devono disporre delle autorizzazioni necessarie. Il modo più semplice per specificare le autorizzazioni richieste consiste nell'usare il [framework di consenso di Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Impostare le autorizzazioni in Graph Explorer

Se si usa Graph Explorer per testare le chiamate, è possibile specificare le autorizzazioni nello strumento.

1. Accedere a [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) come amministratore globale.

1. Fare clic su **Autorizzazioni di modifica**.

    ![Graph Explorer - Autorizzazioni di modifica](./media/pim-apis/graph-explorer.png)

1. Selezionare le caselle di controllo accanto alle autorizzazioni che si vogliono includere. `PrivilegedAccess.ReadWrite.AzureAD` non è ancora disponibile in Graph Explorer.

    ![Graph Explorer - Autorizzazioni di modifica](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Fare clic su **Autorizzazioni di modifica** per applicare le modifiche alle autorizzazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni di riferimento sulle API di Azure Active Directory Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
