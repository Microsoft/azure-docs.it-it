---
title: Proteggere i contenuti con Servizi multimediali di Azure | Documentazione Microsoft
description: Questo articolo offre una panoramica della protezione del contenuto con Servizi multimediali di Azure 2.This article gives an overview of content protection with Azure Media Services v2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 88e0e1c18722fd86e79fc1fa7722b59b3cb8966a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460960"
---
# <a name="content-protection-overview"></a>Panoramica della protezione del contenuto 

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. <br/>Scopri la versione più recente, [Servizi multimediali v3](https://docs.microsoft.com/azure/media-services/latest/). Vedere anche le linee guida per la [migrazione dalla v2 alla v3](../latest/migrate-from-v2-to-v3.md)

È possibile usare Servizi multimediali di Azure per proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. 

L'immagine seguente illustra il flusso di lavoro di protezione dei contenuti di Servizi multimediali: 

![Protezione con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Questo articolo usa una terminologia e illustra concetti importanti per comprendere la protezione dei contenuti con Servizi multimediali. L'articolo include anche collegamenti ad articoli che descrivono come proteggere i contenuti. 

## <a name="dynamic-encryption"></a>Crittografia dinamica

È possibile usare Servizi multimediali per distribuire i contenuti crittografati dinamicamente con chiave non crittografata AES o con crittografia DRM mediante PlayReady, Widevine o FairPlay. Se il contenuto è crittografato con una chiave non crittografata AES e viene inviato tramite HTTPS, non è chiaro fino a quando non raggiunge il client. 

Ogni metodo di crittografia supporta i protocolli di streaming seguenti:
 
- AES: MPEG-DASH, Smooth Streaming e HLS
- PlayReady: MPEG-DASH, Smooth Streaming e HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

La crittografia sui download progressivi non è supportata. 

Per crittografare un asset è necessario associare una chiave simmetrica di crittografia all'asset e configurare anche i criteri di autorizzazione per la chiave. Le chiavi simmetriche possono essere specificate o generate automaticamente da Servizi multimediali.

È anche necessario configurare i criteri di distribuzione degli asset. Se si vuole trasmettere in streaming un asset con contenuto crittografato di archiviazione, assicurarsi di specificare come si intende recapitarlo configurando i criteri di distribuzione dell'asset.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante la chiave non crittografata AES o la crittografia DRM. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi di Servizi multimediali. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati.

## <a name="aes-128-clear-key-vs-drm"></a>Chiave chiara AES-128 rispetto a DRM
I clienti spesso si chiedono se devono usare la crittografia AES o un sistema DRM. La differenza principale tra i due sistemi è che con la crittografia AES la chiave simmetrica viene trasmessa al client in un formato non crittografato ("in chiaro"). Di conseguenza, è possibile visualizzare la chiave usata per crittografare i contenuti in una traccia di rete sul client in testo non crittografato. La crittografia con chiave non crittografata AES-128 è adatta nei casi in cui il visualizzatore è attendibile (ad esempio, la crittografia di video aziendali distribuiti all'interno di un'azienda per essere visualizzati dai dipendenti).

PlayReady, Widevine e FairPlay offrono tutti un livello superiore di crittografia rispetto alla crittografia della chiave non crittografata AES-128. La chiave simmetrica viene trasmessa in un formato crittografato. Inoltre, la decrittografia viene gestita in un ambiente sicuro a livello di sistema operativo, più difficilmente attaccabile da un utente malintenzionato. Il DRM è consigliato per i casi d'uso in cui il visualizzatore potrebbe non essere attendibile ed è necessario il massimo livello di sicurezza.

## <a name="storage-encryption"></a>Crittografia di archiviazione
È possibile usare la crittografia di archiviazione per crittografare i contenuti localmente tramite crittografia AES a 256 bit, quindi caricarli nel servizio Archiviazione di Azure dove vengono archiviati in forma crittografata. Gli asset protetti con la crittografia di archiviazione vengono decrittografati automaticamente e inseriti in un file system crittografato prima della codifica, quindi ricrittografati facoltativamente prima di essere ricaricati come nuovo asset di output. La crittografia di archiviazione viene usata principalmente quando si vogliono proteggere i file multimediali con input di alta qualità con una crittografia avanzata sul disco locale.

Per distribuire un asset con contenuto crittografato di archiviazione, è necessario configurare i criteri di distribuzione dell'asset in modo da informare Servizi multimediali della modalità di distribuzione del contenuto. Per permettere lo streaming dell'asset, il server di streaming rimuove la crittografia ed esegue lo streaming dei contenuti usando i criteri di recapito specificati (ad esempio AES, crittografia comune o nessuna crittografia).

## <a name="types-of-encryption"></a>Tipi di crittografia
PlayReady e Widevine usano la crittografia comune (modalità CTR AES). FairPlay usa la crittografia in modalità CBC AES. La crittografia con chiave non crittografata AES-128 usa la crittografia della busta.

## <a name="licenses-and-keys-delivery-service"></a>Servizio di distribuzione di licenze e chiavi
Servizi multimediali offre un servizio di distribuzione delle chiavi per la distribuzione di licenze DRM (PlayReady, Widevine, FairPlay) e di chiavi AES ai client autorizzati. È possibile usare il [portale di Azure](media-services-portal-protect-content.md), l'API REST o Media Services SDK per .NET per configurare i criteri di autorizzazione e autenticazione per le licenze e le chiavi.

## <a name="control-content-access"></a>Controllare l'accesso ai contenuti
È possibile controllare chi ha accesso ai propri contenuti configurando i criteri di autorizzazione delle chiavi simmetriche. I criteri di autorizzazione delle chiavi simmetriche possono prevedere una limitazione aperta o relativa al token.

### <a name="open-authorization"></a>Autorizzazione aperta
Con i criteri di autorizzazione aperti la chiave simmetrica viene inviata a qualsiasi client (nessuna limitazione).

### <a name="token-authorization"></a>Autorizzazione con token
Con i criteri di autorizzazione con restrizione del token, la chiave simmetrica viene inviata solamente a un client che fornisca un token JSON Web (JWT) o un token Web semplice (SWT) valido nella richiesta di licenza/chiave. Questo token deve essere emesso da un servizio token di sicurezza. È possibile usare Azure Active Directory come servizio token di sicurezza oppure distribuire un servizio token di sicurezza personalizzato. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione delle chiavi di Servizi multimediali restituisce al client la chiave o la licenza richiesta se il token è valido e le attestazioni del token corrispondono a quelle configurate per la chiave o la licenza.

Quando si configurano i criteri di restrizione del token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che rilascia il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

### <a name="token-replay-prevention"></a>Prevenzione della riproduzione dei token

La funzionalità *Di prevenzione* della riproduzione di token consente ai clienti di Servizi multimediali di impostare un limite sul numero di volte in cui lo stesso token può essere utilizzato per richiedere una chiave o una licenza. Il cliente può aggiungere `urn:microsoft:azure:mediaservices:maxuses` un'attestazione di tipo nel token, dove il valore è il numero di volte in cui il token può essere utilizzato per acquisire una licenza o una chiave. Tutte le richieste successive con lo stesso token a Key Delivery restituiranno una risposta non autorizzata. Vedere come aggiungere l'attestazione [nell'esempio DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerazioni

* I clienti devono avere il controllo sulla generazione di token. L'attestazione deve essere inserita nel token stesso.
* Quando si utilizza questa funzionalità, le richieste con token il cui tempo di scadenza è a più di un'ora dal momento in cui la richiesta viene ricevuta vengono rifiutate con una risposta non autorizzata.
* I token sono identificati in modo univoco dalla loro firma. Qualsiasi modifica al payload (ad esempio, l'aggiornamento al tempo di scadenza o all'attestazione) modifica la firma del token e verrà conteggiata come un nuovo token che la consegna delle chiavi non ha mai incontrato prima.
* La riproduzione non riesce `maxuses` se il token ha superato il valore impostato dal cliente.
* Questa funzionalità può essere utilizzata per tutto il contenuto protetto esistente (solo il token rilasciato deve essere modificato).
* Questa funzione funziona sia con JWT che con SWT.

## <a name="streaming-urls"></a>URL di streaming
Se l'asset è stato crittografato con più sistemi DRM, usare un tag di crittografia nell'URL di streaming (format='m3u8-aapl', encryption='xxx').

Si applicano le considerazioni seguenti:

* Può essere specificato solo un tipo di crittografia.
* Il tipo di crittografia non deve essere specificato nell'URL se all'asset è stata applicata una sola crittografia.
* Il tipo di crittografia non fa distinzione tra maiuscole e minuscole.
* Possono essere specificati i seguenti tipi di crittografia:

  * **cenc**: per PlayReady o Widevine (crittografia comune)
  * **cbcs-aapl**: per FairPlay (crittografia CBC AES)
  * **cbc**: per la crittografia della busta AES

## <a name="additional-notes"></a>Note aggiuntive

* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="next-steps"></a>Passaggi successivi
Gli articoli seguenti descrivono i passaggi successivi da eseguire per iniziare a usare la protezione dei contenuti:

* [Proteggere i contenuti con la crittografia di archiviazione](media-services-rest-storage-encryption.md)
* [Proteggere i contenuti con la crittografia AES](media-services-protect-with-aes128.md)
* [Proteggi con PlayReady e/o Widevine](media-services-protect-with-playready-widevine.md)
* [Proteggere con FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Collegamenti correlati

* [Autenticazione tramite token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrare l'app basata su OWIN MVC di Servizi multimediali di Azure con Azure Active Directory e limitare la distribuzione di chiavi simmetriche in base ad attestazioni del token JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
