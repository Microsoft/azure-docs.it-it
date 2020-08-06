---
title: Uso di Servizi multimediali di Azure per la progettazione ibrida di sottosistemi DRM | Microsoft Docs
description: Questo articolo spiega come usare Servizi multimediali di Azure per la progettazione ibrida di sottosistemi DRM.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: ce2fbe8c706870250973974c7a73a3964dedd04a
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831890"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Progettazione ibrida di sottosistemi DRM 

Questo articolo spiega come usare Servizi multimediali di Azure per la progettazione ibrida di sottosistemi DRM.

## <a name="overview"></a>Panoramica

Servizi multimediali di Azure offre supporto per i seguenti tre sistemi DRM:

* PlayReady
* Widevine (modulare)
* FairPlay

Il supporto per DRM include la crittografia DRM (crittografia dinamica) e la distribuzione della licenza, quando Azure Media Player supporta tutti e 3 i DRM come un SDK per lettore di browser.

Per una spiegazione dettagliata della progettazione e implementazione dei sottosistemi DRM/CENC, vedere il documento intitolato [CENC with Multi-DRM and Access Control](media-services-cenc-with-multidrm-access-control.md) (CENC con più DRM e controllo dell'accesso).

Sebbene Microsoft offra un supporto completo per tutti e 3 i sistemi DRM, i clienti hanno talvolta l'esigenza di usare varie parti della propria infrastruttura/dei propri sottosistemi, oltre a Servizi multimediali di Azure, per creare un sottosistema DRM ibrido.

Di seguito sono riportate alcune delle domande comuni poste dai clienti:

* "Posso usare i miei server licenze DRM?" (in questo caso, i clienti hanno investito in un farm di licenze server DRM con logica di business incorporata)
* "Posso usare solo la funzionalità di distribuzione della licenza DRM di Servizi multimediali di Azure senza ospitare contenuti in AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modularità della piattaforma DRM di AMS

In quanto parte di una piattaforma video cloud completa, DRM di Servizi multimediali di Azure è stato progettato all'insegna della flessibilità e modularità. È possibile usare Servizi multimediali di Azure con una delle seguenti combinazioni diverse elencate nella seguente tabella (di seguito è riportata anche una spiegazione della notazione usata nella tabella). 

|**Hosting e origine del contenuto**|**Crittografia del contenuto**|**Distribuzione di licenze DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Terze parti|
|AMS|Terze parti|AMS|
|AMS|Terze parti|Terze parti|
|Terze parti|Terze parti|AMS|

### <a name="content-hosting--origin"></a>Hosting e origine del contenuto

* AMS: l'asset video è ospitato in AMS mentre lo streaming viene eseguito tramite gli endpoint di streaming (ma non necessariamente tramite la creazione dinamica dei pacchetti).
* Terze parti: il video è ospitato e distribuito su una piattaforma di streaming di terze parti, esterna ad AMS.

### <a name="content-encryption"></a>Crittografia del contenuto

* AMS: la crittografia del contenuto viene eseguita dinamicamente/on demand dalla crittografia dinamica di AMS.
* Terze parti: la crittografia del contenuto viene eseguita all'esterno di AMS usando un flusso di lavoro di pre-elaborazione.

### <a name="drm-license-delivery"></a>Distribuzione di licenze DRM

* AMS: la licenza di DRM viene distribuita dal servizio di distribuzione della licenza di AMS.
* Terze parti: la licenza di DRM viene distribuita da un server licenze DRM di terze parti esterno ad AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Configurare in base allo scenario ibrido

### <a name="content-key"></a>Chiave simmetrica

La configurazione di una chiave simmetrica permette di controllare i seguenti attributi della crittografia dinamica e del servizio di distribuzione della licenza di AMS:

* Chiave simmetrica usata per la crittografia dinamica di DRM.
* Contenuto della licenza DRM da distribuire tramite i servizi di distribuzione della licenza: diritti, chiave simmetrica e limitazioni.
* Tipo di **limitazione dei criteri di autorizzazione della chiave simmetrica**: aperta, IP o limitazione del token.
* Se si usa il **token** per la **limitazione dei criteri di autorizzazione della chiave simmetrica**, è necessario che venga soddisfatta **questa limitazione** prima che la licenza possa essere rilasciata.

### <a name="asset-delivery-policy"></a>Criteri di distribuzione degli asset

La configurazione di criteri di distribuzione di un asset permette di controllare i seguenti attributi usati dalla creazione di pacchetti dinamica e dalla crittografia dinamica di AMS di un endpoint di streaming AMS:

* Combinazione di protocollo di streaming e crittografia DRM, come DASH in CENC (PlayReady e Widevine), Smooth Streaming in PlayReady, HLS in Widevine o PlayReady.
* Gli URL di distribuzione della licenza predefiniti/incorporati per ciascuno dei DRM interessati.
* Presenza o meno di una stringa query per l'ID chiave (KID) di Widevine e Fairplay negli URL di acquisizione della licenza (LA_URL) nell'elenco di riproduzione DASH MPD o HLS.

## <a name="scenarios-and-samples"></a>Scenari ed esempi

In base alle spiegazioni della sezione precedente, i cinque scenari ibridi seguenti usano le rispettive combinazioni di configurazione dei criteri di distribuzione degli asset della **chiave**simmetrica - **Asset delivery policy** (gli esempi indicati nell'ultima colonna seguono la tabella):

|**Hosting e origine del contenuto**|**Crittografia DRM**|**Distribuzione di licenze DRM**|**Configurare la chiave simmetrica**|**Configurare i criteri di distribuzione dell'asset**|**Esempio**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Sì|Sì|Esempio 1|
|AMS|AMS|Terze parti|Sì|Sì|Esempio 2|
|AMS|Terze parti|AMS|Sì|No|Esempio 3|
|AMS|Terze parti|Esterno|No|No|Esempio 4|
|Terze parti|Terze parti|AMS|Sì|No|    

Negli esempi, la protezione PlayReady funziona sia per DASH che per Smooth Streaming. Gli URL del video mostrati di seguito sono URL Smooth Streaming. Per ottenere gli URL DASH corrispondenti, aggiungere semplicemente "(format=mpd-time-csf)". È possibile usare il [lettore multimediale di test di Azure](https://aka.ms/amtest) per effettuare il test nel browser. Consente di configurare il protocollo di streaming e specificare in quale tecnologia usarlo. Internet Explorer 11 e Microsoft Edge in Windows 10 supportano PlayReady tramite EME. Per altre informazioni, vedere i [dettagli sullo strumento di test](./offline-playready-streaming-windows-10.md).

### <a name="sample-1"></a>Esempio 1

* URL di origine (base): `https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest` 
* PlayReady LA_URL (DASH e smooth): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 
* Widevine LA_URL (DASH): `https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4` 
* FairPlay LA_URL (HLS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8` 

### <a name="sample-2"></a>Esempio 2

* URL di origine (base): https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH e smooth): `http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx` 

### <a name="sample-3"></a>Esempio 3

* URL di origine: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH e smooth): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 

### <a name="sample-4"></a>Esempio 4

* URL di origine: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH e smooth): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx` 

## <a name="additional-notes"></a>Note aggiuntive

* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="summary"></a>Riepilogo

In sintesi, i componenti DRM di Servizi multimediali di Azure sono flessibili ed è possibile usarli in uno scenario ibrido configurando correttamente la chiave simmetrica e i criteri di distribuzione della licenza, come descritto in questo articolo.

## <a name="next-steps"></a>Passaggi successivi
Visualizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]