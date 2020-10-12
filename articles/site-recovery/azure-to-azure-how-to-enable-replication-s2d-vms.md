---
title: Replicare le macchine virtuali di Azure che eseguono Spazi di archiviazione diretta con Azure Site Recovery
description: Informazioni su come eseguire la replica di macchine virtuali di Azure che eseguono Spazi di archiviazione diretta usando Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 0b4f18c32639ceb2084febe210a8cfd4c423a0cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135752"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replicare le macchine virtuali di Azure che eseguono Spazi di archiviazione diretta in un'altra area

Questo articolo descrive come abilitare il ripristino di emergenza di macchine virtuali di Azure che eseguono Spazi di archiviazione diretta.

>[!NOTE]
>Per i cluster di Spazi di archiviazione diretta sono supportati solo i punti di ripristino coerenti con l'arresto anomalo del sistema.
>

[Spazi di archiviazione diretta (S2D)](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) è un sistema di archiviazione definito dal software, che consente di creare [cluster guest](https://techcommunity.microsoft.com/t5/failover-clustering/bg-p/FailoverClustering) in Azure.  Un cluster guest in Microsoft Azure è un cluster di failover costituito da macchine virtuali IaaS. Consente di eseguire il failover dei carichi di lavoro delle VM ospitate tra cluster guest, ottenendo un contratto di servizio con disponibilità più elevata per le applicazioni, rispetto a una singola macchina virtuale di Azure. È utile negli scenari in cui una macchina virtuale ospita un'applicazione critica come SQL o con scalabilità orizzontale file server.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Ripristino di emergenza con spazi di archiviazione diretta

In uno scenario tipico si può avere un cluster guest di macchine virtuali in Azure per una resilienza superiore dell'applicazione, ad esempio File server di scalabilità orizzontale. Anche se questo fornisce all'applicazione una disponibilità più elevata, si vogliono proteggere le applicazioni usando Site Recovery per qualsiasi errore a livello di area. Site Recovery replica i dati da un'area di Azure a un'altra e attiva il cluster nell'area di ripristino di emergenza in caso di failover.

Il diagramma seguente mostra un cluster di failover di macchine virtuali di Azure a due nodi che usa spazi di archiviazione diretta.

![Spazi di archiviazione diretta](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Due macchine virtuali di Azure in un cluster di failover Windows, ognuna delle quali con due o più dischi dati.
- S2D sincronizza i dati nel disco dati e presenta le risorse di archiviazione sincronizzate sotto forma di pool di archiviazione.
- Il pool di archiviazione si presenta al cluster di failover come un volume condiviso cluster.
- Il cluster di failover usa il volume condiviso cluster per le unità dati.

**Considerazioni sul ripristino di emergenza**

1. Quando si configura il [cloud di controllo](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) per il cluster, mantenere il cloud di controllo nell'area di ripristino di emergenza.
2. Se si intende eseguire il failover delle macchine virtuali in una subnet situata in un'area di ripristino di emergenza diversa dall'area di origine, dopo il failover occorre modificare l'indirizzo IP del cluster.  Per modificare l'indirizzo IP del cluster è necessario usare lo [script del piano di ripristino Site Recovery.](./site-recovery-runbook-automation.md)</br>
[Script di esempio](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) per eseguire il comando all'interno della macchina virtuale usando l'estensione script personalizzata 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Abilitazione di Site Recovery per il cluster S2D:

1. All'interno dell'insieme di credenziali dei servizi di ripristino fare clic su "+ replica"
1. Selezionare tutti i nodi nel cluster e renderli parte di un [gruppo di coerenza tra più macchine virtuali](./azure-to-azure-common-questions.md#multi-vm-consistency)
1. Selezionare i criteri di replica con la coerenza dell'applicazione disattivata* (è disponibile solo il supporto della coerenza per arresto anomalo del sistema)
1. Abilitare la replica

   ![Protezione di Spazi di archiviazione diretta](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Passare agli elementi replicati per visualizzare lo stato di entrambe le macchine virtuali.
3. Entrambe le macchine virtuali sono protette e vengono anche visualizzate come parte di un gruppo di coerenza tra più macchine virtuali.

   ![Protezione di Spazi di archiviazione diretta](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Creazione di un piano di ripristino
Un piano di ripristino supporta la sequenziazione di vari livelli in un'applicazione multilivello durante un failover. La sequenziazione aiuta a mantenere la coerenza delle applicazioni. Quando si crea un piano di ripristino per un'applicazione Web multilivello, completare la procedura descritta in [Creare un piano di ripristino con Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Aggiunta di macchine virtuali a gruppi di failover

1.  Creare un piano di ripristino aggiungendo le macchine virtuali.
2.  Fare clic su "Personalizza" per raggruppare le VM. Per impostazione predefinita, tutte le VM fanno parte di "Gruppo 1".


### <a name="add-scripts-to-the-recovery-plan"></a>Aggiungere script al piano di ripristino
Per far sì che le applicazioni funzionino correttamente, potrebbe essere necessario eseguire alcune operazioni nelle macchine virtuali di Azure dopo il failover o durante un failover di test. È possibile automatizzare alcune operazioni successive al failover. Ad esempio, qui viene collegato il servizio di bilanciamento del carico e viene modificato l'IP del cluster.


### <a name="failover-of-the-virtual-machines"></a>Failover delle macchine virtuali 
È necessario eseguire il failover di entrambi i nodi delle VM usando il [piano di ripristino](./site-recovery-create-recovery-plans.md) Site Recovery 

![Protezione di Spazi di archiviazione diretta](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Eseguire un failover di test
1.  Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Selezionare il piano di ripristino creato.
3.  Selezionare **Failover di test**.
4.  Per avviare il processo di failover di test, selezionare il punto di recupero e la rete virtuale di Azure.
5.  Quando l'ambiente secondario diventa disponibile, eseguire le convalide.
6.  Al termine delle convalide, per pulire l'ambiente di failover di test, selezionare **Pulisci failover di test**.

Per altre informazioni, vedere [Failover di test in Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Eseguire un failover

1.  Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Selezionare il piano di ripristino creato per le applicazioni SAP.
3.  Selezionare **Failover**.
4.  Per avviare il processo di failover, selezionare il punto di recupero.

Per altre informazioni, vedere [Failover in Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](./azure-to-azure-tutorial-failover-failback.md) sull'esecuzione dei failback.
