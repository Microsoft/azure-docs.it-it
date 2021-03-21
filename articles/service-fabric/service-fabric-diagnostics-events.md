---
title: Eventi Service Fabric di Azure
description: Informazioni sugli eventi di Service Fabric forniti automaticamente per monitorare il cluster di Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 638b650e485ad3e83bd6021639a7e55b540d9cdc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75451721"
---
# <a name="service-fabric-events"></a>Eventi di Service Fabric 

La piattaforma Service Fabric scrive diversi eventi strutturati per le attività operative chiave eseguite all'interno del cluster. Queste variano dagli aggiornamenti del cluster alle decisioni di posizionamento delle repliche. Ogni evento esposto da Service Fabric è mappato a una delle seguenti entità nel cluster:
* Cluster
* Applicazione
* Servizio
* Partition
* Replica 
* Contenitore

Visualizzare un elenco completo degli eventi esposti dalla piattaforma - [Elenco degli eventi di Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Di seguito sono riportati alcuni esempi di scenari per cui è possibile visualizzare degli eventi nel cluster. 
* Eventi del ciclo di vita di un nodo: ogni volta che i nodi aumentano o diminuiscono di numero o di dimensioni o vengono attivati o disattivati, vengono esposti eventi che indicano che cosa è successo e consentire di rilevare eventuali problemi del computer stesso o della chiamata di un'API tramite SF per modificare lo stato di un nodo.
* Aggiornamento di un cluster: durante l'aggiornamento del cluster (versione SF o modifica della configurazione), viene visualizzato l'aggiornamento che si avvia, percorre i diversi domini di aggiornamento e si conclude (o ripristina lo stato precedente). 
* Aggiornamenti dell'applicazione: in modo analogo agli aggiornamenti del cluster, è presente un set completo di eventi che si svolgono durante l'esecuzione dell'aggiornamento. Questi eventi possono essere utili per comprendere quando è stato pianificato un aggiornamento, lo stato corrente di un aggiornamento e la sequenza generale degli eventi. Ciò è utile per vedere a posteriori quali aggiornamenti sono stato eseguiti correttamente o se è stato ripristinato lo stato precedente.
* Distribuzione/eliminazione di applicazioni o servizi: sono presenti eventi per ogni applicazione, servizio e contenitore creati o eliminati. Utili in caso di riduzione o aumento, ad esempio del numero di repliche
* Movimenti di partizione (riconfigurazione): ogni volta che una partizione con stato subisce una riconfigurazione (una modifica del set di repliche), viene registrato un evento. Ciò è utile per comprendere la frequenza di modifica o di failover del set di repliche di partizione o per tenere traccia del nodo in cui era in esecuzione la replica primaria in qualsiasi momento specifico.
* Eventi CHAOS: quando si usa il servizio [CHAOS](service-fabric-controlled-chaos.md) di Service Fabric, si vedranno eventi ogni volta che il servizio viene avviato o arrestato o quando provoca un errore nel sistema.
* Eventi di integrità: Service Fabric espone eventi di integrità ogni volta che viene creato un report di integrità di tipo Avviso o Errore, oppure quando un'entità viene reimpostata a uno stato di integrità OK o alla scadenza di un report di integrità. Questi eventi sono molto utili per tenere traccia delle statistiche cronologiche di integrità di un'entità. 

## <a name="how-to-access-events"></a>Come accedere agli eventi

Esistono diversi modi tramite cui è possibile accedere agli eventi di Service Fabric:
* Gli eventi vengono registrati tramite canali standard, ad esempio registri eventi ETW/Windows e possono essere visualizzati da qualsiasi strumento di monitoraggio che supporti tali registri, ad esempio i log di monitoraggio di Azure. Per impostazione predefinita, i cluster creati nel portale hanno attivato la diagnostica e l'agente di diagnostica di Microsoft Azure invia gli eventi all'archiviazione tabelle di Azure, ma è comunque necessario integrarla con la risorsa di log Analytics. Altre informazioni sulla configurazione dell' [agente di diagnostica di Azure](service-fabric-diagnostics-event-aggregation-wad.md) per modificare la configurazione di diagnostica del cluster per prelevare più log o contatori delle prestazioni e l' [integrazione dei log di monitoraggio di Azure](service-fabric-diagnostics-event-analysis-oms.md)
* API Rest del servizio EventStore che consentono di eseguire query del cluster, direttamente o tramite la libreria client di Service Fabric. Vedere [Eseguire query sulle API di EventStore per eventi del cluster](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul monitoraggio del cluster - [Monitoraggio del cluster e della piattaforma](service-fabric-diagnostics-event-generation-infra.md).
* Altre informazioni sul servizio EventStore - [Panoramica del servizio EventStore](service-fabric-diagnostics-eventstore.md)
