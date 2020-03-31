---
title: Limiti - Database di Azure per PostgreSQL - Server singolo
description: Questo articolo descrive i limiti nel database di Azure per PostgreSQL - Server singolo, ad esempio il numero di opzioni del motore di archiviazione e connessione.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 047e722a0e0ade60d1eb93a48e37333fffafd674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76836457"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limiti nel database di Azure per PostgreSQL - Server singoloLimits in Azure Database for PostgreSQL - Single Server
Nelle sezioni seguenti vengono descritti i limiti delle capacità e funzionali nel servizio del database. Per informazioni sui livelli di risorse (calcolo, memoria, archiviazione), vedere l'articolo [dei piani tariffari.](concepts-pricing-tiers.md)


## <a name="maximum-connections"></a>Numero massimo di connessioni
Il numero massimo di connessioni per piano tariffario e vCore è illustrato di seguito. Il sistema Azure richiede cinque connessioni per il monitoraggio del server di Database di Azure per PostgreSQL. 

|**Livello di determinazione dei prezzi**| **vCore**| **Numero massimo di connessioni** | **Numero massimo di connessioni utente** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|Utilizzo generico| 2| 150| 145|
|Utilizzo generico| 4| 250| 245|
|Utilizzo generico| 8| 480| 475|
|Utilizzo generico| 16| 950| 945|
|Utilizzo generico| 32| 1500| 1495|
|Utilizzo generico| 64| 1900| 1895|
|Con ottimizzazione per la memoria| 2| 300| 295|
|Con ottimizzazione per la memoria| 4| 500| 495|
|Con ottimizzazione per la memoria| 8| 960| 955|
|Con ottimizzazione per la memoria| 16| 1900| 1895|
|Con ottimizzazione per la memoria| 32| 1987| 1982|

Quando le connessioni superano il limite, è possibile che venga visualizzato l'errore seguente:
> FATAL: sorry, too many clients already (ERRORE IRREVERSIBILE: ci sono già troppi client)

> [!IMPORTANT]
> Per un'esperienza ottimale, è consigliabile utilizzare un pool di connessioni come pgBouncer per gestire in modo efficiente le connessioni.

Una connessione PostgreSQL, anche inattiva, può occupare circa 10 MB di memoria. Inoltre, la creazione di nuove connessioni richiede tempo. La maggior parte delle applicazioni richiedono molte connessioni di breve durata, che aggrava questa situazione. Il risultato è una riduzione delle risorse disponibili per il carico di lavoro effettivo, con conseguente riduzione delle prestazioni. Un pool di connessioni che riduce le connessioni inattive e riutilizza le connessioni esistenti consentirà di evitare questo problema. Per saperne di più, visita il nostro [post sul blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Limitazioni funzionali
### <a name="scale-operations"></a>Operazioni di scalabilità
- Non è attualmente supportata la scalabilità dinamica tra i piani tariffari.
- La riduzione delle dimensioni di archiviazione del server non è attualmente supportata.

### <a name="server-version-upgrades"></a>Aggiornamenti della versione dei server
- La migrazione automatica tra le versioni del motore del database principale non è attualmente supportata. Se si vuole eseguire l'aggiornamento alla versione principale successiva, eseguire un [dump e ripristinarlo](./howto-migrate-using-dump-and-restore.md) in un server creato con la nuova versione del motore.

> Si noti che prima di PostgreSQL versione 10, i criteri di [controllo delle versioni PostgreSQL](https://www.postgresql.org/support/versioning/) consideravano un aggiornamento della _versione principale_ come un aumento del primo _o_ del secondo numero (ad esempio, da 9,5 a 9,6 era considerato un aggiornamento della versione _principale)._
> A partire dalla versione 10, solo una modifica nel primo numero è considerata un aggiornamento della versione principale (ad esempio, 10.0 a 10.1 è un aggiornamento di versione _secondaria_ e da 10 a 11 è un aggiornamento della versione _principale)._

### <a name="vnet-service-endpoints"></a>Endpoint del servizio di rete virtuale
- Gli endpoint di servizio di rete virtuale sono supportati solo per i server per utilizzo generico e ottimizzati per la memoria.

### <a name="restoring-a-server"></a>Rispristino di un server
- Quando si usa la funzionalità di recupero temporizzato, il nuovo server viene creato con le stesse configurazioni relative al piano tariffario del server su cui si basa.
- Il nuovo server creato durante un ripristino non dispone delle regole del firewall presenti nel server originale. Per questo nuovo server, le regole del firewall devono essere impostate separatamente.
- Il ripristino di un server eliminato non è supportato.

### <a name="utf-8-characters-on-windows"></a>Caratteri UTF-8 in Windows
- In alcuni scenari, i caratteri UTF-8 non sono completamente supportati in PostgreSQL open source per Windows, con ripercussioni su Database di Azure per PostgreSQL. Per ulteriori informazioni, vedere il thread nell'articolo [Bug #15476 in the postgresql-archive](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) (Bug n. 15476 in postgresql-archive).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle [opzioni e prestazioni disponibili in ogni piano tariffario](concepts-pricing-tiers.md)
- Informazione sulle [versioni supportate del Database PostgreSQL](concepts-supported-versions.md)
- Vedere [Come eseguire la procedura di backup e ripristino di un server in Database di Azure per PostgreSQL usando il portale di Azure](howto-restore-server-portal.md)
