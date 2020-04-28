---
title: Configurare il failover/failback in un sito Hyper-V secondario con Azure Site Recovery
description: Informazioni su come eseguire il failover di macchine virtuali Hyper-V in un sito locale secondario e il failback in un sito primario durante il ripristino di emergenza con Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74082591"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Failover e failback di macchine virtuali Hyper-V replicate in un sito locale secondario

Il servizio [Azure Site Recovery](site-recovery-overview.md) gestisce e controlla la replica, il failover e il failback dei computer locali e delle macchine virtuali di Azure.

In questo articolo viene descritto come eseguire il failover di una macchina virtuale Hyper-V gestita in un cloud System Center Virtual Machine Manager (VMM) in un sito VMM secondario. Dopo il failover è possibile eseguire il failback nel sito locale quando disponibile. In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Failover di una macchina virtuale Hyper-V da un cloud VMM primario in un cloud VMM secondario
> * Riprotezione dal sito secondario al sito primario e failback
> * Avvio opzionaale della replica nuovamente da primario a secondario

## <a name="failover-and-failback"></a>Failover e failback

L'operazione di failover e failback comprende tre fasi:

1. **Failover nel sito secondario**: failover di macchine dal sito primario a quello secondario.
2. **Failback dal sito secondario**: replica di macchine virtuali dal sito secondario al sito primario ed esecuzione di un failover pianificato per eseguire il failback.
3. Dopo il failover pianificato, se lo si desidera avviare nuovamente la replica dal sito primario al sito secondario.


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi di aver completato una [esercitazione sul ripristino di emergenza](hyper-v-vmm-test-failover.md) per verificare che tutto funzioni come previsto.
- Per completare il failback, verificare che i server VMM primario e secondario siano connessi a Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Eseguire il failover da primario a secondario

Per le macchine virtuali Hyper-V è possibile eseguire un failover regolare o pianificato.

- Usare un failover regolare per interruzioni impreviste. Quando si esegue questo failover, Site Recovery crea una macchina virtuale nel sito secondario e la accende. Può verificarsi una perdita di dati a seconda dei dati in sospeso che non sono stati sincronizzati.
- È possibile usare un failover pianificato per le attività di manutenzione o durante un'interruzione prevista. Questa opzione offre una perdita di dati pari a zero. Quando viene attivato un failover pianificato, le macchine virtuali di origine vengono arrestate. I dati non sincronizzati vengono sincronizzati e il failover viene attivato. 
- 
  Questa procedura descrive come eseguire un failover regolare.


1. In **Impostazioni** > **elementi replicati** fare clic sulla macchina virtuale > **failover**.
1. Selezionare **Arrestare la macchina prima di iniziare il failover** se si vuole provare ad arrestare le macchine virtuali di origine tramite Site Recovery prima di attivare il failover. Prima di attivare il failover, Site Recovery tenterà anche di sincronizzare i dati locali non ancora inviati al sito secondario. Si noti che il failover continuerà anche se l'arresto non riesce. Nella pagina **Processi** è possibile seguire lo stato del failover.
2. È ora possibile visualizzare la macchina virtuale nel cloud VMM secondario.
3. Dopo la verifica della macchina virtuale, **eseguire il commit** del failover. In questo modo tutti i punti di ripristino disponibili verranno eliminati.

> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.  


## <a name="reverse-replicate-and-failover"></a>Eseguire la replica inversa e il failover

Avviare la replica dal sito secondario al primario ed eseguire il failback nel sito primario. Quando le macchine virtuali sono nuovamente in esecuzione nel sito primario, è possibile replicarle nel sito secondario.  

 
1. Fare clic su VM > fare clic su **Esegui replica inversa**.
2. Al completamento del processo, fare clic su VM > in **Conferma failover pianificato**, verificare la direzione di failover (dal cloud VMM secondario) e selezionare il percorso di origine e di destinazione. 
4. Avviare il failover. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover.
5. Nel cloud VMM primario, controllare che la macchina virtuale sia disponibile.
6. Per avviare di nuovo la replica della macchina virtuale primaria nel sito secondario, fare clic su **Esegui replica inversa**.

## <a name="next-steps"></a>Passaggi successivi
[Rivedere i passaggi](hyper-v-vmm-disaster-recovery.md) per la replica di macchine virtuali Hyper-V in un sito secondario.
