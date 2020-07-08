---
title: Documenti del ruolo di amministratore tra Microsoft 365 Services-Azure AD | Microsoft Docs
description: Trovare i riferimenti alle API e al contenuto per i ruoli di amministratore per i servizi Microsoft 365 in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79e3ed41ad3038cf66d61c25292ebad55c2b5e92
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731698"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Ruoli di amministratore per i servizi Microsoft 365

Tutti i prodotti di Microsoft 365 possono essere gestiti con i ruoli amministrativi in Azure AD. Alcuni prodotti forniscono anche ulteriori ruoli specifici per tale prodotto. Per informazioni sui ruoli supportati da ogni prodotto, consultare la tabella seguente. Discussioni generali sui problemi di delega sono reperibili in [Pianificazione della delega del ruolo in Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Dove trovare il contenuto

Servizio Microsoft 365 | Contenuto del ruolo | Contenuto dell'API
---------------------- | ------------------ | -----------------
Ruoli di amministratore in Office 365 e piani aziendali di Microsoft 365 | [Ruoli di amministrazione di Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Non disponibile
Azure Active Directory (Azure AD) e Azure AD Identity Protection| [Ruoli di amministratore di Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Recuperare le assegnazioni di ruolo](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Controllo degli accessi in base al ruolo di Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell per Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Recuperare le assegnazioni di ruolo](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Ruoli di amministratore di Azure AD](directory-assign-admin-roles.md)<br>Anche [About the SharePoint admin role in Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) (Informazioni sul ruolo di amministratore di SharePoint in Office 365) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Recuperare le assegnazioni di ruolo](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Teams/Skype for Business | [Ruoli di amministratore di Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Recuperare le assegnazioni di ruolo](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Centro sicurezza e conformità (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Ruoli di amministrazione di Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [PowerShell per Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Recuperare le assegnazioni di ruolo](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Punteggio di sicurezza | [Ruoli di amministratore di Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Recuperare le assegnazioni di ruolo](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager | [Ruoli di Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Non disponibile
Azure Information Protection | [Ruoli di amministratore di Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Recuperare le assegnazioni di ruolo](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Informazioni di riferimento sulle API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Gruppi di ruoli di Azure ATP](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Non disponibile
Windows Defender Advanced Threat Protection | [Controllo degli accessi in base al ruolo di Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Non disponibile
Privileged Identity Management | [Ruoli di amministratore di Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Recuperare le assegnazioni di ruolo](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Controllo degli accessi in base al ruolo di Intune](https://docs.microsoft.com/intune/role-based-access-control) | [API Graph](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Recuperare le assegnazioni di ruolo](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Managed Desktop | [Ruoli di amministratore di Azure AD](directory-assign-admin-roles.md) | [API Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Recuperare le assegnazioni di ruolo](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Passaggi successivi

* [Come assegnare o rimuovere ruoli di amministratore di Azure AD](directory-manage-roles-portal.md)
* [Riferimento dei ruoli di amministratore di Azure AD](directory-assign-admin-roles.md)
