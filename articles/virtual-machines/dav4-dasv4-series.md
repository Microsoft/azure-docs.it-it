---
title: Serie Dav4 e Dasv4
description: Specifiche per le macchine virtuali serie Dav4 e Dasv4.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 2876339d1c11802bf47e5f1bd660fe5d07568410
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065372"
---
# <a name="dav4-and-dasv4-series"></a>Serie Dav4 e Dasv4

Le serie Dav4 e Dasv4 sono nuove dimensioni che usano il processore AMD 2.35 GHz EPYC<sup>TM</sup> 7452 in una configurazione multithread con una cache L3 fino a 256 MB che dedicano 8 MB di tale cache L3 a ogni 8 core che aumentano le opzioni dei clienti per l'esecuzione dei carichi di lavoro per uso generico. Le serie Dav4 e Dasv4 hanno le stesse configurazioni di memoria e disco della serie D e Dsv3.

## <a name="dav4-series"></a>Serie Dav4

[ACU](acu.md): 230-260<br>
[Archiviazione Premium](premium-storage-performance.md): non supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): non supportato<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): supportati<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1<br>
<br>

Le dimensioni della serie Dav4 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> 7452 che può ottenere una frequenza massima con boosting di 3.35 GHz. Le dimensioni della serie Dav4 offrono una combinazione di vCPU, memoria e archiviazione temporanea per la maggior parte dei carichi di lavoro di produzione. L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare l'unità SSD Premium, usare le dimensioni Dasv4. I prezzi e i contatori di fatturazione per le dimensioni Dasv4 sono uguali a quelli della serie Dav4.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Schede di interfaccia di rete max | Larghezza di banda di rete prevista (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 | 800 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 | 1600 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 | 3200 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 | 6400 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 | 12800 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000/1000/500 | 8 | 19200 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000/1000/500 | 8 | 25600 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000/1000/500 | 8 | 32000 |

## <a name="dasv4-series"></a>Serie Dasv4

[ACU](acu.md): 230-260<br>
[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): supportati<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1 e 2<br>
<br>

Le dimensioni della serie Dasv4 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> 7452 che può ottenere una frequenza massima con boosting di 3.35 GHz e usare l'unità SSD Premium. Le dimensioni della serie Dasv4 offrono una combinazione di vCPU, memoria e archiviazione temporanea per la maggior parte dei carichi di lavoro di produzione.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max | Larghezza di banda di rete prevista (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 (50)|3200/48|2 | 800 |
| Standard_D4as_v4|4|16|32|8|8000/64 (100)|6400/96|2 | 1600 |
| Standard_D8as_v4|8|32|64|16|16000/128 (200)|12800/192|4 | 3200 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600/384|8 | 6400 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200/768|8 | 12800 |
| Standard_D48as_v4|48|192|384|32|96000/1020 (1200)|76800/1148|8 | 19200 |
| Standard_D64as_v4|64|256|512|32|128000/1020 (1600)|80000/1200|8 | 25600 | 
| Standard_D96as_v4|96|384|768|32|192000/1020 (2400)|80000/1200|8 | 32000 |

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
