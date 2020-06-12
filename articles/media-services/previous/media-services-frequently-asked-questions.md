---
title: Domande frequenti su Servizi multimediali di Azure
description: Questo articolo contiene le risposte alle domande frequenti su Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 67c7955316d4c9670509affb478813df0768f261
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832555"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Domande frequenti su Servizi multimediali di Azure v2

Questo articolo contiene le domande frequenti sollevate dalla community di utenti di Servizi multimediali di Azure (AMS).

## <a name="general-ams-faqs"></a>Domande frequenti generali su AMS

D: Come si esegue lo streaming su dispositivi Apple iOS?

R: Aggiungere il percorso "(format=m3u8-aapl)" alla parte "/Manifest" dell'URL per indicare al server di origine di streaming di restituire contenuto HLS per l'utilizzo in dispositivi nativi Apple iOS. Per informazioni, vedere [Distribuzione di contenuto](media-services-deliver-content-overview.md).

D: Come è possibile ridimensionare l'indicizzazione?

A: Le unità riservate sono le stesse per le attività di codifica e indicizzazione. Seguire le istruzioni in [Come scalare le unità riservate di codifica](media-services-scale-media-processing-overview.md). **Tenere presente** che le prestazioni dell'indicizzatore non vengono influenzate dal tipo di unità riservata.

D: Ho caricato, codificato e pubblicato un video. Quale può essere il motivo per cui il video non viene riprodotto quando provo a trasmetterlo in streaming?

A: Uno dei motivi più comuni consiste nel fatto che l'endpoint di streaming da cui si sta tentando la riproduzione non si trova nello stato **In esecuzione**.  

D: È possibile eseguire la composizione in uno streaming live?

A: La composizione in streaming live non è attualmente disponibile in Servizi multimediali di Azure ed è quindi necessario eseguire una composizione preliminare sul proprio computer.

D: È possibile usare Rete CDN di Azure con lo streaming live?

A: Servizi multimediali supporta l'integrazione con Rete CDN di Azure. Per altre informazioni, vedere [Gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-portal-manage-streaming-endpoints.md).  È quindi possibile usare Live streaming con la rete CDN. Servizi multimediali di Azure fornisce output in formato Smooth Streaming, HLS e MPEG-DASH. Tutti questi formati usano il protocollo HTTP per trasferire dati e ottenere i vantaggi derivanti dalla cache HTTP. In Live Streaming i dati audio/video effettivi vengono divisi in frammenti, ciascuno dei quali viene memorizzato nella rete CDN. L'aggiornamento è necessario solo per i dati manifesto e viene effettuato periodicamente dalla rete CDN.

D: Servizi multimediali di Azure supporta anche l'archiviazione di immagini?

A: Se si vuole archiviare immagini JPEG o PNG, è consigliabile memorizzarle in Archiviazione BLOB di Azure. Aggiungerle all'account di Servizi multimediali non comporta infatti alcun vantaggio, a meno che non si desideri tenerle associate ai propri asset audio o video o sia necessario usarle come sovrimpressioni nel codificatore video. Media Encoder Standard supporta infatti la sovrimpressione di immagini nella parte superiore dei video ed è per questo che i formati JPEG e PNG sono elencati tra i formati di input supportati. Per altre informazioni, vedere [Creazione di sovrimpressioni](media-services-advanced-encoding-with-mes.md#overlay).

D: Come è possibile copiare gli asset da un account di Servizi multimediali a un altro?

A: Per copiare gli asset da un account di Servizi multimediali a un altro con .NET, usare il metodo di estensione [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponibile nel repository [Azure Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/). Per altre informazioni, vedere [questo](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) thread del forum.

D: Quali sono i caratteri supportati per la denominazione dei file quando si usa AMS?

A: Servizi multimediali usa il valore della proprietà IAssetFile.Name durante la creazione di URL per i contenuti in streaming, ad esempio http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters. Per questo motivo, la codifica percentuale non è consentita. Il valore della proprietà **Name** non può contenere i [caratteri riservati per la codifica percentuale](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) seguenti: !*'();:@&=+$,/?%#[]". L'estensione del nome di file, inoltre, può essere preceduta da un solo punto (.).

D: Come è possibile connettersi usando REST?

A: Per informazioni su come connettersi all'API AMS, vedere [Accedere all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

D: Come è possibile ruotare un video durante il processo di codifica?

A: [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) supporta la rotazione in base ad angoli di 90/180/270. Il comportamento predefinito è "Auto", che tenta di rilevare i metadati di rotazione nel file MP4/MOV in arrivo per la compensazione. Includere l'elemento **Sources** seguente in uno dei set di impostazioni JSON definiti [qui](media-services-mes-presets-overview.md):

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
