---
title: Esempio di PowerShell - Esportare o copiare uno snapshot come disco rigido virtuale in un account di archiviazione in un'area diversa
description: Esempio di script di Azure PowerShell - Esportare/copiare uno snapshot come disco rigido virtuale in un account di archiviazione nella stessa area o in un'area diversa
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: c554f82224640b87bcb26f9ed592edcd4f133683
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088707"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Esportare/copiare snapshot gestiti come disco rigido virtuale in un account di archiviazione di un'area diversa con PowerShell

Questo script consente di esportare uno snapshot gestito in un account di archiviazione di un'area diversa. Per prima cosa viene generato l'URI di firma di accesso condiviso dello snapshot, che viene poi usato per copiare lo snapshot in un account di archiviazione di un'area diversa. Usare questo script per gestire il backup dei dischi gestiti in un'area diversa per il ripristino di emergenza.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per generare l'URI di firma di accesso condiviso per uno snapshot gestito e copia lo snapshot in un account di archiviazione usando tale URI. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Grant-AzSnapshotAccess](/powershell/module/az.compute/new-azdisk) | Genera l'URI di firma di accesso condiviso per uno snapshot, che viene usato per copiarlo in un account di archiviazione. |
| [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext) | Crea un contesto per l'account di archiviazione usando nome e chiave dell'account. Questo contesto può essere usato per eseguire operazioni di lettura/scrittura sull'account di archiviazione. |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) | Copia il file VHD sottostante di uno snapshot in un account di archiviazione |

## <a name="next-steps"></a>Passaggi successivi

[Creare un disco gestito da un disco rigido virtuale](virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Creare una macchina virtuale da un disco gestito](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di script PowerShell per la macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
