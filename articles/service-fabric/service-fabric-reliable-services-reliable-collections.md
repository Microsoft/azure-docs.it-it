---
title: Introduzione alle raccolte affidabiliIntroduction to Reliable Collections
description: I servizi con stato di Service Fabric forniscono raccolte Reliable Collections che consentono di sviluppare applicazioni cloud a disponibilità elevata, scalabili e a bassa latenza.
ms.topic: conceptual
ms.date: 1/3/2019
ms.openlocfilehash: 48fa682f4c017f66911729e1f581f3aa91cdc28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609724"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduzione alle Reliable Collections nei servizi con stato di Service Fabric

Reliable Collections consente di sviluppare applicazioni cloud a disponibilità elevata, scalabili e a bassa latenza nello stesso modo in cui si sviluppano applicazioni per singoli computer. Le classi nello spazio dei nomi **Microsoft.ServiceFabric.Data.Collections** forniscono un set di raccolte che assicurano automaticamente la disponibilità elevata dello stato. Gli sviluppatori devono solo programmare le API Reliable Collections e consentire a queste raccolte di gestire lo stato replicato e locale.

La differenza principale tra le raccolte Reliable Collections e altre tecnologie a disponibilità elevata, ad esempio Redis e i servizi tabelle e code di Azure, consiste nel fatto che lo stato viene mantenuto in locale nell'istanza del servizio e ne viene assicurata al tempo stesso la disponibilità elevata. Ciò significa che:

* Tutte le operazioni di lettura sono locali, assicurando quindi bassa latenza e velocità effettiva elevata.
* Tutte le operazioni di scrittura eseguono il numero minimo di operazioni di IO di rete, assicurando quindi bassa latenza e velocità effettiva elevata.

![Immagine dell'evoluzione delle raccolte.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Le raccolte Reliable Collections possono essere considerate l'evoluzione naturale delle classi **System.Collections** , ovvero un nuovo set di raccolte progettate per le applicazioni per il cloud e più computer che non aumentano le complessità per gli sviluppatori. Come tali, le raccolte Reliable Collections sono:

* Replicate: le modifiche apportate allo stato vengono replicate per assicurare disponibilità elevata.
* Persistenti: i dati vengono resi persistenti sul disco per assicurarne la durabilità in caso di guasti su larga scala, ad esempio l'interruzione dell'alimentazione in un data center.
* Poiché le scritture sono persistenti e replicate, non è possibile creare una raccolta ReliableDictionary o ReliableQueue volatile o un'altra raccolta affidabile che salva i dati in modo permanente solo in memoria.
* Asincrone: le API sono asincrone per assicurare che i thread non vengano bloccati durante le operazioni di IO.
* Transazionali: le API utilizzano l'astrazione delle transazioni per consentire all'utente di gestire facilmente più raccolte Reliable Collections all'interno di un servizio.

Le raccolte Reliable Collections offrono garanzie predefinite di coerenza assoluta, per facilitare la definizione della logica relativa allo stato delle applicazioni.
La coerenza assoluta è ottenuta assicurando che i commit delle transazioni siano completati solo dopo che l'intera transazione è stata registrata su un quorum di repliche di maggioranza, inclusa quella primaria.
Per ottenere una coerenza più debole, le applicazioni possono rinviare un acknowledgement al client/richiedente prima della restituzione del commit asincrono.

Le API Reliable Collections sono un'evoluzione delle API delle raccolte disponibili nello spazio dei nomi **System.Collections.Concurrent** :

* Asincrone: restituiscono un'attività dal momento che, a differenza delle raccolte Concurrent Collections, le operazioni vengono replicate e rese persistenti.
* Nessun parametro `ConditionalValue<T>` out: `bool` consente di restituire un e un valore anziché i parametri out. `ConditionalValue<T>` è come `Nullable<T>` ma non richiede una T per essere una struttura.
* Transazioni: usano un oggetto transazione per consentire all'utente di raggruppare azioni su più raccolte Reliable Collections in una transazione.

Attualmente **Microsoft.ServiceFabric.Data.Collections** include tre raccolte:

* [ReliableDictionary](https://msdn.microsoft.com/library/azure/dn971511.aspx): rappresenta una raccolta replicata, transazionale e asincrona di coppie chiave/valore. Simile a **ConcurrentDictionary**, sia la chiave che il valore possono essere di qualsiasi tipo.
* [ReliableQueue](https://msdn.microsoft.com/library/azure/dn971527.aspx): rappresenta una coda FIFO (First-In First-Out) replicata, transazionale e asincrona. Simile a **ConcurrentQueue**, il valore può essere di qualsiasi tipo.
* [ReliableConcurrentQueue](service-fabric-reliable-services-reliable-concurrent-queue.md): rappresenta una coda di ordinamento ottimale replicata, transazionale e asincrona per la velocità effettiva elevata. Simile a **ConcurrentQueue**, il valore può essere di qualsiasi tipo.

## <a name="next-steps"></a>Passaggi successivi

* [Linee guida per la raccolta affidabile & raccomandazioni](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Lavorare con le raccolte Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transazioni e blocchi](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gestione dei dati
  * [Backup e ripristino](service-fabric-reliable-services-backup-restore.md)
  * [Notifiche](service-fabric-reliable-services-notifications.md)
  * [Serializzazione di raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serializzazione e aggiornamento](service-fabric-application-upgrade-data-serialization.md)
  * [Reliable State Manager configuration (Configurazione di Reliable State Manager)](service-fabric-reliable-services-configuration.md)
* Altro
  * [Guida introduttiva di Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Guida di riferimento per gli sviluppatori per Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
