---
title: Esempi dell'interfaccia della riga di comando di Azure per Windows
description: Esempi dell'interfaccia della riga di comando di Azure per Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8d57220f79f1349937a279e57a17d51a76bcccb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033632"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Esempi dell'interfaccia della riga di comando di Azure per macchine virtuali Windows

La tabella seguente fornisce collegamenti a script Bash, compilati tramite l'interfaccia della riga di comando di Azure, che distribuiscono macchine virtuali di Windows.

| | |
|---|---|
|**Creare macchine virtualiCreate virtual machines**||
| [Creare una macchina virtuale](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Consente di creare una macchina virtuale Windows con la configurazione minima. |
| [Creare una macchina virtuale completamente configurata](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Consente di creare un gruppo di risorse, una macchina virtuale e tutte le risorse correlate.|
| [Creare macchine virtuali a disponibilità elevataCreate highly available virtual machines](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Consente di creare più macchine virtuali in una configurazione a disponibilità elevata e con bilanciamento del carico. |
| [Creare una macchina virtuale ed eseguire script di configurazione](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Consente di creare una macchina virtuale e usa l'estensione dello script personalizzato di Azure per installare IIS. |
| [Creare una macchina virtuale ed eseguire la configurazione DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Consente di creare una macchina virtuale e usa l'estensione di configurazione dello stato desiderato di Azure per installare IIS. |
|**Gestisci archiviazione**||
| [Creare un disco gestito da un disco rigido virtuale](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crea un disco gestito da un disco rigido virtuale specializzato come disco del sistema operativo o da un disco VHD di dati come disco dati.  |
| [Creare un disco gestito da uno snapshotCreate a managed disk from a snapshot](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crea un disco gestito da uno snapshot. |
| [Copiare un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Copia un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa ma nella stessa area in cui si trova il disco gestito padre. 
| [Esportare uno snapshot come disco rigido virtuale in un account di archiviazione](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Esporta uno snapshot gestito come disco rigido virtuale in un account di archiviazione in un'area diversa. |
| [Esportare il disco rigido virtuale di un disco gestito in un account di archiviazione](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Esporta il disco rigido virtuale sottostante di un disco gestito in un account di archiviazione di un'area diversa. |
| [Copiare uno snapshot nella stessa sottoscrizione o in una sottoscrizione diversa](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Copia uno snapshot nella stessa sottoscrizione o in una sottoscrizione diversa ma nella stessa area dello snapshot padre. |
|**Macchine virtuali di rete**||
| [Proteggere il traffico di rete tra le macchine virtuali](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Consente di creare due macchine virtuali, tutte le risorse correlate, un gruppo di sicurezza di rete interno e uno esterno. |
|**Proteggere le macchine virtuali**||
| [Crittografare una VM e i dischi dati](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crea un'istanza di Azure Key Vault, una chiave di crittografia e un'entità servizio, quindi crittografa una macchina virtuale. |
|**Monitorare le macchine virtuali**||
| [Monitorare una macchina virtuale con Monitoraggio di Azure](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crea una macchina virtuale, installa l'agente di Log Analytics e registra la macchina virtuale in un'area di lavoro Log Analytics.  |
| | |
