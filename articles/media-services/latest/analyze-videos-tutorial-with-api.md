---
title: Analizzare i video con Servizi multimediali v3
description: Informazioni su come analizzare i video con Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 4a050d838bae9b394f5f292698781a9a824af0bf
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454125"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Esercitazione: Analizzare i video con Servizi multimediali v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Anche se l'esercitazione usa esempi di [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent), le procedure generali sono identiche per l'[API REST](/rest/api/media/liveevents), l'[interfaccia della riga di comando](/cli/azure/ams/live-event) o altri [SDK](media-services-apis-overview.md#sdks) supportati.

Questa esercitazione illustra come analizzare i video usando Servizi multimediali di Azure. Esistono molti scenari in cui può essere opportuno acquisire informazioni dettagliate da video registrati e contenuti audio. Le organizzazioni, ad esempio, per migliorare la soddisfazione dei clienti, possono convertire in testo scritto l'audio delle registrazioni del supporto clienti e trasformarle in un catalogo di consultazione, con indici e dashboard. Possono quindi acquisire informazioni dettagliate sull'andamento dell'attività. Queste informazioni dettagliate includono un elenco dei reclami frequenti, delle origini di tali reclami, nonché altre informazioni utili.

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Scaricare l'app di esempio descritta nell'argomento.
> * Esaminare il codice che analizza il video specificato.
> * Eseguire l'app.
> * Esaminare l'output.
> * Pulire le risorse.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Conformità, privacy e sicurezza
 
È importante ricordare che è necessario usare Video Indexer in conformità con tutte le leggi applicabili e non è possibile usare Video Indexer né qualsiasi altro servizio di Azure in un modo che violi i diritti di altri utenti o possa arrecare danni ad altri utenti. Per poter caricare video, inclusi dati biometrici, nel servizio Video Indexer per elaborarli e archiviarli, è necessario disporre di tutti i diritti appropriati, inclusi tutti i consensi appropriati delle persone che compaiono nel video. Per informazioni su conformità, privacy e sicurezza in Video Indexer, vedere le [condizioni di Servizi cognitivi Microsoft](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Per informazioni sugli obblighi relativi alla privacy e la gestione dei dati degli utenti da parte di Microsoft, vedere l'[informativa sulla privacy](https://privacy.microsoft.com/PrivacyStatement), le [condizioni per l'utilizzo dei servizi online](https://www.microsoft.com/licensing/product-licensing/products) e l'[addendum sull'elaborazione dati](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) Microsoft. Informazioni aggiuntive sulla privacy, tra cui la conservazione e l'eliminazione/distruzione dei dati, sono disponibili nelle condizioni per l'utilizzo dei servizi online e [qui](../video-indexer/faq.md). Usando Video Indexer, l'utente accetta di essere vincolato dalle condizioni di Servizi cognitivi, dalle condizioni per l'utilizzo dei servizi online, dall'addendum sull'elaborazione dati e dall'informativa sulla privacy.

## <a name="prerequisites"></a>Prerequisiti

- Se Visual Studio non è installato, scaricare [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md).<br/>Assicurarsi di ricordare i valori usati per il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.
- Seguire la procedura descritta in [Accedere all'API di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure](./access-api-howto.md) e salvare le credenziali. Sarà necessario usarle per accedere all'API.

## <a name="download-and-configure-the-sample"></a>Scaricare e configurare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

L'esempio si trova nella cartella [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Aprire il file [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) nel progetto scaricato. Sostituire i valori con le credenziali ottenute durante l'[accesso alle API](./access-api-howto.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Esaminare il codice che analizza il video specificato

Questa sezione esamina le funzioni definite nel file [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) del progetto *AnalyzeVideos*.

L'esempio completa le azioni seguenti:

1. Crea una **trasformazione** e un **processo** per analizzare il video.
2. Crea un **asset** di input e carica il video al suo interno. L'asset viene usato come input del processo.
3. Crea un asset di output che archivia l'output del processo.
4. Invia il processo.
5. Controlla lo stato del processo.
6. Scarica i file risultanti dall'esecuzione del processo.

> [!NOTE]
> Quando si usa un set di impostazioni di analisi video o audio, usare il portale di Azure per impostare l'account in modo che abbia 10 Media Reserved Units S3. Per altre informazioni, vedere [Panoramica del ridimensionamento dell'elaborazione multimediale](media-reserved-units-cli-how-to.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, è necessario creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice clonato all'inizio dell'articolo, la funzione **GetCredentialsAsync** crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Creare un asset di input e caricare un file locale nell'asset 

La funzione **CreateInputAsset** crea un nuovo [asset](/rest/api/media/assets) di input e carica al suo interno il file video locale specificato. Questo asset viene usato come input per il processo di codifica. In Servizi multimediali v3 l'input di un processo può essere costituito da un asset oppure da contenuti resi disponibili all'account di Servizi multimediali tramite URL HTTPS. Per informazioni su come codificare contenuti da un URL HTTPS, vedere [questo](job-input-from-http-how-to.md) articolo.  

In Servizi multimediali v3 si usano le API di Archiviazione di Azure per caricare i file. Il frammento di codice .NET seguente illustra come eseguire questa operazione.

La funzione seguente completa queste azioni:

* Crea un asset.
* Ottiene un [URL di firma di accesso condiviso](../../storage/common/storage-sas-overview.md) scrivibile nel [contenitore della risorsa di archiviazione](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) dell'asset.

    Se si usa la funzione [ListContainerSas](/rest/api/media/assets/listcontainersas) dell'asset per ottenere gli URL di firma di accesso condiviso, si noti che la funzione ne restituisce molti, perché sono disponibili due chiavi per ogni account di archiviazione. Il motivo della presenza di due chiavi è che rende possibile la rotazione trasparente delle chiavi dell'account di archiviazione (ad esempio, cambiarne una mentre si usa l'altra, quindi iniziare a usare la nuova chiave e ruotare l'altra). Il primo URL di firma di accesso condiviso rappresenta la chiave Key1 dell'account di archiviazione e il secondo la chiave Key2.
* Carica il file nel contenitore nel servizio di archiviazione usando l'URL di firma di accesso condiviso.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Creare un asset di output per archiviare il risultato del processo

L'[asset](/rest/api/media/assets) di output archivia il risultato del processo. Il progetto definisce la funzione **DownloadResults** che scarica i risultati dall'asset di output nella cartella "output", consentendo così di visualizzare ciò che si è ottenuto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Creare una trasformazione e un processo per analizzare i video

Quando si codificano o si elaborano contenuti in Servizi multimediali, è prassi comune configurare le impostazioni di codifica come una serie di istruzioni. e quindi inviare un oggetto **Job**, ovvero il processo per applicare tali istruzioni a un video. Inviando nuovi processi per ogni nuovo video, si applicano le istruzioni a tutti i video nella libreria. In Servizi multimediali una serie di istruzioni è definita **trasformazione**. Per altre informazioni, vedere [Trasformazioni e processi](./transforms-jobs-concept.md). L'esempio illustrato in questa esercitazione definisce una serie di istruzioni che analizza il video specificato.

#### <a name="transform"></a>Trasformare

Quando si crea una nuova istanza dell'oggetto [Transform](/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. **TransformOutput** è un parametro obbligatorio. Ogni **TransformOutput** contiene un parametro **Preset**. In **Preset** sono descritte le istruzioni dettagliate delle operazioni di elaborazione di contenuti video e/o audio che devono essere usate per generare l'oggetto **TransformOutput** desiderato. In questo esempio viene usato il set di impostazioni **VideoAnalyzerPreset** e viene passata la lingua ("en-US") al relativo costruttore (`new VideoAnalyzerPreset("en-US")`). Questo set di impostazioni consente di estrarre da un video più informazioni dettagliate sui contenuti audio e video. Se è necessario estrarre da un video solo le informazioni dettagliate relative all'audio, è possibile usare il set di impostazioni **AudioAnalyzerPreset**.

Quando si crea una **trasformazione**, è prima necessario verificare se ne esiste già una tramite il metodo **Get**, come illustrato nel codice seguente. In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Processo

Come indicato sopra, l'oggetto [Transform](/rest/api/media/transforms) è la serie di istruzioni, mentre l'oggetto [Job](/rest/api/media/jobs) è la richiesta effettiva inviata a Servizi multimediali per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. Il **processo** specifica informazioni come la posizione del video di input e quella dell'output. È possibile specificare il percorso del video mediante: URL HTTPS, URL SAS o asset presenti nell'account del servizio multimediale.

In questo esempio, l'input del processo è un video locale.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Attendere il completamento del processo

Il completamento del processo richiede qualche istante. Al termine dell'operazione, si vorrà ricevere una notifica. Per la generazione di notifiche relative al completamento del [processo](/rest/api/media/jobs) sono disponibili varie opzioni. La più semplice, qui illustrata, consiste nell'uso del polling.

Il polling non è una procedura consigliata per le app di produzione a causa dei rischi di latenza. Il polling può essere limitato se usato eccessivamente su un account. In alternativa, è preferibile che gli sviluppatori usino Griglia di eventi.

Griglia di eventi è un servizio progettato per garantire disponibilità elevata, coerenza nelle prestazioni e scalabilità dinamica. Con Griglia di eventi, le app possono rimanere in ascolto e reagire agli eventi praticamente da tutti i servizi di Azure, oltre che da origini personalizzate. Questo semplice servizio di gestione degli eventi, reattivo e basato su HTTP, consente di creare soluzioni efficienti tramite funzioni intelligenti di filtraggio e routing di eventi. Per altre informazioni, [Instradare gli eventi a un endpoint Web personalizzato](job-state-events-cli-how-to.md).

L'oggetto **Job** assume progressivamente gli stati seguenti: **Scheduled**, **Queued**, **Processing**, **Finished** (stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Errore**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Annullamento in corso** e, al termine, lo stato **Annullato**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Codici di errore dei processi

Vedere i [codici di errore](/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Scaricare il risultato del processo

La funzione seguente scarica i risultati del processo dall'[asset](/rest/api/media/assets) di output nella cartella "output" per consentire l'analisi dei risultati del processo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Pulire le risorse nell'account di Servizi multimediali

Normalmente è necessario pulire tutti gli oggetti tranne quelli che si prevede di riutilizzare. In genere si riutilizzano le trasformazioni e si salvano in modo permanente i localizzatori di streaming. Se dopo l'attività di sperimentazione si vuole pulire l'account, eliminare le risorse che non si prevede di riutilizzare. Il codice seguente, ad esempio, elimina il processo e l'asset di output:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Premere CTRL+F5 per eseguire l'app *AnalyzeVideos*.

Quando si esegue il programma, il processo genera anteprime per ogni volto rilevato nel video, oltre al file insights.json.

## <a name="examine-the-output"></a>Esaminare l'output

Il file di output risultante dall'analisi dei video è denominato insights.json e contiene informazioni dettagliate sul video analizzato. La descrizione degli elementi presenti nel file json è riportata nell'articolo [Intelligenza dei contenuti multimediali](./analyzing-video-audio-files-concept.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza.

Eseguire il comando dell'interfaccia della riga di comando seguente:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Gli SDK di Servizi multimediali di Azure v3 non sono thread-safe. Quando si usa un'app multithreading, è necessario generare un nuovo oggetto AzureMediaServicesClient per ogni thread.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il caricamento, la codifica e lo streaming di video](stream-files-tutorial-with-api.md)
