---
title: Esempio di script dell'interfaccia della riga di comando di Azure-creare e inviare un processo
description: Lo script dell'interfaccia della riga di comando di Azure in questo argomento mostra come inviare un processo a una semplice trasformazione di codifica con URL HTTPs.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3e05e3f67de03aa300fb650d23357286ee71c52
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123394"
---
# <a name="cli-example-create-and-submit-a-job"></a>Esempio dell'interfaccia della riga di comando: Creare e inviare un processo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Servizi multimediali v3, quando si inviano processi per elaborare i video, è necessario indicare a Servizi multimediali dove trovare il video di input. Una delle opzioni consiste nello specificare un URL HTTPS come input del processo, come illustrato in questo articolo. 

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimediali di Azure](./account-create-how-to.md).

## <a name="example-script"></a>Script di esempio

Quando si esegue `az ams job start`, è possibile impostare un'etichetta per l'output del processo. L'etichetta può essere usata successivamente per identificare a cosa serve questo asset di output. 

- Se si assegna un valore all'etichetta, impostare '--output-assets' su "assetname=label"
- Se non si assegna un valore all'etichetta, impostare '--output-assets' su "assetname=".
  Si noti che si aggiunge "=" a `output-assets`. 

```azurecli
az ams job start \
  --name testJob001 \
  --transform-name testEncodingTransform \
  --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' \
  --files 'Ignite-short.mp4' \
  --output-assets testOutputAssetName= \
  -a amsaccount \
  -g amsResourceGroup 
```

La risposta visualizzata sarà simile alla seguente:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

## <a name="next-steps"></a>Passaggi successivi

[az ams job (interfaccia della riga di comando)](/cli/azure/ams/job)
