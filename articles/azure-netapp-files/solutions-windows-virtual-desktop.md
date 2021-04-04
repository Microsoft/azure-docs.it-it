---
title: Uso di desktop virtuali Windows con Azure NetApp Files | Microsoft Docs
description: Fornisce le procedure consigliate e i progetti di esempio per la distribuzione di desktop virtuali Windows con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 0cd1f6210fbdb74e3fd511150157dccca3e92dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91932465"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>Vantaggi dell'uso di Azure NetApp Files con Desktop virtuale Windows 

Questo articolo fornisce indicazioni sulle procedure consigliate per la distribuzione di desktop virtuali Windows (WVD) con Azure NetApp Files.

Azure NetApp Files è un servizio di archiviazione di file a prestazioni elevate di Azure. Può fornire fino a 450.000 IOPS e latenza in millisecondi, in grado di supportare una scala molto elevata di distribuzioni di desktop virtuali Windows. È possibile regolare la larghezza di banda e modificare il livello di servizio dei volumi Azure NetApp Files su richiesta quasi istantaneamente senza sospendere l'i/o mantenendo l'accesso al piano dati. Questa funzionalità consente di ottimizzare facilmente la scalabilità della distribuzione di WVD per i costi. È anche possibile creare snapshot del volume temporizzati e con efficienza dello spazio senza influito sulle prestazioni del volume. Questa funzionalità consente di eseguire il rollback dei singoli contenitori di [profili utente FSLogix](../virtual-desktop/store-fslogix-profile.md) tramite una copia dalla `~snapshot` Directory o di eseguire istantaneamente il rollback dell'intero volume contemporaneamente tramite la funzionalità di ripristino del volume.  Con fino a 255 snapshot (rotazionali) per proteggere un volume dalla perdita o dal danneggiamento dei dati, gli amministratori hanno molte probabilità di annullare le operazioni eseguite.

## <a name="sample-blueprints"></a>Progetti di esempio

Nei progetti di esempio seguenti viene illustrata l'integrazione di desktop virtuale di Windows con Azure NetApp Files. In uno scenario basato su desktop in pool, gli utenti vengono indirizzati alla migliore sessione disponibile, ovvero l'host in [modalità larghezza-primo](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-method), nel pool, usando le [macchine virtuali](../virtual-desktop/windows-10-multisession-faq.md#what-is-windows-10-enterprise-multi-session)con più sessioni. D'altra parte, i desktop personali sono riservati agli scenari in cui ogni utente dispone di una propria macchina virtuale.

### <a name="pooled-desktop-scenario"></a>Scenario per desktop in pool

Per lo scenario in pool, il team di desktop virtuali Windows [consiglia](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) le seguenti indicazioni per numero di utenti a vCPU. Si noti che in questa raccomandazione non è specificata alcuna dimensione di macchina virtuale.

|     Tipo di carico di lavoro     |     Chiaro    |     Medio    |     Pesante    |
|-----------------------|--------------|---------------|--------------|
|     Utenti per vCPU    |     6        |     4         |     2        |


Questa raccomandazione viene confermata da un test LoginVSI dell'utente 500, che registra circa 62 "Knowledge/Media Users" su ogni macchina virtuale D16as_V4. 

Ad esempio, a 62 utenti per D16as_V4 macchina virtuale, Azure NetApp Files possibile supportare in modo semplice gli utenti 60.000 per ogni ambiente. Il test per valutare il limite superiore della macchina virtuale D32as_v4 è in corso. Se la raccomandazione utente WVD per vCPU è valida per la D32as_v4, più di 120.000 utenti si adattano a 1.000 macchine virtuali prima di inserire [il limite di 1.000 IP VNet](./azure-netapp-files-network-topologies.md), come illustrato nella figura seguente.  

![Scenario per desktop virtuali Windows in pool](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>Scenario desktop personale 

In uno scenario di desktop personale, nella figura seguente viene illustrata la raccomandazione per l'architettura di utilizzo generico. Viene eseguito il mapping degli utenti a Pod desktop specifici e ogni pod ha appena sotto 1.000 macchine virtuali, lasciando spazio per gli indirizzi IP propagati dal VNet di gestione. Azure NetApp Files è in grado di gestire con facilità più di 900 desktop personali per ogni pool host a sessione singola VNet, con il numero effettivo di macchine virtuali uguale a 1.000 meno il numero di host di gestione trovati nell'hub VNet. Se sono necessari più desktop personali, è facile aggiungere altri pod (pool host e reti virtuali), come illustrato nella figura seguente. 

![Scenario di desktop personale desktop virtuale Windows](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

Quando si compila un'architettura basata su POD come questa, l'assegnazione di utenti al Pod corretto al momento dell'accesso è fondamentale per garantire che gli utenti trovino sempre i propri profili utente. 

## <a name="next-steps"></a>Passaggi successivi

- [Architetture di soluzioni con Azure NetApp Files](azure-netapp-files-solution-architectures.md)