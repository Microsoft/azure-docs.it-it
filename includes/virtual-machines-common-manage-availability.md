---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a4140ffc0d4e97afabb1c3080951eeb75c792a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76961494"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Informazioni sui riavvii delle VM: manutenzione e tempo di inattività
Sono tre gli scenari che possono interessare la macchina virtuale in Azure: manutenzione dell'hardware non pianificata, tempo di inattività imprevisto e manutenzione pianificata.

* **Un evento di manutenzione hardware non pianificata** si verifica quando la piattaforma Azure prevede che stanno per verificarsi problemi all'hardware o a un componente della piattaforma associato a un computer fisico. Quando la piattaforma prevede un errore, genera un evento di manutenzione hardware non pianificata per ridurre l'impatto sulle macchine virtuali ospitate in tale hardware. Azure usa la tecnologia [Live Migration](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) per eseguire la migrazione delle macchine virtuali dall'hardware in errore a una macchina fisica integra. Live Migration è un'operazione di mantenimento della VM che sospende la macchina virtuale solo per un breve periodo. Memoria, file aperti e connessioni di rete vengono conservati, ma le prestazioni potrebbero subire un rallentamento prima e/o dopo l'evento. Nei casi in cui non è possibile usare Live Migration, si verificheranno problemi di tempi di inattività imprevisti nella VM, come descritto sotto.


* Si verificano **tempi di inattività imprevisti** in presenza di malfunzionamenti inaspettati dell'hardware o dell'infrastruttura fisica per la macchina virtuale. Può trattarsi, ad esempio, di errori della rete locale, guasti di un disco locale o altri errori a livello di rack. Quando viene rilevato un errore di questo tipo, la piattaforma Azure esegue automaticamente la migrazione (riparazione) della macchina virtuale a un computer fisico integro nello stesso data center. Durante la procedura di riparazione, nelle macchine virtuali si verificano tempi di inattività (riavvio) e in alcuni casi la perdita dell'unità temporanea. Il sistema operativo e i dischi dati collegati vengono sempre conservati.

  Le macchine virtuali possono anche subire tempi di inattività nell'improbabile eventualità di un'interruzione o emergenza che interessa un intero data center o anche un'intera area. Per questi scenari, Azure offre opzioni di protezione che includono [zone di disponibilità](../articles/availability-zones/az-overview.md) e [aree abbinate](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* Gli **eventi di manutenzione pianificata** sono aggiornamenti periodici effettuati da Microsoft sulla piattaforma Azure sottostante per migliorare l'affidabilità, la sicurezza e le prestazioni complessive dell'infrastruttura della piattaforma su cui sono in esecuzione le macchine virtuali. La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali o i servizi cloud. Vedere [Manutenzione con mantenimento delle macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance). Anche se la piattaforma Azure cerca di usare la manutenzione con mantenimento delle VM in tutte le occasioni possibili, in alcuni rari casi questi aggiornamenti richiedono un riavvio della macchina virtuale per applicare gli aggiornamenti necessari all'infrastruttura sottostante. In tal caso, è possibile eseguire la manutenzione pianificata di Azure con l'operazione di ridistribuzione della manutenzione avviando la manutenzione per le VM nell'intervallo di tempo adatto. Per altre informazioni, vedere [Manutenzione pianificata per le macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Per ridurre le conseguenze dei tempi di inattività causati da uno o più di questi eventi, è possibile attuare le seguenti procedure consigliate per aumentare la disponibilità delle macchine virtuali:

* [Configurare più macchine virtuali in un set di disponibilità per la ridondanzaConfigure multiple virtual machines in an availability set for redundancy]
* [Usare Managed Disks per le macchine virtuali nel set di disponibilità]
* [Usare gli eventi pianificati per rispondere in modo proattivo agli eventi di impatto delle macchine virtualiUse scheduled events to proactively response to VM impacting events](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Configurare ogni livello applicazione in set di disponibilità separatiConfigure each application tier into separate availability sets]
* [Combinare un servizio di bilanciamento del carico con i set di disponibilitàCombine a Load Balancer with availability sets]
* [Usare le zone di disponibilità per la protezione dagli errori a livello di data center]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Usare le zone di disponibilità per la protezione dagli errori a livello di data center

[Le zone di disponibilità](../articles/availability-zones/az-overview.md) espandono il livello di controllo necessario per mantenere la disponibilità delle applicazioni e dei dati nelle macchine virtuali. Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono disponibili almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center. I servizi con ridondanza della zona replicano le applicazioni e i dati tra aree di disponibilità per garantire la protezione da singoli punti di errore.

Una zona di disponibilità in un'area di Azure è una combinazione di un dominio di **errore** e un dominio di **aggiornamento.** Ad esempio, se si creano tre o più macchine virtuali in tre aree in un'area di Azure, le macchine virtuali vengono distribuite in modo efficace in tre domini di errore e tre domini di aggiornamento. La piattaforma di Azure riconosce questa distribuzione in domini di aggiornamento per assicurarsi che le macchine virtuali in diverse aree non vengano aggiornate contemporaneamente.

Con le zone di disponibilità, Azure offre un contratto di servizio con tempo di attività delle VM del 99,99% tra i migliori del settore. Progettando le soluzioni per l'uso di macchine virtuali replicate nelle zone, è possibile proteggere le applicazioni e i dati dalla perdita di un data center. Se una zona è compromessa, le app e i dati replicati diventano immediatamente disponibili in un'altra zona.

![Zone di disponibilità](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Altre informazioni sulla distribuzione di una macchina virtuale [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) o [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) in una zona di disponibilità.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configurare più macchine virtuali in un set di disponibilità per la ridondanza
I set di disponibilità sono un'altra configurazione del data center per fornire ridondanza e disponibilità delle macchine virtuali. Questa configurazione in un data center assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia sempre disponibile e soddisfi per almeno il 99,95% i requisiti del contratto di servizio di Azure. Per altre informazioni, vedere [Contratto di Servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Una macchina virtuale a istanza singola in un set di disponibilità deve usare SSD Premium o Disco Ultra per tutti i dischi del sistema operativo e i dischi dati per qualificarsi per il servizio di integrazione applicativa per la connettività della macchina virtuale di almeno il 99,9%.

A ciascuna macchina virtuale nel set di disponibilità viene assegnato un **dominio di aggiornamento** e un **dominio di errore** dalla piattaforma Azure sottostante. Per ogni set di disponibilità vengono assegnati cinque domini di aggiornamento non configurabili dall'utente per impostazione predefinita, è possibile quindi aumentare le distribuzioni Resource Manager per fornire fino a venti domini di aggiornamento, per indicare i gruppi di macchine virtuali con il relativo hardware fisico sottostante che è possibile riavviare contemporaneamente. Se in un set di disponibilità vengono configurate più di cinque macchine virtuali, la sesta macchina viene inserita nello stesso dominio di aggiornamento della prima, la settima nel dominio di aggiornamento della seconda e così via. I domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale durante la manutenzione pianificata, ma ne viene riavviato uno solo alla volta. Un dominio di aggiornamento riavviato ha 30 minuti di tempo per eseguire il ripristino prima che venga avviata la manutenzione in un altro dominio di aggiornamento.

I domini di errore definiscono il gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le macchine virtuali configurate in un set di disponibilità vengono suddivise tra un massimo di tre domini di errore per le distribuzioni di Resource Manager e in due domini di errore per la distribuzione classica. Il raggruppamento di più macchine virtuali in un set di disponibilità non garantisce la protezione dell'applicazione da eventuali errori del sistema operativo o di singole applicazioni, ma limita le conseguenze prodotte da potenziali guasti dell'hardware fisico e interruzioni di rete o di alimentazione.

<!--Image reference-->
   ![Rappresentazione concettuale della configurazione di domini di aggiornamento e di errore](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Usare Managed Disks per le macchine virtuali nel set di disponibilità
Se si usano macchine virtuali con dischi non gestiti, è fortemente consigliabile [convertire le macchine virtuali nel set di disponibilità per l'uso di Managed Disks](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) offre una maggiore affidabilità per i set di disponibilità, perché fa in modo che i dischi delle macchine virtuali in un set di disponibilità siano sufficientemente isolati gli uni dagli altri per evitare singoli punti di errore. Ciò avviene inserendo automaticamente i dischi in domini di errore di archiviazione diversi (cluster di archiviazione) e allineandoli al dominio di errore della macchina virtuale. Se un dominio di errore di archiviazione non riesce a causa di un errore hardware o software, risulterà in errore solo l'istanza della macchina virtuale con dischi nel dominio di errore di archiviazione.
![Domini di errore di Managed Disks](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Il numero di domini di errore per i set di disponibilità gestiti dipende dall'area: due o tre per area. È possibile visualizzare il dominio di errore per ogni area eseguendo gli script seguenti.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> In determinate circostanze, due macchine virtuali nello stesso AvailabilitySet potrebbero condividere lo stesso FaultDomain.In certain circumstances, 2 VMs in the same AvailabilitySet could shared the same FaultDomain. Ciò può essere confermato accedendo al set di disponibilità e controllando la colonna **Dominio di errore.**
> Ciò può essere causato dalla sequenza seguente durante la distribuzione delle macchine virtuali:This can be cause from the following sequence while deploying the VMs:
> - Distribuire la prima macchina virtualeDeploy the 1st VM
> - Arrestare/Dealare la prima macchina virtualeStop/Deallocate the 1st VM
> - Distribuire la seconda macchina virtuale In queste circostanze, il disco del sistema operativo della seconda macchina virtuale potrebbe essere creato nello stesso dominio di errore della prima macchina virtuale e pertanto anche la seconda macchina virtuale verrà attivata nello stesso FaultDomain. 
> Per evitare questo problema, è consigliabile non arrestare o deallocare le macchine virtuali tra le distribuzioni.

Se si intende usare macchine virtuali con dischi non gestiti, seguire queste procedure consigliate per gli account di archiviazione in cui i dischi rigidi virtuali delle macchine virtuali vengono archiviati come [BLOB di pagine](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Mantenere tutti i dischi (sistema operativo e dati) associati a una macchina virtuale nello stesso account di archiviazione**
2. **Esaminare i [limiti](../articles/storage/blobs/scalability-targets-premium-page-blobs.md) per il numero di dischi non gestiti in un account di archiviazione** di Azure prima di aggiungere altri dischi rigidi virtuali a un account di archiviazioneReview the limits on the number of unmanaged disks in an Azure Storage account before adding more VHDs to a storage account
3. **Usare un account di archiviazione separato per ogni macchina virtuale in un set di disponibilità.** Non condividere gli account di archiviazione con più macchine virtuali in uno stesso set di disponibilità. Le macchine virtuali di set di disponibilità diversi possono condividere gli account di archiviazione, purché vengano seguite le procedure consigliate indicate in precedenza in ![Domini di errore dei dischi non gestiti](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Usare Eventi pianificati per rispondere in modo proattivo agli eventi che hanno impatto sulle macchine virtuali

Quando si esegue la sottoscrizione a [eventi pianificati](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events), la macchina virtuale riceve una notifica sugli eventi di manutenzione imminenti che possono influire su di essa. Quando gli eventi pianificati sono abilitati, alla macchina virtuale viene concessa una quantità minima di tempo prima che l'attività di manutenzione venga eseguita. Ad esempio, gli aggiornamenti del sistema operativo host che possono influire sulla macchina virtuale vengono accodati come eventi che specificano l'impatto, insieme a un orario in cui verrà eseguita la manutenzione se non viene effettuata alcuna azione. Gli eventi pianificati vengono accodati anche quando Azure rileva un errore hardware imminente che potrebbe influire sulla macchina virtuale, consentendo di decidere quando deve essere eseguita la correzione. I clienti possono usare l'evento per eseguire attività prima della manutenzione, ad esempio il salvataggio dello stato, il failover su un sistema secondario e così via. Dopo aver completato la logica per gestire correttamente l'evento di manutenzione, è possibile approvare l'evento pianificato in sospeso per consentire alla piattaforma di procedere con la manutenzione.

## <a name="configure-each-application-tier-into-separate-availability-zones-or-availability-sets"></a>Configurare ogni livello applicazione in zone di disponibilità o set di disponibilità separatiConfigure each application tier into separate availability zones or availability sets
Se le macchine virtuali sono quasi identiche e hanno lo stesso scopo per l'applicazione, è consigliabile configurare una zona di disponibilità o un set di disponibilità per ogni livello dell'applicazione.  Se si inseriscono due livelli diversi nella stessa zona di disponibilità o set, tutte le macchine virtuali nello stesso livello applicazione possono essere riavviate contemporaneamente. Configurando almeno due macchine virtuali in una zona di disponibilità o impostate per ogni livello, si garantisce che sia disponibile almeno una macchina virtuale in ogni livello.

Ad esempio, è possibile inserire tutte le macchine virtuali nel front-end dell'applicazione che esegue IIS, Apache e Nginx in un'unica zona di disponibilità o set. Assicurarsi che solo le macchine virtuali front-end siano posizionate nella stessa zona di disponibilità o nella stessa area di disponibilità o nello stesso set. Analogamente, assicurarsi che solo le macchine virtuali livello dati siano collocate nella propria zona di disponibilità o set, ad esempio le macchine virtuali di SQL Server replicate o le macchine virtuali MySQL.

<!--Image reference-->
   ![Livelli di applicazione](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Combinare un servizio di bilanciamento del carico con zone o set di disponibilitàCombine a load balancer with availability zones or sets
Combinare Azure [Load Balancer](../articles/load-balancer/load-balancer-overview.md) con una zona di disponibilità o impostare per ottenere la massima resilienza delle applicazioni. Il servizio di bilanciamento del carico distribuisce il traffico tra più macchine virtuali ed è incluso nelle macchine virtuali di livello Standard. Non tutti i livelli delle macchine virtuali includono Azure Load Balancer. Per altre informazioni sul bilanciamento del carico delle macchine virtuali, vedere [Load Balancing virtual machines](../articles/virtual-machines/virtual-machines-linux-load-balance.md) (Bilanciamento del carico delle macchine virtuali).

Se il bilanciamento del carico non è configurato in modo da bilanciare il traffico tra più macchine virtuali, qualsiasi evento di manutenzione pianificata influirà sull'unica macchina virtuale di gestione del traffico, determinando un'interruzione del livello di applicazione. Associando più macchine virtuali dello stesso livello a un unico servizio di bilanciamento del carico e a uno stesso set di disponibilità, si garantisce che il traffico sia sempre gestito da almeno un'istanza.

Per un'esercitazione su come bilanciare il carico tra le zone di disponibilità, vedere [Bilanciamento del carico delle macchine virtuali in tutte le zone di disponibilità tramite l'interfaccia della riga di comando](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md)di Azure.For a tutorial on how to load balance across availability zones, see Load balance VMs across all availability zones by using the Azure CLI .


<!-- Link references -->
[Configurare più macchine virtuali in un set di disponibilità per la ridondanzaConfigure multiple virtual machines in an availability set for redundancy]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configurare ogni livello applicazione in set di disponibilità separatiConfigure each application tier into separate availability sets]: #configure-each-application-tier-into-separate-availability-zones-or-availability-sets
[Combinare un servizio di bilanciamento del carico con i set di disponibilitàCombine a Load Balancer with availability sets]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Usare Managed Disks per le macchine virtuali nel set di disponibilità]: #use-managed-disks-for-vms-in-an-availability-set
[Usare le zone di disponibilità per la protezione dagli errori a livello di data center]: #use-availability-zones-to-protect-from-datacenter-level-failures
