---
title: Abilitare la replica per un disco della macchina virtuale di Azure aggiunto in Azure Site Recovery
description: Questo articolo descrive come abilitare la replica per un disco aggiunto a una macchina virtuale di Azure abilitata per il ripristino di emergenza con Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973802"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Abilitare la replica per un disco aggiunto a una macchina virtuale di Azure


Questo articolo descrive come abilitare la replica per i dischi dati aggiunti a una macchina virtuale di Azure già abilitata per il ripristino di emergenza in un'altra area di Azure, usando [Azure Site Recovery](site-recovery-overview.md).

L'abilitazione della replica per un disco aggiunto a una macchina virtuale è supportata per le macchine virtuali di Azure con dischi gestiti.

Quando si aggiunge un nuovo disco a una macchina virtuale di Azure che esegue la replica in un'altra area di Azure, si verifica quanto segue:

-   L'integrità della replica per la VM Visualizza un avviso e una nota nel portale informa che uno o più dischi sono disponibili per la protezione.
-   Se si Abilita la protezione per i dischi aggiunti, l'avviso scomparirà dopo la replica iniziale del disco.
-   Se si sceglie di non abilitare la replica per il disco, è possibile scegliere di ignorare l'avviso.

![Nuovo disco aggiunto](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Prima di iniziare

Questo articolo presuppone che sia già stato configurato il ripristino di emergenza per la macchina virtuale a cui si sta aggiungendo il disco. In caso contrario, seguire l' [esercitazione sul ripristino di emergenza da Azure ad Azure](azure-to-azure-tutorial-enable-replication.md).

## <a name="enable-replication-for-an-added-disk"></a>Abilitare la replica per un disco aggiunto

Per abilitare la replica per un disco aggiunto, procedere come segue:

1. Nell'insieme di credenziali > **elementi replicati**fare clic sulla macchina virtuale a cui è stato aggiunto il disco.
2. Fare clic su **dischi**e quindi selezionare il disco dati per il quale si desidera abilitare la replica (questi dischi hanno uno stato **non protetto** ).
3.  In **Dettagli disco**fare clic su **Abilita replica**.

    ![Abilitare la replica per il disco aggiunto](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Dopo l'esecuzione del processo di abilitazione della replica e il completamento della replica iniziale, viene rimosso l'avviso di integrità della replica per il problema del disco.



## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.
