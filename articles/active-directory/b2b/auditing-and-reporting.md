---
title: Controllo e creazione di report per un utente di Collaborazione B2B - Azure AD
description: Le proprietà di un utente guest sono configurabili in Collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2f1478391eccaabcc4dbcd150b54376494d3f56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587528"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Controllo e creazione di report per un utente di Collaborazione B2B
Con gli utenti guest, sono disponibili funzionalità di controllo analoghe a quelle degli utenti membri. 

## <a name="access-reviews"></a>Verifiche di accesso
È possibile usare le verifiche di accesso per verificare periodicamente se gli utenti guest hanno ancora necessità di accedere alle risorse. La funzionalità **Verifiche di accesso** è disponibile in **Azure Active Directory** in **Identità esterne** > **Verifiche di accesso**. È anche possibile cercare le "verifiche di accesso" da **Tutti i servizi** nel portale di Azure. Per informazioni su come usare le verifiche di accesso, vedere [Gestire l'accesso guest con le verifiche di accesso di Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Log di controllo

I log di controllo di Azure AD forniscono i record delle attività di sistema e utente, incluse le attività avviate da parte degli utenti guest. Per accedere ai log di controllo in **Azure Active Directory**, in **Monitoraggio**, selezionare **Log di controllo**. Di seguito è riportato un esempio di cronologia di inviti e riscatti dell'invitato Sam Oogle:

![Screenshot che mostra un esempio di output del log di controllo](./media/auditing-and-reporting/audit-log.png)

È possibile selezionare ogni evento per visualizzare i dettagli. È ad esempio possibile esaminare i dettagli relativi all'accettazione.

![Screenshot che mostra un esempio di output dei dettagli dell'attività](./media/auditing-and-reporting/activity-details.png)

È anche possibile esportare i log da Azure AD e usare lo strumento di creazione di report per ottenere report personalizzati.

### <a name="next-steps"></a>Passaggi successivi

- [Proprietà dell'utente di Collaborazione B2B](user-properties.md)

