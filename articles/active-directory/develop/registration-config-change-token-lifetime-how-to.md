---
title: Modificare le impostazioni predefinite della durata dei token per le app personalizzate di Azure AD. Documenti Microsoft
description: Informazioni su come aggiornare i criteri di durata del token per l'applicazione che si sta sviluppando in Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 431f18b9babb52b5000d3bf4cca75a0f5e29bb93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702811"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Come modificare le impostazioni predefinite per la durata del token per un'applicazione personalizzata

Questo articolo illustra come usare Azure AD PowerShell per impostare criteri di durata dei token. Azure AD Premium consente agli sviluppatori di app e agli amministratori di tenant di configurare la durata dei token emessi per i client non riservati. I criteri di durata dei token vengono impostati a livello di tenant o per le risorse a cui si accede.

1. Per impostare i criteri di durata del token, è necessario scaricare il [modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Eseguire il comando **Connect-AzureAD -Confirm**.

    Un esempio di criteri che impostano il token di aggiornamento di un singolo fattore di età massima è riportato di seguito. Creare i criteri: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Durate dei token configurabile in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) per informazioni su come configurare la durata dei token emessi da Azure AD, tra cui come impostare la durata dei token per tutte le app di un'organizzazione, per un'applicazione multi-tenant o per un'entità servizio specifica in un'organizzazione. 
* [Azure AD Token Reference](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
