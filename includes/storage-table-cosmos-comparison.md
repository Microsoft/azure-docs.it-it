---
title: includere file
description: File di inclusione
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 01/08/2021
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: a7e34f077ce1b2541168df40f2806fdb24a63a79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050757"
---
Se attualmente si usa l'archivio tabelle di Azure, passando all'API Tabelle di Azure Cosmos DB è possibile ottenere i vantaggi seguenti:

|Funzionalità | Archiviazione tabelle di Azure | API Tabelle di Azure Cosmos DB |
| --- | --- | --- |
| Latenza | Veloce, senza limiti superiori per la latenza. | Latenza pari a singole unità di millisecondi per letture e scritture, supportata da latenza minore di 10 ms per le letture e di 15 ms per le scritture al 99° percentile, in base a qualsiasi livello di scalabilità e ovunque nel mondo. |
| Velocità effettiva | Modello a velocità effettiva variabile. Le tabelle hanno un limite di scalabilità di 20.000 operazioni al secondo. | Altamente scalabile con [velocità effettiva riservata dedicata per tabella](../articles/cosmos-db/request-units.md), supportata da contratti di servizio. Non esiste un limite superiore di velocità effettiva per gli account, che supportano oltre 10 milioni di operazioni al secondo per tabella (in modalità provisioning velocità effettiva). |
| Distribuzione globale | Singola area con un'area di lettura secondaria leggibile facoltativa per la disponibilità elevata. Non è possibile avviare il failover. | [Distribuzione globale chiavi in mano](../articles/cosmos-db/distribute-data-globally.md) da una a 30+ aree. Supporto per [failover automatici e manuali](../articles/cosmos-db/high-availability.md) in qualsiasi momento, ovunque nel mondo. |
| Indicizzazione | Solo indice primario in PartitionKey e RowKey. Nessun indice secondario. | Indicizzazione automatica e completa su tutte le proprietà, nessuna gestione degli indici. |
| Query | L'esecuzione di query usa l'indice per la chiave primaria ed esegue l'analisi negli altri casi. | Le query possono trarre vantaggio dall'indicizzazione automatica sulle proprietà, per query con durata ridotta. |
| Consistenza | Assoluta entro l'area primaria. Eventuale entro l'area secondaria. | [Cinque livelli di coerenza ben definiti](../articles/cosmos-db/consistency-levels.md) per bilanciare disponibilità, latenza, velocità effettiva e coerenza in base alle esigenze dell'applicazione. |
| Prezzi | In base al consumo. | Disponibile sia in modalità [basata sul consumo](../articles/cosmos-db/serverless.md) che con [capacità di provisioning](../articles/cosmos-db/set-throughput.md). |
| Contratti di servizio | Disponibilità del 99,99%. | Contratto di servizio con disponibilità del 99,99% per tutti gli account in una singola area e tutti gli account in più aree con coerenza media e [contratti di servizio completi leader del settore](https://azure.microsoft.com/support/legal/sla/cosmos-db/) che regolano la disponibilità generale, con disponibilità in lettura del 99,999% per tutti gli account di database in più aree. |
