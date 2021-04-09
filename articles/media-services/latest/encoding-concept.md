---
title: Codifica di video e audio con servizi multimediali
description: Questo articolo spiega come codificare video e audio con servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 6a486057a265b02ce30059940c8c98837ec43f8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617642"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Codifica di video e audio con servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Il termine codifica in servizi multimediali si applica al processo di conversione di file contenenti video digitali e/o audio da un formato standard a un altro, con lo scopo di (a) di ridurre le dimensioni dei file e/o (b) produrre un formato compatibile con un'ampia gamma di dispositivi e app. Questo processo è noto anche come compressione video o transcodifica. Per ulteriori informazioni sui concetti, vedere [compressione dei dati](https://en.wikipedia.org/wiki/Data_compression) e informazioni su [codifica e transcodifica](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) .

I video vengono in genere recapitati a dispositivi e app tramite [download progressivo](https://en.wikipedia.org/wiki/Progressive_download) o [streaming a bitrate adattivo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

> [!IMPORTANT]
> Servizi multimediali non esegue la fatturazione per i processi annullati o con errori. Ad esempio, un processo che ha raggiunto il 50% di avanzamento e viene annullato non viene fatturato al 50% dei minuti del processo. Vengono addebitati solo i processi finiti.

* Per eseguire il download progressivo, è possibile usare servizi multimediali di Azure per convertire un file multimediale digitale (mezzanino) in un file [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) , che contiene video codificati con il codec [H. 264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) e audio che è stato codificato con il codec [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) . Questo file MP4 viene scritto in un asset nell'account di archiviazione. È possibile usare le API o gli SDK di archiviazione di Azure, ad esempio l' [API REST di archiviazione](../../storage/common/storage-rest-api-auth.md) o [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md), per scaricare direttamente il file. Se l'asset di output è stato creato con un nome di contenitore specifico nello spazio di archiviazione, usare tale percorso. In caso contrario, è possibile usare servizi multimediali per [elencare gli URL del contenitore di asset](/rest/api/media/assets/listcontainersas). 
* Per preparare il contenuto per la distribuzione tramite streaming a bitrate adattivo, è necessario codificare il file in formato intermedio a più velocità in bit (da alto a basso). Per garantire una transizione normale della qualità, la risoluzione del video viene ridotta quando il bitrate viene ridotto. In questo modo si ottiene una cosiddetta scala di codifica, ovvero una tabella di risoluzioni e bitrate (vedere [scala a bitrate adattivo generato automaticamente](autogen-bitrate-ladder.md)). È possibile usare servizi multimediali per codificare i file in formato intermedio a più velocità in bit. In questo modo, si otterrà un set di file MP4 e file di configurazione del flusso associati scritti in un asset nell'account di archiviazione. È quindi possibile usare la funzionalità di creazione [dinamica dei pacchetti](dynamic-packaging-overview.md) di servizi multimediali per distribuire il video tramite protocolli di streaming, ad esempio [MPEG-Dash](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) e [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). A questo scopo, è necessario creare un [localizzatore di streaming](streaming-locators-concept.md) e creare URL di streaming corrispondenti ai protocolli supportati, che possono essere quindi passati a dispositivi/app in base alle relative funzionalità.

Il diagramma seguente illustra il flusso di lavoro per la codifica su richiesta con la creazione dinamica dei pacchetti.

![Flusso di lavoro per la codifica su richiesta con creazione dinamica dei pacchetti](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Questo argomento contiene indicazioni su come codificare il contenuto con Servizi multimediali v3.

## <a name="transforms-and-jobs"></a>Trasformazioni e processi

Per eseguire la codifica con servizi multimediali V3, è necessario creare una [trasformazione](/rest/api/media/transforms) e un [processo](/rest/api/media/jobs). La trasformazione definisce una ricetta per le impostazioni di codifica e gli output. il processo è un'istanza della ricetta. Per altre informazioni, vedere [Trasformazioni e processi](transforms-jobs-concept.md).

Durante la codifica con Servizi multimediali si usano set di impostazioni per indicare al codificatore come elaborare i file multimediali di input. In servizi multimediali V3 si usa il codificatore standard per codificare i file. Ad esempio, è possibile specificare la risoluzione video e/o il numero di canali audio desiderati nel contenuto codificato.

Si può iniziare rapidamente con uno dei set di impostazioni predefiniti basati sulle procedure consigliate del settore oppure si può compilare un set di impostazioni personalizzato per i requisiti di uno specifico scenario o dispositivo. Per altre informazioni, vedere l'articolo sulla [codifica con una trasformazione personalizzata](customize-encoder-presets-how-to.md).

A partire da gennaio 2019, quando si esegue la codifica con il codificatore standard per produrre file MP4, viene generato un nuovo file con estensione MPI che viene aggiunto all'asset di output. Questo file MPI è progettato per migliorare le prestazioni per scenari di [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) e streaming.

> [!NOTE]
> Non è necessario modificare o rimuovere il file MPI né eseguire alcuna dipendenza nel servizio per l'esistenza o meno di un file di questo tipo.

### <a name="creating-job-input-from-an-https-url"></a>Creazione dell'input di un processo da un URL HTTPS

Quando si inviano processi per elaborare i video, è necessario indicare a servizi multimediali dove trovare il video di input. Una delle opzioni consiste nel specificare un URL HTTPS come input del processo. Attualmente, servizi multimediali V3 non supporta la codifica di trasferimento Chunked sugli URL HTTPS.

#### <a name="examples"></a>Esempio

* [Codificare da un URL HTTPS con .NET](stream-files-dotnet-quickstart.md)
* [Codificare da un URL HTTPS con REST](stream-files-tutorial-with-rest.md)
* [Codifica da un URL HTTPS con l'interfaccia della riga di comando](stream-files-cli-quickstart.md)
* [Codificare da un URL HTTPS con Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Creazione dell'input di un processo da un file locale

È possibile archiviare il video di input come asset di Servizi multimediali, nel qual caso si crea un asset di input basato su un file archiviato in locale o nel servizio di Archiviazione BLOB di Azure.

#### <a name="examples"></a>Esempio

[Codificare un file locale usando i set di impostazioni predefiniti](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Creazione dell'input del processo con il troncamento

Quando si codifica un video, è possibile specificare di tagliare o ritagliare anche il file di origine e produrre un output con solo una parte desiderata del video di input. Questa funzionalità funziona con qualsiasi [trasformazione](/rest/api/media/transforms) compilata usando i set di impostazioni [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o i set di impostazioni [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) .

È possibile specificare di creare un [processo](/rest/api/media/jobs/create) con una singola clip di un video su richiesta o di un archivio Live (un evento registrato). L'input del processo può essere un asset o un URL HTTPS.

> [!TIP]
> Se si vuole eseguire lo streaming di un sublip del video senza ricodificare il video, è consigliabile usare i [manifesti di pre-filtro con Dynamic Packager](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Esempio

Ecco alcuni esempi:

* [Creare una clip video secondaria con .NET](subclip-video-dotnet-howto.md)
* [Creare una clip video secondaria con REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Set di impostazioni predefiniti

Servizi multimediali supporta i set di impostazioni di codifica predefiniti seguenti:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) viene usato per configurare un set di impostazioni predefinito per la codifica del video di input con il codificatore Standard.

Sono attualmente supportati i set di impostazioni predefiniti seguenti:

- **EncoderNamedPreset. AACGoodQualityAudio**: produce un singolo file MP4 contenente solo audio stereo codificato a 192 kbps.
- **EncoderNamedPreset. AdaptiveStreaming** (scelta consigliata): supporta la codifica a bitrate adattivo H. 264. Per altre informazioni, vedere [Codificare con una tabella di coppie velocità in bit-risoluzione generata automaticamente](autogen-bitrate-ladder.md).
- **EncoderNamerPreset. H265AdaptiveStreaming** : simile al set di impostazioni AdaptiveStreaming, ma usa il codec HEVC (H. 265). Produce un set di file MP4 allineati GOP con audio AAC stereo e video H. 265. Genera automaticamente una scala a bitrate in base alla risoluzione di input, alla velocità in bit e alla frequenza dei fotogrammi. Il set di impostazioni generato automaticamente non supererà mai la risoluzione di input. Ad esempio, se l'input è 720p, l'output rimarrà 720p al massimo.
- **EncoderNamedPreset. ContentAwareEncoding**: espone un set di impostazioni per la codifica compatibile con il contenuto H. 264. Dato qualsiasi contenuto di input, il servizio tenta di determinare automaticamente il numero ottimale di livelli e le impostazioni di velocità in bit e risoluzione appropriate per il recapito tramite flusso adattivo. Gli algoritmi sottostanti continueranno a evolversi nel tempo. L'output conterrà file MP4 con interfoliazione video e audio. Per altre informazioni, vedere [codifica compatibile](content-aware-encoding.md)con i contenuti.
- **EncoderNamedPreset. H265ContentAwareEncoding**: espone un set di impostazioni per la codifica compatibile con il contenuto HEVC (H. 265). Produce un set di MP4s allineati a GOP utilizzando la codifica compatibile con il contenuto. Dato un contenuto di input, il servizio esegue un'analisi leggera iniziale del contenuto di input e usa i risultati per determinare il numero ottimale di livelli, le impostazioni di velocità in bit e risoluzione appropriate per il recapito tramite flusso adattivo. Questo set di impostazioni è particolarmente efficace per i video con complessità bassa e media, in cui i file di output saranno a velocità in bit inferiori, ma con una qualità che offre agli utenti un'esperienza ottimale. L'output conterrà file MP4 con interfoliazione video e audio.
  > [!NOTE]
  > Assicurarsi di usare **ContentAwareEncoding** not ContentAwareEncodingExperimental, che ora è deprecata

- **EncoderNamedPreset. H264MultipleBitrate1080p**: genera un set di otto file MP4 allineati al GOP, con velocità compresa tra 6000 e 400 kbps e audio AAC stereo. La risoluzione parte da 1080p e può scendere fino a 360p.
- **EncoderNamedPreset. H264MultipleBitrate720p**: genera un set di sei file MP4 allineati al GOP, con velocità compresa tra 3400 e 400 kbps e audio AAC stereo. La risoluzione parte da 720p e può scendere fino a 360p.
- **EncoderNamedPreset. H264MultipleBitrateSD**: genera un set di cinque file MP4 allineati al GOP, con velocità compresa tra 1600 e 400 kbps e audio AAC stereo. La risoluzione parte da 480p e può scendere fino a 360p.
- **EncoderNamedPreset. H264SingleBitrate1080p**: genera un file MP4 in cui il video è codificato con il codec H. 264 a 6750 kbps e un'altezza immagine di 1080 pixel e l'audio stereo è codificato con il codec AAC-LC a 64 kbps.
- **EncoderNamedPreset. H264SingleBitrate720p**: genera un file MP4 in cui il video è codificato con il codec H. 264 a 4500 kbps e un'altezza immagine di 720 pixel e l'audio stereo è codificato con il codec AAC-LC a 64 kbps.
- **EncoderNamedPreset. H264SingleBitrateSD**: genera un file MP4 in cui il video è codificato con il codec H. 264 a 2200 kbps e un'altezza immagine di 480 pixel e l'audio stereo è codificato con il codec AAC-LC a 64 kbps.
- **EncoderNamedPreset. H265SingleBitrate720P**: produce un file MP4 in cui il video è codificato con il codec HEVC (H. 265) a 1800 Kbps e un'altezza immagine di 720 pixel e l'audio stereo è codificato con il codec AAC-LC a 128 kbps.
- **EncoderNamedPreset. H265SingleBitrate1080p**: produce un file MP4 in cui il video è codificato con il codec HEVC (H. 265) a 3500 Kbps e un'altezza immagine di 1080 pixel e l'audio stereo è codificato con il codec AAC-LC a 128 kbps.
- **EncoderNamedPreset. H265SingleBitrate4K**: produce un file MP4 in cui il video è codificato con il codec HEVC (H. 265) a 9500 Kbps e un'altezza immagine di 2160 pixel e l'audio stereo è codificato con il codec AAC-LC a 128 kbps.

Per visualizzare l'elenco dei set di impostazioni più aggiornati, vedere set di [impostazioni predefiniti da usare per la codifica di video](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Per vedere come vengono usati i set di impostazioni, vedere [caricamento, codifica e streaming di file](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) descrive le impostazioni da usare per la codifica del video di input con il codificatore Standard. Usare questo set di impostazioni quando si personalizzano i set di impostazioni di trasformazione.

#### <a name="considerations"></a>Considerazioni

Quando si creano set di impostazioni personalizzati, si applicano le considerazioni seguenti:

- Tutti i valori per altezza e larghezza sul contenuto AVC devono essere un multiplo di quattro.
- In servizi multimediali di Azure V3 tutti i bitrate di codifica sono in bit al secondo. Questa impostazione è diversa da quella delle API v2, che hanno usato kilobit al secondo come unità. Se, ad esempio, la velocità in bit in V2 è stata specificata come 128 (kilobit al secondo), in V3 verrebbe impostata su 128000 (bit al secondo).

### <a name="customizing-presets"></a>Personalizzazione dei set di impostazioni

Servizi multimediali supporta in modo completo la personalizzazione di tutti i valori nei set di impostazioni per soddisfare le esigenze e i requisiti di codifica specifici. Per esempi che illustrano come personalizzare i set di impostazioni del codificatore, vedere l'elenco seguente:

#### <a name="examples"></a>Esempio

- [Personalizzare i set di impostazioni con .NET](customize-encoder-presets-how-to.md)
- [Personalizzare le impostazioni predefinite con l'interfaccia della riga di comando](custom-preset-cli-howto.md)
- [Personalizzare i set di impostazioni con REST](custom-preset-rest-howto.md)


## <a name="preset-schema"></a>Schema preimpostato

In servizi multimediali V3 i set di impostazioni sono entità fortemente tipizzate nell'API stessa. È possibile trovare la definizione dello schema per questi oggetti in [Open API Specification (o spavalderia)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). È anche possibile visualizzare le definizioni di set di impostazioni (ad esempio **StandardEncoderPreset**) nell' [API REST](/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset) (o altri documenti di riferimento di Media Services V3 SDK).

## <a name="scaling-encoding-in-v3"></a>Ridimensionamento della codifica nella versione v3

Per ridimensionare l'elaborazione di contenuti multimediali, vedere [ridimensionare con CLI](media-reserved-units-cli-how-to.md).
Per gli account creati con la versione **2020-05-01** dell'API o tramite il portale di Azure, le unità di ridimensionamento e media reserved unit non sono più necessarie. Il ridimensionamento sarà automatico e gestito internamente dal servizio.

## <a name="billing"></a>Fatturazione

Servizi multimediali non esegue la fatturazione per i processi annullati o con errori. Ad esempio, un processo che ha raggiunto il 50% di avanzamento e viene annullato non viene fatturato al 50% dei minuti del processo. Vengono addebitati solo i processi finiti.

Per ulteriori informazioni, vedere [prezzi](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Caricare, codificare e trasmettere in streaming con servizi multimediali](stream-files-tutorial-with-api.md).
* [Codificare da un URL HTTPS usando i set di impostazioni predefiniti](job-input-from-http-how-to.md).
* [Codificare un file locale usando i set di impostazioni predefiniti](job-input-from-local-file-how-to.md).
* [Creare un set di impostazioni personalizzato per i requisiti specifici di uno scenario o un dispositivo](customize-encoder-presets-how-to.md).
