---
title: Domande frequenti sulla migrazione di Azure Migrate server
description: Risposte alle domande comuni sull'uso della migrazione del server Azure Migrate per eseguire la migrazione dei computer.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 1f32e9788b2255be373a4db8c159ef2a6b6ccf04
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753842"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrazione di Azure Migrate server: domande comuni

Questo articolo risponde alle domande più comuni sull'Azure Migrate: strumento di migrazione server. Per altre domande, vedere le risorse seguenti:

- [Domande generali](resources-faq.md) su Azure migrate
- Domande sull' [appliance Azure migrate](common-questions-appliance.md)
- Domande su [individuazione, valutazione e visualizzazione delle dipendenze](common-questions-discovery-assessment.md)
- Risposte alle domande nel [forum Azure migrate](https://aka.ms/AzureMigrateForum)

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Azure Migrate convertire i computer basati su UEFI in computer basati su BIOS ed eseguirne la migrazione in Azure come macchine virtuali di Azure di prima generazione?
Azure Migrate: lo strumento di migrazione server consente di eseguire la migrazione di tutti i computer basati su UEFI in Azure come macchine virtuali di Azure di seconda generazione. La conversione di macchine virtuali basate su UEFI in macchine virtuali basate su BIOS non è più supportata. Si noti che viene eseguita la migrazione di tutti i computer basati su BIOS in Azure come solo VM di prima generazione di Azure.

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>Come è possibile eseguire la migrazione di computer basati su UEFI in Azure come macchine virtuali di Azure di prima generazione?
Azure Migrate: lo strumento di migrazione server consente di migrare i computer basati su UEFI in Azure come macchine virtuali di Azure di seconda generazione. Se si vuole eseguire la migrazione alle macchine virtuali di Azure di prima generazione, convertire il tipo di avvio in BIOS prima di avviare la replica e quindi usare lo strumento di migrazione Azure Migrate: Server per eseguire la migrazione ad Azure.
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>Quali sistemi operativi sono supportati per la migrazione di computer basati su UEFI in Azure?

| **Sistemi operativi supportati per i computer basati su UEFI** | **Da VMware senza agente ad Azure**                                                                                                             | **Da Hyper-V senza agente ad Azure** | **VMware, fisico e altri cloud basati su agenti in Azure** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019, 2016, 2012 R2, 201                 | S                                                                                                                                         | S                              | S                                                          |
| Windows 10 Pro, Windows 10 Enterprise                   | S                                                                                                                                         | S                              | S                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | S                                                                                                                                         | S                              | S                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | S                                                                                                                                         | S                              | S                                                          |
| Ubuntu Server 16.04, 18.04, 19.04, 19.10                | S                                                                                                                                         | S                              | S                                                          |
| RHEL 8,1, 8,0, 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x        | S<br>                 _RHEL 8. x richiede la [preparazione manuale](./prepare-for-migration.md#linux-machines)_   | S                              | S                                                          |
| Centesimo OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 6. x               | S<br>_Il centesimo OS 8. x richiede la [preparazione manuale](./prepare-for-migration.md#linux-machines)_ | S                              | S                                                          |
| Oracle Linux 7.7, 7.7-CI                                |  S                                                                                                                                        | S                              | S                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>È possibile usare l'insieme di credenziali di servizi di ripristino creato da Azure Migrate per gli scenari di ripristino di emergenza?
Non è consigliabile usare l'insieme di credenziali di servizi di ripristino creato da Azure Migrate per gli scenari di ripristino di emergenza. Questa operazione può causare errori di avvio della replica in Azure Migrate. 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>Dove installare l'appliance di replica per le migrazioni basate su agenti?

Il dispositivo di replica deve essere installato in un computer dedicato. L'appliance di replica non deve essere installata in una macchina virtuale di origine che si vuole replicare o nell'appliance di individuazione e valutazione di Azure Migrate eventualmente installata in precedenza. Per ulteriori informazioni, seguire l' [esercitazione](./tutorial-migrate-physical-virtual-machines.md) .

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>Come è possibile eseguire la migrazione delle istanze di AWS EC2 in Azure?

Leggere questo [articolo](./tutorial-migrate-aws-virtual-machines.md) per individuare, valutare ed eseguire la migrazione delle istanze di AWS EC2 in Azure.

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>È possibile eseguire la migrazione di macchine virtuali AWS che eseguono il sistema operativo Amazon Linux?

Non è possibile eseguire la migrazione delle macchine virtuali che eseguono Amazon Linux così come sono, perché il sistema operativo Amazon Linux è supportato solo in AWS.
Per eseguire la migrazione dei carichi di lavoro in esecuzione in Amazon Linux, è possibile creare una macchina virtuale CentOS/RHEL in Azure ed eseguire la migrazione del carico di lavoro in esecuzione nel computer Linux AWS usando un approccio pertinente per la migrazione del carico di lavoro. A seconda del carico di lavoro, ad esempio, è possibile che siano disponibili strumenti specifici per il carico di lavoro per facilitare la migrazione, ad esempio per i database o gli strumenti di distribuzione nel caso di server Web.

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Quali aree geografiche sono supportate per la migrazione con Azure Migrate?

Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>È possibile usare lo stesso progetto Azure Migrate per eseguire la migrazione a più aree?

Sebbene sia possibile creare valutazioni per più aree in un progetto di Azure Migrate, è possibile usare un progetto Azure Migrate per eseguire la migrazione dei server solo a un'area di Azure. È possibile creare altri progetti di Azure Migrate per ogni area in cui è necessario eseguire la migrazione.

- Per le migrazioni VMware senza agenti, l'area di destinazione viene bloccata una volta abilitata la prima replica.
- Per le migrazioni basate su agenti (VMware, server fisici e server da altri cloud), l'area di destinazione viene bloccata quando si fa clic sul pulsante "crea risorse" nel portale durante la configurazione dell'appliance di replica.
- Per le migrazioni Hyper-V senza agenti, l'area di destinazione viene bloccata quando si fa clic sul pulsante "crea risorse" nel portale durante la configurazione del provider di replica Hyper-V.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>È possibile usare lo stesso progetto Azure Migrate per eseguire la migrazione a più sottoscrizioni? 

Sì, è possibile eseguire la migrazione a più sottoscrizioni nella stessa area di destinazione per un progetto Azure Migrate. È possibile selezionare la sottoscrizione di destinazione durante l'abilitazione della replica per un computer o un set di computer. L'area di destinazione è bloccata prima della replica per le migrazioni VMware senza agenti e durante l'installazione del provider Hyper-V e delle migrazioni basate su agenti rispettivamente per le migrazioni basate su agenti e per le migrazioni Hyper-V senza agenti.

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Quali sono le opzioni di migrazione in Azure Migrate: migrazione del server?

Lo strumento di migrazione Azure Migrate: Server offre due opzioni per eseguire migrazioni di Server/macchine virtuali di origine in Azure, ovvero migrazione senza agente e migrazione basata su agenti.

Indipendentemente dall'opzione di migrazione scelta, il primo passaggio per eseguire la migrazione di un server tramite migrazione di Azure: la migrazione del server consiste nell'abilitare la replica per il server. In questo modo viene eseguita una replica iniziale dei dati VM/Server in Azure. Al termine della replica iniziale, viene stabilita una replica in corso (sincronizzazione Delta in corso) per eseguire la migrazione dei dati incrementali in Azure. Quando l'operazione raggiunge la fase di sincronizzazione Delta, è possibile scegliere di eseguire la migrazione ad Azure in qualsiasi momento.  

Di seguito sono riportate alcune considerazioni da tenere presenti durante la scelta dell'opzione di migrazione.

Le **migrazioni senza agenti** non richiedono la distribuzione di software (agenti) nelle VM o nei server di origine di cui viene eseguita la migrazione. L'opzione senza agente orchestra la replica integrando con la funzionalità fornita dal provider di virtualizzazione.
Le opzioni di replica senza agenti sono disponibili per le macchine virtuali [VMware](./tutorial-migrate-vmware.md) e [Hyper-V](./tutorial-migrate-hyper-v.md).

Le **migrazioni basate su agenti** richiedono l'installazione di software Azure migrate (agenti) nelle VM/computer di origine da migrare. L'opzione basata su agenti non si basa sulla piattaforma di virtualizzazione per la funzionalità di replica e pertanto può essere utilizzata con qualsiasi server che esegue un'architettura x86/x64 e una versione di un sistema operativo supportata dal metodo di replica basato su agenti.

È possibile usare l'opzione di migrazione basata su agenti per le [macchine virtuali VMware](./tutorial-migrate-vmware-agent.md), le VM [Hyper-V](./tutorial-migrate-physical-virtual-machines.md), i [server fisici](./tutorial-migrate-physical-virtual-machines.md), le [VM in esecuzione in AWS](./tutorial-migrate-aws-virtual-machines.md), le macchine virtuali in esecuzione in GCP o le VM in esecuzione in un provider di virtualizzazione diverso. La migrazione basata su agenti considera i computer come server fisici ai fini della migrazione.

Sebbene la migrazione senza agente offra maggiore praticità e semplicità sulle opzioni di replica basate su agenti per gli scenari supportati (VMWare e Hyper-V), è consigliabile usare lo scenario basato su agenti per i casi d'uso seguenti:

- Ambiente vincolato IOPS: la replica senza agenti USA snapshot e utilizza IOPS/larghezza di banda di archiviazione. È consigliabile usare il metodo di migrazione basato su agenti se sono presenti vincoli sull'archiviazione/IOPS nell'ambiente in uso.
- Se non si dispone di un server vCenter, è possibile gestire le macchine virtuali VMware come server fisici e utilizzare il flusso di lavoro di migrazione basato su agenti.

Per altre informazioni, vedere questo [articolo](./server-migrate-overview.md) per confrontare le opzioni di migrazione per le migrazioni VMware.

## <a name="how-does-agentless-migration-work"></a>Come funziona la migrazione senza agenti?

Azure Migrate: la migrazione del server fornisce opzioni di replica senza agenti per la migrazione di macchine virtuali VMware e macchine virtuali Hyper-V che eseguono Windows o Linux. Lo strumento fornisce inoltre un'opzione di replica aggiuntiva basata su agenti per i server Windows e Linux che possono essere usati per eseguire la migrazione di server fisici, nonché di macchine virtuali x86/x64 in VMware, Hyper-V, AWS, GCP e così via. L'opzione di replica basata su agente richiede l'installazione del software dell'agente sul server o sulla macchina virtuale di cui è in corso la migrazione, mentre nell'opzione senza agente non è necessario installare software nelle macchine virtuali stesse, offrendo quindi maggiore praticità e semplicità rispetto all'opzione di replica basata su agenti.

L'opzione di replica senza agenti funziona usando i meccanismi forniti dal provider di virtualizzazione (VMware, Hyper-V). Nel caso di macchine virtuali VMware, il meccanismo di replica senza agenti USA snapshot VMware e la tecnologia di rilevamento dei blocchi modificata VMware per replicare i dati dai dischi delle macchine virtuali. Questo meccanismo è simile a quello usato da molti prodotti di backup. Nel caso di macchine virtuali Hyper-V, il meccanismo di replica senza agenti usa gli snapshot delle VM e la funzionalità di rilevamento delle modifiche della replica Hyper-V per replicare i dati dai dischi delle macchine virtuali.

Quando la replica è configurata per una macchina virtuale, viene prima di tutto eseguita una fase di replica iniziale. Durante la replica iniziale viene effettuato uno snapshot della macchina virtuale e una copia completa dei dati dai dischi degli snapshot viene replicata nei dischi gestiti della sottoscrizione. Al termine della replica iniziale per la macchina virtuale, il processo di replica esegue la transizione a una fase di replica incrementale (delta replication). Nella fase di replica incrementale, le modifiche apportate ai dati dopo l'ultimo ciclo di replica completato vengono replicate e applicate periodicamente ai dischi gestiti di replica, mantenendo la replica sincronizzata con le modifiche che si verificano nella macchina virtuale. Nel caso di macchine virtuali VMware, viene usata la tecnologia di rilevamento blocchi VMware modificata per tenere traccia delle modifiche tra i cicli di replica. All'inizio del ciclo di replica, viene creato uno snapshot della macchina virtuale e il rilevamento dei blocchi modificati viene usato per ottenere le modifiche tra lo snapshot corrente e l'ultimo snapshot replicato correttamente. In questo modo, è necessario replicare solo i dati che sono stati modificati dopo l'ultimo ciclo di replica completato per mantenere sincronizzata la replica per la macchina virtuale. Alla fine di ogni ciclo di replica, lo snapshot viene rilasciato e viene eseguito il consolidamento dello snapshot per la macchina virtuale. Analogamente, nel caso di macchine virtuali Hyper-V, il motore di rilevamento delle modifiche della replica Hyper-V viene usato per tenere traccia delle modifiche tra cicli di replica consecutivi.
Quando si esegue l'operazione di migrazione in una macchina virtuale di replica, è possibile arrestare la macchina virtuale locale ed eseguire una replica incrementale finale per evitare la perdita di dati. Quando si esegue l'opzione migrate, i dischi gestiti di replica corrispondenti alla macchina virtuale vengono usati per creare la macchina virtuale in Azure.

Per iniziare, vedere le esercitazioni sulla migrazione senza agenti di [VMware](./tutorial-migrate-vmware.md) e sulla [migrazione senza agenti Hyper-V](./tutorial-migrate-hyper-v.md) .

## <a name="how-does-agent-based-migration-work"></a>Funzionamento della migrazione basata su agenti

Oltre alle opzioni di migrazione senza agenti per le macchine virtuali VMware e Hyper-V, lo strumento di migrazione server fornisce un'opzione di migrazione basata su agente per eseguire la migrazione di server Windows e Linux in esecuzione su server fisici o in esecuzione come macchine virtuali x86/x64 in VMware, Hyper-V, AWS, Google Cloud Platform e così via.

Il metodo di migrazione basato su agenti usa il software di Agent installato nel server di cui viene eseguita la migrazione per replicare i dati del server in Azure. Il processo di replica usa un'architettura di offload in cui l'agente inoltra i dati di replica a un server di replica dedicato denominato appliance di replica o server di configurazione (o a un server di elaborazione con scalabilità orizzontale). [Altre](./agent-based-migration-architecture.md) informazioni sul funzionamento dell'opzione di migrazione basata su agente. 

Nota: il dispositivo di replica è diverso dal dispositivo di individuazione Azure Migrate e deve essere installato in un computer separato o dedicato.

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>Ricerca per categorie misura il requisito di larghezza di banda per le migrazioni?

La larghezza di banda per la replica dei dati in Azure dipende da una serie di fattori ed è una funzione della velocità con cui l'appliance Azure Migrate locale può leggere e replicare i dati in Azure. La replica è costituita da due fasi: la replica iniziale e la delta replication.

Quando viene avviata la replica per una macchina virtuale, si verifica un ciclo di replica iniziale in cui vengono replicate le copie complete dei dischi. Al termine della replica iniziale, i cicli di replica incrementale (cicli Delta) vengono pianificati periodicamente per trasferire le modifiche apportate dopo il ciclo di replica precedente.

### <a name="agentless-vmware-vm-migration"></a>Migrazione di macchine virtuali VMware senza agente

È possibile calcolare il requisito di larghezza di banda in base al volume dei dati necessari per essere spostati nell'onda e nel tempo entro i quali si desidera che la replica iniziale venga completata (idealmente si desidera eseguire la replica iniziale hanno completato almeno 3-4 giorni prima della finestra di migrazione effettiva per concedere tempo sufficiente per eseguire una migrazione di test prima della finestra effettiva e per ridurre al minimo i tempi di inattività durante la finestra.

È possibile stimare la larghezza di banda o l'ora necessaria per la migrazione di macchine virtuali VMware senza agenti usando la formula seguente:

Tempo necessario per completare la replica iniziale = {dimensioni dei dischi (o dimensioni usate se disponibili) * 0,7 (presupponendo una media di compressione del 30%-stima conservativa)}/bandwidth disponibili per la replica.

### <a name="agent-based-vmware-vm-migration"></a>Migrazione di macchine virtuali VMware basate su agenti

Per un metodo di replica basato su agenti, Deployment Planner può aiutare a profilare l'ambiente per la varianza dei dati e a prevedere il requisito di larghezza di banda necessario. Per altre informazioni, vedere questo [articolo](./agent-based-migration-architecture.md#plan-vmware-deployment). 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Per la replica VMware senza agente, Ricerca per categorie la replica di limitazione delle richieste in uso Azure Migrate Appliance?  

È possibile limitare l'uso di NetQosPolicy. Ad esempio:

Il AppNamePrefix da usare in NetQosPolicy è "GatewayWindowsService.exe". È possibile creare un criterio nell'appliance Azure Migrate per limitare il traffico di replica dall'appliance creando un criterio come quello seguente:

Nome New-NetQosPolicy "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>In che modo i dati vengono trasmessi da un ambiente locale ad Azure? Crittografato prima della trasmissione?

Il Azure Migrate Appliance nel caso di replica senza agente comprime i dati e li crittografa prima del caricamento. I dati vengono trasmessi tramite un canale di comunicazione protetto su HTTPS e usano TLS 1,2 o versione successiva. Inoltre, archiviazione di Azure crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud (crittografia dei dati inattivi).  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>In che modo la percentuale di varianza influisce sulla replica senza agenti?

Poiché la replica senza agenti si riduce nei dati, il *modello di varianza* è più importante della frequenza di *varianza*. Quando un file viene scritto di nuovo e anche in questo caso, la frequenza non ha un notevole effetto. Tuttavia, un modello in cui viene scritto ogni altro settore causa una varianza elevata nel ciclo successivo. Poiché si riduce al minimo la quantità di dati trasferiti, è possibile ridurre il più possibile i dati prima di pianificare il ciclo successivo.

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Con quale frequenza viene pianificato un ciclo di replica?

La formula per pianificare il ciclo di replica successivo è (tempo ciclo precedente/2) o un'ora, a seconda del valore maggiore.

Se, ad esempio, una macchina virtuale impiega quattro ore per un ciclo Delta, il ciclo successivo viene pianificato in due ore e non nell'ora successiva. Il processo è diverso immediatamente dopo la replica iniziale, quando il primo ciclo Delta viene pianificato immediatamente.

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>Ricerca per categorie eseguire la migrazione di Windows Server 2003 in esecuzione su VMware/Hyper-V ad Azure?

Il [supporto esteso di Windows Server 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) è terminato il 14 luglio 2015.  Il team di supporto di Azure continua a contribuire alla risoluzione dei problemi che riguardano l'esecuzione di Windows Server 2003 in Azure. Tuttavia, questo supporto è limitato a problemi che non richiedono la risoluzione dei problemi a livello di sistema operativo o le patch.
La migrazione delle applicazioni nelle istanze di Azure che eseguono una versione più recente di Windows Server è l'approccio consigliato per assicurarsi di sfruttare efficacemente la flessibilità e l'affidabilità del cloud di Azure.

Tuttavia, se si sceglie di eseguire la migrazione di Windows Server 2003 ad Azure, è possibile usare lo strumento di migrazione Azure Migrate: server se Windows Server è una macchina virtuale in esecuzione in VMware o Hyper-V, vedere questo articolo per [preparare i computer Windows server 2003 per la migrazione](./prepare-windows-server-2003-migration.md).

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>Qual è la differenza tra le operazioni di migrazione e migrazione dei test?

La migrazione dei test consente di testare e convalidare le migrazioni prima della migrazione effettiva. La migrazione dei test funziona consentendo di creare copie di test della replica di macchine virtuali in un ambiente sandbox in Azure. L'ambiente sandbox è delimitato da una rete virtuale di test specificata. L'operazione di migrazione dei test non è problematica, con le applicazioni che continuano a essere eseguite nell'origine, consentendo di eseguire test su una copia clonata in un ambiente sandbox isolato. È possibile eseguire più test in base alle esigenze per convalidare la migrazione, eseguire il test delle app e risolvere eventuali problemi prima della migrazione effettiva.

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>Windows Server 2008 e 2008 R2 saranno supportati in Azure dopo la migrazione?

È possibile eseguire la migrazione dei server Windows Server 2008 e 2008 R2 locali in macchine virtuali di Azure e ottenere gli aggiornamenti della sicurezza estesi per tre anni dopo la data di fine del supporto senza costi aggiuntivi rispetto al costo dell'esecuzione della macchina virtuale. Per eseguire la migrazione dei carichi di lavoro di Windows Server 2008 e 2008 R2, è possibile usare lo strumento di migrazione Azure Migrate: Server.

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>È disponibile un'opzione di rollback per Azure Migrate?

È possibile usare l'opzione di migrazione test per convalidare le funzionalità e le prestazioni dell'applicazione in Azure. È possibile eseguire un numero qualsiasi di migrazioni di test ed eseguire la migrazione finale dopo aver stabilito la confidenza tramite l'operazione di migrazione dei test. Una migrazione di test non ha alcun effetto sul computer locale, che rimane operativo e continua la replica fino a quando non si esegue la migrazione effettiva. Se sono stati riscontrati errori durante la migrazione di test UAT, è possibile scegliere di posticipare la migrazione finale e di eseguire la replica o la macchina virtuale di origine in Azure. È possibile ritentare la migrazione finale dopo aver risolto gli errori.  
Nota: dopo aver eseguito una migrazione finale ad Azure e la macchina di origine locale è stata arrestata, non è possibile eseguire il rollback da Azure all'ambiente locale.

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>È possibile selezionare la rete virtuale e la subnet da usare per le migrazioni di test?

È possibile selezionare una rete virtuale per le migrazioni di test. La subnet viene selezionata automaticamente in base alla logica seguente:

- Se una subnet di destinazione (diversa dall'impostazione predefinita) è stata specificata come input durante l'abilitazione della replica, Azure Migrate priorità utilizzando una subnet con lo stesso nome nella rete virtuale selezionata per la migrazione di test.
- Se non viene trovata la subnet con lo stesso nome, Azure Migrate seleziona la prima subnet disponibile in ordine alfabetico che non è una subnet gateway/applicazione/firewall/Bastion.

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>Perché il pulsante di migrazione test è disabilitato per il server?

Il pulsante di migrazione test potrebbe trovarsi in uno stato disabilitato negli scenari seguenti:

- Non è possibile avviare una migrazione di test fino a quando non è stata completata una replica iniziale per la macchina virtuale. Il pulsante test migrazione verrà disabilitato fino al completamento del processo IR. È possibile eseguire una migrazione di test quando la macchina virtuale si trova in una fase di sincronizzazione Delta.
- Il pulsante può essere disabilitato se una migrazione di test è già stata completata, ma non è stata eseguita una pulizia della migrazione di test per tale macchina virtuale. Eseguire una pulizia della migrazione di test e ripetere l'operazione.

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>Cosa accade se non si esegue la pulizia della migrazione di test?

La migrazione di test simula la migrazione effettiva creando una macchina virtuale di Azure di test usando i dati replicati. Il server verrà distribuito con una copia temporizzata dei dati replicati nel gruppo di risorse di destinazione (selezionato durante l'abilitazione della replica) con un suffisso "-test". Le migrazioni di test sono progettate per la convalida delle funzionalità del server, in modo da ridurre al minimo i problemi di post-migrazione. Se la migrazione di test non viene pulita dopo il test, la macchina virtuale di test continuerà a essere eseguita in Azure e comporterà addebiti. Per eseguire la pulizia dopo una migrazione di test, passare alla visualizzazione computer di replica nello strumento di migrazione server e usare l'azione "Pulisci test migrazione" nel computer.

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>È possibile eseguire la migrazione di Active Directory controller di dominio usando Azure Migrate?

Lo strumento di migrazione server è indipendente dall'applicazione e funziona per la maggior parte delle applicazioni. Quando si esegue la migrazione di un server utilizzando lo strumento di migrazione del server, viene eseguita la migrazione di tutte le applicazioni installate nel server. Tuttavia, per alcune applicazioni, metodi di migrazione alternativi diversi dalla migrazione del server possono essere più adatti per la migrazione.  Per Active Directory, nel caso di ambienti ibridi in cui il sito locale è connesso all'ambiente Azure, è possibile estendere la directory in Azure aggiungendo controller di dominio aggiuntivi in Azure e configurando la replica Active Directory. Se si esegue la migrazione in un ambiente isolato in Azure che richiede i propri controller di dominio (o il test delle applicazioni in un ambiente sandbox), è possibile eseguire la migrazione dei server usando lo strumento di migrazione server.

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>Cosa accade se non si arresta la replica dopo la migrazione?

Quando si arresta la replica, lo strumento di migrazione Azure Migrate: server pulisce i dischi gestiti nella sottoscrizione creata per la replica. Se non si arresta la replica dopo una migrazione, si continuerà a sostenere i costi per questi dischi. L'arresto della replica non avrà alcun effetto sui dischi collegati a computer di cui è già stata eseguita la migrazione.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>È necessario VMware vCenter per eseguire la migrazione di macchine virtuali VMware?

Per eseguire la migrazione di [macchine virtuali VMware](server-migrate-overview.md) con la migrazione senza agente o basata su agenti VMware, gli host ESXi in cui si trovano le macchine virtuali devono essere gestiti da server vCenter. Se non si dispone di server vCenter, è possibile eseguire la migrazione di macchine virtuali VMware eseguendone la migrazione come server fisici. [Altre informazioni](migrate-support-matrix-physical-migration.md)

## <a name="can-i-upgrade-my-os-while-migrating"></a>È possibile aggiornare il sistema operativo durante la migrazione?

Lo strumento di migrazione del server Azure Migrate: supporta attualmente solo migrazioni like-for-like. Lo strumento non supporta l'aggiornamento della versione del sistema operativo durante la migrazione. Il computer migrato avrà lo stesso sistema operativo del computer di origine.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>Sono state distribuite due o più appliance per individuare le macchine virtuali nel server vCenter. Tuttavia, quando si tenta di eseguire la migrazione delle macchine virtuali, vengono visualizzate solo le macchine virtuali corrispondenti a una delle appliance.

Se sono configurate più appliance, non devono esistere sovrapposizioni tra le macchine virtuali negli account vCenter forniti. Un'individuazione con una sovrapposizione di questo tipo è uno scenario non supportato.

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>Ricerca per categorie verificare se la macchina virtuale è stata migrata correttamente?

Dopo aver eseguito la migrazione della macchina virtuale o del server, è possibile visualizzare e gestire la macchina virtuale dalla pagina macchine virtuali. Connettersi alla VM di cui è stata eseguita la migrazione per verificare.
In alternativa, è possibile esaminare lo stato del processo per l'operazione per controllare se la migrazione è stata completata correttamente. Se vengono visualizzati errori, risolverli e ripetere l'operazione di migrazione.

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>È possibile consolidare più macchine virtuali di origine in una macchina virtuale durante la migrazione?

Le funzionalità di migrazione del server Azure Migrate supportano attualmente come le migrazioni di come. Non è supportato il consolidamento dei server o l'aggiornamento del sistema operativo come parte della migrazione. 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>In che modo la replica senza agenti influisce sui server VMware?

La replica senza agenti produce un certo effetto sulle prestazioni per gli host server VMware vCenter e VMware ESXi. Poiché la replica senza agenti USA snapshot, USA IOPS sull'archiviazione, quindi è necessaria una larghezza di banda di archiviazione di IOPS. Non è consigliabile usare la replica senza agenti se sono presenti vincoli sull'archiviazione o IOPs nell'ambiente in uso.


## <a name="next-steps"></a>Passaggi successivi

Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).