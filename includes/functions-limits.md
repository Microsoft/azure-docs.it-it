---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: aa75d1d57f44bb1a4d6513823ac97ac9917b260f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934467"
---
| Risorsa |[Piano a consumo](../articles/azure-functions/consumption-plan.md)|[Piano Premium](../articles/azure-functions/functions-premium-plan.md)|[Piano dedicato](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|[Durata del timeout](../articles/azure-functions/functions-scale.md#timeout) predefinita (min) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|[Durata del timeout ](../articles/azure-functions/functions-scale.md#timeout) massima (min) |10 | Senza limiti<sup>7</sup> | Senza limiti<sup>2</sup> | unbounded | unbounded |
| Numero massimo di connessioni in uscita (per istanza) | 600 attive (1200 totali) | unbounded | unbounded | unbounded | unbounded |
| Dimensioni massime della richiesta (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Dipende dal cluster |
| Lunghezza massima della stringa di query<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Dipende dal cluster |
| Lunghezza massima dell'URL della richiesta<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Dipende dal cluster |
|[Unità di calcolo di Azure](../articles/virtual-machines/acu.md) per istanza | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [Prezzi del servizio Azure Kubernetes](https://azure.microsoft.com/pricing/details/container-service/) |
| Dimensioni massime della memoria (GB per istanza) | 1.5 | 3,5-14 | 1,75-14 | 3,5-14 | Qualsiasi nodo è supportato |
| App per le funzioni per piano |100 |100 |Senza limiti<sup>4</sup> | unbounded | unbounded |
| [Piani del servizio app](../articles/app-service/overview-hosting-plans.md) | 100 per [area](https://azure.microsoft.com/global-infrastructure/regions/) |100 per gruppo di risorse |100 per gruppo di risorse | - | - |
| Archiviazione<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | n/d |
| Domini personalizzati per applicazione</a> |500<sup>6</sup> |500 |500 | 500 | n/d |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/configure-ssl-bindings.md) |Connessione SNI SSL senza limiti inclusa | Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse | Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse | n/d |

<sup>1</sup> Per impostazione predefinita, il timeout per il runtime di Funzioni 1.x in un piano di servizio app non prevede limiti.  
<sup>2</sup> Richiede l'impostazione del piano di servizio app su [Always On](../articles/azure-functions/dedicated-plan.md#always-on). Pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard.  
<sup>3</sup> Questi limiti vengono [impostati nell'host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> Il numero effettivo di app per le funzioni che è possibile ospitare dipende dall'attività delle app, dalle dimensioni delle istanze del computer e dall'uso delle risorse corrispondente.  
<sup>5</sup> Il limite di archiviazione corrisponde alle dimensioni totali del contenuto nello spazio di archiviazione temporanea in tutte le app nello stesso piano di servizio app. Il piano a consumo usa File di Azure per l'archiviazione temporanea.  
<sup>6</sup> Quando l'app per le funzioni è ospitata in un [piano a consumo](../articles/azure-functions/consumption-plan.md), è supportata solo l'opzione CNAME. Per le app per le funzioni in un [piano Premium](../articles/azure-functions/functions-premium-plan.md) o in un [piano di servizio app](../articles/azure-functions/dedicated-plan.md), è possibile eseguire il mapping di un dominio personalizzato usando un record CNAME o A.  
<sup>7</sup> Garantito per un massimo di 60 minuti.  
<sup>8</sup> I ruoli di lavoro ospitano app di clienti. e sono disponibili in tre dimensioni fisse: Una vCPU/3,5 GB di RAM, due vCPU/7 GB di RAM oppure quattro vCPU/14 GB di RAM.