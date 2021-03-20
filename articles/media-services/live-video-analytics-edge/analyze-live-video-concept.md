---
title: Analisi di video live senza alcuna registrazione-Azure
description: È possibile usare un grafico multimediale per estrarre solo le analisi da un flusso video live, senza doverle registrare sul perimetro o nel cloud. In questo articolo viene illustrato questo concetto.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 25a7cadc47603b726542fa391d441e1fbca78908
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97398975"
---
# <a name="analyzing-live-video-without-any-recording"></a>Analisi di video live senza registrazione

## <a name="suggested-pre-reading"></a>Letture consigliate 

* [Concetti relativi al grafo multimediale](media-graph-concept.md)
* [Registrazione di video basata su eventi](event-based-video-recording-concept.md)

## <a name="overview"></a>Panoramica  

È possibile usare un grafico multimediale per analizzare video in tempo reale, senza registrare parti del video in un file o in un asset. I grafici dei supporti mostrati di seguito sono simili a quelli nell'articolo sulla [registrazione video basata su eventi](event-based-video-recording-concept.md), ma senza un nodo sink di asset o un nodo di sink di file.

### <a name="motion-detection"></a>Rilevamento movimento

Il grafico multimediale riportato di seguito è costituito da un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) , da un nodo del [processore di rilevamento del movimento](media-graph-concept.md#motion-detection-processor) e da un nodo del sink di messaggi dell' [Hub](media-graph-concept.md#iot-hub-message-sink) Internet. La rappresentazione JSON della topologia Graph di tale grafico multimediale è disponibile [qui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json). Questo grafico consente di rilevare il movimento nel flusso video live in ingresso e di inoltrare gli eventi di movimento ad altre app e servizi tramite il nodo sink di messaggi dell'hub Internet. Le app o i servizi esterni possono attivare un avviso o inviare una notifica al personale appropriato.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Analisi di video live basata su un rilevamento del movimento":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>Analisi di video con un modello di visione artificiale personalizzato 

Il grafico multimediale riportato di seguito consente di analizzare un flusso video live usando un modello di visione artificiale personalizzato incluso in un modulo separato. La rappresentazione JSON della topologia Graph di tale grafico multimediale è disponibile [qui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json). È possibile vedere alcuni esempi di wrapping di [modelli in IOT Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) moduli che vengono eseguiti come servizio di inferenza.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detected-frames.svg" alt-text="Analisi video in tempo reale basata su un modulo esterno di inferenza":::

In questo grafico multimediale, l'input del video dall'origine RTSP viene inviato a un nodo [processore di estensione http](media-graph-concept.md#http-extension-processor) , che invia frame immagine (in formati JPEG, BMP o png) a un servizio di inferenza esterno su REST. I risultati del servizio di inferenza esterno vengono recuperati dal nodo di estensione HTTP e inoltrati all'hub IoT Edge tramite il nodo del sink di messaggi dell'hub Internet. Questo tipo di grafico multimediale può essere usato per creare soluzioni per un'ampia gamma di scenari, ad esempio per comprendere la distribuzione di serie temporali di veicoli in un'intersezione, per comprendere il modello di traffico del cliente in un negozio al dettaglio e così via.
>[!TIP]
> È possibile gestire la frequenza dei fotogrammi all'interno del nodo processore di estensione HTTP usando il `samplingOptions` campo prima di inviarlo a valle.

Un miglioramento di questo esempio consiste nell'utilizzo di un processore di rilevamento di movimento rispetto al nodo del processore di estensione HTTP. Questo consente di ridurre il carico sul servizio di inferenza, perché viene usato solo quando è presente un'attività di movimento nel video.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/custom-model.svg" alt-text="Analisi dei video in tempo reale basata su frame rilevati in movimento tramite un modulo esterno di inferenza":::

## <a name="next-steps"></a>Passaggi successivi

[Registrazione continua di video](continuous-video-recording-concept.md)
