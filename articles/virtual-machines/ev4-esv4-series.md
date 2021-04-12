---
title: Serie Ev4 e Esv4-macchine virtuali di Azure
description: Specifiche per le macchine virtuali serie Ev4 e Esv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 677158c69ef15508ff9fc00e83ff87678cf9f983
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443970"
---
# <a name="ev4-and-esv4-series"></a>Serie Ev4 e Esv4

La serie Ev4 e Esv4 &reg; è in esecuzione sui processori Intel Xeon &reg; Platinum 8272CL (Cascade Lake) in una configurazione con Hyper-Threading, ideale per varie applicazioni aziendali con utilizzo intensivo di memoria e funzionalità fino a 504GIB di RAM. Offre una velocità di clock di tutti i core di 3,4 GHz.

> [!NOTE]
> Per le domande frequenti, vedere  [dimensioni delle macchine virtuali di Azure senza disco temporaneo locale](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Serie Ev4

Le dimensioni della serie Ev4 vengono eseguite su Intel Xeon &reg; Platinum 8272CL (Cascade Lake). Le istanze della serie Ev4 sono ideali per applicazioni aziendali a uso intensivo di memoria. Le macchine virtuali serie Ev4 includono la &reg; tecnologia Intel Hyper-Threading.

L'archiviazione su disco dati remoto viene fatturata separatamente dalle macchine virtuali. Per usare i dischi di archiviazione Premium, usare le dimensioni Esv4. I prezzi e i contatori di fatturazione per le dimensioni Esv4 sono uguali a quelli della serie Ev4.

[ACU](acu.md): 195-210<br>
[Archiviazione Premium](premium-storage-performance.md): non supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): non supportato<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): supportati<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1<br>
[Rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md): supportata (*richiede almeno 4 vCPU*)<br>
[Dischi del sistema operativo temporaneo](ephemeral-os-disks.md): non supportati <br>
<br>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | Solo archiviazione remota | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | Solo archiviazione remota | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | Solo archiviazione remota | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | Solo archiviazione remota | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | Solo archiviazione remota | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | Solo archiviazione remota | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Solo archiviazione remota | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | Solo archiviazione remota | 32| 8|30000 |


## <a name="esv4-series"></a>Serie Esv4

Le dimensioni della serie Esv4 vengono eseguite su Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Le istanze della serie Esv4 sono ideali per applicazioni aziendali a uso intensivo di memoria. Le macchine virtuali serie Evs4 includono la &reg; tecnologia Intel Hyper-Threading. L'archiviazione su disco dati remoto viene fatturata separatamente dalle macchine virtuali.

[ACU](acu.md): 195-210<br>
[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): supportati<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1 e 2<br>
[Rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md): supportata (*richiede almeno 4 vCPU*)<br>
[Dischi del sistema operativo temporaneo](ephemeral-os-disks.md): non supportati <br>
<br>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Solo archiviazione remota | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Solo archiviazione remota | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Solo archiviazione remota | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Solo archiviazione remota | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Solo archiviazione remota | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Solo archiviazione remota | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Solo archiviazione remota | 32 | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Solo archiviazione remota | 32 | 80000/1200 | 8|30000 |
| Standard_E80is_v4 <sup>2</sup> | 80 | 504 | Solo archiviazione remota | 32 | 80000/1500 | 8|30000 |

<sup>1</sup> [Disponibili dimensioni core vincolate)](./constrained-vcpu.md).

<sup>2</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Altre dimensioni e informazioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

Calcolatore prezzi: [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)

Altre informazioni sui tipi di dischi: [tipi di disco](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
