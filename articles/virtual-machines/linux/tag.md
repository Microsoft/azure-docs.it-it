---
title: Come assegnare un tag a una macchina virtuale Linux di Azure
description: Informazioni sull'assegnazione di tag a una macchina virtuale Linux creata in Azure con il modello di distribuzione di Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.custom: devx-track-azurecli
ms.openlocfilehash: a830f6eb604bb1173a7a552ad5f81c936f02d5fd
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500522"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Come contrassegnare una macchina virtuale Linux in Azure
Questo articolo descrive diversi modi per contrassegnare una macchina virtuale Linux in Azure tramite il modello di distribuzione Resource Manager. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Azure supporta attualmente fino a 50 tag per risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente. Si noti che i tag sono supportati solo per le risorse create tramite il modello di distribuzione Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Assegnazione di tag con Azure CLI

Per iniziare, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure ](/cli/azure/install-azure-cli) e aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/reference-index#az-login).

È possibile visualizzare tutte le proprietà per una determinata macchina virtuale, compresi i tag, utilizzando questo comando:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Per aggiungere un nuovo tag della macchina virtuale tramite l'interfaccia della riga di comando di Azure, è possibile utilizzare il comando `azure vm update` insieme al parametro tag **--set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Per rimuovere i tag, è possibile usare il parametro **--remove** nel comando `azure vm update`.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Dopo aver applicato i tag alle risorse tramite l'interfaccia della riga di comando di Azure e il portale, esaminare i dettagli di utilizzo per visualizzare i tag nel portale di fatturazione.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sull'uso dei tag nelle risorse di Azure, vedere [Panoramica di Azure Resource Manager][Azure Resource Manager Overview] e [Uso dei tag per organizzare le risorse di Azure][Using Tags to organize your Azure Resources].
* Per informazioni sull'utilità dei tag nella gestione dell'uso delle risorse di Azure, vedere [Comprendere la fattura per Microsoft Azure][Understanding your Azure Bill] e [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/management/manage-resources-cli.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
