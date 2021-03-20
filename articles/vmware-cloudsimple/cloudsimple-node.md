---
title: Panoramica della soluzione VMware di Azure di CloudSimple-nodes
description: Informazioni sui concetti relativi a CloudSimple, tra cui nodi, nodi di cui è stato effettuato il provisioning, un cloud privato e una soluzione VMware tramite SKU dei nodi CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7de4b5faa997d909089daedab7e48e5d5a6de2e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140837"
---
# <a name="cloudsimple-nodes-overview"></a>Panoramica sui nodi CloudSimple

I nodi sono i blocchi predefiniti di un cloud privato. Un nodo è:

* Un host di calcolo bare metal dedicato in cui è installato un hypervisor VMware ESXi  
* Unità di calcolo di cui è possibile effettuare il provisioning o la riserva per la creazione di cloud privati
* Disponibile per il provisioning o la riserva in un'area in cui è disponibile il servizio CloudSimple

Si crea un cloud privato dai nodi di cui è stato effettuato il provisioning. Per creare un cloud privato, sono necessari almeno tre nodi dello stesso SKU. Per espandere un cloud privato, aggiungere altri nodi.  È possibile aggiungere nodi a un cluster esistente o creare un nuovo cluster eseguendo il provisioning di nodi nel portale di Azure e associando i nodi al servizio CloudSimple.  Tutti i nodi di cui è stato effettuato il provisioning sono visibili nel servizio CloudSimple.  

## <a name="provisioned-nodes"></a>Nodi sottoposti a provisioning

I nodi di cui è stato effettuato il provisioning forniscono capacità con pagamento in base al consumo. I nodi di provisioning consentono di ridimensionare rapidamente il cluster VMware su richiesta. È possibile aggiungere nodi in base alle esigenze o eliminare un nodo di cui è stato effettuato il provisioning per ridurre il cluster VMware. I nodi di cui è stato effettuato il provisioning vengono fatturati su base mensile e addebitati alla sottoscrizione in cui viene effettuato il provisioning.

* Se si paga per la sottoscrizione di Azure tramite carta di credito, la carta verrà fatturata immediatamente.
* Se la fatturazione è fatturata, i costi vengono visualizzati nella fattura successiva.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>SKU della soluzione VMware per nodi CloudSimple

I tipi di nodi seguenti sono disponibili per il provisioning o la prenotazione.

| SKU           | CS28-nodo                 | CS36-nodo                 | CS36m-nodo                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | Stati Uniti orientali, Stati Uniti occidentali            | Stati Uniti orientali, Stati Uniti occidentali            | Europa occidentale                 |
| CPU           | 2x 2.2 GHz, 28 Core (56 HT) | 2x 2,3 GHz, 36 core (72 HT) | 2x 2,3 GHz, 36 core (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disco della cache    | 1,6-TB NVMe                 | 3,2-TB NVMe                 | 3,2-TB NVMe                 |
| Disco di capacità | 5,625 TB raw                | 11,25 TB raw                | 15,36 TB raw                |
| Tipo di archiviazione  | Tutti i flash                   | Tutti i flash                   | Tutti i flash                   |

## <a name="limits"></a>Limiti

I limiti dei nodi seguenti si applicano ai cloud privati.

| Risorsa | Limite |
|----------|-------|
| Numero minimo di nodi per la creazione di un cloud privato | 3 |
| Numero massimo di nodi in un cluster in un cloud privato | 16 |
| Numero massimo di nodi in un cloud privato | 64 |
| Numero minimo di nodi in un nuovo cluster | 3 |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire il [provisioning di nodi](create-nodes.md)
* Informazioni sui [cloud privati](cloudsimple-private-cloud.md)
