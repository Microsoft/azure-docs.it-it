---
title: Migrazione del tempo di inattività minimo al database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come eseguire una migrazione con tempo di inattività minimo di un database PostgreSQL in database di Azure per PostgreSQL-server singolo usando il servizio migrazione del database di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "65067520"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migrazione del tempo di inattività minimo al database di Azure per PostgreSQL-server singolo
È possibile eseguire migrazioni di PostgreSQL in Database di Azure per PostgreSQL con tempo di inattività minimo usando la **funzionalità di sincronizzazione continua** appena introdotta per [Servizio Migrazione del database di Azure](https://aka.ms/get-dms). Questa funzionalità limita il tempo di inattività cui è soggetta l'applicazione.

## <a name="overview"></a>Panoramica
Servizio Migrazione del database di Azure esegue un caricamento iniziale di Database di Azure per PostgreSQL locale e quindi sincronizza continuamente tutte le nuove transazioni in Azure mentre l'applicazione resta in esecuzione. Dopo che i dati sono allineati sul lato Azure di destinazione, è necessario arrestare l'applicazione per un breve momento (tempo di inattività minimo), attendere l'allineamento dell'ultimo batch di dati (dal momento in cui è stata arrestata l'applicazione fino a quando l'applicazione non è effettivamente non disponibile per accettare nuovo traffico) nella destinazione e quindi aggiornare la stringa di connessione in modo da puntare ad Azure. Al termine, l'applicazione sarà live in Azure.

![Sincronizzazione continua con Servizio Migrazione del database di Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare il video [App Modernization with Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102) (Modernizzazione delle app con Microsoft Azure), che contiene una demo che mostra come eseguire la migrazione di app PostgreSQL in Database di Azure per PostgreSQL.
- Vedere l'esercitazione [Eseguire la migrazione di PostgreSQL in Database di Azure per PostgreSQL online mediante il Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
