---
title: Localizzatori di streaming in servizi multimediali di Azure
description: Questo articolo illustra le caratteristiche dei localizzatori di streaming e descrive come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b4994f7014abfafd3853745a4314a75be011c4e4
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213488"
---
# <a name="streaming-locators"></a>Localizzatori di streaming

Per rendere i video nell'asset di output disponibili per la riproduzione da parte dei client, è necessario creare un [localizzatore di streaming](/rest/api/media/streaminglocators) e quindi definire gli URL di streaming. Per creare un URL, è necessario concatenare il nome host dell'endpoint di streaming e il percorso del localizzatore di streaming. Per un esempio .NET, vedere [Ottenere un localizzatore di streaming](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Il processo di creazione di un **localizzatore di streaming** è detto pubblicazione. Per impostazione predefinita, il **localizzatore di streaming** è valido immediatamente dopo aver effettuato le chiamate all'API e dura fino a quando non viene eliminato, a meno che non si configuri l'ora di inizio e di fine facoltativa. 

Quando si crea un **localizzatore di streaming**, è necessario specificare un nome di **Asset** e un nome di **criterio di flusso** . Per altre informazioni, vedere i seguenti argomenti:

* [Asset](assets-concept.md)
* [Criteri di streaming](streaming-policy-concept.md)
* [Criteri chiave simmetrica](content-key-policy-concept.md)

È anche possibile specificare l'ora di inizio e di fine nel localizzatore di streaming, che consente all'utente di riprodurre il contenuto tra questi orari, ad esempio tra 5/1/2019 e 5/5/2019.  

## <a name="considerations"></a>Considerazioni

* I **localizzatori di streaming** non sono aggiornabili. 
* Le proprietà dei **localizzatori di streaming** di tipo Datetime sono sempre in formato UTC.
* È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming tutte le volte che si devono usare le stesse opzioni. Per altre informazioni, vedere [quote e limiti](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Creare localizzatori di streaming  

### <a name="not-encrypted"></a>Non crittografato

Se si vuole trasmettere il file in modo chiaro (non crittografato), impostare i criteri di Clear streaming predefiniti: su' Predefined_ClearStreamingOnly ' (in .NET è possibile usare l'enumerazione PredefinedStreamingPolicy. ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Crittografato 

Se è necessario crittografare il contenuto con la crittografia CENC, impostare i criteri su' Predefined_MultiDrmCencStreaming '. La crittografia Widevine verrà applicata a un flusso DASH e PlayReady a Smooth. La chiave verrà recapitata a un client di riproduzione in base alle licenze DRM configurate.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Se si vuole anche crittografare il flusso HLS con CBCS (FairPlay), usare ' Predefined_MultiDrmStreaming '.

> [!NOTE]
> Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="associate-filters-with-streaming-locators"></a>Associare filtri a localizzatori di streaming

Vedere [filtri: associare i localizzatori di streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Entità di filtro, ordine, indicatore di flusso di pagine

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Elencare i localizzatori di streaming per nome di asset

Per ottenere i localizzatori di streaming in base al nome dell'asset associato, usare le operazioni seguenti:

|Linguaggio|API|
|---|---|
|REST|[liststreaminglocators](/rest/api/media/assets/liststreaminglocators)|
|CLI|[AZ AMS Asset List-streaming-Locators](/cli/azure/ams/asset#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Vedi anche

* [Asset](assets-concept.md)
* [Criteri di streaming](streaming-policy-concept.md)
* [Criteri chiave simmetrica](content-key-policy-concept.md)
* [Esercitazione: caricare, codificare e trasmettere in streaming video con .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Passaggi successivi

[Come creare un localizzatore di streaming e gli URL di compilazione](create-streaming-locator-build-url.md)
