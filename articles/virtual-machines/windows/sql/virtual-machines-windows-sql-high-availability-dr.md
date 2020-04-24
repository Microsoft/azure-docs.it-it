---
title: Disponibilità elevata e ripristino di emergenza di SQL Server | Documentazione Microsoft
description: Descrizione dei vari tipi di strategie HADR per SQL Server in esecuzione su macchine virtuali di Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: f58bb534728660b85f7d16910dde7a37914fd571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249763"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure

Le macchine virtuali di Microsoft Azure con SQL Server possono consentire di ridurre il costo di una soluzione di database a disponibilità elevata e con ripristino di emergenza (HADR). Molte soluzioni HADR di SQL Server sono supportate nelle macchine virtuali di Azure, sia come soluzioni solo Azure sia come soluzioni ibride. In una soluzione solo Azure l'intero sistema HADR viene eseguito in Azure. In una configurazione ibrida parte della soluzione viene eseguita in Azure e l'altra parte viene eseguita localmente nell'organizzazione. La flessibilità dell'ambiente Azure consente di passare ad Azure in parte o completamente per rispondere ai requisiti HADR e di budget dei sistemi di database di SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Comprendere la necessità di una soluzione HADR
L'amministratore del database ha il compito di garantire che il sistema di database disponga delle funzionalità HADR richieste dal contratto di servizio. I meccanismi di disponibilità elevata di Azure, ad esempio la riparazione per i servizi cloud e il rilevamento del ripristino da errore per le macchine virtuali, non garantiscono di per sé la soddisfazione dei requisiti del contratto di servizio desiderato. Questi meccanismi proteggono la disponibilità elevata delle macchine virtuali, ma non la disponibilità elevata di SQL Server che viene eseguito nelle macchine virtuali. È possibile che l'istanza di SQL Server restituisca un errore mentre la macchina virtuale è online e integra. Inoltre, anche i meccanismi di disponibilità elevata forniti in Azure non evitano tempi di inattività delle macchine virtuali a causa di eventi quali il ripristino da errori software o hardware e gli aggiornamenti del sistema operativo.

Neppure l'archiviazione con ridondanza geografica di Azure, implementata con una funzionalità denominata replica geografica, costituisce un'adeguata soluzione di ripristino di emergenza per i database. Poiché la replica geografica invia dati in modo asincrono, è possibile che gli aggiornamenti recenti vadano persi in caso di errore grave. Per altre informazioni relative alle limitazioni della replica geografica, consultare la sezione [Replica geografica non supportata per file di dati e log in dischi separati](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>Architetture di distribuzione HADR
Le tecnologie HADR di SQL Server supportate in Azure includono:

* [Gruppi di disponibilità AlwaysOn](https://technet.microsoft.com/library/hh510230.aspx)
* [Always On istanze del cluster di failover](https://technet.microsoft.com/library/ms189134.aspx)
* [Log shipping](https://technet.microsoft.com/library/ms187103.aspx)
* [Backup e ripristino di SQL Server con il servizio Archiviazione BLOB di Azure](https://msdn.microsoft.com/library/jj919148.aspx)
* [Mirroring del database](https://technet.microsoft.com/library/ms189852.aspx) - Deprecato in SQL Server 2016

È possibile combinare le tecnologie per implementare una soluzione di SQL Server che disponga di funzionalità di disponibilità elevata e ripristino di emergenza. A seconda della tecnologia in uso, una distribuzione ibrida può richiedere un tunnel VPN con la rete virtuale di Azure. Le sezioni seguenti illustrano alcune architetture di distribuzione di esempio.

## <a name="azure-only-high-availability-solutions"></a>Solo Azure: soluzioni di disponibilità elevata

È possibile avere una soluzione a disponibilità elevata per SQL Server a livello di database con Gruppi di disponibilità Always On, denominati gruppi di disponibilità. È anche possibile creare una soluzione a disponibilità elevata a livello di istanza con istanze del cluster di failover Always On, denominate istanze del cluster di failover. Per maggiore ridondanza, è anche possibile creare ridondanza per entrambi i livelli tramite la creazione di gruppi di disponibilità nelle istanze del cluster di failover. 

| Tecnologia | Architetture di esempio |
| --- | --- |
| **Gruppi di disponibilità** |Le repliche di disponibilità in esecuzione nelle macchine virtuali di Azure nella stessa area assicurano disponibilità elevata.  È necessario configurare una macchina virtuale da usare come controller di dominio perché il servizio Clustering di failover di Windows richiede un dominio di Active Directory.<br/><br/> Per una maggiore ridondanza e disponibilità, le macchine virtuali di Azure possono essere distribuite in diverse [zone di disponibilità](../../../availability-zones/az-overview.md) , come illustrato nella panoramica del [gruppo di disponibilità](virtual-machines-windows-portal-sql-availability-group-overview.md). Se le macchine virtuali SQL Server in un gruppo di disponibilità vengono distribuite in zone di disponibilità, usare [Load Balancer standard](../../../load-balancer/load-balancer-standard-overview.md) per il listener, come descritto in questi articoli: modelli di & [avvio rapido](virtual-machines-windows-sql-availability-group-quickstart-template.md)dell'interfaccia della riga di comando di Azure [SQL VM](virtual-machines-windows-sql-availability-group-cli.md).<br/> ![Gruppi di disponibilità](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>Per altre informazioni, vedere [Configurare manualmente un gruppo di disponibilità Always On nelle macchine virtuali di Azure tramite Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Istanze del cluster di failover** |Le istanze del cluster di failover (FCI), che richiedono l'archiviazione condivisa, possono essere create in quattro modi diversi.<br/><br/>1. un cluster di failover a due nodi in esecuzione in macchine virtuali di Azure con archiviazione collegata usando [Windows Server 2016 \(spazi di archiviazione diretta S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) per fornire una San virtuale basata su software.<br/><br/> 2. un cluster di failover a due nodi in esecuzione in macchine virtuali di Azure con la [condivisione file Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md). Le condivisioni file Premium sono condivisioni file con bassa latenza SSD, completamente supportate per l'uso con l'istanza del cluster di failover.<br/><br/>3. cluster di failover a due nodi in esecuzione in macchine virtuali di Azure con archiviazione supportata da una soluzione di clustering di terze parti. Per un esempio specifico in cui viene usato il tipo di oggetto, vedere [disponibilità elevata per una condivisione file con il clustering di failover e il software di terze parti Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>4. un cluster di failover a due nodi in esecuzione in macchine virtuali di Azure con archiviazione a blocchi condivisa di destinazione iSCSI remota tramite ExpressRoute. Ad esempio, NetApp Private Storage (NPS) espone una destinazione iSCSI tramite ExpressRoute con Equinix a macchine virtuali di Azure.<br/><br/>Per le soluzioni di replica dei dati e di archiviazione condivisa di terze parti, contattare il fornitore per eventuali problemi di accesso ai dati durante il failover.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Solo Azure: soluzioni di ripristino di emergenza
È possibile disporre di una soluzione di ripristino di emergenza per i database di SQL Server in Azure tramite i gruppi di disponibilità, il mirroring del database o funzionalità di backup e ripristino con i BLOB di archiviazione.

| Tecnologia | Architetture di esempio |
| --- | --- |
| **Gruppi di disponibilità** |Repliche di disponibilità in esecuzione tra più data center nelle macchine virtuali di Azure per il ripristino di emergenza. Questa soluzione per più aree protegge dalla completa interruzione dell'alimentazione del sito. <br/> ![Gruppi di disponibilità](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>Nell'ambito di un'area tutte le repliche dovranno risiedere nello stesso servizio cloud e nella stessa rete virtuale. Poiché ogni area sarà caratterizzata da una rete virtuale separata, queste soluzioni richiedono la connettività da rete virtuale a rete virtuale. Per altre informazioni, vedere [Configurare una connessione da rete virtuale a rete virtuale con il portale di Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Per istruzioni dettagliate, vedere [Configurare un gruppo di disponibilità SQL Server in macchine virtuali di Azure in aree diverse](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Mirroring del database** |Tutti i server principali, mirror e di controllo in esecuzione in diversi data center per il ripristino di emergenza. È necessario eseguire la distribuzione utilizzando certificati del server. Il mirroring del database SQL Server non è supportato per SQL Server 2008 né SQL Server 2008 R2 in una macchina virtuale di Azure. <br/>![Mirroring del database](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Backup e ripristino con il servizio di archiviazione BLOB di Azure** |Il backup dei database di produzione viene eseguito direttamente nell'archiviazione BLOB in un data center diverso per il ripristino di emergenza.<br/>![Backup e ripristino](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>Per altre informazioni, vedere [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replica e failover di SQL Server in Azure con Azure Site Recovery** |La replica dell'istanza di produzione di SQL Server di un data center di Azure viene eseguita direttamente in Archiviazione di Azure in un diverso data center di Azure per il ripristino di emergenza.<br/>![Replica con Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>Per altre informazioni, vedere [Proteggere SQL Server con il ripristino di emergenza di SQL Server e Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>IT ibrido: soluzioni di ripristino di emergenza
È possibile disporre di una soluzione di ripristino di emergenza per i database di SQL Server in un ambiente IT ibrido tramite i gruppi di disponibilità, il mirroring del database, il log shipping e funzionalità di backup e ripristino con l'archiviazione BLOB di Azure.

| Tecnologia | Architetture di esempio |
| --- | --- |
| **Gruppi di disponibilità** |Alcune repliche di disponibilità in esecuzione nelle macchine virtuali di Azure e altre in esecuzione in locale per il ripristino di emergenza tra siti. Il sito di produzione può essere locale o trovarsi in un data center di Azure.<br/>![Gruppi di disponibilità](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Poiché tutte le repliche di disponibilità devono trovarsi nello stesso cluster di failover, tale cluster deve essere esteso a entrambe le reti (un cluster di failover su più subnet). Questa configurazione richiede una connessione VPN tra Azure e la rete locale.<br/><br/>Per il corretto ripristino di emergenza dei database, è inoltre opportuno installare un controller di dominio di replica nel sito di ripristino di emergenza.<br/><br/>È possibile usare la procedura guidata Aggiungi replica in SSMS per aggiungere una replica di Azure a un gruppo di disponibilità AlwaysOn esistente. Per altre informazioni, vedere l'esercitazione relativa all'estensione del gruppo di disponibilità AlwaysOn ad Azure. |
| **Mirroring del database** |Un partner in esecuzione in una macchina virtuale di Azure e l'altro in esecuzione in locale per il ripristino di emergenza tra siti usando certificati del server. I partner non devono essere nello stesso dominio di Active Directory e non è richiesta alcuna connessione VPN.<br/>![Mirroring del database](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>Un altro scenario di mirroring del database include un partner in esecuzione in una VM di Azure e l'altro in esecuzione in locale nello stesso dominio di Active Directory per il ripristino di emergenza tra siti. È necessaria una [connessione VPN tra la rete virtuale di Azure e la rete locale](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).<br/><br/>Per il corretto ripristino di emergenza dei database, è inoltre opportuno installare un controller di dominio di replica nel sito di ripristino di emergenza. Il mirroring del database SQL Server non è supportato per SQL Server 2008 né SQL Server 2008 R2 in una macchina virtuale di Azure. |
| **Log shipping** |Un server in esecuzione in una macchina virtuale di Azure e l'altro in esecuzione in locale per il ripristino di emergenza tra siti. Il log shipping dipende dalla condivisione dei file di Windows, pertanto è richiesta una connessione VPN tra la rete virtuale di Azure e la rete locale.<br/>![Log shipping](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>Per il corretto ripristino di emergenza dei database, è inoltre opportuno installare un controller di dominio di replica nel sito di ripristino di emergenza. |
| **Backup e ripristino con il servizio di archiviazione BLOB di Azure** |Il backup dei database di produzione locali viene eseguito direttamente nell'archiviazione BLOB di Azure per il ripristino di emergenza.<br/>![Backup e ripristino](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>Per altre informazioni, vedere [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replica e failover di SQL Server in Azure con Azure Site Recovery** |La replica dell'istanza di produzione locale di SQL Server viene eseguita direttamente in Archiviazione di Azure per il ripristino di emergenza.<br/>![Replica con Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>Per altre informazioni, vedere [Proteggere SQL Server con il ripristino di emergenza di SQL Server e Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Replica di ripristino di emergenza gratuita in Azure

Se si dispone di [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), è possibile implementare piani di ripristino di emergenza ibrido con SQL Server senza sostenere costi di licenza aggiuntivi per l'istanza di ripristino di emergenza passiva.

Nell'immagine seguente il programma di installazione usa SQL Server in esecuzione in una macchina virtuale di Azure che usa 12 core come replica di ripristino di emergenza per una distribuzione di SQL Server locale con 12 core. In passato, è necessario concedere in licenza 12 core di SQL Server per la distribuzione in locale e la macchina virtuale di Azure. Il nuovo vantaggio offre vantaggi della replica passiva in esecuzione in una macchina virtuale di Azure. A questo punto, è necessario concedere in licenza solo 12 core di SQL Server eseguiti in locale, purché vengano soddisfatti i criteri di ripristino di emergenza per la replica passiva in una macchina virtuale di Azure.

![Replica di ripristino di emergenza gratuita in Azure](media/virtual-machines-windows-sql-high-availability-dr/free-dr-replica-azure.png)

Per ulteriori informazioni, vedere le [condizioni di licenza del prodotto](https://www.microsoft.com/licensing/product-licensing/products). 

Per abilitare questo vantaggio, passare alla [risorsa SQL Server macchina virtuale](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource), selezionare **Configura** in impostazioni, quindi scegliere l'opzione **ripristino di emergenza** in **SQL Server licenza**. Selezionare la casella di controllo per confermare che questa SQL Server VM verrà usata come replica passiva, quindi selezionare **applica** per salvare le impostazioni. 

![Configurare la replica di ripristino di emergenza in Azure](media/virtual-machines-windows-sql-high-availability-dr/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerazioni importanti per HADR di SQL Server in Azure
La macchina virtuale, l'archiviazione e la rete di Azure hanno caratteristiche operative diverse rispetto a un'infrastruttura IT locale non virtualizzata. Per una corretta implementazione di una soluzione HADR di SQL Server in Azure è necessario capire queste differenze e progettare una soluzione adeguata.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nodi a disponibilità elevata in un set di disponibilità
I set di disponibilità in Azure consentono di collocare i nodi a disponibilità elevata in domini di errore (FD, Fault Domain) e domini di aggiornamento (UD, Update Domain) separati. A ciascuna macchina virtuale nel set di disponibilità viene assegnato un dominio di aggiornamento e un dominio di errore dalla piattaforma Azure sottostante. Questa configurazione in un data center assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia sempre disponibile e soddisfi per almeno il 99,95% i requisiti del contratto di servizio di Azure. Per configurare la configurazione a disponibilità elevata, inserire tutte le macchine virtuali SQL incluse nello stesso set di disponibilità per evitare perdite di dati o applicazioni durante un evento di manutenzione. Solo i nodi dello stesso servizio cloud possono partecipare allo stesso set di disponibilità. Per ulteriori informazioni, vedere [gestire la disponibilità delle macchine virtuali](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Nodi a disponibilità elevata in una zona di disponibilità
Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. La separazione fisica di zone di disponibilità all'interno di un'area protegge le applicazioni e i dati dagli errori dei data center garantendo che almeno una macchina virtuale sia disponibile e soddisfi il 99,99% del contratto di contratto di Azure. Per configurare la disponibilità elevata, inserire le macchine virtuali SQL disponibili distribuite tra zone di disponibilità disponibili nell'area. Vengono applicati addebiti per il trasferimento dei dati da VM a VM tra diverse zone di disponibilità. Per altre informazioni, vedere [zone di disponibilità](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Comportamento del cluster di failover nella rete di Azure
Il servizio DHCP non conforme a RFC di Azure può provocare la non riuscita della creazione di determinate configurazioni del cluster di failover, a causa dell'assegnazione al nome di rete del cluster di un indirizzo IP duplicato (lo stesso indirizzo IP di uno dei nodi del cluster). Ciò costituisce un problema quando si implementano i gruppi di disponibilità, che dipendono dalla funzionalità del cluster di failover di Windows.

Si consideri uno scenario in cui viene creato e portato online un cluster a due nodi:

1. Il cluster è online e NODE1 richiede un indirizzo IP assegnato in modo dinamico per il nome di rete del cluster.
2. Dal servizio DHCP non viene assegnato un indirizzo IP diverso da quello dello stesso NODE1, poiché il servizio DHCP riconosce che la richiesta proviene da NODE1.
3. Windows rileva l'assegnazione di un indirizzo duplicato a NODE1 e al nome di rete del cluster di failover e il gruppo cluster predefinito non può essere portato online.
4. Il gruppo cluster predefinito passa a NODE2, che tratta l'indirizzo IP di NODE1 come l'indirizzo IP del cluster e porta online il gruppo cluster predefinito.
5. Quando NODE2 tenta di stabilire la connessione con NODE1, i pacchetti indirizzati a NODE1 non lasciano mai NODE2 perché l'indirizzo IP di NODE1 viene risolto in se stesso. NODE2 non è in grado di stabilire la connessione con NODE1, quindi perde il quorum e arresta il cluster.
6. Nel frattempo, NODE1 può inviare i pacchetti a NODE2, ma NODE2 non può rispondere. NODE1 perde il quorum e arresta il cluster.

È possibile evitare questo scenario assegnando un indirizzo IP statico inutilizzato, ad esempio un indirizzo IP locale rispetto al collegamento come 169.254.1.1, al nome di rete del cluster per portarlo online. Per semplificare il processo, vedere [Configuring Windows failover cluster in Azure for availability groups](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx) (Configurazione del cluster di failover di Windows in Azure per i gruppi di disponibilità).

Per altre informazioni, vedere [configurare i gruppi di disponibilità in Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Supporto del listener del gruppo di disponibilità
I listener del gruppo di disponibilità sono supportati nelle VM di Azure che eseguono Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Questo supporto viene fornito attraverso l'uso di endpoint con carico bilanciato abilitato nelle macchine virtuali di Azure che costituiscono nodi del gruppo di disponibilità. È necessario eseguire passaggi di configurazione specifici affinché i listener funzionino sia per le applicazioni client eseguite in Azure sia per quelle eseguite in locale.

Sono disponibili due opzioni principali per la configurazione del listener: esterno (pubblico) o interno. Il listener esterno (pubblico) usa un bilanciamento del carico con connessione Internet ed è associato a un indirizzo pubblico Virtuale IP (VIP) che è accessibile tramite Internet. Un listener interno usa bilanciamento del carico interno e supporta solo i client all'interno della stessa rete virtuale. Per un tipo di bilanciamento del carico, è necessario abilitare Direct Server Return. 

Se il gruppo di disponibilità si estende su più subnet di Azure (ad esempio una distribuzione che attraversa aree di Azure), la stringa di connessione client deve includere "**MultisubnetFailover=True**". Di conseguenza, vengono eseguiti tentativi di connessione paralleli alle repliche nelle diverse subnet. Per istruzioni sull'impostazione di un listener, vedere

* [Configurare un listener ILB per i gruppi di disponibilità in Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Configurare un listener esterno per i gruppi di disponibilità in Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

È comunque possibile connettersi separatamente a ogni replica di disponibilità effettuando la connessione direttamente all'istanza del servizio. Inoltre, poiché i gruppi di disponibilità sono compatibili con le versioni precedenti dei client di mirroring del database, è possibile connettersi alle repliche di disponibilità come partner per il mirroring del database purché le repliche siano configurate in modo analogo al mirroring del database:

* Una replica primaria e una replica secondaria
* La replica secondaria è configurata come non leggibile (opzione**Secondario leggibile** impostata su **No**)

Di seguito è riportata una stringa di connessione client di esempio corrispondente a questa configurazione simile al mirroring del database usando ADO.NET o SQL Server Native Client:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Per altre informazioni sulla connettività client, vedere:

* [Utilizzo delle parole chiave delle stringhe di connessione con SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Connessione di client a una sessione di mirroring del database (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Connessione al listener del gruppo di disponibilità in ambiente IT ibrido](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Listener del gruppo di disponibilità, connettività client e failover dell'applicazione (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Uso di stringhe di connessione per il mirroring del database con gruppi di disponibilità](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latenza di rete in ambiente IT ibrido
È opportuno distribuire la soluzione HADR con il presupposto che potrebbero verificarsi lunghi periodi di tempo con latenza di rete elevata tra la rete locale e Azure. Quando si distribuiscono le repliche in Azure, occorre usare il commit asincrono anziché quello sincrono per la modalità di sincronizzazione. Per la distribuzione dei server di mirroring del database in locale e in Azure, usare la modalità a prestazioni elevate anziché la modalità a protezione elevata.

### <a name="geo-replication-support"></a>Supporto della replica geografica
La replica geografica nei dischi di Azure non supporta l'archiviazione del file di dati e del file di log dello stesso database in dischi separati. L'archiviazione con ridondanza geografica replica le modifiche in ogni disco in modo indipendente e asincrono. Questo meccanismo garantisce l'ordine di scrittura in un disco singolo nella copia replicata geograficamente, ma non in più copie replicate geograficamente di più dischi. Se si configura un database per l'archiviazione del file di dati e del file di log in dischi separati, i dischi recuperati dopo un'emergenza potranno contenere una copia più aggiornata del file di dati che del file di log, creando un'interruzione nel log write-ahead in SQL Server e nelle proprietà ACID delle transazioni. Se non è possibile disabilitare la replica geografica nell'account di archiviazione, è consigliabile mantenere tutti i file di dati e i file di log per un determinato database nello stesso disco. Se è necessario usare più dischi a causa delle dimensioni del database, distribuire una delle soluzioni di ripristino di emergenza elencate in precedenza per garantire la ridondanza dei dati.

## <a name="next-steps"></a>Passaggi successivi
Se è necessario creare una macchina virtuale di Azure con SQL Server, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

Per ottenere le prestazioni migliori di SQL Server in esecuzione su una VM di Azure, vedere le indicazioni fornite in [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md).

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Altre risorse
* [Installare una nuova foresta Active Directory in Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Creare cluster di failover per i gruppi di disponibilità in una macchina virtuale di Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

