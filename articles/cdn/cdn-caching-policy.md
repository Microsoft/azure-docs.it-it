---
title: Gestire i criteri di memorizzazione nella cache della rete CDN di Azure in Servizi multimediali di Azure | Documentazione Microsoft
description: Questo articolo illustra come gestire i criteri di memorizzazione nella cache della rete CDN di Azure in servizi multimediali di Azure.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 5807d38e22d8cecf40b5ad4262f9e4662b77ec4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779136"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Gestire i criteri di memorizzazione nella cache della rete CDN di Azure in Servizi multimediali di Azure
Servizi multimediali di Azure fornisce lo streaming adattivo e il download progressivo basati su HTTP. Lo streaming basato su HTTP è altamente scalabile con i vantaggi della cache nei livelli proxy e di rete CDN, nonché della cache sul lato client. Gli endpoint di streaming forniscono funzionalità di streaming generale e configurazione per le intestazioni di cache HTTP. Gli endpoint di streaming impostano le intestazioni HTTP Cache-Control: max-age ed Expires. È possibile ottenere ulteriori informazioni per le intestazioni della cache HTTP da [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Intestazioni di memorizzazione nella cache predefinite
Per impostazione predefinita, gli endpoint di streaming applicano intestazioni di cache di 3 giorni per i dati di streaming on demand (frammenti multimediali effettivi/blocchi) e manifest(playlist). Per lo streaming live, gli endpoint di streaming applicano intestazioni di cache di 3 giorni per i dati (frammenti multimediali effettivi/blocchi) e intestazioni di cache di 2 secondi per le richieste manifest(playlist). Quando il programma live (archivio live) diventa on demand vengono applicate le intestazioni della cache di streaming on demand.

## <a name="azure-cdn-integration"></a>Integrazione della rete CDN di Azure
Servizi multimediali di Azure fornisce [la rete CDN integrata](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) per gli endpoint di streaming. Le intestazioni cache-control si applicano nello stesso modo in cui gli endpoint di streaming si applicano agli endpoint di streaming abilitati dalla rete CDN. La rete CDN di Azure utilizza i valori di cache configurati degli endpoint di streaming per definire la durata degli oggetti memorizzati nella cache internamente e inoltre utilizza questo valore per impostare le intestazioni della cache per i recapiti. Quando si utilizzano gli endpoint di streaming abilitati alla rete CDN non è consigliabile impostare i valori della cache piccoli. In tal modo, si verifica una riduzione delle prestazioni e del vantaggio della rete CDN. Non è consentito impostare intestazioni cache inferiori a 600 secondi per gli endpoint di streaming abilitati alla rete CDN.

> [!IMPORTANT]
>Servizi multimediali di Azure assicura una perfetta integrazione con la rete CDN di Azure. Con un unico clic è possibile integrare tutti i provider di rete CDN di Azure disponibili all'endpoint di streaming che include i prodotti Standard e Premium. Per altre informazioni, vedere questo [annuncio](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Gli addebiti per i dati dall'endpoint di streaming alla rete CDN vengono disabilitati solo se la rete CDN è abilitata tramite le API dell'endpoint di streaming o usando la sezione relativa agli endpoint di streaming del portale di Azure. Gli addebiti per i dati non vengono disabilitati in caso di integrazione manuale o creazione diretta di un endpoint di rete CDN usando le API della rete CDN o la sezione del portale.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configurazione delle intestazioni della cache con Servizi multimediali di Azure
È possibile utilizzare il portale di Azure o le API di Servizi multimediali di Azure per configurare i valori di intestazione della cache.

1. Per configurare le intestazioni delle cache mediante il portale di Azure, vedere la sezione [Come gestire gli endpoint di streaming](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) Configurazione dell'endpoint di streaming.
2. API REST di Servizi multimediali di Azure, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. SDK .NET di Servizi multimediali di Azure, [Proprietà StreamingEndpointCacheControl](/dotnet/api/microsoft.windowsazure.mediaservices.client.streamingendpointcachecontrol).

## <a name="cache-configuration-precedence-order"></a>Ordine di precedenza di configurazione della cache
1. Il valore della cache configurato di Servizi multimediali di Azure sostituisce il valore predefinito.
2. Se non è presente alcuna configurazione manuale, vengono applicati i valori predefiniti.
3. Per impostazione predefinita si applicano le intestazioni di cache di 2 secondi, a prescindere dalla configurazione di servizi multimediali di Azure o archiviazione di Azure, e l'override di questo valore non è disponibile.