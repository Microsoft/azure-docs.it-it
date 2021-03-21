---
title: includere file
description: includere file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 4392e7f146f13e581f722b94f13038ad8abff0ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102245044"
---
## <a name="update-resources"></a>Aggiornare le risorse

Esistono alcune limitazioni sugli elementi che è possibile aggiornare. È possibile aggiornare gli elementi seguenti: 

Raccolta di immagini condivise:
- Descrizione

Definizione delle immagini:
- VCPU consigliati
- Memoria consigliata
- Descrizione
- Data di scadenza

Versione immagine:
- Conteggio di repliche a livello di area
- Aree di destinazione
- Esclusione dalla versione più recente
- Data di scadenza

Se si prevede di aggiungere aree di replica, non eliminare l'immagine gestita di origine. L'immagine gestita di origine è necessaria per la replica della versione dell'immagine in altre aree. 

Aggiornare la descrizione di una raccolta usando ([AZ Sig Update](/cli/azure/sig#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Aggiornare la descrizione di una definizione di immagine usando [AZ sig Image-Definition Update](/cli/azure/sig/image-definition#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Aggiornare una versione dell'immagine per aggiungere un'area in cui eseguire la replica usando [AZ sig Image-Version Update](/cli/azure/sig/image-definition#az-sig-image-definition-update). Questa modifica può richiedere un po' di tempo quando l'immagine viene replicata nella nuova area.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

Questo esempio illustra come usare il comando [AZ sig Image-Version Update](/cli/azure/sig/image-definition#az-sig-image-definition-update) per escludere la versione dell'immagine da usare come immagine *più recente* .

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

Questo esempio illustra come usare il comando [AZ sig Image-Version Update](/cli/azure/sig/image-definition#az-sig-image-definition-update) per includere questa versione dell'immagine in da considerare per l'immagine *più recente* .

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Eliminare le risorse

È necessario eliminare le risorse in ordine inverso, eliminando prima la versione dell'immagine. Dopo aver eliminato tutte le versioni dell'immagine, è possibile eliminare la definizione dell'immagine. Dopo aver eliminato tutte le definizioni dell'immagine, è possibile eliminare la raccolta. 

Eliminare una versione dell'immagine usando [AZ sig Image-version Delete](/cli/azure/sig/image-version#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Eliminare una definizione di immagine usando [AZ sig Image-Definition Delete](/cli/azure/sig/image-definition#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Eliminare una raccolta immagini usando [AZ sig Delete](/cli/azure/sig#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```