---
title: Pianificare la capacità per il ripristino di emergenza di Hyper-V con Azure Site Recovery
description: Questo articolo illustra come calcolare la capacità quando si configura il ripristino di emergenza con il servizio Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: a5764e44db31755110ac99a3e8e8e0984cdf9604
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87490575"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Pianificare la capacità per il ripristino di emergenza di macchine virtuali Hyper-V 

La [Azure Site Recovery Deployment Planner] (site-recovery-hyper-v-deployment-planner.md) per la distribuzione da Hyper-V ad Azure offre quanto segue:

* Valutazione dell'idoneità delle macchine virtuali in base a numero di dischi, dimensioni dei dischi, operazioni di I/O al secondo, varianza e alcune caratteristiche delle macchine virtuali
* Valutazione della larghezza di banda di rete necessaria rispetto al valore RPO
* Requisiti dell'infrastruttura di Azure
* Requisiti dell'infrastruttura locale
* Indicazioni per l'invio in batch della replica iniziale
* Stima dei costi totali del ripristino di emergenza in Azure


Azure Site Recovery Capacity Planner consente di determinare i requisiti di capacità quando si esegue la replica di macchine virtuali Hyper-V con Azure Site Recovery.

Site Recovery Capacity Planner viene usato per analizzare l'ambiente di origine e i carichi di lavoro. Consente di stimare le esigenze di larghezza di banda, le risorse server necessarie per la posizione di origine e le risorse (ad esempio macchine virtuali e archiviazione) necessarie nella posizione di destinazione.

È possibile eseguire lo strumento in due modalità:

* **Pianificazione rapida**: eseguire lo strumento in questa modalità per ottenere le proiezioni di rete e server in base al numero medio di VM, dischi, archiviazione e frequenza di modifica.
* **Pianificazione dettagliata**: fornisce i dettagli di ogni carico di lavoro a livello di macchina virtuale. Analizzare la compatibilità della macchina virtuale e ottenere le proiezioni di rete e server.

## <a name="before-you-start"></a>Prima di iniziare

* Raccogliere informazioni sull'ambiente, incluse le macchine virtuali, i dischi per ogni macchina virtuale e l'archiviazione per ogni disco.
* Identificare la frequenza di modifica giornaliera (varianza) per i dati replicati. Scaricare lo [strumento di pianificazione della capacità di Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) per ottenere la frequenza di modifica. [Altre informazioni](./hyper-v-deployment-planner-overview.md) su questo strumento. È consigliabile eseguire lo strumento per una settimana per acquisire i valori medi.


## <a name="run-the-quick-planner"></a>Eseguire lo strumento di pianificazione rapida
1. Scaricare e aprire [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). È necessario eseguire le macro. Quando richiesto, selezionare le opzioni necessarie per abilitare la modifica e il contenuto.

2. Nella casella di riepilogo **Select a planner type** (Selezionare un tipo di strumento di pianificazione) selezionare **Quick Planner** (Strumento di pianificazione rapida).

   ![Screenshot dell'opzione selezionare un tipo di pianificatore con Planner rapido selezionato.](./media/site-recovery-capacity-planner/getting-started.png)

3. Nel foglio di lavoro **Capacity Planner** immettere le informazioni necessarie. Compilare tutti i campi contrassegnati con un cerchio rosso nello screenshot seguente:

   a. In **Select your scenario** (Selezionare uno scenario) scegliere **Hyper-V to Azure** (Da Hyper-V ad Azure) o **VMware/Physical to Azure** (Da fisico/VMware ad Azure).

   b. In **Average daily data change rate (%)** (Frequenza di modifica dei dati giornaliera media in %) immettere le informazioni raccolte con lo [strumento di pianificazione della capacità per Hyper-V](./hyper-v-deployment-planner-overview.md) o con [Azure Site Recovery Deployment Planner](./site-recovery-deployment-planner.md).

   c. L'impostazione **Compression** (Compressione) non viene usata in caso di replica di macchine virtuali Hyper-V in Azure. Per la compressione usare un'appliance di terze parti, ad esempio Riverbed.

   d. In **Retention in days** (Conservazione in giorni) specificare il numero di giorni in cui le repliche verranno conservate.

   e. In **Number of hours in which initial replication for the batch of virtual machines should complete** (Numero di ore in cui deve essere completata la replica iniziale per il batch delle macchine virtuali) e in **Number of virtual machines per initial replication batch** (Numero di macchine virtuali per batch di replica iniziale) immettere le impostazioni usate per calcolare i requisiti della replica iniziale. Quando Site Recovery viene distribuito, viene caricato l'intero set di dati iniziale.

   ![Screenshot del foglio di Capacity Planner, che mostra le informazioni di input obbligatorie.](./media/site-recovery-capacity-planner/inputs.png)

4. Dopo aver immesso i valori per l'ambiente di origine, l'output visualizzato include:

   * **Bandwidth required for delta replication (in Megabits/sec)** (Larghezza di banda necessaria per la replica differenziale in Mbps): la larghezza di banda di rete per la replica differenziale viene calcolata in base alla frequenza di modifica giornaliera media dei dati.
   * **Bandwidth required for initial replication (in Megabits/sec)** (Larghezza di banda necessaria per la replica iniziale in Mbps): la larghezza di banda di rete per la replica iniziale viene calcolata in base ai valori della replica iniziale immessi.
   * **Storage required (in GBs)** (Spazio di archiviazione necessario in GB): lo spazio totale di archiviazione di Azure necessario.
   * **Total IOPS on Standard Storage** (Totale operazioni di I/O al secondo in archiviazione Standard): il numero viene calcolato in base alla dimensione unitaria delle operazioni di I/O al secondo pari a 8K sul totale degli account di archiviazione Standard. Per lo strumento di pianificazione rapida, il numero viene calcolato in base a tutti i dischi di macchine virtuali di origine e alla frequenza di modifica giornaliera dei dati. Per lo strumento di pianificazione dettagliata, il numero viene invece calcolato in base al numero totale di macchine virtuali con mapping alle macchine virtuali standard di Azure e alla frequenza di modifica dei dati su tali macchine virtuali.
   * **Number of Standard storage accounts required** (Numero di account di archiviazione Standard necessari): il numero di account di archiviazione Standard necessari per proteggere le macchine virtuali. Un account di archiviazione standard può contenere fino a 20.000 operazioni di I/O al secondo in tutte le macchine virtuali dello spazio di archiviazione standard. È supportato un massimo di 500 operazioni di I/O al secondo per ogni disco.
   * **Number of Blob disks required** (Numero di dischi BLOB necessari): il numero di dischi che verranno creati nello spazio di archiviazione di Azure.
   * **Number of premium storage accounts required** (Numero di account di archiviazione Premium necessari): il numero totale di account di archiviazione Premium necessari per proteggere le macchine virtuali. Per una macchina virtuale di origine con un numero di operazioni di I/O al secondo elevato (superiore a 20.000) è necessario un account di archiviazione Premium. Un account di archiviazione di questo tipo può contenere fino a 80.000 operazioni di I/O al secondo.
   * **Total IOPS on Premium Storage** (Totale operazioni di I/O al secondo in archiviazione Premium): il numero viene calcolato in base alla dimensione unitaria delle operazioni di I/O al secondo pari a 256K sul totale degli account di archiviazione Premium. Per lo strumento di pianificazione rapida, il numero viene calcolato in base a tutti i dischi di macchine virtuali di origine e alla frequenza di modifica giornaliera dei dati. Per lo strumento di pianificazione dettagliata, il numero viene calcolato in base al numero totale di macchine virtuali con mapping alle macchine virtuali Premium di Azure (serie DS e GS) e alla frequenza di modifica dei dati su tali macchine virtuali.
   * **Numero di server di configurazione necessari**: Mostra il numero di server di configurazione necessari per la distribuzione.
   * **Number of additional Process Servers required** (Numero di server di elaborazione aggiuntivi necessari): indica se sono necessari server di elaborazione aggiuntivi oltre al server di elaborazione in esecuzione nel server di configurazione per impostazione predefinita.
   * **100% di spazio di archiviazione aggiuntivo nell'origine**: indica se è necessario spazio di archiviazione aggiuntivo nella posizione di origine.

      ![Screenshot dell'output visualizzato in base all'input fornito.](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Eseguire lo strumento di pianificazione dettagliata

1. Scaricare e aprire [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). È necessario eseguire le macro. Quando richiesto, selezionare le opzioni necessarie per abilitare la modifica e il contenuto.

2. Nella casella di riepilogo **Select a planner type** (Selezionare un tipo di strumento di pianificazione) selezionare **Detailed Planner** (Strumento di pianificazione dettagliata).

   ![Screenshot dell'opzione Seleziona un tipo di pianificatore con pianificatore dettagliato selezionato.](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Nel foglio di lavoro **Workload Qualification** (Qualifica carico di lavoro) immettere le informazioni necessarie. È necessario compilare tutti i campi contrassegnati.

   a. In **Core processore**specificare il numero totale di core in un server di origine.

   b. In **Memory allocation (in MBs)** (Allocazione memoria in MB) specificare le dimensioni della RAM di un server di origine.

   c. In **Number of NICs** (Numero di schede di interfaccia di rete) specificare il numero di schede di rete in un server di origine.

   d. In **Totale spazio di archiviazione (in GB)** specificare le dimensioni totali dell'archiviazione della macchina virtuale. Se ad esempio il server di origine ha tre dischi da 500 GB ognuno, lo spazio di archiviazione totale è di 1.500 GB.

   e. In **Number of disks attached** (Numero di dischi collegati) specificare il numero totale dei dischi di un server di origine.

   f. In **Disk capacity utilization (%)** (Utilizzo capacità disco in %) specificare l'utilizzo medio.

   g. In **Daily data change rate (%)** (Frequenza di modifica dei dati giornaliera in %) specificare la frequenza di modifica giornaliera dei dati di un server di origine.

   h. In **Mapping Azure VM size** (Dimensioni VM di Azure per mapping) immettere le dimensioni delle macchine virtuali di Azure di cui eseguire il mapping. Se non si vuole eseguire questa operazione manualmente, selezionare **Compute IaaS VMs** (Calcola VM IaaS). Se si inserisce un'impostazione manuale e quindi si seleziona **Compute IaaS VMs** (Calcola VM IaaS), l'impostazione manuale può essere sovrascritta. Il processo di calcolo identifica automaticamente la migliore corrispondenza per quanto riguarda le dimensioni delle macchine virtuali di Azure.

   ![Screenshot del foglio di lavoro di qualificazione del carico di lavoro, che mostra le informazioni di input obbligatorie.](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Se si seleziona **Compute IaaS VMs** (Calcola VM IaaS), lo strumento esegue queste operazioni:

   * Convalida gli input obbligatori.
   * Calcola le operazioni di I/O al secondo e suggerisce la migliore corrispondenza per quanto riguarda le dimensioni per ogni macchina virtuale idonea alla replica in Azure. Se non viene rilevata una macchina virtuale di Azure della dimensione appropriata, viene visualizzato un errore. Se ad esempio il numero di dischi collegati è 65, viene visualizzato un errore perché la dimensione massima per una macchina virtuale di Azure è 64.
   * Suggerisce un account di archiviazione da usare per una macchina virtuale di Azure.
   * Calcola il numero totale di account di archiviazione standard e account di archiviazione premium necessari per il carico di lavoro. Scorrere verso il basso per visualizzare il tipo e l'account di archiviazione di Azure che può essere usato per un server di origine.
   * Completa e ordina il resto della tabella in base al tipo di archiviazione necessaria (standard o Premium) assegnata per una macchina virtuale e il numero di dischi collegati. Per tutte le VM che soddisfano i requisiti per Azure, la colonna **Is VM qualified?** (VM qualificata?) contiene **Yes** (Sì). Se non è possibile eseguire il backup di una macchina virtuale in Azure, viene visualizzato un errore.

Vengono visualizzate le colonne da AA ad AE che forniscono informazioni per ogni macchina virtuale.

![Screenshot che mostra le colonne di output da AA ad AE.](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Esempio
In questo esempio, per sei macchine virtuali con i valori riportati nella tabella lo strumento calcola e assegna la migliore corrispondenza di macchina virtuale di Azure e i requisiti di archiviazione di Azure.

![Screenshot che illustra le assegnazioni delle qualifiche dei carichi di lavoro](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Nell'output dell'esempio si noti quanto segue:

  * La prima è una colonna di convalida per le macchine virtuali, i dischi e la varianza.
  * Per cinque macchine virtuali sono necessari due account di archiviazione standard e un account di archiviazione premium.
  * La VM3 non è idonea alla protezione perché uno o più dischi sono superiori a 1 TB.
  * La VM1 e la VM2 possono usare il primo account di archiviazione standard.
  * La VM4 può usare il secondo account di archiviazione standard.
  * Per VM5 e VM6 è necessario un account di archiviazione Premium ed entrambe possono usare un singolo account.

    > [!NOTE]
    > Le operazioni totali di input/output in archiviazione standard e premium vengono calcolate a livello di macchina virtuale e non a livello di disco. Una macchina virtuale standard è capace di gestire fino a 500 operazioni di I/O al secondo per ogni disco. Se le operazioni di I/O al secondo per disco sono superiori a 500, è necessaria l'archiviazione premium. Se le operazioni di I/O al secondo per un disco sono superiori a 500 ma quelle per i dischi totali delle macchine virtuali rientrano nei limiti supportati delle macchine virtuali standard di Azure, lo strumento di pianificazione sceglie una macchina virtuale standard e non una della serie DS o GS. I limiti delle macchine virtuali di Azure sono dimensioni della macchina virtuale, numero di dischi, numero di schede, CPU e memoria. È necessario aggiornare manualmente la cella del mapping delle dimensioni di Azure con la VM della serie DS o GS appropriata.


Dopo aver immesso tutte le informazioni, fare clic su **Submit data to the planner tool** (Invia dati a strumento di pianificazione) per aprire Capacity Planner. I carichi di lavoro vengono evidenziati per mostrare se sono idonei per la protezione.

### <a name="submit-data-in-capacity-planner"></a>Inviare dati in Capacity Planner
1. Quando si apre il foglio di lavoro di **Capacity Planner**, viene compilato in base alle impostazioni specificate. La parola "carico di lavoro" viene visualizzata nella cella di **origine degli input infra** per indicare che l'input è il foglio di **lavoro di qualificazione del carico di lavoro**

2. Per apportare modifiche, è necessario modificare il foglio di lavoro **Workload Qualification** (Qualifica carico di lavoro) e quindi selezionare nuovamente **Submit data to the planner tool** (Invia dati a strumento di pianificazione).

   ![Screenshot che Mostra gli input modificati e gli output risultanti nel foglio di Capacity Planner.](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Passaggi successivi
[Informazioni su come eseguire](./hyper-v-deployment-planner-overview.md) lo strumento di pianificazione della capacità.
