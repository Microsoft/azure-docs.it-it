---
title: Panoramica di Hub eventi dedicato - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica dell'hub eventi di Azure dedicato, che offre distribuzioni a tenant singolo di hub eventi.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 721acf354c7d14c1362b4f760982af37d59115f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715616"
---
# <a name="overview-of-event-hubs-dedicated"></a>Panoramica di Hub eventi dedicato

I *cluster di hub eventi* offrono distribuzioni a tenant singolo per i clienti con le esigenze di streaming più complesse. Questa offerta a tenant singolo dispone di un contratto di contratto di 99,99% garantito ed è disponibile solo nel piano tariffario dedicato. Un cluster Hub eventi può gestire l'ingresso di milioni di eventi al secondo con una capacità garantita e una latenza dell'ordine di frazioni di secondo. Gli spazi dei nomi e gli hub eventi creati nel cluster dedicato includono tutte le funzionalità dell'offerta standard e altro ancora, ma senza limiti di ingresso. Include anche la nota funzionalità di [acquisizione di hub eventi](event-hubs-capture-overview.md) senza costi aggiuntivi. Questa funzionalità consente di raggruppare e registrare automaticamente i flussi di dati in archiviazione di Azure o in Azure Data Lake. 

Il provisioning dei cluster viene effettuato e fatturato da **unità di capacità**, una quantità preallocata di risorse di CPU e memoria. È possibile acquistare 1, 2, 4, 8, 12, 16 o 20 unità di capacità per ogni cluster. Quanto è possibile inserire e trasmettere in streaming per CU dipende da diversi fattori, ad esempio quelli riportati di seguito: 

- Numero di Producer e consumer
- Forma del payload
- Velocità in uscita

> [!NOTE]
> Per impostazione predefinita, tutti i cluster di hub eventi sono abilitati per Kafka e supportano gli endpoint Kafka che possono essere usati dalle applicazioni esistenti basate su Kafka. L'abilitazione di Kafka nel cluster non influisce sui casi d'uso non Kafka; non è possibile o non è necessario disabilitare Kafka in un cluster.

## <a name="why-dedicated"></a>Perché dedicate?

Hub eventi dedicati offre tre vantaggi interessanti per i clienti che necessitano di capacità a livello aziendale:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Single-tenant garantisce la capacità per ottenere prestazioni migliori

Un cluster dedicato garantisce la capacità su vasta scala. Consente di inserire fino a gigabyte di dati di streaming con archiviazione completamente durevole e latenza di sottosecondo per supportare eventuali picchi di traffico. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Accesso inclusivo ed esclusivo alle funzionalità 
L'offerta dedicata include funzionalità quali l'acquisizione senza costi aggiuntivi e l'accesso esclusivo alle funzionalità future come Bring Your Own Key (BYOK). Il servizio gestisce anche il bilanciamento del carico, gli aggiornamenti del sistema operativo, le patch di sicurezza e il partizionamento. Quindi, è possibile dedicare meno tempo alla manutenzione dell'infrastruttura e più tempo alla creazione di funzionalità lato client.  

#### <a name="cost-savings"></a>Risparmio sui costi
In volumi in ingresso elevati (>100 unità elaborate), un cluster costi significativamente inferiore all'ora rispetto all'acquisto di una quantità comparabile di unità di velocità effettiva nell'offerta standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Quote e limiti Hub eventi Dedicato

L'offerta Hub eventi Dedicato viene fatturata a un prezzo mensile fisso, con un minimo di 4 ore di utilizzo. Il livello dedicato offre tutte le funzionalità del piano standard, ma con capacità di livello aziendale e limiti per i clienti con carichi di lavoro complessi. 

| Funzionalità | Standard | Dedicato |
| --- |:---|:---|
| Larghezza di banda | 20 unità elaborate (fino a 40 unità elaborate) | 20 unità di capacità |
| Spazi dei nomi |  1 | 50 per unità di capacità |
| Hub eventi |  10 per spazio dei nomi | 1\.000 per spazio dei nomi |
| Eventi in ingresso | Pagamento per ogni milione di eventi | Incluso |
| Dimensioni del messaggio | 1 milione di byte | 1 milione di byte |
| Partizioni | 32 per hub eventi | 1024 per hub eventi<br/>2\.000 per unità di capacità |
| Gruppi di consumer | 20 per hub eventi | Nessun limite per unità di capacità, 1.000 per hub eventi |
| Connessioni negoziate | 1\.000 incluse, massimo 5.000 | 100.000 incluse massimo |
| [Conservazione degli eventi](event-hubs-features.md#event-retention) | 7 giorni, 84 TB inclusi per unità elaborata | 90 giorni, 10 TB inclusi per unità di capacità |
| Acquisizione | Pagamento per ogni ora | Incluso |

Per altre quote e limiti, vedere [quote e limiti di hub eventi](event-hubs-quotas.md)

## <a name="how-to-onboard"></a>Modalità di esecuzione dell'onboarding

L'esperienza self-service per [creare un cluster di hub eventi](event-hubs-dedicated-cluster-create-portal.md) tramite il [portale di Azure](https://aka.ms/eventhubsclusterquickstart) è ora disponibile in anteprima. Per eventuali domande o per richiedere assistenza per l'onboarding in Hub eventi Dedicato, contattare il [team di hub eventi](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Domande frequenti

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui Hub eventi Dedicato, contattare il rappresentante di vendita Microsoft o supporto tecnico Microsoft. È anche possibile creare un cluster o altre informazioni sui piani tariffari di hub eventi visitando i collegamenti seguenti:

- [Creare un cluster di hub eventi tramite il portale di Azure](https://aka.ms/eventhubsclusterquickstart) 
- [Tariffe di Hub eventi dedicato](https://azure.microsoft.com/pricing/details/event-hubs/). Per informazioni aggiuntive sulla capacità di Hub eventi dedicato, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft.
- Le [Domande frequenti si Hub eventi](event-hubs-faq.md) contengono informazioni sui prezzi e risposte ad alcune domande frequenti sugli Hub eventi.
