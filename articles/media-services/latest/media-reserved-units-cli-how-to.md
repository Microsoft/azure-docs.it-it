---
title: INTERFACCIA della riga di comando MRU (media reserved Unit)
description: In questo argomento viene spiegato come usare l’interfaccia della riga di comando per ridimensionare l'elaborazione di servizi multimediali con Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: b1c98bfa6b2cf45a59b70126001442ed80659668
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955886"
---
# <a name="how-to-scale-media-reserved-units"></a>Come ridimensionare media reserved unit

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo illustra come ridimensionare media reserved Unit (MRSs) per una codifica più rapida.

## <a name="prerequisites"></a>Prerequisiti

[Creare un account di Servizi multimediali di Azure](./create-account-howto.md).

Informazioni sulle [unità riservate multimediali](concept-media-reserved-units.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Ridimensionare le Media Reserved Unit con l'interfaccia della riga di comando

Eseguire il comando `mru`.

Il comando [az ams account mru](/cli/azure/ams/account/mru?view=azure-cli-latest) seguente imposta le Media Reserved Unit sull'account "amsaccount" usando i parametri **count** e **type**.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fatturazione

L'addebito è basato sul numero di minuti per cui viene effettuato il provisioning di media reserved Unit nell'account. Ciò si verifica indipendentemente dalla presenza di processi in esecuzione nell'account. Per una spiegazione dettagliata, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Passaggio successivo

[Analizzare i video](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Vedi anche

* [Quote e limiti](limits-quotas-constraints.md)
