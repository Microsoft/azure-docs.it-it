---
title: Codec e formati del flusso di lavoro Premium del codificatore multimediale | Microsoft Docs
description: Questo argomento offre una panoramica dei codec e dei formati del flusso di lavoro Premium del codificatore multimediale
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 87cd7c63939331190530a46071a6b4c40480562f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79269757"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Codec e formati del flusso di lavoro Premium del codificatore multimediale

> [!NOTE]
> Il processore di contenuti multimediali del flusso di lavoro Premium del codificatore multimediale descritto in questo argomento non è disponibile in Cina. 

Questo documento include un elenco dei codec e dei formati di file di input e output supportati nella versione di anteprima pubblica del codificatore per il **flusso di lavoro Premium del codificatore multimediale**.

[Formati e codec di input Media Encoder Premium Workflow](#input_formats)

Codec e formati di output di Media Encoder Premium Workflow

**Flusso di lavoro Premium del codificatore multimediale** supporta i sottotitoli codificati descritti in [questa](#closed_captioning) sezione. 

## <a name="media-encoder-premium-workflow-input-formats-and-codecs"></a><a id="input_formats"></a>Codec e formati di input del flusso di lavoro Premium del codificatore multimediale

La seguente sezione include l'elenco dei codec e dei formati di file supportati come input da questo processore di contenuti multimediali.

### <a name="input-containerfile-formats"></a>Contenitore di input/formati di file

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* MPEG-2 Transport Stream
* MPEG-2 Program Stream
* MPEG-4/MP4
* Windows Media/ASF
* AVI (non compresso 8 bit/10 bit)

### <a name="input-video-codecs"></a>Codec video di input

* AVC 8 bit/10 bit, fino a 4:2:2, incluso AVCIntra
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* HEVC/H.265, Main Profile e Main 10 Profile
* JPEG2000
* MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Codec audio di input

* AES (SMPTE 331M e 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio
* WAV/PCM

## <a name="media-encoder-premium-workflow-output-formats-and-codecs"></a><a id="output_format"></a>Codec e formati di output del flusso di lavoro Premium del codificatore multimediale

La seguente sezione include l'elenco dei codec e dei formati di file supportati come output da questo processore di contenuti multimediali.

### <a name="output-containerfile-formats"></a>Contenitore di output/formati di file

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM e AS02)
* DPP (incluso AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (non compresso 8 bit/10 bit)
* Formato di file Smooth Streaming (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Codec video di output

* AVC (H.264; 8 bit; fino a High Profile, Level 5.2; 4K Ultra HD; AVC Intra)
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
* MPEG-1
* Windows Media Video/VC-1
* Creazione anteprime JPEG
* HEVC (H.265; 8 bit e 10 bit, Main e Main 10 Profile)


### <a name="output-audio-codecs"></a>Codec audio di output

* AES (SMPTE 331M e 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) fino a 7.1
* AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio

>[!NOTE]
>Se si codifica per Dolby® Digital (AC3), l'output può essere scritto solo in un file ISO MP4.

## <a name="support-for-closed-captioning"></a><a id="closed_captioning"></a>Supporto per sottotitoli codificati

In ingresso, il **flusso di lavoro Premium del codificatore multimediale** supporta:

1. File SCC
2. File SMPTE-TT
3. CEA-608/CEA-708 – Trasportato come dati (messaggi SEI di flussi elementari H.264, ATSC/53, SCTE20) o trasportato come dati ausiliari in file MXF/GXF
4. File con sottotitoli STL

In uscita, sono disponibili le seguenti opzioni:

1. Conversione da CEA-608 a CEA-708
2. Pass-through di CEA-608/CEA-708 (incorporato in messaggi SEI di flussi elementari H.264 o trasportato come dati ausiliari in file MXF)
3. SCC
4. SMPTE Timed Text (da origine CEA-608 per SMPTE RP2052; inclusa la creazione di file DFXP)
5. File di sottotitoli SRT
6. Flussi di sottotitoli DVB

> [!NOTE]
> Non tutti i formati di output precedenti sono supportati per il recapito tramite streaming in Servizi multimediali di Azure.

## <a name="known-issues"></a>Problemi noti

Se il video di input non contiene i sottotitoli codificati, l'asset di output conterrà comunque un file TTML vuoto. 

## <a name="need-help"></a>Serve assistenza?

È possibile aprire un ticket di supporto passando alla [nuova richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Inviare feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

