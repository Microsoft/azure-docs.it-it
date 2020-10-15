---
title: Eseguire lo streaming live con Servizi multimediali v3
titleSuffix: Azure Media Services
description: Informazioni su come eseguire lo streaming live con Servizi multimediali di Azure v3.
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
ms.custom: mvc, devx-track-csharp
ms.date: 06/13/2019
ms.author: inhenkel
ms.openlocfilehash: b2e456474a9d052d9515c8169ce233e9577a5c53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89256566"
---
# <a name="tutorial-stream-live-with-media-services"></a>Esercitazione: Eseguire lo streaming live con Servizi multimediali

> [!NOTE]
> Anche se l'esercitazione usa esempi di [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet), i passaggi generali sono gli stessi per [API REST](/rest/api/media/liveevents), [Interfaccia della riga di comando](/cli/azure/ams/live-event?view=azure-cli-latest) o altri [SDK](media-services-apis-overview.md#sdks) supportati.

In Servizi multimediali di Azure le entità [Eventi live](/rest/api/media/liveevents) sono responsabili dell'elaborazione dei contenuti in streaming live. Un'entità evento live fornisce un endpoint di input (URL di inserimento) che può essere a sua volta fornito al codificatore live. L'entità evento live riceve flussi di input live dal codificatore live e li rende disponibili per lo streaming con uno o più [Endpoint di streaming](/rest/api/media/streamingendpoints). Gli eventi live forniscono anche un endpoint di anteprima (URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito. Questa esercitazione illustra come usare .NET Core per creare un evento live di tipo **pass-through**.

L'esercitazione illustra come:

> [!div class="checklist"]
> * Scaricare l'app di esempio descritta nell'argomento.
> * Esaminare il codice che esegue lo streaming live.
> * Osservare l'evento con [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) all'indirizzo [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net).
> * Pulire le risorse.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione sono necessari gli elementi seguenti:

- Installare Visual Studio Code o Visual Studio.
- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md).<br/>Assicurarsi di ricordare i valori usati per il nome del gruppo di risorse e il nome dell'account Servizi multimediali.
- Seguire la procedura descritta in [Accedere all'API di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure](./access-api-howto.md) e salvare le credenziali. Sarà necessario usarle per accedere all'API.
- Una fotocamera o un dispositivo (ad esempio un portatile) usato per trasmettere un evento.
- Un codificatore live locale in grado di convertire i segnali provenienti dalla fotocamera in flussi inviati al servizio di streaming live di Servizi multimediali. Vedere [Codificatori live locali consigliati](recommended-on-premises-live-encoders.md). Il flusso deve essere in formato **RTMP** oppure **Smooth Streaming**.

> [!TIP]
> Assicurarsi di leggere [Live streaming with Azure Media Services v3](live-streaming-overview.md) (Streaming live con Servizi multimediali di Azure v3) prima di procedere. 

## <a name="download-and-configure-the-sample"></a>Scaricare e configurare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET di streaming usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

L'esempio di streaming live è disponibile nella cartella [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

Aprire il file [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) nel progetto scaricato. Sostituire i valori con le credenziali ottenute durante l'[accesso alle API](./access-api-howto.md).

> [!IMPORTANT]
> Questo esempio usa un suffisso univoco per ogni risorsa. Se si annulla il debug o si termina l'app senza eseguirla per intero, per l'account risulteranno disponibili più eventi live. <br/>Assicurarsi di arrestare gli eventi live in esecuzione. In caso contrario, verranno **fatturati**.

## <a name="examine-the-code-that-performs-live-streaming"></a>Esaminare il codice che esegue lo streaming live

Questa sezione esamina le funzioni definite nel file [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) del progetto *MediaV3LiveApp*.

L'esempio crea un suffisso univoco per ogni risorsa in modo che non si verifichino conflitti di nomi se si esegue l'esempio più volte senza effettuare la pulizia.

> [!IMPORTANT]
> Questo esempio usa un suffisso univoco per ogni risorsa. Se si annulla il debug o si termina l'app senza eseguirla per intero, per l'account risulteranno disponibili più eventi live. <br/>
> Assicurarsi di arrestare gli eventi live in esecuzione. In caso contrario, verranno **fatturati**.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, è necessario creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice clonato all'inizio dell'articolo, la funzione **GetCredentialsAsync** crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Creare un evento live

Questa sezione mostra come creare un evento live di tipo **pass-through** (LiveEventEncodingType impostato su None). Per altre informazioni sui tipi di Eventi live disponibili, vedere [Tipi di eventi live](live-events-outputs-concept.md#live-event-types). 
 
Alcune caratteristiche che è possibile specificare durante la creazione dell'evento live sono:

* Località di Servizi multimediali.
* Protocollo di streaming per l'evento live (attualmente, sono supportati i protocolli RTMP e Smooth Streaming).<br/>Non è possibile modificare l'opzione relativa al protocollo durante l'esecuzione dell'evento live o degli output live associati. Se sono necessari protocolli diversi, è necessario creare eventi live separati per ogni protocollo di streaming.  
* Restrizioni IP per l'inserimento e l'anteprima. È possibile definire gli indirizzi IP autorizzati a inserire video in questo evento live. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').<br/>Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.<br/>Gli indirizzi IP devono essere in uno dei formati seguenti: indirizzo IPv4 con 4 numeri o intervallo di indirizzi CIDR.
* Quando si crea l'evento, è possibile impostarne l'avvio automatico. <br/>Quando l'avvio automatico è impostato su true, l'evento live verrà avviato dopo la creazione. Ciò significa che la fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione. Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).
* Per un URL di inserimento predittivo, impostare la modalità di "reindirizzamento a microsito". Per informazioni dettagliate, vedere [URL di inserimento di eventi live](live-events-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Ottenere gli URL di inserimento

Al termine della creazione dell'evento live, è possibile ottenere gli URL di inserimento che verranno passati al codificatore live. Questi URL vengono usati dal codificatore per inserire un flusso live.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Ottenere l'URL di anteprima

Usare previewEndpoint per visualizzare in anteprima e verificare che l'input dal codificatore venga effettivamente ricevuto.

> [!IMPORTANT]
> Assicurarsi che il video raggiunga l'URL di anteprima prima di continuare.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Creare e gestire eventi live e output live

Dopo l'avvio del flusso nell'evento live, è possibile iniziare l'evento di streaming creando un asset, un output live e un localizzatore di streaming. In questo modo il flusso viene archiviato e reso disponibile agli utenti tramite l'endpoint di streaming.

#### <a name="create-an-asset"></a>Creare un asset

Creare un asset utilizzabile dall'output live.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Creare un output live

Gli output live iniziano al momento della creazione e terminano quando vengono eliminati. Quando si elimina l'output live, non si elimina l'asset sottostante e il contenuto dell'asset.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Creare un localizzatore di streaming

> [!NOTE]
> Quando viene creato l'account Servizi multimediali, all'account viene aggiunto un endpoint di streaming **predefinito** nello stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**.

Quando l'asset output live è stato pubblicato usando un localizzatore di streaming, l'evento live (fino alla lunghezza dell'intervallo DVR) continuerà a essere visualizzabile fino alla scadenza o all'eliminazione del localizzatore di streaming, a seconda del valore raggiunto per primo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Pulizia delle risorse nell'account di Servizi multimediali

Se al termine dello streaming degli eventi si vuole pulire le risorse di cui in precedenza è stato effettuato il provisioning, seguire questa procedura:

* Interrompere il push del flusso dal codificatore.
* Arrestare l'evento live. Dopo l'arresto, l'evento live non è soggetto ad alcun addebito. Quando occorrerà riavviarlo, avrà lo stesso URL di inserimento, per cui non sarà necessario riconfigurare il codificatore.
* È possibile arrestare l'endpoint di streaming, a meno che non si voglia continuare a fornire l'archivio dell'evento live come flusso su richiesta. Se l'evento live si trova nello stato Arrestato, non è soggetto ad alcun addebito.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Guardare l'evento

Per guardare l'evento, copiare l'URL di streaming ottenuto durante l'esecuzione del codice descritto in Creare un localizzatore di streaming. È possibile usare il lettore multimediale desiderato. È possibile usare [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) per testare il flusso all'indirizzo https://ampdemo.azureedge.net.

Quando viene arrestato, l'evento live converte automaticamente gli eventi in contenuto su richiesta. Anche dopo l'arresto e l'eliminazione dell'evento, gli utenti potranno riprodurre in streaming il contenuto archiviato sotto forma di video on demand, fintanto che l'asset non viene eliminato. Un asset non può essere eliminato se è usato da un evento. È prima necessario eliminare l'evento.

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza.

Eseguire il comando dell'interfaccia della riga di comando seguente:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Lasciare l'evento live in esecuzione comporta costi di fatturazione. Tenere presente, in caso di arresto anomalo o chiusura del programma/progetto, che l'evento live potrebbe rimanere in esecuzione in stato di fatturazione.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

[Streaming di file](stream-files-tutorial-with-api.md)
 
