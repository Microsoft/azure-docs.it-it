---
title: Panoramica della creazione dinamica dei pacchetti di Servizi multimediali di Azure | Documentazione Microsoft
description: Questo articolo fornisce una panoramica della creazione dinamica dei pacchetti Servizi multimediali di Microsoft Azure.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d2394cba157a7049e3b63b09fda9e018f0a0b32f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013619"
---
# <a name="dynamic-packaging"></a>creazione dinamica dei pacchetti

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selezionare la versione di servizi multimediali in uso:"]
> * [Versione 3](../latest/dynamic-packaging-overview.md)
> * [Versione 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](../latest/index.yml). Vedere anche le [indicazioni sulla migrazione dalla versione 2 alla versione 3](../latest/migrate-v-2-v-3-migration-introduction.md)

Servizi multimediali di Microsoft Azure può essere usato per distribuire molti formati di file di origine multimediali, formati di streaming multimediali e formati di protezione del contenuto in un'ampia gamma di tecnologie client, ad esempio iOS, Xbox, Silverlight e Windows 8. Questi client supportano tuttavia protocolli diversi. iOS, ad esempio, richiede un formato HTTP Live Streaming (HLS) V4, mentre Silverlight e Xbox richiedono Smooth Streaming. Se è presente un set di file MP4 a velocità in bit adattiva, ovvero più velocità in bit, (ISO Base Media 14496-12) o di un set di file Smooth Streaming a velocità in bit adattiva e si vuole renderli disponibili per i client che supportano contenuto MPEG DASH, HLS o Smooth Streaming, è possibile usare la funzionalità di creazione dinamica dei pacchetti di Servizi multimediali.

Con la creazione dinamica dei pacchetti, è necessario creare un asset che contenga un set di file MP4 a velocità in bit adattiva o file Smooth Streaming a bitrate adattivo. In base al formato specificato nella richiesta del manifesto o del frammento, il server di streaming on demand garantirà che il flusso sia ricevuto nel protocollo scelto. Di conseguenza, si archiviano e si pagano solo i file in un singolo formato di archiviazione e il servizio Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

Il diagramma seguente mostra il flusso di lavoro tradizionale di codifica e creazione statica dei pacchetti.

![Codifica statica](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Il diagramma seguente mostra il flusso di lavoro di creazione dinamica dei pacchetti.

![Codifica dinamica](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Scenario comune

1. Caricare un file di input (detto file in formato intermedio). Ad esempio, H.264, MP4 o WMV (per l'elenco dei formati supportati, vedere [Formati e codec Media Encoder Standard](media-services-media-encoder-standard-formats.md)).
2. Codificare il file in formato intermedio in set MP4 a velocità in bit adattiva H.264.
3. Pubblicare l'asset contenente il set MP4 a velocità in bit adattiva creando il localizzatore su richiesta.
4. Creare gli URL di streaming per accedere e al contenuto e trasmetterlo in streaming.

## <a name="preparing-assets-for-dynamic-streaming"></a>Preparazione di asset per lo streaming dinamico

Per preparare l'asset per lo streaming dinamico, sono disponibili le opzioni seguenti:

- [Caricare un file master](media-services-dotnet-upload-files.md).
- [Usare il codificatore Media Encoder Standard per generare set MP4 velocità in bit adattiva H.264](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Trasmettere i contenuti in streaming](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codec audio supportati dalla creazione dinamica dei pacchetti

La creazione dinamica dei pacchetti supporta i file MP4, che contengono audio codificati con [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC V1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 o E-AC3), Dolby Atmos o [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD lossless). Lo streaming del contenuto Dolby Atmos è supportato per standard come il protocollo MPEG-DASH con il formato di flusso comune (CSF) o il formato MP4 frammentato CMAF (Common Media Application Format) e tramite HTTP Live Streaming (HLS) con CMAF.

> [!NOTE]
> La creazione dinamica dei pacchetti non supporta file contenenti audio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) perché si tratta di un codec legacy codec.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
