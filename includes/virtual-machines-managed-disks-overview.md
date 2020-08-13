---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26d6ca641c03395996fdeaa4e54d2a001cf02bef
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88162695"
---
## <a name="benefits-of-managed-disks"></a>Vantaggi dei dischi gestiti

Verranno ora esaminati alcuni dei vantaggi legati all'uso dei dischi gestiti.

### <a name="highly-durable-and-available"></a>Elevati livelli di durabilità e disponibilità

I dischi gestiti sono stati progettati per il 99,999% di disponibilità. I dischi gestiti ottengono questo risultato grazie a tre repliche dei dati, che consentono di garantire una durabilità elevata. In caso di problemi con una o addirittura due repliche, le repliche rimanenti contribuiscono ad assicurare la persistenza dei dati e una tolleranza di errore elevata. Questa architettura ha consentito ad Azure di offrire costantemente durabilità di livello aziendale per i dischi Infrastruttura distribuita come servizio (IaaS, Infrastructure as a Service), con una percentuale di frequenza errori annualizzata pari a ZERO, la migliore del settore.

### <a name="simple-and-scalable-vm-deployment"></a>Distribuzione semplice e scalabile di macchine virtuali

L'uso dei dischi gestiti permette di creare fino a 50.000 **dischi** di macchine virtuali di un tipo in una sottoscrizione per ogni area, consentendo di creare migliaia di **macchine virtuali** in una singola sottoscrizione. Questa funzionalità aumenta ulteriormente la scalabilità dei [set di scalabilità di macchine virtuali](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md), permettendo di creare fino a mille macchine virtuali in un set di scalabilità di macchine virtuali con un'immagine del Marketplace.

### <a name="integration-with-availability-sets"></a>Integrazione con set di disponibilità

I dischi gestiti vengono integrati con una maggiore affidabilità per i set di disponibilità, così che i dischi delle [macchine virtuali in un set di disponibilità](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) siano sufficientemente isolati gli uni dagli altri per evitare singoli punti di errore. I dischi vengono automaticamente posizionati in unità di scala di archiviazione diverse (timbri). Se uno stamp non riesce a causa di un errore hardware o software, hanno esito negativo solo le istanze delle macchine virtuali con dischi in tali stamp. Si prenda ad esempio un'applicazione in esecuzione in cinque macchine virtuali, a loro volta inserite in un set di disponibilità. I dischi di tali macchine virtuali non vengono tutti archiviati nello stesso stamp. In caso di inattività di uno stamp, quindi, le altre istanze dell'applicazione continuano l'esecuzione.

### <a name="integration-with-availability-zones"></a>Integrazione con zone di disponibilità

Managed Disks supporta le [zone di disponibilità](../articles/availability-zones/az-overview.md), una soluzione a disponibilità elevata che permette di proteggere le applicazioni da eventuali problemi del data center. Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza sono presenti almeno tre zone separate in tutte le aree abilitate. Con le zone di disponibilità, Azure offre un contratto di servizio con tempo di attività delle VM del 99,99% tra i migliori del settore.

### <a name="azure-backup-support"></a>Supporto di Backup di Azure

Per proteggersi da emergenze a livello di area, [Backup di Azure](../articles/backup/backup-overview.md) può essere usato per creare un processo di backup con backup pianificati e criteri di conservazione dei backup. In questo modo è possibile eseguire operazioni di ripristino di macchine virtuali e dischi gestiti in base alle esigenze. Attualmente Backup di Azure supporta dimensioni di disco fino a 32 tebibyte (TiB). [Altre informazioni](../articles/backup/backup-support-matrix-iaas.md) sul supporto per il backup di macchine virtuali di Azure.

### <a name="granular-access-control"></a>Controllo di accesso granulare

Per assegnare autorizzazioni specifiche per un disco gestito a uno o più utenti, è possibile usare il [controllo degli accessi in base al ruolo di Azure](../articles/role-based-access-control/overview.md). I dischi gestiti espongono una serie di operazioni, inclusa la lettura, la scrittura (creazione/aggiornamento), l'eliminazione e il recupero di un [URI di firma di accesso condiviso](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) per il disco. È possibile consentire l'accesso solo alle operazioni che servono agli utenti per svolgere il proprio lavoro. Ad esempio, per fare in modo che un utente non possa copiare un disco gestito in un account di archiviazione, è possibile scegliere di non consentire l'accesso all'operazione di esportazione di tale disco gestito. Analogamente, per fare in modo che un utente non possa usare un URI di firma di accesso condiviso per copiare un disco gestito, è possibile scegliere di non concedere l'autorizzazione per il disco gestito.

### <a name="upload-your-vhd"></a>Caricare il disco rigido virtuale

Il caricamento diretto semplifica il trasferimento del disco rigido virtuale in un disco gestito di Azure. In precedenza era necessario seguire una procedura più complessa che includeva lo staging dei dati in un account di archiviazione. Ora sono richiesti meno passaggi. È più facile caricare le VM locali in Azure, anche in grandi dischi gestiti, e il processo di backup e ripristino è semplificato. I costi sono inoltre ridotti, grazie alla possibilità di caricare i dati direttamente nei dischi gestiti senza collegarli a VM. È possibile usare il caricamento diretto di dischi rigidi virtuali di dimensioni fino a 32 TiB.

Per altre informazioni su come trasferire i dischi rigidi virtuali in Azure, vedere gli articoli relativi all'[interfaccia della riga di comando](../articles/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli.md) o a [PowerShell](../articles/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="security"></a>Security

### <a name="private-links"></a>Collegamenti privati

Il supporto del collegamento privato per i dischi gestiti è attualmente disponibile in anteprima e può essere usato per importare o esportare un disco gestito interno alla rete. I collegamenti privati consentono di generare un URI di firma di accesso condiviso con limiti di tempo per gli snapshot e i dischi gestiti non collegati per esportare i dati in altre aree per l'espansione a livello di area, il ripristino di emergenza e l'analisi per scopi legali. L'URI di firma di accesso condiviso può essere usato anche per caricare direttamente un disco rigido virtuale in un disco vuoto dall'ambiente locale. È ora possibile sfruttare i [collegamenti privati](../articles/private-link/private-link-overview.md) per limitare l'esportazione e l'importazione di dischi gestiti alla rete virtuale di Azure. I collegamenti privati assicurano che il traffico dei dati rimanga all'interno della rete backbone Microsoft sicura.

Per informazioni su come abilitare i collegamenti privati per l'importazione o l'esportazione di un disco gestito, vedere gli articoli relativi all'[interfaccia della riga di comando](../articles/virtual-machines/linux/disks-export-import-private-links-cli.md) o al [portale](../articles/virtual-machines/disks-enable-private-links-for-import-export-portal.md).

### <a name="encryption"></a>Crittografia

Con i dischi gestiti vengono offerti due tipi diversi di crittografia. Il primo è la crittografia lato server e viene eseguito dal servizio di archiviazione. Il secondo è Crittografia dischi di Azure, che è possibile abilitare nei dischi del sistema operativo e in quelli dei dati per le VM.

#### <a name="server-side-encryption"></a>Modello di crittografia lato server

La crittografia lato server esegue la crittografia dei dati inattivi e protegge i dati consentendo di soddisfare i criteri di sicurezza e conformità dell'organizzazione. La crittografia lato server è abilitata per impostazione predefinita per tutti i dischi gestiti, gli snapshot e le immagini in tutte le aree in cui sono disponibili dischi gestiti. I dischi temporanei, invece, non vengono crittografati dalla crittografia lato server a meno che non si abiliti la crittografia a livello di host. Vedere [Ruoli del disco: dischi temporanei](#temporary-disk).

È possibile far gestire le chiavi ad Azure (chiavi gestite dalla piattaforma) oppure scegliere di gestirle personalmente (chiavi gestite dal cliente). Per altre informazioni, vedere [Crittografia lato server dell'archiviazione su disco di Azure](../articles/virtual-machines/windows/disk-encryption.md).


#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Crittografia dischi di Azure consente di crittografare i dischi del sistema operativo e i dischi dati usati da una macchina virtuale IaaS. Questo tipo di crittografia include i dischi gestiti. Per Windows, le unità vengono crittografate mediante la tecnologia di crittografia BitLocker standard del settore. Per Linux, i dischi vengono crittografati mediante la tecnologia DM-Crypt, Il processo di crittografia è integrato in Azure Key Vault per consentire il controllo e la gestione delle chiavi di crittografia del disco. Per altre informazioni, vedere [Crittografia dischi di Azure per VM Linux](../articles/virtual-machines/linux/disk-encryption-overview.md) o [Crittografia dischi di Azure per VM Windows](../articles/virtual-machines/windows/disk-encryption-overview.md).

## <a name="disk-roles"></a>Ruoli del disco

Sono disponibili tre ruoli principali per i dischi in Azure: il disco dati, il disco del sistema operativo e il disco temporaneo. Questi ruoli corrispondono ai dischi collegati alla macchina virtuale.

![Ruoli per i dischi in azione](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Disco dati

Un disco dati è un disco gestito collegato a una macchina virtuale per archiviare i dati delle applicazioni o altri dati che è necessario conservare. I dischi dati vengono registrati come unità SCSI ed etichettati con una lettera di propria scelta. Ogni disco dati ha una capacità massima di 32.767 gibibyte (GiB). Le dimensioni della macchina virtuale determinano il numero di dischi dati è possibile collegare e il tipo di archiviazione che è possibile utilizzare per ospitare i dischi.

### <a name="os-disk"></a>Disco del sistema operativo

Tutte le macchine virtuali dispongono di un disco del sistema operativo collegato. Il disco del sistema operativo che dispone di un sistema operativo preinstallato, selezionato quando è stata creata la macchina virtuale. Questo disco contiene il volume di avvio.

Questo disco ha una capacità massima di 2,048 GiB.

### <a name="temporary-disk"></a>Disco temporaneo

Ogni macchina virtuale contiene un disco temporaneo che non è un disco gestito. Il disco temporaneo offre archiviazione a breve termine per applicazioni e processi ed è destinato solo all'archiviazione di dati come file di paging o di scambio. I dati presenti nel disco temporaneo potrebbero andare persi durante un [evento di manutenzione](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) o la [ridistribuzione di una VM](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Durante un riavvio standard della macchina virtuale con esito positivo, i dati nell'unità temporanea vengono mantenuti.  

Nelle VM Linux di Azure il disco temporaneo è in genere /dev/sdb e nelle VM Windows è D: per impostazione predefinita. Il disco temporaneo non viene crittografato dalla crittografia lato server a meno che non si abiliti la crittografia a livello di host.

## <a name="managed-disk-snapshots"></a>Snapshot dei dischi gestiti

Uno snapshot del disco gestito è una copia completa di sola lettura coerente con l'arresto anomalo del sistema di un disco gestito che viene archiviata come disco gestito Standard per impostazione predefinita. Gli snapshot permettono di eseguire il backup dei dischi gestiti in qualsiasi momento. Questi snapshot esistono indipendentemente dal disco di origine e possono essere usati per creare nuove istanze di dischi gestiti. 

Gli snapshot vengono fatturati in base alle dimensioni usate. Ad esempio, se si crea uno snapshot di un disco gestito con una capacità di provisioning di 64 GiB e una dimensione di dati effettivamente usata di 10 GiB, viene addebitato solo lo snapshot relativo alla dimensione di dati usata di 10 GiB. È possibile visualizzare le dimensioni usate degli snapshot esaminando il [report sull'utilizzo di Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). Se ad esempio le dimensioni dei dati usati di uno snapshot sono 10 GiB, il report sull'utilizzo **giornaliero** mostrerà 10 GiB/(31 giorni) = 0,3226 come quantità utilizzata.

Per altre informazioni su come creare snapshot per i dischi gestiti, vedere le risorse seguenti:

* [Creare uno snapshot di un disco gestito in Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Creare uno snapshot di un disco gestito in Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Immagini

I dischi gestiti supportano anche la creazione di un'immagine personalizzata gestita. È possibile creare un'immagine dal disco rigido virtuale personalizzato in un account di archiviazione oppure direttamente da una macchina virtuale (preparata con Sysprep) generalizzata. Questo processo acquisisce una singola immagine. Tutti i dischi gestiti associati a una macchina virtuale, inclusi i dischi dati e del sistema operativo, vengono inclusi in un'unica immagine. Questa immagine personalizzata gestita permette di creare centinaia di macchine virtuali usando l'immagine personalizzata senza dover copiare o gestire alcun account di archiviazione.

Per informazioni sulla creazione di immagini, vedere gli articoli seguenti:

* [Come acquisire un'immagine gestita di una macchina virtuale generalizzata in Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Come generalizzare e acquisire una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Immagini e snapshot

È importante comprendere la differenza tra immagini e snapshot. I dischi gestiti permettono di acquisire l'immagine di una macchina virtuale generalizzata che è stata deallocata. Tale immagine include tutti i dischi collegati alla macchina virtuale e può essere usata per creare una macchina virtuale, che include tutti i dischi.

Uno snapshot è la copia di un disco nel momento in cui lo snapshot viene creato e si applica esclusivamente a un disco. Se la macchina virtuale include un disco, quello del sistema operativo, è possibile creare uno snapshot o acquisire un'immagine del disco e creare una macchina virtuale dallo snapshot o dall'immagine.

Lo snapshot non dispone di alcuna consapevolezza relativa ai dischi, tranne quello che contiene. Questo lo rende problematico da usare negli scenari che richiedono il coordinamento di più dischi, ad esempio lo striping. Gli snapshot dovrebbero essere in grado di coordinarsi tra loro, tuttavia questa funzionalità non è attualmente supportata.

## <a name="disk-allocation-and-performance"></a>Allocazione dei dischi e prestazioni

Il diagramma seguente illustra l'allocazione in tempo reale della larghezza di banda e delle operazioni di I/O al secondo per i dischi, usando un sistema di provisioning a tre livelli:

![Sistema di provisioning a tre livelli con indicazione dell'allocazione della larghezza di banda e delle operazioni di I/O al secondo](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

Il provisioning di primo livello imposta l'assegnazione della larghezza di banda e delle operazioni di I/O al secondo per disco.  Al secondo livello, l'host del server di calcolo implementa il provisioning SSD, applicandolo solo ai dati archiviati nell'unità SSD del server, che include dischi con memorizzazione nella cache (ReadWrite e ReadOnly), oltre a dischi locali e temporanei. Il provisioning della rete delle macchine virtuali viene infine eseguito al terzo livello per qualsiasi operazione di I/O che l'host di calcolo invia al back-end di Archiviazione di Azure. Con questo schema, le prestazioni di una VM dipendono da diversi fattori, dal modo in cui la macchina virtuale usa l'unità SSD locale, dal numero di dischi collegati, nonché dal tipo di prestazioni e memorizzazione nella cache dei dischi collegati.

Come esempio di queste limitazioni, a una macchina virtuale Standard_DS1v1 viene impedito di raggiungere potenzialmente 5.000 operazioni di I/O al secondo di un disco P30, indipendentemente dalla memorizzazione nella cache, a causa dei limiti a livello di unità SSD e di rete:

![Allocazione Standard_DS1v1 di esempio](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure usa il canale di rete in ordine di priorità per il traffico del disco e tale canale ha la precedenza rispetto ad altri tipi di traffico di rete a priorità bassa. In questo modo le prestazioni previste dei dischi risultano inalterate anche in caso di conflitti di rete. Analogamente, Archiviazione di Azure gestisce i conflitti di risorse e altri problemi in background con il bilanciamento del carico automatico. Archiviazione di Azure alloca le risorse necessarie quando si crea un disco e applica il bilanciamento proattivo e reattivo delle risorse per gestire il livello di traffico. In questo modo si garantisce che i dischi possano soddisfare gli obiettivi previsti in termini di operazioni di I/O al secondo e velocità effettiva. È possibile usare metriche a livello di macchina virtuale e di disco per tenere traccia delle prestazioni e configurare gli avvisi in base alle esigenze.

Fare riferimento all'articolo sulla [progettazione per le prestazioni elevate](../articles/virtual-machines/windows/premium-storage-performance.md) per informazioni sulle procedure consigliate per l'ottimizzazione delle configurazioni che includono macchine virtuali e dischi allo scopo di ottenere le prestazioni desiderate.

## <a name="next-steps"></a>Passaggi successivi

Per un video che spiega i dischi gestiti in modo più dettagliato, vedere: [Migliore resilienza delle macchine virtuali di Azure con Managed Disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Nell'articolo sui tipi di disco vengono fornite altre informazioni sui tipi di disco singolo offerti da Azure, sul tipo ideale per le proprie esigenze e sugli obiettivi in termini di prestazioni.
