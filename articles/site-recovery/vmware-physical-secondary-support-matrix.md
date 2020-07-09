---
title: Supporto per il ripristino di emergenza VMware/fisico in un sito secondario con Azure Site Recovery
description: Questo articolo presenta un riepilogo del supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in un sito secondario con Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: c6f0f48df32db0beb9c0a57982d9bc87b26538d8
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135147"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in un sito secondario

Questo articolo presenta un riepilogo degli elementi supportati quando si usa il servizio [Azure Site Recovery](site-recovery-overview.md) per eseguire il ripristino di emergenza di server fisici Windows/Linux o macchine virtuali VMware in un sito secondario VMware.

- Se si vuole replicare macchine virtuali VMware o server fisici in Azure, fare riferimento a [questa matrice di supporto](vmware-physical-azure-support-matrix.md).
- Se si vuole replicare macchine virtuali Hyper-V in un sito secondario, fare riferimento a [questa matrice di supporto](hyper-v-azure-support-matrix.md).

> [!NOTE]
> La replica di macchine virtuali VMware locali e server fisici viene eseguita usando InMage Scout. InMage Scout è incluso nella sottoscrizione del servizio Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Annuncio di fine del supporto
Lo scenario Site Recovery per la replica tra i data center fisici o VMware locali sta raggiungendo la fine del supporto.

- Dal 2018 agosto lo scenario non può essere configurato nell'insieme di credenziali di servizi di ripristino e il software Inmage Scout non può essere scaricato dall'insieme di credenziali. Le distribuzioni esistenti saranno supportate.
- - Dal 31 dicembre 2020 lo scenario non sarà più supportato.
I partner esistenti possono caricare nuovi clienti nello scenario finché non termina il supporto.
- Durante il 2018 e il 2019 verranno rilasciati due aggiornamenti:

    - Aggiornamento 7: consente di risolvere problemi di conformità e di configurazione di rete e fornisce il supporto di TLS 1.2.
    - Aggiornamento 8: aggiunge il supporto per i sistemi operativi Linux RHEL/CentOS 7.3/7.4/7.5 e per SUSE 12.
    - Dopo l'Aggiornamento 8 non verranno rilasciati ulteriori aggiornamenti. Vi sarà un supporto limitato a hotfix per i sistemi operativi aggiunti nell'Aggiornamento 8 e correzioni di bug basate sul massimo sforzo.

## <a name="host-servers"></a>Server host

**Sistema operativo** | **Dettagli**
--- | ---
Server vCenter | vCenter 5.5, 6.0 e 6.5<br/><br/> Se si esegue la versione 6.0 o 6.5, sono supportate solo le funzionalità della versione 5.5.


## <a name="replicated-vm-support"></a>Supporto di macchine virtuali replicate

Nella tabella seguente viene descritto il supporto del sistema operativo per i computer replicati con Site Recovery. Qualsiasi carico di lavoro può essere in esecuzione sul sistema operativo supportato.

**Sistema operativo** | **Dettagli**
--- | ---
Windows Server | Windows Server 2016 a 64 bit, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con almeno il SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> CentOs 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
NFS | Sì 
SMB 3.0 | N/D 
SAN (iSCSI) | Sì 
Percorsi multipli (MPIO) | Sì 

### <a name="guest-or-physical-server-storage"></a>Archiviazione su server fisico o guest

**Configuration** | **Supportato** 
--- | --- 
VMDK | Sì 
VHD/VHDX | N/D 
VM di seconda generazione | N/D 
Disco cluster condiviso | Sì 
Disco crittografato | No 
UEFI| Sì 
NFS | No 
SMB 3.0 | No 
RDM | Sì 
Disco superiore a 1 TB | Sì 
Volume con disco con striping superiore a 1 TB<br/><br/> LVM | Sì 
Spazi di archiviazione | No 
Aggiunta/rimozione a caldo disco | Sì 
Esclusione disco | Sì 
Percorsi multipli (MPIO) | N/D 

## <a name="vaults"></a>Insiemi di credenziali

**Azione** | **Supportato** 
--- | --- 
Spostare gli insiemi di credenziali tra i gruppi di risorse: all'interno o tra le sottoscrizioni | No 
Spostare le risorse di archiviazione, la rete e le VM di Azure tra i gruppi di risorse: all'interno o tra le sottoscrizioni | No 

## <a name="mobility-service-and-updates"></a>Servizio Mobility e aggiornamenti

Il servizio Mobility coordina la replica fra server VMware locali/server fisici e sito secondario. Quando si configura la replica, accertarsi di disporre dell'ultima versione del servizio Mobility e di altri componenti.

| **Update** | **Dettagli** |
| --- | --- |
|Aggiornamenti di Scout | Gli aggiornamenti di Scout sono cumulativi. <br/><br/> [Informazioni introduttive e su come scaricare](vmware-physical-secondary-disaster-recovery.md#updates) gli aggiornamenti più recenti di Scout |
|Aggiornamenti dei componenti | Gli aggiornamenti di Scout includono gli aggiornamenti di tutti i componenti, tra cui server RX, server di configurazione, server di destinazione di elaborazione e master, server vContinuum e server di origine che si vuole proteggere.<br/><br/> [Altre informazioni](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates)|


## <a name="next-steps"></a>Passaggi successivi

Scaricare il [Manuale dell'utente di InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Eseguire la replica di VM Hyper-V nel cloud VMM in un sito secondario](./hyper-v-vmm-disaster-recovery.md)
- [Replicare VM VMware e server fisici in un sito secondario](./vmware-physical-secondary-disaster-recovery.md)
