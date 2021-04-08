---
title: 'Azure AD Connect: Funzionalità in anteprima | Microsoft Docs'
description: Questo argomento descrive in maggiore dettaglio le funzionalità in anteprima di Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d48cd8f95ecaf3e537a5221d766af150a51aa31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517685"
---
# <a name="more-details-about-features-in-preview"></a>Altre informazioni sulle funzionalità in anteprima
Questo argomento descrive come usare le funzionalità attualmente in anteprima.

## <a name="azure-ad-connect-sync-v2-endpoint-api"></a>API dell'endpoint Servizio di sincronizzazione Azure AD Connect v2

È stato distribuito un nuovo endpoint (API) per Azure AD Connect che consente di migliorare le prestazioni delle operazioni del servizio di sincronizzazione per Azure Active Directory. Usando il nuovo endpoint V2, si verificherà un notevole miglioramento delle prestazioni di esportazione e importazione in Azure AD. Questo nuovo endpoint supporta anche i gruppi di sincronizzazione con fino a 250.000 membri. L'uso di questo endpoint consente inoltre di eseguire il writeback Microsoft 365 gruppi unificati, senza limite di appartenenza massimo, al Active Directory locale quando il writeback del gruppo è abilitato. Per ulteriori informazioni, vedere [Azure ad Connect API endpoint Sync V2](how-to-connect-sync-endpoint-api-v2.md).

## <a name="user-writeback"></a>Writeback degli utenti
> [!IMPORTANT]
> La funzionalità di anteprima di writeback degli utenti è stata rimossa nell'aggiornamento di agosto 2015 di Azure AD Connect. Se questa funzionalità è stata abilitata, è necessario disabilitarla.
>
>

## <a name="next-steps"></a>Passaggi successivi
Continuare l'[Installazione personalizzata di Azure AD Connect](how-to-connect-install-custom.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
