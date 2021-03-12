---
title: Creazione dinamica dei pacchetti in Servizi multimediali di Azure versione 3
description: Questo articolo offre una panoramica della creazione dinamica dei pacchetti in Servizi multimediali di Azure.
author: myoungerman
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 4f4f53d4a20397f38b565cb73e74b01d15cc3022
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633054"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Creazione dinamica dei pacchetti in Servizi multimediali versione 3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Servizi multimediali di Microsoft Azure può essere usato per codificare molti formati di file di origini multimediali. Tali formati vengono distribuiti tramite protocolli di streaming diversi, con o senza protezione del contenuto, per raggiungere tutti i dispositivi principali, ad esempio dispositivi iOS e Android. Tali client supportano protocolli diversi. iOS, ad esempio, richiede che i flussi vengano distribuiti nel formato HLS (HTTP Live Streaming), mentre i dispositivi Android supportano HLS e MPEG DASH.

In Servizi multimediali un [endpoint di streaming](streaming-endpoint-concept.md) (origine) rappresenta un servizio di generazione e creazione di pacchetti dinamici (just-in-time) in grado di distribuire contenuti live e on demand direttamente a un'app di riproduzione client. Usa uno dei protocolli multimediali comuni di streaming indicati nella sezione seguente. La *creazione dinamica dei pacchetti* è una funzionalità standard di tutti gli endpoint di streaming.

## <a name="to-prepare-your-source-files-for-delivery"></a>Per preparare i file di origine per la distribuzione

Per sfruttare la creazione dinamica dei pacchetti, è necessario [codificare](encoding-concept.md) il file mezzanine (origine) in un set di file MP4 (ISO Base Media 14496-12) a bitrate multiplo. È necessario avere un [asset](assets-concept.md) con i file MP4 codificati e i file di configurazione di streaming richiesti dalla creazione dinamica dei pacchetti di Servizi multimediali. Da questo set di file MP4, è possibile usare la creazione dinamica dei pacchetti per distribuire video tramite i protocolli di streaming multimediale seguenti.

La creazione dinamica dei pacchetti di Servizi multimediali di Azure supporta solo i file video e audio nel formato contenitore MP4. I file audio devono essere codificati in un contenitore MP4 anche quando si usano codec alternativi come Dolby.  

> [!TIP]
> Un modo per ottenere i file di configurazione di streaming e MP4 consiste nel [codificare il file mezzanine con Servizi multimediali](#encode-to-adaptive-bitrate-mp4s). 

Per rendere i video nell'asset codificato disponibili per la riproduzione da parte dei client, è necessario creare un [localizzatore di streaming](streaming-locators-concept.md) e quindi definire gli URL di streaming. In base al formato specificato nel manifesto del client di streaming (HLS, MPEG DASH o Smooth Streaming), si riceverà il flusso nel protocollo scelto.

Di conseguenza, si archiviano e si pagano solo i file in un singolo formato di archiviazione e il servizio Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

Se si prevede di proteggere il contenuto usando la crittografia dinamica di Servizi multimediali, vedere [Protocolli di streaming e tipi di crittografia](content-protection-overview.md#streaming-protocols-and-encryption-types).

### <a name="hls-protocol"></a>Protocollo HLS

Il client di streaming può specificare i formati HLS seguenti:

|Protocollo|Esempio|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

> [!NOTE]
> Le linee guida precedenti di Apple consigliavano che il fallback per le reti a larghezza di banda ridotta fornissero un flusso solo audio.  Al momento, il codificatore di Servizi multimediali genera automaticamente una traccia solo audio.  Le linee guida di Apple attuali dichiarano che la traccia solo audio *non* deve essere inclusa, specialmente per la distribuzione di Apple TV.  Per evitare che il lettore usi l'impostazione predefinita di una traccia solo audio, è consigliabile usare il tag "audio-only=false" nell'URL, che rimuove il rendering solo audio in HLS o semplicemente usa HLS-V3. Ad esempio: `http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)`.

### <a name="mpeg-dash-protocol"></a>Protocollo MPEG-DASH

Il client di streaming può specificare i formati MPEG-DASH seguenti:

|Protocollo|Esempio|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Protocollo Smooth Streaming

Il client di streaming può specificare i formati Smooth Streaming seguenti:

|Protocollo|Note/esempi| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Smooth Streaming 2.0 (manifesto legacy)|Per impostazione predefinita, il manifesto Smooth Streaming contiene il tag di ripetizione (r-tag). Alcuni lettori, tuttavia, non supportano il tag di ripetizione `r-tag`. I client con questi lettori possono usare un formato che disabilita gli r-tag:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Smooth Streaming richiede che sia audio che video siano presenti nel flusso.

## <a name="on-demand-streaming-workflow"></a>Flusso di lavoro dello streaming on demand

I passaggi seguenti indicano un flusso di lavoro comune di streaming di Servizi multimediali in cui viene usata la creazione dinamica dei pacchetti insieme al codificatore Standard in Servizi multimediali di Azure.

1. [Caricare un file di input](job-input-from-http-how-to.md), ad esempio MP4, QuickTime/MOV o un altro formato di file supportato. Questo tipo di file è anche detto file di origine o mezzanine. Per l'elenco di formati supportati, vedere [Formati supportati dal codificatore standard](media-encoder-standard-formats.md).
1. [Codificare](#encode-to-adaptive-bitrate-mp4s) il file mezzanine in un set di file MP4 H.264/AAC a bitrate adattivo.

    Se sono già presenti file codificati da copiare e trasmettere in streaming, usare le API [CopyVideo](/rest/api/media/transforms/createorupdate#copyvideo) e [CopyAudio](/rest/api/media/transforms/createorupdate#copyaudio). Verrà quindi creato un nuovo file MP4 con un manifesto di streaming (file con estensione ism).
1. Pubblicare l'asset di output contenente il set MP4 a bitrate adattivo. La pubblicazione avviene creando un [localizzatore di streaming](streaming-locators-concept.md).
1. Creare URL destinati a formati diversi (HLS, MPEG-DASH e Smooth Streaming). L'*endpoint di streaming* si occupa di gestire il manifesto corretto e le richieste per tutti questi formati diversi.
    
Il diagramma seguente illustra il flusso di lavoro per lo streaming on demand con la creazione dinamica dei pacchetti.

![Diagramma di un flusso di lavoro per lo streaming on demand con creazione dinamica dei pacchetti](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Il percorso di download è presente nell'immagine precedente solo per mostrare che è possibile scaricare un file MP4 direttamente tramite l'*endpoint di streaming* (origine). I [criteri di streaming](streaming-policy-concept.md) scaricabili si specificano nel localizzatore di streaming.<br/>Lo strumento per la creazione dinamica di pacchetti non altera il file. Se si vuole, è possibile usare le API di archiviazione BLOB di Azure per accedere a un MP4 direttamente per il download progressivo in modo da ignorare le funzionalità di *endpoint di streaming* (origine). 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Eseguire la codifica in MP4 a velocità in bit adattiva

Gli articoli seguenti illustrano alcuni esempi di [come codificare un video con Servizi multimediali](encoding-concept.md):

* [Eseguire la codifica da un URL HTTPS tramite i set di impostazioni predefiniti](job-input-from-http-how-to.md).
* [Eseguire la codifica di un file locale tramite i set di impostazioni predefiniti](job-input-from-local-file-how-to.md).
* [Creare un set di impostazioni personalizzato per i requisiti specifici di uno scenario o un dispositivo](customize-encoder-presets-how-to.md).

Consultare l'elenco di [codec e formati](media-encoder-standard-formats.md) del codificatore standard.

## <a name="live-streaming-workflow"></a>Flusso di lavoro dello streaming live

Un evento live può essere impostato su un *pass-through* (un codificatore live locale invia un flusso a bitrate multiplo) o sulla *codifica live* (un codificatore live locale invia un flusso a bitrate singolo). 

Ecco un flusso di lavoro comune per lo streaming live con la *creazione dinamica dei pacchetti*:

1. Creare un [evento live](live-events-outputs-concept.md).
1. Ottenere l'URL di inserimento e configurare il codificatore a livello locale per usare l'URL per inviare il feed di contributo.
1. Ottenere l'URL di anteprima e usarlo per verificare che l'input dal codificatore venga ricevuto.
1. Creare un nuovo asset.
1. Creare un output live e usare il nome dell'asset creato.<br />L'output live archivia il flusso nell'asset.
1. Creare un localizzatore di streaming con i tipi di criteri di streaming predefiniti.<br />Se si prevede di crittografare il contenuto, rivedere la [Panoramica della protezione del contenuto](content-protection-overview.md).
1. Elencare i percorsi nel localizzatore di streaming per ottenere gli URL da usare.
1. Ottenere il nome host per l'endpoint di streaming da cui si vuole trasmettere.
1. Creare URL destinati a formati diversi (HLS, MPEG-DASH e Smooth Streaming). L'*endpoint di streaming* si occupa di gestire il manifesto corretto e le richieste per i diversi formati.

Questo diagramma illustra il flusso di lavoro per lo streaming live con la *creazione dinamica dei pacchetti*:

![Diagramma di un flusso di lavoro per la codifica pass-through con creazione dinamica dei pacchetti](./media/live-streaming/pass-through.svg)

Per informazioni sullo streaming live in Servizi multimediali v3, vedere [Panoramica dello streaming live](live-streaming-overview.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Codec video supportati dalla creazione dinamica dei pacchetti

La creazione dinamica dei pacchetti supporta file video nel formato di file contenitore MP4 che contengono video codificati con [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC o AVC1) o [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 o hvc1).

> [!NOTE]
> Con la *creazione dinamica dei pacchetti* sono state testate risoluzioni fino a 4K e frequenze dei fotogrammi fino a 60 fotogrammi al secondo.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codec audio supportati dalla creazione dinamica dei pacchetti

La creazione dinamica dei pacchetti supporta anche i file audio archiviati nel formato contenitore di file MP4 che contengono il flusso audio codificato in uno dei codec seguenti:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 o HE-AAC v2). 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 o E-AC3).  Per essere supportato dalla creazione dinamica dei pacchetti, l'audio codificato deve essere archiviato nel formato contenitore MP4.
* Dolby Atmos

   Lo streaming di contenuti Atmos Dolby è supportato per gli standard come il protocollo MPEG-DASH con MP4 frammentato CSF (Common Streaming Format) o CMAF (Common Media Application Format) e tramite HTTP Live Streaming (HLS) con CMAF.
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   I codec DTS supportati dai formati di creazione dei pacchetti DASH-CSF, DASH-CMAF, HLS-M2TS e HLS-CMAF sono:  

    * DTS Digital Surround (dtsc)
    * DTS-HD High Resolution e DTS-HD Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless (senza core) (dtsl)

La creazione dinamica dei pacchetti supporta più tracce audio con DASH o HLS (versione 4 o successive) per lo streaming di asset che hanno diverse tracce audio con più codec e linguaggi.

Per tutti i codec audio elencati sopra, l'audio codificato deve essere archiviato nel formato contenitore MP4 per essere supportato dalla creazione dinamica dei pacchetti. Il servizio non supporta i formati di file di flusso elementare non elaborato nell'archiviazione BLOB (ad esempio, il formato seguente non è supportato - .dts, .ac3). 

Per la creazione di pacchetti audio sono supportati solo i file con estensione mp4 o mp4a. 

### <a name="limitations"></a>Limitazioni

#### <a name="ios-limitation-on-aac-51-audio"></a>Limitazione di iOS per l'audio AAC 5.1

I dispositivi Apple iOS non supportano il codec audio AAC 5.1. L'audio multicanale deve essere codificato usando i codec Dolby Digital o Dolby Digital Plus.

Per informazioni dettagliate, vedere la [specifica per la creazione di contenuti HLS per dispositivi Apple](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices).

> [!NOTE]
> Servizi multimediali non supporta la codifica di formati audio multicanale Dolby Digital, Dolby Digital Plus o Dolby Digital Plus con Dolby Atmos.

#### <a name="dolby-digital-audio"></a>Audio Dolby Digital

La creazione dinamica dei pacchetti di Servizi multimediali non supporta attualmente file che contengono audio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3), perché questo è considerato un codec legacy di Dolby.

## <a name="manifests"></a>Manifesti

Nella *creazione dinamica dei pacchetti di* Servizi multimediali i manifesti dei client di streaming per HLS, MPEG-DASH e Smooth Streaming vengono generati dinamicamente in base al selettore di formato nell'URL.  

Un file manifesto include alcuni metadati di streaming, tra cui il tipo di traccia (audio, video o testo), il nome della traccia, l'ora di inizio e di fine, la velocità in bit (qualità), le lingue della traccia, la finestra di presentazione (finestra scorrevole di durata fissa) e un codec video (FourCC). Indica inoltre al lettore come recuperare il frammento successivo fornendo informazioni sui successivi frammenti video riproducibili disponibili e il relativo percorso. I frammenti (o segmenti) sono i "blocchi" effettivi di un contenuto video.

### <a name="examples"></a>Esempi

#### <a name="hls"></a>HLS

Di seguito è riportato un esempio di file manifesto HLS, detto anche playlist master HLS: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

Di seguito è riportato un esempio di file manifesto MPEG-DASH, detto anche MPEG-DASH MPD (Media Presentation Description):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Smooth Streaming

Di seguito è riportato un esempio di file manifesto Smooth Streaming:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>Denominazione delle tracce nel manifesto

Se nel file con estensione ism viene specificato un nome di traccia audio, Servizi multimediali aggiunge un elemento `Label` all'interno di `AdaptationSet` per specificare le informazioni di texturing per la traccia audio specifica. Esempio del manifesto DASH di output:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

Il lettore può usare l'elemento `Label` da visualizzare nell'interfaccia utente.

### <a name="signaling-audio-description-tracks"></a>Segnalazione delle tracce di descrizione audio

È possibile aggiungere una traccia audio al video per aiutare i clienti con problemi di vista a seguire la registrazione ascoltando la narrazione. È necessario annotare una traccia audio come descrizione audio nel manifesto. A questo scopo, aggiungere i parametri "accessibility" e "role" al file con estensione ism. È responsabilità del cliente impostare correttamente questi parametri per segnalare una traccia audio come descrizione audio. Ad esempio, aggiungere `<param name="accessibility" value="description" />` e `<param name="role" value="alternate"` al file ISM per una traccia audio specifica. 

Per altre informazioni, vedere l'esempio [Come segnalare una traccia audio descrittiva](signal-descriptive-audio-howto.md).

#### <a name="smooth-streaming-manifest"></a>Manifesto Smooth Streaming

Se si riproduce un flusso di Smooth Streaming, il manifesto conterrà i valori negli attributi `Accessibility` e `Role` per la traccia audio. `Role="alternate" Accessibility="description"` verrebbe aggiunto ad esempio nell'elemento `StreamIndex` per indicare che si tratta di una descrizione audio.

#### <a name="dash-manifest"></a>Manifesto DASH

Per il manifesto DASH, verranno aggiunti i due elementi seguenti per segnalare la descrizione audio:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>Playlist HLS

Per HLS V7 e versioni successive a `(format=m3u8-cmaf)`, la playlist contiene `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` quando viene segnalata la traccia della descrizione audio.

#### <a name="example"></a>Esempio

Per altre informazioni, vedere [Come segnalare tracce di descrizione audio](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Manifesto dinamico

Per controllare il numero di tracce, i formati, la velocità in bit e gli intervalli di tempo di presentazione che vengono inviati ai lettori, è possibile usare i filtri dinamici con la creazione dinamica dei pacchetti di Servizi multimediali. Per altre informazioni, vedere [Filtro preliminare dei manifesti con la creazione dinamica dei pacchetti](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Crittografia dinamica

È possibile usare la *crittografia dinamica* per crittografare dinamicamente i contenuti live o on demand tramite AES-128 o con uno dei tre principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali fornisce inoltre un servizio per la distribuzione di chiavi AES e licenze Digital Rights Management ai client autorizzati. Per altre informazioni, vedere [Crittografia dinamica](content-protection-overview.md).

> [!NOTE]
> Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="more-information"></a>Ulteriori informazioni

Consultare [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="need-help"></a>Richiesta di assistenza

Per aprire un ticket di supporto, passare a [Nuova richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

[Caricare, codificare ed eseguire lo streaming dei video](stream-files-tutorial-with-api.md)