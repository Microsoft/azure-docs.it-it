---
title: includere file
description: includere file
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 016ad0e11f3378dba887e0a235f235fa91e3aa03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98109440"
---
### <a name="property-limits"></a>Limiti delle proprietà

Azure Time Series Insights limiti delle proprietà sono aumentati a 1.000 per l'archiviazione a caldo e nessun limite di proprietà per l'archiviazione a freddo. Le proprietà dell'evento fornite presentano colonne JSON, CSV e del grafico corrispondenti che è possibile visualizzare all'interno di [Azure Time Series Insights Gen2 Explorer](../articles/time-series-insights/quickstart-explore-tsi.md).

| SKU | Proprietà massime |
| --- | --- |
| Gen2 (L1) | 1.000 proprietà (colonne) per archiviazione a caldo e senza limiti per archiviazione a freddo|
| Gen1 (S1) | Proprietà 600 (colonne) |
| Gen1 (S2) | Proprietà 800 (colonne) |

### <a name="streaming-ingestion"></a>Inserimento streaming

* Sono disponibili un massimo di due [origini evento](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) per ogni ambiente.

* Le procedure consigliate e le linee guida generali per le origini eventi sono disponibili [qui](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Per impostazione predefinita, Azure Time Series Insights Gen2 è in grado di inserire i dati in ingresso a una velocità di un **massimo di 1 megabyte al secondo (Mbps) per Azure Time Series Insights ambiente Gen2**. [Per ogni partizione dell'hub](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits) ci sono altre limitazioni. È possibile fornire frequenze fino a 2 MBps inviando un ticket di supporto tramite il portale di Azure. Per altre informazioni, vedere [limiti di velocità effettiva](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)di inserimento di flussi.

### <a name="api-limits"></a>Limiti API

I limiti dell'API REST per Azure Time Series Insights Gen2 sono specificati nella [documentazione di riferimento dell'API REST](/rest/api/time-series-insights/preview#limits-1).