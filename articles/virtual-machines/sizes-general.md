---
title: Dimensioni delle macchine virtuali di Azure-utilizzo generico | Microsoft Docs
description: Elenca le diverse dimensioni di utilizzo generico disponibili per le macchine virtuali in Azure. Elenca le informazioni sul numero di vCPU, dischi dati e NIC, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda di rete per le dimensioni di questa serie.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: fc263eb6fbe6c6402aaf529229bb7025f070b8d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81269670"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali di utilizzo generico

Le dimensioni delle macchine virtuali di utilizzo generico offrono un rapporto CPU-memoria equilibrato. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. Questo articolo fornisce informazioni sulle offerte per l'elaborazione di utilizzo generico.

- Le macchine virtuali della [serie AV2](av2-series.md) possono essere distribuite su diversi tipi di hardware e processori. Le VM serie A offrono configurazioni di memoria e prestazioni della CPU ideali per i carichi di lavoro di base, ad esempio quelli di sviluppo e test. La dimensione è limitata in base all'hardware per offrire prestazioni del processore coerenti per l'istanza in esecuzione, indipendentemente dall'hardware in cui è distribuita. Per determinare l'hardware fisico in cui viene distribuita questa dimensione, eseguire una query nell'hardware virtuale dall'interno della macchina virtuale. I casi d'uso di esempio includono server di sviluppo e test, server Web con poco traffico, database da piccoli a medi, modelli di prova e repository di codice.

  > [!NOTE]
  > Le macchine virtuali a8-A11 sono pianificate per il ritiro in 3/2021. Per ulteriori informazioni, vedere la [Guida alla migrazione HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

- [Serie B in sequenza](sizes-b-series-burstable.md) Le VM sono ideali per i carichi di lavoro che non necessitano continuamente delle prestazioni complete della CPU, ad esempio server Web, database di piccole dimensioni e ambienti di sviluppo e test. Questi carichi di lavoro hanno in genere requisiti relativi alle prestazioni che prevedono burst. La serie B offre a questi clienti la possibilità di acquistare dimensioni per la VM con prestazioni baseline in relazione al prezzo, che consentono all'istanza della VM di accumulare crediti quando la VM utilizza meno prestazioni di quelle base. Quando la VM ha accumulato crediti, può eseguire il burst rispetto alla baseline della VM, usando fino al 100% della CPU quando l'applicazione richiede prestazioni superiori per la CPU.

- Le [serie Dav4 e Dasv4](dav4-dasv4-series.md) sono nuove dimensioni che usano il processore AMD 2.35 GHz EPYC<sup>TM</sup> 7452 in una configurazione multithread con una cache L3 fino a 256 MB che dedicano 8 MB della cache L3 a ogni 8 core che aumentano le opzioni dei clienti per l'esecuzione dei carichi di lavoro per uso generico. La serie Dav4 e la serie Dasv4 hanno le stesse configurazioni di memoria e disco della serie D & Dsv3.

- La [serie DCv2](dcv2-series.md) può aiutare a proteggere la riservatezza e l'integrità dei dati e del codice durante l'elaborazione nel cloud pubblico. Questi computer sono supportati dall'ultima generazione del processore Intel XEON E-2288G con la tecnologia SGX. Con la tecnologia Intel Turbo Boost, questi computer possono arrivare fino a 5.0 GHz. Le istanze della serie DCv2 consentono ai clienti di creare applicazioni sicure basate su enclave per proteggere il codice e i dati in uso.

- [Serie dv2 e Dsv2](dv2-dsv2-series.md) Le macchine virtuali, una versione successiva alla serie D originale, sono caratterizzate da una CPU più potente e da una configurazione ottimale di CPU a memoria che li rende adatti alla maggior parte dei carichi di lavoro di produzione. La serie dv2 è più veloce del 35% rispetto alla serie D. La serie dv2 è in esecuzione su Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o i processori Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) con la tecnologia Intel Turbo Boost 2,0. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

- [Serie dv3 e Dsv3](dv3-dsv3-series.md) Le macchine virtuali vengono eseguite sui processori Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) in una configurazione con hyperthreading, offrendo una proposta di valore migliore per i carichi di lavoro per uso generico. La memoria è stata estesa (da ~3,5 GiB/vCPU a 4 GiB/vCPU) mentre i limiti di rete e dei dischi sono stati modificati in base al core per consentire l'allineamento con il passaggio all'hyperthreading. La serie dv3 non ha più le dimensioni delle macchine virtuali a memoria elevata della serie D/dv2, che sono state spostate nella [serie EV3 e Esv3](ev3-esv3-series.md)con ottimizzazione per la memoria.

Esempi di casi d'uso di serie D sono le applicazioni di livello aziendale, i database relazionali, la memorizzazione nella cache in memoria e l'analisi.

## <a name="other-sizes"></a>Altre dimensioni

- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
