---
title: Creare uno snapshot da un disco rigido virtuale per creare più dischi gestiti identici - Esempio di PowerShell
description: 'Esempio di script di Azure PowerShell: Creare uno snapshot da un VHD per ottenere rapidamente più dischi gestiti identici'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 28c97e1cb5dd329269a822be6b0b1f6f120a6b13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75463733"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Creare uno snapshot da un VHD per ottenere rapidamente più dischi gestiti identici con PowerShell

Questo script crea uno snapshot da un file VHD in un account di archiviazione nella stessa sottoscrizione o in una sottoscrizione diversa. Usare lo script per importare un file VHD specializzato (non generico o preparato con Sysprep) in uno snapshot e quindi usare lo snapshot per creare rapidamente più dischi gestiti. Usarlo anche per importare un VHD di dati in uno snapshot e quindi usare lo snapshot per creare rapidamente più dischi gestiti.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un disco gestito da un file VHD in una sottoscrizione diversa. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Crea la configurazione del disco usata per la creazione del disco. Include tipo di archiviazione, posizione, ID risorsa dell'account di archiviazione in cui è archiviato il VHD di origine e URI VHD del file VHD di origine. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Crea un disco accettando come parametri la configurazione del disco, il nome del disco e il nome del gruppo di risorse. |

## <a name="next-steps"></a>Passaggi successivi

[Creare un disco gestito da uno snapshot](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script PowerShell per la macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
