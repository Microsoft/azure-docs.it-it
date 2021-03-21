---
title: Content Moderator la crittografia dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Content Moderator la crittografia dei dati inattivi.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 1401108a594e30790e842ec379724603f11d493f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524478"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator la crittografia dei dati inattivi

Content Moderator crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud, contribuendo a soddisfare gli obiettivi di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono disponibili solo nel piano tariffario di E0. Per richiedere la possibilità di usare chiavi gestite dal cliente, compilare e inviare il [modulo di richiesta di Content Moderator Customer-Managed chiave](https://aka.ms/cogsvc-cmk). Saranno richiesti circa 3-5 giorni lavorativi per ricevere informazioni sullo stato della richiesta. A seconda della richiesta, è possibile che venga inserita in una coda e approvata quando lo spazio diventa disponibile. Una volta approvate per l'uso di CMK con il servizio Content Moderator, sarà necessario creare una nuova risorsa Content Moderator e selezionare E0 come piano tariffario. Una volta creata la risorsa di Content Moderator con il piano tariffario E0, è possibile usare Azure Key Vault per configurare l'identità gestita.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Abilitare la crittografia dei dati per il team di Content Moderator

Per abilitare la crittografia dei dati per il team di Content Moderator Review, vedere la [Guida introduttiva: provare content moderator sul Web](quick-start.md#create-a-review-team).  

> [!NOTE]
> È necessario specificare un _ID risorsa_ con il piano tariffario content moderator E0.

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco completo dei servizi che supportano CMK, vedere [chiavi gestite dal cliente per servizi cognitivi](../encryption/cognitive-services-encryption-keys-portal.md)
* [Che cos'è Azure Key Vault](../../key-vault/general/overview.md)?
* [Modulo di richiesta chiave Customer-Managed di servizi cognitivi](https://aka.ms/cogsvc-cmk)