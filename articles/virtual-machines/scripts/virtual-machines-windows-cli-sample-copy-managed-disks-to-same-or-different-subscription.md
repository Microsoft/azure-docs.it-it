---
title: Copiare i dischi gestiti in una sottoscrizione - Esempio dell'interfaccia della riga di comando
description: "Esempio di script dell'interfaccia della riga di comando di Azure: copiare, o spostare, i dischi gestiti nella stessa sottoscrizione o in una sottoscrizione diversa"
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: b863217057427861112aea2c7ceaffd4152cbfb7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459628"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Copiare i dischi gestiti nella stessa sottoscrizione o in una sottoscrizione diversa con l'interfaccia della riga di comando

Questo script consente di copiare un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa ma nella stessa area. La copia funziona solo quando le sottoscrizioni fanno parte dello stesso tenant AAD.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un nuovo disco gestito nella sottoscrizione di destinazione usando l'ID del disco gestito di origine. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | Ottiene tutte le proprietà di uno snapshot tramite le proprietà del nome e del gruppo di risorse del disco gestito. La proprietà ID viene usata per copiare il disco gestito in una sottoscrizione diversa.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Consente di copiare un disco gestito creando un nuovo disco gestito in una sottoscrizione diversa tramite l'ID e il nome del disco gestito padre.  |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale sono disponibili nella [documentazione della macchina virtuale Windows di Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
