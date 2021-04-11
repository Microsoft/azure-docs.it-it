---
title: Codificare la trasformazione personalizzata .NET
description: Questo argomento illustra come usare servizi multimediali di Azure V3 per codificare una trasformazione personalizzata usando .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: eb72c34489755dad17700a16a5109824ad892e83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553273"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Come codificare con una trasformazione personalizzata-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Quando si esegue la codifica con servizi multimediali di Azure, è possibile iniziare rapidamente con uno dei set di impostazioni predefiniti consigliati in base alle procedure consigliate del settore, come illustrato nell'esercitazione relativa ai [file di streaming](stream-files-tutorial-with-api.md) . È anche possibile creare un set di impostazioni personalizzato per individuare i requisiti specifici dello scenario o del dispositivo.

## <a name="considerations"></a>Considerazioni

Quando si creano set di impostazioni personalizzati, si applicano le considerazioni seguenti:

* Tutti i valori per altezza e larghezza sul contenuto AVC devono essere un multiplo di 4.
* In servizi multimediali di Azure V3 tutti i bitrate di codifica sono in bit al secondo. Questa impostazione è diversa da quella delle API v2, che hanno usato kilobit al secondo come unità. Se, ad esempio, la velocità in bit in V2 è stata specificata come 128 (kilobit al secondo), in V3 verrebbe impostata su 128000 (bit al secondo).

## <a name="prerequisites"></a>Prerequisiti

[Creare un account di Servizi multimediali di Azure.](./create-account-howto.md)

## <a name="download-the-sample"></a>Scaricare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET Core completo usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```
 
L'esempio di set di impostazioni personalizzato si trova nella [codifica con un set di impostazioni personalizzato usando](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264) la cartella .NET.

## <a name="create-a-transform-with-a-custom-preset"></a>Creare una trasformazione con un set di impostazioni personalizzato

Quando si crea un nuovo oggetto [Transform](/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto [TransformOutput](/rest/api/media/transforms/createorupdate#transformoutput), come illustrato nel codice seguente. Ogni **TransformOutput** contiene un parametro **Preset**. Il **set di impostazioni** descrive le istruzioni dettagliate per le operazioni di elaborazione video e/o audio da usare per generare il **TransformOutput** desiderato. L'oggetto **TransformOutput** seguente crea impostazioni di output personalizzate per codec e livello.

Quando si crea un oggetto [Transform](/rest/api/media/transforms), è necessario verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente. In servizi multimediali V3, i metodi **Get** sulle entità restituiscono **null** se l'entità non esiste, ovvero un controllo senza distinzione tra maiuscole e minuscole per il nome.

### <a name="example"></a>Esempio

Nell'esempio seguente viene definito un set di output che si desidera generare quando si utilizza questa trasformazione. Viene innanzitutto aggiunto un livello l'elemento aacaudio per la codifica audio e due livelli H264Video per la codifica video. Nei livelli video si assegnano le etichette in modo da poterle usare nei nomi dei file di output. Successivamente, si vuole che l'output includa anche anteprime. Nell'esempio seguente vengono specificate le immagini in formato PNG, generate al 50% della risoluzione del video di input e tre timestamp: {25%, 50%, 75%} della lunghezza del video di input. Infine, viene specificato il formato per i file di output, uno per video e audio e un altro per le anteprime. Poiché sono presenti più H264Layers, è necessario usare macro che producono nomi univoci per livello. È possibile usare una `{Label}` macro o `{Bitrate}` , l'esempio mostra la prima.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomPreset_H264/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Passaggi successivi

[Streaming di file](stream-files-tutorial-with-api.md) 
