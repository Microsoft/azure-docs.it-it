---
title: Codificare un asset mediante Media Encoder Standard sul portale di Azure | Documentazione Microsoft
description: Questa esercitazione descrive le operazioni di codifica di un asset mediante Media Encoder Standard sul portale di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5f4bb3c9b23ffd68939f1088b1252c6e31c1dad7
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010508"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Codificare un asset mediante Media Encoder Standard sul portale di Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Quando si usa Servizi multimediali di Azure, uno degli scenari più frequenti consiste nella distribuzione di contenuti in streaming a velocità in bit adattiva ai client. Servizi multimediali supporta le tecnologie di streaming a bitrate adattivo seguenti: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming e DASH (Dynamic Adaptive Streaming over HTTP, denominato anche MPEG-DASH). Per preparare i video per lo streaming a bitrate adattivo, codificare prima il video di origine in file a più bitrate. È possibile usare Media Encoder Standard per codificare i video.  

Servizi multimediali consente la creazione dinamica dei pacchetti. Con creazione dinamica dei pacchetti, è possibile recapitare MP4s a più bitrate in HLS, Smooth Streaming e MPEG-DASH, senza riassemblaggio in tali formati di streaming. Quando si usa la creazione dinamica dei pacchetti consente di archiviare e pagare i file in un solo formato di archiviazione. Servizi multimediali creerà e fornirà la risposta appropriata in base alle richieste del client.

Per sfruttare i vantaggi della creazione dinamica dei pacchetti è necessario codificare il file di origine in un set di file MP4 a bitrate multipli. I passaggi della codifica vengono illustrati più avanti in questo articolo.

Per informazioni su come ridimensionare l'elaborazione di contenuti multimediali, vedere [Ridimensionare l'elaborazione di contenuti multimediali tramite il portale di Azure](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Codifica nel portale di Azure

Per codificare il contenuto tramite Media Encoder Standard:

1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali di Azure.
2. Selezionare **Impostazioni** > **Asset**. Selezionare l'asset da codificare.
3. Selezionare il pulsante **Codifica**.
4. Nel riquadro **Codifica un asset** selezionare il processore **Media Encoder Standard** e un set di impostazioni. Per informazioni sui set di impostazioni, vedere [Generare automaticamente una tabella di coppie bitrate-risoluzione](media-services-autogen-bitrate-ladder-with-mes.md) e [Set di impostazioni delle attività MES (Media Encoder Standard)](media-services-mes-presets-overview.md). È importante scegliere il set di impostazioni più idoneo per i video di input. Se ad esempio è noto che il video di input ha una risoluzione di 1920 × 1080 pixel, è possibile scegliere il set di impostazioni **Codec video H.264 a bitrate multiplo con risoluzione 1080p**. Se il video disponibile è a bassa risoluzione (640 × 360), non usare il set di impostazioni **Codec video H.264 a bitrate multiplo con risoluzione 1080p**.
   
   Per una gestione più semplice delle risorse è possibile modificare il nome dell'asset di output e il nome del processo.
   
   ![Codificare gli asset](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Selezionare **Crea**.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Monitorare lo stato di avanzamento del processo di codifica](media-services-portal-check-job-progress.md) nel portale di Azure.  

