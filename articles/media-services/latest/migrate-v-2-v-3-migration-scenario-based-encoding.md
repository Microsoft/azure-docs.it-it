---
title: Linee guida per la migrazione della codifica
description: Questo articolo fornisce indicazioni sulla codifica basate sugli scenari che aiuteranno a eseguire la migrazione da servizi multimediali di Azure V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: a01571f4a1f852deb84b7f20d61b8048e8000790
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490098"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Guida alla migrazione basata su scenari di codifica

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-4.svg)

Questo articolo fornisce indicazioni sulla codifica basate sugli scenari che aiuteranno a eseguire la migrazione da servizi multimediali di Azure V2 a V3.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a modificare il flusso di lavoro di codifica, è consigliabile comprendere le differenze nella modalità di gestione dell'archiviazione.  In AMS V3, l'API di archiviazione di Azure viene usata per gestire gli account di archiviazione associati all'account di servizi multimediali.

> [!NOTE]
> I processi e le attività creati nella versione V2 non vengono visualizzati nella versione V3 perché non sono associati a una trasformazione. Si consiglia di passare a trasformazioni e processi V3.

## <a name="encoding-workflow-comparison"></a>Confronto tra flussi di lavoro di codifica

Per un confronto visivo dei flussi di lavoro di codifica per V2 e V3, è necessario esaminare i diagrammi di flusso seguenti.

### <a name="v2-encoding-workflow"></a>Flusso di lavoro di codifica V2

Fare clic sull'immagine seguente per visualizzare una versione più grande.

[![Flusso di lavoro di codifica per V2 ](./media/migration-guide/V2-pretty.svg)](./media/migration-guide/V2-pretty.svg#lightbox)

1. Eseguire la configurazione
    1. Creare un asset o usare un asset esistente. Se si usa un nuovo asset, caricare il contenuto nell'asset. Se si usa un asset esistente, è necessario codificare i file già esistenti nell'asset.
    2. Ottenere i valori degli elementi seguenti:
        - ID o oggetto media processor
        - Stringa del codificatore (nome) del codificatore che si vuole usare
        - ID asset del nuovo asset o ID dell'asset esistente
    3. Per il monitoraggio, creare una sottoscrizione di notifica a livello di processo o di attività o un gestore eventi SDK
2. Creare il processo che contiene l'attività o le attività. Ogni attività deve includere gli elementi precedenti e:
    - Direttiva che deve essere creata da un asset di output.  L'asset di output viene creato dal sistema.
    - Nome facoltativo per l'asset di output
3. Inviare il processo.
4. Monitorare il processo.

### <a name="v3-encoding-workflow"></a>Flusso di lavoro di codifica V3

[![Flusso di lavoro di codifica per V3](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. Configurazione
    1. Creare un asset o usare un asset esistente. Se si usa un nuovo asset, caricare il contenuto nell'asset. Se si usa un asset esistente, è necessario codificare i file già esistenti nell'asset. Non è necessario *caricare altro contenuto nell'asset.*
    1. Creare un asset di output.  L'asset di output è la posizione in cui verranno archiviati i file codificati e i metadati di input e output.
    1. Ottenere i valori per la trasformazione:
        - Set di impostazioni standard del codificatore
        - Gruppo di risorse AMS
        - Nome dell'account AMS
    1. Creare la trasformazione o utilizzare una trasformazione esistente.  Le trasformazioni sono riutilizzabili. Non è necessario creare una nuova trasformazione ogni volta che si vuole inviare un processo.
1. Creare un processo
    1. Per il processo, ottenere i valori per gli elementi seguenti:
        - Nome trasformazione
        - URI di base per l'URL SAS per l'asset, il percorso di origine HTTPs della condivisione file o il percorso locale dei file. `JobInputAsset`Può anche usare un nome di asset come input.
        - Nome/i file
        - Asset di output
        - Un gruppo di risorse
        - Nome dell'account AMS  
1. Usare [griglia di eventi](monitoring/monitor-events-portal-how-to.md) per il monitoraggio del processo.
1. Inviare il processo.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Set di impostazioni personalizzati dalla codifica V2 alla versione V3

Se il codice V2 ha chiamato il codificatore standard con un set di impostazioni personalizzato, è prima di tutto necessario creare una nuova trasformazione con il set di impostazioni del codificatore standard personalizzato prima di inviare un processo.

I set di impostazioni personalizzati sono ora JSON e non sono più basati su XML. Ricreare il set di impostazioni in JSON dopo lo schema del set di impostazioni personalizzato, come definito nella documentazione di [Transform Open API (spavalderia)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) .

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>File di metadati di input e output da un processo di codifica

Nella versione V2, i file di metadati di input e output XML vengono generati come risultato di un processo di codifica. In V3, il formato dei metadati è stato modificato da XML a JSON. Per altre informazioni sui metadati, vedere metadati di [input](input-metadata-schema.md) e [metadati di output](output-metadata-schema.md).

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Codificatore Premium per il codificatore standard V3 o soluzioni basate su partner

L'API v2 non supporta più il codificatore Premium. Se in precedenza è stato usato il codificatore Premium basato sul flusso di lavoro per la codifica HEVC, è necessario eseguire la migrazione al nuovo [codificatore v3 standard](encode-media-encoder-standard-formats-reference.md) con supporto della codifica HEVC.

Se sono necessarie le funzionalità avanzate del flusso di lavoro del codificatore Premium, si consiglia di iniziare a usare una soluzione partner di codifica avanzata di Azure da [Imagine Communications](https://imaginecommunications.com), [Telestream](https://www.telestream.net)o [Bitmovin](https://bitmovin.com).

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>Processi con input presenti in URL ospitati HTTPS

È ora possibile inviare i processi in V3 da file archiviati in archiviazione di Azure, archiviati localmente o server Web esterni usando il [supporto per l'input del processo http (S)](job-input-from-http-how-to.md).

Se in precedenza sono stati usati flussi di lavoro per copiare file da file BLOB di Azure in asset vuoti prima di inviare i processi, è possibile semplificare il flusso di lavoro passando un URL di firma di accesso condiviso per il file nell'archivio BLOB di Azure direttamente nel processo.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Trascrizione audio dell'indicizzatore V1 alla nuova AudioAnalyzer "modalità di base"

Per i clienti che usano il processore Indexer V1 nell'API v2, è necessario creare una trasformazione che richiama il nuovo `AudioAnalyzer` in [modalità di base](transform-create-basic-audio-how-to.md) prima di inviare un processo.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>Concetti relativi a codifica, trasformazioni e processi, esercitazioni e procedure guidate

### <a name="concepts"></a>Concetti

- [Codifica di video e audio con servizi multimediali](encode-concept.md)
- [Formati e codec standard del codificatore](encode-media-encoder-standard-formats-reference.md)
- [Codifica con una scala a bitrate generata automaticamente](encode-autogen-bitrate-ladder.md)
- [Usare il set di impostazioni di codifica compatibile con il contenuto per trovare il valore di velocità in bit ottimale per una determinata risoluzione](encode-content-aware-concept.md)
- [Media reserved unit](concept-media-reserved-units.md)
- [Metadati di input](input-metadata-schema.md)
- [Metadati di output](output-metadata-schema.md)
- [Creazione dinamica dei pacchetti in servizi multimediali V3: codec audio](encode-dynamic-packaging-concept.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Esercitazioni

- [Esercitazione: Codificare un file remoto basato su URL ed eseguire lo streaming del video - .NET](stream-files-dotnet-quickstart.md)
- [Esercitazione: Caricare, codificare ed eseguire lo streaming di video con Servizi multimediali v3](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Guide pratiche

- [Creare un input del processo da un URL HTTPS](job-input-from-http-how-to.md)
- [Creare un input del processo da un file locale](job-input-from-local-file-how-to.md)
- [Creare una trasformazione audio di base](transform-create-basic-audio-how-to.md)
- Con .NET
  - [Come codificare con una trasformazione personalizzata-.NET](transform-custom-presets-how-to.md)
  - [Come creare una sovrapposizione con Media Encoder Standard](transform-create-overlay-how-to.md)
  - [Come generare anteprime usando il codificatore standard con .NET](transform-generate-thumbnails-dotnet-how-to.md)
- Con l'interfaccia della riga di comando di Azure
  - [Come codificare con una trasformazione personalizzata-interfaccia della riga di comando di Azure](transform-custom-preset-cli-how-to.md)
- Con REST
  - [Come codificare con una trasformazione personalizzata-REST](transform-custom-preset-rest-how-to.md)
  - [Come generare anteprime usando il codificatore standard con REST](transform-generate-thumbnails-rest-how-to.md)
- [Sottoclip di un video durante la codifica con servizi multimediali-.NET](transform-subclip-video-dotnet-how-to.md)
- [Sottoclip di un video durante la codifica con servizi multimediali-REST](transform-subclip-video-rest-how-to.md)

## <a name="samples"></a>Esempi

È anche possibile [confrontare il codice V2 e V3 negli esempi di codice](migrate-v-2-v-3-migration-samples.md).
