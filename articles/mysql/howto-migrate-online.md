---
title: Migrazione con tempi di inattività minimi-database di Azure per MySQL
description: Questo articolo descrive come eseguire una migrazione con tempo di inattività minimo di un database MySQL in Database di Azure per MySQL tramite Servizio Migrazione del database di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 75b548b53de4c0f4ea39959c3d70e9ddb361d64c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540332"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migrazione con tempo di inattività minimo a Database di Azure per MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

È possibile eseguire migrazioni di MySQL in Database di Azure per MySQL con tempo di inattività minimo usando la **funzionalità di sincronizzazione continua** appena introdotta per [Servizio Migrazione del database di Azure](https://aka.ms/get-dms). Questa funzionalità limita il tempo di inattività cui è soggetta l'applicazione.

È anche possibile fare riferimento alla [Guida alla migrazione del database](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) per informazioni dettagliate e casi d'uso sulla migrazione di database al database di Azure per MySQL. Questa guida fornisce indicazioni che comporteranno la corretta pianificazione e l'esecuzione di una migrazione di MySQL in Azure.

## <a name="overview"></a>Panoramica
Servizio Migrazione del database di Azure esegue un caricamento iniziale di Database di Azure per MySQL locale e quindi sincronizza continuamente tutte le nuove transazioni in Azure mentre l'applicazione resta in esecuzione. Dopo che i dati sono allineati sul lato Azure di destinazione, è necessario arrestare l'applicazione per un breve momento (tempo di inattività minimo), attendere l'allineamento dell'ultimo batch di dati (dal momento in cui è stata arrestata l'applicazione fino a quando l'applicazione non è effettivamente non disponibile per accettare nuovo traffico) nella destinazione e quindi aggiornare la stringa di connessione in modo da puntare ad Azure. Al termine, l'applicazione sarà live in Azure.

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Sincronizzazione continua con Servizio Migrazione del database di Azure":::

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulla migrazione dei database in Database di Azure per MySQL, vedere [Database Migration Guide](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) (Guida alla migrazione di database).
- Visualizzare il video [Easily migrate MySQL/PostgreSQL apps to Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) (Eseguire la migrazione di app MySQL/PostgreSQL nel servizio gestito di Azure in tutta semplicità), che contiene una demo che mostra come migrare app MySQL in Database di Azure per MySQL.
- Vedere l'esercitazione [Eseguire la migrazione di MySQL in Database di Azure per MySQL online mediante il Servizio Migrazione del database](../dms/tutorial-mysql-azure-mysql-online.md).