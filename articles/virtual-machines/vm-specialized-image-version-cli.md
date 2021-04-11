---
title: Creare una macchina virtuale da una versione di immagine specializzata usando l'interfaccia della riga di comando di Azure
description: Creare una VM usando una versione di immagine specializzata in una raccolta di immagini condivise usando l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe081b0e74acf771e10406c15a3dea4e09956c37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560965"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Creare una VM usando una versione di immagine specializzata con l'interfaccia della riga di comando di Azure

Creare una macchina virtuale da una [versione di immagine specializzata](./shared-image-galleries.md#generalized-and-specialized-images) archiviata in una raccolta di immagini condivise. Se si vuole creare una VM usando una versione di immagine generalizzata, vedere [creare una macchina virtuale da una versione di immagine generalizzata](vm-generalized-image-version-cli.md).

Sostituire i nomi delle risorse di questo esempio secondo necessità. 

Elencare le definizioni di immagine in una raccolta usando [AZ sig Image-Definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) per visualizzare il nome e l'ID delle definizioni.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Per creare la macchina virtuale, usare [az vm create](/cli/azure/vm#az-vm-create) con il parametro --specialized per indicare che si tratta di un'immagine specializzata. 

Usare l'ID definizione immagine per `--image` per creare la macchina virtuale dalla versione più recente dell'immagine disponibile. È anche possibile creare la macchina virtuale da una versione specifica fornendo l'ID versione dell'immagine per `--image`. 

In questo esempio viene creata una macchina virtuale dalla versione più recente dell'immagine *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Passaggi successivi
Il [Generatore di immagini di Azure (anteprima)](./image-builder-overview.md) consente di automatizzare la creazione della versione di immagine. è anche possibile usarla per aggiornare e [creare una nuova versione dell'immagine da una versione di immagine esistente](./linux/image-builder-gallery-update-image-version.md). 

È anche possibile creare una risorsa di raccolta di immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creare una macchina virtuale dalla versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)