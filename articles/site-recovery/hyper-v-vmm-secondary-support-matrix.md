---
title: Supporto per Matrix-ripristino di emergenza Hyper-V in un sito VMM secondario con Azure Site Recovery
description: Offre un riepilogo del supporto per la replica delle macchine virtuali Hyper-V di cloud VMM in un sito secondario con Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 1126a85ed22ee17879767a93ca75dc76dd04b747
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74132957"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Matrice di supporto per il ripristino di emergenza delle macchine virtuali Hyper-V in un sito secondario

Questo articolo riepiloga gli elementi supportati quando si usa il servizio [Azure Site Recovery](site-recovery-overview.md) per replicare le VM Hyper-V gestite in Cloud System Center Virtual Machine Manager (VMM) in un sito secondario. Per replicare macchine virtuali Hyper-V in Azure, vedere [questa matrice di supporto](hyper-v-azure-support-matrix.md).

> [!NOTE]
> È possibile eseguire la replica in un sito secondario solo quando gli host Hyper-V sono gestiti nei cloud VMM.


## <a name="host-servers"></a>Server host

**Sistema operativo** | **Dettagli**
--- | ---
R2 per Windows Server 2012 | I server devono eseguire gli aggiornamenti più recenti.
Windows Server 2016 |  I cloud VMM 2016 con una combinazione di host Windows Server 2016 e 2012 R2 non sono attualmente supportati.<br/><br/> Le distribuzioni con aggiornamenti da System Center 2012 R2 VMM 2012 R2 a System Center 2016 non sono attualmente supportate.


## <a name="replicated-vm-support"></a>Supporto di macchine virtuali replicate

Nella tabella seguente viene descritto il supporto del sistema operativo per i computer replicati con Site Recovery. Qualsiasi carico di lavoro può essere in esecuzione sul sistema operativo supportato.

**Versione di Windows** | **Hyper-V (con VMM)**
--- | ---
Windows Server 2016 | Qualsiasi sistema operativo guest [supportato da Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) in Windows Server 2016 
R2 per Windows Server 2012 | Qualsiasi sistema operativo guest [supportato da Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) in Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Archiviazione di computer Linux

È possibile eseguire la replica solo di computer Linux con le caratteristiche di archiviazione seguenti:

- File system (EXT3, ETX4, ReiserFS, XFS).
- Software con percorsi multipli: mapper dispositivi.
- Gestore volumi: LVM2.
- I server fisici con archiviazione del controller HP CCISS non sono supportati.
- Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Configurazione di rete - Macchine virtuali host/guest

**Configuration** | **Supportato**  
--- | --- 
Host - Gruppo NIC | Sì 
Host - VLAN | Sì 
Host - IPv4 | Sì 
Host - IPv6 | No 
VM guest - Gruppo NIC | No
VM guest - IPv4 | Sì
VM guest - IPv6 | No
VM guest - Windows/Linux - Indirizzo IP statico | Sì
VM guest - Più NIC | Sì


## <a name="storage"></a>Archiviazione

### <a name="host-storage"></a>Archiviazione su host

**Archiviazione (host)** | **Supportato**
--- | --- 
NFS | N/D
SMB 3.0 |  Sì
SAN (iSCSI) | Sì
Percorsi multipli (MPIO) | Sì

### <a name="guest-or-physical-server-storage"></a>Archiviazione su server fisico o guest

**Configuration** | **Supportato**
--- | --- | 
VMDK |  N/D
VHD/VHDX | Sì (fino a 16 dischi)
VM di seconda generazione | Sì
Disco cluster condiviso | No
Disco crittografato | No
UEFI| N/D
NFS | No
SMB 3.0 | No
RDM | N/D
Disco superiore a 1 TB | Sì
Volume con disco con striping superiore a 1 TB<br/><br/> LVM | Sì
Spazi di archiviazione | Sì
Aggiunta/rimozione a caldo disco | No
Esclusione disco | Sì
Percorsi multipli (MPIO) | Sì

## <a name="vaults"></a>Insiemi di credenziali

**Azione** | **Supportato**
--- | --- 
Spostare gli insiemi di credenziali tra i gruppi di risorse: all'interno o tra le sottoscrizioni |  No
Spostare le risorse di archiviazione, la rete e le VM di Azure tra i gruppi di risorse: all'interno o tra le sottoscrizioni | No

## <a name="azure-site-recovery-provider"></a>Provider di Azure Site Recovery

Il provider coordina le comunicazioni tra server VMM. 

**Più recente** | **Aggiornamenti**
--- | --- 
5.1.19 ([disponibile dal portale](https://aka.ms/downloaddra)) | [Funzionalità e correzioni più recenti](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Passaggi successivi

[Eseguire la replica di VM Hyper-V nel cloud VMM in un sito secondario](tutorial-vmm-to-vmm.md)

