---
title: Configurare lo streaming PlayReady offline
description: Questo articolo illustra come configurare l'account di servizi multimediali di Azure V3 per lo streaming di PlayReady per Windows 10 offline.
services: media-services
keywords: DASH, DRM, modalità offline Widevine, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: f720549d0e9edba0865254d6427c19995d70fd8f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069061"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Streaming PlayReady offline per Windows 10 con servizi multimediali V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Servizi multimediali di Azure supporta il download o la riproduzione offline con la protezione DRM. Questo articolo illustra il supporto offline di Servizi multimediali di Azure per i client Windows 10/PlayReady. Per altre informazioni sul supporto della modalità offline per i dispositivi iOS/FairPlay e Android/Widevine, vedere gli articoli seguenti:

- [Streaming FairPlay offline per iOS](drm-offline-fairplay-for-ios-concept.md)
- [Streaming Widevine offline per Android](drm-offline-widevine-for-android.md)

> [!NOTE]
> Il DRM offline viene addebitato solo per l'esecuzione di una singola richiesta di licenza quando si Scarica il contenuto. Eventuali errori non vengono addebitati.

## <a name="background-on-offline-mode-playback"></a>Sfondo della riproduzione in modalità offline

Questa sezione descrive alcuni concetti di base sulla riproduzione in modalità offline, in particolare perché:

* In alcuni paesi o aree geografiche la disponibilità Internet e/o la larghezza di banda è ancora limitata. Gli utenti possono scegliere di scaricare a priori il contenuto per poterlo visualizzare in una risoluzione sufficientemente elevata a garantire un'esperienza di visualizzazione soddisfacente. In questo caso, più spesso, il problema non è la disponibilità della rete, ma una limitazione nella larghezza di banda. I provider OTT/OVP richiedono il supporto della modalità offline.
* Come ha dichiarato Reed Hastings, CEO di Netflix, durante la conferenza degli azionisti dell'azienda del terzo trimestre 2016, il download del contenuto è una "funzionalità molto richiesta", verso la quale "abbiamo un atteggiamento di apertura".
* Alcuni provider di contenuti possono impedire la distribuzione di licenze DRM oltre il bordo di un paese/area geografica. Se un utente vuole consultare il contenuto durante una trasferta all'estero, è necessario il download offline.
 
Il problema che è necessario affrontare nell'implementazione della modalità offline è il seguente:

* Il formato MP4 è supportato da numerosi lettori e strumenti di codifica, ma non è disponibile alcuna associazione tra il contenitore MP4 e DRM.
* A lungo termine, la soluzione sarà CFF con CENC. Tuttavia, l'ecosistema di supporto per strumenti e lettori non è ancora in grado di offrire queste funzionalità. È necessaria una soluzione immediata.
 
L'idea è: il formato di file Smooth Streaming ([PIFF](/iis/media/smooth-streaming/protected-interoperable-file-format)) con H264/AAC ha un'associazione con PlayReady (CTR AES-128). Un singolo file Smooth Streaming con estensione ismv (presupponendo che sia stato eseguito il muxing dell'audio nel video) è esso stesso un file MP4 frammentato (fMP4) e può essere usato per la riproduzione. Se a un contenuto Smooth Streaming viene applicata la crittografia PlayReady, ogni file con estensione ismv diventa un file fMP4 protetto da PlayReady. È possibile scegliere un file con estensione ismv con la velocità in bit preferita e rinominarlo come MP4 per il download.

Sono disponibili due opzioni per l'hosting di file MP4 protetti da PlayReady per il download progressivo:

* Inserire questo file MP4 nello stesso contenitore/asset di Servizi multimediali e sfruttare l'endpoint di streaming di Servizi multimediali di Azure per il download progressivo.
* Usare il localizzatore di firma di accesso condiviso per il download progressivo direttamente da Archiviazione di Azure, ignorando Servizi multimediali di Azure.
 
Sono disponibili due tipi di distribuzioni di licenze PlayReady:

* Servizio di distribuzione di licenze PlayReady in Servizi multimediali di Azure.
* Server licenze PlayReady ospitati in qualsiasi posizione.

Di seguito sono riportati due set di asset di test: il primo usa la distribuzione di licenze PlayReady in Servizi multimediali di Azure, mentre il secondo usa un server licenze PlayReady ospitato in una macchina virtuale di Azure:

## <a name="asset-1"></a>#1 asset

* URL di download progressivo: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* LA_URL di PlayReady (Servizi multimediali di Azure): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

## <a name="asset-2"></a>#2 asset

* URL di download progressivo: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* LA_URL PlayReady (locale): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

Per la riproduzione di test, è stata usata un'applicazione universale di Windows in Windows 10. Negli [esempi di applicazioni universali di Windows 10](https://github.com/Microsoft/Windows-universal-samples) è disponibile un esempio di lettore di base denominato [Adaptive Streaming Sample](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). È sufficiente aggiungere il codice per selezionare il video scaricato e usarlo come origine, invece dell'origine del flusso adattivo. Le modifiche sono nel gestore dell'evento Click del pulsante:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![Riproduzione in modalità offline di un file fMP4 protetto da PlayReady](./media/offline-playready-for-windows/offline-playready1.jpg)

Poiché il video è protetto da PlayReady, lo screenshot non sarà in grado di includere il video.

In sintesi, è stata ottenuta la modalità offline in Servizi multimediali di Azure:

* La transcodifica del contenuto e la crittografia PlayReady possono essere eseguite in Servizi multimediali di Azure o altri strumenti.
* Il contenuto può essere ospitato in Servizi multimediali di Azure o Archiviazione di Azure per il download progressivo.
* La distribuzione delle licenze PlayReady può provenire da Servizi multimediali di Azure o altre posizioni.
* Il contenuto Smooth Streaming preparato può comunque essere usato per lo streaming online tramite DASH o Smooth con PlayReady come DRM.
