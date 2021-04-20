---
title: Dimensioni delle macchine virtuali
description: Elenca le diverse dimensioni disponibili per le macchine virtuali in Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 07/21/2020
ms.author: jushiman
ms.openlocfilehash: f2a4083e8cbbdbfbf971c441d8942b48e712c472
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740026"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Dimensioni delle macchine virtuali in Azure

Questo articolo descrive le dimensioni e le opzioni disponibili per le macchine virtuali di Azure che è possibile usare per eseguire app e carichi di lavoro. Offre anche considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse. 

:::image type="content" source="media/sizes/azurevmsthumb.jpg" alt-text="Video di YouTube per la selezione delle dimensioni giuste per la macchina virtuale." link="https://youtu.be/zOSvnJFd3ZM":::

| Type | Dimensioni | Descrizione |
|------|-------|-------------|
| [Utilizzo generico](sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4, Ddsv4  | Rapporto equilibrato tra CPU e memoria. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. |
| [Con ottimizzazione per il calcolo](sizes-compute.md) | F, Fs, Fsv2 | Rapporto elevato tra CPU e memoria. Soluzione idonea per server Web con livelli medi di traffico, dispositivi di rete, processi batch e server applicazioni. |
| [Ottimizzate per la memoria](sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Rapporto elevato tra memoria e CPU. Soluzione ideale per server di database relazionali, cache medio-grandi e analisi in memoria.                 |
| [Ottimizzate per l'archiviazione](sizes-storage.md) | Lsv2 | I/O e velocità effettiva del disco elevati ideali per i database NoSQL, SQL e Big Data, data warehousing e database transazionali di grandi dimensioni.  |
| [GPU](sizes-gpu.md) | NC, NCv2, NCv3, NCasT4_v3, ND, NDv2, NV, NVv3, NVv4 | Macchine virtuali specializzate ottimizzate per livelli intensivi di rendering della grafica e modifica di video, nonché per il training e l'inferenza dei modelli (ND) con apprendimento profondo. disponibili con GPU singole o più GPU. |
| [High Performance Computing (HPC)](sizes-hpc.md) | HB, HBv2, HC, H | Le nostre macchine virtuali con CPU più veloci e potenti, con interfacce di rete ad alta velocità effettiva facoltative (RDMA). |

- Per informazioni sui prezzi delle diverse dimensioni, vedere le pagine dei prezzi per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) o [Windows.](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows)
- Per la disponibilità delle dimensioni di VM nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).
- Per trovare i limiti generali delle VM di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Per altre informazioni sul modo in cui Azure assegna un nome alle macchine virtuali, vedere [Convenzioni di](./vm-naming-conventions.md)denominazione delle dimensioni delle macchine virtuali di Azure .

## <a name="rest-api"></a>API REST

Per informazioni sull'uso dell'API REST per query relative alle dimensioni delle macchine virtuali, vedere gli argomenti seguenti:

- [List available virtual machine sizes for resizing](/rest/api/compute/virtualmachines/listavailablesizes) (Elencare le dimensioni delle macchine virtuali disponibili per il ridimensionamento)
- [List available virtual machine sizes for a subscription](/rest/api/compute/resourceskus/list) (Elencare le dimensioni delle macchine virtuali disponibili per una sottoscrizione)
- [List available virtual machine sizes in an availability set](/rest/api/compute/availabilitysets/listavailablesizes) (Elencare le dimensioni delle macchine virtuali disponibili in un set di disponibilità)

## <a name="acu"></a>Unità di calcolo di Azure

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.

## <a name="benchmark-scores"></a>Punteggi di benchmark

Altre informazioni sulle prestazioni di calcolo per le macchine virtuali Linux usando i [punteggi di benchmark CoreMark](./linux/compute-benchmark-scores.md).

Altre informazioni sulle prestazioni di calcolo per le macchine virtuali Windows usando i punteggi [di benchmark SPECInt](./windows/compute-benchmark-scores.md).

## <a name="manage-costs"></a>Gestire i costi

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle diverse dimensioni di macchina virtuale disponibili:

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- Vedere la pagina [Generazione precedente](sizes-previous-gen.md) per informazioni sulle serie A Standard, Dv1 (D1-4 e D11-14 v1) e A8-A11
