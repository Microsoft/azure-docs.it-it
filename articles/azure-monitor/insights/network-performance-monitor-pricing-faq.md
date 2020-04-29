---
title: Domande frequenti sui prezzi di Monitoraggio prestazioni rete di Azure | Microsoft Docs
description: Domande frequenti - Monitoraggio prestazioni rete di Azure
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: c5a80c7ff6d08ce601a6c2bbb0db0ef866d3b425
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77654393"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Modifiche ai prezzi per il Monitoraggio prestazioni rete di Azure

In risposta al feedback degli utenti, di recente è stata introdotta una [nuova esperienza di acquisto](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) per i vari servizi di monitoraggio disponibili in Azure. Questo articolo illustra le modifiche ai prezzi relative a [Monitoraggio prestazioni rete](https://docs.microsoft.com/azure/networking/network-monitoring-overview) di Azure in un semplice formato basato su domanda e risposta.

Monitoraggio prestazioni rete è costituito da tre componenti:
* [Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitoraggio endpoint di servizio](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [Monitoraggio di ExpressRoute](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

Le sezioni seguenti illustrano le modifiche dei prezzi per i componenti di Monitoraggio delle prestazioni di rete.

## <a name="performance-monitor"></a>Monitoraggio delle prestazioni

**Come era fatturato l'utilizzo di Performance Monitor nel vecchio modello?**

La fatturazione per Monitoraggio delle prestazioni di rete era basata sull'uso e il consumo di due componenti:
* **Nodi**: tutte le transazioni sintetiche hanno origine e terminano nei nodi. I nodi sono anche denominati agenti o Microsoft Monitoring Agent.
* **Dati**: i risultati dei vari test di rete vengono archiviati nell'area di lavoro log Analytics.

Nel modello precedente, la fattura era calcolata in base al numero di nodi e al volume dei dati generati. 

**Come viene addebitato l'utilizzo di performance monitor nel nuovo modello?**

La funzionalità Performance Monitor in Monitoraggio delle prestazioni di rete viene ora fatturata in base a una combinazione di: 

* Collegamenti a subnet monitorati
* Volume dati

**Che cos'è un collegamento a subnet?**

Performance Monitor monitora la connettività tra due o più posizioni nella rete. La connessione tra un gruppo di nodi o agenti in una subnet e un gruppo di nodi in un'altra subnet è detto collegamento a subnet.

**Ho due subnet (A e B) e ho diversi agenti in ogni subnet. Performance Monitor monitora la connettività da tutti gli agenti nella subnet a a tutti gli agenti nella subnet B. Verranno addebitati i costi in base al numero di connessioni tra subnet?**

No. Ai fini della fatturazione, tutte le connessioni dalla subnet A alla subnet B vengono raggruppate in un unico collegamento a subnet e la fatturazione avviene per una singola connessione. Performance Monitor continua a monitorare la connettività tra i vari agenti su ciascuna subnet.

**Quali sono i costi per il monitoraggio di un collegamento a subnet?**

Fare riferimento alla sezione [Ping Mesh](https://azure.microsoft.com/pricing/details/network-watcher/) per i costi di monitoraggio di un singolo collegamento a subnet per l'intero mese.

**Quali sono i costi per i dati generati da Performance Monitor?**

L'addebito per l'inserimento (caricamento dei dati nell'area di lavoro Log Analytics in monitoraggio, elaborazione e indicizzazione di Azure) è disponibile nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/log-analytics/) per log Analytics, nella sezione inserimento dati. Il costo per la conservazione dati (opzione dei dati conservati presso il cliente, oltre il primo mese) è anch'esso disponibile nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/log-analytics/), nella sezione Conservazione dati.


## <a name="expressroute-monitor"></a>Monitoraggio di ExpressRoute

**Quali sono i costi per l'utilizzo di Monitoraggio di ExpressRoute?**

I costi per Monitoraggio di ExpressRoute vengono addebitati in base al volume dei dati generati durante il monitoraggio. Per altre informazioni, vedere "Quali sono i costi per i dati generati da Performance Monitor?"

**Si usa ExpressRoute monitor per monitorare più circuiti ExpressRoute. Vengono addebitati i costi in base al numero di circuiti monitorati?**

Gli addebiti non sono basati sul numero di circuiti, né sul tipo di peering (ad esempio, peering privato o peering Microsoft), bensì sul volume dei dati, come illustrato in precedenza.

**Qual è il volume dei dati generati per il monitoraggio di un singolo circuito da parte di ExpressRoute?**

Il volume dei dati generati ogni mese per il monitoraggio di una connessione di peering privata da parte di ExpressRoute è il seguente:

|Percentile      |Dati/mese (MB)|
| :---:          |           ---:|
|50<sup>°</sup> |            192|
|60<sup>°</sup> |            256|
|70<sup>°</sup> |            360|
|80<sup>°</sup> |            498|
|90<sup>°</sup> |            870|
|95<sup>°</sup> |           1560|


In base a questa tabella, i clienti al 50° percentile pagano per 192 MB di dati. A € 1,94/GB per il primo mese, il costo sostenuto per il monitoraggio di un circuito è di € 0,36 (= 192 * 0,36 / 1024).

**Quali sono i motivi alla base delle variazioni nel volume dei dati?**

Il volume dei dati di monitoraggio generati dipende da diversi fattori, tra cui:
* Numero di agenti. L'accuratezza dell'isolamento degli errori aumenta con l'aumento del numero di agenti.
* Numero di hop nella rete.
* Numero di percorsi tra l'origine e la destinazione.

I clienti nei percentili più alti (nella tabella precedente) in genere monitorano i circuiti da vari punti privilegiati sulla rete locale. Altri agenti vengono inseriti anche più all'interno della rete, a una distanza maggiore dal router perimetrale del provider di servizi. Gli agenti sono spesso inseriti in più siti utente, rami e rack all'interno dei data center.

## <a name="service-endpoint-monitor"></a>Monitoraggio endpoint di servizio

**Quali sono i costi per l'utilizzo di Monitoraggio endpoint di servizio?**

I costi per l'utilizzo di Monitoraggio endpoint di servizio vengono calcolati in base a:
* Numero di connessioni
* Volume dei dati

**Che cos'è una connessione?**

Una connessione è un test della raggiungibilità di un endpoint (URL o servizio di rete) da parte di un singolo agente per l'intero mese. Ad esempio, il monitoraggio di una connessione a bing.com da parte di tre agenti rappresenta tre connessioni.

**Quali sono i costi di Monitoraggio endpoint di servizio?**

Vedere la sezione [monitoraggio della connessione](https://azure.microsoft.com/pricing/details/network-watcher/) per il costo del monitoraggio di un endpoint per l'intero mese. Gli addebiti per i dati sono disponibili nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/log-analytics/) di Log Analytics, nella sezione Inserimento dati.

## <a name="references"></a>Riferimenti

[Domande frequenti sui prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/): la sezione delle domande frequenti contiene informazioni sul livello gratuito, il prezzo per nodo e altri dati relativi ai prezzi.

