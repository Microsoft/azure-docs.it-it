---
title: Creare una VM collegando un disco gestito come disco del sistema operativo - Esempio dell'interfaccia della riga di comando
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una VM collegando un disco gestito come disco del sistema operativo
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 83f0ea094c26a1ff664ef27729731b77d987e7ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86501498"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Creare una macchina virtuale usando un disco del sistema operativo gestito esistente con l'interfaccia della riga di comando

Questo script crea una macchina virtuale collegando un disco gestito esistente come disco del sistema operativo. Usare questo script negli scenari precedenti:
* Creare una VM da un disco del sistema operativo gestito esistente che è stato copiato da un disco gestito in una sottoscrizione diversa
* Creare una VM da un disco gestito esistente che è stato creato da un file VHD specializzato 
* Creare una VM da un disco del sistema operativo gestito esistente che è stato creato da uno snapshot 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per ottenere le proprietà di un disco gestito, collegare un disco gestito a una nuova VM e creare una VM. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az disk show](/cli/azure/disk) | Ottiene le proprietà del disco gestito usando il nome del disco e il nome del gruppo di risorse. La proprietà Id viene usata per collegare un disco gestito a una nuova VM |
| [az vm create](/cli/azure/vm) | Crea una VM utilizzando un disco del sistema operativo gestito |
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
