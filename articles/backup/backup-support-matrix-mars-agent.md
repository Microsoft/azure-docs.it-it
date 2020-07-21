---
title: Matrice di supporto per l'agente MARS
description: Questo articolo riepiloga il supporto di backup di Azure quando si esegue il backup dei computer che eseguono l'agente di Servizi di ripristino di Microsoft Azure (MARS).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 5ff9510dfa31bb947d50b1a91fb7f73c2d767471
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538650"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matrice di supporto per il backup con l'agente di Servizi di ripristino di Microsoft Azure

È possibile usare il [servizio backup di Azure](backup-overview.md) per eseguire il backup di computer e app locali ed eseguire il backup di macchine virtuali (VM) di Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto quando si usa l'agente di Servizi di ripristino di Microsoft Azure (MARS) per eseguire il backup dei computer.

## <a name="the-mars-agent"></a>Agente MARS

Backup di Azure usa l'agente MARS per eseguire il backup dei dati da computer locali e VM di Azure a un insieme di credenziali di servizi di ripristino di backup in Azure. L'agente MARS può:

- Eseguire nei computer Windows locali in modo che possano eseguire il backup direttamente in un insieme di credenziali di servizi di ripristino di backup in Azure.
- Eseguire sulle macchine virtuali Windows in modo che possano eseguire il backup direttamente in un insieme di credenziali.
- Eseguire in Backup di Microsoft Azure Server (MAB) o in un server System Center Data Protection Manager (DPM). In questo scenario, i computer e i carichi di lavoro eseguono il backup in MAB o nel server DPM. L'agente MARS esegue quindi il backup di questo server in un insieme di credenziali in Azure.

> [!NOTE]
>Backup di Azure non supporta la regolazione automatica del clock per l'ora legale (DST). Modificare i criteri per assicurarsi che venga tenuto conto dell'ora legale per evitare la discrepanza tra l'ora effettiva e l'ora di backup pianificata.

Le opzioni di backup variano a seconda della posizione in cui è installato l'agente. Per altre informazioni, vedere [architettura di backup di Azure con l'agente Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Per informazioni su MAB e sull'architettura di backup di DPM, vedere backup [in DPM o MAB](backup-architecture.md#architecture-back-up-to-dpmmabs). Vedere anche [requisiti](backup-support-matrix-mabs-dpm.md) per l'architettura di backup.

**Installazione** | **Dettagli**
--- | ---
Scaricare la versione più recente dell'agente MARS | È possibile scaricare la versione più recente dell'agente dall'insieme di credenziali oppure [scaricarla direttamente](https://aka.ms/azurebackup_agent).
Installazione diretta in un computer | È possibile installare l'agente MARS direttamente in un server Windows locale o in una VM Windows in cui è in esecuzione uno dei [sistemi operativi supportati](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).
Installazione in un server di backup | Quando si configura DPM o il server di Backup di Microsoft Azure per eseguire il backup in Azure, si scarica e installa nel server l'agente di Servizi di ripristino di Microsoft Azure. È possibile installare l'agente nei [sistemi operativi supportati](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) nella matrice di supporto del server di backup.

> [!NOTE]
> Per impostazione predefinita, le macchine virtuali di Azure abilitate per il backup hanno un'installazione dell'estensione di backup di Azure. Questa estensione esegue il backup dell'intera macchina virtuale. È possibile installare ed eseguire l'agente di Servizi di ripristino di Microsoft Azure in una macchina virtuale di Azure insieme all'estensione se si intende eseguire il backup di cartelle e file specifici anziché della macchina virtuale completa.
> Quando si esegue l'agente MARS in una macchina virtuale di Azure, viene eseguito il backup di file o cartelle presenti nell'archivio temporaneo della macchina virtuale. I backup non riescono se i file o le cartelle vengono rimossi dall'archiviazione temporanea o se l'archiviazione temporanea viene rimossa.

## <a name="cache-folder-support"></a>Supporto della cartella cache

Quando si usa l'agente MARS per eseguire il backup dei dati, l'agente acquisisce uno snapshot dei dati e lo archivia in una cartella della cache locale prima di inviare i dati ad Azure. La cartella cache (Scratch) presenta diversi requisiti:

**Cache** | **Dettagli**
--- | ---
Dimensione |  Lo spazio disponibile nella cartella della cache deve essere almeno compreso tra 5 e 10% delle dimensioni complessive dei dati di backup.
Location | La cartella della cache deve essere archiviata localmente nel computer di cui viene eseguito il backup e deve essere online. La cartella della cache non deve trovarsi in una condivisione di rete, in un supporto rimovibile o in un volume offline.
Cartella | La cartella della cache non deve essere crittografata in un volume deduplicato o in una cartella compressa, di tipo sparse o con un punto di analisi.
Modifiche alla posizione | È possibile modificare il percorso della cache arrestando il motore di backup ( `net stop bengine` ) e copiando la cartella della cache in una nuova unità. Assicurarsi che la nuova unità disponga di spazio sufficiente. Aggiornare quindi due voci del registro di sistema in **HKLM\Software\Microsoft\Windows Azure Backup** (**config/ScratchLocation** e **config/CloudBackupProvider/ScratchLocation**) al nuovo percorso e riavviare il motore.

## <a name="networking-and-access-support"></a>Supporto delle funzionalità di rete e dell'accesso

### <a name="url-and-ip-access"></a>URL e accesso IP

L'agente di Servizi di ripristino di Microsoft Azure deve poter accedere a questi URL:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.windowsazure.com
- *. MicrosoftOnline.com
- *. Windows.net

E a questi indirizzi IP:

- 20.190.128.0/18
- 40.126.0.0/18

L'accesso a tutti gli URL e gli indirizzi IP elencati in precedenza usa il protocollo HTTPS sulla porta 443.

### <a name="azure-expressroute-support"></a>Supporto di Azure ExpressRoute

È possibile eseguire il backup dei dati tramite Azure ExpressRoute con il peering pubblico (disponibile per i circuiti precedenti) e il peering Microsoft. Il backup sul peering privato non è supportato.

Con peering pubblico: garantire l'accesso ai seguenti domini/indirizzi:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

Con il peering Microsoft, selezionare i seguenti servizi/aree e i valori della community pertinenti:

- Azure Active Directory (12076:5060)
- Area Microsoft Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)
- Archiviazione di Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)

Per ulteriori informazioni, vedere i [requisiti di routing di ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>Il peering pubblico è deprecato per i nuovi circuiti.

### <a name="throttling-support"></a>Supporto della limitazione della larghezza di banda della rete

**Funzionalità** | **Dettagli**
--- | ---
Controllo della larghezza di banda | Supportata. Nell'agente MARS usare **modifica proprietà** per regolare la larghezza di banda.
Limitazione della larghezza di banda della rete | Non disponibile per computer sottoposti a backup che eseguono Windows Server 2008 R2, Windows Server 2008 SP2 o Windows 7.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

>[!NOTE]
> L'agente MARS non supporta gli SKU di Windows Server Core.

Per eseguire il backup direttamente in Azure nei sistemi operativi elencati di seguito, è possibile usare l'agente MARS:

1. Server Windows locali
2. Macchine virtuali di Azure che eseguono Windows

I sistemi operativi devono essere di 64 bit ed eseguire gli aggiornamenti e i pacchetti di servizi più recenti. Nella tabella seguente sono riepilogati questi sistemi operativi:

**Sistema operativo** | **File/cartelle** | **Stato del sistema** | **Requisiti del software/modulo**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Sì | No |  Controllare la versione del server corrispondente per i requisiti software/modulo
Windows 8.1 (Enterprise, Pro)| Sì |No | Controllare la versione del server corrispondente per i requisiti software/modulo
Windows 8 (Enterprise, Pro) | Sì | No | Controllare la versione del server corrispondente per i requisiti software/modulo
Windows Server 2016 (Standard, Datacenter, Essentials) | Sì | Sì | -.NET 4,5 <br> -Windows PowerShell <br> -Versione più recente compatibile di Microsoft VC + + Redistributable <br> -Microsoft Management Console (MMC) 3,0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Sì | Sì | -.NET 4,5 <br> -Windows PowerShell <br> -Versione più recente compatibile di Microsoft VC + + Redistributable <br> -Microsoft Management Console (MMC) 3,0
Windows Server 2012 (Standard, Datacenter, Foundation) | Sì | Sì |-.NET 4,5 <br> - Windows PowerShell <br> -Versione più recente compatibile di Microsoft VC + + Redistributable <br> -Microsoft Management Console (MMC) 3,0 <br> -Gestione e manutenzione immagini distribuzione (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (standard, Workgroup) | Sì | No | -.NET 4,5 <br> -Windows PowerShell <br> -Versione più recente compatibile di Microsoft VC + + Redistributable <br> -Microsoft Management Console (MMC) 3,0
Windows Server 2019 (Standard, Datacenter, Essentials) | Sì | Sì | -.NET 4,5 <br> -Windows PowerShell <br> -Versione più recente compatibile di Microsoft VC + + Redistributable <br> -Microsoft Management Console (MMC) 3,0

Per ulteriori informazioni, vedere i [sistemi operativi supportati da MAB e DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>Sistemi operativi alla fine del supporto

I sistemi operativi seguenti sono al termine del supporto ed è vivamente consigliabile aggiornare il sistema operativo per continuare a rimanere protetti.

Se gli impegni esistenti impediscono l'aggiornamento del sistema operativo, è consigliabile eseguire la migrazione dei server Windows alle macchine virtuali di Azure e sfruttare i backup delle VM di Azure per continuare a rimanere protetti. Visitare la [pagina](https://azure.microsoft.com/migration/windows-server/) relativa alla migrazione per ulteriori informazioni sulla migrazione di Windows Server.

Per gli ambienti locali o ospitati, in cui non è possibile aggiornare il sistema operativo o eseguire la migrazione ad Azure, attivare gli aggiornamenti della sicurezza estesa affinché i computer continuino a rimanere protetti e supportati. Si noti che solo edizioni specifiche sono idonee per gli aggiornamenti della sicurezza estesi. Per ulteriori informazioni, visitare la [pagina delle domande frequenti](https://www.microsoft.com/windows-server/extended-security-updates) .

| **Sistema operativo**                                       | **File/cartelle** | **Stato del sistema** | **Requisiti del software/modulo**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, starter) | Sì               | No                 | Controllare la versione del server corrispondente per i requisiti software/modulo |
| Windows Server 2008 R2 (standard, Enterprise, Datacenter, Foundation) | Sì               | Sì                | -.NET 3,5, .NET 4,5 <br>  -Windows PowerShell <br>  -Compatibile con Microsoft VC + + Redistributable <br>  -Microsoft Management Console (MMC) 3,0 <br>  -Gestione e manutenzione immagini distribuzione (DISM.exe) |
| Windows Server 2008 SP2 (standard, Datacenter, Foundation)  | Sì               | No                 | -.NET 3,5, .NET 4,5 <br>  -Windows PowerShell <br>  -Compatibile con Microsoft VC + + Redistributable <br>  -Microsoft Management Console (MMC) 3,0 <br>  -Gestione e manutenzione immagini distribuzione (DISM.exe) <br>  -Server virtuale 2005 base + KB KB948515 |

## <a name="backup-limits"></a>Limiti relativi a Backup

### <a name="size-limits"></a>Limiti di dimensioni

Backup di Azure limita le dimensioni di un'origine dati file o cartella di cui è possibile eseguire il backup. Gli elementi di cui si esegue il backup da un singolo volume non possono superare le dimensioni riepilogate in questa tabella:

**Sistema operativo** | **Limite dimensioni**
--- | ---
Windows Server 2012 o versioni successive |54400 GB
Windows Server 2008 R2 SP1 |1\.700 GB
Windows Server 2008 SP2| 1\.700 GB
Windows 8 o versione successiva| 54400 GB
Windows 7| 1\.700 GB

### <a name="other-limitations"></a>Altre limitazioni

- MARS non supporta la protezione di più computer con lo stesso nome in un unico insieme di credenziali.

## <a name="supported-file-types-for-backup"></a>Tipi di file supportati per il backup

**Tipo** | **Supporto**
--- | ---
Crittografati<sup>*</sup>| Supportata.
Compresso | Supportata.
Sparse | Supportata.
Compresso e sparse |Supportata.
Collegamenti reali| Non supportato. Ignorato.
Reparse point| Non supportato. Ignorato.
Crittografato e sparse |Non supportato. Ignorato.
Flusso compresso| Non supportato. Ignorato.
Flusso di tipo sparse| Non supportato. Ignorato.
OneDrive (i file sincronizzati sono flussi sparse)| Non supportato.
Cartelle con Replica DFS abilitata | Non supportato.

\*Verificare che l'agente MARS abbia accesso ai certificati richiesti per accedere ai file crittografati. I file inaccessibili verranno ignorati.

## <a name="supported-drives-or-volumes-for-backup"></a>Unità o volumi supportati per il backup

**Unità/volume** | **Supporto** | **Dettagli**
--- | --- | ---
Volumi di sola lettura| Non supportato | Il servizio Copia Shadow del volume (VSS) funziona solo se il volume è scrivibile.
Volumi offline| Non supportato |VSS funziona solo se il volume è online.
Condivisione di rete| Non supportato |Il volume deve essere locale nel server.
Volumi bloccati da BitLocker| Non supportato |Il volume deve essere sbloccato prima dell'avvio del backup.
Identificazione del file System| Non supportato |È supportato solo NTFS.
Supporti rimovibili| Non supportato |Lo stato di tutte le origini degli elementi di backup deve essere *fisso* .
Unità deduplicate | Supportato | Il servizio Backup di Azure converte i dati deduplicati in dati normali. Consente di ottimizzare, crittografare, archiviare e inviare i dati all'insieme di credenziali.

## <a name="support-for-initial-offline-backup"></a>Supporto per il backup offline iniziale

Backup di Azure supporta il *seeding offline* per trasferire i dati di backup iniziali in Azure usando dischi. Questo supporto è utile se il backup iniziale è probabilmente compreso nell'intervallo di dimensioni di terabyte (TBs). Il backup offline è supportato nei seguenti casi:

- Backup diretto di file e cartelle nei computer locali in cui è in esecuzione l'agente MARS.
- Backup di carichi di lavoro e file da un server DPM o da un server di Backup di Microsoft Azure.

Non è possibile usare il backup offline per i file di stato del sistema.

## <a name="support-for-data-restoration"></a>Supporto per il ripristino dei dati

Usando la funzionalità di [ripristino istantaneo](backup-instant-restore-capability.md) di backup di Azure, è possibile ripristinare i dati prima che vengano copiati nell'insieme di credenziali. Il computer di cui viene eseguito il backup deve eseguire .NET Framework 4.5.2 o versione successiva.

Non è possibile ripristinare i backup in un computer di destinazione in cui è in esecuzione una versione precedente del sistema operativo. Ad esempio, è possibile ripristinare un backup eseguito da un computer che esegue Windows 7 in Windows 8 o versione successiva. Tuttavia, un backup eseguito da un computer che esegue Windows 8 non può essere ripristinato in un computer che esegue Windows 7.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull' [architettura di backup che usa l'agente Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Informazioni sulle funzionalità supportate quando si [esegue l'agente Mars su MAB o su un server DPM](backup-support-matrix-mabs-dpm.md).
