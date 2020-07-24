---
title: Proteggere il contenuto HLS con Apple FairPlay offline | Microsoft Docs
description: Questo argomento offre una panoramica su come usare Servizi multimediali di Azure per crittografare dinamicamente il contenuto HTTP Live Streaming (HLS) con Apple FairPlay in modalità offline.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2020
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: eee718627f35154c878c717e02febe0ea7958fe6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87000551"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Modalità offline di FairPlay Streaming per iOS 

> [!div class="op_single_selector" title1="Selezionare la versione di servizi multimediali in uso:"]
> * [Versione 3](../latest/offline-fairplay-for-ios.md)
> * [Versione 2](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](../latest/index.yml). Vedere anche le [indicazioni sulla migrazione dalla versione 2 alla versione 3](../latest/migrate-from-v2-to-v3.md)

Servizi multimediali di Azure include un set di [servizi di protezione del contenuto](https://azure.microsoft.com/services/media-services/content-protection/) ben progettati che riguardano le tecnologie seguenti:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Crittografia AES-128

La crittografia DRM (Digital Rights Management) o AES (Advanced Encryption Standard) del contenuto viene eseguita in modo dinamico su richiesta per vari protocolli di streaming. Servizi multimediali offre anche servizi di distribuzione delle chiavi di licenza DRM o decrittografia AES.

Oltre alla protezione del contenuto per lo streaming online su vari protocolli, anche la modalità offline per il contenuto protetto è una funzionalità molto richiesta. Il supporto della modalità offline è necessaria per gli scenari seguenti:

* Riproduzione del contenuto quando la connessione Internet non è disponibile, ad esempio in viaggio.
* Alcuni provider di contenuti potrebbero non consentire la distribuzione di licenze DRM oltre il bordo di un paese/area geografica. Se gli utenti desiderano guardare il contenuto mentre si recano all'esterno del paese, è necessario scaricare offline.
* In alcuni paesi o aree geografiche la disponibilità Internet e/o la larghezza di banda è ancora limitata. Gli utenti possono quindi scegliere di scaricare prima il contenuto per ottenere una risoluzione sufficientemente elevata a garantire un'esperienza di visualizzazione soddisfacente. In questo caso, il problema in genere non riguarda la disponibilità della rete, ma la larghezza di banda di rete limitata. I provider OTT (Over-The-Top) o OVP (Online Video Platform) richiedono il supporto della modalità offline.

Questo articolo illustra il supporto della modalità offline di FairPlay Streaming (FPS) per dispositivi che eseguono iOS 10 o versioni successive. Questa funzionalità non è supportata per altre piattaforme Apple, ad esempio watchOS, tvOS o Safari su macOS.

## <a name="preliminary-steps"></a>Operazioni preliminari
Prima di implementare la tecnologia DRM offline per FairPlay in un dispositivo iOS 10 o versioni successive:

* Acquisire familiarità con la protezione del contenuto online per FairPlay. Per altre informazioni, vedere gli articoli e gli esempi seguenti:

    - [Apple FairPlay Streaming for Azure Media Services is generally available](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/) (Apple FairPlay Streaming per Servizi multimediali di Azure è disponibile a livello generale)
    - [Proteggere il contenuto HLS con Apple FairPlay o Microsoft PlayReady](./media-services-protect-hls-with-fairplay.md)
    - [A sample for online FPS streaming](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/) (Esempio di streaming FPS online)

* Ottenere FPS SDK da Apple Developer Network. Questo SDK è costituito da due componenti:

    - FPS Server SDK, che contiene il modulo KSM (Key Security Module), esempi di client, una specifica e un set di vettori di test.
    - FPS Deployment Pack, che contiene la specifica della funzione D, le istruzioni su come generare il certificato FPS, la chiave privata specifica del cliente e la chiave privata dell'applicazione. Apple rilascia FPS Deployment Pack ai soli provider di contenuti con licenza.

## <a name="configuration-in-media-services"></a>Configurazione in Servizi multimediali
Per configurare la modalità offline di FPS tramite [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices), usare Media Services .NET SDK versione 4.0.0.4 o successive, che include l'API necessaria per questa configurazione.
È inoltre necessario il codice funzionante per configurare la protezione del contenuto per FPS in modalità online. Dopo avere ottenuto il codice per eseguire questa configurazione, è sufficiente apportare le due modifiche seguenti.

## <a name="code-change-in-the-fairplay-configuration"></a>Modifica del codice nella configurazione di FairPlay
La prima modifica consiste nel definire un valore booleano per abilitare la modalità offline, denominato objDRMSettings.EnableOfflineMode, che è true quando si abilita lo scenario DRM offline. In base a questo indicatore, apportare la modifica seguente alla configurazione di FairPlay:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Modifica del codice nella configurazione dei criteri di distribuzione degli asset
La seconda modifica consiste nell'aggiungere la terza chiave nell'oggetto Dictionary<AssetDeliveryPolicyConfigurationKey, string>.
Aggiungere AssetDeliveryPolicyConfigurationKey come illustrato di seguito:
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Dopo questo passaggio, la stringa <Dictionary_AssetDeliveryPolicyConfigurationKey> nei criteri di distribuzione degli asset di FPS contiene le tre voci seguenti:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl o AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, a seconda di vari fattori come il server delle chiavi o KSM usato per FPS e l'intenzione di riutilizzare gli stessi criteri di distribuzione per più asset
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

L'account di Servizi multimediali è ora configurato per la distribuzione di licenze FairPlay offline.

## <a name="sample-ios-player"></a>Esempio di lettore iOS
Il supporto della modalità offline di FPS è disponibile solo in iOS 10 e versioni successive. FPS Server SDK (versione 3.0 o successive) contiene il documento di istruzioni e un esempio per la modalità offline di FPS. In particolare, FPS Server SDK (versione 3.0 o successive) contiene i due elementi seguenti correlati alla modalità offline:

* Documento: "Offline Playback with FairPlay Streaming and HTTP Live Streaming" (Riproduzione offline con FairPlay Streaming e HTTP Live Streaming). Apple, 14 settembre 2016. In FPS Server SDK versione 4.0 questo documento è stato unito al documento principale di FPS.
* Codice di esempio: esempio HLSCatalog per la modalità offline di FPS in \FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. Nell'app HLSCatalog di esempio, per implementare le funzionalità della modalità offline vengono usati i file di codice seguenti:

    - File di codice AssetPersistenceManager.swift: AssetPersistenceManager è la classe principale in questo esempio che illustra quanto segue:

        - Come gestire il download dei flussi HLS, ad esempio le API usate per avviare e annullare i download e per eliminare asset esistenti dai dispositivi.
        - Come monitorare lo stato di avanzamento del download.
    - File di codice AssetListTableViewController.swift e AssetListTableViewCell.swift: AssetListTableViewController è l'interfaccia principale di questo esempio. Fornisce un elenco degli asset che l'esempio può usare per eseguire operazioni di riproduzione, download o eliminazione o per annullare un download. 

Questi passaggi illustrano come configurare un lettore iOS in esecuzione. Se si inizia dall'esempio HLSCatalog in FPS Server SDK versione 4.0.1, apportare le modifiche seguenti al codice:

In HLSCatalog\Shared\Managers\ContentKeyDelegate.swift implementare il metodo `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` usando il codice seguente. "drmUrl" deve essere una variabile assegnata all'URL di HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

In HLSCatalog\Shared\Managers\ContentKeyDelegate.swift implementare il metodo `requestApplicationCertificate()`. Questa implementazione varia a seconda del fatto che si incorpori o meno il certificato (solo chiave pubblica) nel dispositivo o si ospiti il certificato sul Web. L'implementazione seguente usa il certificato dell'applicazione ospitato degli esempi di test. "certUrl" deve essere una variabile che contiene l'URL del certificato dell'applicazione.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Per il test integrato finale, l'URL del video e l'URL del certificato dell'applicazione sono entrambi riportati nella sezione "Test integrato".

In HLSCatalog\Shared\Resources\Streams.plist aggiungere l'URL del video di test. Per l'ID della chiave simmetrica, usare l'URL di acquisizione delle licenze FairPlay con il protocollo skd come valore univoco.

![Flussi dell'app iOS FairPlay offline](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Per l'URL del video di test, l'URL di acquisizione delle licenze FairPlay e l'URL del certificato dell'applicazione, usare valori personalizzati, se configurati. In alternativa, è possibile continuare con la sezione successiva, che contiene esempi di test.

## <a name="integrated-test"></a>Test integrato
Per Servizi multimediali sono disponibili tre test di esempio che riguardano questi tre scenari:

* FPS protetto, con video, audio e traccia audio alternativa
* FPS protetto, con video e audio, ma senza traccia audio alternativa
* FPS protetto, solo con video, senza audio

È possibile trovare gli esempi in [questo sito di demo](https://aka.ms/poc#22), con il certificato dell'applicazione corrispondente ospitato in un'app Web di Azure.
Con l'esempio versione 3 o 4 di FPS Server SDK, se una playlist master contiene una traccia audio alternativa, durante la modalità offline viene riprodotto solo l'audio. È pertanto necessario rimuovere la traccia audio alternativa. In altre parole, il secondo e il terzo esempio elencati in precedenza funzionano in modalità online e offline. L'esempio elencato per primo riproduce solo l'audio durante la modalità offline, mentre lo streaming online funziona correttamente.

## <a name="faq"></a>Domande frequenti
Questa sezione include le risposte ad alcune domande frequenti utili per la risoluzione dei problemi:

- **Perché in modalità offline viene riprodotto solo l'audio senza video?** Questo comportamento sembra essere predefinito nell'app di esempio. Quando è presente una traccia audio alternativa (come nel caso di HLS) durante la modalità offline, l'impostazione predefinita di iOS 10 e iOS 11 è la traccia audio alternativa. Per compensare questo comportamento per la modalità offline di FPS, rimuovere la traccia audio alternativa dal flusso. Per eseguire questa operazione in Servizi multimediali, aggiungere il filtro di manifesto dinamico "audio-only=false". In altre parole, un URL HLS termina con .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Perché, anche dopo aver aggiunto audio-only=false, in modalità offline continua a essere riprodotto solo l'audio senza il video?** A seconda del modo in cui è progettata la chiave di cache della rete CDN, è possibile che il contenuto sia memorizzato nella cache. Ripulire la cache.
- **La modalità offline di FPS è anche supportata in iOS 11 oltre che in iOS 10?** Sì. La modalità offline di FPS è supportata per iOS 10 e iOS 11.
- **Il documento "Offline Playback with FairPlay Streaming and HTTP Live Streaming" non è incluso in FPS Server SDK. Perché?** A partire dalla versione 4 di FPS Server SDK, questo documento è stato incluso in "FairPlay Streaming Programming Guide" (Guida alla programmazione di FairPlay Streaming).
- **Qual è lo stato dell'ultimo parametro nell'API seguente per la modalità offline di FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Per informazioni su questa API, vedere [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration Method](/dotnet/api/microsoft.windowsazure.mediaservices.client.fairplay.fairplayconfiguration.createserializedfairplayoptionconfiguration?view=azure-dotnet) (Metodo FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration). Il parametro rappresenta la durata del noleggio offline, con il secondo come unità.
- **Qual è la struttura del file scaricato/offline nei dispositivi iOS?** La struttura del file scaricato in un dispositivo iOS è simile a quella illustrata nello screenshot seguente. La cartella `_keys` archivia le licenze FPS scaricate, con un singolo file di archivio per ogni host del servizio licenze. La cartella `.movpkg` archivia il contenuto audio e video. La prima cartella con il nome che termina con un trattino seguito da un valore numerico include contenuto video. Il valore numerico è il valore PeakBandwidth del rendering del video. La seconda cartella con il nome che termina con un trattino seguito da 0 include contenuto audio. La terza cartella denominata "Data" contiene la playlist master del contenuto FPS. Infine, boot.xml fornisce una descrizione completa del contenuto della cartella `.movpkg`. 

![Struttura del file dell'app di esempio iOS di FairPlay offline](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Un file boot.xml di esempio:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="additional-notes"></a>Note aggiuntive

* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="summary"></a>Riepilogo
Questo documento include le procedure e le informazioni seguenti che è possibile usare per implementare la modalità offline di FPS:

* La procedura di configurazione della protezione del contenuto in Servizi multimediali tramite l'API .NET di Servizi multimediali consente di configurare la crittografia dinamica FairPlay e la distribuzione delle licenze FairPlay in Servizi multimediali.
* In base all'esempio fornito in FTP Server SDK viene configurato un lettore iOS in grado di riprodurre contenuto FPS in modalità di streaming online o in modalità offline.
* Per il test della modalità offline e dello streaming online vengono usati video FPS di esempio.
* La sezione delle domande frequenti include le soluzioni ai problemi relativi alla modalità offline di FPS.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
