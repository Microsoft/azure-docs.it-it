---
title: 'Architettura: ripristino di emergenza di VMware/fisico in un sito secondario con Azure Site Recovery'
description: Questo articolo fornisce una panoramica dei componenti e dell'architettura usati durante il ripristino di emergenza di macchine virtuali VMware locali o di server fisici Windows/Linux in un sito VMware secondario con Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b0a46dcf8fe298494a53713f122b1bda8ce07e5e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "73954583"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>Architettura per la replica di server VMware o fisici in un sito locale secondario

Questo articolo descrive l'architettura e i processi usati per la replica, il failover e il ripristino di emergenza di macchine virtuali (VM) VMware locali o server Windows/Linux fisici in un sito VMware secondario usando [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componenti dell'architettura

**Area** | **Componente** | **Dettagli**
--- | --- | ---
**Azure** | Questo scenario viene distribuito con InMage Scout. | Per ottenere InMage Scout, è necessaria una sottoscrizione di Azure.<br/><br/> Dopo aver creato un insieme di credenziali di Servizi di ripristino, scaricare InMage Scout e installare gli aggiornamenti più recenti per configurare la distribuzione.
**Server di elaborazione** | Situato nel sito primario | Il server di elaborazione viene distribuito per gestire la memorizzazione nella cache, la compressione e l'ottimizzazione dei dati.<br/><br/> Gestisce anche l'installazione push di Unified Agent nei computer da proteggere.
**Server di configurazione** | Situato nel sito secondario | Il server di configurazione gestisce, configura e monitora la distribuzione usando il sito Web di gestione o la console vContinuum.
**Server vContinuum** | Facoltativa. Installato nella stessa posizione del server di configurazione. | Fornisce una console per la gestione e il monitoraggio dell'ambiente protetto.
**Server master di destinazione** | Situato nel sito secondario | Questo server contiene i dati replicati. Riceve i dati dal server di elaborazione e crea un computer di replica nel sito secondario e include i punti di conservazione dei dati.<br/><br/> Il numero di server di destinazione master necessari dipende dal numero di computer da proteggere.<br/><br/> Se si vuole eseguire il failback al sito primario, deve essere disponibile anche un server di destinazione master. Unified Agent viene installato in questo server.
**Server VMware ESX/ESXi e vCenter** |  Le macchine virtuali sono ospitate in host ESX/ESXi. Gli host vengono gestiti con un server vCenter. | È necessaria un'infrastruttura VMware per replicare le VM VMware.
**VM/server fisici** |  Unified Agent installato nei server fisici e nelle macchine virtuali VMware da replicare. | L'agente funge da provider di comunicazioni tra tutti i componenti.

## <a name="replication-process"></a>Processo di replica

1. Configurare i server del componente in ogni sito, ovvero configurazione, elaborazione, destinazione master, e installare Unified Agent nei computer da replicare.
2. Dopo la replica iniziale, l'agente installato in ogni computer invia le modifiche della replica differenziale al server di elaborazione.
3. Il server di elaborazione ottimizza i dati e li trasferisce nel server di destinazione master nel sito secondario. Il server di configurazione gestisce il processo di replica.

**Figura 6: Replica da VMware a VMware**

![Da VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Passaggi successivi

[Configurare](vmware-physical-secondary-disaster-recovery.md) il ripristino di emergenza di macchine virtuali VMware e server fisici in un sito secondario.
