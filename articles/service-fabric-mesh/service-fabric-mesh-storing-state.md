---
title: Opzioni di archiviazione dello stato in Azure Service Fabric mesh
description: Informazioni sull'archiviazione affidabile dello stato nelle applicazioni mesh Service Fabric eseguite su Azure Service Fabric Mesh.
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 651329b1b061f2cf5a06dbdc9d60f73cec64e7d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625550"
---
# <a name="state-management-with-service-fabric"></a>Gestione dello stato con Service Fabric

> [!IMPORTANT]
> L'anteprima di Azure Service Fabric mesh è stata ritirata. Le nuove distribuzioni non saranno più consentite tramite l'API Service Fabric mesh. Il supporto per le distribuzioni esistenti continuerà fino al 28 aprile 2021.
> 
> Per informazioni dettagliate, vedere il [ritiro anteprima di Azure Service Fabric mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Service Fabric supporta molte opzioni diverse per l'archiviazione dello stato. Per una panoramica concettuale dei modelli di gestione dello stato e Service Fabric, vedere [concetti relativi a Service Fabric: Stato del servizio](../service-fabric/service-fabric-concepts-state.md). Tutti questi stessi concetti si applicano se i servizi vengono eseguiti all'interno o all'esterno di mesh Service Fabric. 

Con mesh Service Fabric puoi distribuire facilmente una nuova applicazione e connetterla a un archivio dati esistente ospitato in Azure. Oltre a usare qualsiasi database remoto, sono disponibili diverse opzioni per l'archiviazione dei dati, a seconda che il servizio richieda l'archiviazione locale o remota. 

## <a name="volumes"></a>Volumi

I contenitori spesso usano dischi temporanei. I dischi sono temporanei, quindi si riceve un nuovo disco temporaneo e si perdono le informazioni in caso di arresto anomalo di un contenitore. È anche difficile condividere le informazioni sui dischi temporanei con altri contenitori. I volumi sono le directory che vengono montate all'interno di istanze del contenitore che è possibile usare per mantenere la persistenza dello stato. I volumi offrono una risorsa di archiviazione file per utilizzo generico e consentono di leggere/scrivere i file usando le normali API di file I/O del disco. La risorsa Volume spiega come montare una directory e quale risorsa di archiviazione di backup usare. È possibile scegliere Archiviazione file di Azure o un disco volume di Service Fabric per archiviare i dati.

![Il diagramma mostra il servizio, che passa a volume, che consente di Service Fabric volume affidabile su un disco locale replicato e di File di Azure volume nell'archiviazione di rete.][image3]

### <a name="service-fabric-reliable-volume"></a>Reliable Volume di Service Fabric

Reliable Volume di Service Fabric è un driver di volume Docker usato per montare un volume locale in un contenitore. Lettura e scrittura sono operazioni locali e veloci. I dati vengono replicati in nodi secondari e sono quindi estremamente disponibili. Anche il failover è veloce. Quando un contenitore si arresta in modo anomalo, esegue il failover in un nodo che contiene già una copia dei dati. Per un esempio, vedere [come distribuire un'app con Service Fabric volume affidabile](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Volume di File di Azure

Il volume di File di Azure è un driver di volume Docker usato per montare una condivisione di File di Azure in un contenitore. La risorsa di archiviazione di File di Azure usa l'archiviazione di rete, quindi le operazioni di lettura e scrittura vengono eseguite in rete. Rispetto a Reliable Volume di Service Fabric, l'archiviazione di File di Azure è meno efficiente, ma offre un'opzione più economica e completamente affidabile per i dati. Per un esempio, vedere [Archiviazione dello stato in un'applicazione di Azure Service Fabric Mesh tramite il montaggio di un volume basato su File di Azure all'interno del contenitore](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul modello dell'applicazione, consultare [risorse di Service Fabric](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
