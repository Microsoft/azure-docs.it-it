---
title: Controllo del file system all'avvio di una macchina virtuale di Azure | Microsoft Docs
description: Informazioni su come risolvere il problema del controllo del file system nella macchina virtuale all'avvio | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 196f49a72932906e0a21b3c6c534c79d291a845f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632994"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>All'avvio di una macchina virtuale di Azure viene indicato il controllo in corso del file system

Questo articolo descrive l'errore "Controllo del file system" che può verificarsi all'avvio di una macchina virtuale Windows in Microsoft Azure.


## <a name="symptom"></a>Sintomo 

La macchina virtuale Windows non si avvia. Gli screenshot di avvio disponibili in [Avvia diagnostica](boot-diagnostics.md) indicano che è in esecuzione il processo di controllo del disco (chkdsk.exe) con uno dei seguenti messaggi che indica:

- L'analisi e il ripristino dell'unità (C)
- Il controllo del file system su C:

## <a name="cause"></a>Causa

Se viene rilevato un errore NTFS nel file system, la coerenza del disco viene controllata e riparata automaticamente al successivo riavvio. In genere questa condizione si verifica in caso di riavvio imprevisto della macchina virtuale o di improvvisa interruzione del processo di arresto della macchina virtuale.

## <a name="solution"></a>Soluzione 

> [!TIP]
> Se si dispone di un backup recente della macchina virtuale, è possibile provare a [ripristinare la macchina virtuale dal backup](../../backup/backup-azure-arm-restore-vms.md) per correggere il problema di avvio.

Windows si avvia normalmente dopo il completamento del processo di controllo del disco. Se la macchina virtuale si blocca nel processo di controllo del disco, provare a eseguire il controllo nella macchina virtuale offline:
1. Creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).
2. [Alleghi il disco del sistema operativo a una macchina virtuale di ripristino](troubleshoot-recovery-disks-portal-windows.md).  
3. Nella macchina virtuale di ripristino eseguire il controllo del disco sul disco del sistema operativo collegato. Nell'esempio seguente la lettera di unità del disco del sistema operativo collegato è E: 

    ```console
    chkdsk E: /f
    ```

4. Dopo il complemento del controllo del disco, scollegare il disco dalla macchina virtuale di ripristino e quindi ricollegarlo alla macchina virtuale interessata come disco del sistema operativo. Per altre informazioni, vedere [Risolvere i problemi relativi a una macchina virtuale Windows collegando il disco del sistema operativo a una macchina virtuale di ripristino](troubleshoot-recovery-disks-portal-windows.md).
