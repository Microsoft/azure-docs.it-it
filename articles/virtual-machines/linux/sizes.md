---
title: Linux VM sizes in Azure
description: Elenca le diverse dimensioni disponibili per le macchine virtuali Linux in Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 246ab6551667f54e3ef4ec8f91573d9aaa98d64c
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758405"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Dimensioni delle macchine virtuali Linux in Azure

Questo articolo descrive le dimensioni e le opzioni disponibili per le macchine virtuali di Azure che è possibile usare per eseguire le app Linux e i carichi di lavoro. Offre anche considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse. Questo articolo è disponibile anche per le [macchine virtuali Windows](../windows/sizes.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json).

| Type | Dimensioni | Descrizione |
|------|-------|-------------|
| [Utilizzo generico](../sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2  | Rapporto equilibrato tra CPU e memoria. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. |
| [Ottimizzate per il calcolo](../sizes-compute.md) | Fsv2 | Rapporto elevato tra CPU e memoria. Soluzione idonea per server Web con livelli medi di traffico, dispositivi di rete, processi batch e server applicazioni. |
| [Ottimizzate per la memoria](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2 | Rapporto elevato tra memoria e CPU. Soluzione ideale per server di database relazionali, cache medio-grandi e analisi in memoria.                 |
| [Ottimizzate per l'archiviazione](../sizes-storage.md) | Lsv2 | I/O e velocità effettiva del disco elevati ideali per i database NoSQL, SQL e Big Data, data warehousing e database transazionali di grandi dimensioni.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (anteprima), NV, NVv3, NVv4 | Macchine virtuali specializzate ottimizzate per livelli intensivi di rendering della grafica e modifica di video, nonché per il training e l'inferenza dei modelli (ND) con apprendimento profondo. disponibili con GPU singole o più GPU. |
| [High Performance Computing (HPC)](../sizes-hpc.md) | HB, HBv2, HC, H | Le nostre macchine virtuali con CPU più veloci e potenti, con interfacce di rete ad alta velocità effettiva facoltative (RDMA). |

- Per informazioni sui prezzi di varie dimensioni, vedere [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Per la disponibilità delle dimensioni di VM nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).
- Per trovare i limiti generali delle VM di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-subscription-service-limits.md).
- Altre informazioni su come le [unità di calcolo di Azure](../acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.

## <a name="rest-api"></a>API REST

Per informazioni sull'uso dell'API REST per query relative alle dimensioni delle macchine virtuali, vedere gli argomenti seguenti:

- [List available virtual machine sizes for resizing](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes) (Elencare le dimensioni delle macchine virtuali disponibili per il ridimensionamento)
- [List available virtual machine sizes for a subscription](https://docs.microsoft.com/rest/api/compute/resourceskus/list) (Elencare le dimensioni delle macchine virtuali disponibili per una sottoscrizione)
- [List available virtual machine sizes in an availability set](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes) (Elencare le dimensioni delle macchine virtuali disponibili in un set di disponibilità)

## <a name="acu"></a>Unità di calcolo di Azure

Altre informazioni su come le [unità di calcolo di Azure](../acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.

## <a name="benchmark-scores"></a>Punteggi di benchmark

Altre informazioni sulle prestazioni di calcolo per le macchine virtuali Linux usando i [punteggi di benchmark CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle diverse dimensioni di macchina virtuale disponibili:

- [Utilizzo generico](../sizes-general.md)
- [Ottimizzate per il calcolo](../sizes-compute.md)
- [Ottimizzate per la memoria](../sizes-memory.md)
- [Ottimizzate per l'archiviazione](../sizes-storage.md)
- [GPU](../sizes-gpu.md)
- [High Performance Computing (HPC)](../sizes-hpc.md)
- Vedere la pagina [Generazione precedente](../sizes-previous-gen.md) per informazioni sulle serie A Standard, Dv1 (D1-4 e D11-14 v1) e A8-A11