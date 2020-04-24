---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814769"
---
## <a name="before-you-begin"></a>Prima di iniziare

Per completare l'esempio in questo articolo, è necessario disporre di un'immagine gestita esistente di una macchina virtuale generalizzata. Per altre informazioni, vedere [esercitazione: creare un'immagine personalizzata di una macchina virtuale di Azure con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Se l'immagine gestita contiene un disco dati, le dimensioni del disco dati non possono superare 1 TB.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, vedere [Install Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini 

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. I caratteri consentiti per i nomi delle raccolte sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini.   I nomi di raccolta devono essere univoci all'interno della sottoscrizione. 

Creare una raccolta di immagini usando [sig az create](/cli/azure/sig#az-sig-create). L'esempio seguente crea una raccolta denominata *myGallery* nelle *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine

Le definizioni di immagine creano un raggruppamento logico per le immagini. Vengono utilizzati per gestire le informazioni sulle versioni delle immagini create al suo interno. I nomi delle definizioni di immagine possono essere costituiti da lettere maiuscole o minuscole, cifre, punti, trattini e punti. Per ulteriori informazioni sui valori che è possibile specificare per la definizione di un'immagine, vedere [definizioni di immagine](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Creare una definizione dell'immagine iniziale nella raccolta usando [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>Creare una versione di immagine 

Creare versioni dell'immagine in base alle esigenze usando [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). È necessario passare l'ID dell'immagine gestita da usare come baseline per creare la versione dell'immagine. È possibile usare [elenco di immagini di az](/cli/azure/image?view#az-image-list) per ottenere informazioni sulle immagini in un gruppo di risorse. 

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

In questo esempio, la versione dell'immagine è *1.0.0* e verranno create 2 repliche nell'area stati *Uniti centro-occidentali* , una replica nell'area Stati Uniti centro- *meridionali* e una replica nell'area *Stati Uniti orientali 2* con archiviazione con ridondanza della zona.


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> È necessario attendere che la versione dell'immagine completi la compilazione e la replica prima di poter usare la stessa immagine gestita per creare un'altra versione dell'immagine.
>
> È anche possibile archiviare tutte le repliche di versione dell'immagine nell' [archiviazione con ridondanza della zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) aggiungendo `--storage-account-type standard_zrs` quando si crea la versione dell'immagine.
>

## <a name="share-the-gallery"></a>Condividere la raccolta

Si consiglia di condividere con altri utenti a livello di raccolta. Per ottenere l'ID oggetto della raccolta, usare [AZ sig Show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Usare l'ID oggetto come ambito, insieme a un indirizzo di posta elettronica e [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create) per concedere a un utente l'accesso alla raccolta di immagini condivise.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


