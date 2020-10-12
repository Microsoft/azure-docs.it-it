---
title: Gestire lo stato nei servizi Service Fabric di Azure
description: Informazioni sullo stato in Service Fabric di Azure, incluse informazioni su come definire e gestire lo stato del servizio in servizi Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75614571"
---
# <a name="service-state"></a>Stato del servizio
**Stato del servizio** si riferisce ai dati in memoria o su disco di cui un servizio necessita per funzionare. Si tratta ad esempio delle strutture di dati e delle variabili membro che vengono lette e scritte dal servizio per il funzionamento. A seconda di come è progettato il servizio, può anche includere file o altre risorse archiviati su disco. I file ad esempio che un database userebbe per archiviare log delle transazioni e dati.

Come un servizio di esempio, si consideri una calcolatrice. Questo servizio di calcolatrice di base accetta due numeri per restituirne la somma. L'esecuzione di questo calcolo non implica alcuna variabile membro o altre informazioni.

Si consideri ora la stessa calcolatrice, ma con un metodo aggiuntivo di memorizzazione e restituzione dell'ultima somma calcolata. Il servizio è ora con stato. Con stato significa che contiene uno stato in cui scrive quando calcola una nuova somma e da cui legge quando viene richiesto di restituire l'ultima somma calcolata.

In Service Fabric di Azure il primo servizio è denominato servizio senza stato. Il secondo invece è denominato servizio con stato.

## <a name="storing-service-state"></a>Archiviazione dello stato del servizio
Lo stato può essere archiviato all'esterno oppure condividere la posizione con il codice che modifica lo stato. L'esternalizzazione dello stato viene in genere eseguita usando un database esterno o un altro archivio dati eseguito in altri computer sulla rete o all'esterno del processo nello stesso computer. In questo esempio di calcolatrice, l'archivio dati potrebbe essere un database SQL o un'istanza dell'archivio tabelle di Azure. Ogni richiesta per calcolare la somma esegue un aggiornamento di questi dati e richiede al servizio di restituire il risultato nel valore corrente recuperato dall'archivio. 

Lo stato può anche condividere la posizione con il codice che modifica lo stato. I servizi con stato in Service Fabric vengono in genere creati usando questo modello. Service Fabric fornisce l'infrastruttura per assicurare che questo stato sia con disponibilità elevata, coerente e durevole e che i servizi creati in questo modo possano essere facilmente scalabili.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

* [Disponibilità dei servizi di Service Fabric](service-fabric-availability-services.md)
* [Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)
* [Partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)
* [Reliable Services di Service Fabric](service-fabric-reliable-services-introduction.md)
