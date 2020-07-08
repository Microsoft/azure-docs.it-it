---
title: includere il file
description: includere file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67180159"
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

Aggiornare la descrizione di una raccolta usando ([AZ Sig Update](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Aggiornare la descrizione di una definizione di immagine usando [AZ sig Image-Definition Update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Aggiornare una versione dell'immagine per aggiungere un'area in cui eseguire la replica usando [AZ sig Image-Version Update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Questa modifica può richiedere un po' di tempo quando l'immagine viene replicata nella nuova area.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Eliminare le risorse

È necessario eliminare le risorse in ordine inverso, eliminando prima la versione dell'immagine. Dopo aver eliminato tutte le versioni dell'immagine, è possibile eliminare la definizione dell'immagine. Dopo aver eliminato tutte le definizioni dell'immagine, è possibile eliminare la raccolta. 

Eliminare una versione dell'immagine usando [AZ sig Image-version Delete](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Eliminare una definizione di immagine usando [AZ sig Image-Definition Delete](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Eliminare una raccolta immagini usando [AZ sig Delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
