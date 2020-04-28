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
ms.openlocfilehash: 6bcaadc8dd61899aff860ad246e30170c99ec0f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "61463753"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Gestire la velocità e la concorrenza della codifica  

Questo articolo illustra brevemente la procedura per gestire la velocità e la concorrenza delle attività o dei processi di codifica.

## <a name="overview"></a>Panoramica

Nei Servizi multimediali, un **tipo di unità riservata** determina la velocità di elaborazione delle attività multimediali. È possibile scegliere uno dei seguenti tipi di unità riservata: **S1**, **S2** o **S3**. Lo stesso processo di codifica viene eseguito più velocemente quando si usa ad esempio il tipo di unità riservata **S2** rispetto al tipo **S1**. L'argomento sulle [unità di codifica della scalabilità](media-services-scale-media-processing-overview.md) mostra una tabella che consente di scegliere tra diverse velocità di codifica.

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con le **unità riservate**. Il numero delle unità riservate sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione quando un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

Per informazioni dettagliate ed esempi che illustrano come ridimensionare le unità di codifica, vedere [questo](media-services-scale-media-processing-overview.md) argomento.

## <a name="next-step"></a>Passaggio successivo

[Ridimensionare le unità di codifica](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Inviare feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

