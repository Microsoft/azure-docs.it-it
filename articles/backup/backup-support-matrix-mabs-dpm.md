---
title: Matrice di supporto di MAB & System Center DPM
description: Questo articolo riepiloga il supporto di backup di Azure quando si usa Backup di Microsoft Azure Server (MAB) o System Center DPM per eseguire il backup delle risorse locali e delle macchine virtuali di Azure.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6664f7b226b75b364fd1c83f2abc56b5a275eff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582654"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matrice di supporto per il backup con Backup di Microsoft Azure server o System Center DPM

È possibile usare il [servizio backup di Azure](backup-overview.md) per eseguire il backup di computer e carichi di lavoro locali e di macchine virtuali (VM) di Azure. Questo articolo riepiloga le impostazioni di supporto e le limitazioni per il backup dei computer con Backup di Microsoft Azure Server (MAB) o System Center Data Protection Manager (DPM) e backup di Azure.

## <a name="about-dpmmabs"></a>Informazioni su DPM/MAB

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) è una soluzione aziendale che consente di configurare, semplificare e gestire il backup e il ripristino di dati e computer aziendali. e fa parte della famiglia di prodotti [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing).

MAB è un prodotto server che può essere usato per eseguire il backup di server fisici, macchine virtuali e app locali in esecuzione su di essi.

MAB si basa su System Center DPM e fornisce funzionalità simili con alcune differenze:

- Per eseguire il server di Backup di Microsoft Azure non è necessaria alcuna licenza System Center.
- Per gli oggetti MAB e DPM, Azure offre un'archiviazione di backup a lungo termine. DPM inoltre consente di eseguire il backup dei dati per l'archiviazione a lungo termine su nastro. Questa funzionalità non è disponibile con il server di Backup di Microsoft Azure.
- È possibile eseguire il backup di un server DPM primario con un server DPM secondario. Il server secondario proteggerà il database del server primario e le repliche delle origini dati archiviate sul server primario. In caso di guasto del server primario, il server secondario può continuare a proteggere i carichi di lavoro protetti dal server primario, finché il server primario non sarà nuovamente disponibile.  Questa funzionalità non è disponibile con il server di Backup di Microsoft Azure.

È possibile scaricare MAB dall' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57520). Può essere eseguito in locale o in una macchina virtuale di Azure.

DPM e il server di Backup di Microsoft Azure supportano il backup di una vasta gamma di app e di sistemi operativi server e client. Essi offrono diversi scenari di backup:

- È possibile eseguire il backup a livello di computer con un backup dello stato del sistema o un backup bare metal.
- È possibile eseguire il backup di volumi, condivisioni, cartelle e file specifici.
- È possibile eseguire il backup di app specifiche usando impostazioni ottimizzate compatibili con le app.

## <a name="dpmmabs-backup"></a>Backup di DPM/MAB

Il backup con DPM/MAB e backup di Azure funziona nel modo seguente:

1. L'agente protezione DPM/MAB è installato in ogni computer di cui verrà eseguito il backup.
1. Il backup di macchine virtuali e app viene eseguito nella risorsa di archiviazione locale in DPM/MAB.
1. L'agente di Servizi di ripristino di Microsoft Azure viene installato nel server DPM o nel server di Backup di Microsoft Azure.
1. L'agente MARS esegue il backup dei dischi DPM/MAB in un insieme di credenziali di backup di servizi di ripristino in Azure tramite backup di Azure.

Per altre informazioni:

- [Vedere altre informazioni](backup-architecture.md#architecture-back-up-to-dpmmabs) sull'architettura del server di Backup di Microsoft Azure.
- [Esaminare gli elementi supportati](backup-support-matrix-mars-agent.md) per l'agente Mars.

## <a name="supported-scenarios"></a>Scenari supportati

**Scenario** | **Agente** | **Posizione**
--- | --- | ---
**Backup di carichi di lavoro e computer locali** | L'agente protezione DPM/MAB viene eseguito nei computer di cui si vuole eseguire il backup.<br/><br/> Agente MARS nel server DPM/MAB.<br/> La versione minima dell'agente di Servizi di ripristino di Microsoft Azure o dell'agente di Backup di Azure richiesta per l'abilitazione di questa funzionalità è la 2.0.8719.0.  | DPM/MAB deve essere eseguito in locale.

## <a name="supported-deployments"></a>Distribuzioni supportate

DPM/MAB può essere distribuito come riepilogato nella tabella seguente.

**Distribuzione** | **Supporto** | **Dettagli**
--- | --- | ---
**Distribuito in locale** | Server fisico<br/><br/>Macchina virtuale Hyper-V<br/><br/> Macchina virtuale VMware | Se DPM/MAB è installato come macchina virtuale VMware, esegue solo il backup di macchine virtuali VMware e carichi di lavoro in esecuzione in tali macchine virtuali.
**Distribuito come macchina virtuale di Azure Stack** | Solo server di Backup di Microsoft Azure | Non è possibile usare DPM per eseguire il backup di macchine virtuali di Azure Stack.
**Distribuito come macchina virtuale di Azure** | Protegge le macchine virtuali e i carichi di lavoro di Azure in esecuzione in tali macchine virtuali | DPM/MAB in esecuzione in Azure non può eseguire il backup dei computer locali.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemi operativi supportati per il server di Backup di Microsoft Azure e DPM

Backup di Azure può eseguire il backup di istanze di DPM/MAB che eseguono uno dei sistemi operativi seguenti. Devono essere in uso gli ultimi Service Pack e aggiornamenti dei sistemi operativi.

**Scenario** | **DPM/MAB**
--- | ---
**Server di Backup di Microsoft Azure in una macchina virtuale di Azure** |  Windows 2016 datacenter.<br/><br/> Windows 2019 datacenter.<br/><br/> Si consiglia di iniziare con un'immagine del Marketplace.<br/><br/> Standard_A4_v2 minimo con quattro core e 8 GB di RAM.
**DPM in una macchina virtuale di Azure** | System Center 2012 R2 con Update 3 o versioni successive.<br/><br/> Sistema operativo Windows [richiesto da System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Si consiglia di iniziare con un'immagine del Marketplace.<br/><br/> Standard_A4_v2 minimo con quattro core e 8 GB di RAM.
**Server di Backup di Microsoft Azure in locale** |  MAB V3 e versioni successive: Windows Server 2016 o Windows Server 2019
**DPM in locale** | Server fisico/macchina virtuale Hyper-V: System Center 2012 SP1 o versione successiva.<br/><br/> VM VMware: System Center 2012 R2 con aggiornamento 5 o versione successiva.

>[!NOTE]
>L'installazione di server di Backup di Azure non è supportata in Windows Server Core o Microsoft Hyper-V Server.

## <a name="management-support"></a>Supporto della gestione

**Problema** | **Dettagli**
--- | ---
**Installazione** | Installare DPM/MAB in un computer con un solo scopo.<br/><br/> Non installare DPM/MAB in un controller di dominio, in un computer con l'installazione del ruolo server applicazioni, in un computer che esegue Microsoft Exchange Server o System Center Operations Manager o in un nodo del cluster.<br/><br/> [Esaminare tutti i requisiti di sistema di DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Dominio** | DPM/MAB deve essere aggiunto a un dominio. Installare e quindi aggiungere DPM o il server di Backup di Microsoft Azure a un dominio. Lo spostamento di DPM o del server di Backup di Microsoft Azure in un nuovo dominio dopo la distribuzione non è supportato.
**Archiviazione** | Modern backup storage (MBS) è supportato da DPM 2016/MAB V2 e versioni successive. Non è disponibile per il server di Backup di Microsoft Azure v1.
**Aggiornamento del server di Backup di Microsoft Azure** | È possibile installare direttamente il server di Backup di Microsoft Azure v3 oppure eseguire l'aggiornamento dal server di Backup di Microsoft Azure v2 al server di Backup di Microsoft Azure v3. [Altre informazioni](backup-azure-microsoft-azure-backup.md#upgrade-mabs)
**Spostamento del server di Backup di Microsoft Azure** | Se si usa MBS, è possibile spostare il server di Backup di Microsoft Azure in un nuovo server conservando l'archivio.<br/><br/> Il server deve avere lo stesso nome dell'originale. Non è possibile cambiare il nome se si intende mantenere lo stesso pool di archiviazione e usare lo stesso database del server di Backup di Microsoft Azure per archiviare i punti di ripristino dei dati.<br/><br/> Si dovrà disporre di un backup del database del server di Backup di Microsoft Azure perché sarà necessario ripristinarlo.

## <a name="mabs-support-on-azure-stack"></a>Supporto per il server di Backup di Microsoft Azure in Azure Stack

Se si distribuisce il server di Backup di Microsoft Azure in una macchina virtuale di Azure Stack, sarà possibile gestire il backup delle macchine virtuali di Azure Stack e dei carichi di lavoro da un'unica posizione.

**Componente** | **Dettagli**
--- | ---
**Server di Backup di Microsoft Azure in una macchina virtuale di Azure Stack** | Almeno la dimensione a2. È consigliabile iniziare con un'immagine di Windows Server 2012 R2 o Windows Server 2016 da Azure Marketplace.<br/><br/> Non installare altri oggetti nella VM di MAB.
**Archiviazione del server di Backup di Microsoft Azure** | Usare un account di archiviazione separato per la macchina virtuale di MAB. Per l'agente MARS eseguito su MAB è necessaria l'archiviazione temporanea per un percorso della cache e per conservare i dati ripristinati dal cloud.
**Pool di archiviazione del server di Backup di Microsoft Azure** | Le dimensioni del pool di archiviazione MAB sono determinate dal numero e dalle dimensioni dei dischi collegati alla macchina virtuale di MAB. Per ogni dimensione di macchina virtuale di Azure Stack è previsto un numero massimo di dischi. Ad esempio, per A2 il numero massimo è quattro dischi.
**Conservazione del server di Backup di Microsoft Azure** | Non mantenere i dati di cui è stato eseguito il backup nei dischi MAB locali per più di cinque giorni.
**Aumento delle prestazioni del server di Backup di Microsoft Azure** | Per aumentare le prestazioni della distribuzione, è possibile aumentare le dimensioni della macchina virtuale del server di Backup di Microsoft Azure. Ad esempio, è possibile passare dalla serie a alla serie D.<br/><br/> È anche possibile assicurarsi che si stia eseguendo regolarmente il offload dei dati con backup in Azure. Se necessario, è possibile distribuire server MAB aggiuntivi.
**.NET Framework nel server di Backup di Microsoft Azure** | Per la macchina virtuale MAB è necessario .NET Framework 3,3 SP1 o versione successiva.
**Dominio del server di Backup di Microsoft Azure** | La macchina virtuale del server di Backup di Microsoft Azure deve essere aggiunta a un dominio. Un utente del dominio con privilegi di amministratore deve installare il server di Backup di Microsoft Azure nella macchina virtuale.
**Backup dei dati di macchine virtuali di Azure Stack** | È possibile eseguire il backup di file, cartelle e app.
**Backup supportato** | Questi sistemi operativi sono supportati per le macchine virtuali di cui si vuole eseguire il backup:<br/><br/> Canale semestrale di Windows Server (Datacenter, Enterprise, standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Supporto SQL Server per le macchine virtuali Azure Stack** | Eseguire il backup di SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Eseguire il backup e il ripristino di un database.
**Supporto SharePoint per macchine virtuali di Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Eseguire il backup e il ripristino di una farm, un database, un front-end e un server Web.
**Requisiti di rete per macchine virtuali sottoposte a backup** | Tutte le macchine virtuali nel carico di lavoro Azure Stack devono appartenere alla stessa rete virtuale e appartenere alla stessa sottoscrizione.

## <a name="dpmmabs-networking-support"></a>Supporto delle funzionalità di rete per DPM o il server di Backup di Microsoft Azure

### <a name="url-access"></a>accesso con URL

Il server DPM o il server di Backup di Microsoft Azure deve poter accedere agli URL seguenti:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.windowsazure.com
- *.microsoftonline.com
- *.windows.net

### <a name="azure-expressroute-support"></a>Supporto di Azure ExpressRoute

È possibile eseguire il backup dei dati tramite Azure ExpressRoute con il peering pubblico (disponibile per i circuiti precedenti) e il peering Microsoft. Il backup sul peering privato non è supportato.

Con peering pubblico: garantire l'accesso ai seguenti domini/indirizzi:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Con il peering Microsoft, selezionare i seguenti servizi/aree e i valori della community pertinenti:

* Azure Active Directory (12076:5060)
* Area Microsoft Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)
* Archiviazione di Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)

Per ulteriori informazioni, vedere i [requisiti di routing di ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>Il peering pubblico è deprecato per i nuovi circuiti.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Connettività di DPM o del server di Backup di Microsoft Azure al servizio Backup di Azure

Per il corretto funzionamento dei backup, è necessaria la connettività al servizio Backup di Azure e la sottoscrizione di Azure deve essere attiva. La tabella seguente illustra il comportamento che si riscontra quando queste due condizioni non si verificano.

**Server di Backup di Microsoft Azure ad Azure** | **Sottoscrizione** | **Backup/ripristino**
--- | --- | ---
Connesso | Attivo | Eseguire il backup nel disco di DPM/MAB.<br/><br/> Esegui il backup in Azure.<br/><br/> Ripristinare dal disco.<br/><br/> Ripristinare da Azure.
Connesso | Scaduta/sottoposta a deprovisioning | Nessun backup su disco o in Azure.<br/><br/> Se la sottoscrizione è scaduta, è possibile eseguire il ripristino dal disco o da Azure.<br/><br/> Se la sottoscrizione viene ritirata, non è possibile eseguire il ripristino dal disco o da Azure. I punti di ripristino di Azure vengono eliminati.
Nessuna connettività per più di 15 giorni | Attivo | Nessun backup su disco o in Azure.<br/><br/> È possibile eseguire il ripristino dal disco o da Azure.
Nessuna connettività per più di 15 giorni | Scaduta/sottoposta a deprovisioning | Nessun backup su disco o in Azure.<br/><br/> Se la sottoscrizione è scaduta, è possibile eseguire il ripristino dal disco o da Azure.<br/><br/> Se la sottoscrizione viene ritirata, non è possibile eseguire il ripristino dal disco o da Azure. I punti di ripristino di Azure vengono eliminati.

## <a name="dpmmabs-storage-support"></a>Supporto dell'archiviazione per DPM o il server di Backup di Microsoft Azure

I dati di cui viene eseguito il backup in DPM/MAB vengono archiviati nell'archiviazione su disco locale.

**Archiviazione** | **Dettagli**
--- | ---
**MBS** | Modern backup storage (MBS) è supportato da DPM 2016/MAB V2 e versioni successive. Non è disponibile per il server di Backup di Microsoft Azure v1.
**Archiviazione del server di Backup di Microsoft Azure in una macchina virtuale di Azure** | I dati vengono archiviati nei dischi di Azure collegati alla macchina virtuale DPM/MAB e gestiti in DPM/MAB. Il numero di dischi che è possibile usare per il pool di archiviazione DPM/MAB è limitato dalle dimensioni della macchina virtuale.<br/><br/> VM a2:4 dischi; VM A3:8 dischi; VM A4:16 dischi, con una dimensione massima di 1 TB per ogni disco. Ciò determina il pool di archiviazione di backup totale disponibile.<br/><br/> La quantità di dati di cui è possibile eseguire il backup dipende dal numero e dalle dimensioni dei dischi collegati.
**Conservazione dei dati del server di Backup di Microsoft Azure in una macchina virtuale di Azure** | Si consiglia di conservare i dati per un giorno nel disco di Azure DPM/MAB ed eseguire il backup da DPM/MAB nell'insieme di credenziali per un periodo di conservazione più lungo. In questo modo è possibile proteggere una maggiore quantità di dati tramite l'offload nel servizio Backup di Azure.

### <a name="modern-backup-storage-mbs"></a>Modern Backup Storage (MBS)

Da DPM 2016/MAB V2 (in esecuzione su Windows Server 2016) e versioni successive, è possibile sfruttare i vantaggi di Modern backup storage (MBS).

- I backup di MBS vengono archiviati in un disco ReFS (Resilient File System).
- MBS usa la clonazione dei blocchi ReFS per un backup più rapido e un uso più efficiente dello spazio di archiviazione.
- Quando si aggiungono volumi al pool di archiviazione DPM/MAB locale, questi vengono configurati con lettere di unità. È quindi possibile configurare l'archiviazione dei carichi di lavoro in volumi diversi.
- Quando si creano gruppi protezione dati per eseguire il backup dei dati in DPM o nel server di Backup di Microsoft Azure, si seleziona l'unità che si intende usare. Ad esempio, è possibile archiviare i backup per SQL o altri carichi di lavoro di IOPS elevati in un'unità a prestazioni elevate e archiviare i carichi di lavoro di cui è stato eseguito il backup con minore frequenza in un'unità di prestazioni inferiore.

## <a name="supported-backups-to-mabs"></a>Backup nel server di Backup di Microsoft Azure supportati

Per informazioni sui vari server e carichi di lavoro che è possibile proteggere con server di Backup di Azure, vedere la [matrice di protezione server di backup di Azure](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Backup in DPM supportati

Per informazioni sui vari server e carichi di lavoro che è possibile proteggere con Data Protection Manager, vedere l'articolo su [cosa è possibile eseguire il backup di DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019).

- I carichi di lavoro del cluster sottoposti a backup da DPM/MAB devono trovarsi nello stesso dominio di DPM/MAB o in un dominio figlio o trusted.
- È possibile usare l'autenticazione NTLM/del certificato per eseguire il backup dei dati in gruppi di lavoro o domini non attendibili.

## <a name="next-steps"></a>Passaggi successivi

- [Vedere altre informazioni](backup-architecture.md#architecture-back-up-to-dpmmabs) sull'architettura del server di Backup di Microsoft Azure.
- [Rivedere](backup-support-matrix-mars-agent.md) gli scenari supportati per l'agente di Servizi di ripristino di Microsoft Azure.
- [Installare](backup-azure-microsoft-azure-backup.md) un server di Backup di Microsoft Azure.
- [Configurare DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
