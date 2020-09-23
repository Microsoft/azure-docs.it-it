---
title: Ottimizzare la raccolta delle statistiche di query-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come è possibile ottimizzare la raccolta delle statistiche di query in un database di Azure per PostgreSQL-server singolo
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: bc731f6f6a5a60bce0851bf8fe5874f7149f3899
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90901456"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Ottimizzare la raccolta delle statistiche sulle query in un database di Azure per PostgreSQL-server singolo
Questo articolo descrive come ottimizzare la raccolta di statistiche query in un server di Database di Azure per PostgreSQL.

## <a name="use-pg_stats_statements"></a>Usare pg_stats_statements
**Pg_stat_statements** è un'estensione di PostgreSQL abilitata per impostazione predefinita in Database di Azure per PostgreSQL. Questa estensione consente di tenere traccia delle statistiche di esecuzione per tutte le istruzioni SQL eseguite da un server. Questo modulo interessa ogni esecuzione di query e ha quindi un impatto di un certo rilievo sulle prestazioni. L'abilitazione di **pg_stat_statements** comporta operazioni di scrittura del testo delle query su file su disco.

Se si hanno query univoche con testo particolarmente lungo o non si esegue attivamente il monitoraggio di **pg_stat_statements**, disabilitare **pg_stat_statements** per ottenere prestazioni ottimali. A tale scopo, modificare l'impostazione su `pg_stat_statements.track = NONE`.

Per alcuni carichi di lavoro dei clienti è stato riscontrato un miglioramento delle prestazioni fino al 50% in seguito alla disabilitazione di **pg_stat_statements**. Disabilitando pg_stat_statements si ha tuttavia lo svantaggio di non poter risolvere i problemi relativi alle prestazioni.

Per impostare `pg_stat_statements.track = NONE`:

- Nel portale di Azure passare alla [pagina di gestione delle risorse PostgreSQL e selezionare il pannello dei parametri del server](howto-configure-server-parameters-using-portal.md).

  :::image type="content" source="./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png" alt-text="Pannello dei parametri del server PostgreSQL":::

- Usare il comando dell'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md) az postgres server configuration set con i parametri `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Usare Query Store 
La funzionalità [Query Store](concepts-query-store.md) di Database di Azure per PostgreSQL offre un metodo più efficace per tenere traccia delle statistiche query. È consigliabile usare questa funzionalità come alternativa a *pg_stats_statements*. 

## <a name="next-steps"></a>Passaggi successivi
È consigliabile impostare `pg_stat_statements.track = NONE` nel [portale di Azure](howto-configure-server-parameters-using-portal.md) o usare l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).

Per altre informazioni, vedere: 
- [Scenari di utilizzo di Query Store](concepts-query-store-scenarios.md) 
- [Procedure consigliate Query Store](concepts-query-store-best-practices.md) 
