---
title: Crittografia DRM e distribuzione delle licenze di servizi multimediali
description: Informazioni su come usare il servizio di crittografia dinamica e di distribuzione delle licenze DRM per distribuire flussi crittografati con licenze di Microsoft PlayReady, Google Widevine o Apple FairPlay.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: af6933ecadb4d008ad45c284f5182158dda68877
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211087"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Esercitazione: Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Anche se l'esercitazione usa esempi di [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet), le procedure generali sono identiche per l'[API REST](/rest/api/media/liveevents), l'[interfaccia della riga di comando](/cli/azure/ams/live-event) o altri [SDK](media-services-apis-overview.md#sdks) supportati.

È possibile usare Servizi multimediali di Azure per distribuire i flussi crittografati con licenze Microsoft PlayReady, Google Widevine o Apple FairPlay. Per una spiegazione approfondita, vedere [Protezione del contenuto con la crittografia dinamica](content-protection-overview.md).

Servizi multimediali offre anche un servizio per la distribuzione di licenze DRM PlayReady, Widevine e FairPlay. Quando un utente richiede contenuto protetto tramite DRM, l'app lettore richiede una licenza dal servizio licenze di Servizi multimediali. Se l'app lettore viene autorizzata, il servizio licenze di Servizi multimediali rilascia una licenza per il lettore. Una licenza contiene la chiave di decrittografia che può essere usata dal lettore client per decrittografare e trasmettere il contenuto.

Questo articolo si basa sull'esempio [Encrypting with DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) (Crittografia con DRM).

L'esempio descritto in questo articolo genera il risultato seguente:

![AMS con video protetto con DRM in Azure Media Player](./media/protect-with-drm/ams_player.png)

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare una trasformazione di codifica.
> * Impostare la chiave di firma usata per la verifica del token.
> * Impostare i requisiti per i criteri di chiave simmetrica.
> * Creare uno StreamingLocator con i criteri di streaming specificati.
> * Creare un URL usato per la riproduzione del file.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione sono necessari gli elementi seguenti:

* Vedere l'articolo [Panoramica della protezione del contenuto](content-protection-overview.md).
* Fare riferimento a [Progettare un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](design-multi-drm-system-with-access-control.md).
* Installare Visual Studio Code o Visual Studio.
* Creare un nuovo account di Servizi multimediali in Azure, come descritto in [questa guida introduttiva](./create-account-howto.md).
* Ottenere le credenziali necessarie per usare le API di Servizi multimediali seguendo [Accedere alle API](./access-api-howto.md)
* Impostare i valori appropriati nel file di configurazione dell'app (appsettings.json).

## <a name="download-code"></a>Scaricare il codice

Clonare nel computer un repository GitHub contenente l'esempio .NET completo trattato in questo articolo usando il comando seguente:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
L'esempio "Encrypt with DRM" (Crittografia con DRM) si trova nella cartella [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> L'esempio crea risorse univoche ogni volta che si esegue l'app. In genere si riutilizzano le risorse esistenti, ad esempio criteri e trasformazioni, se hanno le configurazioni richieste.

## <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice clonato all'inizio dell'articolo, la funzione **GetCredentialsAsync** crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Creare un asset di output  

L'[asset](assets-concept.md) di output archivia il risultato del processo di codifica.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Ottenere o creare una trasformazione di codifica

Quando si crea una nuova istanza dell'oggetto [Transform](transforms-jobs-concept.md), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto `transformOutput`, come illustrato nel codice seguente. Ogni TransformOutput contiene un parametro **Preset**. In Preset sono descritte le istruzioni dettagliate delle operazioni di elaborazione di contenuti video e/o audio che devono essere usate per generare l'oggetto TransformOutput desiderato. L'esempio descritto in questo articolo è basato su un set di impostazioni predefinito denominato **AdaptiveStreaming**. Il set di impostazioni codifica il video di input in una tabella di coppie velocità in bit-risoluzione generata automaticamente in base alla risoluzione e alla velocità in bit dell'input e genera file ISO MP4 con standard video H.264 e audio AAC corrispondente a ogni coppia velocità in bit-risoluzione. 

Prima di creare un nuovo oggetto **Transform**, è necessario verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente.  In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Inviare il processo

Come indicato sopra, l'oggetto **Transform** è la serie di istruzioni, mentre l'oggetto [Job](transforms-jobs-concept.md) è la richiesta effettiva inviata a Servizi multimediali per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. Il **processo** specifica informazioni come la posizione del video di input e quella dell'output.

In questa esercitazione si crea l'input del processo in base a un file che viene inserito direttamente da un [URL di origine HTTPS](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Attendere il completamento del processo

Il completamento del processo richiede qualche istante. Al termine dell'operazione, si vorrà ricevere una notifica. L'esempio di codice seguente illustra come eseguire il polling del servizio per determinare lo stato del **processo**. Il polling non è una procedura consigliata per le app di produzione a causa dei rischi di latenza. Il polling può essere limitato se usato eccessivamente su un account. In alternativa, è preferibile che gli sviluppatori usino Griglia di eventi. Vedere [Instradare gli eventi verso un endpoint Web personalizzato](job-state-events-cli-how-to.md).

L'oggetto **Job** assume progressivamente gli stati seguenti: **Scheduled**, **Queued**, **Processing**, **Finished** (stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Errore**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Annullamento in corso** e, al termine, lo stato **Annullato**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Creare i criteri chiave simmetrica

Una chiave simmetrica consente l'accesso sicuro agli asset. È necessario creare [criteri di chiave simmetrica](content-key-policy-concept.md) durante la crittografia del contenuto con DRM. I criteri configurano la modalità di distribuzione della chiave simmetrica ai client finali. La chiave simmetrica è associata a un localizzatore di streaming. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia e licenze agli utenti autorizzati.

È necessario impostare i requisiti (restrizioni) nei **criteri di chiave simmetrica** che devono essere soddisfatti per la distribuzione delle chiavi con la configurazione specificata. In questo esempio vengono impostate le configurazioni e i requisiti seguenti:

* Configurazione

    Le licenze [PlayReady](playready-license-template-overview.md) e [Widevine](widevine-license-template-overview.md) sono configurate in modo che possano essere distribuite dal servizio di distribuzione delle licenze di Servizi multimediali. Anche se questa app di esempio non configura la licenza [FairPlay](fairplay-license-overview.md), contiene un metodo che è possibile usare per configurare FairPlay. È possibile aggiungere la configurazione di FairPlay come un'altra opzione.

* Restrizione

    L'app imposta una restrizione di tipo token JWT (JSON Web Token) nei criteri.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di chiave simmetrica specificati per tale chiave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Creare un localizzatore di streaming

Al termine della codifica e dopo l'impostazione dei criteri di chiave simmetrica, il passaggio successivo consiste nel rendere disponibile ai client il video nell'asset di output per la riproduzione. Il video viene reso disponibile in due passaggi:

1. Creare un [localizzatore di streaming](streaming-locators-concept.md).
2. Creare gli URL di streaming che i client possono usare.

Il processo di creazione di un **localizzatore di streaming** è detto pubblicazione. Per impostazione predefinita, il **localizzatore di streaming** diventa valido subito dopo aver effettuato le chiamate API. Rimane valido fino all'eliminazione, a meno che non si configurino le ore di inizio e di fine facoltative.

Quando si crea un **localizzatore di streaming**, è necessario specificare il parametro `StreamingPolicyName` desiderato. In questa esercitazione si userà uno dei criteri di streaming predefiniti per indicare a Servizi multimediali di Azure come pubblicare il contenuto per lo streaming. In questo esempio, StreamingLocator.StreamingPolicyName verrà impostato sul criterio "Predefined_MultiDrmCencStreaming". Viene quindi applicata la crittografia PlayReady e Widevine e la chiave viene distribuita al client di riproduzione in base alle licenze DRM configurate. Se si vuole anche crittografare il flusso con CBCS (FairPlay), usare "Predefined_MultiDrmStreaming".

> [!IMPORTANT]
> Se si usano [criteri di streaming](streaming-policy-concept.md) personalizzati, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming ogni volta che si devono usare gli stessi protocolli e le stesse opzioni di crittografia. L'account di Servizi multimediali prevede una quota per il numero di occorrenze di StreamingPolicy. Evitare quindi di creare un nuovo oggetto StreamingPolicy per ogni StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Ottenere un token di test

In questa esercitazione si specifica una restrizione del token per i criteri di chiave simmetrica. I criteri con restrizione del token richiedono la presenza di un token rilasciato da un servizio token di sicurezza. Servizi multimediali supporta i token nei formati [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) e saranno configurati in questo modo nell'esempio.

In ContentKeyPolicy viene usato ContentKeyIdentifierClaim, il che significa che il token presentato al servizio di distribuzione delle chiavi deve contenere l'identificatore dell'entità ContentKey. Nell'esempio non si specifica una chiave simmetrica quando si crea il localizzatore di streaming, ma il sistema crea un valore casuale. Per generare il token di test, è necessario ottenere il ContentKeyId da inserire nell'attestazione ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Creare un URL di streaming

Ora che è stato creato l'oggetto [StreamingLocator](/rest/api/media/streaminglocators), è possibile ottenere gli URL di streaming. Per creare un URL, è necessario concatenare il nome host [StreamingEndpoint](/rest/api/media/streamingendpoints) e il percorso del **localizzatore di streaming**. In questo esempio viene usato l'*endpoint di streaming* **predefinito**. Quando si crea un account di Servizi multimediali per la prima volta, l'*endpoint di streaming* **predefinito** è in stato arrestato ed è quindi necessario chiamare **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Quando si esegue l'app, viene visualizzata la schermata seguente:

![Proteggere con DRM](./media/protect-with-drm/playready_encrypted_url.png)

È possibile aprire un browser e incollare l'URL risultante per avviare la pagina della demo di Azure Media Player con l'URL e il token già inseriti.

## <a name="clean-up-resources-in-your-media-services-account"></a>Pulire le risorse nell'account di Servizi multimediali

Normalmente è necessario pulire tutti gli oggetti tranne quelli che si prevede di riutilizzare. In genere si riutilizzano le trasformazioni, i localizzatori di streaming e così via. Se dopo l'attività di sperimentazione si vuole pulire l'account, eliminare le risorse che non si prevede di riutilizzare. Ad esempio, il codice seguente elimina il processo, gli asset creati e i criteri di chiave simmetrica:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza.

Eseguire il comando dell'interfaccia della riga di comando seguente:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Note aggiuntive

* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

Estrai

> [!div class="nextstepaction"]
> [Proteggere con AES-128](protect-with-aes128.md)
