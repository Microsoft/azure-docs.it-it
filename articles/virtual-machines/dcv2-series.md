---
title: Serie DC - Macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie DC.
author: susaxen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 1cbd4c92ec4c124cfd384f45a4cd6f47fdc60be6
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180167"
---
# <a name="dcsv2-series"></a>Serie DCsv2


La serie DCsv2 consente di proteggere la riservatezza e l'integrità dei dati e del codice durante l'elaborazione nel cloud pubblico. Queste macchine sono supportate dall'ultima generazione di processori Intel XEON E-2288G con tecnologia SGX. Con la tecnologia Intel Turbo Boost, queste macchine possono arrivare fino a 5,0 GHz. Le istanze della serie DCsv2 consentono ai clienti di creare applicazioni sicure basate su enclave per proteggere il codice e i dati mentre sono in uso.

I casi d'uso di esempio includono: condivisione tra più parti di dati riservati, rilevamento delle frodi, antiriciclaggio, blockchain, analisi di utilizzo riservata, analisi di Business Intelligence e Machine Learning riservato.

[Archiviazione Premium](premium-storage-performance.md): supportato *<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): non supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): non supportato<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 2<br>

*Tranne che per Standard_DC8_v2

| Dimensione             | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Schede di interfaccia di rete max/Larghezza di banda di rete prevista (Mbps) | Memoria EPC (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- Le macchine virtuali della serie DCsv2 sono [macchine virtuali di seconda generazione](./generation-2.md#creating-a-generation-2-vm) e supportano solo immagini `Gen2`.
- Attualmente disponibile nelle aree elencate [qui](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).
- Generazione precedente di macchine virtuali di calcolo riservato: [Serie DC](sizes-previous-gen.md#preview-dc-series)
- Creare le macchine virtuali DCsv2 usando il [portale di Azure](./linux/quick-create-portal.md) o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



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
