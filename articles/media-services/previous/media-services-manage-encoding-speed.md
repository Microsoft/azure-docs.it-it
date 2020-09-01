---
title: Gestire la velocità e la concorrenza della codifica con Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra brevemente la procedura per gestire la velocità e la concorrenza delle attività o dei processi di codifica con Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 4b6f843678d64bddd276f6123a432699efc89ad9
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269285"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Gestire la velocità e la concorrenza della codifica

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Questo articolo illustra brevemente la procedura per gestire la velocità e la concorrenza delle attività o dei processi di codifica.

## <a name="overview"></a>Panoramica

Nei Servizi multimediali, un **tipo di unità riservata** determina la velocità di elaborazione delle attività multimediali. È possibile scegliere uno dei seguenti tipi di unità riservata: **S1**, **S2** o **S3**. Lo stesso processo di codifica viene eseguito più velocemente quando si usa ad esempio il tipo di unità riservata **S2** rispetto al tipo **S1**. L'argomento sulle [unità di codifica della scalabilità](media-services-scale-media-processing-overview.md) mostra una tabella che consente di scegliere tra diverse velocità di codifica.

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con le **unità riservate**. Il numero delle unità riservate sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione quando un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

Per informazioni dettagliate ed esempi che illustrano come ridimensionare le unità di codifica, vedere [questo](media-services-scale-media-processing-overview.md) argomento.

## <a name="next-step"></a>Passaggio successivo

[Ridimensionare le unità di codifica](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

