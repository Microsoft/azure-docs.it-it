---
title: Criteri di supporto della versione-database di Azure per MySQL-server singolo e server flessibile (anteprima)
description: Descrive i criteri relativi alle versioni principale e secondaria di MySQL nel database di Azure per MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 8ad79f2f27864b4fbc78b7c104828230ff7f93bc
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103465658"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Criteri di supporto della versione per database di Azure per MySQL

Questa pagina descrive i criteri di controllo delle versioni di database di Azure per MySQL ed è applicabile alle modalità di distribuzione di database di Azure per MySQL-server singolo e database di Azure per MySQL-flessibili (anteprima).

## <a name="supported--mysql-versions"></a>Versioni di MySQL supportate

Database di Azure per MySQL è stato sviluppato da [MySQL Community Edition](https://www.mysql.com/products/community/), usando il motore di archiviazione InnoDB. Il servizio supporta tutte le versioni principali correnti supportate dalla community, ovvero MySQL 5,6, 5,7 e 8,0. MySQL usa lo schema di denominazione X. Y. Z dove X è la versione principale, Y è la versione secondaria e Z è il rilascio della correzione di bug. Per altre informazioni sullo schema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Nell'opzione di distribuzione a server singolo viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MySQL impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MySQL. Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL.

Database di Azure per MySQL supporta attualmente le versioni principali e secondarie seguenti di MySQL:

| Versione | Server unico <br/> Versione secondaria corrente |Server flessibile (anteprima) <br/> Versione secondaria corrente  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL versione 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(ritirata) | Non supportato|
|MySQL versione 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL versione 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Leggere i criteri di supporto della versione per le versioni ritirate nella [documentazione dei criteri di supporto](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql) delle versioni.

## <a name="major-version-support"></a>Supporto della versione principale
Ogni versione principale di MySQL sarà supportata da database di Azure per MySQL dalla data in cui Azure inizia a supportare la versione fino a quando la versione non viene ritirata dalla community di MySQL, come specificato nei [criteri di controllo delle versioni](https://www.mysql.com/support/eol-notice.html).

## <a name="minor-version-support"></a>Supporto della versione secondaria
Database di Azure per MySQL esegue automaticamente aggiornamenti della versione secondaria per la versione di MySQL preferita di Azure nell'ambito della manutenzione periodica. 

## <a name="major-version-retirement-policy"></a>Criteri di ritiro della versione principale
La tabella seguente fornisce i dettagli relativi al ritiro per le versioni principali di MySQL. Le date seguono i [criteri di controllo delle versioni di MySQL](https://www.mysql.com/support/eol-notice.html).

| Versione | Novità | Data di inizio del supporto di Azure | Data di ritiro|
| ----- | ----- | ------ | ----- |
| [MySQL 5,6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funzionalità](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 marzo 2018 | 2021 febbraio
| [MySQL 5,7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Funzionalità](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 marzo 2018 | 2023 ottobre
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Funzionalità](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11 dicembre 2019 | Aprile 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Versioni del motore MySQL ritirate non supportate nel database di Azure per MySQL

Dopo la data di ritiro per ogni versione del database MySQL, se si continua a eseguire la versione ritirata, tenere presenti le restrizioni seguenti:
- Poiché la community non rilascerà ulteriori correzioni di bug o correzioni per la sicurezza, il database di Azure per MySQL non patcherà il motore di database ritirato per eventuali bug o problemi di sicurezza o per adottare misure di sicurezza in relazione al motore di database ritirato. Tuttavia, Azure continuerà a eseguire periodicamente interventi di manutenzione e applicazione di patch per l'host, il sistema operativo, i contenitori e tutti gli altri componenti correlati ai servizi.
- Se è possibile che si verifichi un problema di supporto correlato al database MySQL, potrebbe non essere possibile fornire supporto. In questi casi, sarà necessario aggiornare il database in modo da fornire supporto.
<!-- - You will not be able to create new database servers for the retired version. However, you will be able to perform point-in-time recoveries and create read replicas for your existing servers. -->
- Le nuove funzionalità del servizio sviluppate da database di Azure per MySQL possono essere disponibili solo per le versioni del server di database supportate.
- I contratti di servizio con tempo di attività verranno applicati esclusivamente ai problemi correlati al servizio database di Azure per MySQL e non ai tempi di inattività causati da bug correlati al motore di database.  
- Nell'evento estremo di una grave minaccia per il servizio causato dalla vulnerabilità del motore di database MySQL identificata nella versione del database ritirata, Azure può scegliere di arrestare il nodo di calcolo del server di database per proteggere il servizio. Verrà richiesto di aggiornare il server prima di portare online il server. Durante il processo di aggiornamento, i dati verranno sempre protetti usando i backup automatici eseguiti sul servizio, che possono essere usati per ripristinare la versione precedente, se necessario. 



## <a name="next-steps"></a>Passaggi successivi
- Vedere database di Azure per MySQL- [versioni supportate](./concepts-supported-versions.md) da un singolo server
- Vedere le [versioni supportate](flexible-server/concepts-supported-versions.md) di database di Azure per MySQL-server flessibili (anteprima)
- Per eseguire gli aggiornamenti, vedere MySQL [dump and Restore](./concepts-migrate-dump-restore.md) .
