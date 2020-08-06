---
title: Note sulla versione di Servizi multimediali di Azure
description: Questo articolo illustra le note sulla versione di Servizi multimediali di Microsoft Azure v2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: 5348b4cfb1961bdada7c32f3c437cdb2819ef6d0
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87833029"
---
# <a name="azure-media-services-release-notes"></a>Note sulla versione di Servizi multimediali di Azure

Nelle presenti note sulla versione per Servizi multimediali di Azure vengono riepilogate le modifiche rispetto alle versioni precedenti e i problemi noti.

> [!NOTE]
> Non saranno aggiunte nuove funzionalità a Servizi multimediali v2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](../latest/index.yml). Vedere anche le [indicazioni sulla migrazione dalla versione 2 alla versione 3](../latest/migrate-from-v2-to-v3.md).

Microsoft invita i clienti a segnalare eventuali problemi e si impegna a risolverli. Per segnalare un problema o rivolgere domande, pubblicare un post nel [forum MSDN su Servizi multimediali di Microsoft Azure]. 

## <a name="known-issues"></a><a name="issues"></a>Problemi noti
### <a name="media-services-general-issues"></a><a name="general_issues"></a>Problemi generali di Servizi multimediali

| Problema | Descrizione |
| --- | --- |
| Nell'API REST non sono disponibili alcune intestazioni HTTP comuni. |Se si sviluppano applicazioni di Servizi multimediali tramite l'API REST, alcuni campi di intestazione HTTP comuni, ad esempio CLIENT-REQUEST-ID, REQUEST-ID e RETURN-CLIENT-REQUEST-ID, non sono supportati. Le intestazioni verranno aggiunte in un futuro aggiornamento. |
| La codifica percentuale non è consentita. |Servizi multimediali usa il valore della proprietà IAssetFile.Name durante la generazione di URL per i contenuti in streaming (ad esempio, `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`). Per questo motivo, la codifica percentuale non è consentita. Il valore della proprietà Name non può contenere i [caratteri riservati per la codifica percentuale](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) seguenti: !*'();:@&=+$,/?%#[]". L'estensione del nome di file, inoltre, può essere preceduta da un solo punto (.). |
| Il metodo ListBlobs di Azure Storage SDK versione 3.x non riesce. |Servizi multimediali genera URL di firma di accesso condiviso basati sulla versione [2012-02-12](/rest/api/storageservices/version-2012-02-12) . Se si vuole usare Storage SDK per elencare oggetti BLOB in un contenitore BLOB, usare il metodo [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) disponibile in Storage SDK versione 2.x. |
| Il meccanismo di limitazione delle richieste di Servizi multimediali limita l'uso delle risorse per le applicazioni che inviano un numero elevato di richieste al servizio. Il servizio può restituire il codice di stato HTTP 503 indicante che il servizio non è disponibile. |Per altre informazioni, vedere la descrizione del codice di stato HTTP 503 in [Codici di errore di Servizi multimediali](media-services-encoding-error-codes.md). |
| Quando si esegue una query di entità, è previsto un limite di 1.000 entità restituite in una sola volta perché la versione 2 pubblica di REST limita i risultati della query a 1.000 risultati. |Usare Skip e Take (.NET)/top (REST) come descritto in [questo esempio .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e in [questo esempio di API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Alcuni client possono riscontrare un problema di tag di ripetizione nel manifesto Smooth Streaming. |Per altre informazioni, vedere [questa sezione](media-services-deliver-content-overview.md#known-issues). |
| Gli oggetti Media Services .NET SDK non possono essere serializzati e di conseguenza non funzionano con la Cache di Azure per Redis. |Se si prova a serializzare l'oggetto AssetCollection dell'SDK per aggiungerlo a Cache di Azure per Redis, viene generata un'eccezione. |
|Quando si tenta di ottenere un filtro a livello di asset o di account, l'API REST risponde con un messaggio di errore che indica che non è possibile accedere al filtro da questa versione dell'API REST.|Il filtro è stato creato o modificato con una versione dell'API più recente di quella usata per provare a ottenere il filtro. Questo problema può verificarsi se due versioni dell'API vengono usate dal codice o dagli strumenti usati dal cliente.  La soluzione migliore consiste nell'aggiornare il codice o gli strumenti alla versione più recente delle due versioni dell'API.|

## <a name="rest-api-version-history"></a><a name="rest_version_history"></a>Cronologia delle versioni dell'API REST
Per informazioni sulla cronologia delle versioni dell'API REST di Servizi multimediali, vedere [Informazioni di riferimento sull'API REST di Servizi multimediali di Azure].

## <a name="february-2020"></a>Febbraio 2020

Alcuni processori di contenuti multimediali di analisi verranno ritirati. Per informazioni sulle date di ritiro, vedere l'argomento relativo ai [componenti legacy](legacy-components.md).

## <a name="september-2019"></a>Settembre 2019

### <a name="deprecation-of-media-processors"></a>Deprecazione dei processori di contenuti multimediali

Si annuncia la deprecazione di *Azure Media Indexer* e *Anteprima di Azure Media Indexer 2*. [Video Indexer di Servizi multimediali di Azure](../video-indexer/index.yml) sostituisce questi processori di contenuti multimediali legacy.

Per le date di ritiro, vedere questo argomento relativo ai [componenti legacy](legacy-components.md).

Vedere anche l'articolo [Eseguire la migrazione da Azure Media Indexer e Azure Media Indexer 2 a Video Indexer di Servizi multimediali di Azure](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto 2019

### <a name="deprecation-of-media-processors"></a>Deprecazione dei processori di contenuti multimediali

Si annuncia la deprecazione dei processori di contenuti multimediali *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME). Per le date di ritiro, vedere questo argomento relativo ai [componenti legacy](legacy-components.md).

Per informazioni dettagliate, vedere [Eseguire la migrazione da WAME a Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) e [Eseguire la migrazione da AME a Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).

## <a name="march-2019"></a>Marzo 2019

La funzionalità di anteprima Media Hyperlapse di Servizi multimediali di Azure è deprecata.

## <a name="december-2018"></a>Dicembre 2018

La funzionalità di anteprima Media Hyperlapse di Servizi multimediali di Azure verrà ritirata a breve. A partire dal 19 dicembre 2018 non verranno più apportati miglioramenti o modifiche alla funzionalità Media Hyperlapse di Servizi multimediali. Il 29 marzo 2019 questa funzionalità verrà ritirata e non sarà più disponibile.

## <a name="october-2018"></a>Ottobre 2018

### <a name="cmaf-support"></a>Supporto per CMAF

Supporto della crittografia CMAF e 'cbcs' per lettori Apple HLS (iOS 11 +) e MPEG-DASH che supportano CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Sprite di anteprima WebVTT

È ora possibile usare Servizi multimediali per generare degli sprite di anteprima WebVTT con le API v2. Per altre informazioni, vedere [Generare uno sprite di anteprima](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Luglio 2018

Con la versione più recente del servizio c'è un minor numero di modifiche di formattazione per i messaggi di errore restituiti dal servizio quando un processo fallisce rispetto al modo in cui è suddiviso in due o più righe di formattazione secondarie.

## <a name="may-2018"></a>Maggio 2018 

A partire dal 12 maggio 2018 i canali live non supporteranno più il protocollo di inserimento del flusso di trasporto RTP/MPEG-2. Eseguire la migrazione da RTP/MPEG-2 ai protocolli di inserimento RTMP o MP4 frammentato (Smooth Streaming).

## <a name="october-2017-release"></a>Versione di ottobre 2017
> [!IMPORTANT] 
> Il supporto delle chiavi di autenticazione del Servizio di controllo di accesso di Azure in Servizi multimediali è in fase di deprecazione. A partire dal 22 giugno 2018 non sarà più possibile autenticarsi con il back-end di Servizi multimediali tramite il codice usando le chiavi del Servizio di controllo di accesso. È necessario aggiornare il codice in modo da usare Azure Active Directory (Azure AD) come indicato in [Accesso all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md). Controllare gli avvisi relativi a questa modifica nel portale di Azure.

### <a name="updates-for-october-2017"></a>Aggiornamenti di ottobre 2017
#### <a name="sdks"></a>SDK
* .NET SDK è stato aggiornato per supportare l'autenticazione di Azure AD. Il supporto per l'autenticazione del Servizio di controllo di accesso è stato rimosso dalla versione più recente di .NET SDK in Nuget.org allo scopo di velocizzare la migrazione ad Azure AD. 
* JAVA SDK è stato aggiornato per supportare l'autenticazione di Azure AD. È stato aggiunto il supporto per l'autenticazione di Azure AD in Java SDK. Per informazioni su come usare Java SDK con Servizi multimediali, vedere [Introduzione a Java Client SDK per Servizi multimediali di Azure](media-services-java-how-to-use.md).

#### <a name="file-based-encoding"></a>Codifica basata su file
* È ora possibile usare il codificatore Premium per codificare il contenuto nel codec video ad alta efficienza HEVC, noto come H.265. La scelta del codec H.265 rispetto ad altri codec, come H.264, non influisce in alcun modo sui prezzi. Per informazioni sulle licenze di brevetto per il codec HEVC, vedere [Condizioni per l'Utilizzo dei Servizi Online](https://azure.microsoft.com/support/legal/).
* Per i video di origine codificati con il codec video H.265 (HEVC), ad esempio i video acquisiti con iOS11 o GoPro Hero 6, è ora possibile usare il codificatore Premium o Standard per codificare questi video. Per informazioni sulle licenze di brevetto, vedere [Condizioni per l'Utilizzo dei Servizi Online](https://azure.microsoft.com/support/legal/).
* Per il contenuto con tracce audio in più lingue, i valori delle lingue devono essere identificati in modo corretto in base alla specifica del formato di file corrispondente, ad esempio, ISO MP4. È quindi possibile usare il codificatore Standard per codificare il contenuto per lo streaming. Il localizzatore di streaming risultante elenca le lingue disponibili per l'audio.
* Il codificatore Standard supporta ora due nuovi set di impostazioni solo audio, ovvero "Audio AAC" e "Audio AAC di buona qualità". Entrambi producono output in formato Advanced Audio Coding (AAC) stereo, rispettivamente a velocità in bit di 128 kbps e 192 kbps.
* Il codificatore Premium supporta ora i formati di file QuickTime/MOV come input. Il codec video deve essere uno dei [tipi Apple ProRes elencati in questo articolo su GitHub](./media-services-media-encoder-standard-formats.md). L'audio deve avere il formato AAC o modulazione PCM. Il codificatore Premium non supporta, ad esempio, come input il video DVC/DVCPro incapsulato in file QuickTime/MOV. Il codificatore Standard non supporta questi codec video.
* Nei codificatori sono state apportate le correzioni di bug seguenti:

    * È possibile ora inviare processi usando un asset di input. Al termine di questi processi è possibile modificare l'asset, ad esempio aggiungendo, eliminando o rinominando i file all'interno dell'asset, e inviare processi aggiuntivi.
    * La qualità delle anteprime JPEG prodotte dal codificatore Standard è stata migliorata.
    * La gestione dei metadati di input e della generazione di anteprime nei video di durata molto breve è stata migliorata.
    * Miglioramenti apportati al decodificatore H.264 usato nel codificatore Standard allo scopo di eliminare alcuni elementi rari. 

#### <a name="media-analytics"></a>Analisi Servizi multimediali
Disponibilità generale di Azure Media Redactor: questo processore di contenuti multimediali esegue l'anonimizzazione tramite la sfocatura dei visi di persone selezionate ed è ideale per l'uso in scenari multimediali di pubblica sicurezza e notizie. 

Per una panoramica di questo nuovo processore, vedere [questo post di blog](https://azure.microsoft.com/blog/azure-media-redactor/). Per informazioni sulla documentazione e le impostazioni, vedere [Offuscare i volti con Analisi Servizi multimediali di Azure](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Versione di giugno 2017

Servizi multimediali supporta ora l'[autenticazione basata su Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Servizi multimediali supporta attualmente il modello di autenticazione del Servizio di controllo di accesso. L'autorizzazione del Servizio di controllo di accesso verrà dichiarata deprecata il 1° giugno 2018. È consigliabile eseguire al più presto la migrazione al modello di autenticazione di Azure AD.

## <a name="march-2017-release"></a>Versione di marzo 2017

È ora possibile usare il codificatore Standard per [generare automaticamente un bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) specificando la stringa del set di impostazioni "Flusso adattivo" durante la creazione di un'attività di codifica. Per codificare un video per lo streaming con Servizi multimediali, usare il set di impostazioni "Flusso adattivo". Per personalizzare un set di impostazioni di codifica per uno scenario specifico, è possibile iniziare con [questi set di impostazioni](media-services-mes-presets-overview.md).

Ora è possibile usare Media Encoder Standard o Media Encoder Premium Workflow per [creare un'attività di codifica che genera blocchi fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Versione di febbraio 2017

A partire dal 1° aprile 2017, tutti i record di processo presenti nell'account e più vecchi di 90 giorni vengono eliminati automaticamente, insieme ai record attività associati. Questa operazione viene eseguita anche se il numero totale di record è inferiore alla quota massima. Per archiviare le informazioni su processi o attività, è possibile usare il codice descritto in [Manage assets and related entities with the Media Services .NET SDK](media-services-dotnet-manage-entities.md) (Gestire asset ed entità correlate con Media Services .NET SDK).

## <a name="january-2017-release"></a>Versione di gennaio 2017

In Servizi multimediali un endpoint di streaming rappresenta un servizio di streaming in grado di distribuire contenuti direttamente a un'applicazione di lettore client o a una rete per la distribuzione di contenuti (CDN) per la successiva distribuzione. Servizi multimediali fornisce anche un'integrazione completa della rete per la distribuzione dei contenuti di Azure. Il flusso in uscita da un servizio StreamingEndpoint può essere costituito da un flusso live, da un video on demand o da un download progressivo dell'asset associato all'account di Servizi multimediali. Ogni account di Servizi multimediali include un endpoint di streaming predefinito. Nell'account è possibile creare altri endpoint di streaming. 

Esistono due versioni di endpoint di streaming, ovvero 1.0 e 2.0. A partire dal 10 gennaio 2017, qualsiasi account di Servizi multimediali creato di recente include l'endpoint di streaming predefinito versione 2.0. Anche gli altri endpoint di streaming che verranno aggiunti a questo account avranno la versione 2.0. Questa modifica non influisce sugli account esistenti. Gli endpoint di streaming esistenti hanno la versione 1.0 e possono essere aggiornati alla versione 2.0. Questa modifica implica cambiamenti a livello di comportamento, fatturazione e funzionalità. Per altre informazioni, vedere [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Panoramica degli endpoint di streaming).

A partire dalla versione 2.15, in Servizi multimediali sono state aggiunte le proprietà seguenti all'entità endpoint di streaming:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Per altre informazioni su queste proprietà, vedere [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Versione di dicembre 2016

 È ora possibile usare Servizi multimediali per accedere ai dati di telemetria e delle metriche relativi ai servizi. È possibile usare la versione corrente di Servizi multimediali per raccogliere i dati di telemetria relativi alle entità canale live, endpoint di streaming e archivio. Per altre informazioni, vedere [Telemetria di Servizi multimediali](media-services-telemetry-overview.md).

## <a name="july-2016-release"></a><a name="july_changes16"></a>Versione di luglio 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Aggiornamenti del file manifesto (\*.ISM) generato dalle attività di codifica
Quando viene inviata a Media Encoder Standard o a Media Encoder Premium, l'attività di codifica genera un [file manifesto di streaming](media-services-deliver-content-overview.md) (\*.ism) nell'asset di output. Con la versione più recente del servizio è stata aggiornata la sintassi del file manifesto di streaming.

> [!NOTE]
> La sintassi del file manifesto di streaming (con estensione .ism) è riservata per l'uso interno. Sono previste modifiche nelle versioni future. Non modificare o manipolare il contenuto di questo file.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Un nuovo file manifesto client (\*.ISMC) viene generato nell'asset di output quando un'attività di codifica restituisce uno o più file MP4.
A partire dalla versione del servizio più recente, dopo il completamento di un'attività di codifica che genera uno più file MP4, l'asset di output contiene anche un file manifesto client di streaming (\*.ismc). Il file con estensione ismc consente di migliorare le prestazioni del flusso dinamico. 

> [!NOTE]
> La sintassi del file manifesto client (con estensione .ismc) è riservata per l'uso interno. Sono previste modifiche nelle versioni future. Non modificare o manipolare il contenuto di questo file.
> 
> 

Per altre informazioni, vedere [questo blog](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).

### <a name="known-issues"></a>Problemi noti
Alcuni client possono riscontrare un problema di tag di ripetizione nel manifesto Smooth Streaming. Per altre informazioni, vedere [questa sezione](media-services-deliver-content-overview.md#known-issues).

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Versione di aprile 2016
### <a name="media-analytics"></a>Analisi Servizi multimediali
 Servizi multimediali presenta la funzionalità di Analisi Servizi multimediali per una straordinaria esperienza di video intelligence. Per altre informazioni, vedere la [panoramica di Analisi Servizi multimediali](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (anteprima)
È ora possibile usare Servizi multimediali per crittografare dinamicamente i contenuti di HTTP Live Streaming (HLS) usando Apple FairPlay. È possibile anche usare il servizio di distribuzione delle licenze di Servizi multimediali per distribuire le licenze FairPlay ai client. Per altre informazioni, vedere "Use Azure Media Services to stream your HLS content protected with Apple FairPlay" (Usare Servizi multimediali di Azure per trasmettere il contenuto HLS in modo protetto con Apple FairPlay).

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Versione di febbraio 2016
La versione più recente di Media Services SDK per .NET (3.5.3) contiene una correzione di bug relativa a Google Widevine. Non era possibile riusare AssetDeliveryPolicy per più asset crittografati con Widevine. Per correggere il bug è stata anche aggiunta la proprietà seguente all'SDK: WidevineBaseLicenseAcquisitionUrl.

```csharp
Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
{
    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

};
```

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Versione di gennaio 2016
Le unità riservate di codifica sono state rinominate per ridurre la confusione con i nomi di codificatore.

Le unità riservate di codifica Basic, Standard e Premium sono state rinominate rispettivamente come unità riservate S1, S2 e S3. L'etichetta indicata nel portale di Azure e nella fattura per i clienti che usano unità riservate di codifica Basic sarà ora S1. Le etichette indicate per i clienti che usano unità Standard e Premium saranno rispettivamente S2 e S3. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>Versione di dicembre 2015

### <a name="media-encoder-deprecation-announcement"></a>Annuncio di deprecazione per Media Encoder

 Media Encoder verrà deprecato a partire da circa 12 mesi dal rilascio di Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Azure SDK per PHP
Il team di Azure SDK ha pubblicato una nuova versione del pacchetto [Azure SDK per PHP](https://github.com/Azure/azure-sdk-for-php) che contiene aggiornamenti e nuove funzionalità per Servizi multimediali. In particolare, Media Services SDK per PHP supporta ora le funzioni più recenti di [protezione del contenuto](media-services-content-protection-overview.md). Queste funzioni sono la crittografia dinamica con AES e DRM (PlayReady e Widevine) con e senza restrizioni del token. Supporta anche il ridimensionamento delle [unità di codifica](media-services-dotnet-encoding-units.md).

Per altre informazioni, vedere:

* Gli [esempi di codice](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) seguenti consentono di iniziare rapidamente:
  * **vodworkflow_aes.php**: questo file PHP illustra come usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi. Si basa sull'esempio .NET illustrato in [Use AES-128 dynamic encryption and the key delivery service](media-services-protect-with-aes128.md) (Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi).
  * **vodworkflow_aes.php**: questo file PHP illustra come usare la crittografia dinamica PlayReady e il servizio di distribuzione delle licenze. Si basa sull'esempio .NET illustrato in [Usare la crittografia comune dinamica Widevine e/o PlayReady](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: questo file PHP illustra come ridimensionare le unità riservate di codifica.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>Versione di novembre 2015
 Servizi multimediali offre ora il servizio di distribuzione delle licenze Widevine nel cloud. Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Vedere anche [questa esercitazione](media-services-protect-with-playready-widevine.md) e il [repository GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

I servizi di distribuzione delle licenze Widevine forniti da Servizi multimediali sono in anteprima. Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Versione di ottobre 2015
Servizi multimediali di Microsoft Azure è ora disponibile nei seguenti data center: Brasile meridionale, India occidentale, India meridionale e India centrale. È ora possibile usare il portale di Azure per [creare account di Servizi multimediali](media-services-portal-create-account.md) ed eseguire diverse attività descritte nella [pagina Web sulla documentazione di Servizi multimediali](https://azure.microsoft.com/documentation/services/media-services/). In questi data center la codifica live non è abilitata. Inoltre, non sono disponibili tutti i tipi di unità riservate di codifica.

* Brasile meridionale:                                          sono disponibili solo unità riservate di codifica Standard e Basic.
* India occidentale, India meridionale e India centrale:             sono disponibili solo unità riservate di codifica Basic.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Versione di settembre 2015
Servizi multimediali offre ora la possibilità di proteggere i flussi live e video on demand con la tecnologia DRM modulare Widevine. È possibile ricorrere ai partner dei servizi di distribuzione seguenti per facilitare la distribuzione di licenze Widevine:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
È possibile usare [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) a partire dalla versione 3.5.1 o l'API REST per configurare AssetDeliveryConfiguration per l'uso di Widevine. 
* Servizi multimediali ha aggiunto il supporto per i video Apple ProRes. È ora possibile caricare i file video di origine QuickTime che utilizzano Apple ProRes o altri codec. Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* È ora possibile usare Media Encoder Standard per eseguire l'estrazione di clip secondarie e di archivio live. Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Sono stati apportati i seguenti aggiornamenti per il filtro. 
  
  * È ora possibile usare il formato Apple HLS con il filtro per il solo audio. È possibile usare questo aggiornamento per rimuovere la traccia solo audio specificando (audio-only=false) nell'URL.
  * Quando si definiscono i filtri per gli asset, ora è possibile combinare più filtri (fino a 3) in un singolo URL.
    
    Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Servizi multimediali supporta ora gli I-Frame in HLS versione 4. Il supporto per I-Frame consente di ottimizzare le operazioni di avanzamento veloce e riavvolgimento. Per impostazione predefinita, tutti gli output HLS versione 4 includono la playlist I-Frame (EXT-X-I-FRAME-STREAM-INF).
Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Versione di agosto 2015
* Sono ora disponibili la versione 0.8.0 di Media Services SDK per Java e nuovi esempi. Per altre informazioni, vedere:
    
* Azure Media Player è stato aggiornato con il supporto per i flussi multi-audio. Per altre informazioni, vedere [questo post di blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Versione di luglio 2015
* È stata annunciata la disponibilità generale di Media Encoder Standard. Per altre informazioni, vedere [questo post di blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard usa i set di impostazioni, come descritto in [questa sezione](https://go.microsoft.com/fwlink/?LinkId=618336). Quando si usa un set di impostazioni per 4.000 codifiche, acquistare il tipo di unità riservata Premium. Per altre informazioni, vedere l'argomento su come [ridimensionare la codifica](media-services-scale-media-processing-overview.md).
* Sono state usate didascalie live in tempo reale con Servizi multimediali e Media Player. Per altre informazioni, vedere [questo post di blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Aggiornamenti di Media Services .NET SDK
Media Services .NET SDK è ora disponibile nella versione 3.4.0.0. Sono stati eseguiti gli aggiornamenti seguenti: 

* È stato implementato il supporto per l'archivio live. Non è possibile scaricare un asset che contiene un archivio live.
* È stato implementato il supporto per i filtri dinamici.
* La funzionalità è stata implementata per consentire agli utenti di mantenere un contenitore di archiviazione durante l'eliminazione di un asset.
* Sono stati corretti i bug relativi ai criteri per i tentativi nei canali.
* Media Encoder Premium Workflow è stato abilitato.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Versione di giugno 2015
### <a name="media-services-net-sdk-updates"></a>Aggiornamenti di Media Services .NET SDK
Media Services .NET SDK è ora disponibile nella versione 3.3.0.0. Sono stati eseguiti gli aggiornamenti seguenti: 

* È stato aggiunto il supporto per la specifica di individuazione OpenId Connect.
* È stato aggiunto il supporto per la gestione del rollover delle chiavi sul lato del provider di identità.

Se si usa un provider di identità che espone un documento di individuazione OpenID Connect (come avviene con Azure AD, Google e Salesforce), è possibile fare in modo che Servizi multimediali ottenga le chiavi di firma per la convalida dei token JSON Web (JWT) dalla specifica di individuazione OpenID Connect. 

Per altre informazioni, vedere l'argomento relativo all'[uso delle chiavi Web JSON dalla specifica di individuazione OpenID Connect per l'utilizzo con l'autenticazione JWT in Servizi multimediali](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Versione di maggio 2015
Sono state annunciate le nuove funzionalità seguenti:

* [Anteprima della codifica live con Servizi multimediali](media-services-manage-live-encoder-enabled-channels.md)
* [Manifesto dinamico](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Versione di aprile 2015
### <a name="general-media-services-updates"></a>Aggiornamenti generali di Servizi multimediali
* È stata annunciata la disponibilità di [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
* A partire dalla versione 2.10 di REST di Servizi multimediali, i canali configurati per l'inserimento di un protocollo di messaggistica in tempo reale (RTMP) vengono creati con URL di inserimento primari e secondari. Per altre informazioni, vedere [Configurazioni di inserimento del canale](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer è stato aggiornato.
* È stato aggiunto il supporto per la lingua spagnola.
* È stata aggiunta una nuova configurazione per il formato XML.

Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Aggiornamenti di Media Services .NET SDK
Media Services .NET SDK è ora disponibile nella versione 3.2.0.0. Sono stati eseguiti gli aggiornamenti seguenti:

* Modifica che causa un'interruzione: sono stati modificati TokenRestrictionTemplate.Issuer e TokenRestrictionTemplate.Audience in modo che siano di tipo string.
* Sono stati eseguiti aggiornamenti per la creazione di criteri per i tentativi personalizzati.
* Sono stati corretti bug correlati all'upload/download di file.
* La classe MediaServicesCredentials accetta ora endpoint di controllo di accesso primari e secondari per l'autenticazione.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Versione di marzo 2015
### <a name="general-media-services-updates"></a>Aggiornamenti generali di Servizi multimediali
* Servizi multimediali fornisce ora un'integrazione completa della rete per la distribuzione di contenuti. Per supportare l'integrazione, la proprietà CdnEnabled è stata aggiunta a StreamingEndpoint. È possibile usare CdnEnabled con le API REST, a partire dalla versione 2.9. Per altre informazioni, vedere [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). È possibile usare CdnEnabled con .NET SDK, a partire dalla versione 3.1.0.2. Per altre informazioni, vedere [StreamingEndpoint](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).
* È stata annunciata la disponibilità di Media Encoder Premium Workflow. Per altre informazioni, vedere [Introduzione alla codifica Premium in Servizi multimediali di Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Versione di febbraio 2015
### <a name="general-media-services-updates"></a>Aggiornamenti generali di Servizi multimediali
L'API REST di Servizi multimediali è ora disponibile nella versione 2.9. A partire da questa versione, è possibile abilitare l'integrazione della rete per la distribuzione di contenuti con endpoint di streaming. Per altre informazioni, vedere [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Versione di gennaio 2015
### <a name="general-media-services-updates"></a>Aggiornamenti generali di Servizi multimediali
È stata annunciata la disponibilità generale della protezione del contenuto con la crittografia dinamica. Per altre informazioni, vedere la pagina relativa a [Servizi multimediali per il miglioramento della sicurezza di flusso con la versione di disponibilità generale della tecnologia DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Aggiornamenti di Media Services .NET SDK
Media Services .NET SDK è ora disponibile nella versione 3.1.0.1.

In questa versione il costruttore Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate è contrassegnato come obsoleto. Il nuovo costruttore accetta TokenType come argomento.

```csharp
TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
```


## <a name="december-2014-release"></a><a id="december_changes_14"></a>Versione di dicembre 2014
### <a name="general-media-services-updates"></a>Aggiornamenti generali di Servizi multimediali
* Sono stati aggiunti alcuni aggiornamenti e nuove funzionalità a Media Indexer. Per altre informazioni, vedere le [note sulla versione di Azure Media Indexer versione 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* È stata aggiunta una nuova API REST che è possibile usare per aggiornare le unità riservate di codifica. Per altre informazioni, vedere [EncodingReservedUnitType con REST](/rest/api/media/operations/encodingreservedunittype).
* È stato aggiunto il supporto CORS per il servizio di distribuzione delle chiavi.
* Sono stati apportati miglioramenti alle prestazioni delle opzioni per i criteri di autorizzazioni delle query.
* Nel data center in Cina l'[URL di distribuzione delle chiavi](/rest/api/media/operations/contentkey#get_delivery_service_url) viene ora fornito per ogni cliente, come in tutti gli altri data center.
* È stata aggiunta una durata con destinazione automatica HLS. Quando si esegue lo streaming live, la creazione di pacchetti in HLS avviene sempre in modo dinamico. Per impostazione predefinita, Servizi multimediali calcola automaticamente il rapporto per la creazione di pacchetti dei segmenti HLS (FragmentsPerSegment) in base all'intervallo tra fotogrammi chiave (KeyFrameInterval). Questo metodo è noto anche come Group of Pictures (GOP) ricevuti dal codificatore live. Per altre informazioni, vedere come [usare lo streaming live di Servizi multimediali](/previous-versions/azure/dn783466(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a>Aggiornamenti di Media Services .NET SDK
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) è ora disponibile nella versione 3.1.0.0. Sono stati eseguiti gli aggiornamenti seguenti:

* La dipendenza da .NET SDK è stata aggiornata a .NET Framework 4.5.
* È stata aggiunta una nuova API che è possibile usare per aggiornare le unità riservate di codifica. Per altre informazioni, vedere come [aggiornare il tipo di unità riservata e aumentare le unità riservate di codifica usando .NET](media-services-dotnet-encoding-units.md).
* È stato aggiunto il supporto di JWT per l'autenticazione dei token. Per altre informazioni, vedere l'articolo relativo all'[autenticazione di token JWT in Servizi multimediali e alla crittografia dinamica](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Nel modello di licenza PlayReady sono stati aggiunti offset relativi per BeginDate ed ExpirationDate.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>Versione di novembre 2014
* È ora possibile usare Servizi multimediali per inserire contenuti live di Smooth Streaming (fMP4) tramite una connessione TLS. Per l'inserimento tramite TLS, assicurarsi di aggiornare l'URL di inserimento a HTTPS. Attualmente Servizi multimediali non supporta TLS con i domini personalizzati. Per altre informazioni sullo streaming live, vedere [Uso di Live Streaming di Servizi multimediali di Azure](/previous-versions/azure/dn783466(v=azure.100)).
* Attualmente non è possibile inserire uno streaming live RTMP tramite una connessione TLS.
* Lo streaming tramite TLS è possibile solo se l'endpoint di streaming da cui si distribuiscono i contenuti è stato creato dopo il 10 settembre 2014. Se gli URL di streaming si basano sugli endpoint di streaming creati dopo il 10 settembre 2014, l'URL contiene "streaming.mediaservices.windows.net" (il nuovo formato). Gli URL di streaming contenenti "origin.mediaservices.windows.net" (il vecchio formato) non supportano TLS. Se l'URL è nel vecchio formato e si vuole eseguire lo streaming tramite TLS, [creare un nuovo endpoint di streaming](media-services-portal-manage-streaming-endpoints.md). Per lo streaming dei contenuti tramite TLS, usare gli URL basati sul nuovo endpoint di streaming.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>SDK di Servizi multimediali per .NET
Le estensioni di Media Services SDK per .NET sono ora disponibili nella versione 2.0.0.3.

Media Services SDK per .NET è ora disponibile nella versione 3.0.0.8. Sono stati eseguiti gli aggiornamenti seguenti:

* Il refactoring è stato implementato nelle classi dei criteri per i tentativi.
* È stata aggiunta una stringa agente utente alle intestazioni delle richieste HTTP.
* È stato aggiunto un passaggio di compilazione del ripristino NuGet.
* Sono stati corretti i test di scenario per usare il certificato x509 dal repository.
* Sono state aggiunte impostazioni di convalida disponibili al termine dell'aggiornamento del canale e dello streaming.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nuovo repository GitHub per l'hosting di esempi di Servizi multimediali
Gli esempi sono disponibili nel [repository GitHub degli esempi di Servizi multimediali](https://github.com/Azure/Azure-Media-Services-Samples).

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Versione di settembre 2014
I metadati REST di Servizi multimediali sono ora disponibili nella versione 2.7. Per altre informazioni sugli ultimi aggiornamenti di REST, vedere [Informazioni di riferimento sull'API REST di Servizi multimediali](/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services SDK per .NET è ora disponibile nella versione 3.0.0.7.

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Modifiche di rilievo
* Origin è stata rinominata in [StreamingEndpoint].
* È stato modificato il comportamento predefinito quando si usa il portale di Azure per codificare e quindi pubblicare file MP4.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Nuove funzionalità o scenari nella versione di disponibilità generale
* È stato presentato il processore di contenuti multimediali Media Indexer. Per altre informazioni, vedere [Indicizzazione di file multimediali con Media Indexer](/previous-versions/azure/dn783455(v=azure.100)).
* È possibile usare l'entità [StreamingEndpoint] per aggiungere nomi (host) di dominio personalizzati.
  
    Per usare un nome di dominio personalizzato come nome dell'endpoint di streaming di Servizi multimediali, aggiungere nomi host personalizzati all'endpoint di streaming. Per aggiungere nomi host personalizzati, usare le API REST di Servizi multimediali o .NET SDK.
  
    Si applicano le considerazioni seguenti:
  
  * È necessario avere la proprietà del nome di dominio personalizzato.
  * La proprietà del nome di dominio deve essere convalidata da Servizi multimediali. Per convalidare il dominio, creare un CName che esegua il mapping del dominio padre MediaServicesAccountId per verificare il DNS mediaservices-dns-zone.
  * È necessario creare un altro oggetto CName che mappa il nome host personalizzato, ad esempio sports.contoso.com, al nome host StreamingEndpont di Servizi multimediali, ad esempio, amstest.streaming.mediaservices.windows.net.

    Per altre informazioni, vedere la proprietà CustomHostNames nell'articolo [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Nuove funzionalità o scenari nella versione di anteprima pubblica
* Anteprima dello streaming live. Per altre informazioni, vedere come [usare lo streaming live di Servizi multimediali](/previous-versions/azure/dn783466(v=azure.100)).
* Servizio di distribuzione delle chiavi. Per altre informazioni, vedere [Use AES-128 dynamic encryption and the key delivery service](/previous-versions/azure/dn783457(v=azure.100)) (Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi).
* Crittografia dinamica AES. Per altre informazioni, vedere [Use AES-128 dynamic encryption and the key delivery service](/previous-versions/azure/dn783457(v=azure.100)) (Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi).
* Servizio di distribuzione delle licenze PlayReady. 
* Crittografia dinamica PlayReady. 
* Modello di licenza PlayReady di Servizi multimediali. Per altre informazioni, vedere [Panoramica del modello di licenza PlayReady di Servizi multimediali].
* Streaming di asset con contenuto crittografato di archiviazione. Per altre informazioni, vedere [Streaming di contenuto crittografato di archiviazione](/previous-versions/azure/dn783451(v=azure.100)).

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Versione di agosto 2014
Quando si esegue la codifica di un asset, al termine del processo viene restituito un asset di output. Fino a questa versione, il codificatore di Servizi multimediali ha restituito metadati relativi ad asset di output. A partire da questa versione, il codificatore restituisce anche metadati relativi ad asset di input. Per altre informazioni, vedere [Metadati di input] e [Metadati di output].

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Versione di luglio 2014
Le seguenti correzioni di bug sono state introdotte per lo strumento per la creazione di pacchetti e il componente di crittografia di Servizi multimediali di Azure:

* Quando un asset di archivio live viene trasmesso in HLS, viene riprodotto solo l'audio: Questo problema è stato risolto: adesso si possono riprodurre sia audio che video.
* Quando un asset viene incluso nel pacchetto per la busta crittografica con HLS e AES a 128 bit, i flussi nei pacchetti non vengono riprodotti su dispositivi Android: questo bug è stato risolto e i flussi in pacchetti vengono riprodotti in dispositivi Android che supportano HLS.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Versione di maggio 2014
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Aggiornamenti generali di Servizi multimediali
È ora possibile usare la [creazione dinamica dei pacchetti] per lo streaming HLS versione 3. A tale scopo, aggiungere il formato seguente al percorso del localizzatore di origine: * .ism/manifest(format=m3u8-aapl-v3). Per altre informazioni, vedere questo [forum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

La creazione dinamica dei pacchetti supporta attualmente la distribuzione di HLS (versione 3 e versione 4) con crittografia PlayReady basata sulla funzionalità Smooth Streaming crittografata staticamente con PlayReady. Per informazioni su come crittografare Smooth Streaming con PlayReady, vedere come [proteggere Smooth Streaming con PlayReady](/previous-versions/azure/dn189154(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Aggiornamenti di Media Services .NET SDK
Media Services .NET SDK è ora disponibile nella versione 3.0.0.5. Sono stati eseguiti gli aggiornamenti seguenti:

* Sono state migliorate la velocità e la resilienza per l'upload e il download di asset di file multimediali.
* Sono stati apportati miglioramenti alla logica di ripetizione dei tentativi e alla gestione delle eccezioni temporanee: 
  
  * Il rilevamento degli errori temporanei e la gestione della logica di ripetizione dei tentativi sono stati migliorati per le eccezioni generate dall'esecuzione di query, dal salvataggio delle modifiche, dall'upload o dal download di file. 
  * Quando vengono generate eccezioni Web, ad esempio durante una richiesta di token del Servizio di controllo di accesso, il fallimento e la risposta agli errori irreversibili è immediata.

Per altre informazioni, vedere [Logica di ripetizione dei tentativi in Media Services SDK per .NET].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Versioni di gennaio/febbraio 2014
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 e 3.0.0.3
Le modifiche apportate alle versioni 3.0.0.1 e 3.0.0.2 includono:

* Sono stati corretti i problemi correlati all'uso di query LINQ con istruzioni OrderBy.
* Le soluzioni di test in [GitHub] sono state divise in test basati su unità e test basati su scenario.

Per altre informazioni su queste modifiche, vedere: [Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html) (Azure Media Services .NET SDK versioni 3.0.0.1 e 3.0.0.2).

Nella versione 3.0.0.3 sono state apportate le modifiche seguenti:

* Le dipendenze dell'archiviazione di Azure sono state aggiornate per usare la versione 3.0.3.0.
* È stato risolto un problema relativo alla compatibilità con le versioni precedenti per le versioni 3.0. *.* risolto.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>Versione di dicembre 2013
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> Le versioni 3.0.x.x non sono compatibili con le versioni precedenti 2.4.x.x.
> 
> 

L'SDK di Servizi multimediali è ora disponibile nella versione 3.0.0.0. È possibile scaricare il pacchetto più recente da NuGet oppure ottenere i bit da [GitHub].

A partire da Media Services SDK versione 3.0.0.0, è possibile usare nuovamente i token del [Servizio di controllo di accesso di Azure AD](/previous-versions/azure/azure-services/hh147631(v=azure.100)). Per altre informazioni, vedere la sezione relativa al riutilizzo di token del Servizio di controllo di accesso nell'argomento relativo alla [connessione a Servizi multimediali con Media Services SDK per .NET](/previous-versions/azure/jj129571(v=azure.100)) .

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Estensioni 2.0.0.0 di Media Services .NET SDK
 Le estensioni di Media Services .NET SDK sono un set di funzioni di supporto e metodi di estensione che semplificano il codice e lo sviluppo con Servizi multimediali. È possibile ottenere i bit più recenti sul sito Web relativo alle [estensioni di Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a name="november-2013-release"></a><a id="november_changes_13"></a>Versione di novembre 2013
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Modifiche apportate a Media Services .NET SDK
A partire da questa versione, Media Services SDK per .NET gestisce gli errori temporanei che possono verificarsi quando si eseguono chiamate a livello API REST di Servizi multimediali.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Versione di agosto 2013
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Cmdlet di PowerShell per Servizi multimediali inclusi negli strumenti SDK di Azure
I seguenti cmdlet di PowerShell per Servizi multimediali sono stati inclusi negli [strumenti SDK di Azure](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Ad esempio: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Ad esempio: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Ad esempio: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Ad esempio: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Versione di giugno 2013
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Modifiche a Servizi multimediali
Le modifiche seguenti citate in questa sezione sono aggiornamenti inclusi nelle versioni di Servizi multimediali di giugno 2013:

* Possibilità di collegare più account di archiviazione a un account di Servizi multimediali. 
    * StorageAccount
    * Asset.StorageAccountName e Asset.StorageAccount
* Possibilità di aggiornare Job.Priority. 
* Entità e proprietà correlate alle notifiche: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Processo
* Asset.Uri 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Modifiche apportate a Media Services .NET SDK
Le modifiche seguenti sono state apportate alle versioni di Media Services SDK di giugno 2013. Il modulo Media Services SDK più recente è disponibile in GitHub.

* A partire dalla versione 2.3.0.0, Media Services SDK supporta il collegamento di più account di archiviazione a un unico account di Servizi multimediali. Questa funzionalità è supportata dalle seguenti API:
  
    * Tipo IStorageAccount
    * Proprietà Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts
    * Proprietà StorageAccount
    * Proprietà StorageAccountName
  
      Per altre informazioni, vedere [Gestione di asset di Servizi multimediali su più account di archiviazione](/previous-versions/azure/dn271889(v=azure.100)).
* API correlate alle notifiche. A partire dalla versione 2.2.0.0 è possibile ascoltare le notifiche dal servizio Archiviazione code di Azure. Per altre informazioni, vedere [Gestione delle notifiche dei processi di Servizi multimediali](/previous-versions/azure/dn261241(v=azure.100)).
  
    * Proprietà Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions
    * Tipo Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint
    * Tipo Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription
    * Tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection
    * Tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType
* Dipendenza da Storage Client SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Dipendenza da OData 5.5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>Versione di dicembre 2012
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Modifiche apportate a Media Services .NET SDK
* IntelliSense: è stata aggiunta documentazione di IntelliSense mancante per molti tipi.
* Microsoft.Practices.TransientFaultHandling.Core: è stato risolto un problema quando per l'SDK era ancora presente una dipendenza da una versione precedente di questo assembly. L'SDK ora fa riferimento alla versione 5.1.1209.1 dell'assembly.

Risoluzioni di problemi rilevati nell'SDK del novembre 2012:

* IAsset.Locators.Count: questo valore è ora riportato in modo corretto nelle nuove interfacce IAsset dopo l'eliminazione di tutti i localizzatori.
* IAssetFile.ContentFileSize: questo valore viene ora impostato correttamente dopo un caricamento da IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: questa proprietà ora può essere impostata quando si crea un file di asset, mentre in precedenza era di sola lettura.
* IAssetFile.Upload(filepath): è stato risolto un problema quando il metodo di caricamento sincrono generava l'errore seguente durante il caricamento di più file nell'asset. L'errore era: "Autenticazione della richiesta non riuscita nel server. Verificare che il formato dell'intestazione Authorization, firma inclusa, sia corretto".
* IAssetFile.UploadAsync: è stato risolto un problema che limitava l'upload simultaneo a cinque file.
* IAssetFile.UploadProgressChanged: questo evento è ora fornito dall'SDK.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): questo overload del metodo è ora disponibile.
* IAssetFile.DownloadAsync: è stato risolto un problema che limitava il download simultaneo a cinque file.
* IAssetFile.Delete(): è stato risolto un problema quando la chiamata al metodo delete genera un'eccezione se per IAssetFile non è stato caricato alcun file.
* Jobs: è stato risolto un problema quando il concatenamento di un'attività di passaggio da MP4 a flussi digitali uniformi con un'attività di protezione PlayReady tramite un modello di processo non crea alcuna attività.
* EncryptionUtils.GetCertificateFromStore(): questo metodo non genera più un'eccezione con riferimento null a causa di un errore nella ricerca del certificato in base a problemi di configurazione del certificato stesso.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>Versione di novembre 2012
Le modifiche citate in questa sezione sono aggiornamenti inclusi nell'SDK versione 2.0.0.0 di novembre 2012. Queste modifiche possono richiedere la riscrittura o la modifica di un codice scritto per la versione di anteprima di SDK di giugno 2012.

* Asset
  
    * IAsset.Create(assetName) è la *sola* funzione di creazione degli asset. IAsset.Create non carica più file come parte della chiamata al metodo. Usare IAssetFile per il caricamento.
    * Il metodo IAsset.Publish e il valore di enumerazione AssetState.Publish sono stati rimossi da Media Services SDK. Qualsiasi codice che si basa su questo valore deve essere riscritto.
* FileInfo
  
    * Questa classe è stata rimossa e sostituita da IAssetFile.
  
* IAssetFiles
  
    * IAssetFile sostituisce FileInfo e ha un comportamento diverso. Per usarla, creare un'istanza dell'oggetto IAssetFiles e caricare un file tramite Media Services SDK o Storage SDK. È possibile usare uno dei seguenti metodi di overload IAssetFile.Upload:
  
        * IAssetFile.Upload(filePath): questo metodo sincrono blocca il thread ed è consigliabile usarlo solo quando si carica un unico file.
        * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): questo metodo asincrono è il meccanismo di caricamento preferito. 
    
            Bug noto: se si usa il token di annullamento, il caricamento viene annullato. Le attività possono avere più stati di annullamento. È necessario rilevare e gestire le eccezioni in modo appropriato.
* Localizzatori
  
    * Le versioni specifiche dell'origine sono state rimosse. SAS-specific context.Locators.CreateSasLocator(asset, accessPolicy) verrà contrassegnato come deprecato o rimosso dalla fase di disponibilità generale. Vedere la sezione "Localizzatori" in "Nuova funzionalità" per il comportamento aggiornato.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Versione di anteprima di giugno 2012
Le funzionalità seguenti sono state introdotte nella versione di novembre dell'SDK:

* Eliminazione di entità
  
    * Gli oggetti IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey vengono ora eliminati a livello di oggetto, ovvero IObject.Delete(), anziché richiedere un'eliminazione nella raccolta, ovvero cloudMediaContext.ObjCollection.Delete(objInstance).
* Localizzatori
  
    * È ora necessario creare i localizzatori tramite il metodo CreateLocator. I localizzatori devono usare i valori di enumerazione LocatorType.SAS o LocatorType.OnDemandOrigin come argomento per il tipo specifico di localizzatore da creare.
    * Nuove proprietà sono state aggiunte ai localizzatori per semplificare il recupero di URI usabili per il contenuto dell'utente. Questa riprogettazione dei localizzatori fornisce maggiore flessibilità per un'estendibilità futura a terze parti e per aumentare la semplicità d'uso per applicazioni client multimediali.
* Supporto di metodi asincroni
  
    * A tutti i metodi è stato aggiunto il supporto asincrono.

## <a name="additional-notes"></a>Note aggiuntive

* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Microsoft Q&A question page for Azure Media Services]: /answers/topics/azure-media-services.html
[Informazioni di riferimento sull'API REST di Servizi multimediali di Azure]: /rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Metadati di input]: ./media-services-input-metadata-schema.md
[Metadati di output]: ./media-services-output-metadata-schema.md
[Deliver content]: /previous-versions/azure/hh973618(v=azure.100)
[Index media files with the Azure Media Indexer]: /previous-versions/azure/dn783455(v=azure.100)
[StreamingEndpoint]: /rest/api/media/operations/streamingendpoint
[Work with Media Services live streaming]: /previous-versions/azure/dn783466(v=azure.100)
[Use AES-128 dynamic encryption and the key delivery service]: /previous-versions/azure/dn783457(v=azure.100)
[Use PlayReady dynamic encryption and the license delivery service]: /previous-versions/azure/dn783467(v=azure.100)
[Preview features]: https://azure.microsoft.com/services/preview/
[Panoramica del modello di licenza PlayReady di Servizi multimediali]: /previous-versions/azure/dn783459(v=azure.100)
[Stream storage-encrypted content]: /previous-versions/azure/dn783451(v=azure.100)
[Azure portal]: https://portal.azure.com
[Creazione dinamica dei pacchetti]: /previous-versions/azure/jj889436(v=azure.100)
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: /previous-versions/azure/dn189154(v=azure.100)
[Logica di ripetizione dei tentativi in Media Services SDK per .NET]: ./media-services-retry-logic-in-dotnet-sdk.md
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: /previous-versions/azure/dn303341(v=azure.100)
[Create overlays]: /previous-versions/azure/dn640496(v=azure.100)
[Stitch video segments]: /previous-versions/azure/dn640504(v=azure.100)
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: /previous-versions/azure/azure-services/hh147631(v=azure.100)
[Connect to Media Services with the Media Services SDK for .NET]: /previous-versions/azure/jj129571(v=azure.100)
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: /previous-versions/azure/dn271889(v=azure.100)
[Handle Media Services job notifications]: /previous-versions/azure/dn261241(v=azure.100)