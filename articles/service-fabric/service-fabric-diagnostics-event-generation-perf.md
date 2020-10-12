---
title: Monitoraggio delle prestazioni di Azure Service Fabric
description: Informazioni sui contatori delle prestazioni per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75464708"
---
# <a name="performance-metrics"></a>Metriche delle prestazioni

Per comprendere le prestazioni del cluster e delle applicazioni in esecuzione al suo interno è necessario raccogliere alcune metriche. Per i cluster di Service Fabric è consigliabile raccogliere i contatori delle prestazioni seguenti.

## <a name="nodes"></a>Nodi

Per i computer presenti nel cluster è opportuno raccogliere i contatori delle prestazioni seguenti per comprendere meglio il carico di ogni computer e prendere decisioni appropriate sulla scalabilità del cluster.

| Categoria contatore | Nome contatore |
| --- | --- |
| Disco logico | Spazio disponibile su disco logico |
| PhysicalDisk(per Disk) | Lunghezza media coda lettura disco |
| PhysicalDisk(per Disk) | Lunghezza media coda di scrittura su disco |
| PhysicalDisk(per Disk) | Avg. Disk sec/Read |
| PhysicalDisk(per Disk) | Avg. Disk sec/Write |
| PhysicalDisk(per Disk) | Letture disco/sec |
| PhysicalDisk(per Disk) | Byte letti da disco/sec |
| PhysicalDisk(per Disk) | Scritture disco/sec |
| PhysicalDisk(per Disk) | Byte scritti su disco/sec |
| Memoria | MByte disponibili |
| PagingFile | % Usage |
| Processor(Total) | % di tempo processore |
| Process (per service) | % di tempo processore |
| Process (per service) | ID Process |
| Process (per service) | Private Bytes |
| Process (per service) | Thread Count |
| Process (per service) | Virtual Bytes |
| Process (per service) | Working Set |
| Process (per service) | Working Set - Private |
| Network Interface(all-instances) | Byte ricevuti |
| Network Interface(all-instances) | Byte inviati |
| Network Interface(all-instances) | Byte totali |
| Network Interface(all-instances) | Lunghezza della coda di output |
| Network Interface(all-instances) | Pacchetti in uscita scartati |
| Network Interface(all-instances) | Pacchetti ricevuti scartati |
| Network Interface(all-instances) | Errori pacchetti in uscita |
| Network Interface(all-instances) | Errori pacchetti ricevuti |

## <a name="net-applications-and-services"></a>Applicazioni e servizi .NET

Se si distribuiscono servizi .NET nel cluster, raccogliere i contatori seguenti. 

| Categoria contatore | Nome contatore |
| --- | --- |
| .NET CLR Memory (per service) | ID di processo |
| .NET CLR Memory (per service) | # Total committed Bytes |
| .NET CLR Memory (per service) | # Total reserved Bytes |
| .NET CLR Memory (per service) | # Bytes in all Heaps |
| .NET CLR Memory (per service) | Dimensione heap Large Object |
| .NET CLR Memory (per service) | Handle GC |
| .NET CLR Memory (per service) | # Gen 0 Collections |
| .NET CLR Memory (per service) | # Gen 1 Collections |
| .NET CLR Memory (per service) | # Gen 2 Collections |
| .NET CLR Memory (per service) | Percentuale tempo in GC |

### <a name="service-fabrics-custom-performance-counters"></a>Contatori delle prestazioni personalizzati di Service Fabric

Service Fabric genera una quantità significativa di contatori delle prestazioni personalizzati. Se è installato l'SDK, è possibile visualizzare l'elenco completo disponibile nel computer Windows mediante l'applicazione Performance Monitor (Start > Performance Monitor). 

Se si usa Reliable Actors, nelle applicazioni che si stanno distribuendo nel cluster aggiungere i contatori delle categorie `Service Fabric Actor` e `Service Fabric Actor Method`. Vedere [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md).

Analogamente, se si usa Reliable Services o Servizio remoto, sono presenti categorie di contatori `Service Fabric Service` e `Service Fabric Service Method` da cui è necessario raccogliere contatori. Vedere [Monitoraggio con Servizio remoto](service-fabric-reliable-serviceremoting-diagnostics.md) e [Contatori delle prestazioni di Reliable Services](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Se si usa Reliable Collection, infine, è consigliabile aggiungere il contatore `Avg. Transaction ms/Commit` della categoria `Service Fabric Transactional Replicator` per raccogliere la latenza di commit media per ogni transazione.


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [generazione di eventi a livello piattaforma](service-fabric-diagnostics-event-generation-infra.md) in Service Fabric
* Raccogliere metriche delle prestazioni tramite l'[agente di Log Analytics](service-fabric-diagnostics-oms-agent.md)
