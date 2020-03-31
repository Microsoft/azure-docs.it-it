---
title: Migrazione del tempo minimo di inattività - Database di Azure per MySQL
description: Questo articolo descrive come eseguire una migrazione con tempo di inattività minimo di un database MySQL in Database di Azure per MySQL tramite Servizio Migrazione del database di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: df818fa4106aec341607d8142b2a672699b8e9d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063350"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migrazione con tempo di inattività minimo a Database di Azure per MySQL
È possibile eseguire migrazioni di MySQL in Database di Azure per MySQL con tempo di inattività minimo usando la **funzionalità di sincronizzazione continua** appena introdotta per [Servizio Migrazione del database di Azure](https://aka.ms/get-dms). Questa funzionalità limita il tempo di inattività cui è soggetta l'applicazione.

## <a name="overview"></a>Panoramica
Servizio Migrazione del database di Azure esegue un caricamento iniziale di Database di Azure per MySQL locale e quindi sincronizza continuamente tutte le nuove transazioni in Azure mentre l'applicazione resta in esecuzione. Dopo che i dati sono allineati sul lato Azure di destinazione, è necessario arrestare l'applicazione per un breve momento (tempo di inattività minimo), attendere l'allineamento dell'ultimo batch di dati (dal momento in cui è stata arrestata l'applicazione fino a quando l'applicazione non è effettivamente non disponibile per accettare nuovo traffico) nella destinazione e quindi aggiornare la stringa di connessione in modo da puntare ad Azure. Al termine, l'applicazione sarà live in Azure.

![Sincronizzazione continua con Servizio Migrazione del database di Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare il video [Easily migrate MySQL/PostgreSQL apps to Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) (Eseguire la migrazione di app MySQL/PostgreSQL nel servizio gestito di Azure in tutta semplicità), che contiene una demo che mostra come migrare app MySQL in Database di Azure per MySQL.
- Vedere l'esercitazione [Eseguire la migrazione di MySQL in Database di Azure per MySQL online mediante il Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
