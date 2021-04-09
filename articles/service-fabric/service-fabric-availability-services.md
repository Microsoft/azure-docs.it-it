---
title: Disponibilità dei servizi di Service Fabric
description: Descrive il rilevamento degli errori, il failover e il ripristino di un servizio in un'applicazione Service Fabric di Azure.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018903"
---
# <a name="availability-of-service-fabric-services"></a>Disponibilità dei servizi di Service Fabric
Questo articolo fornisce una panoramica del modo in cui Azure Service Fabric gestisce la disponibilità di un servizio.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilità dei servizi di Service Fabric senza stato
I servizi di Service Fabric possono essere con o senza stato. Un servizio senza stato è un servizio dell'applicazione che non ha uno [stato locale](service-fabric-concepts-state.md) che richiede disponibilità o affidabilità elevata.

La creazione di un servizio senza stato richiede la definizione di un `InstanceCount`. per il numero di istanze di logica dell'applicazione del servizio senza stato da eseguire nel cluster. L'aumento del numero di istanze è la soluzione consigliata per implementare la scalabilità orizzontale di un servizio senza stato.

Quando un'istanza di un servizio denominato senza stato ha esito negativo, viene creata una nuova istanza in un nodo idoneo del cluster. Un'istanza di un servizio senza stato potrebbe ad esempio avere esito negativo in Node1 e venire ricreata in Node5.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilità dei servizi di Service Fabric con stato
Un servizio con stato ha uno stato associato. In Service Fabric un servizio con stato è modellato come set di repliche. Ogni replica è un'istanza in esecuzione del codice del servizio. La replica ha anche una copia dello stato per il servizio. Le operazioni di lettura e scrittura vengono eseguite in una replica, detta *primaria*. I cambiamenti di stato dovuti a operazioni di scrittura vengono *replicati* nelle altre repliche nel set di repliche, dette repliche *secondarie attive*, e applicati. 

Può esistere solo una replica primaria, ma possono esistere più repliche secondarie attive. Il numero di repliche secondarie attive è configurabile. Più alto è il numero di repliche, maggiore è il numero di errori hardware e software simultanei che è possibile tollerare.

Se la replica primaria diventa inattiva, Service Fabric crea come nuova replica primaria una delle repliche secondarie attive. La replica secondaria attiva ha già la versione aggiornata dello stato, mediante il *processo di replica*, e può continuare a elaborare altre operazioni di lettura e scrittura. Questo processo è noto come *riconfigurazione* e viene descritto più dettagliatamente in [Riconfigurazione](service-fabric-concepts-reconfiguration.md).

Il concetto di replica primaria o replica secondaria attiva è noto come *ruolo di replica*. Le repliche sono descritte in maggiore dettaglio nell'articolo [Repliche e istanze](service-fabric-concepts-replica-lifecycle.md). 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

- [Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)
- [Partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)
- [Definizione e gestione dello stato](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

