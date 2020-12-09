---
title: Esempio di PowerShell - Visualizzare le informazioni estese per le app di Application Proxy
description: Esempio di PowerShell che elenca tutte le applicazioni di Azure Active Directory (Azure AD) Application Proxy unitamente all'ID applicazione (AppId), al nome (DisplayName), all'URL esterno (ExternalUrl), all'URL interno (InternalUrl) e al tipo di autenticazione (ExternalAuthenticationType).
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 330bd9b78c2f550ab40f1f4f3679b6c9788ddb64
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96859372"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Recuperare tutte le app di Application Proxy e visualizzare le informazioni estese

Questo script di esempio di PowerShell visualizza informazioni su tutte le applicazioni di Azure Active Directory (Azure AD) Application Proxy, inclusi l'ID applicazione (AppId), il nome (DisplayName), l'URL esterno (ExternalUrl), l'URL interno (InternalUrl) e il tipo di autenticazione (ExternalAuthenticationType).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Per questo esempio è richiesto il [modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) o la [versione di anteprima del modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Recupera un'entità servizio. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Recupera un'applicazione Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Recupera un'applicazione configurata per Application Proxy in Azure AD. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere la [panoramica del modulo di Azure AD PowerShell](/powershell/azure/active-directory/overview).

Per altri esempi di PowerShell relativi ad Application Proxy, vedere [Esempi di Azure AD PowerShell per Azure AD Application Proxy](../application-proxy-powershell-samples.md).
