---
title: Dimensioni delle macchine virtuali di Azure-ottimizzate per il calcolo | Microsoft Docs
description: Elenca le diverse dimensioni ottimizzate per il calcolo disponibili per le macchine virtuali in Azure. Elenca informazioni sul numero di vCPU, dischi dati e schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 01eba7bff571a8db25591b8bfa5c5e712511588f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557693"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per il calcolo

Le dimensioni delle macchine virtuali ottimizzate per il calcolo hanno un rapporto elevato tra CPU e memoria. Queste dimensioni sono valide per server Web con traffico medio, appliance di rete, processi batch e server applicazioni. Questo articolo fornisce informazioni sul numero di vCPU, dischi dati e schede di rete. Sono inoltre incluse informazioni sulla velocità effettiva di archiviazione e sulla larghezza di banda di rete per ogni dimensione di questo raggruppamento.

La [serie Fsv2](fsv2-series.md) viene eseguita con processori Intel® Xeon® Platinum 8272CL (Cascade Lake) di seconda generazione e processori Intel® Xeon® Platinum 8168 (Skylake). Include una velocità di clock di base di livello principale di 3,4 GHz e una frequenza Turbo Single Core massima di 3,7 GHz. Le istruzioni Intel® AVX-512 sono nuove per i processori Intel scalabili. Queste istruzioni forniscono fino a un incremento delle prestazioni di 2X ai carichi di lavoro di elaborazione vettoriali nelle operazioni a virgola mobile a precisione singola e doppia. In altre parole, sono molto veloci per qualsiasi carico di lavoro di calcolo.

Con un prezzo di listino orario più basso, la serie Fsv2 presenta il migliore rapporto prezzo-prestazioni nel portfolio Azure basato sull'unità di calcolo di Azure (ACU, Azure Compute Unit) per ogni vCPU.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.

Per altre informazioni sul modo in cui Azure denomina le proprie macchine virtuali, vedere [convenzioni di denominazione di macchine virtuali di Azure](./vm-naming-conventions.md).