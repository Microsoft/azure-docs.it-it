---
title: Aree in cui è disponibile Video Indexer - Azure
titleSuffix: Azure Media Services
description: Questo articolo illustra le aree di Azure in cui è disponibile Video Indexer di servizi multimediali di Azure.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c91b38fcbfb9b517651adead010408425e519a82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80382750"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Aree di Azure in cui esiste Video Indexer

Le API di Video Indexer contengono un parametro **location** che è consigliabile impostare sull'area di Azure a cui deve essere indirizzata la chiamata. Questa deve essere un'[area di Azure in cui è disponibile Video Indexer](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Percorsi

Al parametro **location** deve essere assegnato come valore il nome del codice di un'area di Azure. Se si usa Video Indexer in modalità di anteprima, inserire *"trial"* come valore. In alternativa, per ottenere il nome del codice dell'area di Azure in cui si trova l'account e a cui deve essere indirizzata la chiamata, è possibile eseguire la riga seguente nell'[interfaccia della riga di comando di Azure](/cli/azure):

```azurecli-interactive
az account list-locations
```

Dopo avere eseguito la riga precedente, viene visualizzato un elenco di tutte le aree di Azure. Passare all'area di Azure con il *displayName* desiderato e usare il valore *name* per il parametro **location**.

Ad esempio, per l'area di Azure Stati Uniti occidentali 2 (indicata di seguito), usare "westus2" per il parametro **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Passaggi successivi

- [Customize Language model using APIs](customize-language-model-with-api.md) (Personalizzare il modello linguistico usando le API)
- [Personalizzare il modello di Marchi tramite API](customize-brands-model-with-api.md)
- [Personalizzare il modello delle persone usando le API](customize-person-model-with-api.md)
