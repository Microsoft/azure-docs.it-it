---
title: Serie EV3 e serie Esv3
description: Specifiche per le macchine virtuali serie EV3 e Esv3.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 86de6f94dc5bf44836fad37680a439d26d66ce6d
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837075"
---
# <a name="ev3-and-esv3-series"></a>Serie Ev3 e Esv3

Le serie EV3 e Esv3 vengono eseguite su Intel® Xeon® Platinum 8272CL (Cascade Lake), o Intel® Xeon® 8171M 2,1 GHz (Skylake) o il processore Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) in una configurazione con Hyper-Threading, fornendo una proposta di valore migliore per la maggior parte dei carichi di lavoro per uso generico e portando l'allineamento dei EV3 con le VM per utilizzo generico della maggior parte degli altri cloud.  La memoria è stata estesa (da 7 GiB/vCPU a 8 GiB/vCPU) mentre i limiti di rete e dei dischi sono stati modificati in base al core per consentire l'allineamento con il passaggio all'hyperthreading. La serie Ev3 rappresenta un passo avanti rispetto alle dimensioni delle macchine virtuali con memoria elevata delle famiglie D/Dv2.

## <a name="ev3-series"></a>Serie Ev3

Le istanze della serie EV3 vengono eseguite su Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) o i processori Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) e includono la tecnologia Intel Turbo Boost 2,0. Le istanze della serie Ev3 sono ideali per applicazioni aziendali a uso intensivo di memoria.

L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare dischi di archiviazione Premium, usare le dimensioni ESv3. I prezzi e i contatori di fatturazione per le dimensioni ESv3 sono uguali a quelli della serie Ev3.

La funzionalità della VM serie EV3 Intel® tecnologia Hyper-Threading.

Unità di calcolo di Azure: 160-190

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

Live Migration: Supportato

Manutenzione con mantenimento della memoria: Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Schede di interfaccia di rete max | Larghezza di banda di rete |
|---|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2|1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2|2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4|4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8|8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8|10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8|16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8|24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8|30000 |
| Standard_E64i_v3 <sup>1, 2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8|30000 |

<sup>1</sup> dimensioni core vincolate disponibili.

<sup>2</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

## <a name="esv3-series"></a>Serie Esv3

Le istanze della serie Esv3 sono eseguite su Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) o sul processore Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), che offre la tecnologia Intel Turbo Boost 2,0 e Usa archiviazione Premium. Le istanze della serie Esv3 sono ideali per applicazioni aziendali a uso intensivo di memoria.

La funzionalità della VM serie Esv3 Intel® tecnologia Hyper-Threading.

Unità di calcolo di Azure: 160-190

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

Live Migration: Supportato

Manutenzione con mantenimento della memoria: Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps (dimensioni della cache in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2|2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4|4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8|8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8|24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |

<sup>1</sup> dimensioni core vincolate disponibili.

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

Altre informazioni sui tipi di dischi: [tipi di disco](./linux/disks-types.md#ultra-disk)


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.