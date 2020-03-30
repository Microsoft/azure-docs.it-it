---
title: Supporto per il ripristino di emergenza delle macchine virtuali Hyper-V in Azure con Azure Site RecoverySupport for disaster recovery of Hyper-V VMs to Azure with Azure Site Recovery
description: Questo articolo presenta un riepilogo dei componenti supportati e dei requisiti per il ripristino di emergenza di macchine virtuali Hyper-V in Azure con Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 1/27/2020
ms.author: raynew
ms.openlocfilehash: d4409fe61bfe1f0a9fe74171f5b1ec471b9a6a26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258057"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Matrice di supporto per il ripristino di emergenza di macchine virtuali Hyper-V locali in Azure


Questo articolo riepiloga i componenti supportati e le impostazioni per il ripristino di emergenza di macchine virtuali Hyper-V locali in Azure con [Azure Site Recovery](site-recovery-overview.md).



## <a name="supported-scenarios"></a>Scenari supportati

**Scenario** | **Dettagli**
--- | ---
Hyper-V con Virtual Machine Manager <br> <br>| È possibile eseguire il ripristino di emergenza in Azure per le macchine virtuali in esecuzione in host Hyper-V gestiti nell'infrastruttura di System Center Virtual Machine Manager.<br/><br/> Si può distribuire questo scenario con il portale di Azure o tramite PowerShell.<br/><br/> Se gli host Hyper-V vengono gestiti da Virtual Machine Manager, è anche possibile eseguire il ripristino di emergenza in un sito locale secondario. Per altre informazioni su questo scenario, vedere [questa esercitazione](hyper-v-vmm-disaster-recovery.md).
Hyper-V senza Virtual Machine Manager | È possibile eseguire il ripristino di emergenza in Azure per le macchine virtuali in esecuzione in host Hyper-V non gestiti da Virtual Machine Manager.<br/><br/> Si può distribuire questo scenario con il portale di Azure o tramite PowerShell.

## <a name="on-premises-servers"></a>Server locali

**Server** | **Requisiti** | **Dettagli**
--- | --- | ---
Hyper-V (in esecuzione senza Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016 (inclusa l'installazione dei componenti di base del server), Windows Server 2012 R2 con gli aggiornamenti più recenti | Se si è già configurato Windows Server 2012 R2 con/o SCVMM 2012 R2 con Azure Site Recovery e si prevede di aggiornare il sistema operativo, seguire le indicazioni nella [documentazione](upgrade-2012R2-to-2016.md) correlata. 
Hyper-V (in esecuzione con Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Se si utilizza Virtual Machine Manager, gli host Windows Server 2019 devono essere gestiti in Virtual Machine Manager 2019. Analogamente, gli host Windows Server 2016 devono essere gestiti in Virtual Machine Manager 2016.<br/><br/> Nota: il failback nel percorso alternativo non è supportato per gli host Windows Server 2019.


## <a name="replicated-vms"></a>VM replicate


La tabella seguente riepiloga il supporto per VM. Site Recovery supporta i carichi di lavoro in esecuzione in un sistema operativo supportato.

 **Componente** | **Dettagli**
--- | ---
Configurazione della macchina virtuale | Le VM che eseguono la replica in Azure devono soddisfare i [requisiti di Azure](#azure-vm-requirements).
Sistema operativo guest | Qualsiasi sistema operativo guest [supportato per Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases).<br/><br/> Windows Server 2016 Nano Server non è supportato.


## <a name="vmdisk-management"></a>Gestione della macchina virtuale e del disco

**Azione** | **Dettagli**
--- | ---
Ridimensionare li disco nella macchina virtuale Hyper-V replicata | Non supportato. Disabilitare la replica, apportare la modifica e quindi riabilitare la replica per la macchina virtuale.
Aggiungere il disco nella macchina virtuale Hyper-V replicata | Non supportato. Disabilitare la replica, apportare la modifica e quindi riabilitare la replica per la macchina virtuale.

## <a name="hyper-v-network-configuration"></a>Configurazione di rete Hyper-V

**Componente** | **Hyper-V con Virtual Machine Manager** | **Hyper-V senza Virtual Machine Manager**
--- | --- | ---
Rete host: gruppo NIC | Sì | Sì
Rete host: VLAN | Sì | Sì
Rete host: IPv4 | Sì | Sì
Rest host: IPv6 | No | No
Rete delle macchine virtuali guest: gruppo NIC | No | No
Rete delle macchine virtuali guest: IPv4 | Sì | Sì
Rete delle macchine virtuali guest: IPv6 | No | Sì
Rete delle macchine virtuali guest: IP statico (Windows) | Sì | Sì
Rete delle macchine virtuali guest: IP statico (Linux) | No | No
Rete delle macchine virtuali guest: più NIC | Sì | Sì



## <a name="azure-vm-network-configuration-after-failover"></a>Configurazione di rete per VM di Azure (dopo il failover)

**Componente** | **Hyper-V con Virtual Machine Manager** | **Hyper-V senza Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Sì | Sì
ILB | Sì | Sì
ELB | Sì | Sì
Gestione traffico di Azure | Sì | Sì
Più NIC | Sì | Sì
IP riservato | Sì | Sì
IPv4 | Sì | Sì
Conservazione indirizzo IP di origine | Sì | Sì
Endpoint servizio di rete virtuale di Azure<br/> (senza firewall di Archiviazione di Azure) | Sì | Sì
Rete accelerata | No | No


## <a name="hyper-v-host-storage"></a>Archiviazione host Hyper-V

**Archiviazione** | **Hyper-V con Virtual Machine Manager** | **Hyper-V senza Virtual Machine Manager**
--- | --- | --- 
NFS | ND | ND
SMB 3.0 | Sì | Sì
SAN (iSCSI) | Sì | Sì
Percorsi multipli (MPIO). Testato con:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM per CLARiiON | Sì | Sì

## <a name="hyper-v-vm-guest-storage"></a>Archiviazione VM guest Hyper-V

**Archiviazione** | **Hyper-V con Virtual Machine Manager** | **Hyper-V senza Virtual Machine Manager**
--- | --- | ---
VMDK | ND | ND
VHD/VHDX | Sì | Sì
VM Gen 2 | Sì | Sì
EFI/UEFI<br></br>La macchina virtuale migrata in Azure verrà convertita automaticamente in una macchina virtuale di avvio del BIOS. La macchina virtuale deve eseguire solo Windows Server 2012 e versioni successive. Il disco del sistema operativo deve avere fino a cinque partizioni o meno e la dimensione del disco del sistema operativo deve essere inferiore a 300 GB.| Sì | Sì
Disco cluster condiviso | No | No
Disco crittografato | No | No
NFS | ND | ND
SMB 3.0 | No | No
RDM | ND | ND
Disco superiore a 1 TB | Sì, fino a 4.095 GB | Sì, fino a 4.095 GB
Disco: dimensioni logiche di settore e dimensioni fisiche a 4 KB | Non supportato: Gen 1/Gen 2 | Non supportato: Gen 1/Gen 2
Disco: settore fisico 4K logico e 512 byte | Sì |  Sì
Gestione volumi logici (LVM). LVM è supportata solo nei dischi dati. Azure ha un solo disco di sistema operativo. | Sì | Sì
Volume con disco con striping superiore a 1 TB | Sì | Sì
Spazi di archiviazione | No | No
Aggiunta/rimozione a caldo disco | No | No
Esclusione disco | Sì | Sì
Percorsi multipli (MPIO) | Sì | Sì

## <a name="azure-storage"></a>Archiviazione di Azure

**Componente** | **Hyper-V con Virtual Machine Manager** | **Hyper-V senza Virtual Machine Manager**
--- | --- | ---
Archiviazione con ridondanza locale | Sì | Sì
Archiviazione con ridondanza geografica | Sì | Sì
Archiviazione con ridondanza geografica e accesso in lettura | Sì | Sì
Archiviazione ad accesso sporadico | No | No
Archiviazione ad accesso frequente| No | No
BLOB in blocchi | No | No
Crittografia per dati inattivi (SSE)| Sì | Sì
Crittografia inattivi (CMK) <br></br> (Solo per il failover su dischi gestiti)| Sì (tramite il modulo PowerShell Az 3.3.0 in poi) | Sì (tramite il modulo PowerShell Az 3.3.0 in poi)
Archiviazione Premium | Sì | Sì
Servizio di importazione/esportazione | No | No
Account di Archiviazione di Azure con il firewall abilitatoAzure Storage accounts with firewall enabled | Sì. Per l'archiviazione e la cache di destinazione. | Sì. Per l'archiviazione e la cache di destinazione.
Modifica dell'account di archiviazione | No. L'account di archiviazione di Azure di destinazione non può essere modificato dopo l'abilitazione della replica. Per modificare, disabilitare e quindi riattivare il ripristino di emergenza. | No


## <a name="azure-compute-features"></a>Funzionalità di calcolo di Azure

**Funzionalità** | **Hyper-V con Virtual Machine Manager** | **Hyper-V senza Virtual Machine Manager**
--- | --- | ---
Set di disponibilità | Sì | Sì
HUB | Sì | Sì  
Dischi gestiti | Sì, per il failover.<br/><br/> Non è supportato il failback di dischi gestiti. | Sì, per il failover.<br/><br/> Non è supportato il failback di dischi gestiti.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Le macchine virtuali locali replicate in Azure devono soddisfare i requisiti per le VM di Azure riepilogati in questa tabella.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Site Recovery supporta tutti i sistemi operativi [supportati da Azure.](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)  | Il controllo dei prerequisiti ha esito negativo se non supportato.
Architettura del sistema operativo guest | 32 bit (Windows Server 2008)/64 bit | Il controllo dei prerequisiti ha esito negativo se non supportato.
Dimensioni disco del sistema operativo | Fino a 2.048 GB per le macchine virtuali di prima generazione.<br/><br/> Fino a 300 GB per le VM Gen 2.  | Il controllo dei prerequisiti ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo dei prerequisiti ha esito negativo se non supportato.
Conteggio dischi dati | Fino a 16  | Il controllo dei prerequisiti ha esito negativo se non supportato.
Dimensioni VHD dischi dati | Fino a 4.095 GB | Il controllo dei prerequisiti ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede |
VHD condiviso | Non supportate | Il controllo dei prerequisiti ha esito negativo se non supportato.
Disco FC | Non supportate | Il controllo dei prerequisiti ha esito negativo se non supportato.
Formato disco rigido | VHD  <br/><br/>  VHDX | In Site Recovery VHDX viene convertito automaticamente in VHD quando si esegue il failover in Azure. Quando si esegue il failback in locale, le macchine virtuali continuano a usare il formato VHDX.
BitLocker | Non supportate | Prima di abilitare la replica per una macchina virtuale occorre disabilitare BitLocker.
Nome della VM. | Tra 1 e 63 caratteri. Limitato a lettere, numeri e trattini. Il nome della macchina virtuale deve iniziare e terminare con una lettera o un numero. | Aggiornare il valore nelle proprietà della VM in Site Recovery.
Tipo di macchina virtuale | Prima generazione<br/><br/> Seconda generazione - Windows | Sono supportate le macchine virtuali di seconda generazione con disco del sistema operativo di base che include uno o più volumi di dati in formato VHDX e inferiori a 300 GB di spazio su disco.<br></br>Le macchine virtuali Linux di seconda generazione non sono supportate. [Scopri di più](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Azioni dell'insieme di credenziali dei Servizi di ripristino

**Azione** |  **Hyper-V con VMM** | **Hyper-V senza VMM**
--- | --- | ---
Spostamento insieme di credenziali tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No | No
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No | No

> [!NOTE]
> Quando si replicano le hyper-vm da locale ad Azure, è possibile eseguire la replica in un solo tenant di Active Directory da un ambiente specifico: sito Hyper-V o Hyper-V con VMM, se applicabile.


## <a name="provider-and-agent"></a>Provider e agente

Per assicurarsi che la distribuzione sia compatibile con le impostazioni in questo articolo, accertarsi di eseguire le versioni più recenti del provider e dell'agente.

**Nome** | **Descrizione** | **Dettagli**
--- | --- | --- 
Provider di Azure Site Recovery | Coordina le comunicazioni tra server locali e Azure <br/><br/> Hyper-V con Virtual Machine Manager: installato nei server Virtual Machine Manager<br/><br/> Hyper-V senza Virtual Machine Manager: installato negli host Hyper-V| Versione più recente: 5.1.2700.1 (disponibile dal portale di Azure)<br/><br/> [Funzionalità e correzioni più recenti](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agente di Servizi di ripristino di Microsoft Azure | Coordina la replica tra le macchine virtuali Hyper-V e Azure<br/><br/> Installato nei server Hyper-V locali (con o senza Virtual Machine Manager) | Agente più recente disponibile dal portale






## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [preparare Azure](tutorial-prepare-azure.md) per il ripristino di emergenza delle VM Hyper-V.
