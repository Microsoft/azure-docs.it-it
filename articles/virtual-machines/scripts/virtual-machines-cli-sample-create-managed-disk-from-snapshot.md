---
title: Creare un disco gestito da uno snapshot (Linux) - Esempio dell'interfaccia della riga di comando
description: "Esempio di script dell'interfaccia della riga di comando di Azure: creare un disco gestito da uno snapshot"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 7c5e9e16b81c0befb6524a284d63fb2e106bff7d
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322725"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli-linux"></a>Creare un disco gestito da uno snapshot con l'interfaccia della riga di comando (Linux)

Questo script crea un disco gestito da uno snapshot. Può essere usato per ripristinare una macchina virtuale da snapshot dei dischi del sistema operativo e di dati. Creare dischi dati e del sistema operativo gestiti dai rispettivi snapshot e quindi creare una nuova macchina virtuale collegando i dischi gestiti. Collegando i dischi dati creati da snapshot, è anche possibile ripristinare i dischi di dati di una macchina virtuale esistente.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un disco gestito da snapshot. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Ottiene tutte le proprietà di uno snapshot tramite le proprietà del nome e del gruppo di risorse dello snapshot. La proprietà ID viene usata per creare un disco gestito.  |
| [az disk create](/cli/azure/disk) | Crea un disco gestito mediante l'ID dello snapshot di uno snapshot gestito |

## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale collegando un disco gestito come disco del sistema operativo](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale aggiuntiva sono reperibili nella [documentazione della macchina virtuale Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
