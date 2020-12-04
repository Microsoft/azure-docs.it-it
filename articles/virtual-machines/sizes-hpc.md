---
title: Dimensioni delle macchine virtuali di Azure-HPC | Microsoft Docs
description: Elenca le diverse dimensioni disponibili per High Performance Computing macchine virtuali in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 21b1004a347dec3a7f2a6460d8b853350bf36ff0
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571046"
---
# <a name="high-performance-computing-vm-sizes"></a>Dimensioni delle macchine virtuali di calcolo ad alte prestazioni

Le macchine virtuali della serie H di Azure sono progettate per offrire prestazioni, scalabilità e efficienza dei costi della classe di leadership per un'ampia gamma di carichi di lavoro HPC reali.

[Serie HBv2](hbv2-series.md) Le macchine virtuali sono ottimizzate per le applicazioni basate sulla larghezza di banda della memoria, ad esempio fluidodinamica, analisi degli elementi finiti e simulazione del serbatoio. Macchine virtuali HBv2 funzionalità 120 processori AMD EPYC 7742 core, 4 GB di RAM per core CPU e nessun multithreading simultaneo. Ogni macchina virtuale HBv2 offre fino a 340 GB al secondo di larghezza di banda di memoria e fino a 4 teraflop di calcolo FP64.

Funzionalità VM HBv2 200 GB/sec Mellanox HDR InfiniBand, mentre le VM serie HB e HC includono 100 GB/sec Mellanox EDR InfiniBand. Ognuno di questi tipi di VM è connesso in un albero Fat senza blocco per prestazioni RDMA ottimizzate e coerenti. Le macchine virtuali HBv2 supportano il routing adattivo e il trasporto con connessione dinamica (DCT, in aggiunta a trasporti standard RC e UD). Queste funzionalità migliorano le prestazioni, la scalabilità e la coerenza delle applicazioni e l'utilizzo di tali funzionalità è fortemente consigliato.

[Serie HB](hb-series.md) Le macchine virtuali sono ottimizzate per le applicazioni basate sulla larghezza di banda della memoria, ad esempio fluidodinamica, analisi degli elementi finiti espliciti e modellazione meteorologica. Macchine virtuali HB funzionalità 60 processori AMD EPYC 7551 core, 4 GB di RAM per core CPU e senza hyperthreading. La piattaforma EPYC AMD offre più di 260 GB al secondo di larghezza di banda di memoria.

[Serie HC](hc-series.md) Le macchine virtuali sono ottimizzate per le applicazioni basate su un calcolo denso, ad esempio l'analisi implicita degli elementi finiti, le dinamiche molecolari e la chimica computazionale. Macchine virtuali HC funzionalità 44 Core processore Intel Xeon Platinum 8168, 8 GB di RAM per core CPU e nessun hyperthreading. La piattaforma Intel Xeon Platinum supporta il ricco ecosistema di strumenti software di Intel, ad esempio la libreria del kernel matematico di Intel.

[Serie H](h-series.md) Le macchine virtuali sono ottimizzate per le applicazioni basate su frequenze di CPU elevate o memoria di grandi dimensioni per i requisiti di base. Le macchine virtuali serie H dispongono di core del processore Intel Xeon E5 2667 V3, 7 o 14 GB di RAM per core CPU e senza hyperthreading. Le funzionalità della serie H 56 GB/sec Mellanox FDR InfiniBand in una configurazione di albero FAT non bloccante per prestazioni RDMA coerenti. Le macchine virtuali serie H supportano Intel MPI 5. x e MS-MPI.

> [!NOTE]
> Le [macchine virtuali a8-a11](./sizes-previous-gen.md#a-series---compute-intensive-instances) sono pianificate per il ritiro in 3/2021. Per altre informazioni, vedere [Guida alla migrazione HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Istanze con supporto per RDMA

La maggior parte delle dimensioni delle VM HPC (HBv2, HB, HC, H16r, H16mr, A8 e A9) è caratterizzata da un'interfaccia di rete per la connettività ad accesso diretto a memoria remota (RDMA). Anche le dimensioni selezionate della [serie N](./nc-series.md) designate con ' r ' (ND40rs_v2, ND24rs, NC24rs_v3, NC24rs_v2 e NC24r) sono in grado di supportare RDMA. Questa interfaccia è aggiunta all'interfaccia di rete Ethernet standard di Azure disponibile nelle altre dimensioni delle macchine virtuali.

Questa interfaccia consente alle istanze con supporto per RDMA di comunicare attraverso una rete InfiniBand (IB), operando a frequenze HDR per HBv2, tariffe EDR per HB, HC, NDv2, tariffe FDR per H16r, H16mr e altre macchine virtuali serie N con supporto per RDMA e tariffe QDR per le VM a8 e A9. Queste funzionalità RDMA possono migliorare la scalabilità e le prestazioni di determinate applicazioni di interfaccia MPI (Message Passing Interface).

> [!NOTE]
> In HPC di Azure sono disponibili due classi di macchine virtuali, a seconda che si tratti di SR-IOV abilitato per InfiniBand. Attualmente, quasi tutte le macchine virtuali abilitate per la generazione, RDMA o InfiniBand in Azure sono abilitate per SR-IOV ad eccezione di H16r, H16mr, NC24r, A8 e A9.
> RDMA è abilitato solo sulla rete InfiniBand (IB) ed è supportato per tutte le VM con supporto per RDMA.
> IP over IB è supportato solo nelle VM abilitate per SR-IOV.
> RDMA non è abilitato sulla rete Ethernet.

- **Sistema operativo** : Linux è molto supportato per le VM HPC. sono comunemente usate distribuzioni come CentOS, RHEL, Ubuntu e SUSE. Per quanto riguarda il supporto di Windows, Windows Server 2016 e versioni più recenti sono supportati in tutte le VM della serie HPC. Windows Server 2012 R2, Windows Server 2012 sono supportati anche nelle VM abilitate per non SR-IOV (H16r, H16mr, A8 e A9). Si noti che [Windows Server 2012 R2 non è supportato in HBv2 e in altre VM con più di 64 core (virtuali o fisici)](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Vedere [Immagini VM](./workloads/hpc/configure.md) per un elenco di immagini di VM supportate nel Marketplace e come è possibile configurarle in modo appropriato.

- **InfiniBand e driver** : per le macchine virtuali abilitate per InfiniBand, i driver appropriati sono necessari per abilitare RDMA. In Linux, per le macchine virtuali abilitate per SR-IOV e non SR-IOV, le immagini di VM CentOS-HPC nel Marketplace sono preconfigurate con i driver appropriati. Le immagini di macchine virtuali Ubuntu possono essere configurate con i driver appropriati seguendo le istruzioni riportate [qui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). Per altri dettagli sulle immagini del sistema operativo Linux pronte per l'uso, vedere [configurare e ottimizzare le VM per il sistema operativo Linux](./workloads/hpc/configure.md) .

   In Linux l' [estensione della macchina virtuale InfiniBandDriverLinux](./extensions/hpc-compute-infiniband-linux.md) può essere usata per installare i driver OFED di Mellanox e abilitare InfiniBand sulle VM serie H e N abilitate per SR-IOV. Altre informazioni sull'abilitazione di InfiniBand in macchine virtuali con supporto per RDMA in [carichi di lavoro HPC](./workloads/hpc/enable-infiniband.md).

   In Windows, l' [estensione della macchina virtuale InfiniBandDriverWindows](./extensions/hpc-compute-infiniband-windows.md) installa i driver di rete diretta di Windows (in VM non SR-IOV) o i driver OFED di Mellanox (in VM SR-IOV) per la connettività RDMA. In alcune distribuzioni di istanze A8 e A9 l'estensione HpcVmDrivers viene aggiunta automaticamente. Si noti che l'estensione della macchina virtuale HpcVmDrivers è deprecata. non verrà aggiornata.

   Per aggiungere l'estensione macchina virtuale a una macchina virtuale, è possibile usare i cmdlet di [Azure PowerShell](/powershell/azure/). Per altre informazioni, vedere [Estensioni e funzionalità della macchina virtuale](./extensions/overview.md). È anche possibile usare estensioni delle macchine virtuali nel [modello di distribuzione classico](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **MPI** : le dimensioni delle VM abilitate per SR-IOV in Azure consentono di usare praticamente qualsiasi versione di MPI con Mellanox OFED. Nelle VM abilitate per non SR-IOV, le implementazioni MPI supportate usano l'interfaccia di Microsoft Network Direct (ND) per la comunicazione tra le macchine virtuali. Di conseguenza, sono supportate solo le versioni Microsoft MPI (MS-MPI) 2012 R2 o versioni successive e Intel MPI 5. x. Le versioni successive (2017, 2018) della libreria di runtime Intel MPI possono o non essere compatibili con i driver RDMA di Azure. Per altri dettagli sulla configurazione di MPI nelle VM HPC in Azure, vedere [Setup MPI for HPC](./workloads/hpc/setup-mpi.md) .

- **Spazio degli indirizzi della rete RDMA** : la rete RDMA in Azure riserva lo spazio degli indirizzi 172.16.0.0/16. Per eseguire applicazioni MPI in istanze distribuite in una rete virtuale di Azure, assicurarsi che lo spazio degli indirizzi di rete virtuale non si sovrapponga alla rete RDMA.

## <a name="cluster-configuration-options"></a>Opzioni di configurazione del cluster

Azure offre varie opzioni per la creazione di cluster di macchine virtuali HPC Windows in grado di comunicare con la rete RDMA, tra cui: 

- **Macchine virtuali**  : distribuire le VM HPC con supporto per RDMA nello stesso set di scalabilità o set di disponibilità (quando si usa il modello di distribuzione Azure Resource Manager). Se si usa il modello di distribuzione classico, distribuire le macchine virtuali nello stesso servizio cloud.

- **Set di scalabilità di macchine virtuali** : in un set di scalabilità di macchine virtuali, assicurarsi di limitare la distribuzione a un singolo gruppo di posizionamento per la comunicazione InfiniBand all'interno del set di scalabilità. In un modello di Resource Manager, ad esempio, impostare la proprietà `singlePlacementGroup` su `true`. Si noti che la dimensione massima del set di scalabilità che può essere attivata con la `singlePlacementGroup` proprietà `true` è limitata alle VM 100 per impostazione predefinita. Se le esigenze di scalabilità dei processi HPC sono superiori a 100 VM in un singolo tenant, è possibile richiedere un aumento, [aprire una richiesta di assistenza clienti online](../azure-portal/supportability/how-to-create-azure-support-request.md) senza alcun addebito. Il limite per il numero di macchine virtuali in un singolo set di scalabilità può essere aumentato a 300. Si noti che quando si distribuiscono macchine virtuali usando i set di disponibilità, il limite massimo è di 200 VM per set di disponibilità.

- **MPI tra macchine virtuali** : se è necessario RDMA (ad esempio, usando la comunicazione MPI) tra macchine virtuali (VM), assicurarsi che le VM si trovino nello stesso set di scalabilità di macchine virtuali o nel set di disponibilità.

- **Azure CycleCloud** : creare un cluster HPC in [Azure CycleCloud](/azure/cyclecloud/) per eseguire i processi MPI.

- **Azure batch** creare un pool di [Azure batch](../batch/index.yml) per eseguire carichi di lavoro MPI. Per usare istanze a elevato uso di calcolo quando si eseguono applicazioni MPI con Azure Batch, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).

- **Microsoft HPC Pack**  -  [HPC Pack](/powershell/high-performance-computing/overview) include un ambiente di runtime per MS-MPI che usa la rete RDMA di Azure in caso di distribuzione in VM Linux con supporto per RDMA. Per le distribuzioni di esempio, vedere [configurare un cluster Linux RDMA con HPC Pack per eseguire applicazioni MPI](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

- **Sottoscrizione di Azure**: per distribuire numerose istanze a elevato utilizzo di calcolo, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Con un [account gratuito di Azure](https://azure.microsoft.com/free/)è possibile usare solo un numero limitato di core di calcolo di Azure.

- **Prezzi e disponibilità**: queste dimensioni di VM sono offerte solo con il piano tariffario Standard. Per informazioni sulla disponibilità nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/) .

- **Quota di core**: potrebbe essere necessario aumentare la quota di core nella sottoscrizione di Azure rispetto al valore predefinito. La sottoscrizione può anche limitare il numero di core che è possibile distribuire in alcune famiglie di dimensioni di macchina virtuale, inclusa la serie H. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](../azure-portal/supportability/how-to-create-azure-support-request.md) senza alcun addebito. I limiti predefiniti possono variare in base alla categoria della sottoscrizione.

  > [!NOTE]
  > Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure. Le quote di Azure sono limiti di credito e non garanzie di capacità. A prescindere dalla quota, viene addebitato solo l'uso dei core effettivamente impiegati.
  
- **Rete virtuale**: non è necessaria una [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) di Azure per usare le istanze a elevato utilizzo di calcolo. Per molte distribuzioni è tuttavia necessaria almeno una rete virtuale di Azure basata sul cloud. Per l'accesso alle risorse locali, è necessaria anche una connessione da sito a sito. Quando è necessaria, creare una nuova rete virtuale per distribuire le istanze. L'aggiunta di una VM a elevato uso di calcolo a una rete virtuale in un gruppo di affinità non è supportata.

- **Ridimensionamento: a** causa dell'hardware specifico, è possibile ridimensionare solo le istanze a elevato utilizzo di calcolo all'interno della stessa famiglia di dimensioni (serie H o serie N). Ad esempio, è possibile ridimensionare una VM della serie H solo da una dimensione della serie H a un'altra. Per alcune macchine virtuali, potrebbe essere necessario prendere in considerazione ulteriori considerazioni sul supporto dei driver InfiniBand e sui dischi NVMe.


## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [configurazione delle macchine virtuali](./workloads/hpc/configure.md), sull' [Abilitazione di InfiniBand](./workloads/hpc/enable-infiniband.md), sulla configurazione di [MPI](./workloads/hpc/setup-mpi.md) e sull'ottimizzazione di applicazioni HPC per Azure in [carichi di lavoro HPC](./workloads/hpc/overview.md).
- Leggere gli ultimi annunci e alcuni esempi HPC e risultati nei [blog della community tecnica di Calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
