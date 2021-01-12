---
title: Risoluzione dei problemi relativi al pool SQL dedicato (in precedenza SQL DW)
description: Risoluzione dei problemi relativi al pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 8db1825e7abfaaeca4650cbd03dd05eec4777c21
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121278"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Risoluzione dei problemi relativi al pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics

Questo articolo elenca i problemi comuni di risoluzione dei problemi nel pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics.

## <a name="connecting"></a>Connecting

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Accesso non riuscito per l'utente 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, Errore: 18456) | Questo errore si verifica quando un utente di Azure AD prova a connettersi al database master, ma non ha un utente in tale database.  Per risolvere il problema, specificare il pool SQL dedicato (in precedenza SQL DW) a cui si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Per ulteriori dettagli, vedere l'articolo [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md) . |
| L'entità server "MyUserName" non può accedere al database "master" nel contesto di sicurezza corrente. Impossibile aprire il database utente predefinito. Accesso non riuscito. Accesso non riuscito per l'utente 'MyUserName'. (Microsoft SQL Server, Errore: 916) | Questo errore si verifica quando un utente di Azure AD prova a connettersi al database master, ma non ha un utente in tale database.  Per risolvere il problema, specificare il pool SQL dedicato (in precedenza SQL DW) a cui si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Per ulteriori dettagli, vedere l'articolo [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md) . |
| Errore CTAIP                                                  | Questo errore può verificarsi quando è stato creato un account di accesso nel database master del database SQL, ma non nel database SQL specifico.  Se si verifica questo errore, vedere l'articolo [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md) .  In questo articolo viene illustrato come creare un account di accesso e un utente nel database master e come creare un utente in un database SQL. |
| Blocco da parte del firewall                                          | Il pool SQL dedicato (in precedenza SQL DW) è protetto da firewall per garantire che solo gli indirizzi IP noti abbiano accesso a un database. I firewall sono protetti per impostazione predefinita, il che significa che è necessario abilitare in modo esplicito un indirizzo IP o un intervallo di indirizzi prima di potersi connettere.  Per configurare il firewall per l'accesso, seguire la procedura descritta in [Configurare l'accesso al firewall del server per l'indirizzo IP del client](create-data-warehouse-portal.md) nella [Procedura di configurazione del provisioning](create-data-warehouse-portal.md). |
| Impossibile connettersi con lo strumento o il driver                           | Il pool SQL dedicato (in precedenza SQL DW) consiglia di usare [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [SSDT per Visual Studio](sql-data-warehouse-install-visual-studio.md)o [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md) per eseguire query sui dati. Per altre informazioni sui driver e sulla connessione ad Azure Synapse, vedere gli articoli [Driver per Azure Synapse](sql-data-warehouse-connection-strings.md) e [connettersi ad Azure Synapse](sql-data-warehouse-connect-overview.md). |

## <a name="tools"></a>Strumenti

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| In Esplora oggetti di Visual Studio mancano utenti di Azure AD           | Questo è un problema noto  Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Per altre informazioni sull'uso di Azure Active Directory con un pool SQL dedicato (in precedenza SQL DW), vedere [autenticazione in sinapsi di Azure](sql-data-warehouse-authentication.md) . |
| L'esecuzione manuale di script, l'uso della creazione guidata script o la connessione tramite SSMS sono lenti, si bloccano o generano errori | Assicurarsi che gli utenti siano stati creati nel database master. In opzioni di scripting verificare anche che l'edizione del motore sia impostata come "Microsoft Azure sinapsi Analytics Edition" e che il tipo di motore sia "database SQL di Microsoft Azure". |
| Errore della generazione di script in SSMS                               | La generazione di uno script per il pool SQL dedicato (in precedenza SQL DW) ha esito negativo se l'opzione "genera script per oggetti dipendenti" è impostata su "true". Per risolvere il problema, gli utenti devono passare manualmente a **Strumenti -> Opzioni -> Esplora oggetti di SQL Server -> Genera script per oggetti dipendenti e impostare l'opzione su false** |

## <a name="data-ingestion-and-preparation"></a>Inserimento e preparazione dei dati

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| L'esportazione di stringhe vuote tramite CETAS comporterà valori NULL nei file parquet e ORC. Si noti che se si esportano stringhe vuote da colonne con vincoli NOT NULL, CETAS comporterà la reiezione dei record e l'esportazione potrà potenzialmente avere esito negativo. | Rimuovere le stringhe vuote o la colonna che causa il danneggiamento nell'istruzione SELECT del CETAS. |
| Il caricamento di un valore non compreso nell'intervallo 0-127 in una colonna tinyint per il formato di file parquet e ORC non è supportato. | Specificare un tipo di dati più grande per la colonna di destinazione.           |

## <a name="performance"></a>Prestazioni

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Risoluzione dei problemi di prestazioni delle query                            | Se si sta cercando di risolvere i problemi relativi a una determinata query, un ottimo punto di partenza è l'articolo su come [imparare a monitorare le query](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemi di spazio di TempDB | [Monitorare l'utilizzo dello spazio](sql-data-warehouse-manage-monitor.md#monitor-tempdb) di TempDB.  Le cause più comuni dell'esaurimento dello spazio di TempDB sono:<br>- Risorse allocate alla query insufficienti, che causano l'espansione dei dati in TempDB.  Vedere [Gestione dei carichi di lavoro](resource-classes-for-workload-management.md) <br>- Statistiche mancanti o non aggiornate, che causano uno spostamento eccessivo dei dati.  Per informazioni dettagliate su come creare statistiche, vedere [Gestione delle statistiche nelle tabelle](sql-data-warehouse-tables-statistics.md)<br>- Spazio TempDB allocato per livello di servizio.  [Il ridimensionamento del pool SQL dedicato (in precedenza SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) a un'impostazione DWU superiore consente di allocare più spazio tempdb.|
| Piani e prestazioni delle query di scarsa qualità sono spesso causati dalla mancanza di statistiche | La causa più comune di prestazioni di scarsa qualità è la mancanza di statistiche per le tabelle.  Per conoscere i dettagli su come creare statistiche e sui motivi per cui sono fondamentali per le prestazioni, vedere [Gestione delle statistiche nelle tabelle](sql-data-warehouse-tables-statistics.md). |
| Concorrenza bassa/query in coda                             | Comprendere la [gestione del carico di lavoro](resource-classes-for-workload-management.md) è importante per capire come bilanciare l'allocazione di memoria con la concorrenza. |
| Come implementare le procedure consigliate                              | Il modo migliore per iniziare a imparare a migliorare le prestazioni delle query è l'articolo [dedicato alle procedure consigliate per il pool SQL (in precedenza SQL DW)](sql-data-warehouse-best-practices.md) . |
| Come migliorare le prestazioni con la scalabilità                      | A volte la soluzione per migliorare le prestazioni consiste nel aggiungere semplicemente una maggiore potenza di calcolo alle query [ridimensionando il pool SQL dedicato (in precedenza SQL DW)](sql-data-warehouse-manage-compute-overview.md). |
| Scarse prestazioni delle query a causa di scarsa qualità degli indici     | A volte le query possono rallentare a causa della [scarsa qualità degli indici columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Vedere questo articolo per ulteriori informazioni e per capire come [ricompilare gli indici per migliorare la qualità dei segmenti](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Gestione del sistema

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Messaggio 40847: Non è stato possibile eseguire l'operazione perché il server avrebbe superato la quota di DTU consentita di 45000. | Ridurre il [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) del database che si sta tentando di creare oppure [richiedere un aumento della quota](sql-data-warehouse-get-started-create-support-ticket.md). |
| Analisi dell'uso dello spazio                              | Per comprendere l'uso dello spazio nel sistema, vedere la [tabella sulle dimensioni](sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Aiuto nella gestione delle tabelle                                    | Per informazioni su come gestire le tabelle, vedere la [Panoramica sulle tabelle](sql-data-warehouse-tables-overview.md).  Questo articolo contiene anche collegamenti ad articoli più dettagliati, ad esempio relativi a [tipi di dati delle tabelle](sql-data-warehouse-tables-data-types.md), [distribuzione di una tabella](sql-data-warehouse-tables-distribute.md), [indicizzazione di una tabella](sql-data-warehouse-tables-index.md), [partizionamento di una tabella](sql-data-warehouse-tables-partition.md), [gestione delle statistiche delle tabelle](sql-data-warehouse-tables-statistics.md) e [tabelle temporanee](sql-data-warehouse-tables-temporary.md). |
| L'indicatore di stato TDE (Transparent Data Encryption) non viene aggiornato nel portale di Azure | È possibile visualizzare lo stato di Transparent Data Encryption tramite [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

## <a name="differences-from-sql-database"></a>Differenze rispetto al database SQL

| Problema                                 | Risoluzione                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Funzionalità non supportate del database SQL     | Vedere [Funzionalità non supportate delle tabelle](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Tipi di dati non supportati del database SQL   | Vedere [Tipi di dati non supportati](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Limitazioni delle stored procedure          | Per capire alcune limitazioni delle stored procedure, vedere [Limitazioni delle stored procedure](sql-data-warehouse-develop-stored-procedures.md#limitations) . |
| Le UDF non supportano istruzioni SELECT | Si tratta di una limitazione corrente delle UDF.  Per conoscere la sintassi supportata, vedere [CREATE FUNCTION](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) . |

## <a name="next-steps"></a>Passaggi successivi

Se non si riesce a trovare una soluzione al problema, ecco alcune altre risorse che è possibile provare.

* [Blog](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Richieste di funzionalità](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Video](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md)
* [Pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-synapse-analytics.html)
* [Forum su Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)