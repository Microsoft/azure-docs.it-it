---
title: Documenti del ruolo di amministratore tra Microsoft 365 Services-Azure AD | Microsoft Docs
description: Trovare i riferimenti alle API e al contenuto per i ruoli di amministratore per i servizi Microsoft 365 in Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae5761c4a31662f447a107e6b5eb89bcf02f70c8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055536"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Ruoli per i servizi Microsoft 365 in Azure Active Directory

Tutti i prodotti in Microsoft 365 possono essere gestiti con ruoli amministrativi in Azure Active Directory (Azure AD). Alcuni prodotti forniscono anche ulteriori ruoli specifici per tale prodotto. Per informazioni sui ruoli supportati da ogni prodotto, consultare la tabella seguente. Per le linee guida sulla pianificazione della sicurezza dei ruoli, vedere [protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure ad](security-planning.md).

## <a name="where-to-find-content"></a>Dove trovare il contenuto

Servizio Microsoft 365 | Contenuto del ruolo | Contenuto dell'API
---------------------- | ------------------ | -----------------
Ruoli di amministratore in Office 365 e piani aziendali di Microsoft 365 | [Ruoli di amministratore Microsoft 365](/office365/admin/add-users/about-admin-roles) | Non disponibile
Azure Active Directory (Azure AD) e Azure AD Identity Protection| [Ruoli di amministratore di Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Recuperare le assegnazioni di ruolo](/graph/api/directoryrole-list)
Exchange Online| [Controllo degli accessi in base al ruolo di Exchange](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell per Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Recuperare le assegnazioni di ruolo](/powershell/module/exchange/role-based-access-control/get-rolegroup)
SharePoint Online | [Ruoli di amministratore di Azure AD](permissions-reference.md)<br>Inoltre, [informazioni sul ruolo di amministratore di SharePoint in Microsoft 365](/sharepoint/sharepoint-admin-role) | [API Graph](/graph/api/overview)<br>[Recuperare le assegnazioni di ruolo](/graph/api/directoryrole-list)
Teams/Skype for Business | [Ruoli di amministratore di Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Recuperare le assegnazioni di ruolo](/graph/api/directoryrole-list)
Centro sicurezza e conformità (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Ruoli di amministrazione di Office 365](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [PowerShell per Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Recuperare le assegnazioni di ruolo](/powershell/module/exchange/role-based-access-control/get-rolegroup)
Punteggio di sicurezza | [Ruoli di amministratore di Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Recuperare le assegnazioni di ruolo](/graph/api/directoryrole-list)
Compliance Manager | [Ruoli di Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Non disponibile
Azure Information Protection | [Ruoli di amministratore di Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Recuperare le assegnazioni di ruolo](/graph/api/directoryrole-list)
Microsoft Cloud App Security | [Controllo degli accessi in base al ruolo](/cloud-app-security/manage-admins) | [Informazioni di riferimento sulle API](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Gruppi di ruoli di Azure ATP](/azure-advanced-threat-protection/atp-role-groups) | Non disponibile
Windows Defender Advanced Threat Protection | [Controllo degli accessi in base al ruolo di Windows Defender ATP](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Non disponibile
Privileged Identity Management | [Ruoli di amministratore di Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Recuperare le assegnazioni di ruolo](/graph/api/directoryrole-list)
Intune | [Controllo degli accessi in base al ruolo di Intune](/intune/role-based-access-control) | [API Graph](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[Recuperare le assegnazioni di ruolo](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
Managed Desktop | [Ruoli di amministratore di Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Recuperare le assegnazioni di ruolo](/graph/api/directoryrole-list)

## <a name="next-steps"></a>Passaggi successivi

* [Come assegnare o rimuovere ruoli di amministratore di Azure AD](manage-roles-portal.md)
* [Riferimento dei ruoli di amministratore di Azure AD](permissions-reference.md)