---
title: Che cos'è il Servizio Migrazione del database di Azure?
description: Panoramica del Servizio Migrazione del database di Azure, che offre migrazioni senza interruzioni da diverse origini di database alle piattaforme dati di Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: overview
ms.date: 02/20/2020
ms.openlocfilehash: 328c29afee3752ecb11b83f22d67f20aa3a2c93e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94963012"
---
# <a name="what-is-azure-database-migration-service"></a>Che cos'è il Servizio Migrazione del database di Azure?

Il Servizio Migrazione del database di Azure è un servizio completamente gestito progettato per consentire migrazioni senza interruzioni da più origini di database alle piattaforme dati di Azure con tempi di inattività minimi (migrazioni online).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Eseguire la migrazione dei database in Azure con strumenti familiari

Il Servizio Migrazione del database di Azure integra alcune funzionalità degli strumenti e dei servizi esistenti. Offre ai clienti una soluzione a disponibilità elevata completa. Il servizio usa il [Data Migration Assistant](/sql/dma/dma-overview) per generare report di valutazione che forniscono indicazioni che consentono di eseguire le modifiche necessarie prima di una migrazione. È compito dell'utente eseguire le correzioni necessarie. Quando si è pronti a iniziare il processo di migrazione, il Servizio Migrazione del database di Azure esegue tutti i passaggi necessari. È possibile avviare i progetti di migrazione con tranquillità sapendo che il processo si avvale delle procedure consigliate stabilite da Microsoft. 

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium.

## <a name="regional-availability"></a>Disponibilità a livello di area

Per informazioni aggiornate sulla disponibilità a livello di area del Servizio Migrazione del database di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).

## <a name="pricing"></a>Prezzi

Per informazioni aggiornate sui prezzi del servizio Migrazione del database di Azure, vedere [prezzi del servizio Migrazione del database di Azure](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Passaggi successivi

* [Stato degli scenari di migrazione supportati dal Servizio Migrazione del database di Azure](resource-scenario-status.md).
* [Creare un'istanza del Servizio Migrazione del database di Azure usando il portale di Azure](quickstart-create-data-migration-service-portal.md).
* [Eseguire la migrazione di SQL Server nel database SQL di Azure](tutorial-sql-server-to-azure-sql.md).
* [Panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure](pre-reqs.md).
* [Domande frequenti sull'uso del Servizio Migrazione del database di Azure](faq.md).
* [Servizi e strumenti disponibili per scenari di migrazione dei dati](dms-tools-matrix.md).