---
title: includere il file
description: File di inclusione
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75901975"
---
Le tabelle seguenti forniscono le quote e i limiti specifici di [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Per informazioni sui prezzi di hub eventi, vedere [prezzi di hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

I limiti seguenti sono comuni nei livelli Basic, standard e dedicati. 

| Limite | Scope | Note | valore |
| --- | --- | --- | --- |
| Numero di spazi dei nomi di Hub eventi per sottoscrizione |Subscription |- |100 |
| Numero di hub eventi per ogni spazio dei nomi |Spazio dei nomi |Le successive richieste di creazione di un nuovo hub eventi vengono rifiutate. |10 |
| Numero di partizioni per hub eventi |Entità |- |32 |
| Dimensione massima del nome di un hub eventi |Entità |- |50 caratteri |
| Numero di ricevitori non epoch per gruppo consumer |Entità |- |5 |
| Numero massimo di unità di velocità effettiva |Spazio dei nomi |Il superamento del limite di unità elaborate causa la limitazione della velocità dei dati e la generazione di un' [eccezione server occupato](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Per richiedere un numero maggiore di unità di velocità effettiva per un livello standard, archiviare una [richiesta di supporto](/azure/azure-portal/supportability/how-to-create-azure-support-request). Le [unità elaborate aggiuntive](../articles/event-hubs/event-hubs-auto-inflate.md) sono disponibili in blocchi da 20 in base a un acquisto con impegno. |20 |
| Numero di regole di autorizzazione per spazio dei nomi |Spazio dei nomi|Le successive richieste di creazione della regola di autorizzazione vengono rifiutate.|12 |
| Numero di chiamate al Metodo GetRuntimeInformation | Entità | - | 50 al secondo | 
| Numero di regole di rete virtuale (VNet) e di configurazione IP | Entità | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Hub eventi Basic e standard-quote e limiti
| Limite | Scope | Note | Basic | Standard |
| --- | --- | --- | -- | --- |
| Dimensione massima degli eventi di Hub eventi|Entità | &nbsp; | 256 kB | 1 MB |
| Numero di gruppi consumer per hub eventi |Entità | &nbsp; |1 |20 |
| Numero di connessioni AMQP per spazio dei nomi |Spazio dei nomi |Le successive richieste di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. |100 |5\.000|
| Periodo di conservazione massimo dei dati dell'evento |Entità | &nbsp; |1 giorno |1-7 giorni |
|Spazio dei nomi abilitato Apache Kafka|Spazio dei nomi |Flussi di spazio dei nomi di hub eventi con protocollo Kafka |No | Sì |
|Acquisizione |Entità | Se abilitata, micro-batch nello stesso flusso |No |Sì |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Hub eventi Dedicato-quote e limiti
L'offerta Hub eventi Dedicato viene fatturata a un prezzo mensile fisso, con un minimo di 4 ore di utilizzo. Il livello dedicato offre tutte le funzionalità del piano standard, ma con capacità e limiti di scalabilità aziendale per i clienti con carichi di lavoro complessi. 

| Funzionalità | Limiti |
| --- | ---|
| Larghezza di banda |  20 CUs |
| Spazi dei nomi | 50 per CU |
| Hub eventi |  1000 per spazio dei nomi |
| Eventi in ingresso | Incluso |
| Dimensioni del messaggio | 1 MB |
| Partizioni | 2000 per CU |
| Gruppi di consumer | Nessun limite per CU, 1000 per hub eventi |
| Connessioni negoziate | 100 K incluse |
| Conservazione dei messaggi | 90 giorni, 10 TB inclusi per CU |
| Acquisizione | Incluso |
