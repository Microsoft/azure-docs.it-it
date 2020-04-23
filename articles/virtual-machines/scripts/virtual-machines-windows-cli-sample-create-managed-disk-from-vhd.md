---
title: Creare un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione - Esempio dell'interfaccia della riga di comando
description: "Esempio di script dell'interfaccia della riga di comando di Azure: creare un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione"
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
ms.openlocfilehash: b0ce935e03a6202ac444987cbecf853ee6717d3b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459521"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Creare un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione con l'interfaccia della riga di comando

Questo script crea un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione. Usare questo script per importare un disco rigido virtuale specializzato (non generico/preparato con Sysprep) nel disco del sistema operativo gestito per creare una macchina virtuale. In alternativa, è possibile usarlo per importare un disco rigido virtuale di dati in un disco di dati gestiti.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un disco gestito da un disco rigido virtuale. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Crea un disco gestito usando l'URI del disco rigido virtuale in un account di archiviazione nella stessa sottoscrizione |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale sono disponibili nella [documentazione della macchina virtuale Windows di Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
