---
title: Novità del server di Backup di Microsoft Azure
description: Il server di Backup di Microsoft Azure offre funzionalità avanzate di backup per la protezione di macchine virtuali, file e cartelle, carichi di lavoro e altro ancora.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 39050d0f658e29b82f270f1fe53026e2fb80bfa1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332764"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>Novità di Backup di Microsoft Azure Server (MAB)

## <a name="whats-new-in-mabs-v3-ur1"></a>Novità di MAB V3 UR1

Backup di Microsoft Azure Server (MAB) versione 3 UR1 è l'aggiornamento più recente e include correzioni di bug critiche e altre funzionalità e miglioramenti. Per visualizzare l'elenco dei bug corretti e le istruzioni di installazione per MAB V3 UR1, vedere l'articolo [4534062](https://support.microsoft.com/help/4534062)della Knowledge Knowledge.

>[!NOTE]
>Il supporto per l'agente protezione 32 bit è deprecato con MAB V3 UR1. Vedere [deprecazione di 32 bit Protection Agent](#32-bit-protection-agent-deprecation).

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>Backup più veloci con archiviazione a livelli con unità SSD

In MAB V2 è stato introdotto [Modern backup storage](backup-mabs-add-storage.md) (MBS), che migliora l'utilizzo e le prestazioni dello spazio di archiviazione. MBS usa ReFS come file system sottostante ed è progettato l'uso dell'archiviazione ibrida come l'archiviazione a livelli.

Per ottenere scalabilità e prestazioni per MB è consigliabile usare una piccola percentuale (4% di archiviazione complessiva) di archiviazione Flash (SSD) con MAB V3 UR1 come volume a livelli in combinazione con l'archiviazione HDD di DPM. MAB V3 UR1 con archiviazione a livelli offre backup del 50-70% più veloci. Vedere l'articolo su DPM [configurare MBS con archiviazione a livelli](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage) per i passaggi necessari per configurare l'archiviazione a livelli.

### <a name="support-for-refs-volumes"></a>Supporto per i volumi ReFS

Con MAB V3 UR1, è possibile eseguire il backup dei volumi ReFS e dei carichi di lavoro distribuiti nel volume ReFS. È possibile eseguire il backup dei carichi di lavoro seguenti distribuiti nei volumi ReFS:

* Sistema operativo (64 bit) : Windows Server 2019, 2016, 2012 R2, 2012.
* SQL Server: SQL Server 2019, SQL Server 2017, 2016.
* Exchange: Exchange 2019, 2016.
* SharePoint: SharePoint 2019, 2016 con SP più recente.

>[!NOTE]
> Il backup di macchine virtuali Hyper-V archiviate in un volume ReFS è supportato con MAB V3

>IMPORTANTE Sono stati identificati alcuni problemi relativi al backup di volumi ReFS deduplicati. Stiamo lavorando per correggere questi aggiornamenti e aggiorniamo questa sezione non appena sarà disponibile una correzione. Fino ad allora, viene rimosso il supporto per il backup dei volumi ReFS deduplicati da MABSv3 UR1.

### <a name="azure-vmware-solution-protection-support"></a>Supporto per la protezione della soluzione VMware di Azure

Con MAB V3 UR1 è ora possibile proteggere le macchine virtuali distribuite nella [soluzione VMware di Azure](../azure-vmware/index.yml).

### <a name="vmware-parallel-backups"></a>Backup paralleli di VMware

Con MAB V3 UR1, tutti i backup di macchine virtuali VMware in un singolo gruppo protezione dati saranno paralleli, con un conseguente 25% di backup di VM più veloci.
Con le versioni precedenti di MAB, i backup paralleli venivano eseguiti solo tra gruppi protezione dati. Con MAB V3 UR1, i processi di delta replication VMware vengono eseguiti in parallelo. Per impostazione predefinita, il numero di processi da eseguire in parallelo è impostato su 8. Altre informazioni sui [backup paralleli VMware](backup-azure-backup-server-vmware.md#vmware-parallel-backups).

### <a name="disk-exclusion-for-vmware-vm-backup"></a>Esclusione del disco per il backup di macchine virtuali VMware

Con MAB V3 UR1 è possibile escludere dischi specifici da un backup di macchine virtuali VMware. Altre informazioni sull' [esclusione di dischi dal backup di macchine virtuali VMware](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup).  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>Supporto per un livello aggiuntivo di autenticazione per eliminare il backup online

Con MAB V3 UR1, viene aggiunto un ulteriore livello di autenticazione per le operazioni critiche. Verrà richiesto di immettere un PIN di sicurezza quando si esegue l' **arresto della protezione con le operazioni di eliminazione dei dati** .

### <a name="offline-backup-improvements"></a>Miglioramenti al backup offline

MAB V3 UR1 migliora l'esperienza del backup offline con il servizio importazione/esportazione di Azure. Per ulteriori informazioni, vedere la procedura aggiornata [qui](./backup-azure-backup-server-import-export.md).

>[!NOTE]
>L'aggiornamento porta anche l'anteprima per il backup offline usando Azure Data Box in MAB. [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)Per ulteriori informazioni, contattare.

### <a name="new-cmdlet-parameter"></a>Nuovo parametro del cmdlet

MAB V3 UR1 include un nuovo parametro **[-CheckReplicaFragmentation]**. Il nuovo parametro calcola la percentuale di frammentazione per una replica ed è inclusa nel cmdlet **Copy-DPMDatasourceReplica** .

### <a name="32-bit-protection-agent-deprecation"></a>deprecazione dell'agente protezione 32 bit

Con MAB V3 UR1, il supporto per l'agente protezione a 32 bit non è più supportato. Non sarà possibile proteggere i carichi di lavoro a 32 bit dopo l'aggiornamento del server MAB V3 a UR1. Eventuali agenti protezione a 32 bit esistenti saranno in uno stato disabilitato e i backup pianificati avranno esito negativo e l' **agente verrà disabilitato** . Se si desidera mantenere i dati di backup per questi agenti, è possibile arrestare la protezione con l'opzione Mantieni dati. In caso contrario, è possibile rimuovere l'agente protezione.

>[!NOTE]
>Esaminare la [matrice di protezione aggiornata](./backup-mabs-protection-matrix.md) per informazioni sui carichi di lavoro supportati per la protezione con MAB UR 1.

## <a name="whats-new-in-mabs-v3-rtm"></a>Novità di MAB V3 RTM

Backup di Microsoft Azure Server versione 3 (MAB V3) include correzioni di bug critiche, supporto di Windows Server 2019, supporto di SQL 2017 e altre funzionalità e miglioramenti. Per visualizzare l'elenco dei bug corretti e le istruzioni di installazione di MABS V3, vedere l'articolo della knowledge base [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

In MABS V3 sono incluse le funzionalità seguenti:

### <a name="volume-to-volume-migration"></a>Migrazione da volume a volume

Con Modern Backup Storage (MBS) in MABS V2, è stata annunciata l'archiviazione con riconoscimento del carico di lavoro, in cui è possibile configurare alcuni carichi di lavoro per il backup in specifiche risorse di archiviazione, in base alle proprietà di archiviazione. Dopo la configurazione, tuttavia, può risultare necessario spostare i backup di alcune origini dati in altre risorse di archiviazione per un utilizzo ottimizzato delle risorse. MAB V3 offre la possibilità di eseguire la migrazione dei backup e configurarli in modo che vengano archiviati in un volume diverso in [tre passaggi](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

### <a name="prevent-unexpected-data-loss"></a>Evitare perdite di dati impreviste

Nelle aziende, MABS viene gestito da un team di amministratori. Sebbene esistano linee guida sull'archiviazione che devono essere seguite per l'esecuzione dei backup, specificare un volume non corretto come archivio di backup di MABS può causare la perdita di dati di importanza critica. Con MAB V3, è possibile impedire tali scenari configurando tali volumi come quelli non disponibili per l'archiviazione con [questi cmdlet di PowerShell](./backup-mabs-add-storage.md).

### <a name="custom-size-allocation"></a>Allocazione dimensioni personalizzate

Modern Backup Storage (MBS) utilizza l'archiviazione in modo mirato, in base a specifiche esigenze. A tale scopo, MAB calcola le dimensioni dei dati di cui viene eseguito il backup quando è configurato per la protezione. Se, tuttavia, molti file e cartelle vengono sottoposti a backup contemporaneamente, come nel caso di un file server, il calcolo delle dimensioni può richiedere molto tempo. Con MAB V3, è possibile configurare MAB in modo da accettare le dimensioni del volume come predefinite, anziché calcolare le dimensioni di ogni file, risparmiando tempo.

### <a name="optimized-cc-for-rct-vms"></a>CC ottimizzati per le macchine virtuali RCT

MABS usa RCT (la funzionalità di rilevamento delle modifiche nativa in Hyper-V), che riduce la necessità di verifiche della coerenza molto dispendiose in termini di tempo in scenari come arresti anomali della macchina virtuale. RCT offre una migliore resilienza del rilevamento delle modifiche incluso nei backup basati su snapshot di VSS. MABS V3 ottimizza ulteriormente il consumo di archiviazione e rete trasferendo solo i dati modificati durante le verifiche della coerenza.

### <a name="support-to-tls-12"></a>Support per TLS 1.2

TLS 1.2 è la modalità di comunicazione più sicura suggerita da Microsoft con il miglior livello di crittografia. MABS ora supporta la comunicazione TLS 1.2 tra MABS e i server protetti, per l'autenticazione basata su certificati e per i backup nel cloud.

### <a name="vmware-vm-protection-support"></a>Supporto della protezione di VM VMware

Il backup di VM VMware è ora supportato per le distribuzioni di produzione. MABS V3 offre quanto segue per la protezione di VM VMware:

* Supporto per vCenter ed ESXi 6.5, insieme al supporto per 5.5 e 6.0.
* Protezione automatica delle macchine virtuali VMware nel cloud. Se le nuove macchine virtuali VMware vengono aggiunte a una cartella protetta, vengono protette automaticamente su disco e cloud.
* Miglioramenti dell'efficienza per il ripristino del percorso alternativo di VMware.

### <a name="sql-2017-support"></a>Supporto per SQL 2017

MABS V3 può essere installato con SQL 2017 come database di MABS. È possibile eseguire l'aggiornamento di SQL Server da SQL 2016 a SQL 2017 o una nuova installazione. Con MABS V3 è anche possibile eseguire il backup dei carichi di lavoro di SQL 2017 in ambienti cluster e non cluster.

### <a name="windows-server-2019-support"></a>Supporto per Windows Server 2019

MABS V3 può essere installato in Windows Server 2019. Per usare MAB V3 con WS2019, è possibile aggiornare il sistema operativo a WS2019 prima di installare/aggiornare a MAB V3 oppure è possibile aggiornare il sistema operativo dopo l'installazione o l'aggiornamento di V3 su WS2016.

MABS V3 è una versione completa e può essere installato direttamente in Windows Server 2016, Windows Server 2019 o può essere aggiornato da MABS V2. Prima di eseguire l'aggiornamento o di installare il server di Backup V3, leggere i prerequisiti di installazione.
In [questo articolo](./backup-azure-microsoft-azure-backup.md#software-package) sono disponibili altre informazioni sulla procedura di installazione/aggiornamento per MABS.

> [!NOTE]
>
> MABS ha la stessa base di codice di System Center Data Protection Manager. MABS V3 corrisponde a Data Protection Manager 1807. MAB V3 UR1 equivale a Data Protection Manager 2019 UR1.

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni su come preparare il server o iniziare a proteggere un carico di lavoro:

* [Preparare i carichi di lavoro del server di backup](backup-azure-microsoft-azure-backup.md)
* [Usare il server di backup per eseguire il backup di un server VMware](backup-azure-backup-server-vmware.md)
* [Usare il server di backup per eseguire il backup di SQL Server](backup-azure-sql-mabs.md)
* [Usare Modern Backup Storage con il server di backup](backup-mabs-add-storage.md)
